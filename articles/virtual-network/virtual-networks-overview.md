<properties
   pageTitle="Übersicht über Azure Virtual Network (VNet)"
   description="Weitere Informationen zu virtuellen Netzwerken (VNets) in Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Virtuelle Netzwerke im Überblick

Ein virtuelles Azure-Netzwerk (VNet) ist eine Darstellung Ihres eigenen Netzwerks in der Cloud. Es ist eine logische Isolierung von der Azure-Cloud für Ihr Abonnement. Sie können die IP-Adressblöcke, DNS-Einstellungen, Sicherheitsrichtlinien und Routentabellen in diesem Netzwerk vollständig steuern. Außerdem können Sie Ihr VNet in Subnetze segmentieren und virtuelle Azure IaaS-Maschinen (VMs) und/oder [Cloud-Dienste (PaaS-Instanzen)](../cloud-services/cloud-services-choose-me.md) starten. Zudem können Sie das virtuelle Netzwerk mit einer der [Konnektivitätsoptionen](../vpn-gateway/vpn-gateway-cross-premises-options.md) in Azure mit Ihrem lokalen Netzwerk verbinden. Im Wesentlichen können Sie Ihr Netzwerk mit vollständiger Kontrolle über IP-Adressblöcke auf Azure ausdehnen und von der Azure-Skalierung auf Unternehmensebene profitieren.

Die unten stehende Abbildung zeigt ein vereinfachtes lokales Netzwerk und ermöglicht ein besseres Verständnis von VNets.

![Lokales Netzwerk](./media/virtual-networks-overview/figure01.png)

Die obige Abbildung zeigt ein lokales Netzwerk, das über einen Router mit dem öffentlichen Internet verbunden ist. Sie sehen auch eine Firewall zwischen dem Router und einer DMZ, die einen DNS-Server und eine Webserverfarm hostet. Für den Lastenausgleich der Webserverfarm wird ein Hardware-Load Balander verwendet, der über das Internet zugänglich ist und Ressourcen aus dem internen Subnetz nutzt. Das interne Subnetz ist zudem durch eine weitere Firewall von der DMZ getrennt und hostet Active Directory-Domänencontrollerserver, Datenbankserver sowie Anwendungsserver.

Das gleiche Netzwerk kann wie in der folgenden Abbildung dargestellt in Azure gehostet werden.

![Azure Virtual Network](./media/virtual-networks-overview/figure02.png)

Die Azure-Infrastruktur übernimmt die Rolle des Routers und lässt ohne jeglichen Konfigurationsbedarf den Zugriff auf Ihr VNet über das öffentliche Internet zu. Firewalls können durch Netzwerksicherheitsgruppen ersetzt werden, die auf jedes einzelne Subnetz angewendet werden. Physische Load Balancer werden durch Load Balancer mit Internetzugriff und interne Load Balancer in Azure ersetzt.

>[AZURE.NOTE] In Azure stehen zwei Bereitstellungsmodi zur Auswahl: klassisch (auch bekannt als Dienstverwaltung) und Azure-Ressourcen-Manager (ARM). Klassische VNets können einer Affinitätsgruppe hinzugefügt oder als regionales VNet erstellt werden. Wenn Sie ein VNet in einer Affinitätsgruppe haben, wird die [Migration zu einem regionalen VNet](virtual-networks-migrate-to-regional-vnet.md) empfohlen.

## Vorteile von virtuellen Netzwerken

- **Isolation** VNets sind vollständig voneinander isoliert. Daher können Sie separate Netzwerke für Entwicklung, Tests und Produktion erstellen, die die gleichen CIDR-Adressblöcke verwenden.

- **Zugriff auf das öffentliche Internet**. Alle IaaS-VMs und PaaS-Rolleninstanzen in einem VNet können standardmäßig auf das öffentliche Internet zugreifen. Der Zugriff kann mithilfe von Netzwerksicherheitsgruppen gesteuert werden.

- **Zugriff auf VMs im VNet**. PaaS-Rolleninstanzen und IaaS-VMs können im selben virtuellen Netzwerk gestartet werden und über private IP-Adressen eine Verbindung miteinander herstellen (auch, wenn sie sich in unterschiedlichen Subnetzen befinden), ohne dass dafür ein Gateway konfiguriert oder öffentliche IP-Adressen verwendet werden müssen.

- **Namensauflösung**. Azure bietet eine interne Namensauflösung für die im VNet bereitgestellten IaaS-VMs und PaaS-Rolleninstanzen. Sie können auch eigene DNS-Server bereitstellen und das VNet zur Verwendung dieser Server konfigurieren.

- **Sicherheit**. Der ein- und ausgehende Datenverkehr der virtuellen Maschinen und PaaS-Rolleninstanzen in einem VNet kann mit Netzwerksicherheitsgruppen gesteuert werden.

- **Konnektivität** VNets können mithilfe einer Standort-zu-Standort-VPN-Verbindung oder einer ExpressRoute-Verbindung miteinander und sogar mit Ihrem lokalen Datencenter verbunden werden. Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN-Gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md). Weitere Informationen zu ExpressRoute finden Sie unter [ExpressRoute – Technische Übersicht](../expressroute/expressroute-introduction.md).

    >[AZURE.NOTE] Vor der Bereitstellung von IaaS-VMs oder PaaS-Rolleninstanzen in Ihrer Azure-Umgebung müssen Sie ein VNet erstellen. ARM-basierte VMs erfordern ein VNet, und wenn Sie kein vorhandenes VNet angeben, erstellt Azure ein Standard-VNet, bei dem möglicherweise ein CIDR-Adressblockkonflikt mit Ihrem lokalen Netzwerk vorliegt. Wenn dies der Fall ist, ist es nicht möglich, das VNet mit Ihrem lokalen Netzwerk zu verbinden.

