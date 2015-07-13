<properties 
	pageTitle="Bereitstellen einer API-App mit einem vorhandenen Gateway" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage zum Bereitstellen einer API-App, die ein vorhandenes Gateway und einen App Service-Plan verwendet." 
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
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>

# Bereitstellen einer API-App mit einem vorhandenen Gateway

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, die eine Azure API-App und ein vorhandenes Gateway bereitstellt. Sie erfahren, wie Sie definieren, welche Ressourcen bereitgestellt werden, und wie Sie Parameter definieren, die beim Ausführen der Bereitstellung angegeben werden. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Weitere Informationen zum Bereitstellen von Apps finden Sie unter [Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).

Die vollständige Vorlage finden Sie unter [Vorlage für API-App mit vorhandenem Gateway](../../templates/app-service-api-arm-existing-gateway-provision/).

## Was Sie bereitstellen

In dieser Vorlage stellen Sie eine API-App bereit, die einem vorhandenen App Service-Hostingplan und einem vorhandenen Gateway zugeordnet wird.

## Parameter

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

Der Bezeichner des vorhandenen Hostingplans.

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

Die Einstellungen des vorhandenen Hostingplans.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
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

### Web-App zum Hosten einer API-App

Erstellt eine Web-App zum Hosten der API-App.

Beachten Sie, dass **kind** auf **apiApp** festgelegt ist. Hierdurch wird das Azure-Portal darüber informiert, dass diese Web-App ein Gateway hostet. Das Portal blendet die Web-App auf dem Blatt zum Durchsuchen der Web-App aus. Die App umfasst eine Erweiterung zum Installieren des leeren API-App-Standardpakets. Zwischen der API-App und der hostenden Web-App wird ein Link definiert. Der Abschnitt mit den App-Einstellungen enthält die erforderlichen Werte zum Hosten der API-App.

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
        "serverFarmId": "[parameters('hostingPlanId')]",
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
              "value": "[concat('https://', parameters('gatewayName'), '.azurewebsites.net')]"
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
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]"
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

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/existing-gateway-existing-plan-new-apiapp.json

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/existing-gateway-existing-plan-new-apiapp.json


 

<!---HONumber=62-->