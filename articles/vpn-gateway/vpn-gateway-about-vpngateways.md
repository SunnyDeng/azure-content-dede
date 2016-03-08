<properties 
   pageTitle="Informationen zu VPN-Gateways für standortübergreifende Verbindungen in Virtual Network | Microsoft Azure"
   description="Erhalten Sie Informationen zu VPN-Gateways, die für standortübergreifende Verbindungen für Hybridkonfigurationen verwendet werden können. Dieser Artikel enthält Informationen zu Gateway-SKUs (Basic, Standard und High Performance), zur gemeinsamen Verwendung von VPN Gateways und ExpressRoute, zu Gatewayroutingtypen (statisch, dynamisch, richtlinienbasiert und routenbasiert) und zu den Gatewayanforderungen für die Konnektivität virtueller Netzwerke, die für das klassische und das Resource Manager-Bereitstellungsmodell gelten."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="cherylmc" />

# Informationen zu VPN-Gateways

VPN-Gateways werden zum Senden von Netzwerkdatenverkehr zwischen virtuellen Netzwerken und lokalen Standorten verwendet. Außerdem dienen sie zum Senden von Datenverkehr zwischen mehreren virtuellen Netzwerken in Azure (VNET-zu-VNET). Beim Erstellen eines Gateways müssen Sie einige Faktoren berücksichtigen.

- Die Gateway-SKU, die Sie verwenden möchten
- Den VPN-Typ für Ihre Verbindung
- Das VPN-Gerät, falls dies für die Verbindung erforderlich ist

**Informationen zu Bereitstellungsmodellen**

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 

## Gateway-SKUs

[AZURE.INCLUDE [vpn-gateway-table-sku](../../includes/vpn-gateway-table-sku-include.md)]

## Typen von VPN Gateways

Es gibt zwei VPN-Typen:

- **Richtlinienbasiert:** Richtlinienbasierte Gateways werden im klassischen Bereitstellungsmodell als *statische Gateways* bezeichnet. Die Funktionalität eines statischen Gateways wurde nicht geändert, auch wenn sich der Name geändert hat. Diese Art von Gateway unterstützt richtlinienbasierte VPNs. Bei richtlinienbasierten VPNs werden Pakete durch IPsec-Tunnel geleitet. Dies geschieht anhand von Datenverkehrselektoren basierend auf den Kombinationen der Adresspräfixe zwischen Ihrem lokalen Netzwerk und Ihrem Azure VNet. Die Datenverkehrselektoren bzw. Richtlinien werden normalerweise als Zugriffslisten in Ihren VPN-Konfigurationen festgelegt.
 
- **Routenbasiert:** Routenbasierte Gateways werden im klassischen Bereitstellungsmodell als *dynamische Gateways* bezeichnet. Die Funktionalität eines dynamischen Gateways wurde nicht geändert, auch wenn sich der Name geändert hat. Routenbasierte Gateways implementieren routenbasierte VPNs. Bei routenbasierten VPNs werden die „Routen“ der IP-Weiterleitungs- oder -Routingtabelle verwendet, um Pakete an die entsprechenden VPN-Tunnelschnittstellen zu leiten. Über die Tunnelschnittstellen werden die Pakete für die Tunnel dann ver- oder entschlüsselt (ein- und ausgehend). Die Richtlinie bzw. der Datenverkehrselektor für routenbasierte VPNs werden im Any-to-Any-Format (bzw. als Platzhalter) konfiguriert.

Einige Verbindungen (z. B. Punkt-zu-Standort- und VNET-zu-VNET-Verbindungen) funktionieren nur mit einem bestimmten VPN-Typ. Die Gatewayanforderungen sind in dem Artikel aufgeführt, der dem gewünschten Verbindungsszenario entspricht.

VPN-Geräte weisen auch Konfigurationseinschränkungen auf. Beim Erstellen eines VPN Gateways wählen Sie den VPN-Typ aus, der für Ihre Verbindung erforderlich ist. Dabei stellen Sie sicher, dass das gewünschte VPN-Gerät diesen Routingtyp auch unterstützt. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).

Wenn Sie beispielsweise die Verwendung einer Standort-zu-Standort-Verbindung parallel zu einer Punkt-zu-Standort-Verbindung planen, müssen Sie ein routenbasiertes VPN-Gateway konfigurieren. Auch wenn Standort-zu-Standort-Verbindungen mit richtlinienbasierten Gateways funktionieren, ist für Punkt-zu-Standort-Verbindungen ein routenbasierter Gatewaytyp erforderlich. Da beide Verbindungen über das gleiche Gateway führen, müssen Sie den Gatewaytyp auswählen, von dem beide Verbindungen unterstützt werden. Darüber hinaus muss das verwendete VPN-Gerät auch routenbasierte Konfigurationen unterstützen.


## Gatewayanforderungen


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## Nächste Schritte

Wählen Sie das VPN-Gerät für Ihre Konfiguration aus. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_0302_2016-->