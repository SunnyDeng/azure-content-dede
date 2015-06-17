<properties 
	pageTitle="Erstellen Sie eine Logik-App mit einer API-Anwendung" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um eine Logik-App und einer API-Anwendung bereitzustellen." 
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

# Erstellen Sie eine Logik App sowie API-app mithilfe einer Vorlage

In diesem Thema erfahren Sie, wie eine Azure-Ressourcen-Manager-Vorlage zum Erstellen einer app, Logik mit einer App-Dienst-API-Anwendung zu erstellen. Sie können die app Logik zum Entwerfen von Workflows, die Absicht, durch einen Trigger und eine Reihe von Schritten, jede die API-Anwendung aufrufen, während die sichere Authentifizierung und best Practices wie dauerhafte Ausführung kümmern formulieren.

Erfahren Sie, wie um zu definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder Ihren Anforderungen anpassen.

Weitere Informationen zu den Anwendungseigenschaften Logik, finden Sie unter [Logik App Workflow-Management-API](https://msdn.microsoft.com/library/azure/dn948513.aspx). Beispiele für die Definition selbst, finden Sie unter [Autor Logik App Definitionen](app-service-logic-author-definitions.md).

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Authoring Azure Resource Manager Vorlagen](../resource-group-authoring-templates.md).

Der vollständige Vorlage finden Sie unter [Logik-Anwendung mit API-app-Vorlage](https://github.com/tfitzmac/AppServiceTemplates/blob/master/LogicAppAndAPIApp.json).

## Was Sie bereitstellen

Mit dieser Vorlage können Sie Folgendes bereitstellen:

- Logik App
- API-Anwendung

## Parameter

[AZURE.INCLUDE [App-Service-Logik-bereitstellen-Parameter](../../includes/app-service-logic-deploy-parameters.md)]

### apiAppName

Der Name der API-app.

    "apiAppName": {
        "type": "string"
    }

### gatewayName

Der Name des Gateways.

    "gatewayName": {
        "type": "string"
    }

### gatewayToApiAppSecret

Der geheime Schlüssel für die API-Anwendung.

    "gatewayToApiAppSecret": {
        "defaultValue": "0000000000000000000000000000000000000000000000000000000000000000",
        "type": "securestring"
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

### Web-app, die das Gateway hostet.

Erstellt die Webanwendung als host für das Gateway.

    {
        "type": "Microsoft.Web/sites",
        "apiVersion": "2015-04-01",
        "name": "[parameters('gatewayName')]",
        "location": "[resourceGroup().location]",
        "kind": "gateway",
        "tags": {
            "displayName": "GatewayHost"
        },
        "resources": [
            {
                "type": "providers/links",
                "apiVersion": "2015-01-01",
                "name": "Microsoft.Resources/gateway",
                "dependsOn": [
                    "[resourceId('Microsoft.Web/sites',parameters('gatewayName'))]"
                ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
                }
            }
        ],
        "dependsOn": [
            "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
        ],
        "properties": {
            "name": "[parameters('gatewayName')]",
            "gatewaySiteName": "[parameters('gatewayName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('svcPlanName'))]",
            "siteConfig": {
                "appSettings": [
                    {
                        "name": "ApiAppsGateway_EXTENSION_VERSION",
                        "value": "latest"
                    },
                    {
                        "name": "EmaStorage",
                        "value": "D:\\home\\data\\apiapps"
                    },
                    {
                        "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
                        "value": "1"
                    }
                ]
            }
        }
    }

### Gateway

Beachten Sie, dass das Gateway eine untergeordnete Ressource für ein Authentifizierungstoken enthält. Dieses Token wird von der Anwendung Logik zum Aufrufen des Gateways verwendet.

    {
        "type": "Microsoft.AppService/gateways",
        "apiVersion": "2015-03-01-preview",
        "name": "[parameters('gatewayName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "Gateway"
        },
        "resources": [
            {
                "type": "providers/links",
                "apiVersion": "2015-01-01",
                "name": "Microsoft.Resources/gatewaySite",
                "dependsOn": [
                    "[resourceId('Microsoft.AppService/gateways',parameters('gatewayName'))]"
                ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Web/sites',parameters('gatewayName'))]"
                }
            },
            {
                "type": "tokens",
                "apiVersion": "2015-03-01-preview",
                "location": "[resourceGroup().location]",
                "name": "[parameters('logicAppName')]",
                "tags": {
                    "displayName": "AuthenticationToken"
                },
                "dependsOn": [
                    "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
                ]
            }
        ],
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
        ],
        "properties": {
            "host": {
                "resourceName": "[parameters('gatewayName')]"
            }
        }
    }

