<properties
   pageTitle="Übersicht über Azure Virtual Network (VNet)"
   description="Weitere Informationen zu virtuellen Netzwerken (VNets) in Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="telmos" />

# Virtuelle Netzwerke im Überblick

Ein virtuelles Azure-Netzwerk \(VNet\) ist eine Darstellung Ihres eigenen Netzwerks in der Cloud. Sie können Ihre Azure-Netzwerkeinstellungen steuern und DHCP-Adressblöcke, DNS-Einstellungen, Sicherheitsrichtlinien sowie das Routing definieren. Außerdem können Sie Ihr VNet in Subnetze segmentieren und virtuelle Azure-IaaS-Computer \(VMs\) und PaaS-Rolleninstanzen genauso bereitstellen wie Sie physische und virtuelle Computer im lokalen Datencenter bereitstellen. Im Wesentlichen bietet ein VNet Ihnen die Möglichkeit, Ihr Netzwerk unter Verwendung Ihrer eigenen IP-Adressblöcke auf Azure auszudehnen.

Die unten stehende Abbildung zeigt ein vereinfachtes lokales Netzwerk und ermöglicht ein besseres Verständnis von VNets.

![Lokales Netzwerk](./media/virtual-networks-overview/figure01.png)

Die obige Abbildung zeigt ein lokales Netzwerk, das über einen Router mit dem öffentlichen Internet verbunden ist. Sie sehen auch eine Firewall zwischen dem Router und einer DMZ, die einen DNS-Server und eine Webserverfarm hostet. Für den Lastenausgleich der Webserverfarm wird ein Hardware-Load Balander verwendet, der über das Internet zugänglich ist und Ressourcen aus dem internen Subnetz nutzt. Das interne Subnetz ist zudem durch eine weitere Firewall von der DMZ getrennt und hostet Active Directory-Domänencontrollerserver, Datenbankserver sowie Anwendungsserver.

Das gleiche Netzwerk kann wie in der folgenden Abbildung dargestellt in Azure gehostet werden.

![Azure Virtual Network](./media/virtual-networks-overview/figure02.png)

Die Azure-Infrastruktur übernimmt die Rolle des Routers und lässt ohne jeglichen Konfigurationsbedarf den Zugriff auf Ihr VNet über das öffentliche Internet zu. Firewalls können durch Netzwerksicherheitsgruppen ersetzt werden, die auf jedes einzelne Subnetz angewendet werden. Physische Load Balancer werden durch Load Balancer mit Internetzugriff und interne Load Balancer in Azure ersetzt.

## Virtuelle Netzwerke

VNets bieten die folgenden Dienste für IaaS-VMs und auf ihnen bereitgestellte PaaS-Rolleninstanzen:

- **Isolation** VNets sind vollständig voneinander isoliert. Daher können Sie separate VNets für Entwicklung, Tests und Produktion erstellen, die die gleichen CIDR-Adressblöcke verwenden.

- **Einschluss**. VNets können nicht mehrere Azure-Regionen umfassen.

    >[AZURE.NOTE]In Azure stehen zwei Bereitstellungsmodi zur Auswahl: klassisch \(auch bekannt als Dienstverwaltung\) und Azure-Ressourcen-Manager \(ARM\). Klassische VNets können einer Affinitätsgruppe hinzugefügt oder als regionales VNet erstellt werden. Wenn Sie ein VNet in einer Affinitätsgruppe haben, wird die [Migration zu einem regionalen VNet](./virtual-networks-migrate-to-regional-vnet.md) empfohlen.

- **Zugriff auf das öffentliche Internet**. Alle IaaS-VMs und PaaS-Rolleninstanzen in einem VNet können standardmäßig auf das öffentliche Internet zugreifen. Der Zugriff kann mithilfe von Netzwerksicherheitsgruppen gesteuert werden.

- **Zugriff auf VMs im VNet**. IaaS-VMs und PaaS-Rolleninstanzen können im selben VNet eine Verbindung miteinander herstellen \(auch, wenn sie sich in unterschiedlichen Subnetzen befinden\), ohne dass dafür ein Gateway konfiguriert oder öffentliche IP-Adressen verwendet werden müssen. Ihre PaaS- und IaaS-Umgebungen werden somit vereint.

- **Namensauflösung**. Azure bietet eine interne Namensauflösung für die im VNet bereitgestellten IaaS-VMs und PaaS-Rolleninstanzen. Sie können auch eigene DNS-Server bereitstellen und das VNet zur Verwendung dieser Server konfigurieren.