## Subnetze

Ein Subnetz ist ein Bereich von IP-Adressen im VNet. Sie können ein VNet aus Organisations- und Sicherheitsgründen in mehrere Subnetze unterteilen. VMs und PaaS-Rolleninstanzen, die in (denselben oder unterschiedlichen) Subnetzen in einem VNet bereitgestellt werden, können ohne zusätzliche Konfiguration miteinander kommunizieren. Sie können auch Routentabellen und NSGs zu einem Subnetz konfigurieren.

## IP-Adressen


Es gibt zwei Arten von IP-Adressen, die Ressourcen in Azure zugewiesen werden: *öffentliche* und *private*. Mit öffentlichen IP-Adressen können Azure-Ressourcen mit dem Internet und anderen öffentlichen Azure-Diensten wie [Azure Redis Cache](https://azure.microsoft.com/services/cache/) und [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/) kommunizieren. Private IP-Adressen ermöglichen die Kommunikation zwischen Ressourcen in einem virtuellen Netzwerk sowie per VPN verbundenen Ressourcen ohne Verwendung von per Internet zugänglichen IP-Adressen.

Weitere Informationen zu IP-Adressen in Azure finden Sie unter [IP-Adressen im virtuellen Netzwerk](virtual-network-ip-addresses-overview-arm.md).

## Azure-Load Balancer

Virtuelle Maschinen und Clouddienste in einem virtuellen Netzwerk können über Azure Load Balancer für das Internet verfügbar gemacht werden. Bei internen Branchenanwendungen ist der Lastenausgleich nur über einen internen Load Balancer möglich.

- **Externer Load Balancer**. Mit einem externen Load Balancer können Sie hohe Verfügbarkeit für IaaS-VMs und PaaS-Rolleninstanzen bereitstellen, auf die über das öffentliche Internet zugegriffen wird.

- **Interner Load Balancer**. Mit einem internen Load Balancer können Sie hohe Verfügbarkeit für IaaS-VMs und PaaS-Rolleninstanzen bereitstellen, auf die von anderen Diensten in Ihrem VNet zugegriffen wird.

Weitere Informationen zum Lastenausgleich in Azure finden Sie unter [Übersicht über Load Balancer](../load-balancer/load-balancer-overview.md).

## Netzwerksicherheitsgruppen (NSG)

Sie können Netzwerksicherheitsgruppen zum Steuern des ein- und ausgehenden Zugriffs auf NICs, VMs und Subnetze erstellen. Eine Netzwerksicherheitsgruppe enthält eine oder mehrere Regeln, die basierend auf IP-Quelladresse, Quellport, IP-Zieladresse und Zielport bestimmen, ob Datenverkehr zugelassen oder verweigert wird. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe?](virtual-networks-nsg.md).

## Virtuelle Geräte

Ein virtuelles Gerät ist eine andere VM in Ihrem VNet, die eine softwarebasierte Gerätefunktion ausführt, z. B. eine Firewall, WAN-Optimierung oder Angriffserkennung. Sie können eine Route in Azure erstellen, um den Datenverkehr Ihres VNets über ein virtuelles Gerät weiterzuleiten und dessen Funktionen zu nutzen.

Netzwerksicherheitsgruppen können z. B. verwendet werden, um Sicherheit für das VNet bereitzustellen. Allerdings bieten Netzwerksicherheitsgruppen eine Layer 4-Zugriffssteuerungsliste (ACL) für ein- und ausgehende Pakete. Wenn Sie ein Layer 7-Sicherheitsmodell verwenden möchten, müssen Sie ein Firewallgerät einsetzen.

Virtuelle Geräte sind von [benutzerdefinierten Routen und IP-Weiterleitung](virtual-networks-udr-overview.md) abhängig.

## Grenzen
Die Anzahl der zulässigen virtuellen Netzwerke in einem Abonnement ist beschränkt. Weitere Informationen finden Sie unter [Azure-Netzwerkbeschränkungen](../azure-subscription-service-limits.md#networking-limits).

## Preise
Für die Verwendung virtueller Netzwerke in Azure fallen keine Zusatzkosten an. Für die im VNET gestarteten Computerinstanzen werden die unter [Virtual Machines – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/) aufgeführten Standardpreise berechnet. Für die im VNET verwendeten [VPN-Gateways](https://azure.microsoft.com/pricing/details/vpn-gateway/) und [öffentlichen IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/) werden ebenfalls die Standardpreise berechnet.

## Nächste Schritte

- [Erstellen eines VNets](virtual-networks-create-vnet-arm-pportal.md) und von Subnetzen
- [Erstellen einer VM in einem VNet](../virtual-machines/virtual-machines-windows-tutorial.md)
- Weitere Informationen zu [Netzwerksicherheitsgruppen](virtual-networks-nsg.md)
- Weitere Informationen zu [Load Balancern](../load-balancer/load-balancer-overview.md)
- [Reservieren einer internen IP-Adresse](virtual-networks-reserved-private-ip.md)
- [Reservieren einer öffentlichen IP-Adresse](virtual-networks-reserved-public-ip.md)
- Weitere Informationen zu [benutzerdefinierten Routen und IP-Weiterleitung](virtual-networks-udr-overview.md)

<!---HONumber=AcomDC_0218_2016-->