<properties 
	pageTitle="Bereitstellen einer Redis Cache-Instanz" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um einen Azure Redis Cache bereitzustellen." 
	services="redis-cache" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="tomfitz"/>

# Erstellen einer Redis Cache-Instanz mithilfe einer Vorlage

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, die einen Azure Redis Cache bereitstellt. Sie erfahren, wie Sie definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Die vollständige Vorlage finden Sie unter [Redis Cache-Vorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

## Was Sie bereitstellen

In dieser Vorlage stellen Sie einen Azure Redis Cache bereit:

Klicken Sie auf folgende Schaltfläche, um die Bereitstellung automatisch auszuführen:

[![Bereitstellen in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## Parameter

Mit dem Azure-Ressourcen-Manager definieren Sie die Parameter für Werte, die Sie bei der Bereitstellung der Vorlage angeben möchten. Die Vorlage enthält einen Abschnitt namens "Parameters", der alle Parameterwerte enthält. Definieren Sie einen Parameter für die Werte, die basierend auf dem bereitgestellten Projekt oder der bereitgestellten Umgebung variieren. Definieren Sie keine Parameter für Werte, die sich nicht ändern. Jeder Parameterwert wird in der Vorlage verwendet, um die bereitgestellten Ressourcen zu definieren.

Jeder Parameter wird in der Vorlage beschrieben.

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Der Speicherort der Redis Cache-Instanz. Um eine optimale Leistung zu erzielen, verwenden Sie den gleichen Speicherort wie für die App, die den Cache nutzen wird.

    "redisCacheLocation": {
      "type": "string"
    }

    
## Bereitzustellende Ressourcen

### Redis-Cache

Erstellt den Azure Redis Cache.

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "sku": {
          "name": "[parameters('redisCacheSKU')]",
          "family": "[parameters('redisCacheFamily')]",
          "capacity": "[parameters('redisCacheCapacity')]"
        },
        "redisVersion": "[parameters('redisCacheVersion')]",
        "enableNonSslPort": true
      }
    }
     



## Befehle zum Ausführen der Bereitstellung

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=July15_HO3-->