<properties
	pageTitle="Kontoverwaltung mit Batch Management .NET | Microsoft Azure"
	description="Erstellen, löschen und ändern Sie Azure-Batch-Konten in Ihren Anwendungen mit der Batch Management .NET-Bibliothek."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="11/10/2015"
	ms.author="v-marsma"/>

# Verwalten von Azure Batch-Konten und -Kontingenten mit Batch Management .NET

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Sie können den Wartungsaufwand in Ihren Azure Batch-Anwendungen verringern, indem Sie die [Batch Management .NET][api_mgmt_net]-Bibliothek verwenden, um das Erstellen des Batch-Kontos, das Löschen, die Schlüsselverwaltung und die Kontingentermittlung zu automatisieren.

- **Erstellen und Löschen von Batch-Konten** in jeder Region. Wenn Sie als unabhängiger Softwareanbieter (ISV) beispielsweise Ihren Kunden einen Dienst anbieten, bei dem jedem ein separates Batch-Konto zu Abrechnungszwecken zugewiesen wird, können Sie dem Kundenportal Funktionen zum Erstellen und Löschen von Konten hinzufügen.
- **Programmgesteuertes Abrufen und erneutes Erstellen von Kontenschlüsseln** für alle Batch-Konten. Dies ist besonders nützlich für die Einhaltung der Sicherheitsrichtlinien, die ein regelmäßiges Rollover oder den Ablauf von Kontenschlüsseln erzwingen können. Bei mehreren Batch-Konten in verschiedenen Azure-Regionen, erhöht die Automatisierung dieses Rolloverprozesses die Lösungseffizienz.
- **Überprüfen von Kontokontingenten** und Beseitigen der Mutmaßungen des Trial-and-Error-Prinzips beim Festlegen der Einschränkungen für die jeweiligen Batch-Konten. Durch Überprüfen der Kontokontingente vor dem Starten von Aufträgen, Erstellen von Pools oder Hinzufügen von Computeknoten können Sie proaktiv anpassen, wo und wann diese Computeressourcen erstellt werden. Sie können bestimmen, für welche Konten die Kontingente erhöht werden müssen, bevor zusätzliche Ressourcen in diesen Konten zugewiesen werden.
- **Kombinieren Sie Funktionen von anderen Azure-Diensten**, um eine Verwaltung mit optimalem Funktionsumfang zu ermöglichen. Nutzen Sie dafür z. B. Batch Management .NET, [Azure Active Directory][aad_about] und den [Azure-Ressourcen-Manager][resman_overview] gemeinsam in derselben Anwendung. Wenn Sie diese Funktionen und ihre APIs nutzen, können Sie ein reibungsloses Authentifizierungserlebnis, Möglichkeiten zum Erstellen und Löschen von Ressourcengruppen und die oben beschriebenen Funktionen für eine End-to-End-Verwaltungslösung bereitstellen.

> [AZURE.NOTE]Auch wenn sich dieser Artikel auf die programmgesteuerte Verwaltung der Batch-Konten, -Schlüssel und -Kontingente konzentriert, können Sie viele dieser Aktivitäten mithilfe des [Azure-Portals][azure_portal] ausführen. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Azure Batch-Kontos im Azure-Portal](batch-account-create-portal.md) und [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md).

## Erstellen und Löschen von Batch-Konten

Wie bereits erwähnt, ist eine der Hauptfunktionen der Batch Management-API das Erstellen und Löschen von Batch-Konten innerhalb einer Azure-Region. Zu diesem Zweck verwenden Sie [BatchManagementClient.Accounts.CreateAsync][net_create] und [DeleteAsync][net_delete] oder ihre synchronen Gegenstücke.

Mit dem folgenden Codeausschnitt wird ein Konto erstellt, und das neu erstellte Konto wird aus dem Batch-Dienst abgerufen und anschließend gelöscht. In diesem und anderen Ausschnitten in diesem Artikel ist `batchManagementClient` eine vollständig initialisierte Instanz von [BatchManagementClient][net_mgmt_client].

