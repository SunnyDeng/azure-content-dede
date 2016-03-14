<properties
   pageTitle="Vom Ressourcen-Manager unterstützte Dienste, Regionen, Schemas und Versionen | Microsoft Azure"
   description="Beschreibt die Ressourcenanbieter, die den Ressourcen-Manager, die zugehörigen Schemas und verfügbaren API-Versionen sowie die Regionen unterstützen, die die Ressourcen hosten können."
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
   ms.date="03/01/2016"
   ms.author="tomfitz"/>

# Anbieter, Regionen, API-Versionen und Schemas für den Ressourcen-Manager

Der Azure-Ressourcen-Manager bietet eine neue Möglichkeit, die Dienste, aus denen Ihre Anwendungen bestehen, bereitzustellen und zu verwalten. Die meisten (jedoch nicht alle) Dienste unterstützen den Ressourcen-Manager, und einige Dienste unterstützen ihn nur teilweise. Microsoft ermöglicht den Ressourcen-Manager für jeden Dienst, der für zukünftige Lösungen wichtig ist. Bis diese Unterstützung jedoch konsistent ist, müssen Sie den aktuellen Status jedes Diensts kennen. Dieses Thema enthält eine Liste der unterstützten Ressourcenanbieter für den Azure-Ressourcen-Manager.

