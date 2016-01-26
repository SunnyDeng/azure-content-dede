<properties
   pageTitle="Voraussetzungen für den Wechsel zu ExpressRoute | Microsoft Azure"
   description="Diese Seite enthält eine Liste der Anforderungen, die erfüllt sein müssen, bevor Sie eine Azure ExpressRoute-Verbindung anfordern können."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>


# ExpressRoute-Voraussetzungen   

Zum Herstellen einer Verbindung mit den Microsoft-Clouddiensten über ExpressRoute müssen Sie sicherstellen, dass die in den Abschnitten unten genannten Voraussetzungen erfüllt sind.

## Kontoanforderungen

- Ein gültiges und aktives Microsoft Azure-Konto. Dies ist erforderlich, um die ExpressRoute-Verbindung einzurichten. Bei ExpressRoute-Verbindungen handelt es sich um Ressourcen innerhalb von Azure-Abonnements. Ein Azure-Abonnement ist eine Anforderung, auch wenn die Konnektivität auf Microsoft-Clouddienste (keine Azure-Dienste) wie z. B. Office 365-Dienste und CRM online beschränkt ist.
- Ein aktives Office 365-Abonnement (bei Nutzung von Office 365-Diensten). Weitere Informationen finden Sie im Abschnitt [Besondere Vorgaben für Office 365](#office-365-specific-requirements) in diesem Artikel.

## Beziehungen von Konnektivitätsanbietern

- Eine Beziehung mit einem Konnektivitätsanbieter aus der Liste der unterstützten Anbieter, der die Verbindung ermöglichen muss. Sie müssen über eine bestehende Geschäftsbeziehung mit Ihrem Konnektivitätsanbieter verfügen. Sie müssen sicherstellen, dass der mit dem Konnektivitätsanbieter bestehende Dienst mit ExpressRoute kompatibel ist.
- Wenn Sie einen Konnektivitätsanbieter nutzen möchten, der nicht in der Liste der unterstützten Anbieter aufgeführt ist, können Sie weiterhin eine Verbindung mit Microsoft-Clouddiensten über Exchange erstellen.
	- Fragen Sie Ihren Verbindungsanbieter, ob er an einem der Exchange-Standorte in dieser Liste vertreten ist.
	- Fordern Sie Ihren Konnektivitätsanbieter auf, Ihr Netzwerk auf den Exchange-Standort Ihrer Wahl zu erweitern.
	- Bestellen Sie eine ExpressRoute-Verbindung mit Exchange als Konnektivitätsanbieter.

## Physische Verbindung zwischen Ihrem Netzwerk und dem Konnektivitätsanbieter

Ausführliche Informationen zu Konnektivitätsmodellen finden Sie im Abschnitt zu eben jenen. Kunden müssen sicherstellen, dass ihre lokale Infrastruktur physisch mit der Infrastruktur des Anbieters über eines der beschriebenen Modelle verbunden ist.

## Redundanzanforderungen für die Konnektivität

Es gibt keine Redundanzanforderungen an die physische Konnektivität zwischen der Infrastruktur des Kunden und der des Dienstanbieters. Microsoft erfordert die Redundanz in Schicht 3. Microsoft erfordert, dass das Redundanzrouting zwischen Edge von Microsoft und dem Kundennetzwerk über den Dienstanbieter für jeden der Peerings aktiviert wird. Wenn Routingsitzungen nicht auf redundante Weise konfiguriert sind, ist die SLA zur Dienstverfügarkeit nichtig.

## Überlegungen zu IP-Adressen und Routing

Kunden- und Konnektivitätsanbieter sind verantwortlich für das Einrichten von redundanten BGP-Sitzungen mit der Edge-Infrastruktur von Microsoft. Kunden, die sich dazu entschließen, über IP-VPN-Anbieter eine Verbindung herzustellen, verlassen sich in der Regel darauf, dass Konnektivitätsanbieter die Routingkonfigurationen verwalten. Kunden, die mit Exchange zusammengestellt wurden oder über eine Punkt-zu-Ethernet-Verbindung eine Verbindung zu Microsoft herstellen, müssen redundante BGP-Sitzungen pro Peering konfigurieren, um die SLA-Anforderungen zur Verfügbarkeit zu erfüllen. Konnektivitätsanbieter können diesen Dienst mit erhöhtem Nutzen anbieten. Weitere Informationen zu Grenzwerten finden Sie in der Routingtabelle von Domänen im Artikel [ExpressRoute-Verbindungen und -Routingdomänen](expressroute-circuit-peerings.md).

## Sicherheit und Firewalls

Informationen zur Sicherheit und zu Firewalls finden Sie unter [Microsoft Clouddienste und Netzwerksicherheit](../best-practices-network-security.md).

## NAT-Konfiguration für öffentliches Azure-Peering und Microsoft-Peering

Eine ausführliche Anleitung zu Anforderungen und Konfigurationen finden Sie unter [NAT-Anforderungen für ExpressRoute](expressroute-nat.md). Wenden Sie sich an Ihren Konnektivitätsanbieter, um abzuklären, ob er die NAT-Einrichtung und -Verwaltung für Sie verwaltet. In der Regel verwalten Layer 3-Konnektivitätsanbieter NAT für Sie.

## Besondere Vorgaben für Office 365

Überprüfen Sie die folgenden Ressourcen für weitere Informationen zu Office 365-Vorgaben.

- [Planen des Netzwerks und Leistungsoptimierung für Office 365](http://aka.ms/tune)
- [Verwaltung des Netzwerkdatenverkehrs für Office 365](https://support.office.com/article/Office-365-network-traffic-management-e1da26c6-2d39-4379-af6f-4da213218408)
- Ausführliche Anleitungen zu QoS-Anforderungen und -Konfigurationen finden Sie im Artikel [Quality of Service (QoS)-Anforderungen für ExpressRoute](expressroute-qos.md). Fragen Sie bei Ihrem Konnektivitätsanbieter nach, ob mehrere Dienstklassen für Ihr VPN angeboten werden. 

## Nächste Schritte

- Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
- Einen Dienstanbieter finden. Siehe [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md).
- Sehen Sie sich die Anforderungen für [Routing](expressroute-routing.md), [NAT](expressroute-nat.md) und [Dienstqualität (QoS)](expressroute-qos.md) an.
- Konfigurieren Sie Ihre ExpressRoute-Verbindung.
	- [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md)
	- [Konfigurieren des Routings](expressroute-howto-routing-classic.md)
	- [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0121_2016-->