```
// Create a new Batch account
await batchManagementClient.Accounts.CreateAsync("MyResourceGroup",
	"mynewaccount",
	new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mynewaccount");
AccountResource account = getResponse.Resource;

// Delete the account
await batchManagementClient.Accounts.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE]Anwendungen, die die Batch Management .NET-Bibliothek und die zugehörige „BatchManagementClient“-Klasse verwenden, benötigen einen Zugriff als **Dienstadministrator** oder **Co-Administrator** auf das Abonnement, das Besitzer des zu verwaltenden Batch-Kontos ist. Weitere Informationen finden Sie im nachfolgenden Abschnitt zu [Azure Active Directory](#aad) und im [AccountManagement][acct_mgmt_sample]-Codebeispiel.

## Abrufen und erneutes Generieren von Kontoschlüsseln

Rufen Sie primäre und sekundäre Kontoschlüssel aus jedem Batch-Konto innerhalb Ihres Abonnements mithilfe von [ListKeysAsync][net_list_keys] ab. Sie können diese Schlüssel mit [RegenerateKeyAsync][net_regenerate_keys] erneut generieren.

```
// Get and print the primary and secondary keys
BatchAccountListKeyResponse accountKeys = await batchManagementClient.Accounts.ListKeysAsync("MyResourceGroup", "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.PrimaryKey);
Console.WriteLine("Secondary key: {0}", accountKeys.SecondaryKey);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys = await batchManagementClient.Accounts.RegenerateKeyAsync(
	"MyResourceGroup",
	"mybatchaccount",
	new BatchAccountRegenerateKeyParameters() { KeyName = AccountKeyType.Primary });
```

> [AZURE.TIP]Sie können einen optimierten Verbindungsworkflow für Ihre Verwaltungsanwendungen erstellen. Rufen Sie zuerst mithilfe von [ListKeysAsync][net_list_keys] einen Kontoschlüssel für das zu verwaltende Batch-Konto ab. Verwenden Sie diesen Schlüssel anschließend beim Initialisieren der [BatchSharedKeyCredentials][net_sharedkeycred]-Klasse aus der Batch .NET-Bibliothek. Diese Klasse wird beim Initialisieren von [BatchClient][net_batch_client] verwendet.

## Überprüfen des Azure-Abonnements und der Batch-Kontokontingente

Azure-Abonnements und die einzelnen Azure-Dienste wie z. B. Batch verfügen über Standardkontingente, die die Anzahl von bestimmten darin enthaltenen Entitäten begrenzen. Die Standardkontingente für Azure-Abonnements finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](./../azure-subscription-service-limits.md). Die Standardkontingente für den Batch-Dienst finden Sie unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md). Mithilfe der Batch Management .NET-Bibliothek können Sie diese Kontingente in Ihren Anwendungen überprüfen. Dadurch können Sie Zuordnungsentscheidungen treffen, bevor Sie Konten oder Computeressourcen hinzufügen (z. B. Pools und Computeknoten).

### Überprüfen des Azure-Abonnements für Batch-Kontokontingente

Vor dem Erstellen eines Batch-Kontos in einer Region können Sie Ihr Azure-Abonnement überprüfen, um festzustellen, ob Sie ein Konto in der betreffenden Region hinzufügen können.

Im folgenden Codeausschnitt verwenden wir zunächst [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts], um eine Auflistung aller Batch-Konten innerhalb eines Abonnements abzurufen. Nach dem Abrufen dieser Auflistung bestimmen wir, wie viele Konten sich in der Zielregion befinden. Anschließend rufen wir mithilfe von [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] das Batch-Kontokontingent ab und bestimmen, wie viele Konten (falls möglich) in dieser Region erstellt werden können.

```
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse = await batchManagementClient.Accounts.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}", creds.SubscriptionId, accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Im obigen Codeausschnitt ist `creds` eine Instanz von [TokenCloudCredentials][azure_tokencreds]. Ein Beispiel zum Erstellen dieses Objekts finden Sie im [AccountManagement][acct_mgmt_sample]-Codebeispiel auf GitHub.

### Überprüfen eines Batch-Kontos für Computeressourcenkontingente

Bevor Sie Computeressourcen in Ihrer Batch-Lösung erhöhen, können Sie sich vergewissern, dass diese neu zuzuordnenden Ressourcen nicht die derzeit geltenden Kontokontingente übersteigen. Mit dem folgenden Codeausschnitt werden einfach die Kontingentinformationen für das Batch-Konto `mybatchaccount` ausgegeben. In Ihrer eigenen Anwendung können Sie anhand solcher Informationen jedoch bestimmen, ob das Konto die zusätzlichen Ressourcen, die Sie erstellen möchten, überhaupt aufnehmen kann.

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT]Solange Standardkontingente für Azure-Abonnements und -Dienste vorliegen, können viele dieser Grenzen durch eine Anforderung im [Azure-Portal][azure_portal] erhöht werden. Anleitungen zum Erhöhen der Batch-Kontokontingente finden Sie z. B. unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md).

## Batch Management .NET, Azure Active Directory und Ressourcen-Manager

Beim Arbeiten mit der Batch Management .NET-Bibliothek nutzen Sie in der Regel die Funktionen von [Azure Active Directory][aad_about] (Azure AD) und [Azure-Ressourcen-Manager][resman_overview]. Im nachfolgend beschriebenen Beispielprojekt wird sowohl Azure Active Directory als auch der Ressourcen-Manager verwendet, um die Nutzung der Batch Management .NET-API zu demonstrieren.

### <a name="aad"></a>Azure Active Directory