### Webanwendung zum Hosten der API-app

    {
        "type": "Microsoft.Web/sites",
        "apiVersion": "2015-04-01",
        "name": "[parameters('apiAppName')]",
        "location": "[resourceGroup().location]",
        "kind": "apiApp",
        "tags": {
            "displayName": "APIAppHost"
        },
        "dependsOn": [
            "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
        ],
        "resources": [
            {
                "type": "siteextensions",
                "tags": {
                    "displayName": "APIAppExtension"
                },
                "apiVersion": "2015-04-01",
                "name": "[variables('$packageId')]",
                "dependsOn": [
                    "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
                ],
                "properties": {
                    "type": "WebRoot",
                    "feed_url": "[variables('$nugetFeed')]"
                }
            },
            {
                "type": "providers/links",
                "apiVersion": "2015-01-01",
                "name": "Microsoft.Resources/apiApp",
                "dependsOn": [
                    "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
                ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
                }
            }
        ],
        "properties": {
            "name": "[parameters('apiAppName')]",
            "gatewaySiteName": "[parameters('gatewayName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('svcPlanName'))]",
            "siteConfig": {
                "appSettings": [
                    {
                        "name": "EMA_MicroserviceId",
                        "value": "[parameters('apiAppName')]"
                    },
                    {
                        "name": "EMA_Secret",
                        "value": "[parameters('gatewayToAPIappSecret')]"
                    },
                    {
                        "name": "EMA_RuntimeUrl",
                        "value": "[concat('https://', reference(resourceId('Microsoft.Web/sites', parameters('gatewayName'))).hostNames[0])]"
                    },
                    {
                        "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
                        "value": "1"
                    }
                ]
            }
        }
    }

### API-Anwendung

    {
        "type": "Microsoft.AppService/apiapps",
        "apiVersion": "2015-03-01-preview",
        "name": "[parameters('apiAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "APIApp"
        },
        "resources": [
            {
                "type": "providers/links",
                "apiVersion": "2015-01-01",
                "name": "Microsoft.Resources/apiAppSite",
                "dependsOn": [
                    "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
                ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
                }
            }
        ],
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), 'Microsoft.ApiApp')]"
        ],
        "properties": {
            "package": {
                "id": "Microsoft.ApiApp"
            },
            "host": {
                "resourceName": "[parameters('apiAppName')]"
            },
            "gateway": {
                "resourceName": "[parameters('gatewayName')]"
            },
            "dependencies": [ ]
        }
    }

### Logik app

Erstellt die Anwendung Logik.

Eine Logik-app benötigt einen Namen, Speicherort, SKU (diese App Dienstplan zeigt), Definition und optional Parameter.

Beachten Sie, dass die Logik app das Token verwendet, um das Gateway aufzurufen.

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "dependsOn": [
            "[resourceId('Microsoft.AppService/apiApps', parameters('apiAppName'))]"
        ],
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
                    "token": {
                        "defaultValue": "[reference(resourceId('Microsoft.AppService/gateways/tokens', parameters('gatewayName'), parameters('logicAppName'))).token]",
                        "type": "String",
                        "metadata": {
                            "token": {
                                "name": "token"
                            }
                        }
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
                    "getValues": {
                        "type": "ApiApp",
                        "inputs": {
                            "apiVersion": "2015-01-14",
                            "host": {
                                "id": "[concat(resourceGroup().id, '/providers/Microsoft.AppServices/apiApps/',parameters('apiAppName'))]",
                                "gateway": "[concat('https://', reference(resourceId('Microsoft.Web/sites', parameters('gatewayName'))).hostNames[0])]"
                            },
                            "operation": "Values_Get",
                            "parameters": { },
                            "authentication": {
                                "type": "Raw",
                                "scheme": "Zumo",
                                "parameter": "@parameters('token')"
                            }
                        }
                    }
                },
                "outputs": {
                    "result": {
                        "type": "array",
                        "value": "@body('readValues')"
                    }
                }
            },
            "parameters": { }
        }
    }

## Befehle zum Ausführen der Bereitstellung

[AZURE.INCLUDE [App-Dienst bereitstellen Befehle](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicAppAndAPIApp.json -ResourceGroupName ExampleDeployGroup

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicAppAndAPIApp.json -g ExampleDeployGroup


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->