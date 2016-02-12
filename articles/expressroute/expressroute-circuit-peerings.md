<properties 
   pageTitle="ExpressRoute-Verbindungen und Routingdomänen | Microsoft Azure"
   description="Diese Seite enthält eine Übersicht über ExpressRoute-Verbindungen und Routingdomänen."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# ExpressRoute-Verbindungen und Routingdomänen

 Für eine Verbindung Ihrer lokalen Infrastruktur mit Microsoft über einen Konnektivitätsanbieter müssen Sie eine *ExpressRoute-Verbindung* anfordern. Die folgende Abbildung bietet eine logische Darstellung der Konnektivität zwischen Ihrem WAN und Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## ExpressRoute-Verbindungen

Eine *ExpressRoute-Verbindung* stellt eine logische Verbindung zwischen der lokalen Infrastruktur und den Microsoft-Clouddiensten über einen Konnektivitätsanbieter dar. Sie können mehrere ExpressRoute-Verbindungen bestellen. Alle Verbindungen können sich in derselben Region oder in verschiedenen Regionen befinden und über verschiedene Konnektivitätsanbieter mit dem jeweiligen Standort verbunden sein.

ExpressRoute-Verbindungen werden keinen physischen Entitäten zugeordnet. Eine Verbindung wird eindeutig durch eine Standard-GUID, einen sogenannten Dienstschlüssel, identifiziert. Der Dienstschlüssel ist die einzige Information, die zwischen Microsoft, dem Konnektivitätsanbieter und Ihnen ausgetauscht wird. Der Dienstschlüssel unterliegt aus Sicherheitsgründen nicht der Geheimhaltung. Zwischen einer ExpressRoute-Verbindung und dem Dienstschlüssel besteht eine 1:1-Zuordnung.

Eine ExpressRoute-Verbindung kann bis zu drei unabhängige Peerings aufweisen: öffentliches Azure-Peering, privates Azure-Peering und Microsoft-Peering. Jedes Peering besteht aus einem Paar unabhängiger BGP-Sitzungen, die für hohe Verfügbarkeit jeweils redundant konfiguriert sind. Zwischen einer ExpressRoute-Verbindung und Routingdomänen besteht eine 1:N-Zuordnung (1 <= N <= 3). Für eine ExpressRoute-Verbindung können einzelne, zwei oder alle drei Peerings pro ExpressRoute-Verbindung aktiviert sein.
 
Jede Verbindung verfügt über eine feste Bandbreite (50 MBit/s, 100 MBit/s, 200 MBit/s, 500 MBit/s, 1 GBit/s, 10 GBit/s) und ist einem Konnektivitätsanbieter und einem Peeringstandort zugewiesen. Die ausgewählte Bandbreite wird von allen Peerings gemeinsam für die Verbindung genutzt.

### Kontingente, Grenzwerte und Einschränkungen

Standardkontingente und -grenzwerte gelten für alle ExpressRoute-Verbindungen. Aktuelle Informationen zu Kontingenten finden Sie auf der Seite [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und -Dienste](../azure-subscription-service-limits.md).

## ExpressRoute-Routingdomänen

Einer ExpressRoute-Verbindung sind mehrere Routingdomänen zugeordnet: öffentliche Azure-Routingdomäne, private Azure-Routingdomäne und Microsoft-Routingdomänen. Alle Routingdomänen sind für hohe Verfügbarkeit auf einem Routerpaar identisch konfiguriert (mit einer Aktiv/Aktiv-Konfiguration oder einer Nutzlastverteilungskonfiguration). Azure-Dienste sind zur Darstellung der IP-Adressierungsschemas als *Azure – Öffentlich* und *Azure – Privat* kategorisiert.


![](./media/expressroute-circuit-peerings/expressroute-peerings.png)


### Privates Peering

Azure-Computedienste, sprich virtuelle Computer (IaaS) und Clouddienste (PaaS), die in einem virtuellen Netzwerk bereitgestellt werden, können über die private Peeringdomäne verbunden werden. Die private Peeringdomäne gilt als vertrauenswürdige Erweiterung Ihres Kernnetzwerks für Microsoft Azure. Sie können eine bidirektionale Verbindung zwischen Ihrem Kernnetzwerk und den virtuellen Azure-Netzwerken (VNets) einrichten. Dadurch können Sie direkt über ihre privaten IP-Adressen eine Verbindung zwischen virtuellen Computern und Clouddiensten herstellen.

Sie können mehr als ein virtuelles Netzwerk mit der privaten Peeringdomäne verbinden. Informationen zu Grenzwerten und Einschränkungen finden Sie auf der [FAQ-Seite](expressroute-faqs.md). Aktuelle Informationen zu Grenzwerten finden Sie auf der Seite [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und -Dienste](../azure-subscription-service-limits.md). Ausführliche Informationen zur Routingkonfiguration finden Sie unter [Routing](expressroute-routing.md).

### Öffentliches Peering

Dienste, wie Azure Storage, SQL-Datenbanken und Websites, werden über öffentliche IP-Adressen angeboten. Über die öffentliche Peeringrouting-Domäne können Sie eine private Verbindung mit unter öffentlichen IP-Adressen gehosteten Diensten herstellen (darunter VIPs Ihrer Clouddienste). Sie können die öffentliche Peeringdomäne mit der DMZ verbinden und aus Ihrem WAN heraus eine Verbindung mit allen Azure-Diensten unter ihren öffentlichen IP-Adressen herstellen, ohne eine Verbindung über das Internet herstellen zu müssen.

