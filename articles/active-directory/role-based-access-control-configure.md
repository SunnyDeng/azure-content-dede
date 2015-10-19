<properties
	pageTitle="Rollenbasierte Zugriffssteuerung über das Microsoft Azure-Portal"
	description="Beschreibt die Funktionsweise und Einrichtung der rollenbasierten Zugriffssteuerung."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/05/2015"
	ms.author="inhenk"/>

# Rollenbasierte Zugriffssteuerung über das Microsoft Azure-Portal

Es wird nun die rollenbasierte Zugriffssteuerung (RBAC) über das Microsoft Azure-Portal unterstützt, damit Organisationen ihren Zugriffsverwaltungsanforderungen einfach und präzise nachkommen können. Lesen Sie diesen [Blogeintrag](http://go.microsoft.com/fwlink/?LinkId=511576), um sich mit dieser Funktion vertraut zu machen und eine rasche Einführung zu erhalten. Die Konzepte werden detailliert beschrieben und an zusätzlichen Anwendungsfällen verdeutlicht.


## RBAC in Azure

Jedes Azure-Abonnement ist mit einem Azure Active Directory verknüpft. Benutzer und Dienste, die über das Microsoft Azure-Verwaltungsportal oder über die Azure-Ressourcen-Manager-API auf Abonnementressourcen zugreifen, müssen sich zunächst beim Azure Active Directory authentifizieren.

![][1]

Mithilfe der rollenbasierten Zugriffssteuerung von Azure kann Azure-AD-Benutzern, -Gruppen und -Diensten Zugriff gewährt werden, indem ihnen Rollen auf Abonnementbasis oder für eine Ressourcengruppe bzw. einzelne Ressourcen zugewiesen werden. Die zugewiesene Rolle bestimmt die Zugriffsebene der Benutzer, Gruppen oder Dienste auf eine Azure-Ressource.

### Rolle

Eine Rolle ist eine Sammlung von Aktionen, die an Azure-Ressourcen durchgeführt werden können. Ein Benutzer oder ein Dienst darf eine Aktion an einer Azure-Ressource durchführen, wenn ihnen eine Rolle zugewiesen ist, die diese Aktion umfasst. Eine Liste der integrierten Rollen und **deren** Aktions- und **Nicht-Aktions**-Eigenschaften finden Sie unter [Integrierte Rollen](#builtinroles).

### Rollenzuweisung

Azure AD-Benutzer und -Dienste erhalten Zugriff, indem ihnen die entsprechende Rolle für eine Azure-Ressource zugewiesen wird.

#### Azure AD-Sicherheitsprinzipale

Den folgenden Azure AD-Sicherheitsprinzipalen können Rollen zugewiesen werden:

+ **Benutzer**: Organisationsbenutzern können Rollen zugewiesen werden, wenn sie Teil des Azure AD sind, das mit dem Azure-Abonnement verknüpft ist. Auch Benutzern von externen Microsoft-Konten Rollen zugewiesen werden (wie joe@outlook.com): Dabei wird einem Benutzer mithilfe der Invite-Aktion eine Rolle über das Azure-Portal zugewiesen. Wenn einem externen Microsoft Account-Benutzer eine Rolle zugewiesen wird, wird in Azure AD ein Gastkonto für diesen Benutzer erstellt. Wird das Gastkonto im Verzeichnis deaktiviert, ist der Benutzer nicht berechtigt, auf bereits gewährte Azure-Ressourcen zuzugreifen.
+ **Gruppen**: Azure AD-Sicherheitsgruppen können Rollen zugewiesen werden. Ein Benutzer erhält automatisch Zugriff auf eine Ressource, wenn er Mitglied einer Gruppe mit Zugriffsrechten wird. Ebenso verliert der Benutzer automatisch Zugriff auf die Ressource, wenn er aus der Gruppe entfernt wird. Es wird empfohlen, Zugriffsrechte über Gruppen zu verwalten, d. h. anstatt einzelnen Benutzern werden Gruppen Rollen zugewiesen und Benutzer zur Gruppe hinzugefügt oder daraus entfernt. Verteilungslisten können in Azure RBAC keine Rollen zugewiesen werden. Durch die Möglichkeit, Gruppen Rollen zuweisen zu können, kann eine Organisation ihr bestehendes Zugriffssteuerungsmodell von ihrem lokalen Verzeichnis auf die Cloud ausweiten. Dadurch lassen sich bereits für die lokale Zugriffssteuerung festgelegte Sicherheitsgruppen für die Zugriffssteuerung auf Ressourcen im Azure-Portal wiederverwenden. Weitere Informationen über die verschiedenen Optionen zur Synchronisation von Benutzern und Gruppen von einem lokalen Verzeichnis aus finden Sie unter [Verzeichnisintegration](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium bietet außerdem eine [Funktion zur delegierten Gruppenverwaltung](http://msdn.microsoft.com/library/azure/dn641267.aspx). Mit dieser Funktion kann die Erstellung und Verwaltung von Gruppen an Nichtadministratorbenutzer von Azure AD delegiert werden.
+ **Dienstprinzipale**: Dienstidentitäten werden im Verzeichnis als Dienstprinzipale dargestellt. Sie authentifizieren sich mit Azure AD und können sicher miteinander kommunizieren. Diensten kann der Zugriff auf Azure-Ressourcen gewährleistet werden, indem dem Azure AD-Dienstprinzipal, der den Dienst darstellt, Rollen über das Azure-Modul für Windows PowerShell zugewiesen werden.

#### Ressourcenbereich

Zugriffsrechte müssen nicht für das gesamte Abonnement gewährt werden. Rollen können für Ressourcengruppen oder einzelne Ressourcen vergeben werden. In Azure RBAC erbt eine Ressource die Rollenzuweisung der ihr übergeordneten Ressource. Wenn also einem Benutzer, einer Gruppe oder einem Dienst Zugriff auf nur eine Ressourcengruppe eines Abonnements gewährt wird, können sie nur auf diese Ressourcengruppe und die zugehörigen Ressourcen zugreifen. Andere Ressourcengruppen desselben Abonnements sind vom Zugriff ausgeschlossen. Ein weiteres Beispiel: Eine Sicherheitsgruppe kann zur Leserolle für eine Ressourcengruppe und gleichzeitig zur Rolle „Mitwirkender“ für eine Datenbank innerhalb dieser Ressourcengruppe hinzugefügt werden.

![][2]

## Koexistenz von RBAC mit Abonnement-Co-Administratoren

Abonnementadministrator und -Co-Administratoren erhalten weiterhin Vollzugriff auf Azure-Portale und Verwaltungs-APIs. Im RBAC-Modell wird ihnen die Besitzerrolle auf Abonnementebene zugewiesen. Das neue RBAC-Modell wird jedoch nur durch die APIs des Azure-Portals und des Azure-Ressourcen-Managers unterstützt. Benutzer und Dienste, denen RBAC-Rollen zugewiesen wurden, können nicht auf das Azure-Verwaltungsportal oder auf die Service Management-APIs zugreifen. Wenn ein Benutzer über das Azure-Portal zur Besitzerrolle eines Abonnements hinzugefügt wird, ist er nicht automatisch Co-Administrator eines Abonnements im normalen Azure-Portal.

Wenn Sie einem Benutzer Zugriff auf eine Azure-Ressource gewähren möchten, die noch nicht zur Verwaltung im Azure-Portal zur Verfügung steht, fügen Sie den Benutzer über das normale Azure-Verwaltungsportal zu den Co-Administratoren des Abonnements hinzu. Service Bus und StorSimple sind Beispiele für Ressourcen, die momentan nicht mit RBAC verwaltet werden können.

## Autorisierung für Verwaltungsvorgänge vs. Datenvorgänge

Die rollenbasierte Zugriffssteuerung wird nur für Verwaltungsvorgänge von Azure-Ressourcen im Azure-Portal und in den Azure-Ressourcen-Manager-APIs unterstützt. Über RBAC können nicht alle Vorgänge auf Datenebene für Azure-Ressourcen autorisiert werden. Während das Erstellen/Lesen/Aktualisieren/Löschen von Speicherkonten über RBAC gesteuert werden kann, lassen sich dieselben Vorgänge für Blobs oder Tabellen in einem Speicherkonto noch nicht über RBAC steuern. Ebenso kann das Erstellen/Lesen/Aktualisieren/Löschen einer SQL-Datenbank über RBAC gesteuert werden, während sich dieselben Vorgänge noch nicht für SQL-Tabellen in einer Datenbank über RBAC steuern lassen.

## Hinzufügen und Entfernen von Zugriffsrechten

Im Folgenden wird anhand eines Beispiels erklärt, wie ein Ressourcenbesitzer in einer Organisation den Zugriff auf eine Ressource verwalten kann. In diesem Szenario arbeiten mehrere Personen an verschiedenen Test- und Produktionsprojekten, die mithilfe von Azure-Ressourcen erstellt wurden. Die Zugriffsberechtigung sollte nach bewährten Methoden vergeben werden. Dabei sollten Benutzer auf alle erforderlichen, jedoch nicht auf zusätzliche Ressourcen zugreifen können. Machen Sie möglichst von den bereits eingerichteten Prozessen und Tools Gebrauch, um die im lokalen Active Directory verwalteten Sicherheitsgruppen nutzen zu können. In den folgenden Abschnitten wird die Zugriffsvergabe auf die zuvor erwähnten Ressourcen beschrieben:

* [Zugriff hinzufügen](#add-access)
* [Zugriff entfernen](#remove-access)
* [Zugriff für externe Benutzer hinzufügen und entfernen](#add-or-remove-access-for-external-user)

### Zugriff hinzufügen

Es folgt eine Zusammenfassung der Zugriffsanforderungen und wie diese in Azure festgelegt werden.

Benutzer/Gruppe | Zugriffsanforderung | Rolle und Bereich für Zugriff
------------- | -------------  | ------------
Gesamtes Team von Jana Schulz | Lesen aller Azure-Ressourcen | Hinzufügen der AD-Gruppe, die das Team von Jana Schulz darstellt, zur Leserolle für das Azure-Abonnement
Gesamtes Team von Jana Schulz | Erstellen und Verwalten der gesamten Ressourcen in der Test-Ressourcengruppe | Hinzufügen der AD-Gruppe, die das Team von Jana Schulz darstellt, zur Rolle "Mitwirkender" für die Test-Ressourcengruppe
Brock | Erstellen und Verwalten der gesamten Ressourcen in der Prod-Ressourcengruppe | Hinzufügen von Brock zur Rolle "Mitwirkender" für die Prod-Ressourcengruppe


Zunächst wird für alle Ressourcen eines Abonnements Lesezugriff hinzugefügt. Klicken Sie auf **Durchsuchen > Alles > Abonnements**.

![][3]

Klicken Sie auf *Name des Abonnements* ** > Leser > Hinzufügen**. Wählen Sie aus der Liste mit Benutzern und Gruppen den Namen der Active Directory-Gruppe aus, oder geben Sie ihn ein.

![][4]

Fügen Sie dasselbe Team zur Rolle „Mitwirkender“ der Test-Ressourcengruppe hinzu. Klicken Sie auf eine Ressourcengruppe, um deren Eigenschaften anzuzeigen. Klicken Sie unter **Rollen** auf **Mitwirkender > Hinzufügen**, und geben Sie den Namen des Teams ein.

![][5]

Um Brock zur Rolle "Mitwirkender" der Prod-Ressourcengruppe hinzuzufügen, klicken Sie auf die Ressourcengruppe, dann auf **Mitwirkender > Hinzufügen**, und geben Sie den Namen "Brock" ein.

![][6]

Rollenzuweisungen können auch mit dem Microsoft Azure-Modul für Windows PowerShell verwaltet werden. Im folgenden Beispiel wird das Konto von Brock über das New-AzureRoleAssignment-Cmdlet anstatt über das Portal hinzugefügt:

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Weitere Informationen über das Hinzufügen und Entfernen von Zugriffsrechten mit Windows PowerShell finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung mit Windows PowerShell](role-based-access-control-powershell.md).

### Zugriff entfernen

Zugriffsberechtigungen können einfach wieder entfernt werden. Angenommen, Sie möchten einen Benutzer namens Bernd Ahrend von der Leserolle der TestDB-Ressourcengruppe entfernen. Öffnen Sie das Ressourcengruppenfenster, und klicken Sie auf **Leser > Bernd Ahrend > Entfernen**.

![][7]

Im folgenden Beispiel wird der Benutzer Bernd Ahrend über das Remove-AzureRoleAssignment-Cmdlet entfernt:

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### Zugriff für externe Benutzer hinzufügen und entfernen

Die Registerkarte **Konfigurieren** eines Verzeichnisses enthält Optionen zur Zugriffssteuerung für externe Benutzer. Diese Optionen können nur über die Benutzeroberfläche (es gibt keine Windows PowerShell- oder API-Methode) im vollständigen Azure-Portal durch einen globalen Verzeichnisadministrator geändert werden. Klicken Sie zum Öffnen im Azure-Portal auf die Registerkarte **Konfigurieren** und dann auf **Active Directory** und den Namen des Verzeichnisses.

![][10]

Anschließend können Sie die Optionen zur Zugriffssteuerung für externe Benutzer ändern.

![][8]

In der Standardeinstellung können Gäste den Verzeichnisinhalt nicht auflisten lassen. Somit können sie Benutzer und Gruppen in der **Mitgliederliste** nicht anzeigen. Gäste können jedoch durch Eingabe der vollständigen E-Mail-Adresse nach einem Benutzer suchen und ihm dann Zugriff gewähren. Für Gäste gelten folgende allgemeine Beschränkungen:

- Benutzer und Gruppen können nicht im Verzeichnis aufgelistet werden.
- Wenn die E-Mail-Adresse des Benutzers bekannt ist, können nur eingeschränkte Informationen zu diesem Benutzer angezeigt werden.
- Wenn der Name einer Gruppe bekannt ist, können nur eingeschränkte Informationen zu dieser Gruppe angezeigt werden.

Durch die Anzeige eingeschränkter Informationen zu einem Benutzer oder einer Gruppe können Gäste andere Personen einladen und Informationen zu Personen anzeigen, mit denen sie zusammenarbeiten.

Gehen Sie wie folgt vor, um Zugriff für einen externen Benutzer hinzuzufügen. Wir fügen einen externen Benutzer zur selben Leserolle für die TestDB-Ressourcengruppe hinzu, sodass der Benutzer beim Beheben eines Fehlers helfen kann. Öffnen Sie das Ressourcengruppenfenster, klicken Sie auf **Leser > Hinzufügen > Einladen**, und geben Sie die E-Mail-Adresse des hinzuzufügenden Benutzers ein.

![][9]

Beim Hinzufügen eines externen Benutzers wird im Verzeichnis ein Gastkonto erstellt. Anschließend kann der Gast zu einer Gruppe hinzugefügt oder daraus entfernt werden. Sie können einen Gast auch separat zu einer Rolle hinzufügen oder davon entfernen, so wie Sie es mit anderen Verzeichnisbenutzern tun würden.

Sie können einen Gast von jeder Rolle entfernen, so als würden Sie einen Benutzer entfernen. Wenn Sie einen Gast von einer Rolle einer Ressource entfernen, wird er nicht aus dem Verzeichnis entfernt.

## Nachverfolgen von Änderungen an Rollenzuweisungen

Änderungen an den Rollenzuweisungen werden in [Überwachungsprotokollen](http://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) aufgezeichnet, ähnlich wie andere Ereignisse. Das Protokoll der Rollenzuweisungsänderungen kann mit [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) oder der [REST-API von Azure-Ressourcen-Manager](https://msdn.microsoft.com/library/azure/dn931927.aspx) abgerufen werden.

Führen Sie beispielsweise zum Abrufen der Liste der Rollenzuweisungsänderungen für ein gesamtes Abonnement die folgenden beiden Cmdlets mit Azure PowerShell aus. Mit dem ersten Cmdlet wird der Azure-Ressourcen-Manager-Modus aktiviert.

`Switch-AzureMode -name AzureResourceManager`

`Get-AzureSubscriptionIdLog –DetailedOutput -StartTime '06-15-15' -EndTime '06-29-15'`

Rollenzuweisungsänderungen werden als Ereignisse erfasst, wobei der ResourceProviderName `Microsoft.Authorization` ist. Die tatsächlichen Details der Zuweisung werden in den Ereignisdetails aufgezeichnet: welcher Prinzipal wurde welcher Rolle und für welchen Bereich zugewiesen. Rollenzuweisungsänderungen sind beim Browsen durch die Überwachungsprotokolle im Portal sichtbar, im Portal werden jedoch nicht die Ereignisdetails angezeigt. Um die Ereignisdetails anzuzeigen, müssen Sie Azure PowerShell verwenden.

###Ereignisdetails

Hier ist ein Beispiel für ein Ereignisdetail für eine Rollenzuweisungsänderung:

```
Authorization        :
                       Scope     : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-W
                       estUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authoriza
                       tion/roleAssignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
                       Action    : Microsoft.Authorization/roleAssignments/write
                       Role      : Subscription Admin
                       Condition :
Caller               : William.Hennum@contoso.com
Claims               :
                       aud            : https://management.core.windows.net/
                       iss            : https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/
                       iat            : 1435333533
                       nbf            : 1435333533
                       exp            : 1435337433
                       ver            : 1.0
                       http://schemas.microsoft.com/identity/claims/tenantid: 72f988bf-86f1-41af-91ab-2d7cd011db47
                       http://schemas.microsoft.com/identity/claims/objectidentifier:
                       dda50086-5e3d-4a4b-b8bc-f54771104d89
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn: William.Hennum@contoso.com
                       puid           : 10030000803CDC0B
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier:
                       MJwntjqWaULfl30NJMiDRVSVCWMX5GzmMNU4oqitDXs
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname: William
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname: Hennum
                       name           : William Hennum
                       http://schemas.microsoft.com/claims/authnmethodsreferences: rsa,wia,mfa
                       _claim_names   : {"groups":"src1"}
                       _claim_sources : {"src1":{"endpoint":"https://graph.windows.net/72f988bf-86f1-41af-91ab-2d7cd011
                       db47/users/dda50086-5e3d-4a4b-b8bc-f54771104d89/getMemberObjects"}}
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name: William.Hennum@contoso.com
                       onprem_sid     : S-1-5-21-1721254763-462695806-1538882281-3175325
                       appid          : c44b4083-3bb0-49c1-b47d-974e53cbdf3c
                       appidacr       : 2
                       http://schemas.microsoft.com/identity/claims/scope: user_impersonation
                       http://schemas.microsoft.com/claims/authnclassreference: 1
CorrelationId        : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
Description          :
EventChannels        : Operation
EventDataId          : ed8e79b6-c7d1-4332-adcf-70d37546c5a6
EventName            : BeginRequest
EventSource          : Administrative
EventTimestamp       : 6/26/2015 3:53:34 PM
HttpRequest          :
                       ClientId        : F7272386-295A-4545-96BD-21F0856A43FE
                       Method          : PUT
                       Url             :
                       ClientIpAddress : 23.99.81.159
Id                   : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78/events/ed8e79b6-c7d1-4332-adcf-70d37546c5a6/ticks/
                       635709308140011864
Level                : Informational
OperationId          : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
OperationName        : Microsoft.Authorization/roleAssignments/write
Properties           :
                       requestbody    : {"Id":"531f036a-37ff-40c1-9bb9-aa580ebe7e78","Properties":{"PrincipalId":"dda50
                       086-5e3d-4a4b-b8bc-f54771104d89","RoleDefinitionId":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5
                       fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7"
                       ,"Scope":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-Wes
                       tUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes"}}
ResourceGroupName    : Default-Storage-WestUS
ResourceProviderName : Microsoft.Authorization
ResourceId           : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
Status               : Started
SubmissionTimestamp  : 6/26/2015 3:53:50 PM
SubscriptionId       : ff945b8d-441a-41ef-a9db-7bd5fcc99978
SubStatus            :`
```

Die Informationen im Ereignis werden wie folgt interpretiert:

| Feld | Wert | Details |
| --- | --- | --- |
| Caller |	`William.Hennum@contoso.com` | Der Prinzipal, der die Rollenzuweisung vorgenommen hat. Der Prinzipal kann ein Benutzer, eine Gruppe oder ein Dienstprinzipal sein.
| HttpRequest: Method | `PUT` | Die ausgeführte Aktion. PUT gewährt eine Zuweisung, und DELETE entfernt eine Zuordnung. |
| Properties: PrincipalId | `dda50086-5e3d-4a4b-b8bc-f54771104d89` | 	Die Objekt-ID des Prinzipals, die der Rolle zugewiesen wurde. Der Prinzipal kann ein Benutzer, eine Gruppe oder ein Dienstprinzipal sein. Sie können den Namen und Typ des Prinzipals bestimmen, indem Sie ihn mit Azure PowerShell in Azure Active Directory suchen. |
| Properties: RoleDefinitionId |	`/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7	` | Die Rolle, die zugewiesen wurde. Sie können den Anzeigenamen der Rolle mit Azure PowerShell bestimmen. |
| Properties: Scope | `/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes` |	Die Ressource, für die die Rollenzuweisung vorgenommen wurde. Dies kann eine Ressource, eine Ressourcengruppe oder ein Abonnement sein. |

###PowerShell-Beispielcodeausschnitte

Hier sehen Sie zunächst einen Azure PowerShell-Beispielcode, mit dem ein PrincipalId-Element einem Namen und Typ zugeordnet wird:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
```

Als Nächstes sehen Sie einen Azure PowerShell-Beispielcode, mit dem ein Bereich einem Ressourcennamen und Typ zugeordnet wird:

```
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
```
Hier wird als Nächstes ein Azure PowerShell-Beispielcode gezeigt, mit dem ein RoleDefinitionId-Element einem Anzeigenamen der Rolle zugeordnet wird:

```

# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}

```

###Azure PowerShell-Beispielskript

All diese Beispiele zusammen ergeben ein Beispielskript, mit dem die Rollenzuweisungsereignisse für einen bestimmten Zeitraum abgerufen und als Tabelle ausgegeben werden:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}
# Sample - output the list of role assignment events
function Get-AzureRBACAuditLog($startDateTime, $endDateTime)
{
    $log = Get-AzureSubscriptionIdLog -DetailedOutput -StartTime $startDateTime -EndTime $endDateTime
    $log = $log | ? { $_.ResourceProviderName -ieq "Microsoft.Authorization" }
    $startEvents = $log | ? { $_.httpRequest -and $_.Status -ieq "Started" }
    $endEvents = @{}
    $log | ? { $_.httpRequest -and $_.Status -ne "Started" } | % { $endEvents[$_.OperationId] = $_ }

    $startEvents | ? { $endEvents.ContainsKey($_.OperationId) } | % {
        $endEvent = $endEvents[$_.OperationId];
        $out = "" | select Timestamp, Caller, Action, PrincipalId, PrincipalName, PrincipalType, RoleName, Scope, ScopeName, ScopeType, RoleDefinitionId
        $out.Timestamp = $endEvent.EventTimestamp
        $out.Caller = $_.Caller
        if ($_.HttpRequest.Method -ieq "PUT") {
            $out.Action = "Granted"
            if ($_.Properties.Content.ContainsKey("requestbody")) {
                $messageBody = ConvertFrom-Json $_.Properties.Content["requestbody"]
            }
        }
        elseif ($_.HttpRequest.Method -ieq "DELETE") {
            $out.Action = "Revoked"
            if ($endEvent.Properties.Content.ContainsKey("responseBody")) {
                $messageBody = ConvertFrom-Json $endEvent.Properties.Content["responseBody"]
            }
        }

        if ($messageBody) {
            $out.PrincipalId = $messageBody.properties.principalId
            $pd = Get-PrincipalDetails $out.PrincipalId
            $out.PrincipalName = $pd.Name
            $out.PrincipalType = $pd.Type
            $out.RoleName = (Get-AzureRoleDefinitionName $messageBody.properties.roleDefinitionId)
            $out.Scope = $messageBody.properties.Scope
            $rd = Get-ResourceDetails $out.Scope
            $out.ScopeName = $rd.Name
            $out.ScopeType = $rd.Type
            $out.RoleDefinitionId = $messageBody.properties.roleDefinitionId
        }

        $out
    }
}

```

Und hier sind die Befehle zum Ausführen des Skripts:

```
$log = Get-AzureRBACAuditLog '2015-06-26' '2015-06-27'

$log | Format-Table
```

## Bekannte Probleme bei der Verwendung der rollenbasierten Zugriffssteuerung

Wenn Sie bei der Verwendung der rollenbasierten Zugriffssteuerung auf Probleme stoßen, finden Sie unter [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung](role-based-access-control-troubleshooting.md) bekannte Probleme, die mit dem vorliegenden Problem in Zusammenhang stehen können.


## Integrierte Rollen

Die rollenbasierte Zugriffssteuerung von Azure umfasst die folgenden integrierten Rollen, die Benutzer, Gruppen und Diensten zugewiesen werden können. Die Definition integrierter Rollen kann nicht geändert werden. In einer zukünftigen Version von Azure RBAC können Sie benutzerdefinierte Rollen definieren. Dazu können Sie aus einer Liste mit verfügbaren Aktionen eine Reihe an Aktionen auswählen, die an Azure-Ressourcen durchgeführt werden dürfen.

Klicken Sie auf den entsprechenden Link, um die **Aktions**- und **Nicht-Aktions**-Eigenschaften einer Rollendefinition anzuzeigen. Die **Aktions**-Eigenschaft gibt die zulässigen Aktionen für Azure-Ressourcen an. Für Aktionszeichenfolgen dürfen Platzhalter verwendet werden. Die **Nicht-Aktions**-Eigenschaft einer Rollendefinition gibt die Aktionen an, die von den zulässigen Aktionen ausgeschlossen werden müssen.


Rollenname | Beschreibung
------------- | -------------  
[Mitwirkender des API-Verwaltungsdienstes](#api-management-service-contributor) | Ermöglicht Ihnen die Verwaltung des API-Verwaltungsdienstes, aber nicht den Zugriff darauf.
[Mitwirkender der Application Insights-Komponente](#application-insights-component-contributor) | Ermöglicht Ihnen die Verwaltung der Application Insights-Komponenten, aber nicht den Zugriff darauf.
[Mitwirkender von BizTalk](#biztalk-contributor) | Ermöglicht Ihnen die Verwaltung der BizTalk-Dienste, aber nicht den Zugriff darauf.
[Mitwirkender von ClearDB-MySQL-DB](#cleardb-mysql-db-contributor) | Ermöglicht Ihnen die Verwaltung von ClearDB-MySQL-Datenbanken, aber nicht den Zugriff darauf.
[Mitwirkender](#contributor) | Mitwirkende können alles außer den Zugriff verwalten.
[Mitwirkender von Data Factory](#data-factory-contributor) | Ermöglicht Ihnen die Verwaltung der Data Factories, aber nicht den Zugriff darauf.
[Mitwirkender von DocumentDB-Konto](#document-db-account-contributor) | Ermöglicht Ihnen die Verwaltung von DocumentDB-Konten, aber nicht den Zugriff darauf.
[Mitwirkender von Intelligent Systems-Konto](#intelligent-systems-account-contributor) | Ermöglicht Ihnen die Verwaltung von Intelligent Systems-Konten, aber nicht den Zugriff darauf.
[Mitwirkender von NewRelic APM-Konto](#newrelic-apm-account-contributor) | Ermöglicht Ihnen die Verwaltung von New Relic Application Performance Management-Konten und -Anwendungen, aber nicht den Zugriff darauf.
[Besitzer](#owner) | Besitzer können alles, einschließlich den Zugriff, verwalten.
[Leser](#reader) | Leser können alle Dateien anzeigen, jedoch keine Änderungen vornehmen.
[Mitwirkender von Redis-Cache](#redis-cache-contributor) | Ermöglicht Ihnen die Verwaltung von Redis-Caches, aber nicht den Zugriff darauf.
[Mitwirkender von SQL DB](#sql-db-contributor) | Ermöglicht Ihnen die Verwaltung von SQL-Datenbanken, aber nicht den Zugriff darauf. Sie können nicht auch ihre sicherheitsbezogenen Richtlinien oder ihre übergeordneten SQL-Server verwalten.
[SQL-Sicherheits-Manager](#sql-security-manager) | Ermöglicht Ihnen die Verwaltung der Richtlinien mit Sicherheitsbezug von SQL-Servern und -Datenbanken, aber nicht den Zugriff darauf.
[Mitwirkender von SQL Server](#sql-server-contributor) | Ermöglicht Ihnen die Verwaltung von SQL-Servern und -Datenbanken, aber nicht den Zugriff darauf und nicht die Verwaltung ihrer Richtlinien mit Sicherheitsbezug.
[Mitwirkender von Zeitplanungsauftragssammlung](#scheduler-job-collections-contributor) | Ermöglicht Ihnen die Verwaltung von Zeitplanungsauftragssammlungen, aber nicht den Zugriff darauf.
[Mitwirkender von Suchdienst](#search-service-contributor) | Ermöglicht Ihnen die Verwaltung der Suchdienste, aber nicht den Zugriff darauf.
[Mitwirkender von Speicherkonto](#storage-account-contributor) | Ermöglicht Ihnen die Verwaltung von Speicherkonten, aber nicht den Zugriff darauf.
[Benutzerzugriffsadministrator](#user-access-administrator) | Ermöglicht Ihnen die Verwaltung von Benutzerzugriffen auf Azure-Ressourcen.
[Mitwirkender von virtuellen Computern](#virtual-machine-contributor) | Ermöglicht Ihnen die Verwaltung von virtuellen Computern, aber nicht den Zugriff darauf und nicht die Verwaltung des virtuellen Netzwerks oder Speicherkontos, mit dem sie verbunden sind.
[Mitwirkender von virtuellem Netzwerk](#virtual-network-contributor) | Ermöglicht Ihnen die Verwaltung von virtuellen Netzwerken, aber nicht den Zugriff darauf.
[Mitwirkender von Webplan](#web-plan-contributor) | Ermöglicht Ihnen die Verwaltung von Webplänen, aber nicht den Zugriff darauf.
[Mitwirkender von Website](#website-contributor) | Ermöglicht Ihnen die Verwaltung von Websites (nicht Webplänen), aber nicht den Zugriff darauf.


### Mitwirkender des API-Verwaltungsdienstes

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Erstellen und Verwalten von API-Verwaltungsdiensten</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender der Application Insights-Komponente

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Erstellen und Verwalten von Insights-Komponenten</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>Erstellen und Verwalten von Webtests</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von BizTalk

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>Erstellen und Verwalten von BizTalk-Diensten</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von ClearDB-MySQL-DB

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>Erstellen und Verwalten von ClearDB MySQL-Datenbanken</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>*</td>
<td>Erstellen und Verwalten von Ressourcen aller Typen</td>
</tr>
<tr>
<th colspan="2">Nicht-Aktionen</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>Rollen und Rollenzuweisungen können nicht erstellt werden. </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>Rollen und Rollenzuweisungen können nicht gelöscht werden.</td>
</tr>
</table>

### Mitwirkender von Data Factory

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>Erstellen und Verwalten von Data Factories</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von DocumentDB-Konto

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>Erstellen und Verwalten von DocumentDB-Konten</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von Intelligent Systems-Konto

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>Erstellen und Verwalten von Intelligent Systems-Konten</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von NewRelic APM-Konto

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>Erstellen und Verwalten von NewRelic Application Performance Management-Konten</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Besitzer

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>*</td>
<td>Erstellen und Verwalten von Ressourcen aller Typen</td>
</tr>
</table>

### Leser

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>*/Lesen</td>
<td>Lesen der Ressourcen aller Typen. Geheime Schlüssel können jedoch nicht gelesen werden.</td>
</tr>
</table>

### Mitwirkender von Redis-Cache

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Erstellen und Verwalten von Redis-Caches</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von SQL DB

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Lesen von SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>Erstellen und Verwalten von SQL-Datenbanken</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Lesen von Abonnementressourcen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Lesen von Ressourcengruppen-Ressourcen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
<tr>
<th colspan="2">Nicht-Aktionen</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Überwachungsrichtlinien von SQL-Datenbanken können nicht verwaltet werden.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Verbindungsrichtlinien von SQL-Datenbanken können nicht verwaltet werden.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Maskierungsrichtlinien von SQL-Datenbanken können nicht verwaltet werden.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Sicherheitsmetriken von SQL-Datenbanken können nicht verwaltet werden.</td>
</tr>
</table>

### SQL-Sicherheits-Manager

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Lesen von SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Erstellen und Verwalten von SQL Server-Überwachungsrichtlinien</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>Lesen von SQL-Datenbanken</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Erstellen und Verwalten von Überwachungsrichtlinien von SQL-Datenbanken</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Erstellen und Verwalten von Verbindungsrichtlinien von SQL-Datenbanken</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Erstellen und Verwalten von Maskierungsrichtlinien von SQL-Datenbanken</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Erstellen und Verwalten von Sicherheitsmetriken von SQL-Datenbanken</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von SQL Server

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>Erstellen und Verwalten von SQL-Servern</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Lesen von Abonnementressourcen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Lesen von Ressourcengruppen-Ressourcen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
<tr>
<th colspan="2">Nicht-Aktionen</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Überwachungsrichtlinien von SQL Server können nicht verwaltet werden.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Überwachungsrichtlinien von SQL-Datenbanken können nicht verwaltet werden.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Verbindungsrichtlinien von SQL-Datenbanken können nicht verwaltet werden.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Maskierungsrichtlinien von SQL-Datenbanken können nicht verwaltet werden.</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Sicherheitsmetriken von SQL-Datenbanken können nicht verwaltet werden.</td>
</tr>
</table>

### Mitwirkender von Zeitplanungsauftragssammlung

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>Erstellen und Verwalten von Zeitplanungsauftragssammlung</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von Suchdienst

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>Erstellen und Verwalten von Suchdiensten</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von Speicherkonto

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>Erstellen und Verwalten von Speicherkonten</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Benutzerzugriffsadministrator

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>*/Lesen</td>
<td>Lesen der Ressourcen aller Typen</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>Erstellen und Verwalten von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von virtuellen Computern

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>Lesen von Speicherkonten</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>Abrufen von Speicherkontoschlüsseln</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>Lesen von virtuellen Netzwerken</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>Teilnahme an virtuellen Netzwerken</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>Lesen von reservierten IP-Adressen</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>Link zu reservierten IP-Adressen</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>Erstellen und Verwalten von Clouddiensten</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>Erstellen und Verwalten von virtuellen Computern</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von virtuellem Netzwerk

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>Erstellen und Verwalten von virtuellen Netzwerken</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von Webplan

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>Erstellen und Verwalten von Webplänen</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>

### Mitwirkender von Website

<table style=width:100%">
<tr>
<th colspan="2">Aktionen</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Lesen von Webplänen</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Teilnahme an Webplänen</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>Erstellen und Verwalten von Websites</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>Erstellen und Verwalten von Websitezertifikaten</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lesen von Rollen und Rollenzuweisungen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lesen von Ressourcengruppen</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Erstellen und Verwalten von Bereitstellungen der Ressourcengruppe</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Erstellen und Verwalten von Warnungsregeln</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Erstellen und Verwalten von Supporttickets</td>
</tr>
</table>


## Feedback geben

Wir bitten Sie, Azure RBAC zu testen und uns [Feedback](http://aka.ms/azurerbacfeedback) zu senden.


## Nächste Schritte

Die folgenden Ressourcen bieten weitere Unterstützung für die Verwendung der rollenbasierten Zugriffssteuerung:

+ [Verwalten der rollenbasierten Zugriffssteuerung mit Windows PowerShell](role-based-access-control-powershell.md)
+ [Verwalten der rollenbasierten Zugriffssteuerung mit XPLAT CLI](role-based-access-control-xplat-cli-install.md)
+ [Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium und Basic](active-directory-editions.md)
+ [Verknüpfung von Azure-Abonnements mit Azure AD](active-directory-how-subscriptions-associated-directory.md)
+ Eine Einführung zur Self-Service-Gruppenverwaltung von Sicherheitsgruppen finden Sie im [Active Directory Team-Blog](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx).

<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png

<!---HONumber=Oct15_HO2-->