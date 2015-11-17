<properties
   pageTitle="Ressourcen-Manager-Vorlage für Ressourcenverknüpfungen | Microsoft Azure"
   description="Zeigt das Ressourcen-Manager-Vorlagenschema zum Erstellen von Verknüpfungen zwischen verwandten Ressourcen."
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
   ms.date="10/31/2015"
   ms.author="tomfitz"/>

# Ressourcenverknüpfungen – Vorlagenschema

Erstellt eine Verknüpfung zwischen zwei Ressourcen. Die Verknüpfung gehört zu einer Quellressource genannten Ressource. Die zweite Ressource in der Verknüpfung wird als Zielressource bezeichnet.

## Schemaformat

Fügen Sie zum Erstellen einer Verknüpfung das folgende Schema im Ressourcenabschnitt der Vorlage hinzu.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## Werte

In den folgenden Tabellen sind die Werte beschrieben, die Sie im Schema festlegen müssen.

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| Typ | enum | Ja | **{Namespace}/{Typ}/providers/links** | Der zu erstellende Ressourcentyp Die Werte {Namespace} und {Typ} Werte beziehen sich auf den Anbieternamespace und Ressourcentyp der Quellressource. |
| apiVersion | enum | Ja | **2015-01-01** | Die API-Version zum Erstellen der Ressource. |  
| Name | string | Ja | **{Ressource}/Microsoft.Resources/{Verknüpfungname}**<br /><br />bis zu 64 Zeichen<br />Nicht zulässig sind <, > %, &, ? bzw. alle Steuerzeichen. | Ein Wert, der den Namen der Quellressource und einen Namen für die Verknüpfung angibt. |
| dependsOn | array | Nein | Eine durch Trennzeichen getrennte Liste von Ressourcennamen oder eindeutigen Ressourcenbezeichnern. | Die Auflistung von Ressourcen, von denen diese Verknüpfung abhängt. Wenn die Ressourcen, die Sie verknüpfen, in der gleichen Vorlage bereitgestellt sind, fügen Sie diese Ressourcennamen diesem Element hinzu, um sicherzustellen, dass sie zuerst bereitgestellt werden. | 
| Eigenschaften | object | Ja | (siehe unten) | Ein Objekt, das die zu verknüpfende Ressource identifiziert, und Hinweise zur Verknüpfung. |  

### Objekt „properties“

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ------- | ---- | ---------------- | -------- | ----------- |
| targetId | string | Ja | | Der Bezeichner der zu verknüpfenden Zielressource. |
| HDInsight | string | Nein | 512 Zeichen | Beschreibung der Sperre. |


## Verwenden der Verknüpfungsressource

Sie stelle eine Verknüpfung zwischen zwei Ressourcen her, wenn die Ressourcen eine Abhängigkeit aufweisen, die nach der Bereitstellung bestehen bleibt. Beispielsweise kann eine App eine Verbindung mit einer Datenbank in einer anderen Ressourcengruppe herstellen. Sie können diese Abhängigkeit definieren, indem Sie eine Verknüpfung der App mit der Datenbank erstellen. Verknüpfungen ermöglichen Ihnen das Dokumentieren der Beziehung zwischen zwei Ressourcen. Später können Sie oder andere Personen in Ihrer Organisation eine Ressource auf Verknüpfungen abfragen, um zu ermitteln, wie die Ressource mit anderen Ressourcen zusammenarbeitet.

Alle verknüpften Ressourcen müssen zum selben Abonnement gehören. Jede Ressource kann mit 50 anderen Ressourcen verknüpft werden. Wenn verknüpfte Ressourcen gelöscht oder verschoben werden, muss der Besitzer des Links den verbleibenden Link bereinigen.

Informationen zum Arbeiten mit Verknüpfungen über REST finden Sie unter [Verknüpfte Ressourcen](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Verwenden Sie den folgenden Azure PowerShell-Befehl, um alle Verknüpfungen in Ihrem Abonnement anzuzeigen. Sie können andere Parameter angeben, um die Ergebnisse einzuschränken.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -OutputObjectFormat New

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
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
	    {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
	        "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
	    },
	    {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
    	    }
	],
	"outputs": {}
    }


## Nächste Schritte

- Informationen zur Vorlagenstruktur finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).

<!---HONumber=Nov15_HO2-->