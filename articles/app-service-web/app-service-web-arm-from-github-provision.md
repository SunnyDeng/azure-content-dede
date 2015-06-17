<properties 
	pageTitle="Bereitstellen einer Webanwendung, die Repositorys verknüpft ist" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um eine Webanwendung bereitstellen, die ein Projekt von einem GitHub-Repository enthält." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# Bereitstellen einer Webanwendung mit Repositorys verknüpft

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, die eine Webanwendung bereit, die mit einem Projekt in Repositorys verknüpft ist. Erfahren Sie, wie um zu definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder Ihren Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Authoring Azure Resource Manager Vorlagen](../resource-group-authoring-templates.md).

Der vollständige Vorlage finden Sie unter [Web App verknüpfte GitHub Vorlage](https://github.com/tfitzmac/AppServiceTemplates/blob/master/WebAppLinkedToGithub.json).

## Was Sie bereitstellen

Mit dieser Vorlage stellen Sie eine Webanwendung mit dem Code aus einem Projekt in GitHub.

## Parameter

[AZURE.INCLUDE [App-Service-Web-Deploy-Web-Parameters](../../includes/app-service-web-deploy-web-parameters.md)]
    
## Ressourcen zum Bereitstellen

[AZURE.INCLUDE [App-Service-Web-Deploy-Web-Host](../../includes/app-service-web-deploy-web-host.md)]

### Web-app

Erstellt die Webanwendung, die dem Projekt in GitHub verknüpft ist.

Geben Sie den Namen der Webanwendung über die **SiteName** Parameter und den Speicherort der Webanwendung über die **SiteLocation** Parameter. In der **DependsOn** -Element, die Vorlage definiert die Webanwendung als hosting Plan Dienst abhängig. Da es den hostingplan abhängig ist, wird die Webanwendung erst erstellt, Webhosting-Plan abgeschlossen ist, erstellt wird. Die **DependsOn** Element wird nur verwendet, um Bereitstellungsreihenfolge anzugeben. Wenn Sie die Webanwendung als abhängig von den Webhosting-Plan nicht aktivieren, Azure Resource Manager – wird versucht, beide Ressourcen zur gleichen Zeit zu erstellen, und Sie möglicherweise ein Fehler auf, wenn die Webanwendung vor der Webhosting-Plan erstellt wird.

Die Webanwendung verfügt auch über eine untergeordnete Ressource definiert in **Ressourcen** weiter unten. Diese untergeordnete Ressource definiert die Quellcodeverwaltung für das Projekt mit der Webanwendung bereitgestellt. In dieser Vorlage wird das Datenquellen-Steuerelement mit bestimmten Repositorys verknüpft. Das GitHub-Repository wird durch den Code definiert **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"** Sie möglicherweise hart zu kodieren, die Repository-URL beim Erstellen einer Vorlage, die ein einzelnes Projekt wiederholt bereitstellt, erforderlich ist die minimale Anzahl von Parametern werden sollen. Anstelle von hart codierte URL des Repositorys, auch einen Parameter für die Repository-URL hinzufügen und diesen Wert für die **RepoUrl** Eigenschaft. Sehen Sie ein Beispiel für Repository-URL-Parameter in der [Webanwendung mit der Vorlage der Webaufträge](../app-service-web-deploy-web-app-with-webjobs.md).

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

[AZURE.INCLUDE [App-Dienst bereitstellen Befehle](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->