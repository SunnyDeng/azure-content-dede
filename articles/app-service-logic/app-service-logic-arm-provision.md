<properties 
	pageTitle="Erstellen einer Logik-App" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um eine leere Logik-App zum Definieren von Workflows bereitzustellen." 
	services="app-service\logic" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# Erstellen einer Logik-App mithilfe einer Vorlage

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, um eine leere Logik-App zu erstellen, die zum Definieren von Workflows verwendet werden kann. Sie erfahren, wie Sie definieren, welche Ressourcen bereitgestellt werden, und wie Sie Parameter definieren, die beim Ausführen der Bereitstellung angegeben werden. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Weitere Informationen zu den Eigenschaften der Logik-App finden Sie unter [API für die Workflowverwaltung einer Logik-App](https://msdn.microsoft.com/library/azure/dn948513.aspx). Beispiele für die Definition selbst finden Sie unter [Schreiben von Logik-App-Definitionen](app-service-logic-author-definitions.md).

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Die vollständige Vorlage finden Sie unter [Logik-App-Vorlage](https://github.com/tfitzmac/AppServiceTemplates/blob/master/LogicApp.json).

## Was Sie bereitstellen

Mit dieser Vorlage stellen Sie eine Logik-App bereit.

## Parameter

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/de-de/status/feed/"
      }
    
## Bereitzustellende Ressourcen

### App Services-Plan

Erstellt einen App Services-Plan.

Der Plan verwendet den gleichen Speicherort wie die Ressourcengruppe, in der er bereitgestellt wird.

    {
        "apiVersion": "2014-06-01",
        "name": "[parameters('svcPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "AppServicePlan"
        },
        "properties": {
            "name": "[parameters('svcPlanName')]",
            "sku": "[parameters('sku')]",
            "workerSize": "[parameters('svcPlanSize')]",
            "numberOfWorkers": 1
        }
    }

### Logik-App

Erstellt die Logik-App.

Die Vorlage erstellt einen Parameterwert für den Namen der Logik-App. Sie legt den Speicherort der Logik-App auf den Speicherort für die Ressourcengruppe fest.

Diese spezifische Definition wird einmal pro Stunde ausgeführt und pingt den im Parameter **testUri** angegebenen Speicherort.

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "testURI": {
                        "type": "string",
                        "defaultValue": "[parameters('testUri')]"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "recurrence",
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                },
                "actions": {
                    "http": {
                        "type": "Http",
                        "inputs": {
                            "method": "GET",
                            "uri": "@parameters('testUri')"
                        }
                    }
                },
                "outputs": { }
            },
            "parameters": { }
        }
    }

## Befehle zum Ausführen der Bereitstellung

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicApp.json -ResourceGroupName ExampleDeployGroup

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicApp.json -g ExampleDeployGroup


 

<!---HONumber=62-->