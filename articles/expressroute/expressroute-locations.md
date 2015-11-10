<properties
   pageTitle="ExpressRoute-Standorte | Microsoft Azure"
   description="Dieser Artikel enthält eine ausführliche Übersicht über die Standorte, an denen Dienste angeboten werden, sowie Informationen darüber, wie Sie eine Verbindung mit den Azure-Regionen herstellen können."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="cherylmc" />

# ExpressRoute-Partner und Peeringstandorte

In den Tabellen in diesem Artikel finden Sie Informationen zu ExpressRoute-Konnektivitätsanbietern, zum geografischen Geltungsbereich von ExpressRoute, zu Microsoft-Clouddiensten, die über ExpressRoute unterstützt werden, und zu ExpressRoute-Systemintegratoren (SIs).

## ExpressRoute-Konnektivitätsanbieter

ExpressRoute wird in allen Azure-Regionen und an allen Standorten unterstützt. Die folgende Karte zeigt die Azure-Regionen und ExpressRoute-Standorte. ExpressRoute-Standorte beziehen sich auf jene Orte, an denen Microsoft eine Peeringkooperation mit mehreren Service Providern bietet.

![](./media/expressroute-locations/expressroute-locations-map.png)

Wenn Sie mit mindestens einem ExpressRoute-Standort innerhalb der geopolitischen Region eine Verbindung hergestellt haben, haben Sie Zugriff auf die Azure-Dienste in allen Regionen dieser geopolitischen Region. Die folgende Tabelle bietet eine Übersicht über die Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region.

|**Geopolitische Region**|**Azure-Regionen**|**ExpressRoute-Standorte**|
|---|---|---|
|**Nordamerika**|Alle US-Regionen: Osten USA, Westen USA, Osten USA 2, USA Süd Mitte, USA Nord Mitte|Atlanta, Chicago, Dallas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC|
|**Südamerika**|Brasilien Süd|Sao Paulo|
|**Europa**|Nordeuropa, Westeuropa|Amsterdam, Dublin+, London|
|**Asien**|Ostasien, Südostasien|Hongkong, Singapur|
|**Japan**|Japan West, Japan Ost|Osaka, Tokio|
|**Australien**|Südostaustralien, Ostaustralien|Melbourne, Sydney|
|**Indien**|Indien, Westen, Indien, Mitte, Indien, Süden|Chennai, Mumbai|



In der folgenden Tabelle finden Sie Informationen zu Regionen und geopolitischen Grenzen für nationale Clouds.

|**Geopolitische Region**|**Azure-Regionen**|**ExpressRoute-Standorte**|
|---|---|---|---|
|**US-Government Cloud**|US Government|Iowa, Virginia|Ashburn, Chicago|


Konnektivität zwischen geopolitischen Regionen wird bei der ExpressRoute-Standard-SKU nicht unterstützt. Sie müssen das ExpressRoute Premium-Add-On aktivieren, um Unterstützung für globale Konnektivität zu erhalten. Verbindungen mit nationalen Cloudumgebungen werden nicht unterstützt. Wenden Sie sich an Ihren Konnektivitätsanbieter, wenn Sie derartige Verbindungen implementieren möchten.


## Standorte von Konnektivitätsanbietern

### Azure-Produktionsumgebungen

