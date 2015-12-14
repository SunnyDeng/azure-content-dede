<properties 
	pageTitle="Bereitstellen einer Redis Cache-Instanz" 
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um einen Azure Redis Cache bereitzustellen." 
	services="app-service" 
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
	ms.date="10/28/2015" 
	ms.author="tomfitz"/>

# Erstellen einer Redis Cache-Instanz mithilfe einer Vorlage

In diesem Thema erfahren Sie, wie Sie eine Azure-Ressourcen-Manager-Vorlage erstellen, die einen Azure Redis Cache bereitstellt. Der Cache kann mit einem vorhandenen Speicherkonto verwendet werden, um Diagnosedaten aufzunehmen. Sie erfahren, wie Sie definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Derzeit werden für alle Caches in derselben Region für ein Abonnement dieselben Diagnoseeinstellungen verwendet. Ein Aktualisieren eines Cache in der Region wirkt sich auf alle anderen Caches in der Region aus.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Die vollständige Vorlage finden Sie unter [Redis Cache-Vorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE]ARM-Vorlagen für den neuen [Tarif „Premium“](cache-premium-tier-intro.md) sind verfügbar.
>
>-    [Erstellen eines Premium-Redis Caches mit Clustering](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Erstellen eines Premium-Redis Caches mit Datenpersistenz](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Erstellen eines Premium-Redis Caches mithilfe von VNet und optionalem Clustering](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Um die neuesten Vorlagen zu ermitteln, suchen Sie in [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/) nach `Redis Cache`.

## Was Sie bereitstellen

In dieser Vorlage stellen Sie einen Azure Redis Cache bereit, der ein vorhandenes Speicherkonto für Diagnosedaten verwendet.

Klicken Sie auf folgende Schaltfläche, um die Bereitstellung automatisch auszuführen:

[![Bereitstellen in Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## Parameter

Mit dem Azure-Ressourcen-Manager definieren Sie die Parameter für Werte, die Sie bei der Bereitstellung der Vorlage angeben möchten. Die Vorlage enthält einen Abschnitt namens "Parameters", der alle Parameterwerte enthält. Definieren Sie einen Parameter für die Werte, die basierend auf dem bereitgestellten Projekt oder der bereitgestellten Umgebung variieren. Definieren Sie keine Parameter für Werte, die sich nicht ändern. Jeder Parameterwert wird in der Vorlage verwendet, um die bereitgestellten Ressourcen zu definieren.

Jeder Parameter wird in der Vorlage beschrieben.

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Der Speicherort der Redis Cache-Instanz. Um eine optimale Leistung zu erzielen, verwenden Sie den gleichen Speicherort wie für die App, die den Cache nutzen wird.

    "redisCacheLocation": {
      "type": "string"
    }

### diagnosticsStorageAccountName

Der Name des vorhandenen Speicherkontos, das für Diagnosen verwendet werden soll.

    "diagnosticsStorageAccountName": {
      "type": "string"
    }

### enableNonSslPort

Ein boolescher Wert, der angibt, ob Zugriff über Nicht-SSL-Ports zugelassen werden soll.

    "enableNonSslPort": {
      "type": "bool"
    }

### diagnosticsStatus

Ein Wert, der angibt, ob Diagnosen aktiviert sind. Verwenden Sie ON oder OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## Bereitzustellende Ressourcen

### Redis-Cache

Erstellt den Azure Redis Cache.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "redisVersion": "[parameters('redisCacheVersion')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
        "resources": [
          {
            "apiVersion": "2014-04-01",
            "type": "diagnosticSettings",
            "name": "service", 
            "location": "[parameters('redisCacheLocation')]",
            "dependsOn": [
              "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
            ],
            "properties": {
              "status": "[parameters('diagnosticsStatus')]",
              "storageAccountName": "[parameters('diagnosticsStorageAccountName')]",
              "retention": "30"
            }
          }
        ]
    }

## Befehle zum Ausführen der Bereitstellung

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Azure-Befehlszeilenschnittstelle

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=AcomDC_1203_2015-->