<properties 
	pageTitle="Bereitstellen einer Web-App, die mit einem GitHub-Repository verknüpft ist" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um eine Web-App bereitzustellen, die ein Projekt aus einem GitHub-Repository enthält." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2015" 
	ms.author="tomfitz"/>

# Bereitstellen einer Web-App in einem GitHub-Repository

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, die eine Web-App bereitstellt, die mit einem Projekt in einem GitHub-Repository verknüpft ist. Sie erfahren, wie Sie definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Die vollständige Vorlage finden Sie unter [Web-App verknüpft mit GitHub-Vorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Was Sie bereitstellen

Mit dieser Vorlage stellen Sie eine Web-App mit dem Code aus einem Projekt in GitHub bereit.

Klicken Sie auf folgende Schaltfläche, um die Bereitstellung automatisch auszuführen:

[![Bereitstellen in Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## Parameter

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### repoURL

URL des GitHub-Repositorys, das das bereitzustellende Projekt enthält. Dieser Parameter enthält einen Standardwert, der aber nur den Zweck hat, Ihnen zu zeigen, wie Sie die URL für das Repository angeben. Sie können diesen Wert verwenden, wenn Sie die Vorlage testen. Sie müssen allerdings die URL Ihres eigenen Repositorys angeben, wenn Sie mit der Vorlage arbeiten.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### Verzweigung

Die beim Bereitstellen der Anwendung zu verwendende Verzweigung des Repositorys. Der Standardwert ist "master", doch Sie können den Namen einer beliebigen Verzweigung im Repository angeben, die Sie bereitstellen möchten.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## Bereitzustellende Ressourcen

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Web-App

Erstellt die Web-App, die mit dem Projekt in GitHub verknüpft ist.

Sie geben den Namen der Webanwendung über den **siteName**-Parameter und den Speicherort der Web-App über den **siteLocation**-Parameter an. Im **dependsOn**-Element definiert die Vorlage die Web-App als abhängig vom Diensthostingplan. Da sie vom Hostingplan abhängig ist, wird die Web-App erst erstellt, wenn der Hostingplan vollständig erstellt wurde. Das **dependsOn**-Element wird nur verwendet, um die Bereitstellungsreihenfolge anzugeben. Wenn Sie die Webanwendung nicht als abhängig vom Webhostingplan markieren, versucht der Azure-Ressourcen-Manager, beide Ressourcen zur gleichen Zeit zu erstellen, und Sie erhalten möglicherweise einen Fehler, wenn die Web-App vor dem Hostingplan erstellt wird.

Die Web-App verfügt auch über eine untergeordnete Ressource, die unter **Ressourcen** weiter unten definiert wird. Diese untergeordnete Ressource definiert die Quellcodeverwaltung für das Projekt, das mit der Web-App bereitgestellt wird. In dieser Vorlage wird die Quellcodeverwaltung mit einem bestimmten GitHub-Repository verknüpft. Das GitHub-Repository wird durch den Code **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"** definiert. Sie können die Repository-URL hart codieren, wenn Sie eine Vorlage erstellen möchten, die ein einzelnes Projekt wiederholt bereitstellt, während die minimale Anzahl von Parametern erforderlich ist. Anstelle einer hart codierten Repository-URL können Sie auch einen Parameter für die Repository-URL hinzufügen und diesen Wert für die **RepoUrl**-Eigenschaft verwenden. Sie sehen ein Beispiel für den Repository-URL-Parameter in der [Web-App mit der Vorlage für Webaufträge](../app-service-web-deploy-web-app-with-webjobs.md).

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## Befehle zum Ausführen der Bereitstellung

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 

<!---HONumber=AcomDC_1217_2015-->