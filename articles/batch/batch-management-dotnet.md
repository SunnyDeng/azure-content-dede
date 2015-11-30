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
- [Azure preview portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Geringerer Wartungsaufwand in der Azure Batch-Anwendung durch Nutzung der [Batch Management .NET][api_mgmt_net]-Bibliothek, um das Erstellen des Batch-Kontos, die Schlüsselverwaltung und die Kontingentermittlung zu automatisieren.

- **Erstellen und Löschen von Batch-Konten** in jeder Region. Wenn Sie als unabhängiger Softwareanbieter (ISV) beispielsweise Ihren Kunden einen Dienst anbieten, bei dem jedem ein separates Batch-Konto zu Abrechnungszwecken zugewiesen wird, können Sie dem Kundenportal Funktionen zum Erstellen und Löschen von Konten hinzufügen.
- **Programmgesteuertes Abrufen und erneutes Erstellen von Kontenschlüsseln** für alle Batch-Konten. Dies ist besonders nützlich für die Einhaltung der Sicherheitsrichtlinien, die ein regelmäßiges Rollover oder den Ablauf von Kontenschlüsseln erzwingen können. Bei mehreren Batch-Konten in verschiedenen Azure-Regionen, erhöht die Automatisierung dieses Rolloverprozesses die Lösungseffizienz.
- **Überprüfen von Kontokontingenten** und Beseitigen der Mutmaßungen des Trial-and-Error-Prinzips beim Festlegen der Einschränkungen für die jeweiligen Batch-Konten. Durch Überprüfen der Kontokontingente vor dem Starten von Aufträgen, Erstellen von Pools oder Hinzufügen von Computeknoten können Sie proaktiv anpassen, wo und wann diese Computeressourcen erstellt werden. Sie können bestimmen, für welche Konten die Kontingente erhöht werden müssen, bevor zusätzliche Ressourcen in diesen Konten zugewiesen werden.
- **Kombinieren Sie Funktionen von anderen Azure-Diensten** für eine Verwaltung mit vollem Funktionsumfang durch die gemeinsame Nutzung von Batch Management .NET, [Azure Active Directory][aad_about] und den [Azure-Ressourcen-Manager][resman_overview] in derselben Anwendung. Durch die Nutzung dieser Funktionen und ihrer APIs können Sie einen reibungslosen Authentifizierungs- sowie Erstellungs- und Löschvorgang von Ressourcengruppen und die oben beschriebenen Funktionen für eine End-to-End-Verwaltungslösung bereitstellen.

> [AZURE.NOTE]Auch wenn sich dieser Artikel auf die programmgesteuerte Verwaltung der Batch-Konten, -Schlüssel und -Kontingente konzentriert, können Sie viele dieser Aktivitäten mithilfe des [Azure-Vorschauportals][azure_portal] ausführen. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Azure Batch-Kontos im Azure-Vorschauportal](batch-account-create-portal.md) und [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md).

## Erstellen und Löschen von Batch-Konten

Wie bereits erwähnt, ist eine der Hauptfunktionen der Batch Management-API das Erstellen und Löschen von Batch-Konten innerhalb einer Azure-Region. Zu diesem Zweck verwenden Sie [BatchManagementClient.Accounts.CreateAsync][net_create] und [DeleteAsync][net_delete] oder ihre synchronen Gegenstücke.

Mit dem folgenden Codeausschnitt wird ein Konto erstellt, das neu erstellte Konto aus dem Batch-Dienst abgerufen und anschließend gelöscht. In diesem und anderen Ausschnitten in diesem Artikel ist `batchManagementClient` eine vollständig initialisierte Instanz von [BatchManagementClient][net_mgmt_client].

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

