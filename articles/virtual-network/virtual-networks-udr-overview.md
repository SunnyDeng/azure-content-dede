<properties 
   pageTitle="Übersicht über benutzerdefinierte Routen und die IP-Weiterleitung"
   description="Grundlegende Informationen zu UDR und IP-Weiterleitung"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2015"
   ms.author="telmos" />

# Benutzerdefinierte Routen und IP-Weiterleitung
Wenn Sie virtuelle Computer in einem virtuellen Netzwerk (VNet) in Azure hinzufügen, werden Sie feststellen, dass die virtuellen Computer automatisch über das Netzwerk miteinander kommunizieren können. Sie müssen kein Gateway angeben, auch wenn die virtuellen Computer sich in unterschiedlichen Subnetzen befinden. Das gleiche gilt für die Kommunikation zwischen den virtuellen Computern und dem öffentlichen Internet und sogar für das lokale Netzwerk, wenn eine Hybridverbindung zwischen Azure und Ihrem eigenen Rechenzentrum vorhanden ist.

Dieser Kommunikationsfluss ist möglich, weil Azure eine Reihe von Systemrouten verwendet, um den IP-Datenverkehr zu definieren. Mit Systemrouten kann der Kommunikationsfluss in den folgenden Szenarien gesteuert werden:

- Aus dem gleichen Subnetz.
- Aus einem Subnetz in ein anderes Subnetz innerhalb eines VNet.
- Von virtuellen Computern zum Internet.
- Aus einem VNet in ein anderes VNet über ein VPN-Gateway.
- Aus einem VNet in ein lokales Netzwerk über ein VPN-Gateway.

Die folgende Abbildung zeigt ein einfaches Setup mit einem VNet, zwei Subnetzen und einigen virtuellen Computern sowie den Systemrouten, die den IP-Datenverkehr ermöglichen.

![Systemrouten in Azure](./media/virtual-networks-udr-overview/Figure1.png)

Obwohl durch die Verwendung von Systemrouten der Datenverkehr für Ihre Bereitstellung automatisch ermöglicht wird, gibt es Fälle, in denen Sie das Routing von Paketen über ein virtuelles Gerät steuern möchten. Hierzu können Sie benutzerdefinierte Routen erstellen, die den nächsten Hop für Pakete in ein bestimmtes Subnetz angeben, und festlegen, dass die Pakete stattdessen an Ihre virtuelle Anwendung gesendet werden sollen. Außerdem können Sie die IP-Weiterleitung für den virtuellen Computer aktivieren, der als virtuelles Gerät ausgeführt wird.

Die folgende Abbildung zeigt ein Beispiel für benutzerdefinierte Routen und IP-Weiterleitung, mit denen erzwungen wird, dass an ein Subnetz gesendete Pakete durch ein virtuelles Gerät in ein drittes Subnetz gesendet werden.

![Systemrouten in Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT]Benutzerdefinierte Routen werden nur auf den ein Subnetz verlassenden Datenverkehr angewendet. Sie können keine Routen erstellen, um anzugeben, wie Datenverkehr z. B. aus dem Internet in ein Subnetz gelangt. Das Gerät, an das Sie Datenverkehr weiterleiten, darf sich außerdem nicht in dem Subnetz befinden, aus dem der Datenverkehr stammt. Erstellen Sie immer ein separates Subnetz für Ihre Geräte.

## Routing
Pakete werden über ein TCP/IP-Netzwerk weitergeleitet, das auf einer Routentabelle beruht, die auf den einzelnen Knoten im physischen Netzwerk definiert wird. Eine Routentabelle ist eine Sammlung der einzelnen Routen, anhand derer entschieden wird, wohin die Pakete beruhend auf der Ziel-IP-Adresse weitergeleitet werden. Eine Route besteht aus den folgenden Elementen:

- **Adresspräfix**. Das Ziel-CIDR, das für die Route gilt, z. B. 10.1.0.0/16.
- **Nächster Hop-Typ**. Der Azure-Hop-Typ, an den das Paket gesendet werden soll. Mögliche Werte:
	- **Lokal**. Entspricht dem lokalen virtuellen Netzwerk. Wenn Sie z. B. im gleichen virtuellen Netzwerk über die beiden Subnetze 10.1.0.0/16 und 10.2.0.0/16 verfügen, weist die Route für die einzelnen Subnetze in der Routentabelle für den nächsten Hop den Wert *Lokal* auf.
	- **VPN-Gateway**. Entspricht einem Azure S2S-VPN-Gateway. 
	- **Internet**. Entspricht dem Standard-Internet-Gateway der Azure-Infrastruktur. 
	- **Virtuelles Gerät**. Entspricht einem virtuellen Gerät, das Sie Ihrem virtuellen Azure-Netzwerk hinzugefügt haben.
	- **NULL**. Entspricht einem schwarzen Loch. Pakete, die an ein schwarzes Loch weitergeleitet werden, werden überhaupt nicht weitergeleitet.
- **Wert für den nächsten Hop**. Der Wert für den nächsten Hop enthält die IP-Adresse, an die die Pakete weitergeleitet werden sollen. Die Werte für den nächsten Hop dürfen nur für Routen verwendet werden, für die als Typ des nächsten Hops *Virtuelles Gerät* ausgewählt wurde.

## Systemrouten
Jedes in einem virtuellen Netzwerk erstellte Subnetz wird automatisch einer Routentabelle zugeordnet, die folgende Systemroutenregeln enthält:

