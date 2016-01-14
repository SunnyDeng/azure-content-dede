<properties
   pageTitle="Ressourcen-Manager-Vorlage für Ressourcensperren | Microsoft Azure"
   description="Zeigt das Ressourcen-Manager-Schema für Ressourcensperren an."
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
   ms.date="10/25/2015"
   ms.author="tomfitz"/>

# Ressourcensperren – Vorlagenschema

Erstellt eine neue Sperre für eine Ressource und ihre untergeordneten Ressourcen.

## Schemaformat

Fügen Sie zum Erstellen einer Sperre das folgende Schema im Ressourcenabschnitt der Vorlage hinzu.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## Werte

In den folgenden Tabellen sind die Werte beschrieben, die Sie im Schema festlegen müssen.

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| Typ | enum | Ja | Für Ressourcen: <br />**{Namespace}/{Typ}/providers/locks**<br /><br />Für Ressourcengruppen:<br />**Microsoft.Authorization/locks** | Der zu erstellende Ressourcentyp. |
| apiVersion | enum | Ja | **2015-01-01** | Die API-Version zum Erstellen der Ressource. |  
| name | string | Ja | Für Ressourcen:<br />**{Ressource}/Microsoft.Authorization/{Sperrname}**<br /><br />Für Ressourcengruppen:<br />**{Sperrname}****<br /><br />Bis zu 64 Zeichen<br />Darf nicht die Zeichen „<, >, %, &, ?“ und keine Steuerzeichen enthalten. | Ein Wert, der sowohl die zu sperrende Ressource als auch einen Namen für die Sperre angibt. |
| dependsOn | Array | Nein | Eine durch Trennzeichen getrennte Liste mit Ressourcennamen oder eindeutigen Ressourcenbezeichnern. | Die Sammlung von Ressourcen, von denen diese Sperre abhängt. Wenn die gesperrte Ressource in derselben Vorlage bereitgestellt wird, fügen Sie den Namen dieser Ressource in diesem Element ein, um sicherzustellen, dass die Ressource zuerst bereitgestellt wird. |
| properties | Objekt | Ja | (siehe unten) | Ein Objekt, das den Typ der Sperre angibt, und Hinweise zu der Sperre. | 

### properties-Objekt

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ------- | ---- | ---------------- | -------- | ----------- |
| level | enum | Ja | **CannotDelete** <br /> **ReadOnly** | Der Typ der Sperre, der auf den Bereich angewendet wird. Bei „CannotDelete“ können Änderungen vorgenommen werden, jedoch keine Löschvorgänge durchgeführt. Bei „ReadOnly“ sind weder Änderungen noch Löschvorgänge möglich. |
| HDInsight | string | Nein | 512 Zeichen | Beschreibung der Sperre. |


## Verwenden der gesperrten Ressource

Sie fügen diese Ressource Ihrer Vorlage hinzu, um zu verhindern, dass bestimmte Aktionen für eine Ressource ausgeführt werden. Die Sperre gilt für alle Benutzer und Gruppen. In der Regel wenden Sie eine Sperre nur für einen bestimmten Zeitraum an, z. B. wenn ein Prozess ausgeführt wird und Sie sicherstellen möchten, dass niemand in Ihrer Organisation eine Ressource versehentlich ändert oder löscht.

Zum Erstellen oder Löschen von Verwaltungssperren müssen Sie Zugriff auf Aktionen vom Typ **Microsoft.Authorization/*** oder **Microsoft.Authorization/locks/*** haben. Von den integrierten Rollen verfügen nur **Owner** (Besitzer) und **User Access Administrator** (Benutzerzugriffsadministrator) über diese Aktionen. Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Access Control in Azure](./active-directory/role-based-access-control-configure.md).

Die Sperre wird auf die angegebene Ressource und alle untergeordneten Ressourcen angewendet. Wenn Sie mehr als eine Sperre auf eine Ressource anwenden, hat jeweils die restriktivste Sperre Vorrang. Wenn Sie beispielsweise „ReadOnly“ auf der übergeordneten Ebene (z. B. für die Ressourcengruppe) und „CannotDelete“ auf eine Ressource in dieser Gruppe anwenden, hat die restriktivere Sperre (ReadOnly) der übergeordneten Ebene Vorrang.

Sie können eine Sperre mit dem PowerShell-Befehl **Remove-AzureRmResourceLock** oder mit dem [Löschvorgang](https://msdn.microsoft.com/library/azure/mt204562.aspx) der REST-API entfernen.

## Beispiele

Im folgenden Beispiel wird eine Schreibschutzsperre für eine Web-App angewendet.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
      			"type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "ReadOnly",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

Im nächsten Beispiel wird eine Schreibschutzsperre auf die Ressourcengruppe angewendet.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "ReadOnly",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## Nächste Schritte

- Informationen zur Vorlagenstruktur finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
- Weitere Informationen zu Sperren finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](resource-group-lock-resources.md).

<!----HONumber=AcomDC_1223_2015-->