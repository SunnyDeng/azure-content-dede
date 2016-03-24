<properties 
   pageTitle="Übersicht über IPv6 | Microsoft Azure"
   description="Erfahren Sie mehr über die IPv6-Adressierung in Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="telmos" />

# IPv6-Support in Azure

Das IPv6-Protokoll wurde ursprünglich im Jahr 1998 von der Internet Engineering Task Force (IETF) eingeführt, um der Einschränkung von 32-Bit-IPv4-Adressen im öffentlichen Internet zu begegnen. Durch die erhöhte Anzahl der mit dem Internet verbundenen mobilen Geräte und dem Aufstieg des Internets der Dinge (IoT) werden IPv6-Adressen im öffentlichen Internet immer häufiger eingesetzt.

>[AZURE.IMPORTANT] IPv6 in Azure befindet sich derzeit in der privaten Vorschauversion und ist nicht für alle Kunden verfügbar. Sobald diese Funktion allen Kunden zur Verfügung steht, wird diese Seite aktualisiert.

Sie können IPv6-Adressen auf Ihren Azure-IaaS-VMs in den Ressourcen-Manager-Bereitstellungen für aus- und eingehende Verbindungen mit dem öffentlichen Internet verwenden. In der folgenden Liste werden die IPv6-Funktionen in Azure beschrieben.

- **IaaS-VMs im Ressourcen-Manager**
	- VMs haben einen IPv4- und IPv6-Endpunkt.
	- Virtuelle Computer können sich über eine Lasten ausgleichende, öffentliche IP-Adresse für eingehende Verbindungen (Lastenausgleich und NAT) und ausgehende Verbindungen (SNAT) mit dem Internet verbinden.
	- VMs verbinden sich mit allen Azure-Diensten über ihre IPv4-Endpunkte.
	- VMs verwenden nur IPv4-Endpunkte für die Ost-West-Kommunikation.
- **Azure DNS**
	- Azure DNS unterstützt IPv6 und stellt AAAA- und IPv4 A-Einträge zur Verfügung.
	- Azure DNS antwortet auf Anfragen mithilfe der AAAA- und A-Einträge für Suchen, wenn beide vorhanden sind. 
- **Office 365**
	- Office 365-Dienste unterstützen IPv6.
	- Sie können sich von Ihrem lokalen Netzwerk aus mithilfe einer ExpressRoute-Verbindung mit IPv6-Ressourcen in Office 365 verbinden.

## IaaS-VMs im Ressourcen-Manager

Sie können IPv6-Verbindungen nur mit dem Ressourcen-Manager-Bereitstellungsmodell nutzen. Obwohl alle virtuellen Computer in einer Ressourcen-Manager-Bereitstellung über IPv4- und IPv6-Endpunkte verfügen, kann der IPv6-Endpunkt für die Verbindung mit dem öffentlichen Internet über einen Lastenausgleich nur wie unten dargestellt verwendet werden.

![IPv6-Konnektivität](./media/virtual-network-ipv6-overview/figure1.png)

## Azure DNS

Sie können IPv6-AAAA-Einträge in Azure DNS hosten. Wenn Azure DNS eine Abfrage für eine Ressource empfängt, antwortet der Dienst mit allen verfügbaren Einträgen für den Hostnamen. Wenn Sie beispielsweise einen A-und einen AAAA-Eintrag für einen angegebenen Hostnamen (z. B. www.contoso.com) haben, sendet Azure DNS beide Adressen als Antwort auf die Abfrage. Es ist Aufgabe des DNS-Clients, zu entscheiden, ob die IPv4- oder die IPv6-Endpunktverbindung verwendet wird, sobald er die Antwort von Azure DNS oder einem anderen, AAAA-Einträge unterstützenden DNS-Server empfängt

>[AZURE.NOTE] Windows-Computer bevorzugen immer eine IPv6- vor einer IPv4-Verbindung.

## Office 365

Sie können IPv6-Konnektivität zwischen Ihrem lokalen Netzwerk und Office 365 mithilfe einer ExpressRoute-Verbindung und Microsoft-Peering verwenden. Stellen Sie sicher, dass Sie die [Funktionsweise von ExpressRoute](../expressroute/expressroute-introduction.md) im Zusammenhang mit Microsoft-Peering verstehen.

## Nächste Schritte

- Erfahren Sie mehr über [Azure DNS](../dns/dns-overview.md).
- Erfahren Sie mehr zu [ExpressRoute](../expressroute/expressroute-introduction.md).

<!---HONumber=AcomDC_0309_2016-->