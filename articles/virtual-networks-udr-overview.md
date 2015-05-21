<properties 
   pageTitle="Übersicht über benutzerdefinierte Routen und die IP-Weiterleitung"
   description="Übersicht über benutzerdefinierte Routen und die IP-Weiterleitung"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Benutzerdefinierte Routen und IP-Weiterleitung
Azure verwendet eine Routentabelle, um zu entscheiden, wie der IP-Datenverkehr basierend auf dem Ziel der einzelnen Pakete weitergeleitet wird. Obwohl Azure eine auf den Einstellungen des virtuellen Netzwerks beruhende Standardroutentabelle bereitstellt, müssen Sie dieser möglicherweise benutzerdefinierte Routen hinzufügen. Der häufigste Grund für benutzerdefinierte Einträge in die Routentabelle ist die Verwendung eines virtuellen Geräts in der Azure-Umgebung. Berücksichtigen Sie das in der folgenden Abbildung dargestellte Szenario. Angenommen, Sie möchten sicherstellen, dass der gesamte Datenverkehr vom Front-End-Subnetz zur mittleren Ebene und den gesicherten Subnetzen ein virtuelles Firewallgerät durchläuft. Wenn Sie das Gerät einfach Ihrem virtuellen Netzwerk hinzufügen und mit den verschiedenen Subnetzen verbinden, erreichen Sie dieses Ziel nicht. Sie müssen zusätzlich die Routingtabelle für Ihr Subnetz ändern, um sicherzustellen, dass die Pakete an das virtuelle Firewallgerät weitergeleitet werden.

Dasselbe gilt, wenn Sie ein virtuelles NAT-Gerät zur Steuerung des Datenverkehrs zwischen dem virtuellen Azure-Netzwerk und dem Internet implementieren. Um sicherzustellen, dass das virtuelle Gerät verwendet wird, müssen Sie eine Route erstellen, die dafür sorgt, dass der gesamte Datenverkehr für das Internet zum virtuellen Gerät weitergeleitet werden muss.

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

## Standardrouten
Alle in einem virtuellen Netzwerk erstellten Subnetze werden automatisch einer Routentabelle zugeordnet, die die folgenden Standardroutenregeln enthält: 
- **Local Vnet Rule**: Diese Regel wird automatisch für jedes Subnetz in einem virtuellen Netzwerk erstellt. Sie gibt an, dass eine direkte Verbindung zwischen den virtuellen Computern im VNet besteht und dass kein unmittelbarer nächster Hop vorhanden ist. 
- **On-premises Rule**: Diese Regel gilt für den gesamten Datenverkehr zum lokalen Adressbereich, wobei das VPN-Gateway als die nächstes Hop-Ziel fungiert. 
- **Internet Rule**: Diese Regel gilt für den gesamten Datenverkehr in Richtung öffentliches Internet, für den das Infrastrukturinternet-Gateway als nächster Hop fungiert.

## BGP-Routen
Wenn Sie über eine ExpressRoute-Verbindung zwischen dem lokalen Netzwerk und Azure verfügen, können Sie BGP für das Weitergeben der Routen aus Ihrem lokalen Netzwerk an Azure aktivieren. Diese BGP-Routen werden auf die gleiche Weise wie Standard- und benutzerdefinierte Routen in den einzelnen Azure-Subnetzen verwendet. Weitere Informationen finden Sie unter [Einführung zu ExpressRoute](../expressroute-introduction).

[AZURE.IMPORTANT]Sie können die Azure-Umgebung für die Verwendung der Tunnelerzwingung über das lokale Netzwerk konfigurieren, indem Sie für das Subnetz 0.0.0.0/0 eine benutzerdefinierte Route erstellen, die als nächsten Hop das VPN-Gateway verwendet. Dies funktioniert nur mit einem VPN-Gateway, nicht jedoch mit ExpressRoute. In ExpressRoute wird die Tunnelerzwingung über BGP konfiguriert.

## Benutzerdefinierte Routen
Die oben angegebenen Standardrouten können in der Azure-Umgebung nicht angezeigt werden. Zudem handelt es sich in den meisten Umgebungen um die einzigen erforderlichen Routen. Möglicherweise müssen Sie in bestimmten Fällen jedoch eine Routentabelle erstellen und dieser eine oder mehrere Routen hinzufügen. Die gilt z. B. für:

- Tunnelerzwingung für das Internet über das lokale Netzwerk.
- Verwendung von virtuellen Geräten in Ihrer Azure-Umgebung.
- 
In den oben genannten Szenarios müssen Sie eine Routentabelle erstellen und dieser benutzerdefinierte Routen hinzufügen. Sie können über mehrere Routentabellen verfügen, wobei eine Routentabelle einem oder mehreren Subnetzen zugeordnet werden kann. Die einzelnen Subnetze können jeweils nur einer Routentabelle zugeordnet werden. Alle virtuellen Computer und Clouddienste in einem Subnetz verwenden die diesem Subnetz zugeordnete Routentabelle.

Für die Subnetze gelten solange Standardrouten, bis diesen eine Routentabelle zugeordnet wurde. Sobald eine Zuordnung vorhanden ist, erfolgt das Routing beruhend auf der auf längsten Präfixübereinstimmung zwischen den benutzerdefinierten und Standardrouten. Wenn mehrere Routen mit identischer längster Präfixübereinstimmung vorhanden sind, wird die Route in der folgenden Reihenfolge beruhend ihrem Ursprung ausgewählt:

1. Benutzerdefinierte Route
1. BGP-Route (bei Verwendung von ExpressRoute)
1. Standardroute

[AZURE.IMPORTANT]Benutzerdefinierte Routen sind nur für virtuelle Azure-Computer und Clouddienste möglich. Wenn Sie beispielsweise zwischen dem lokalen Netzwerk und Azure ein virtuelles Firewallgerät hinzufügen möchten, müssen Sie eine benutzerdefinierte Route für die Azure-Routentabellen erstellen, die den gesamten Datenverkehr zum lokalen Adressraum an das virtuelle Gerät weiterleitet. Allerdings durchläuft der eingehende Datenverkehr vom lokalen Adressraum das VPN-Gateway oder den ExpressRoute-Kreis, um unter Umgehung des virtuellen Geräts direkt zur Azure-Umgebung zu gelangen.

## SSL-Weiterleitung
Wie oben angeführt, ist einer der wichtigsten Gründe für das Erstellen einer benutzerdefinierten Route das Weiterleiten von Datenverkehr an virtuelle Geräte. Ein virtuelles Gerät ist letztlich nur ein virtueller Computer, der eine Anwendung zur Verarbeitung des Netzwerkverkehrs ausführt, z. B. eine Firewall oder ein NAT-Gerät.

Dieser virtuelle Computer muss eingehenden Datenverkehr empfangen können, der nicht an ihn selbst adressiert ist. Damit ein virtueller Computer an andere Ziele gerichteten Datenverkehr empfangen kann, müssen Sie auf dem virtuellen Computer die IP-Weiterleitung aktivieren.

## Weitere Informationen

[Erstellen von Routen und Aktivieren der IP-Weiterleitung in Azure](../virtual-networks-udr-how-to)

[Öffentliche IP auf Instanzebene](../virtual-networks-instance-level-public-ip)

[Virtuelle Netzwerke im Überblick](https://msdn.microsoft.com/library/azure/jj156007.aspx)

<!--HONumber=52-->