- **Konnektivität** VNets können mithilfe einer Standort-zu-Standort-VPN-Verbindung oder einer ExpressRoute-Verbindung miteinander und sogar mit Ihrem lokalen Datencenter verbunden werden. Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN-Gateways](./vpn-gateway-about-vpngateways.md). Weitere Informationen zu ExpressRoute finden Sie unter [ExpressRoute – Technische Übersicht](./expressroute-introduction.md).

    >[AZURE.NOTE]Vor der Bereitstellung von IaaS-VMs oder PaaS-Rolleninstanzen in Ihrer Azure-Umgebung müssen Sie ein VNet erstellen. ARM-basierte VMs erfordern ein VNet, und wenn Sie kein vorhandenes VNet angeben, erstellt Azure ein Standard-VNet, bei dem möglicherweise ein CIDR-Adressblockkonflikt mit Ihrem lokalen Netzwerk vorliegt. Wenn dies der Fall ist, ist es nicht möglich, das VNet mit Ihrem lokalen Netzwerk zu verbinden.

## Subnetze

Sie können Ihr VNet aus Organisations- und Sicherheitsgründen in mehrere Subnetze unterteilen. Subnetze in einem VNet können ohne zusätzliche Konfiguration miteinander kommunizieren. Sie können auch Routingeinstellungen auf der Subnetzebene ändern und Subnetze Netzwerksicherheitsgruppen zuweisen.

## IP-Adressen

Es gibt zwei Arten von IP-Adressen, die Komponenten in Azure zugewiesen werden: öffentliche und private. Den Netzwerkkarten \(NICs\) der in einem Azure-Subnetz bereitgestellten IaaS-VMs und PaaS-Rolleninstanzen wird basierend auf den CIDR-Adressblöcken des Subnetzes automatisch eine private IP-Adresse zugewiesen. Sie können Ihren IaaS-VMs und PaaS-Rolleninstanzen auch eine öffentliche IP-Adresse zuweisen.

Diese IP-Adressen sind dynamisch, d. h. sie können sich jederzeit ändern. Wenn Sie sicherstellen möchten, dass die IP-Adresse für bestimmte Dienste immer gleich bleibt, können Sie eine IP-Adresse reservieren und so zu einer statischen Adresse machen.

## Azure-Load Balancer

In Azure sind zwei Load Balancer-Typen verfügbar:

- **Externer Load Balancer**. Mit einem externen Load Balancer können Sie hohe Verfügbarkeit für IaaS-VMs und PaaS-Rolleninstanzen bereitstellen, auf die über das öffentliche Internet zugegriffen wird.

- **Interner Load Balancer**. Mit einem internen Load Balancer können Sie hohe Verfügbarkeit für IaaS-VMs und PaaS-Rolleninstanzen bereitstellen, auf die von anderen Diensten in Ihrem VNet zugegriffen wird.

Weitere Informationen zum Lastenausgleich in Azure finden Sie unter [Übersicht über Load Balancer](../load-balancer-overview.md).

## Netzwerksicherheitsgruppen

Sie können Netzwerksicherheitsgruppen zum Steuern des ein- und ausgehenden Zugriffs auf NICs, VMs und Subnetze erstellen. Eine Netzwerksicherheitsgruppe enthält eine oder mehrere Regeln, die basierend auf IP-Quelladresse, Quellport, IP-Zieladresse und Zielport bestimmen, ob Datenverkehr zugelassen oder verweigert wird. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe?](../virtual-networks-nsg.md).

## Virtuelle Geräte

Ein virtuelles Gerät ist eine andere VM in Ihrem VNet, die eine softwarebasierte Gerätefunktion ausführt, z. B. eine Firewall, WAN-Optimierung oder Angriffserkennung. Sie können eine Route in Azure erstellen, um den Datenverkehr Ihres VNets über ein virtuelles Gerät weiterzuleiten und dessen Funktionen zu nutzen.

Netzwerksicherheitsgruppen können z. B. verwendet werden, um Sicherheit für das VNet bereitzustellen. Allerdings bieten Netzwerksicherheitsgruppen eine Layer 4-Zugriffssteuerungsliste \(ACL\) für ein- und ausgehende Pakete. Wenn Sie ein Layer 7-Sicherheitsmodell verwenden möchten, müssen Sie ein Firewallgerät einsetzen.

Virtuelle Geräte sind von [benutzerdefinierten Routen und IP-Weiterleitung](../virtual-networks-udr-overview.md) abhängig.

## Nächste Schritte

- [Erstellen eines VNets](../virtual-networks-create-a-vnet.md) und von Subnetzen
- [Erstellen einer VM in einem VNet](../virtual-machines-windows-tutorial.md)
- Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-networks-nsg.md)
- Weitere Informationen zu [Load Balancern](../load-balancer-overview.md)
- [Reservieren einer internen IP-Adresse](../virtual-networks-reserved-private-ip.md)
- [Reservieren einer öffentlichen IP-Adresse](../virtual-networks-reserved-public-ip.md)
- Weitere Informationen zu [benutzerdefinierten Routen und IP-Weiterleitung](virtual-networks-udr-overview.md)

<!---HONumber=August15_HO7-->