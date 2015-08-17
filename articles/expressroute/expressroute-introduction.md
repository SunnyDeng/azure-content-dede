<properties 
   pageTitle="Einführung in ExpressRoute | Microsoft Azure"
   description="Diese Seite enthält eine Übersicht über den ExpressRoute-Dienst, einschließlich der Funktionsweise einer ExpressRoute-Verbindung, der mit Exchange-Anbietern und Anbietern von Netzwerkdiensten arbeitet, sowie mit öffentlichem, privatem und Microsoft-ExpressRoute-Peering."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/15/2015"
   ms.author="cherylmc"/>


# ExpressRoute - Technische Übersicht

Microsoft Azure ExpressRoute ermöglicht es Ihnen, private Verbindungen zwischen Microsoft-Datencentern und der Infrastruktur bei Ihnen vor Ort oder in einer Kollokationsumgebung zu erstellen. Mit ExpressRoute können Sie Verbindungen zu Microsoft Cloud-Diensten, wie z. B. Microsoft Azure und Office 365, an einer ExpressRoute Partnerkollokationseinrichtung einrichten. Alternativ können Sie direkt von Ihrem vorhandenen WAN-Netzwerk aus eine Verbindung aufbauen, indem Sie z. B. ein MPLS-VPN nutzen, das von einem Netzwerkdienstanbieter bereitgestellt wird.
 
ExpressRoute-Verbindungen bieten eine größere Sicherheit, höhere Zuverlässigkeit, schnellere Geschwindigkeit und weniger Latenz als herkömmliche Verbindungen über das Internet. In einigen Fällen können durch die Verwendung von ExpressRoute-Verbindungen zum Übertragen von Daten zwischen einem lokalen Netzwerk und Azure auch drastische Kosteneinsparungen erzielt werden. Wenn Sie aus Ihrem lokalen Netzwerk heraus bereits eine standortübergreifende Verbindung mit Azure hergestellt haben, können Sie eine Migration auf eine ExpressRoute-Verbindung durchführen, während das virtuelle Netzwerk intakt bleibt.

Weitere Informationen finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

## Funktionsweise einer ExpressRoute-Verbindung

Für eine Verbindung Ihres WAN mit Microsoft-Clouddiensten müssen Sie eine dedizierte Verbindung anfordern und sie von einem Konnektivitätsanbieter aktivieren lassen. Es gibt zwei Anbietertypen: Direct Layer 3 über einen Exchange-Anbieter (EXP) oder Layer 3 über einen Netzwerkdienstanbieter (NSP). Sie können einen der beiden oder beide Konnektivitätstypen zwischen Ihrem WAN und der Microsoft-Cloud aktivieren.

## Exchange-Anbieter und Netzwerkdienstanbieter
ExpressRoute-Anbieter werden als Netzwerkdienstanbieter (NSPs) und Exchange-Anbieter (EXPs) klassifiziert.

![](./media/expressroute-introduction/expressroute-nsp-exp.png)


