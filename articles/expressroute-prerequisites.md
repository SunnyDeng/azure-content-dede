<properties 
	pageTitle="Voraussetzungen für den Wechsel zu ExpressRoute"
	description="Diese Seite enthält eine Liste der Anforderungen, die erfüllt sein müssen, bevor Sie eine ExpressRoute-Verbindung anfordern können."
	documentationCenter="na"
	services="expressroute"
	authors="cherylmc"
	manager="adinah"
	editor="tysonn"/> 

<tags 
	ms.service="expressroute"
	ms.devlang="na"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services" 
	ms.date="05/06/2015"
	ms.author="cherylmc"/>


# ExpressRoute-Voraussetzungen  

Zum Herstellen einer Verbindung mit den Microsoft-Clouddiensten über ExpressRoute müssen Sie sicherstellen, dass die folgenden Voraussetzungen erfüllt sind:

## Konnektivitätsvoraussetzungen

- Ein gültiges und aktives Microsoft Azure-Konto
- Eine Beziehung mit einem Netzwerkdienstanbieter (NSP) oder einem Exchange-Anbieter (EXP) aus der [Liste der unterstützten Anbieter](expressroute-locations.md), der die Verbindung ermöglichen muss. Sie müssen eine Geschäftsbeziehung mit dem Netzwerkdienstanbieter oder Exchange-Anbieter haben. Sie müssen sicherstellen, dass der von Ihnen verwendete Dienst mit ExpressRoute kompatibel ist. 
- Wenn Sie mit einem Netzwerkdienstanbieter zusammenarbeiten möchten, der nicht in der obigen Liste aufgeführt ist, können Sie dennoch eine Verbindung mit Azure erstellen. 
	- Fragen Sie Ihren Netzwerkdienstanbieter, ob er an einem der Exchange-Standorte in der oben stehenden Liste vertreten ist.
	- Fordern Sie Ihren Netzwerkanbieter auf, Ihr Netzwerk auf den Exchange-Standort Ihrer Wahl zu erweitern.
	- Fordern Sie dann eine ExpressRoute-Verbindung über den Exchange-Anbieter an, um eine Verbindung mit Azure herzustellen.
- Konnektivität mit der Infrastruktur des Dienstanbieters. Sie müssen für mindestens eines der folgenden aufgeführten Elemente die angeforderten Kriterien erfüllen:
	- Sie sind ein VPN-Kunde des Netzwerkdienstanbieters, und mindestens ein lokaler Standort ist mit der VPN-Infrastruktur des Netzwerkdienstanbieters verbunden. Fragen Sie Ihren Netzwerkdienstanbieter, ob Ihr VPN-Dienst die Anforderungen für ExpressRoute erfüllt.
	- Ihre Infrastruktur teilt sich den Standort mit dem Datencenter des Exchange-Anbieters.
	- Sie verfügen über eine Ethernetverbindung mit der Ethernet-Exchange-Infrastruktur des Exchange-Anbieters.	
- IP-Adressen und AS-Nummern für die Routingkonfiguration. 
	- Sie können private AS-Nummern verwenden, um eine Verbindung zur privaten Peeringrouting-Domäne von Azure herzustellen. Wenn Sie sich dafür entscheiden, muss die Nummer größer als 65000 sein. Weitere Informationen zu AS-Nummern finden Sie unter [Autonome Systemnummern (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- IP-Adressen zum Konfigurieren von Routen. Es ist ein /28-Subnetz erforderlich. Dies darf sich nicht mit den lokal oder in Azure verwendeten IP-Adressbereichen überschneiden.
	- Verwenden Sie zum Konfigurieren von BGP-Sitzungen mit öffentlichen Azure-Diensten Ihre eigenen öffentlichen AS-Nummern.

## Nächste Schritte

- Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
- Informationen zum Konfigurieren der ExpressRoute-Verbindung finden Sie unter 
	- [Konfigurieren einer ExpressRoute-Verbindung über einen Netzwerkdienstanbieter](expressroute-configuring-nsps.md)
	- [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md)
<!--HONumber=54-->