<properties 
   pageTitle="Informationen zu VPN-Gateways für standortübergreifende Verbindungen in Virtual Network | Microsoft Azure"
   description="Erhalten Sie Informationen zu VPN-Gateways, die für standortübergreifende Verbindungen für Hybridkonfigurationen verwendet werden können. Dieser Artikel enthält Informationen zu Gateway-SKUs (Basis, Standard und Hochleistung), zur gemeinsamen Verwendung von VPN-Gateways und ExpressRoute, zu Gatewayroutingtypen (statisch, dynamisch, richtlinienbasiert und routenbasiert) und zu den Gatewayanforderungen für die Konnektivität virtueller Netzwerke."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/15/2015"
   ms.author="cherylmc" />

# Informationen zu VPN-Gateways

VPN-Gateways werden zum Senden von Netzwerkdatenverkehr zwischen virtuellen Netzwerken und lokalen Standorten verwendet. Außerdem dienen sie zum Senden von Datenverkehr zwischen mehreren virtuellen Netzwerken in Azure. Beim Erstellen eines Gateways müssen Sie einige Faktoren berücksichtigen.
 
Beachten Sie bei der Planung die folgenden Elemente:

- Die Gateway-SKU, die Sie verwenden möchten
- Den Gatewayroutingtyp für die Verbindung
- Das VPN-Gerät, falls dies für die Verbindung erforderlich ist

## Gateway-SKUs

Es gibt drei VPN-Gateway-SKUs:

- Basic
- Standard
- Leistung

In der folgenden Tabelle sind die Gatewaytypen und der geschätzte zusammengefasste Durchsatz angegeben. Für die einzelnen Gateway-SKUs gelten unterschiedliche Preise. Informationen zu den Preisen finden Sie unter [VPN-Gateway – Preise](http://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Gemeinsame Verwendung von VPN-Gateway und ExpressRoute | ExpressRoute-Gateway-Durchsatz | VPN-Gateway-Durchsatz | Max. IPsec-Tunnel für VPN-Gateway |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| Basic | Nein | 500 MBit/s | 100 MBit/s | 10 |
| Standard | Ja | 1\.000 MBit/s | 100 MBit/s | 10 |
| Leistung | Ja | 2\.000 MBit/s | 200 MBit/s | 30 |

**Hinweis:** Der VPN-Durchsatz ist eine grobe Schätzung, die auf Messungen zwischen den VNets einer Azure-Region basiert. Dies ist keine Garantie für den Wert, den Sie für standortübergreifende Verbindungen über das Internet erzielen können. Die Angabe sollte stattdessen als maximal möglicher Wert angesehen werden.

## Gatewayroutingtypen

Es gibt zwei Gatewayroutingtypen:

- **Richtlinienbasiert:** Richtlinienbasierte Gateways wurden zuvor als *Statische Gateways* bezeichnet. Die Funktionalität eines statischen Gateways wurde nicht geändert, auch wenn sich der Name geändert hat. Diese Art von Gateway unterstützt richtlinienbasierte VPNs. Bei richtlinienbasierten VPNs werden Pakete durch IPsec-Tunnel geleitet. Dies geschieht anhand von Datenverkehrselektoren basierend auf den Kombinationen der Adresspräfixe zwischen Ihrem lokalen Netzwerk und Ihrem Azure VNet. Die Datenverkehrselektoren bzw. Richtlinien werden normalerweise als Zugriffslisten in Ihren VPN-Konfigurationen festgelegt.
 
- **Routenbasiert:** Routenbasierte Gateways wurden zuvor als *Dynamische Gateways* bezeichnet. Die Funktionalität eines dynamischen Gateways wurde nicht geändert, auch wenn sich der Name geändert hat. Routenbasierte Gateways implementieren routenbasierte VPNs. Bei routenbasierten VPNs werden die „Routen“ der IP-Weiterleitungs- oder -Routingtabelle verwendet, um Pakete an die entsprechenden VPN-Tunnelschnittstellen zu leiten. Über die Tunnelschnittstellen werden die Pakete für die Tunnel dann ver- oder entschlüsselt (ein- und ausgehend). Die Richtlinie bzw. der Datenverkehrselektor für routenbasierte VPNs werden im Any-to-Any-Format (bzw. als Platzhalter) konfiguriert.

Einige Verbindungen (z. B. Punkt-zu-Standort- und VNet-zu-VNet-Verbindungen) funktionieren nur mit einem bestimmten Gatewayroutingtyp. Die Gatewayanforderungen sind in dem Artikel aufgeführt, der dem gewünschten Verbindungsszenario entspricht.

VPN-Geräte weisen auch Konfigurationseinschränkungen auf. Beim Erstellen eines VPN-Gateways wählen Sie den Gatewayroutingtyp aus, der für Ihre Verbindung erforderlich ist. Dabei stellen Sie sicher, dass das gewünschte VPN-Gerät diesen Routingtyp auch unterstützt. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).

Wenn Sie beispielsweise die Verwendung einer Standort-zu-Standort-Verbindung parallel zu einer Punkt-zu-Standort-Verbindung planen, müssen Sie ein routenbasiertes VPN-Gateway konfigurieren. Auch wenn Standort-zu-Standort-Verbindungen mit richtlinienbasierten Gateways funktionieren, ist für Punkt-zu-Standort-Verbindungen ein routenbasierter Gatewaytyp erforderlich. Da beide Verbindungen über das gleiche Gateway führen, müssen Sie den Gatewaytyp auswählen, von dem beide Verbindungen unterstützt werden. Darüber hinaus muss das verwendete VPN-Gerät auch routenbasierte Konfigurationen unterstützen.


## Gatewayanforderungen

In der folgenden Tabelle sind die Anforderungen für statische und dynamische VPN-Gateways aufgeführt.


| **Eigenschaft** | **Richtlinienbasiertes VPN-Gateway** | **Routenbasiertes VPN-Gateway** | **Standard-VPN-Gateway** | **VPN-Hochleistungsgateway** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Site-to-Site-Konnektivität (S2S) | Richtlinienbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration |
| Punkt-zu-Site-Konnektivität (P2S) | Nicht unterstützt | Unterstützt (gemeinsame Verwendung mit S2S möglich) | Unterstützt (gemeinsame Verwendung mit S2S möglich) | Unterstützt (gemeinsame Verwendung mit S2S möglich) |
| Authentifizierungsmethode | Vorinstallierter Schlüssel | - Vorinstallierter Schlüssel für S2S-Konnektivität - Zertifikate für P2S-Konnektivität | - Vorinstallierter Schlüssel für S2S-Konnektivität - Zertifikate für P2S-Konnektivität | - Vorinstallierter Schlüssel für S2S-Konnektivität - Zertifikate für P2S-Konnektivität |
| Maximale Anzahl von S2S-Verbindungen | 1 | 10 | 10 | 30 |
| Maximale Anzahl von P2S-Verbindungen | Nicht unterstützt | 128 | 128 | 128 |
| Aktive Routingunterstützung (BGP) | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt |


## Nächste Schritte

Wählen Sie das VPN-Gerät für Ihre Konfiguration aus. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_1217_2015-->