<properties
   pageTitle="Verwalten und Überwachen des Zugriffs auf Ressourcen"
   description="Verwenden Sie die rollenbasierte Zugriffssteuerung (RBAC) zum Verwalten von Berechtigungen für in Azure bereitgestellten Ressourcen."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="tomfitz"/>

# Verwalten des Zugriffs auf Ressourcen

Mit dem Azure-Ressourcen-Manager können Sie sicherstellen, dass die Benutzer in Ihrer Organisation über die entsprechenden Berechtigungen verfügen, um Ressourcen zu verwalten oder darauf zuzugreifen. Der Ressourcen-Manager nutzt die rollenbasierte Zugriffssteuerung (RBAC). Daher können Sie einfach Sicherheitsrichtlinien auf einzelne Ressourcen oder Ressourcengruppen anwenden. Sie können z. B. einem Benutzer Zugriff auf einen bestimmten virtuellen Computer in einem Abonnement gewähren oder es einem Benutzer ermöglichen, alle Websites in einem Abonnement zu verwalten, jedoch keine weiteren Ressourcen.

Dieses Thema konzentriert sich auf die Befehle zum Zuweisen von Rollen und Berechtigungen. Eine Übersicht über die rollenbasierte Zugriffssteuerung finden Sie unter [Rollenbasierte Zugriffssteuerung im Microsoft Azure-Portal](../active-directory/role-based-access-control-configure.md).

## Konzepte

Es gibt einige Schlüsselkonzepte bei der rollenbasierten Zugriffssteuerung, die Sie kennen sollten:

1. Prinzipal - Entität, der die Berechtigung erteilt wurde, wie z. B. ein Benutzer, eine Sicherheitsgruppe oder eine Anwendung.
2. Rolle - Satz zulässiger Aktionen
3. Umfang - Ebene, für die eine Rolle gilt, z. B. Abonnement, Ressourcengruppe oder Ressource.
3. Rollenzuweisung – Prozess des Zuweisens eines Prinzipals zu einer Rolle und des Festlegens des Umfangs

## Beispiele für Rollen
Um RBAC-Konzepte zu verstehen, betrachten wir einige Beispiele allgemeiner Rollendefinitionen:

| Rolle | Zulässige Aktionen |
| ------- | ----------------- |
| Reader | **/Lesen (Lesezugriff für alles) | | Besitzer | * (Lese-/Schreibzugriff für alles) |

Um die Rolle **Leser** zu **Benutzer A** für die Ressourcengruppe mit dem Namen **ExampleGroup** und die Rolle **Besitzer** zu **Benutzer B** für das gesamte Abonnement zuzuweisen, würden Sie Folgendes zuweisen:

| Rolle | Prinzipal | Umfang |
| --------|-------------|---------- |
| Leser | Benutzer A | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| Besitzer | Benutzer B | /subscriptions/{subscriptionId} |

## Beispielszenarien

In diesem Abschnitt sehen Sie, wie Sie die folgenden gängigen Szenarien in Azure PowerShell, der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows und der REST-API durchführen.

1. Anzeigen der verfügbaren Rollen in einem Abonnement und der zulässigen Aktionen für diese Rollen
2. Erteilen der Berechtigungen von Lesern an Mitglieder einer Gruppe für das Abonnement.
3. Erteilen der Berechtigungen von Mitwirkenden an eine Anwendung, um der Anwendung das Verwalten von Ressourcen innerhalb der Ressourcengruppe zu ermöglichen
4. Erteilen der Berechtigungen von Besitzern für eine bestimmte Website an einen einzelnen Benutzer
5. Auflisten von Überwachungsprotokollen der Ressourcengruppe


## Verwenden von PowerShell zum Verwalten des Zugriffs

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]


