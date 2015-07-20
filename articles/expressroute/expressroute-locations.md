<properties
   pageTitle="ExpressRoute-Standorte"
   description="Diese Seite enthält eine ausführliche Übersicht über die Standorte, an denen Dienste angeboten werden, sowie Informationen darüber, wie Sie eine Verbindung mit den Azure-Regionen herstellen können."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2015"
   ms.author="cherylmc" />

# ExpressRoute-Partner und Peeringstandorte
In den Tabellen auf dieser Seite finden Sie Informationen zu ExpressRoute-Konnektivitätsanbietern (EXPs und NSPs), zum geografischen Geltungsbereich von ExpressRoute, zu Microsoft-Clouddiensten, die über ExpressRoute unterstützt werden, zu ExpressRoute-Systemintegratoren (SIs).

## ExpressRoute-Konnektivitätsanbieter
ExpressRoute wird in allen Azure-Regionen und an allen Standorten unterstützt. Die folgende Karte zeigt die Azure-Regionen und ExpressRoute-Standorte. ExpressRoute-Standorte beziehen sich auf jene Orte, an denen Microsoft eine Peeringkooperation mit mehreren Dienstanbietern bietet.
 
![](./media/expressroute-locations/expressroute-locations-map.png)

Wenn Sie mit mindestens einem ExpressRoute-Standort innerhalb der geopolitischen Region eine Verbindung hergestellt haben, haben Sie Zugriff auf die Azure-Dienste in allen Regionen dieser geopolitischen Region. Die folgende Tabelle bietet eine Übersicht über die Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region.

|**Geopolitische Region**|**Azure-Regionen**|**ExpressRoute-Standorte**|
|---|---|---|
|**USA**|Alle US-Regionen: Osten USA, Westen USA, Osten USA 2, USA Süd Mitte, USA Nord Mitte|Atlanta, Chicago, Dallas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC|
|**Südamerika**|Brasilien Süd|Sao Paulo|
|**Europa**|Nordeuropa, Westeuropa|Amsterdam, London|
|**Asien**|Ostasien, Südostasien|Hongkong, Singapur|
|**Japan**|Japan West, Japan Ost|Tokio|
|**Australien**|Südostaustralien, Ostaustralien|Melbourne, Sydney|
|**Indien**|Indien, Westen, Indien, Mitte, Indien, Süden|Chennai, Mumbai|

Konnektivität zwischen geopolitischen Regionen wird nicht unterstützt. Sie können mit Ihrem Konnektivitätsanbieter gemeinsam die Konnektivität über geopolitische Regionen hinweg erweitern, indem Sie dessen Netzwerk verwenden.


## Standorte von Exchange-Anbietern (EXP)