| **Service Provider** |**Microsoft Azure** | **Office 365** | **Standorte** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Unterstützt | Unterstützt | Amsterdam, Silicon Valley, Singapur, Washington, D.C. |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Unterstützt | Unterstützt | Amsterdam, London, Dallas, Silicon Valley, Washington, D.C. |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Unterstützt | Unterstützt | Amsterdam, London, Silicon Valley, Singapur, Tokio, Washington, D.C. |
|**China Telecom Global** | In Kürze verfügbar | Nicht unterstützt | Hongkong+ |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Unterstützt | Nicht unterstützt | Amsterdam, London |
| **Comcast** | Unterstützt | Nicht unterstützt | Silicon Valley, Washington, D.C. |
| **CoreSite** | Unterstützt | Nicht unterstützt | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Unterstützt | Unterstützt | Amsterdam, Atlanta, Chicago, Dallas, Hongkong, London, Los Angeles, Melbourne, New York, Osaka, São Paulo, Seattle, Silicon Valley, Singapur, Sydney, Tokio, Washington, D.C. |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | Unterstützt | Nicht unterstützt | Tokio |
| **[InterCloud](https://www.intercloud.com/)** | Unterstützt | Unterstützt | Amsterdam, London, Singapur, Washington, D.C. |
| **Internet Solutions – Cloud Connect** | Unterstützt | Unterstützt | Amsterdam, London |
| **Interxion** | Unterstützt | Nicht unterstützt | Amsterdam |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Unterstützt | Nicht unterstützt | Chicago, Dallas, London, Seattle, Silicon Valley, Washington, D.C. |
| **Megaport** | Unterstützt | Nicht unterstützt | Melbourne, Sydney |
| **MTN** | Unterstützt | Nicht unterstützt | London |
| **NTT Communications** | Unterstützt | Nicht unterstützt | London+, Tokio |
| **NEXTDC** | Unterstützt | Nicht unterstützt | Melbourne, Sydney+ |
| **[Orange](http://www.orange-business.com/)** | Unterstützt | Nicht unterstützt | Amsterdam, Hongkong, London, Silicon Valley, Singapur, Washington, D.C. |
| **PCCW Global Limited** | Unterstützt | Nicht unterstützt | Hongkong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Unterstützt | Nicht unterstützt | Singapur |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Unterstützt | Unterstützt | Amsterdam, Chennai+, Hongkong, London, Mumbai, Singapur |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Unterstützt | Unterstützt | Amsterdam, London |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Unterstützt | Nicht unterstützt | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Unterstützt | Unterstützt | Hongkong, London, Silicon Valley, Sydney, Washington, D.C. |
| **Vodafone** | Unterstützt | Nicht unterstützt | London | 
| **[Zayo Group](http://www.zayo.com/)** | Unterstützt | Nicht unterstützt | Chicago, Silicon Valley, Washington, D.C. |

 **+** steht für "In Kürze"

### Nationale Cloudumgebungen

#### US-Government Cloud

| **Service Provider** |**Microsoft Azure** | **Office 365** | **Standorte** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Unterstützt | Nicht unterstützt | Chicago+, Washington, D.C. |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | In Kürze verfügbar | Nicht unterstützt | Chicago+, Washington DC+ |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Unterstützt | Nicht unterstützt | Chicago+, Washington, D.C. |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Unterstützt | Nicht unterstützt | Chicago+, Washington, D.C. |

## Konnektivität über nicht aufgeführte Service Provider

Wenn Ihr Konnektivitätsanbieter nicht in den vorherigen Abschnitten enthalten ist, können Sie dennoch eine Verbindung erstellen.

- Fragen Sie Ihren Konnektivitätsanbieter, ob eine Verbindung mit einem der Exchange-Standorte aus der vorherigen Tabelle bereitgestellt werden kann. Sie können auch die folgenden Links überprüfen, um weitere Informationen über die von den Exchange-Anbietern angebotenen Dienste zu erhalten. Viele Konnektivitätsanbieter sind bereits mit Ethernet-Exchanges verbunden.

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
- Bitten Sie Ihren Konnektivitätsanbieter, Ihr Netzwerk auf den Peeringstandort Ihrer Wahl zu erweitern.
	- Stellen Sie sicher, dass Ihr Konnektivitätsanbieter Ihre Konnektivität mit hoher Verfügbarkeit erweitert, sodass es keine einzelnen Fehlerquellen mehr gibt.
- Bestellen Sie eine ExpressRoute-Verbindung mit der Exchange als Konnektivitätsanbieter für die Verbindung mit Microsoft.
	- Führen Sie die Schritte in [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, um die Verbindung einzurichten.

|**Konnektivitätsanbieter**|**Exchange**|**Peeringstandorte**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|

## ExpressRoute-Systemintegratoren

Je nach Ihrer Netzwerkgröße kann das Aktivieren privater Verbindungen für Ihre Anforderungen problematisch sein. Alle Systemintegratoren, die in der folgenden Tabelle aufgeführt sind, können Ihnen beim Integrieren von ExpressRoute helfen.

|**Systemintegrator**|**Kontinent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Nächste Schritte

- Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
- Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind. Informationen finden Sie unter [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

<!---HONumber=Nov15_HO2-->