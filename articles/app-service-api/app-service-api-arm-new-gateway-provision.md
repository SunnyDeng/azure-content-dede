<properties 
	pageTitle="Bereitstellen einer API-App mit einem neuen Gateway" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage zum Bereitstellen einer API-App mit einem neuen Gateway und einem neuen App Service-Plan." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="tomfitz"/>

# Bereitstellen einer API-App mit einem neuen Gateway

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, die eine Azure API-App und ein Gateway bereitstellt. Sie erfahren, wie Sie definieren, welche Ressourcen bereitgestellt werden, und wie Sie Parameter definieren, die beim Ausführen der Bereitstellung angegeben werden. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Weitere Informationen zum Bereitstellen von Apps finden Sie unter [Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).

Die vollständige Vorlage finden Sie unter [Vorlage für API-App mit neuem Gateway](https://github.com/Azure/azure-quickstart-templates/blob/master/201-api-app-gateway-new/azuredeploy.json).

## Was Sie bereitstellen

In dieser Vorlage stellen Sie Folgendes bereit:

- Eine API-App
- Ein neues Gateway
- Einen neuen App Service-Hostingplan

Klicken Sie auf folgende Schaltfläche, um die Bereitstellung automatisch auszuführen:

[![Bereitstellen in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-app-gateway-new%2Fazuredeploy.json)

## Parameter

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanSettings

Die Einstellungen für den neuen Hostingplan.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "computeMode": "Dedicated",
        "siteMode": "Limited",
        "sku": "Standard",
        "workerSize": "0",
        "hostingEnvironment": ""
      }
    }
    
## Variablen

Diese Vorlage definiert eine Variable, die beim Bereitstellen der Ressourcen verwendet wird.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
Der Wert wird nachstehend als **variables('packageId')** verwendet.

## Bereitzustellende Ressourcen

### Hostingplan

Erstellt den Diensthostingplan für die API-App.

    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2015-04-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "properties": {
        "name": "[parameters('hostingPlanName')]",
        "sku": "[parameters('hostingPlanSettings').sku]",
        "workerSize": "[parameters('hostingPlanSettings').workerSize]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "numberOfWorkers": 1
      }
    }

### Web-App zum Hosten des Gateways

Erstellt eine Web-App, die das Gateway hostet.

Beachten Sie, dass **kind** auf **gateway** festgelegt ist. Hierdurch wird das Azure-Portal darüber informiert, dass diese Web-App ein Gateway hostet. Das Portal blendet die Web-App auf dem Blatt zum Durchsuchen von Web-Apps aus. Zwischen der hostenden Web-App und dem Gateway wird ein Link definiert. Der Abschnitt mit den App-Einstellungen enthält die erforderlichen Werte zum Hosten der API-App.


    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('gatewayName')]",
      "location": "[parameters('location')]",
      "kind": "gateway",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/gateway",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('gatewayName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
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
      },
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ]
    }

### Gateway

Erstellt das Gateway.

Die hostende Web-App ist als eine Eigenschaft des Gateways definiert.

    {
      "type": "Microsoft.AppService/gateways",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('gatewayName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/gatewaySite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
          }
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

Erstellt eine Web-App zum Hosten der API-App.

Beachten Sie, dass **kind** auf **apiApp** festgelegt ist. Hierdurch wird das Azure-Portal darüber informiert, dass diese Web-App eine API-App hostet. Das Portal blendet die Web-App auf dem Blatt zum Durchsuchen von Web-Apps aus. Die App umfasst eine Erweiterung zum Installieren des leeren API-App-Standardpakets. Zwischen der API-App und der hostenden Web-App wird ein Link definiert. Der Abschnitt mit den App-Einstellungen enthält die erforderlichen Werte zum Hosten der API-App.

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
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
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
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
      },
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]",
        "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
      ]
    }

### API-App

Erstellt die API-App.

Beachten Sie, dass die Namen der hostenden Web-App und des Gateways als Eigenschaften in der API-App definiert sind.

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
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
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]",
        "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }

## Befehle zum Ausführen der Bereitstellung

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-new/azuredeploy.json

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-new/azuredeploy.json


 

<!---HONumber=July15_HO3-->