> [AZURE.NOTE]Anwendungen, die die Batch Management .NET-Bibliothek und den BatchManagementClient verwenden, benötigen einen Zugriff als **Dienstadministrator** oder **Co-Administrator** auf das Abonnement, das Besitzer des zu verwaltenden Batch-Kontos ist. Weitere Informationen finden Sie im nachfolgenden Abschnitt [Azure Active Directory](#aad) und im [AccountManagement][acct_mgmt_sample]-Codebeispiel.

## Abrufen und erneutes Generieren von Kontoschlüsseln

Rufen Sie primäre und sekundäre Kontoschlüssel aus jedem Batch-Konto innerhalb Ihres Abonnements mit [ListKeysAsync][net_list_keys] ab, und generieren Sie diese Schlüssel mit [RegenerateKeyAsync][net_regenerate_keys] erneut.

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

> [AZURE.TIP]Sie können einen optimierten Verbindungsworkflow für Ihre Verwaltungsanwendungen erstellen. Besorgen Sie sich zuerst einen Kontoschlüssel für das Batch-Konto, das Sie mit [ListKeysAsync][net_list_keys] verwalten möchten, verwenden Sie dann diesen Schlüssel beim Initialisieren von [BatchSharedKeyCredentials][net_sharedkeycred] der Batch .NET-Bibliothek, die beim Initialisieren eines [BatchClient][net_batch_client] verwendet wird.

## Überprüfen des Azure-Abonnements und der Batch-Kontokontingente

Azure-Abonnements und die einzelnen Azure-Dienste, wie Batch, verfügen alle über Standardkontingente, die die Anzahl von bestimmten darin enthaltenen Entitäten begrenzen. Die Standardkontingente für Azure-Abonnements finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](./../azure-subscription-service-limits.md), und die des Batch-Diensts finden Sie unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md). Die Batch Management .NET-Bibliothek ermöglicht das Überprüfen dieser Kontingente in Ihren Anwendungen, was es Ihnen ermöglicht, Entscheidungen hinsichtlich der Zuordnung zu treffen, bevor Sie Konten oder Computeressourcen wie Pools und Computeknoten hinzufügen.

### Überprüfen des Azure-Abonnements für Batch-Kontokontingente

Vor dem Erstellen eines Batch-Kontos in einer Region können Sie Ihr Azure-Abonnement überprüfen, um festzustellen, ob Sie ein Konto in der betreffenden Region hinzufügen können.

Im folgenden Codeausschnitt verwenden wir zunächst [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts], um eine Auflistung aller Batch-Konten innerhalb eines Abonnements abzurufen. Sobald wir diese Auflistung erhalten haben, bestimmen wir, wie viele Konten sich in der Zielregion befinden, dann verwenden wir [BatchManagementClient.Subscriptions][net_mgmt_subscriptions], um das Batch-Kontokontingent abzurufen und zu bestimmen, wie viele Konten (sofern vorhanden) in dieser Region erstellt werden können.

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

Bevor Sie Computeressourcen in Ihrer Batch-Lösung erhöhen, können Sie überprüfen, ob die Ressourcen, die Sie zuordnen möchten, nicht die derzeit geltenden Kontokontingente übersteigen. Im folgenden Codeausschnitt drucken wir die Kontingentinformationen für das Batch-Konto mit dem Namen `mybatchaccount` einfach aus, aber in Ihrer eigenen Anwendung können Sie solche Informationen verwenden, um zu bestimmen, ob das Konto die zusätzlichen Ressourcen, die Sie erstellen möchten, verarbeiten kann.

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT]Solange Standardkontingente für Azure-Abonnements und -Dienste vorliegen, können viele dieser Grenzen durch eine Anforderung im [Azure-Vorschauportal][azure_portal] erhöht werden. Anleitungen zum Erhöhen der Batch-Kontokontingente finden Sie beispielsweise unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md).

## Batch Management .NET, AAD und Ressourcen-Manager

Beim Arbeiten mit der Batch Management .NET-Bibliothek nutzen Sie in der Regel die Funktionen von [Azure Active Directory][aad_about] (AAD) und des [Azure-Ressourcen-Manager][resman_overview]. Das nachfolgend beschriebene Beispielprojekt verwendet sowohl Azure Active Directory als auch den Ressourcen-Manager für die Vorführung der Batch Management .NET-API.

### <a name="aad"></a>Azure Active Directory

Azure selbst verwendet Azure Active Directory (AAD) für die Authentifizierung seiner Kunden, Dienstadministratoren und Unternehmensbenutzer. Im Kontext von Batch Management .NET verwenden Sie es, um einen Abonnementadministrator oder Co-Administrator zu authentifizieren, wodurch dann die Verwaltungsbibliothek den Batch-Dienst abfragen und die in diesem Artikel erläuterten Vorgänge durchführen kann.

