<properties
   pageTitle="Ressourcen-Manager-Vorlage für Rollenzuweisungen | Microsoft Azure"
   description="Zeigt das Ressourcen-Manager-Schema für die Bereitstellung einer Rollenzuweisung über eine Vorlage."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="tomfitz"/>

# Vorlagenschema für Rollenzuweisungen

Weist einen Benutzer, eine Gruppe oder einen Dienstprinzipal einer Rolle in einem angegebenen Bereich zu.

## Schemaformat

Fügen Sie zum Erstellen einer Rollenzuweisung das folgende Schema im Ressourcenabschnitt der Vorlage hinzu:
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## Werte

In den folgenden Tabellen sind die Werte beschrieben, die Sie im Schema festlegen müssen.

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| Typ | enum | Ja | **Microsoft.Authorization/roleAssignments** | Der zu erstellende Ressourcentyp. |
| apiVersion | enum | Ja | **2015-07-01** | Die API-Version zum Erstellen der Ressource. |  
| name | string | Ja | Globally Unique Identifier (GUID) | Ein Bezeichner für die neue Rollenzuweisung |
| dependsOn | array | Nein | Eine durch Trennzeichen getrennte Liste von Ressourcennamen oder eindeutigen Ressourcenbezeichnern. | Die Auflistung der Ressourcen, von denen diese Rollenzuweisung abhängt. Wenn Sie eine Rolle zuweisen, die auf eine Ressource beschränkt ist, und diese Ressource in derselben Vorlage bereitgestellt wird, nehmen Sie den Ressourcennamen in dieses Element auf, um sicherzustellen, dass die Ressource zuerst bereitgestellt wird. | 
| Eigenschaften | Objekt | Ja | (siehe unten) | Ein Objekt, das die Rollendefinition, den Prinzipal und den Bereich bezeichnet |  

### properties-Objekt

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ------- | ---- | ---------------- | -------- | ----------- |
| roleDefinitionId | string | Ja | **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** | Der Bezeichner einer vorhandenen Rollendefinition, der für die Rollenzuweisung verwendet werden soll. |
| principalId | string | Ja | Globally Unique Identifier (GUID) | Der Bezeichner eines vorhandenen Prinzipals. Dieser Bezeichner wird der ID im Verzeichnis zugeordnet. Er kann auf einen Benutzer, Dienstprinzipal oder eine Sicherheitsgruppe verweisen. |
| Bereich | string | Ja | Für eine Ressourcengruppe:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**<br /><br />Für eine Ressource:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** | Der Bereich, für den diese Rollenzuweisung gilt |


## Verwenden der Rollenzuweisungsressource

Sie fügen Ihrer Vorlage eine Rollenzuweisung zu, wenn Sie einer Rolle während der Bereitstellung einen Benutzer, eine Gruppe oder einen Dienstprinzipal hinzufügen müssen. Rollenzuweisungen werden von höheren Bereichsebenen geerbt. Wenn Sie also einer Rolle bereits auf Abonnementebene einen Prinzipal hinzugefügt haben, müssen Sie ihn nicht erneut für die Ressourcengruppe oder die Ressource zuordnen.

Sie können wie folgt für **name** einen neuen Bezeichner generieren:

    PS C:\> $name = [System.Guid]::NewGuid().toString()

Sie können die GUID für eine Rollendefinition mit folgendem Befehl abrufen:

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Sie können den Bezeichner des Prinzipals mit einem der folgenden Befehle abrufen.

Für eine Gruppe namens **Auditors**:

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Für einen Benutzer namens **exampleperson**:

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Für einen Dienstprinzipal namens **exampleapp**:

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## Beispiele

Das folgende Beispiel weist eine Gruppe zu einer Rolle für die Ressourcengruppe zu.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }

## Schnellstartvorlagen

Die folgenden Vorlagen zeigen, wie die Rollenzuweisungsressource verwendet wird:

- [Zuweisen einer integrierten Rolle zu einer Ressourcengruppe](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)
- [Zuweisen einer integrierten Rolle zu einem vorhandenen virtuellen Computer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)
- [Zuweisen einer integrierten Rolle zu mehreren vorhandenen virtuellen Computer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)

## Nächste Schritte

- Informationen zur Vorlagenstruktur finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
- Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0107_2016-->