| |**Exchange-Anbieter**|**Netzwerkdienstanbieter**|
|---|---|---|
|**Typisches Konnektivitätsmodell**| Point-to-Point-Ethernetlinks oder Konnektivität mit einem Cloud-Exchange | Any-to-Any-Konnektivität über das VPN eines Telekommunikationsanbieters |
|**Unterstützte Bandbreiten**|200 Mbit/s, 500 Mbit/s, 1 Gbit/s und 10 Gbit/s|10 Mbit/s, 50 Mbit/s, 100 Mbit/s, 500 Mbit/s, 1 Gbit/s|
|**Konnektivitätsanbieter**|[Exchange-Anbieter](expressroute-locations.md)|[Netzwerkdienstanbieter](expressroute-locations.md)|
|**Routing**|BGP-Sitzungen direkt mit Edge-Routern des Kunden| BGP-Sitzungen mit Telekommunikationsanbieter|
|**Preise**|[EXP-Preise](http://azure.microsoft.com/pricing/details/expressroute/)|[NSP-Preise](http://azure.microsoft.com/pricing/details/expressroute/)|

### Exchange-Anbieter (EXPs)
Durch die Kooperation mit Cloud-Exchange-Dienstanbietern wie Equinix und der TeleCity Group sowie mit Point-to-Point-Konnektivitätsdienstanbietern wie Cole und Level 3 können wir Verbindungen zwischen Azure und dem Standort des Kunden herstellen. Dabei bieten wir Verbindungsbandbreiten von 200 Mbit/s, 10 Gbit/s (200 Mbit/s, 500 Mbit/s, 1 Gbit/s und 10 Gbit/s).

Wenn Sie eine Direct Layer 3-Verbindung über einen Exchange-Anbieter wünschen, gibt es dafür drei verschiedene Möglichkeiten:

- Eine Kollokation mit den Cloud-Exchanges, z. B. Cloud Exchange von Equinix oder Cloud IX von TeleCity, an den Standorten, an denen wir unsere Dienste anbieten. In solchen Fällen fordern Sie eine redundante Konnektivität mit dem Cloud-Exchange an. 
- Sie können mit Anbietern wie Ebene 3 arbeiten, um zwischen Ihren Datencentern und Microsoft eine Ethernetverbindung einzurichten. 
- Sie können mit Ihrem lokalen Konnektivitätsanbieter arbeiten, um eine redundante Konnektivität mit dem nächsten Exchange-Anbieter zu erwerben und eine Verbindung mit dem Cloud-Exchange herzustellen.

Um die Anforderungen unserer SLA zu erfüllen, brauchen Sie redundante Konnektivität. Wir unterstützen keine direkte Konnektivität zum Microsoft-Edge. Dedizierte Verbindungen werden immer über einen Ethernetanbieter oder den lokalen Cloud-Exchange aktiviert. Auch wenn dadurch eine Layer 2-Konnektivität zwischen Microsoft und Ihrem Netzwerk eingerichtet wird, unterstützen wir nicht die Erweiterung der Layer 2-Domäne. Für eine Layer 3-Konnektivität müssen Sie redundante Routingsitzungen zwischen Ihren Edge-Routern und den Microsoft-Edge-Routern einrichten.

Weitere Informationen zur Konfiguration sowie praktische Beispiele finden Sie unter der folgenden schrittweisen Anleitung: [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md).


### Netzwerkdienstanbieter (NSPs)

Dank der Kooperation mit Telekommunikationsunternehmen wie AT&T und British Telecom können wir Konnektivität zwischen Azure und Ihrem WAN anbieten. Dabei bieten wir Verbindungsbandbreiten von 10 Mbit/s bis 1 Gbit/s (10 Mbit/s, 50 Mbit/s, 100 Mbit/s, 500 Mbit/s und 1 Gbit/s).

Wenn Sie einen der VPN-Dienste von einem unserer Netzwerkdienstanbieter-Partner verwenden, können sie die Netzwerke für Azure erweitern, ohne neue Hardware bereitstellen zu müssen oder wesentliche Änderungen an der Konfiguration der vorhandenen Netzwerke vornehmen zu müssen.

Weitere Informationen zur Konfiguration sowie praktische Beispiele finden Sie unter der folgenden schrittweisen Anleitung: [Konfigurieren einer ExpressRoute-Verbindung über einen Netzwerkdienstanbieter](expressroute-configuring-nsps.md).

## ExpressRoute-Peerings
Die folgende Abbildung bietet eine logische Darstellung der Konnektivität zwischen Ihrem WAN und Microsoft. Für eine Verbindung Ihres WAN mit Microsoft über einen Konnektivitätsanbieter (NSP/EXP) müssen Sie eine *dedizierte Verbindung* anfordern. Eine dedizierte Verbindung ist eine logische Verbindung zwischen Ihrem WAN und Microsoft über den Konnektivitätsanbieter. Sie können viele dedizierte Verbindungen anfordern, von denen jede sich in der gleichen oder einer anderen Region befinden kann und die über verschiedene Dienstanbieter mit Ihrem WAN verbunden werden können.

![](./media/expressroute-introduction/expressroute-basic.png)

Einer dedizierten Verbindung werden mehrere Routingdomänen zugeordnet: öffentliche, private und Microsoft. Alle Routingdomänen sind für hohe Verfügbarkeit auf einem Routerpaar identisch konfiguriert (mit einer Aktiv-aktiv-Konfiguration oder einer Nutzlastverteilungskonfiguration).

![](./media/expressroute-introduction/expressroute-peerings.png)


### Privates Peering
Azure-Serverdienste, sprich virtuelle Computer (IaaS) und Clouddienste (PaaS), die in einem virtuellen Netzwerk bereitgestellt werden, können über die private Peeringdomäne verbunden werden. Die private Peeringdomäne gilt als vertrauenswürdige Erweiterung Ihres Kernnetzwerks für Microsoft Azure. Sie können eine bidirektionale Verbindung zwischen Ihrem Kernnetzwerk und den virtuellen Azure-Netzwerken (VNets) einrichten. Dadurch können Sie eine Verbindung zwischen virtuellen Computern und Clouddiensten direkt über ihre privaten IP-Adressen herstellen.

Sie können mehr als ein virtuelles Netzwerk mit der privaten Peeringdomäne verbinden. Informationen zu Grenzwerten und Einschränkungen finden Sie auf der [FAQ-Seite](expressroute-faqs.md).
  

### Öffentliches Peering
Dienste, wie Azure Storage, SQL-Datenbanken und Websites, werden über öffentliche IP-Adressen angeboten. Über die öffentliche Peeringrouting-Domäne können Sie eine private Verbindung mit unter öffentlichen IP-Adressen gehosteten Diensten herstellen (darunter VIPs Ihrer Clouddienste). Sie können die öffentliche Peeringdomäne mit dem Extranet verbinden und aus Ihrem WAN heraus eine Verbindung mit allen Azure-Diensten unter ihren öffentlichen IP-Adressen herstellen, ohne eine Verbindung über das Internet herstellen zu müssen. Die Verbindung wird immer von Ihrem WAN aus mit den Microsoft Azure-Diensten initiiert. Microsoft Azure-Dienste können über diese Routingdomäne keine Verbindungen in Ihr Netzwerk initiieren. Sobald öffentliches Peering aktiviert ist, werden Sie eine Verbindung mit allen Azure-Diensten herstellen können. Es ist Ihnen nicht gestattet, selektiv Dienste auszuwählen, zu denen wir Routen ankündigen. Auf der Seite [Microsoft Azure Datacenter IP-Bereiche](http://www.microsoft.com/download/details.aspx?id=41653) finden Sie eine Liste der Ihnen über dieses Peering angekündigten Präfixe. Innerhalb des Netzwerks können Sie benutzerdefinierte Routingfilter definieren, um nur die benötigten Routen zu nutzen.

Auf der [FAQ-Seite](expressroute-faqs.md) finden Sie weitere Informationen zu Diensten, die von der öffentlichen Peeringrouting-Domäne unterstützt werden.
 
### Microsoft-Peering
Verbindungen mit allen anderen Onlinediensten von Microsoft (z. B. Office 365-Dienste) erfolgen über das Microsoft-Peering. Über die Peeringrouting-Domäne von Microsoft ermöglichen wir eine bidirektionale Konnektivität zwischen Ihrem WAN und den Microsoft-Clouddiensten. Sie dürfen nur über öffentliche IP-Adressen, die Ihnen oder Ihrem Konnektivitätsanbieter gehören, eine Verbindung mit den Microsoft-Clouddiensten herstellen und müssen alle von uns definierten Regeln einhalten. Auf der Seite [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md) finden Sie weitere Informationen.

Auf der [FAQ-Seite](expressroute-faqs.md) finden Sie weitere Informationen zu unterstützten Diensten, Kosten und Konfigurationsdetails. Auf der Seite [ExpressRoute-Standorte](expressroute-locations.md) finden Sie Informationen zur Liste der Konnektivitätsanbieter, die Microsoft-Peeringsupport anbieten.


In der folgenden Tabelle werden die drei Routingdomänen verglichen.

||**Privates Peering**|**Öffentliches Peering**|**Microsoft-Peering**| |---|---|---|---| |**Max. unterstützte Anzahl Präfixe pro Peering**|Standardmäßig 4000, bei ExpressRoute Premium 10.000|Standardmäßig 4000, bei ExpressRoute Premium 10.000|200| |**Unterstützte IP-Adressbereiche**|Jede gültige IPv4-Adresse innerhalb des WAN|Öffentliche IPv4-Adressen in Ihrem Besitz oder dem des Konnektivitätsanbieters|Öffentliche IPv4-Adressen in Ihrem Besitz oder dem des Konnektivitätsanbieters| |**Anforderungen AS-Nummer**|Private und öffentliche AS-Nummern. Kunde muss öffentliche AS-Nummer besitzen. | Private und öffentliche AS-Nummern. Kunde muss öffentliche AS-Nummer besitzen. | Nur öffentliche AS-Nummern. AS-Nummer muss anhand von Routingregistrierungen überprüft werden, um den Besitz zu prüfen.| |**IP-Adressen der Routingschnittstelle**|RFC1918 und öffentliche IP-Adressen|In Routingregistrierungen beim Kunden/NSP registrierte öffentliche IP-Adressen.|In Routingregistrierungen beim Kunden/NSP registrierte öffentliche IP-Adressen.| |**MD5-Hash-Unterstützung**| Ja|Ja|Ja|

Sie können eine oder mehrere der Routingdomänen als Teil der dedizierten Verbindung aktivieren. Sie können alle Routingdomänen durch das gleiche VPN (im Fall eines NSP) leiten, wenn sie diese zu einer einzelnen Routingdomäne zusammenführen möchten. Sie können sie auch getrennt halten, ähnlich dem oben gezeigten Diagramm. Die empfohlene Konfiguration sieht folgendermaßen aus: Das private Peering ist direkt mit dem Kernnetzwerk verbunden und die öffentlichen und Microsoft-Peeringlinks sind mit Ihrem Extranet verbunden.
 
Wenn Sie sich für alle drei Peeringsitzungen entscheiden, müssen Sie über drei BGP-Sitzungspaare (ein Paar für jeden Peeringtyp) verfügen. Die BGP-Sitzungspaare bieten einen hoch verfügbaren Link. Wenn Sie die Verbindung über EXPs herstellen, sind Sie dafür verantwortlich, das Routing zu konfigurieren und zu verwalten (es sei denn, der EXP bietet Ihnen an, die Verwaltung zu übernehmen). Wenn Sie die Verbindung über NSPs herstellen, können Sie die Verwaltung des Routings dem NSP überlassen. Weitere Informationen erhalten Sie, wenn Sie sich Workflows zum Einrichten von ExpressRoute anschauen.


## Nächste Schritte

- Einen Dienstanbieter finden. Siehe [ExpressRoute-Dienstanbieter und -Standorte](expressroute-locations.md)
- Konfigurieren Sie Ihre ExpressRoute-Verbindung. Eine Anleitung finden Sie unter [ExpressRoute-Verbindung über einen Netzwerkdienstanbieter konfigurieren](expressroute-configuring-nsps.md) oder [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md). 

<!---HONumber=August15_HO6-->