- **Regel für das lokale VNet**: Diese Regel wird automatisch für jedes Subnetz in einem virtuellen Netzwerk erstellt. Sie gibt an, dass eine direkte Verbindung zwischen den virtuellen Computern im VNet besteht und dazwischen kein nächster Hop vorhanden ist.
- **Lokale Regel**: Diese Regel gilt für den gesamten Datenverkehr an den lokalen Adressbereich. Hierbei wird das VPN-Gateway als der nächste Hop verwendet.
- **Internetregel**: Diese Regel behandelt den gesamten Datenverkehr in das öffentliche Internet und verwendet das Infrastruktur-Internetgateway als nächsten Hop für den gesamten Datenverkehr ins Internet.

## Benutzerdefinierte Routen
Für die meisten Umgebungen benötigen Sie nur die von Azure bereits definierten Systemrouten. Möglicherweise müssen Sie in bestimmten Fällen jedoch eine Routentabelle erstellen und dieser eine oder mehrere Routen hinzufügen. Die gilt z. B. für:

- Tunnelerzwingung für das Internet über das lokale Netzwerk.
- Verwendung von virtuellen Geräten in Ihrer Azure-Umgebung.

In den oben genannten Szenarios müssen Sie eine Routentabelle erstellen und dieser benutzerdefinierte Routen hinzufügen. Sie können über mehrere Routentabellen verfügen, wobei eine Routentabelle einem oder mehreren Subnetzen zugeordnet werden kann. Die einzelnen Subnetze können jeweils nur einer Routentabelle zugeordnet werden. Alle virtuellen Computer und Clouddienste in einem Subnetz verwenden die diesem Subnetz zugeordnete Routentabelle.

Für die Subnetze gelten solange Systemrouten, bis dem Subnetz eine Routentabelle zugeordnet wird. Sobald eine Zuordnung vorhanden ist, erfolgt das Routing beruhend auf der auf längsten Präfixübereinstimmung zwischen den benutzerdefinierten Routen und Systemrouten. Wenn mehrere Routen mit identischer längster Präfixübereinstimmung vorhanden sind, wird die Route in der folgenden Reihenfolge beruhend ihrem Ursprung ausgewählt:

1. Benutzerdefinierte Route
1. BGP-Route (bei Verwendung von ExpressRoute)
1. Systemroute

Informationen zum Erstellen von benutzerdefinierten Routen finden Sie unter [Erstellen von Routen und Aktivieren der IP-Weiterleitung in Azure](../virtual-networks-udr-how-to#How-to-manage-routes).

>[AZURE.IMPORTANT]Benutzerdefinierte Routen sind nur für virtuelle Azure-Computer und Clouddienste möglich. Wenn Sie beispielsweise zwischen dem lokalen Netzwerk und Azure ein virtuelles Firewallgerät hinzufügen möchten, müssen Sie eine benutzerdefinierte Route für die Azure-Routentabellen erstellen, die den gesamten Datenverkehr zum lokalen Adressraum an das virtuelle Gerät weiterleitet. Allerdings durchläuft der eingehende Datenverkehr vom lokalen Adressraum das VPN-Gateway oder den ExpressRoute-Kreis, um unter Umgehung des virtuellen Geräts direkt zur Azure-Umgebung zu gelangen.

## BGP-Routen
Wenn Sie über eine ExpressRoute-Verbindung zwischen dem lokalen Netzwerk und Azure verfügen, können Sie BGP für das Weitergeben der Routen aus Ihrem lokalen Netzwerk an Azure aktivieren. Diese BGP-Routen werden auf die gleiche Weise wie System- und benutzerdefinierte Routen in den einzelnen Azure-Subnetzen verwendet. Weitere Informationen finden Sie unter [Einführung zu ExpressRoute](../expressroute-introduction).

>[AZURE.IMPORTANT]Sie können die Azure-Umgebung für die Verwendung der Tunnelerzwingung über das lokale Netzwerk konfigurieren, indem Sie für das Subnetz 0.0.0.0/0 eine benutzerdefinierte Route erstellen, die als nächsten Hop das VPN-Gateway verwendet. Dies funktioniert nur mit einem VPN-Gateway, nicht jedoch mit ExpressRoute. In ExpressRoute wird die Tunnelerzwingung über BGP konfiguriert.

## SSL-Weiterleitung
Wie oben angeführt, ist einer der wichtigsten Gründe für das Erstellen einer benutzerdefinierten Route das Weiterleiten von Datenverkehr an virtuelle Geräte. Ein virtuelles Gerät ist letztlich nur ein virtueller Computer, der eine Anwendung zur Verarbeitung des Netzwerkverkehrs ausführt, z. B. eine Firewall oder ein NAT-Gerät.

Dieser virtuelle Computer muss eingehenden Datenverkehr empfangen können, der nicht an ihn selbst adressiert ist. Damit ein virtueller Computer an andere Ziele gerichteten Datenverkehr empfangen kann, müssen Sie für den virtuellen Computer die IP-Weiterleitung aktivieren. Hierbei handelt es sich um eine Azure-Einstellung, keine Einstellung im Gastbetriebssystem.

## Nächste Schritte

- Erfahren Sie, wie Sie [Routen im Ressourcen-Manager-Bereitstellungsmodell erstellen](../virtual-network-create-udr-arm-template) und diese Subnetzen zuordnen. 
- Erfahren Sie, wie Sie [Routen im klassischen Bereitstellungsmodell erstellen](../virtual-network-create-udr-classic-ps) und diese Subnetzen zuordnen.

<!---HONumber=Oct15_HO3-->