Azure selbst verwendet Azure Active AD für die Authentifizierung seiner Kunden, Dienstadministratoren und Organisationsbenutzer. Im Kontext von Batch Management .NET verwenden Sie Azure AD zum Authentifizieren des Administrators oder Co-Administrators eines Abonnements. Dadurch kann die Verwaltungsbibliothek anschließend den Batch-Dienst abfragen und die in diesem Artikel beschriebenen Vorgänge ausführen.

Im nachfolgenden Beispielprojekt wird die Azure [Active Directory-Authentifizierungsbibliothek][aad_adal] (ADAL, Active Directory Authentication Library) verwendet, um den Benutzer zur Eingabe der Microsoft-Anmeldeinformationen aufzufordern. Wenn Dienstadministrator- oder Co-Administrator-Anmeldeinformationen angegeben werden, kann die Anwendung Azure nach einer Liste von Abonnements abfragen und sowohl Ressourcengruppen als auch Batch-Konten erstellen und löschen.

### Ressourcen-Manager

Wenn Sie Batch-Konten mit der Batch Management .NET-Bibliothek erstellen, erstellen Sie sie in der Regel innerhalb einer [Ressourcengruppe][resman_overview]. Sie können die Ressourcengruppe programmgesteuert erstellen, indem Sie die [ResourceManagementClient][resman_client]-Klasse in der [Resource Manager .NET][resman_api]-Bibliothek verwenden. Oder Sie können einer vorhandenen, zuvor im [Azure-Portal][azure_portal] erstellten Ressourcengruppe ein Konto hinzufügen.

## <a name="sample"></a>Beispielprojekt auf GitHub

Sehen Sie sich das [AccountManagement][acct_mgmt_sample] -Beispielprojekt auf GitHub an, um die Funktionsweise der Batch Management .NET-Bibliothek zu sehen. Diese Konsolenanwendung veranschaulicht die Erstellung und Verwendung von [BatchManagementClient][net_mgmt_client] und [ResourceManagementClient][resman_client]. Außerdem veranschaulicht sie die Verwendung der Azure [Active Directory-Authentifizierungsbibliothek][aad_adal] (ADAL), die für beide Clients erforderlich ist.

> [AZURE.IMPORTANT]Zum erfolgreichen Ausführen der Beispielanwendung müssen Sie sie zuerst über das Azure-Portal bei Azure AD registrieren. Lesen Sie den Abschnitt „Hinzufügen einer Anwendung“ unter [Integrieren von Anwendungen in Azure Active Directory][aad_integrate]. Führen Sie anschließend die in dem Artikel genannten Schritte aus, um die Beispielanwendung in Ihrem eigenen Konto zu registrieren.

Die Beispielanwendung veranschaulicht die folgenden Vorgänge:

1. Erwerben eines Sicherheitstokens von Azure Active AD mithilfe von [ADAL][aad_adal]. Wenn der Benutzer noch nicht angemeldet ist, wird er aufgefordert, die Azure-Anmeldeinformationen einzugeben.
2. Unter Verwendung eines von Azure AD abgerufenen Sicherheitstokens wird eine [SubscriptionClient][resman_subclient] erstellt, um Azure nach einer Liste von Abonnements abzufragen, die mit dem Konto verknüpft sind. Dadurch kann der Benutzer ein Abonnement auswählen, falls mehrere gefunden werden.
3. Erstellen eines neuen Anmeldeinformationsobjekts, das dem ausgewählten Abonnement zugeordnet ist.
4. Erstellen von [ResourceManagementClient][resman_client] mithilfe der neuen Anmeldeinformationen.
5. Verwenden von [ResourceManagementClient][resman_client] zum Erstellen einer neuen Ressourcengruppe.
6. Verwenden von [BatchManagementClient][net_mgmt_client] zum Ausführen einer Reihe von Batch-Kontovorgängen:
  - Erstellen eines neuen Batch-Kontos innerhalb der neu erstellten Ressourcengruppe.
  - Abrufen des neu erstellten Kontos aus dem Batch-Dienst.
  - Drucken der Kontoschlüssel für das neue Konto.
  - Erneutes Generieren eines neuen Primärschlüssels für das Konto.
  - Ausgeben der Kontingentinformationen für das Konto.
  - Ausgeben der Kontingentinformationen für das Abonnement.
  - Ausgeben aller Konten innerhalb des Abonnements.
  - Löschen des neu erstellten Kontos.
7. Löschen Sie die Ressourcengruppe.

Vor dem Löschen des neu erstellten Batch-Kontos und der Ressourcengruppe können Sie beides im [Azure-Portal][azure_portal] überprüfen:

![Azure-Portal mit Ressourcengruppe und Batch-Konto][1] <br /> *Azure-Portal mit neuer Ressourcengruppe und neuem Batch-Konto*

[aad_about]: ../active-directory/active-directory-whatis.md "Was ist Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Authentifizierungsszenarien für Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrieren von Anwendungen in Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png

<!---HONumber=AcomDC_0121_2016-->