Im nachfolgenden Beispielprojekt wird die Azure [Active Directory-Authentifizierungsbibliothek][aad_adal] (ADAL) verwendet, um den Benutzer zur Eingabe der Microsoft ID-Anmeldeinformationen aufzufordern. Wenn Dienstadministrator- oder Co-Administrator-Anmeldeinformationen angegeben sind, kann die Anwendung Azure nach einer Liste von Abonnements abfragen und sowohl Ressourcengruppen als auch Batch-Konten erstellen und löschen.

### Ressourcen-Manager

Beim Erstellen von Batch-Konten mit der Batch Management .NET-Bibliothek erstellen Sie sie in der Regel innerhalb einer [Ressourcengruppe][resman_overview]. Sie können die Ressourcengruppe programmgesteuert mithilfe von [ResourceManagementClient][resman_client] innerhalb der [Resource Manager .NET][resman_api]-Bibliothek erstellen, oder Sie können ein Konto einer vorhandenen Ressourcengruppe hinzufügen, die Sie zuvor mit dem [Azure-Vorschauportal][azure_portal] erstellt haben.

## <a name="sample"></a>Beispielprojekt auf GitHub

Sehen Sie sich das [AccountManagement][acct_mgmt_sample] -Beispielprojekt auf GitHub an, um die Funktionsweise der Batch Management .NET-Bibliothek zu sehen. Diese Konsolenanwendung zeigt die Erstellung und Verwendung von [BatchManagementClient][net_mgmt_client] und [ResourceManagementClient][resman_client] und veranschaulicht die Nutzung der Azure [Active Directory-Authentifizierungsbibliothek][aad_adal] (ADAL), die für beide Clients erforderlich ist.

> [AZURE.IMPORTANT]Zum erfolgreichen Ausführen der Beispielanwendung müssen Sie sich zuerst über das Azure-Verwaltungsportal bei Azure Active Directory registrieren. Lesen Sie zuerst **Hinzufügen einer Anwendung** unter [Integrieren von Anwendungen in Azure Active Directory][aad_integrate], und führen Sie dann die Schritte im Artikel durch, um die Beispielanwendung in Ihrem eigenen Konto zu registrieren.

Die Beispielanwendung veranschaulicht die folgenden Vorgänge:

1. Erwerben eines Sicherheitstoken von Azure Active Directory (AAD) mithilfe von [ADAL][aad_adal]. Wenn der Benutzer noch nicht angemeldet ist, wird er aufgefordert, die Azure-Anmeldeinformationen einzugeben.
2. Erstellen Sie mithilfe des von AAD abgerufenen Sicherheitstokens einen [SubscriptionClient][resman_subclient], um Azure nach einer Liste von Abonnements abzufragen, die mit dem Konto verknüpft sind, damit der Benutzer eines auswählen kann, wenn mehrere gefunden werden.
3. Erstellen eines neuen Anmeldeinformationsobjekts, das dem ausgewählten Abonnement zugeordnet ist
4. Erstellen eines [ResourceManagementClient][resman_client] mithilfe der neuen Anmeldeinformationen
5. Verwenden des [ResourceManagementClient][resman_client] zum Erstellen einer neuen Ressourcengruppe
6. Verwenden des [BatchManagementClient][net_mgmt_client] zum Durchführen einer Reihe von Batch-Konto-Vorgängen:
  - Erstellen eines neuen Batch-Kontos innerhalb der neu erstellten Ressourcengruppe
  - Abrufen des neu erstellten Kontos aus dem Batch-Dienst
  - Drucken der Kontoschlüssel für das neue Konto
  - Erneutes Generieren eines neuen Primärschlüssels für das Konto
  - Drucken der Kontingentinformationen für das Konto
  - Drucken der Kontingentinformationen für das Abonnement
  - Drucken aller Konten innerhalb des Abonnements
  - Löschen des neu erstellten Kontos
7. Löschen der Ressourcengruppe

Vor dem Löschen des neu erstellten Batch-Kontos und der Ressourcengruppe können Sie beides im [Azure-Vorschauportal][azure_portal] überprüfen:

![Azure-Vorschauportal mit Ressourcengruppe und Batch-Konto][1] <br /> *Azure-Vorschauportal mit neuer Ressourcengruppe und neuem Batch-Konto*

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

<!---HONumber=Nov15_HO4-->