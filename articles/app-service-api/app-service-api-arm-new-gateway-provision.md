<properties 
	pageTitle="Bereitstellen einer API-Anwendung, mit der ein neues gateway" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um eine API-Anwendung mit einer neuen Gateway und neue App Dienstplan bereitzustellen." 
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

# Bereitstellen einer API-Anwendung mit einem neuen gateway

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage zu erstellen, die eine Azure-API-Anwendung und ein Gateway bereitstellt. Erfahren Sie, wie um zu definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder Ihren Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Authoring Azure Resource Manager Vorlagen](../resource-group-authoring-templates.md).

Der vollständige Vorlage finden Sie unter [API-Anwendung mit neuen Gateway-Vorlage](https://github.com/tfitzmac/AppServiceTemplates/blob/master/new-gateway-new-plan-new-apiapp.json).

## Was Sie bereitstellen

In dieser Vorlage können Sie Folgendes bereitstellen:

- API-Anwendung
- Neues gateway
- neue App hosting Dienstplan

## Parameter

[AZURE.INCLUDE [App-Dienst-api-bereitstellen-Parameter](../../includes/app-service-api-deploy-parameters.md)]
    
## Variablen

Diese Vorlage definiert eine Variable, die beim Bereitstellen von Ressourcen verwendet wird.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
Der Wert wird als unten verwendet **variables('packageId')**.

## Ressourcen zum Bereitstellen

### Webhosting-plan

Den Plan für die API-app-hosting-Dienst wird erstellt.

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

### Web-app, die das Gateway hostet.

Erstellt eine Webanwendung, die das Gateway hostet.

Beachten Sie, dass **Art** minFreeThreads auf **Gateway** der Azure-Portal teilt mit, dass diese Webanwendung einen Gateway gehostet wird. Das Portal wird die Webanwendung aus dem Durchsuchen Web app Blade ausgeblendet. Ein Link wird zwischen der Hostanwendung und dem Gateway definiert. Der Abschnitt für die Anwendungseinstellungen enthält die erforderlichen Werte für das hosting der API-Anwendung.


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

### Geteway

Das Gateway wird erstellt.

Die hostenden Webanwendung wird als Eigenschaft des Gateways definiert.

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

### Web-app, die die API-Anwendung hostet.

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

### API-Anwendung

Erstellt die API-Anwendung.

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

[AZURE.INCLUDE [App-Dienst bereitstellen Befehle](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/new-gateway-new-plan-new-apiapp.json

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/new-gateway-new-plan-new-apiapp.json


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->