<properties 
   pageTitle="Informationen über sichere, standortübergreifende Verbindungen für virtuelle Netzwerke | Microsoft Azure"
   description="Erfahren Sie mehr über die Arten von sicheren, standortübergreifenden Verbindungen für virtuelle Netzwerke, einschließlich der Standort-zu-Standort-Punkt-zu-Standort- und ExpressRoute-Verbindungen."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2015"
   ms.author="cherylmc" />

# Informationen über sichere, standortübergreifende Verbindungen für virtuelle Netzwerke

Wenn Sie Ihre lokalen Standorten sicher zu einem virtuellen Netzwerk verbinden möchten, stehen Ihnen drei Optionen zur Verfügung: [Standort-zu-Standort](#site-to-site-connections), [Punkt-zu-Standort](#point-to-site-connections) und [ExpressRoute](#expressroute-connections).

Die von Ihnen gewählte Option kann von einer Vielzahl von Faktoren abhängen:


- Welche Form von Durchsatz benötigt Ihre Lösung?
- Soll die Kommunikation über das öffentliche Internet über ein sicheres VPN oder über eine private Verbindung erfolgen?
- Steht Ihnen eine öffentliche IP-Adresse zur Verfügung?
- Planen Sie den Einsatz eines VPN-Geräts? Wenn ja, ist es kompatibel?
- Stellen Sie nur eine Verbindung zwischen ein paar Computern her, oder möchten Sie eine dauerhafte Verbindung für Ihren Standort?
- Welche Art von VPN-Gateway ist für die Lösung erforderlich, die Sie erstellen möchten?

Die folgende Tabelle kann Ihnen dabei helfen, die beste Verbindungsoption für Ihre Lösung zu finden.

| - | **Punkt-zu-Standort** | **Standort-zu-Standort** | **ExpressRoute - EXP** | **ExpressRoute - NSP** | |------------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------| | **Von Azure unterstützte Dienste** | Cloud Services Virtual Computer | Cloud Services Virtual Machines | [Dienstliste](../expressroute/expressroute-faqs.md#supported-azure-services) | [Dienstliste](../expressroute/expressroute-faqs.md#supported-azure-services) || **Typische Bandbreiten** | In der Regel < 100-Mbit/s-Aggregat | In der Regel < 100 Mbit/s-Aggregat | 200 Mbit/s, 500 Mbit/s, 1 Gbit/s und 10 Gbit/s | 10 Mbit/s, 50 Mbit/s, 100 Mbit/s, 500 Mbit/s, 1 Gbit/s || **Unterstützte Protokolle** | Secure Sockets Tunneling-Protokoll (SSTP) | [IPsec](http://go.microsoft.com/fwlink/p/?LinkId=618592) | Direkte Verbindung über VLANs | VPN-Technologien von NSP (MPLS, VPLS...) || **Routing** | Statisch | Statisch – Wir unterstützen Richtlinien-basiert (statisches Routing) und Routen-basiert (VPN mit dynamischem Routing) | BGP | BGP | | **Verbindungsstabilität** | Aktiv-Passiv | Aktiv-Passiv | Aktiv-Aktiv | Aktiv-Aktiv | | **Typischer Anwendungsfall** | Erstellen von Prototypen, Szenarien für Entwicklung / Test / Labs für Cloud-Dienste und virtuelle Computer | Szenarien für Entwicklung / Test / Labs und kleine Produktionsarbeitslasten für Cloud-Dienste und virtuelle Computer | Zugriff auf alle Azure-Dienste (überprüfte Liste), Enterprise-Klasse und unternehmenskritische Arbeitsauslastungen, Backup, Big Data, Azure als DR-Standort | Zugriff auf alle Azure-Dienste (überprüfte Liste), Enterprise-Klasse und unternehmenskritische Arbeitsauslastungen, Backup, Big Data, Azure als DR-Standort | | **SLA** | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.Microsoft.com/support/legal/sla/) | | **Preise** | [Preise](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [Preise](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [Preise](http://azure.microsoft.com/pricing/details/expressroute/) | [Preise](http://azure.microsoft.com/pricing/details/expressroute/) || **Technische Dokumentation** | [Dokumentation zum VPN-Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Dokumentation zum VPN-Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [ExpressRoute-Dokumentation](https://azure.microsoft.com/documentation/services/expressroute/) | [ExpressRoute-Dokumentation](https://azure.microsoft.com/documentation/services/expressroute/) || **FAQs** | [FAQs zu VPN-Gateway](vpn-gateway-vpn-faq.md) | [FAQs zu VPN-Gateway](vpn-gateway-vpn-faq.md) | [FAQs zu ExpressRoute](../expressroute/expressroute-faqs.md) | [FAQs zu ExpressRoute](../expressroute/expressroute-faqs.md) |
                                                                                 



## Standort-zu-Standort-Verbindungen

Mit einem Standort-zu-Standort-VPN können Sie eine sichere Verbindung zwischen Ihrem lokalen Standort und dem virtuellen Netzwerk herstellen. Um eine Standort-zu-Standort-Verbindung herzustellen, wird ein VPN-Gerät im lokalen Netzwerk so konfiguriert, dass eine sichere Verbindung mit dem Azure-VPN-Gateway hergestellt wird. Nachdem die Verbindung hergestellt wurde, können Ressourcen in Ihrem lokalen Netzwerk und Ressourcen in Ihrem virtuellen Netzwerk direkt und sicher miteinander kommunizieren. Für Standort-zu-Standort-Verbindungen müssen Sie keine eigene Verbindung für jeden Clientcomputer in Ihrem lokalen Netzwerk herstellen, um auf die Ressourcen im virtuellen Netzwerk zuzugreifen.

**Verwenden Sie eine Standort-zu-Standort-Verbindung in den folgenden Fällen:**

- Sie möchten eine Hybrid-Lösung erstellen.
- Sie möchten eine Verbindung zwischen Ihrem lokalen Standort und dem virtuellen Netzwerk ohne clientseitige Konfiguration herstellen.
- Sie möchten eine dauerhafte Verbindung herstellen. 

**Anforderungen**

- Das lokale VPN-Gerät muss eine Internet-zugängliche IPv4-IP-Adresse haben. Es darf sich nicht hinter einem NAT-Gerät befinden.
- Sie müssen über ein kompatibles VPN-Gerät verfügen. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](http://go.microsoft.com/fwlink/p/?LinkID=615099). 
- Das VPN-Gerät, das Sie verwenden, muss mit dem Gatewaytyp kompatibel sein, der für Ihre Lösung erforderlich ist. Siehe [Zu VPN-Gateways](vpn-gateway-about-vpngateways.md).
- Die Gateway-SKU wirkt sich auch auf den aggregierten Durchsatz aus. Weitere Informationen finden Sie unter [Gateway-SKUs](vpn-gateway-about-vpngateways.md#gateway-skus). 

Informationen zum Konfigurieren einer Standort-zu-Standort-VPN-Gatewayverbindung finden Sie unter [Konfigurieren eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Verbindung](vpn-gateway-site-to-site-create.md).

Informationen zum Erstellen einer Standort-zu-Standort-VPN-Gatewayverbindung mit RRAS finden Sie unter [Konfigurieren eines Standort-zu-Standort-VPN mit Windows Server 2012 RRAS (Routing- und RAS-Dienst)](https://msdn.microsoft.com/library/dn636917.aspx).


## Punkt-zu-Standort-Verbindungen

Mit einem Punkt-zu-Standort-VPN können Sie auch eine sichere Verbindung mit dem virtuellen Netzwerk herstellen. In einer Punkt-zu-Standort-Konfiguration wird die Verbindung einzeln auf jedem Clientcomputer konfiguriert, den Sie mit dem virtuellen Netzwerk verbinden möchten. Für Punkt-zu-Standort-Verbindungen ist kein VPN-Gerät erforderlich. Diese Art von Verbindung verwendet einen VPN-Client, den Sie auf jedem Clientcomputer installieren. Richten Sie das VPN ein, indem Sie die Verbindung vom lokalen Clientcomputer aus manuell starten.

Punkt-zu-Standort- und Standort-zu-Standort-Konfigurationen können gleichzeitig vorhanden sein. Im Gegensatz zu Standort-zu-Standort-Verbindungen können Punkt-zu-Standort-Verbindungen jedoch nicht gleichzeitig mit einer ExpressRoute-Verbindung zum gleichen virtuellen Netzwerk konfiguriert werden.

**Verwenden Sie eine Punkt-zu-Standort-Verbindung in den folgenden Fällen:**

- Sie möchten nur einige Clients für die Verbindung mit einem virtuellen Netzwerk konfigurieren.

- Sie möchten von einem Remotestandort aus eine Verbindung zu Ihrem virtuellen Netzwerk herstellen. Zum Beispiel eine Verbindung in einem Café oder an einem Tagungsort.

- Sie haben eine Standort-zu-Standort-Verbindung, verfügen jedoch über einige Clients, die von einem Remotestandort aus eine Verbindung herstellen müssen.

- Sie haben keinen Zugriff auf ein VPN-Gerät, das die Mindestanforderungen für eine Standort-zu-Standort-Verbindung erfüllt.

- Sie haben keine Internet-zugängliche IPv4-IP-Adresse für Ihr VPN-Gerät.

Weitere Informationen zum Konfigurieren einer Punkt-zu-Standort-Verbindung finden Sie unter [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem virtuellen Netzwerk](vpn-gateway-point-to-site-create.md).

## ExpressRoute-Verbindungen

Azure ExpressRoute ermöglicht es Ihnen, private Verbindungen zwischen Azure-Datencentern und einer Infrastruktur bei Ihnen vor Ort oder in einer Kollokationsumgebung zu erstellen. ExpressRoute-Verbindungen erfolgen nicht über das öffentliche Internet und bieten eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet.

In einigen Fällen können durch die Verwendung von ExpressRoute-Verbindungen zum Übertragen von Daten zwischen lokalen Standorten und Azure auch drastische Kosteneinsparungen erzielt werden. Mit ExpressRoute können Sie Verbindungen mit Azure an einem ExpressRoute-Standort (Exchange-Anbietereinrichtung) oder direkt mit Azure von Ihrem vorhandenen WAN-Netzwerk (z. B. ein MPLS VPN) eines Netzwerkdienstanbieters aus herstellen.

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – Technische Übersicht](../expressroute/expressroute-introduction.md).


## Nächste Schritte

Weitere Informationen finden Sie unter [FAQs zu ExpressRoute](../expressroute/expressroute-faqs.md) und [FAQs zu VPN-Gateway](vpn-gateway-vpn-faq.md).

<!---HONumber=Oct15_HO2-->