Beim Bereitstellen der Ressourcen müssen Sie auch wissen, welche Bereiche diese Ressourcen unterstützen und welche API-Versionen für die Ressourcen verfügbar sind. Der Abschnitt [Unterstützte Regionen](#supported-regions) zeigt, wie Sie herausfinden, in welchen Regionen Ihr Abonnement und Ihre Ressourcen verwendet werden können. Im Abschnitt [Unterstützte API-Versionen](#supported-api-versions) wird gezeigt, wie Sie ermitteln, welche API-Versionen Sie verwenden können.

Informationen dazu, welche Dienste im Azure-Portal und im klassischen Portal unterstützt werden, finden Sie im [Verfügbarkeitsdiagramm für die Azure-Portale](https://azure.microsoft.com/features/azure-portal/availability/). Informationen dazu, welche Dienste das Verschieben von Ressourcen ermöglichen, finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](resource-group-move-resources.md).

Die folgenden Tabellen zeigen, welche Dienste Bereitstellung und Verwaltung über den Ressourcen-Manager unterstützen und welche nicht. Der Link in der Spalte **Schnellstartvorlagen** sendet eine Abfrage an das Azure-Repository mit Schnellstartvorlagen für den angegebenen Ressourcenanbieter. Schnellstartvorlagen werden häufig hinzugefügt und aktualisiert. Dass ein Link für einen bestimmten Dienst vorhanden ist, bedeutet nicht unbedingt, dass durch die Abfrage Vorlagen aus dem Repository zurückgegeben werden.


## Compute

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------------------------ |-------- | ------ | ------ |
| Batch | Ja | [Batch REST (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Dynamics Lifecycle Services | Ja | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (Vorschau) | Ja | [Service Fabric REST](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Virtual Machines | Ja | [VM REST](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Virtual Machines (klassisch) | Eingeschränkt | - | - | | Remote App | Nein | - | - | | Cloud Services (klassisch) | Eingeschränkt (siehe unten) | - | - | - |

"Virtuelle Computer (klassisch)" bezieht sich auf Ressourcen, die über das klassische Bereitstellungsmodell statt über das Ressourcen-Manager-Bereitstellungsmodell bereitgestellt wurden. Im Allgemeinen unterstützen diese Ressourcen keine Ressourcen-Manager-Vorgänge, es wurden jedoch einige Vorgänge aktiviert. Weitere Informationen zu diesen Bereitstellungsmodellen finden Sie unter [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](resource-manager-deployment-model.md).

Cloud Services (klassisch) kann mit anderen klassischen Ressourcen verwendet werden. Klassische Ressourcen profitieren jedoch nicht von allen Funktionen des Ressourcen-Managers und sind keine gute Option für zukünftige Lösungen. Ändern Sie stattdessen Ihre Infrastruktur, um Ressourcen aus den Namespaces Microsoft.Compute, Microsoft.Storage und Microsoft.Network zu verwenden.


## Netzwerk

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | -------  | -------- | ------ | ------ |
| Application Gateway | Ja | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | Ja | [DNS REST](https://msdn.microsoft.com/library/azure/mt163862.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Ja | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Lastenausgleichsmodul | Ja | [Load Balancer REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Traffic Manager | Ja | [Traffic Manager REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Virtuelle Netzwerke | Ja| [Virtual Network REST](https://msdn.microsoft.com/de-DE/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| VPN Gateway | Ja | [Netzwerkgateway – REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connections](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |



## Daten und Speicher

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Ja | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis-Cache | Ja | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Suchen | Ja | [Azure-Suchdienst-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Speicher | Ja | [Storage REST](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Speicherkonto](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| SQL-Datenbank | Ja | [SQL-Datenbank REST](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse | Ja | | |
| StorSimple | Nein | - | - | - |

## Web- und mobile Anwendungen

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------- | -------- | ------ | ------ |
| API-Apps | Ja | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [API-Apps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| API Management | Ja | [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Logik-Apps | Ja | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobile Apps | Ja | | | |
| Mobile Engagements | Ja | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Web-Apps | Ja | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## Analyse

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | -------  | -------- | ------ | ------ |
| Data Factory | Ja | [Data Factory REST](https://msdn.microsoft.com/library/azure/dn906738.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Data Lake Analytics | Ja | | | |
| Data Lake-Speicher | Ja | | | |
| HDInsights | Ja | [HDInsights REST](https://msdn.microsoft.com/library/azure/mt622197.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Stream Analytics | Ja | [Stream Analytics REST](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| Machine Learning | Nein | - | - | | Data Catalog | Nein | - | - |

## Internet der Dinge

| Service | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------- | -------- | ------ | ------ |
| Event Hub | Ja | [Event Hub REST](https://msdn.microsoft.com/library/azure/dn790674.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| IoTHubs | Ja | [IoT Hub REST](https://msdn.microsoft.com/library/azure/mt589014.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Notification Hubs | Ja | [Notification Hub REST](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## Medien und CDN

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------- | -------- | ------ | ------ |
| CDN | Ja | [CDN REST](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Mediendienst | Nein | | |


## Hybridintegration

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------- | -------- | ------ | ------ |
| BizTalk Services | Ja | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Service Bus | Ja | | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| Backup | Nein | - | - | | Site Recovery | Nein | - | - |

## Identitäts- und Zugriffsverwaltung 

Azure Active Directory arbeitet mit dem Ressourcen-Manager zusammen, um die rollenbasierte Access Control für Ihr Abonnement zu aktivieren. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](./active-directory/role-based-access-control-configure.md).

## Entwicklerdienste 

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------- | -------- | ------ | ------ |
| Application Insights | Ja | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps | Ja | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| Dev/Test Labs (Vorschau) | Ja | | [2015-05-21-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Visual Studio-Konto | Ja | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## Verwaltung und Sicherheit

| Dienst | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------- | -------- | ------ | ------ |
| Automation | Ja | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Schlüsseltresor | Ja | [Referenz zur Schlüsseltresor-REST-API](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Schlüsseltresor](resource-manager-template-keyvault.md)<br />[Geheimer Schlüssel im Tresor](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Operational Insights | Ja | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| Scheduler | Ja | [Scheduler REST](https://msdn.microsoft.com/library/azure/mt629143.aspx) | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Sicherheit (Vorschau) | Ja | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## Ressourcen-Manager

| Funktion | Ressourcen-Manager aktiviert | REST-API | Schema | Schnellstartvorlagen |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorisierung | Ja | [Verwaltungssperren](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Rollenbasierte Zugriffssteuerung](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Ressourcensperre](resource-manager-template-lock.md)<br />[Rollenzuweisungen](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ressourcen | Ja | [Verknüpfte Ressourcen](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Ressourcenlinks](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## Ressourcenanbieter und -typen

Beim Bereitstellen von Ressourcen müssen Sie häufig Informationen zu den Ressourcenanbietern und -typen abrufen. Sie können diese Informationen über die REST-API, Azure PowerShell oder die Azure-Befehlszeilenschnittstelle abrufen.

Damit Sie mit einem Ressourcenanbieter arbeiten können, muss dieser Ressourcenanbieter in Ihrem Konto registriert werden. Viele Ressourcenanbieter werden standardmäßig automatisch registriert, dennoch müssen Sie unter Umständen einige Ressourcenanbieter manuell registrieren. In den nachfolgenden Beispielen wird gezeigt, wie der Registrierungsstatus eines Ressourcenanbieters abgerufen und der Ressourcenanbieter bei Bedarf registriert wird.

### REST-API

Verwenden Sie zum Abrufen aller verfügbaren Ressourcenanbieter, einschließlich Typen, Speicherorten, API-Versionen und Registrierungsstatus, den Vorgang [Auflisten aller Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790524.aspx). Informationen zum Registrieren eines Ressourcenanbieters finden Sie unter [Registrieren eines Abonnements bei einem Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### PowerShell

Im folgenden Beispiel wird veranschaulicht, wie Sie alle verfügbaren Ressourcenanbieter abrufen.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable
    
Die Ausgabe ähnelt der folgenden:

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    ...

Im nächsten Beispiel wird gezeigt, wie die Ressourcentypen für einen bestimmten Ressourcenanbieter abgerufen werden.

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
Die Ausgabe ähnelt der folgenden:

    ResourceTypeName                Locations                                         ApiVersions
    ----------------                ---------                                         ------
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} {20...
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} {20...
    ...
    
Geben Sie zum Registrieren eines Ressourcenanbieters den Namespace an:

    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### Azure-Befehlszeilenschnittstelle

Im folgenden Beispiel wird veranschaulicht, wie Sie alle verfügbaren Ressourcenanbieter abrufen.

    azure provider list
    
Die Ausgabe ähnelt der folgenden:

    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Mit dem folgenden Befehl können Sie die Informationen für einen bestimmten Ressourcenanbieter in einer Datei speichern.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Geben Sie zum Registrieren eines Ressourcenanbieters den Namespace an:

    azure provider register -n Microsoft.ServiceBus

## Unterstützte Regionen

Bei der Bereitstellung von Ressourcen müssen Sie in der Regel eine Region für die Ressourcen angeben. Der Ressourcen-Manager wird in allen Regionen unterstützt, aber die Ressourcen, die Sie bereitstellen, werden möglicherweise nicht in allen Regionen unterstützt. Darüber hinaus liegen möglicherweise Einschränkungen Ihres Abonnements vor, die verhindern, dass Sie einige Regionen verwenden, die die Ressource unterstützen. Diese Einschränkungen können im Zusammenhang mit steuerlichen Problemen in Ihrem Land stehen, oder Sie sind das Ergebnis einer Richtlinie, die von Ihrem Abonnementadministrator implementiert wurde, sodass Sie nur bestimmte Regionen verwenden können.

Eine vollständige Liste aller unterstützten Regionen für alle Azure-Dienste finden Sie unter [Dienste nach Region](https://azure.microsoft.com/regions/#services). Diese Liste kann jedoch Regionen enthalten, die von Ihrem Abonnement nicht unterstützt werden. Sie können die Regionen für einen bestimmten von Ihrem Abonnement unterstützten Ressourcentyp ermitteln, indem Sie einen der folgenden Befehle ausführen.

### REST-API

Verwenden Sie für die Ermittlung der verfügbaren Regionen für einen bestimmten Ressourcentyp im Abonnement den Vorgang [Auflisten aller Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

Das folgende Beispiel zeigt, wie Sie die unterstützten Regionen für Websites abrufen.

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

Im folgenden Beispiel wird veranschaulicht, wie Sie die verfügbaren API-Versionen für einen bestimmten Ressourcentyp abrufen.

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

### Azure-Befehlszeilenschnittstelle

Sie können die Informationen (einschließlich der verfügbaren API-Versionen) für einen Ressourcenanbieter mit dem folgenden Befehl in einer Datei speichern.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Sie können die Datei öffnen und nach dem Element **apiVersions** suchen.

## Nächste Schritte

- Weitere Informationen zum Erstellen von Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
- Informationen zum Bereitstellen von Vorlagen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0302_2016-->