<properties 
	pageTitle="Erstellen einer Logik-App mit einer API-App" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um eine Logik-App und eine API-App bereitzustellen." 
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

# Erstellen einer Logik-App und einer API-App mithilfe einer Vorlage

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, um eine Logik-App mit einer App Service-API-App zu erstellen. Sie können die Logik-App verwenden, um Workflows zu entwerfen, in denen ein beabsichtigter Ablauf mithilfe eines Triggers und einer Reihe von Schritten ausgedrückt wird. Bei jedem Schritt wird eine API-App aufgerufen, und zugleich wird für eine sichere Authentifizierung und beständige Ausführung gesorgt.

Sie erfahren, wie Sie definieren, welche Ressourcen bereitgestellt werden, und wie Sie Parameter definieren, die beim Ausführen der Bereitstellung angegeben werden. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Weitere Informationen zu den Eigenschaften der Logik-App finden Sie unter [API für die Workflowverwaltung einer Logik-App](https://msdn.microsoft.com/library/azure/dn948513.aspx). Beispiele für die Definition selbst finden Sie unter [Schreiben von Logik-App-Definitionen](app-service-logic-author-definitions.md).

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Die vollständige Vorlage finden Sie unter [Logik-App mit API-App-Vorlage](https://github.com/tfitzmac/AppServiceTemplates/blob/master/LogicAppAndAPIApp.json).

## Was Sie bereitstellen

Mit dieser Vorlage stellen Sie Folgendes bereit:

- Logik-App
- API-App

## Parameter

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### apiAppName

Der Name der API-App.

    "apiAppName": {
        "type": "string"
    }

### gatewayName

Der Name des Gateways.

    "gatewayName": {
        "type": "string"
    }

### gatewayToApiAppSecret

Der geheime Schlüssel für die API-App.

    "gatewayToApiAppSecret": {
        "defaultValue": "0000000000000000000000000000000000000000000000000000000000000000",
        "type": "securestring"
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

### Web-App zum Hosten des Gateways

Erstellt die Web-App, die das Gateway hostet.

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
                        "value": "D:\home\data\apiapps"
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

Beachten Sie, dass das Gateway eine untergeordnete Ressource für ein Authentifizierungstoken enthält. Dieses Token wird von der Logik-App zum Aufrufen des Gateways verwendet.

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

### Web-App zum Hosten der API-App

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

### API-App

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

### Logik-App

Erstellt die Logik-App.

Eine Logik-App erfordert einen Namen, einen Speicherort, eine SKU (die auf den App Service-Plan zeigt), eine Definition sowie optional Parameter.

Beachten Sie, dass die Logik-App das Token verwendet, um das Gateway aufzurufen.

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

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicAppAndAPIApp.json -ResourceGroupName ExampleDeployGroup

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/LogicAppAndAPIApp.json -g ExampleDeployGroup


 

<!---HONumber=62-->