Die Verbindung wird immer von Ihrem WAN aus mit den Microsoft Azure-Diensten initiiert. Microsoft Azure-Dienste können über diese Routingdomäne keine Verbindungen in Ihr Netzwerk initiieren. Sobald öffentliches Peering aktiviert ist, werden Sie eine Verbindung mit allen Azure-Diensten herstellen können. Es ist Ihnen nicht gestattet, selektiv Dienste auszuwählen, zu denen wir Routen ankündigen. Auf der Seite [IP-Bereiche des Microsoft Azure-Rechenzentrums](http://www.microsoft.com/download/details.aspx?id=41653) finden Sie eine Liste der Ihnen über dieses Peering angekündigten Präfixe. Die Seite wird jede Woche aktualisiert.

Innerhalb des Netzwerks können Sie benutzerdefinierte Routingfilter definieren, um nur die benötigten Routen zu nutzen. Ausführliche Informationen zur Routingkonfiguration finden Sie unter [Routing](expressroute-routing.md). Innerhalb des Netzwerks können Sie benutzerdefinierte Routingfilter definieren, um nur die benötigten Routen zu nutzen.

Auf der [FAQ-Seite](expressroute-faqs.md) finden Sie weitere Informationen zu Diensten, die von der öffentlichen Peeringrouting-Domäne unterstützt werden.
 
### Microsoft-Peering

Verbindungen mit allen anderen Onlinediensten von Microsoft (z. B. Office 365-Dienste) erfolgen über das Microsoft-Peering. Über die Peeringrouting-Domäne von Microsoft ermöglichen wir eine bidirektionale Konnektivität zwischen Ihrem WAN und den Microsoft-Clouddiensten. Sie dürfen nur über öffentliche IP-Adressen, die Ihnen oder Ihrem Konnektivitätsanbieter gehören, eine Verbindung mit den Microsoft-Clouddiensten herstellen und müssen alle definierten Regeln einhalten. Weitere Informationen finden Sie auf der Seite [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

Auf der [FAQ-Seite](expressroute-faqs.md) finden Sie weitere Informationen zu unterstützten Diensten, Kosten und Konfigurationsdetails. Auf der Seite [ExpressRoute-Standorte](expressroute-locations.md) finden Sie Informationen zur Liste der Konnektivitätsanbieter, die Microsoft-Peeringsupport anbieten.

## Vergleich von Routingdomänen

In der folgenden Tabelle werden die drei Routingdomänen verglichen.

|**Privates Peering**|**Öffentliches Peering**|**Microsoft-Peering**|
|---|---|---|---|
|**Max. Anzahl von unterstützten Präfixen pro Peering**|Standardmäßig 4.000, bei ExpressRoute Premium 10.000|200|200|
|**Unterstützte IP-Adressbereiche**|Jede gültige IPv4-Adresse innerhalb des WANs.|Öffentliche IPv4-Adressen in Ihrem Besitz oder im Besitz des Konnektivitätsanbieters.|Öffentliche IPv4-Adressen in Ihrem Besitz oder im Besitz des Konnektivitätsanbieters.|
|**Anforderungen für AS-Nummern**|Private und öffentliche AS-Nummern. Kunde muss öffentliche AS-Nummer besitzen. | Private und öffentliche AS-Nummern. Kunde muss öffentliche AS-Nummer besitzen.| Nur öffentliche AS-Nummern. AS-Nummer muss anhand von Routingregistrierungen überprüft werden, um den Besitz zu prüfen.|
|**IP-Adressen der Routingschnittstelle**|RFC1918 und öffentliche IP-Adressen|In Routingregistrierungen für den Kunden registrierte öffentliche IP-Adressen.| In Routingregistrierungen für den Kunden registrierte öffentliche IP-Adressen.|
|**MD5-Hash-Unterstützung**| Ja|Ja|Ja|

Sie können mehrere Routingdomänen als Teil der ExpressRoute-Verbindung aktivieren. Sie können alle Routingdomänen durch das gleiche VPN leiten, wenn sie diese zu einer einzelnen Routingdomäne zusammenführen möchten. Sie können sie auch getrennt halten, ähnlich wie im Diagramm. Die empfohlene Konfiguration sieht folgendermaßen aus: Das private Peering ist direkt mit dem Kernnetzwerk verbunden, und die öffentlichen Peeringlinks und die Microsoft-Peeringlinks sind mit Ihrer DMZ verbunden.
 
Wenn Sie sich für alle drei Peeringsitzungen entscheiden, müssen Sie über drei BGP-Sitzungspaare (ein Paar für jeden Peeringtyp) verfügen. Die BGP-Sitzungspaare bieten einen hoch verfügbaren Link. Wenn Sie die Verbindung über Layer 2-Konnektivitätsanbieter herstellen, sind Sie für das Konfigurieren und Verwalten des Routings verantwortlich. Weitere Informationen erhalten Sie, wenn Sie sich [Workflows](expressroute-workflows.md) zum Einrichten von ExpressRoute anschauen.

## Nächste Schritte

- Einen Dienstanbieter finden. Informationen finden Sie unter [ExpressRoute-Dienstanbieter und -Standorte](expressroute-locations.md).
- Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind. Informationen finden Sie unter [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).
- Konfigurieren Sie Ihre ExpressRoute-Verbindung.
	- [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md)
	- [Konfigurieren des Routings (Verbindungspeerings)](expressroute-howto-routing-classic.md)
	- [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0204_2016-->