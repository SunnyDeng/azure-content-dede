| | **Punkt-zu-Standort** | **Standort-zu-Standort** | **ExpressRoute** |
|------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Von Azure unterstützte Dienste** | Cloud Services und Virtual Machines | Cloud Services und Virtual Machines | [Dienstliste](../expressroute/expressroute-faqs.md#supported-services) |
| **Typische Bandbreiten** | In der Regel insgesamt < 100 MBit/s | In der Regel insgesamt < 100 MBit/s | 50 MBit/s, 100 MBit/s, 200 MBit/s, 500 MBit/s, 1 GBit/s, 2 GBit/s, 5 GBit/s, 10 GBit/s |
| **Unterstützte Protokolle** | Secure Sockets Tunneling Protocol (SSTP) | IPsec | Direkte Verbindung über VLANs, VPN-Technologien des NSP (MPLS, VPLS ...) |
| **Routing** | Routenbasiert (dynamisch) | Wir unterstützen richtlinienbasierte (statisches Routing) und routenbasierte (dynamisches Routing) VPNs | BGP |
| **Verbindungsstabilität** | Aktiv-passiv | Aktiv-passiv | Aktiv-aktiv |
| **Typisches Anwendungsbeispiel** | Erstellen von Prototypen, Entwicklungs-/Test-/Laborszenarios für Cloud Services und Virtual Machines | Entwicklungs-/Test-/Laborszenarios und kleine Produktionsworkloads für Cloud Services und Virtual Machines | Zugriff auf alle Azure-Dienste (überprüfte Liste), unternehmensbezogene und wichtige Workloads, Sicherung, Big Data, Azure als DR-Standort |
| **SLA** | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Preise** | [Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/) | [Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/) | [Preise](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Technische Dokumentation** | [VPN Gateway-Dokumentation](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [VPN Gateway-Dokumentation](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [ExpressRoute-Dokumentation](https://azure.microsoft.com/documentation/services/expressroute/) |
| **HÄUFIG GESTELLTE FRAGEN** | [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md) | [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md) | [ExpressRoute – FAQ](../expressroute/expressroute-faqs.md) |

<!---HONumber=AcomDC_0224_2016-->