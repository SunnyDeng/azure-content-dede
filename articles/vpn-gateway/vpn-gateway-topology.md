<properties 
   pageTitle="VPN Gateway-Verbindungstopologien | Microsoft Azure"
   description="Informieren Sie sich über VPN Gateway-Verbindungstopologien und die verfügbaren Konfigurationstools und Bereitstellungsmodelle."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# Azure VPN Gateway-Verbindungstopologien

In diesem Artikel werden die grundlegenden VPN Gateway-Verbindungstopologien beschrieben. Die Grafiken und Beschreibungen können Ihnen als Hilfe beim Auswählen der Konfigurationstopologie dienen, die Ihre Anforderungen erfüllt. In diesem Artikel geht es zwar um die wichtigsten grundlegenden Topologien, aber Sie können auch komplexere Topologien erstellen, indem Sie die Diagramme als Anhaltspunkte verwenden.

Jede Topologie enthält eine Tabelle mit dem Bereitstellungsmodell, für das die Topologie verfügbar ist, und mit den Bereitstellungstools, die Sie zum Konfigurieren der einzelnen Topologien verwenden können. Falls ein Artikel verfügbar ist, ist jeweils ein direkter Link angegeben. Die Tabellen werden häufig aktualisiert, wenn neue Artikel und Bereitstellungstools verfügbar sind und von Ihnen genutzt werden können.

Weitere Informationen zu VPN Gateways finden Sie unter [Informationen zu VPN Gateways](vpn-gateway-about-vpngateways.md).



## Site-to-Site und Multi-Site

Eine Site-to-Site-Verbindung ist eine Verbindung über einen VPN-Tunnel vom Typ IPsec/IKE (IKEv1 oder IKEv2). Für diese Art von Verbindung ist ein VPN-Gerät oder Windows Server RRAS lokal erforderlich. Standort-zu-Standort-Verbindungen können für standortübergreifende Konfigurationen und Hybridkonfigurationen verwendet werden.


**S2S-Diagramm**

![S2S-Verbindung](./media/vpn-gateway-topology/site2site.png "Site-to-Site")

Sie können mehr als eine S2S-VPN-Verbindung mit Ihren lokalen Netzwerken erstellen und konfigurieren, wenn Sie routingbasierte Azure-VPNs verwenden. Diese Art der Konfiguration wird häufig als Multi-Site-Verbindung bezeichnet.
 

**Multi-Site-Diagramm**

![Multi-Site-Verbindung](./media/vpn-gateway-topology/multisite.png "Multi-Site")


**Verfügbare Bereitstellungsmodelle und -methoden**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet-zu-VNet

Das Verbinden eines virtuellen Netzwerks mit einem anderen virtuellen Netzwerk (VNet-zu-VNet) ähnelt dem Verbinden eines VNet mit einem lokalen Standort. Beide Verbindungstypen verwenden ein Azure VPN Gateway, um einen sicheren Tunnel mit IPSec/IKE bereitzustellen. Die VNets, die Sie verbinden, können sich in verschiedenen Regionen oder unter verschiedenen Abonnements befinden. Sie können sogar VNet-zu-VNet-Kommunikation mit Konfigurationen für mehrere Standorte kombinieren. Auf diese Weise können Sie Netzwerktopologien einrichten, die standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren.

Azure verfügt derzeit über zwei Bereitstellungsmodelle: Azure Service Management (als „klassisch“ bezeichnet) und Azure Resource Manager. Wenn Sie Azure schon länger nutzen, führen Sie Azure-VMs und Instanzrollen vermutlich in einem klassischen VNet aus, während Ihre neueren VMs und Rolleninstanzen ggf. in einem per ARM erstellten VNet ausgeführt werden. Sie können auch dann eine Verbindung zwischen virtuellen Netzwerken erstellen, wenn sich diese in unterschiedlichen Bereitstellungsmodellen befinden.


**VNet2VNet-Diagramm**

![VNet-zu-VNet-Verbindung](./media/vpn-gateway-topology/vnet2vnet.png "VNet-zu-VNet")


**Verfügbare Bereitstellungsmodelle und -methoden**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]



## Parallel bestehende Site-to-Site- und ExpressRoute-Verbindungen

ExpressRoute ist eine direkte, dedizierte Verbindung aus Ihrem WAN mit Microsoft Services, einschließlich Azure, die nicht über das öffentliche Internet verläuft. Site-to-Site-VPN-Datenverkehr wird verschlüsselt über das öffentliche Internet gesendet. Die Möglichkeit, Site-to-Site-VPN- und ExpressRoute-Verbindungen für dasselbe virtuelle Netzwerk zu konfigurieren, hat mehrere Vorteile. Sie können Site-to-Site-VPNs als sicheren Failoverpfad für ExpressRoute konfigurieren oder für die Verbindung mit Websites nutzen, die nicht Teil Ihres Netzwerks, aber über ExpressRoute verbunden sind.


**Diagramm zu gleichzeitig bestehenden Verbindungen**

![Gleichzeitig bestehende Verbindungen](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**Verfügbare Bereitstellungsmodelle und -methoden**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## Punkt-zu-Standort

Mit einer Punkt-zu-Standort-Konfiguration können Sie von einem Clientcomputer eine einzelne sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Eine VPN-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten. Dies ist eine nützliche Lösung, wenn Sie von einem Remotestandort, z.B. von zu Hause oder in einer Konferenz, eine Verbindung mit Ihrem VNet herstellen möchten. Diese Methode eignet sich auch, wenn Sie nur wenige Clients besitzen, die mit einem virtuellen Netzwerk verbunden werden müssen.

Eine Point-to-Site-Verbindung ist eine VPN-Verbindung per SSTP (Secure Socket Tunneling Protocol). Damit Punkt-zu-Standort-Verbindungen funktionieren, ist kein VPN-Gerät und keine öffentliche IP-Adresse erforderlich.

**P2S-Diagramm**

![Point-to-Site-Verbindung](./media/vpn-gateway-topology/point2site.png "Point-to-Site")

**Verfügbare Bereitstellungsmodelle und -methoden**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## Nächste Schritte

Es ist ratsam, dass Sie sich mit den Informationen in den Artikeln [Informationen zu VPN-Gateways](vpn-gateway-about-vpngateways.md) und [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md) vertraut machen. So entwickeln Sie ein besseres Verständnis für VPN Gateways, bevor Sie mit der Planung und dem Entwurf Ihrer Verbindung fortfahren.





 

<!---HONumber=AcomDC_0323_2016-->