### Anzeigen verfügbarer Rollen
Um alle verfügbaren Rollen für Ihr Abonnement anzuzeigen, führen Sie den Befehl **Get-AzureRmRoleDefinition** aus.

    PS C:\> Get-AzureRmRoleDefinition
    
    Name             : API Management Service Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage API Management services, but not access to them.
    Actions          : {Microsoft.ApiManagement/Services/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read,
                       Microsoft.Resources/subscriptions/resourceGroups/resources/read...}
    NotActions       : {}
    AssignableScopes : {/}

    Name             : Application Insights Component Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage Application Insights components, but not access to them.
    Actions          : {Microsoft.Insights/components/*, Microsoft.Insights/webtests/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read...}
    NotActions       : {}
    AssignableScopes : {/}
    ...

### Erteilen der Berechtigung von Lesern an eine Gruppe für das Abonnement
1. Überprüfen Sie die Rollendefinition für **Leser**, indem Sie den Rollennamen bereitstellen, wenn Sie den Befehl **Get-AzureRmRoleDefinition** ausführen. Überprüfen Sie, ob die zulässigen Aktionen die sind, die Sie zuweisen möchten.

        PS C:\> Get-AzureRmRoleDefinition Reader
   
        Name             : Reader
        Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
        IsCustom         : False
        Description      : Lets you view everything, but not make any changes.
        Actions          : {*/read}
        NotActions       : {}
        AssignableScopes : {/}

2. Rufen Sie die erforderliche Sicherheitsgruppe durch Ausführen des Befehls **Get-AzureRmADGroup** ab. Geben Sie den tatsächlichen Namen der Gruppe in Ihrem Abonnement an. ExampleAuditorGroup ist unten dargestellt.

        PS C:\> $group = Get-AzureRmAdGroup -SearchString ExampleAuditorGroup

3. Erstellen Sie die Rollenzuweisung für die Sicherheitsgruppe "Auditor". Wenn der Befehl abgeschlossen ist, wird die neue Rollenzuweisung zurückgegeben.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader


###Erteilen der Berechtigung von Mitwirkenden an eine Anwendung für eine Ressourcengruppe
1. Überprüfen Sie die Rollendefinition für **Mitwirkende**, indem Sie den Rollennamen bereitstellen, wenn Sie den Befehl **Get-AzureRmRoleDefinition** ausführen. Überprüfen Sie, ob die zulässigen Aktionen die sind, die Sie zuweisen möchten.

        PS C:\> Get-AzureRmRoleDefinition Contributor

2. Rufen Sie die Objekt-ID des Dienstprinzipals ab, indem Sie den Befehl **Get-AzureRmADServicePrincipal** ausführen und den Namen der Anwendung in Ihrem Abonnement bereitstellen. ExampleApplication ist unten dargestellt.

        PS C:\> $service = Get-AzureRmADServicePrincipal -SearchString ExampleApplicationName

3. Erstellen Sie die Rollenzuweisungen für den Dienstprinzipal, indem Sie den Befehl **New-AzureRmRoleAssignment** ausführen.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Eine ausführlichere Erläuterung der Einrichtung einer Azure Active Directory-Anwendung und eines Dienstprinzipals finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](../resource-group-authenticate-service-principal.md).

###Erteilen der Berechtigungen von Besitzern an einen Benutzer für eine Ressource
1. Überprüfen Sie die Rollendefinition für **Besitzer**, indem Sie den Rollennamen bereitstellen, wenn Sie den Befehl **Get-AzureRmRoleDefinition** ausführen. Überprüfen Sie, ob die zulässigen Aktionen die sind, die Sie zuweisen möchten.

        PS C:\> Get-AzureRmRoleDefinition Owner

2. Erstellen Sie die Rollenzuweisungen für den Benutzer.

        PS C:\> New-AzureRmRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Auflisten von Überwachungsprotokollen der Ressourcengruppe
Um das Überwachungsprotokoll für eine Ressourcengruppe abzurufen, führen Sie den Befehl **Get-AzureRmLog** (oder **Get-AzureResourceGroupLog** für Azure PowerShell vor Version 1.0 Preview) aus.

      PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroupName

## Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows

Wenn Sie die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows nicht installiert haben oder Ihr Organisationskonto nicht für die Verwendung mit der Azure-Befehlszeilenschnittstelle konfiguriert haben, finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) weitere Informationen.

