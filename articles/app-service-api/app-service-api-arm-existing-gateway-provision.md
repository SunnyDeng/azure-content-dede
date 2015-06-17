<properties 
	pageTitle="Bereitstellung einer API-Anwendung mit einem vorhandenen gateway" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um eine API-Anwendung bereitstellen, die ein vorhandenes Gateway und App-Service-Plan verwendet." 
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
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# Bereitstellen einer API-Anwendung mit einem vorhandenen gateway

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage zu erstellen, die eine Azure-API-Anwendung und ein vorhandenes Gateway bereitstellt. Erfahren Sie, wie um zu definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder Ihren Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Authoring Azure Resource Manager Vorlagen](../resource-group-authoring-templates.md).

Der vollständige Vorlage finden Sie unter [API-App mit vorhandenen Gateway-Vorlage](https://github.com/tfitzmac/AppServiceTemplates/blob/master/existing-gateway-existing-plan-new-apiapp.json).

## Was Sie bereitstellen

In dieser Vorlage werden Sie eine API-Anwendung bereitstellen, die einen vorhandenen hosting Plan und ein vorhandenes Gateway App-Dienst zugeordnet ist.

## Parameter

[AZURE.INCLUDE [App-Dienst-api-bereitstellen-Parameter](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

Der Bezeichner des vorhandenen hosting Plans.

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

Die Einstellungen der vorhandenen Hosting planen.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "hostingEnvironment": ""
      }
    }

## Variablen

Diese Vorlage definiert eine Variable, die beim Bereitstellen von Ressourcen verwendet wird.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
Der Wert wird als unten verwendet **variables('packageId')**.

## Ressourcen zum Bereitstellen

### Web-app an die-API-Hostanwendung

Erstellt eine Webanwendung, die die API-Anwendung hostet.

Beachten Sie, dass **Art** minFreeThreads auf **ApiApp** der Azure-Portal teilt mit, dass diese Webanwendung einen Gateway gehostet wird. Das Portal wird die Webanwendung aus dem Durchsuchen Web app Blade ausgeblendet. Die Anwendung enthält eine Erweiterung, um das standardmäßige leere-API app-Paket installieren. Ein Link wird zwischen der API-Anwendung und der hostenden Webanwendung definiert. Der Abschnitt für die Anwendungseinstellungen enthält die erforderlichen Werte für das hosting der API-Anwendung.

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

### API-Anwendung

Die API-Anwendung zu erstellen.

Beachten Sie, dass die Namen der hostenden Webanwendung und Gateway als Eigenschaften in der API-Anwendung definiert sind.

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

[AZURE.INCLUDE [App-Dienst bereitstellen Befehle](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/existing-gateway-existing-plan-new-apiapp.json

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/existing-gateway-existing-plan-new-apiapp.json


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->