| **Dienstanbieter** |**Microsoft Azure** | **Office 365** | **Standorte** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Unterstützt | Nicht unterstützt | Silicon Valley, Singapur, Washington, D.C. |
| **[Colt Ethernet](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Unterstützt | Nicht unterstützt | Amsterdam, London |
| **Comcast** | Unterstützt | Nicht unterstützt | Silicon Valley, Washington, D.C. |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Unterstützt | In Kürze verfügbar | Amsterdam, Atlanta, Chicago, Dallas, Hongkong, London, Los Angeles, Melbourne, New York, São Paulo, Seattle, Silicon Valley, Singapur, Sydney, Tokio, Washington, D.C. |
| **[InterCloud](https://www.intercloud.com/)** | Unterstützt | Nicht unterstützt | Amsterdam, London, Singapur, Washington, D.C. |
| **Internet Solutions – Cloud Connect** | Unterstützt | Nicht unterstützt | Amsterdam, London |
| **Interxion** | Unterstützt | Nicht unterstützt | Amsterdam |
| **[Level 3 Communications – Exchange](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Unterstützt | Nicht unterstützt | Chicago, Dallas, London, Seattle, Silicon Valley, Washington, D.C. |
| **NEXTDC** | Unterstützt | Nicht unterstützt | Melbourne, Sydney+ |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Unterstützt | In Kürze verfügbar | Amsterdam, London |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Unterstützt | Nicht unterstützt | Melbourne+, Sydney |
| **[Zayo Group](http://www.zayo.com/)** | Unterstützt | Nicht unterstützt | Washington DC |

 **+** steht für "In Kürze"

Unter [Konfigurieren einer ExpressRoute-Verbindung](expressroute-configuring-exps.md) finden Sie Schritte zum Einrichten der Verbindung.

## Standorte von Netzwerkdienstanbietern (NSP)


| **Dienstanbieter** |**Microsoft Azure** | **Office 365** | **Standorte** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Unterstützt | In Kürze verfügbar | Amsterdam +, London, Dallas, Silicon Valley, Washington DC. |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Unterstützt | In Kürze verfügbar | Amsterdam, London, Silicon Valley+, Washington, D.C. |
|**China Telecom Global** | In Kürze verfügbar | Nicht unterstützt | Hongkong+ |
| **[Colt IPVPN](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Unterstützt | Nicht unterstützt | Amsterdam, London |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | Unterstützt | Nicht unterstützt | Tokio |
| **[Level 3 Communications – IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Unterstützt | Nicht unterstützt | Chicago, Dallas, London, Seattle, Silicon Valley, Washington, D.C. |
| **NTT Communications** | In Kürze verfügbar | Nicht unterstützt | Tokio+ | 
| **[Orange](http://www.orange-business.com/)** | Unterstützt | Nicht unterstützt | Amsterdam, London, Silicon Valley, Washington, D.C. |
| **PCCW Global Limited** | Unterstützt | Nicht unterstützt | Hongkong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Unterstützt | Nicht unterstützt | Singapur |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Unterstützt | In Kürze verfügbar | Amsterdam, Chennai+, Hongkong, London, Mumbai+, Singapur |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Unterstützt | Nicht unterstützt | Melbourne+, Sydney |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Unterstützt | Nicht unterstützt | London, Hongkong, Silicon Valley, Washington, D.C. |

 **+** steht für "In Kürze"

Unter [Konfigurieren einer NSP-Verbindung](expressroute-configuring-nsps.md) finden Sie Schritte zum Einrichten der Verbindung.

## Konnektivität über nicht aufgeführte Dienstanbieter 

Wenn Ihr Konnektivitätsanbieter nicht in den oben stehenden Listen enthalten ist, können Sie dennoch eine Verbindung erstellen.

- Fragen Sie Ihren Konnektivitätsanbieter, ob er mit einem der Exchange-Anbieter an den aufgeführten EXP-Standorten verbunden ist. Sie können auch die Links unten überprüfen, um weitere Informationen zu den von den Exchange-Anbietern angebotenen Diensten zu erhalten. Mehrere Konnektitivätsanbieter sind bereits mit den Ethernet-Exchanges des EXP verbunden.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Fordern Sie Ihren Konnektivitätsanbieter auf, Ihr Netzwerk auf den Exchange-Standort Ihrer Wahl zu erweitern.
	- Stellen Sie sicher, dass Ihr Konnektivitätsanbieter Ihre Konnektivität mit hoher Verfügbarkeit erweitert, sodass es keine einzelnen Fehlerquellen mehr gibt.
	- Konnektivitätsanbieter (insbesondere Ethernetanbieter) fordern Sie u. U. auf, ein Verbindungspaar für die Ethernet-Exchanges zu erwerben, um hohe Verfügbarkeit sicherzustellen. 
- Fordern Sie dann eine ExpressRoute-Verbindung über den Exchange-Anbieter an, um eine Verbindung mit Azure herzustellen.
	- Führen Sie die Schritte unter [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md) aus, um die Verbindung einzurichten.

|**Konnektivitätsanbieter**|**Exchange-Anbieter**|**Peeringstandort**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|

## ExpressRoute-Systemintegratoren
Je nach Ihrer Netzwerkgröße kann das Aktivieren privater Verbindungen für Ihre Anforderungen problematisch sein. Alle Systemintegratoren, die in der folgenden Tabelle aufgeführt sind, können Ihnen beim Integrieren von ExpressRoute helfen.


|**Systemintegrator**|**Kontinent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Nächste Schritte
- Stellen Sie sicher, dass Sie die [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md) erfüllen.
- Weitere Informationen finden Sie in den [FAQs](expressroute-faqs.md).
- Wenn Sie eine ExpressRoute-Verbindung konfigurieren möchten, lesen Sie [Konfigurieren der EXP-Verbindung](expressroute-configuring-exps.md) oder [Konfigurieren der NSP-Verbindung](expressroute-configuring-nsps.md).
- Wenn Sie sowohl eine VPN-Verbindung zwischen Standorten und ExpressRoute für dasselbe virtuelle Netzwerk konfigurieren möchten, lesen Sie [Koexistenz von ExpressRoute und VPN-Verbindungen zwischen Standorten](expressroute-coexist.md).
 

<!---HONumber=July15_HO2-->