1. Melden Sie sich mit Ihren Anmeldeinformationen bei Ihrem Azure-Konto an. Der Befehl gibt das Ergebnis der Anmeldung zurück.

        azure login

        ...
        info:    login command OK

2. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

        azure account set <YourSubscriptionNameOrId>

3. Wechseln Sie zum Azure-Ressourcen-Manager-Modul. Der neue Modus wird bestätigt.

        azure config mode arm
        
        info:     New mode is arm

### Anzeigen verfügbarer Rollen
Zeigen Sie alle verfügbaren Rollen für Ihr Abonnement an. Es wird eine Liste von Rollendefinitionen zurückgegeben.

    azure role list

### Erteilen der Berechtigung von Lesern an eine Gruppe für das Abonnement
1. Rufen Sie die Rollendefinition für die Rolle **Leser** ab. Überprüfen Sie, ob die zulässigen Aktionen die sind, die Sie zuweisen möchten.

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. Rufen Sie die erforderliche Sicherheitsgruppe und die Objekt-ID ab, indem Sie nach der Gruppe basierend auf dem Namen suchen. Im folgenden Beispiel wird ExampleAuditorGroup gezeigt.

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. Erstellen Sie die Rollenzuweisung für die Sicherheitsgruppe.

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### Erteilen der Berechtigung von Mitwirkenden an eine Anwendung für eine Ressourcengruppe
1. Rufen Sie die Rollendefinition für die Rolle **Mitwirkender** ab. Überprüfen Sie, ob die zulässigen Aktionen die sind, die Sie zuweisen möchten.

        azure role show Contributor

2. Rufen Sie die Objekt-ID für die Anwendung ab. Geben Sie den Namen der abzurufenden Anwendung an.

        azure ad sp show --search ExampleApplicationName

2. Erstellen Sie die Rollenzuweisung für die Anwendung.

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###Erteilen der Berechtigungen von Besitzern an einen Benutzer für eine bestimmte Website
1. Rufen Sie die Rollendefinition für die Rolle **Besitzer** ab. Überprüfen Sie, ob die zulässigen Aktionen die sind, die Sie zuweisen möchten.

        azure role show Owner

2. Erstellen Sie die Rollenzuweisung für den Benutzer.

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###Auflisten von Überwachungsprotokollen der Ressourcengruppe
Um das Überwachungsprotokoll für eine Ressourcengruppe abzurufen, führen Sie die den Befehl **azure group log show** aus, und geben Sie den Namen der gewünschten Ressourcengruppe an.

         azure group log show ExampleGroupName


## Verwenden der REST-API
Um die rollenbasierte Zugriffssteuerung über die REST-API im Azure-Ressourcen-Manager zu verwalten, müssen Sie allgemeine Header und Parameter (einschließlich Authentifizierungstoken) beim Senden von Anforderungen festlegen. Weitere Informationen finden Sie unter [Allgemeine Parameter und Header](https://msdn.microsoft.com/library/azure/dn906885.aspx).
   
Führen Sie zum Ermitteln der unterstützten API-Versionen folgenden Befehl aus:

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

Sie können die Version `2014-10-01-preview` für dieses Thema verwenden.

###Erstellen einer Rollenzuweisung
Rufen Sie die verfügbaren Rollendefinitionen ab.

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

Erstellen Sie die Rollenzuweisung.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###Löschen einer Rollenzuweisung

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## Nächste Schritte

- Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Zugriffssteuerung im Microsoft Azure-Portal](../role-based-access-control-configure.md).
- Weitere Informationen zum Arbeiten mit Dienstprinzipalen zum Verwalten des Zugriffs für Anwendungen in Ihrem Abonnement finden Sie unter [Authentifizieren eines Dienstprinzipals über Azure Resource Manager](../resource-group-authenticate-service-principal.md) und [Erstellen eines neuen Azure-Dienstprinzipals über das klassische Azure-Portal](../resource-group-create-service-principal-portal.md).
- Weitere Informationen zum Überwachen von Vorgängen in Ihrer Organisation finden Sie unter [Überwachen von Vorgängen mit dem Ressourcen-Manager](../resource-group-audit.md).

 

<!---HONumber=Oct15_HO3-->