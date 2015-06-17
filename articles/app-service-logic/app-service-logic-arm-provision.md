<properties 
	pageTitle="Erstellen einer Logik-App" 
	description="Verwenden Sie eine Azure Resource Manager-Vorlage, um eine leere App für die Logik für das Definieren von Workflows bereitstellen." 
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

# Erstellen Sie eine Logik App mithilfe einer Vorlage

In diesem Thema erfahren Sie, wie Erstellen einer Azure-Ressourcen-Manager-Vorlage zum Erstellen einer leeren Logik-app, die zum Definieren des Workflows verwendet werden kann. Erfahren Sie, wie um zu definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder Ihren Anforderungen anpassen.

Weitere Informationen zu den Anwendungseigenschaften Logik, finden Sie unter [Logik App Workflow-Management-API](https://msdn.microsoft.com/library/azure/dn948513.aspx). Beispiele für die Definition selbst, finden Sie unter [Autor Logik App Definitionen](app-service-logic-author-definitions.md).

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Authoring Azure Resource Manager Vorlagen](../resource-group-authoring-templates.md).

Der vollständige Vorlage finden Sie unter [Logik App-Vorlage](https://github.com/tfitzmac/AppServiceTemplates/blob/master/LogicApp.json).

## Was Sie bereitstellen

Mit dieser Vorlage wird eine Logik app bereitgestellt werden.

## Parameter

[AZURE.INCLUDE [App-Service-Logik-bereitstellen-Parameter](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## Ressourcen zum Bereitstellen

### App-Dienstplan

Erstellt ein Dienstplan app.

Verwendet den gleichen Speicherort wie die Ressourcengruppe, die bereitgestellt wird.

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

### Logik app

Erstellt die Anwendung Logik.

Die Vorlagen verwendet einen Parameterwert für den Anwendungsnamen Logik. Den Speicherort der Logik app festgelegt an demselben Speicherort wie die Ressourcengruppe.

Diese bestimmten Definition wird einmal pro Stunde ausgeführt und Ping den Speicherort angegeben, der **TestUri** Parameter.

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

[AZURE.INCLUDE [App-Dienst bereitstellen Befehle](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicApp.json -ResourceGroupName ExampleDeployGroup

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicApp.json -g ExampleDeployGroup


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->