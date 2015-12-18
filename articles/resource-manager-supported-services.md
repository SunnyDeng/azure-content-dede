<properties
   pageTitle="Vom Ressourcen-Manager unterstützte Dienste und Regionen | Microsoft Azure"
   description="Beschreibt die Ressourcenanbieter, die den Ressourcen-Manager und die Regionen unterstützen, die die Ressourcen hosten können."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/01/2015"
   ms.author="tomfitz"/>

# Ressourcen-Manager-Unterstützung für Dienste, Regionen und API-Versionen

Der Azure-Ressourcen-Manager bietet eine neue Möglichkeit, die Dienste, aus denen Ihre Anwendungen bestehen, bereitzustellen und zu verwalten. Die meisten (jedoch nicht alle) Dienste unterstützen den Ressourcen-Manager, und einige Dienste unterstützen ihn nur teilweise. Microsoft ermöglicht den Ressourcen-Manager für jeden Dienst, der für zukünftige Lösungen wichtig ist. Bis diese Unterstützung jedoch konsistent ist, müssen Sie den aktuellen Status jedes Diensts kennen. Dieses Thema enthält eine Liste der unterstützten Ressourcenanbieter für den Azure-Ressourcen-Manager.

Beim Bereitstellen der Ressourcen müssen Sie auch wissen, welche Bereiche diese Ressourcen unterstützen und welche API-Versionen für die Ressourcen verfügbar sind. Der Abschnitt [Unterstützte Regionen](#supported-regions) zeigt, wie Sie herausfinden, in welchen Regionen Ihr Abonnement und Ihre Ressourcen verwendet werden können. Im Abschnitt [Unterstützte API-Versionen](#supported-api-versions) wird gezeigt, wie Sie ermitteln, welche API-Versionen Sie verwenden können.

Die folgenden Tabellen zeigen, welche Dienste Bereitstellung und Verwaltung über den Ressourcen-Manager unterstützen und welche nicht. Die Spalte mit der Bezeichnung **Ressourcen verschieben** bezieht sich darauf, ob Ressourcen dieses Typs in eine neue Ressourcengruppe und ein neues Abonnement verschoben werden können. Die Spalte mit der Bezeichnung **Portal** gibt an, ob Sie den Dienst über das [Azure-Portal](https://portal.azure.com) erstellen können.


## Compute

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Virtual Machines | Ja | Ja, mehrere Optionen | Nein       | [VM erstellen](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Batch   | Ja     | [Ja (nur klassisch)](https://portal.azure.com/#create/Microsoft.BatchAccount) | Ja | [Batch REST (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn820158.aspx) |        |
| Dynamics Lifecycle Services | Ja | Nein |    |      |        |
| Virtuelle Computer (klassisch) | Eingeschränkt | Ja, mehrere Optionen | Teilweise (siehe unten) | - | - |
| Remote-App | Nein   | Nein | -              | -        | -      |
| Service Fabric | Nein | -           | -        | -      |

"Virtuelle Computer (klassisch)" bezieht sich auf Ressourcen, die über das klassische Bereitstellungsmodell statt über das Ressourcen-Manager-Bereitstellungsmodell bereitgestellt wurden. Im Allgemeinen unterstützen diese Ressourcen keine Ressourcen-Manager-Vorgänge, es wurden jedoch einige Vorgänge aktiviert. Weitere Informationen zu diesen Bereitstellungsmodellen finden Sie unter [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](resource-manager-deployment-model.md).

Ressourcen des Typs "Virtuelle Computer (klassisch)" können in neue Ressourcengruppen verschoben werden, nicht jedoch in ein neues Abonnement.

## Netzwerk

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Application Gateway | Ja | | | | |
| DNS | Ja | | | [Erstellen einer DNS-Zone](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Lastenausgleichsmodul | Ja | | | [Erstellen oder Aktualisieren eines Lastenausgleichs](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Virtuelle Netzwerke | Ja | [Ja](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | Nein | [Erstellen eines virtuellen Netzwerks](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Traffic Manager | Ja | Nein | | [Erstellen oder Aktualisieren eines Traffic Manager-Profils](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| ExpressRoute | Ja | Nein | Nein | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |

## Daten und Speicher

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | Ja | [Ja](https://portal.azure.com/#create/Microsoft.DocumentDB) | Ja | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| Storage | Ja | [Ja](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) | Nein | [Erstellen des Speicherkontos](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [Speicherkonto](resource-manager-template-storage.md) |
| Redis-Cache | Ja | [Ja](https://portal.azure.com/#create/Microsoft.Cache.1.0.4) | Ja | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| SQL-Datenbank | Ja | [Ja](https://portal.azure.com/#create/Microsoft.SQLDatabase.0.5.9-preview) | Ja | [Erstellen einer Datenbank](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| Suchen | Ja | [Ja](https://portal.azure.com/#create/Microsoft.Search) | Ja | [Azure-Suchdienst-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL Data Warehouse | Ja | [Ja](https://portal.azure.com/#create/Microsoft.SQLDataWarehouse.0.1.12-preview) | | | |
| StorSimple | Nein | Nein | - | - | - | | Verwalteter Cache | Nein | Nein | - | - | - |

## Web- und mobile Anwendungen

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| API Management | Ja | Nein | Ja | [API erstellen](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| API-Apps | Ja | [Ja](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| Web-Apps | Ja | [Ja](https://portal.azure.com/#create/Microsoft.WebSite) | Ja, mit Einschränkungen (siehe unten) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Notification Hubs | Ja | [Ja](https://portal.azure.com/#create/Microsoft.NotificationHub) | Ja | [Notification Hub erstellen:](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| Logik-Apps | Ja | [Ja](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) | Ja | | |
| Mobile Engagements | Ja | Nein | Ja | | |

Bei der Arbeit mit Web-Apps können Sie nicht nur einen App Services-Plan verschieben. Zum Verschieben von Web-Apps stehen folgende Optionen bereit:

- Verschieben Sie alle Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe, wenn die Zielressourcengruppe noch keine Microsoft.Web-Ressourcen enthält.
- Verschieben Sie Web-Apps in eine andere Ressourcengruppe, behalten Sie jedoch den App Services-Plan in der ursprünglichen Ressourcengruppe bei.

## Analyse

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Event Hub | Ja   | Nein |         | [Event Hub erstellen](https://msdn.microsoft.com/library/azure/dn790676.aspx) |        |
| Stream Analytics | Ja | [Ja](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) |        |          |        |
| HDInsights | Ja  | [Ja](https://portal.azure.com/#create/Microsoft.HDInsightCluster) | Ja     |          |        |
| Data Factory | Ja | [Ja](https://portal.azure.com/#create/Microsoft.DataFactory) | Ja | [Erstellen einer Data Factory](https://msdn.microsoft.com/library/azure/dn906717.aspx) |    |
| Machine Learning | Nein | Nein | -          | -        | -      |
| Data Catalog | Nein | Nein |  -             | -        | -       |

## Medien und CDN

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| CDN | Ja (Vorschau) | Nein | | | |
| Mediendienst | Nein | Nein | | | |


## Hybridintegration

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| BizTalk Services | Ja | Nein | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Service Bus | Ja | Nein | | [Service Bus REST-API-Referenz](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |
| Backup | Nein | Nein | - | - | - | | Site Recovery | Nein | Nein | - | - | - |

## Identitäts- und Zugriffsverwaltung 

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | Nein | Nein | - | - | - | | Azure Actice Directory B2C | Nein | Nein | - | - | - | | Multi-Factor Authentication | Nein | Nein | - | - | - |

## Entwicklerdienste 

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Ja | [Ja](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) | Nein | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing Maps | Ja | [Ja](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) | | | |
| Visual Studio-Konto | Ja | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## Verwaltung 

| Dienst | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Automation | Ja | [Ja](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) | Ja | | |
| Schlüsseltresor | Ja | Nein | Ja | [Referenz zur Schlüsseltresor-REST-API](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Scheduler | Ja | Nein | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | Ja | Nein | Ja | | |
| IoTHubs | Ja | [Ja](https://portal.azure.com/#create/Microsoft.IotHub) | | | |

## Ressourcen-Manager

| Funktion | Ressourcen-Manager aktiviert | Portal | Ressourcen verschieben | REST-API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Autorisierung | Ja | N/V | N/V | [Verwaltungssperren](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Rollenbasierte Zugriffssteuerung](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Ressourcensperre](resource-manager-template-lock.md)<br />[Rollenzuweisungen](resource-manager-template-role.md) |
| Ressourcen | Ja | N/V | N/V | [Verknüpfte Ressourcen](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Ressourcenlinks](resource-manager-template-links.md) |


## Unterstützte Regionen

Bei der Bereitstellung von Ressourcen müssen Sie in der Regel eine Region für die Ressourcen angeben. Der Ressourcen-Manager wird in allen Regionen unterstützt, aber die Ressourcen, die Sie bereitstellen, werden möglicherweise nicht in allen Regionen unterstützt. Darüber hinaus liegen möglicherweise Einschränkungen Ihres Abonnements vor, die verhindern, dass Sie einige Regionen verwenden, die die Ressource unterstützen. Diese Einschränkungen können im Zusammenhang mit steuerlichen Problemen in Ihrem Land stehen, oder Sie sind das Ergebnis einer Richtlinie, die von Ihrem Abonnementadministrator implementiert wurde, sodass Sie nur bestimmte Regionen verwenden können.

Eine vollständige Liste aller unterstützten Regionen für alle Azure-Dienste finden Sie unter [Dienste nach Region](https://azure.microsoft.com/regions/#services). Diese Liste kann jedoch Regionen enthalten, die nicht von Ihrem Abonnement abgedeckt werden. Sie können die Regionen für einen bestimmten von Ihrem Abonnement unterstützten Ressourcentyp ermitteln, indem Sie einen der folgenden Befehle ausführen.

### REST-API

Verwenden Sie für die Ermittlung der verfügbaren Regionen für einen bestimmten Ressourcentyp im Abonnement den Vorgang [Auflisten aller Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

Das folgende Beispiel zeigt, wie Sie die unterstützten Regionen für Websites mithilfe von Azure PowerShell 1.0 abrufen. Weitere Informationen zur Version 1.0 finden Sie unter [Azure PowerShell 1.0 ](https://azure.microsoft.com/blog/azps-1-0/) (in englischer Sprache).

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
Die Ausgabe ähnelt der folgenden:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

Verwenden Sie für Azure PowerShell 0.9.8 den folgenden Befehl:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Azure-Befehlszeilenschnittstelle

Im folgenden Beispiel werden alle unterstützten Orte für jeden Ressourcentyp zurückgegeben.

    azure location list

Sie können die Ergebnisse auch mit einem Tool wie **jq** filtern. Informationen zu Tools wie „jq“ finden Sie unter [Nützliche Tools für die Interaktion mit Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Ausgabe des Befehls:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## Unterstützte API-Versionen

Beim Bereitstellen einer Vorlage müssen Sie eine API-Version angeben, die zum Erstellen der einzelnen Ressourcen verwendet werden soll. Die API-Version entspricht einer Version von REST-API-Vorgängen, die vom Ressourcenanbieter freigegeben werden. Wenn ein Ressourcenanbieter neue Features aktiviert, wird eine neue Version der REST-API veröffentlicht. Deshalb wirkt sich die Version der API, die Sie in Ihrer Vorlage angeben, darauf aus, welche Eigenschaften Sie in der Vorlage angeben können. Im Allgemeinen sollten Sie die neueste Version der API wählen, wenn Sie neue Vorlagen erstellen. Für vorhandene Vorlagen können Sie entscheiden, ob Sie weiterhin eine frühere Version der API verwenden oder die Vorlage für die aktuelle Version aktualisieren möchten, um die neuen Features zu nutzen.

### REST-API

Verwenden Sie den Vorgang zum [Auflisten aller Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790524.aspx), um zu ermitteln, welche API-Versionen für Ressourcentypen verfügbar sind.

### PowerShell

Im folgenden Beispiel wird veranschaulicht, wie Sie die verfügbaren API-Versionen für einen bestimmten Ressourcentyp mit Azure PowerShell 1.0 abrufen.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
Die Ausgabe ähnelt der folgenden:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Verwenden Sie für Azure PowerShell 0.9.8:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure-Befehlszeilenschnittstelle

Sie können die Informationen (einschließlich der verfügbaren API-Versionen) für einen Ressourcenanbieter mit dem folgenden Befehl in einer Datei speichern.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Sie können die Datei öffnen und nach dem Element **apiVersions** suchen.

## Nächste Schritte

- Weitere Informationen zum Erstellen von Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
- Informationen zum Bereitstellen von Vorlagen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_1203_2015-->
