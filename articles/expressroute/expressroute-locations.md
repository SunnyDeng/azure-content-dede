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
   ms.date="04/29/2015"
   ms.author="cherylmc" />

# ExpressRoute-Partner und Peeringstandorte
Diese Tabelle enthält Details zu

1. ExpressRoute-Konnektivitätsanbietern (EXPs und NSPs)
2. zum geografischen Geltungsbereich von ExpressRoute
3. zu Microsoft-Clouddiensten, die von ExpressRoute unterstützt werden
4. zu ExpressRoute-Systemintegratoren (SIs)

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
|**Australien**|Südostaustralien, Ostaustralien|Sydney|

Konnektivität zwischen geopolitischen Regionen wird nicht unterstützt. Sie können mit Ihrem Konnektivitätsanbieter gemeinsam die Konnektivität über geopolitische Regionen hinweg erweitern, indem Sie dessen Netzwerk verwenden.


### Standorte von Exchange-Anbietern (EXP)
- In dieser [Tabelle](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP) finden Sie eine Liste der Exchange-Anbieter und Standorte, an denen sie unterstützt werden.
-  Unter [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md) finden Sie Schritte zum Einrichten der Verbindung.

### Standorte von Netzwerkdienstanbietern (NSP)
- In dieser [Tabelle](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP) finden Sie eine Liste der Netzwerkdienstanbieter und Standorte, an denen sie unterstützt werden.
- Unter [Konfigurieren einer ExpressRoute-Verbindung über einen Netzwerkdienstanbieter](expressroute-configuring-nsps.md) finden Sie Schritte zum Einrichten der Verbindung.

### Konnektivität über nicht aufgeführte Dienstanbieter

Wenn Ihr Konnektivitätsanbieter nicht in den oben stehenden Listen enthalten ist, können Sie dennoch eine Verbindung erstellen.

- Fragen Sie Ihren Konnektivitätsanbieter, ob er mit einem der Exchange-Anbieter an den aufgeführten EXP-Standorten verbunden ist. Sie können auch die Links unten überprüfen, um weitere Informationen zu den von den Exchange-Anbietern angebotenen Diensten zu erhalten. Mehrere Konnektitivätsanbieter sind bereits mit den Ethernet-Exchanges des EXP verbunden.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Fordern Sie Ihren Konnektivitätsanbieter auf, Ihr Netzwerk auf den Exchange-Standort Ihrer Wahl zu erweitern.
	- Stellen Sie sicher, dass Ihr Konnektivitätsanbieter Ihre Konnektivität mit hoher Verfügbarkeit erweitert, sodass es keine einzelnen Fehlerquellen mehr gibt.
	- Konnektivitätsanbieter (insbesondere Ethernetanbieter) fordern Sie u. U. auf, ein Verbindungspaar für die Ethernet-Exchanges zu erwerben, um die hohe Verfügbarkeit sicherzustellen. 
- Fordern Sie dann eine ExpressRoute-Verbindung über den Exchange-Anbieter an, um eine Verbindung mit Azure herzustellen.
	- Führen Sie die Schritte unter [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md) aus, um die Verbindung einzurichten.

|**Konnektivitätsanbieter**|**Exchange-Anbieter**|**Peeringstandort**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|


## ExpressRoute und Microsoft-Clouddienste
Die folgenden Tabellen enthalten Details zu Konnektivitätsanbietern sowie eine Liste der Microsoft-Clouddienste, die sie unterstützen. Wenden Sie sich an Ihren Dienstanbieter

**Exchange-Anbieter (EXPs)**

|**Dienstanbieter**|**Microsoft Azure-Dienste**|**Office 365-Dienste**|
|---|---|---|
|**Aryaka**|Unterstützt||
|**Colt Ethernet**|Unterstützt||
|**Equinix**|Unterstützt|In Kürze verfügbar|
|**InterCloud**|Unterstützt||
|**Level 3 EVPL Service**|Unterstützt||
|**TeleCity Group**|Unterstützt||
|**Zayo Group**|Unterstützt||

**Netzwerkdienstanbieter (NSPs)**

|**Dienstanbieter**|**Microsoft Azure-Dienste**|**Office 365-Dienste**|
|---|---|---|
|**AT&T**|Unterstützt|In Kürze verfügbar|
|**British Telecom**|Unterstützt|In Kürze verfügbar|
|**Colt IPVPN**|Unterstützt||
|**Internet Initiative Japan Inc. - IIJ**|Unterstützt||
|**Level3 IPVPN**|Unterstützt||
|**Orange**|Unterstützt|| 
|**SingTel**|Unterstützt||
|**Tata Communications**|Unterstützt||
|**Telstra Corporation**|Unterstützt||
|**Verizon**|Unterstützt|| 


## ExpressRoute-Systemintegratoren
Je nach Ihrer Netzwerkgröße kann das Aktivieren von privaten Verbindungen für Ihre Anforderungen problematisch sein. Alle Systemintegratoren, die in der folgenden Tabelle aufgeführt sind, können Ihnen beim Integrieren von ExpressRoute helfen.


|**Systemintegrator**|**Kontinent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Nächste Schritte
- Stellen Sie sicher, dass Sie die [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md) erfüllen.
- Weitere Informationen finden Sie in den [FAQs](expressroute-faqs.md).
- Wählen Sie Ihren Anbieter, und konfigurieren Sie die Verbindung. Konfigurationsinformationen finden Sie unter [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md) oder [Konfigurieren einer ExpressRoute-Verbindung über einen Netzwerkdienstanbieter](expressroute-configuring-nsps.md).


<!---HONumber=54--> 