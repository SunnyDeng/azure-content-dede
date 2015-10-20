<properties
   pageTitle="Routinganforderungen für ExpressRoute | Microsoft Azure"
   description="Diese Seite enthält ausführliche Anforderungen für das Konfigurieren und Verwalten des Routings für ExpressRoute-Verbindungen."
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
   ms.date="10/06/2015"
   ms.author="cherylmc"/>


# ExpressRoute-Routinganforderungen  

Zum Herstellen einer Verbindung mit Microsoft-Clouddiensten per ExpressRoute müssen Sie das Routing einrichten und verwalten. Einige Konnektivitätsanbieter bieten das Einrichten und Verwalten des Routings als verwalteten Dienst an. Fragen Sie bei Ihrem Konnektivitätsanbieter nach, ob dieser Dienst angeboten wird. Wenn dies nicht der Fall ist, müssen Sie die unten beschriebenen Anforderungen erfüllen.

Der Artikel [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md) enthält eine Beschreibung der Routingsitzungen, die zum Herstellen der Konnektivität eingerichtet werden müssen.

**Hinweis**: Microsoft unterstützt keine Routerredundanzprotokolle (z. B. HSRP, VRRP) für Konfigurationen mit hoher Verfügbarkeit. Wir nutzen ein redundantes Paar mit BGP-Sitzungen pro Peering, um die hohe Verfügbarkeit sicherzustellen.

## IP-Adressen für Peerings

Sie müssen einige Blöcke mit IP-Adressen reservieren, um das Routing zwischen Ihrem Netzwerk und den Microsoft Enterprise-Edgeroutern (MSEEs) zu konfigurieren. Dieser Abschnitt enthält eine Liste mit den Anforderungen, und es werden die Regeln zur Beschaffung und Verwendung dieser IP-Adressen beschrieben.

### IP-Adressen für privates Azure-Peering

Sie können entweder private IP-Adressen oder öffentliche IP-Adressen verwenden, um die Peerings zu konfigurieren. Der Adressbereich zum Konfigurieren der Routen darf sich nicht mit Adressbereichen überlappen, die in Azure zum Erstellen von virtuellen Netzwerken verwendet werden.

 - Sie müssen ein /29-Subnetz oder zwei /30-Subnetze für Routingschnittstellen reservieren.
 - Die für das Routing verwendeten Subnetze können entweder private IP-Adressen oder öffentliche IP-Adressen sein.
 - Die Subnetze dürfen nicht mit dem Bereich in Konflikt stehen, der vom Kunden für die Nutzung in der Microsoft Cloud reserviert ist.
 - Wenn ein /29-Subnetz verwendet wird, wird es in zwei /30-Subnetze unterteilt. 
	 - Das erste /30-Subnetz wird für die primäre Verknüpfung verwendet, und das zweite /30-Subnetz wird für die sekundäre Verknüpfung verwendet.
	 - Für jedes /30-Subnetz müssen Sie zuerst die IP-Adresse des /30-Subnetzes auf dem Router angeben. Microsoft verwendet die zweite IP-Adresse des /30-Subnetzes zum Einrichten einer BGP-Sitzung.
	 - Sie müssen beide BGP-Sitzungen einrichten, damit unsere [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/) gültig sind.  

#### Beispiel für privates Peering

Wenn Sie zum Einrichten des Peerings „a.b.c.d/29“ verwenden, wird eine Unterteilung in zwei /30-Subnetze vorgenommen. Im folgenden Beispiel sehen wir uns an, wie das a.b.c.d/29-Subnetz verwendet wird.

a.b.c.d/29 wird in a.b.c.d/30 und a.b.c.d+4/30 unterteilt und über die Bereitstellungs-APIs an Microsoft übergeben. Sie verwenden a.b.c.d+1 als VRF-IP für das primäre PE-Element, und Microsoft nutzt a.b.c.d+2 als VRF-IP für den primären MSEE. Sie verwenden a.b.c.d+5 als VRF-IP für das sekundäre PE-Element, und Microsoft nutzt a.b.c.d+6 als VRF-IP für den sekundären MSEE.

Betrachten wir den Fall, in dem Sie 192.168.100.128/29 zum Einrichten des privaten Peerings auswählen. 192.168.100.128/29 enthält die Adressen von 192.168.100.128 bis 192.168.100.135, für die Folgendes gilt:

- 192\.168.100.128/30 wird link1 zugewiesen, wobei der Anbieter 192.168.100.129 und Microsoft 192.168.100.130 verwendet.
- 192\.168.100.132/30 wird link2 zugewiesen, wobei der Anbieter 192.168.100.133 und Microsoft 192.168.100.134 verwendet.

### IP-Adressen für öffentliches Azure-Peering und Microsoft-Peering

Sie müssen eigene öffentliche IP-Adressen zum Einrichten der BGP-Sitzungen verwenden. Microsoft muss in der Lage sein, die Eigentümerschaft der IP-Adressen über Routing Internet Registries (RIR) und Internet Routing Registries (IRR) zu überprüfen.

- Sie müssen ein eindeutiges /29-Subnetz oder zwei /30-Subnetze verwenden, um das BGP-Peering für jedes Peering pro ExpressRoute-Verbindung einzurichten (falls Sie über mehr als ein Peering verfügen). 
- Wenn ein /29-Subnetz verwendet wird, wird es in zwei /30-Subnetze unterteilt. 
	- Das erste /30-Subnetz wird für die primäre Verknüpfung verwendet, und das zweite /30-Subnetz wird für die sekundäre Verknüpfung verwendet.
	- Für jedes /30-Subnetz müssen Sie zuerst die IP-Adresse des /30-Subnetzes auf dem Router verwenden. Microsoft verwendet die zweite IP-Adresse des /30-Subnetzes zum Einrichten einer BGP-Sitzung.
	- Sie müssen beide BGP-Sitzungen einrichten, damit unsere [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/) gültig sind.

Stellen Sie sicher, dass Ihre IP-Adresse und die AS-Nummer für Sie in einer der unten aufgeführten Registrierungen registriert sind.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPE NCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)
- [LEVEL3](http://rr.Level3.net/)


## Dynamischer Routenaustausch

Der Routingaustausch verläuft über das eBGP-Protokoll. EBGP-Sitzungen werden zwischen den MSEEs und Ihren Routern eingerichtet. Die Authentifizierung von BGP-Sitzungen ist nicht unbedingt erforderlich. Bei Bedarf kann ein MD5-Hash konfiguriert werden. Unter [Konfigurieren des Routings](expressroute-howto-routing-classic.md) und [Bereitstellungsworkflows für ExpressRoute-Verbindungen und Verbindungszustände](expressroute-workflows.md) finden Sie Informationen zum Konfigurieren von BGP-Sitzungen.

## Autonome Systemnummern

Microsoft verwendet AS 12076 für das öffentliche und private Azure-Peering und das Microsoft-Peering. Wir haben AS 65515 für die interne Verwendung reserviert. Sowohl AS-Nummern mit 16 als auch mit 32 Bit werden unterstützt. Sie können private AS-Nummern für das private Azure-Peering verwenden. Für das öffentliche Azure-Peering und das Microsoft-Peering müssen Sie öffentliche AS-Nummern verwenden, die für Sie registriert sind.

Es gibt keine Anforderungen in Bezug auf die Symmetrie der Datenübertragung. Die Weiterleitungs- und Rückgabepfade können unterschiedliche Routerpaare durchlaufen. Identische Routen müssen von beiden Seiten über mehrere Verbindungspaare angekündigt werden, die in Ihrem Besitz sind. Routenmetriken müssen nicht identisch sein.

## Routenaggregation- und Präfixgrenzen

Wir unterstützen bis zu 4.000 Präfixe, die uns per privatem Azure-Peering angekündigt werden. Dies kann auf bis zu 10.000 Präfixe erhöht werden, wenn das ExpressRoute Premium-Add-On aktiviert wird. Wir akzeptieren bis zu 200 Präfixe pro BGP-Sitzung für das öffentliche Azure-Peering und das Microsoft-Peering.

Die BGP-Sitzung wird verworfen, wenn die Anzahl der Präfixe den Grenzwert überschreitet. Wir akzeptieren Standardrouten nur über den Link für privates Peering. Der Anbieter muss die Standardroute und private IP-Adressen (RFC 1918) aus den Pfaden für das öffentliche Azure-Peering und das Microsoft-Peering herausfiltern.

## Transitrouting und regionsübergreifendes Routing

ExpressRoute kann nicht als Transitrouter konfiguriert werden. Transitrouting-Dienste erhalten Sie bei Ihrem Konnektivitätsanbieter.

## Ankündigen von Standardrouten

Standardrouten sind nur für Sitzungen mit privatem Azure-Peering zulässig. In diesem Fall wird der gesamte Datenverkehr aus den zugeordneten virtuellen Netzwerken an Ihr Netzwerk geleitet. Das Ankündigen von Standardrouten für das private Peering führt dazu, dass der Internetpfad aus Azure blockiert wird. Sie müssen den Edge-Bereich Ihres Unternehmens nutzen, um Datenverkehr für in Azure gehostete Dienste aus dem Internet und in das Internet zu leiten.

 Um die Konnektivität mit anderen Azure-Diensten und Infrastrukturdiensten zu ermöglichen, müssen Sie sicherstellen, dass eines der folgenden Elemente vorhanden ist:

 - Öffentliches Azure-Peering ist zum Leiten von Datenverkehr an öffentliche Endpunkte aktiviert.
 - Sie verwenden das benutzerdefinierte Routing, um Internetkonnektivität für alle Subnetze zuzulassen, die dies erfordern.

**Hinweis**: Das Ankündigen von Standardrouten führt dazu, dass die Aktivierung von Windows- und anderen VM-Lizenzen verloren geht. Führen Sie die Schritte [dieser Anleitung](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) aus, um dies zu umgehen.

## Unterstützung für BGP-Communitys

Dieser Abschnitt enthält eine Übersicht darüber, wie BGP-Communitys mit ExpressRoute verwendet werden. Microsoft kündigt Routen in den Pfaden für das öffentliche Peering und Microsoft-Peering an, und die Routen sind dabei mit den entsprechenden Communitywerten versehen. Die Gründe für diese Vorgehensweise und die Details zu den Communitywerten sind weiter unten beschrieben. Microsoft berücksichtigt aber keine Communitywerte, mit denen Routen gekennzeichnet sind, die gegenüber Microsoft angekündigt werden.

Wenn Sie an einem Peeringstandort in einer geopolitischen Region per ExpressRoute eine Verbindung mit Microsoft herstellen, haben Sie Zugriff auf alle Microsoft-Clouddienste innerhalb dieser geopolitischen Grenze.

Wenn Sie beispielsweise in Amsterdam per ExpressRoute mit Microsoft verbunden sind, haben Sie Zugriff auf alle Microsoft-Clouddienste, die in Nordeuropa und Westeuropa gehostet werden.

Die Seite [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md) enthält eine ausführliche Liste mit geopolitischen Regionen, dazugehörigen Azure-Regionen und entsprechenden ExpressRoute-Peeringstandorten.

Sie können mehr als eine ExpressRoute-Verbindung pro geopolitischer Region erwerben. Wenn Sie über mehrere Verbindungen verfügen, ergeben sich für Sie aufgrund der Georedundanz daraus erhebliche Vorteile in Bezug auf hohe Verfügbarkeit. Bei Verwendung mehrerer ExpressRoute-Verbindungen erhalten Sie die gleiche Gruppe von Präfixen, die von Microsoft über die Pfade für das öffentliche Peering und das Microsoft-Peering angekündigt werden. Dies bedeutet, dass Sie dann mehrere Pfade aus dem Netzwerk zu Microsoft nutzen können. Dies kann unter Umständen dazu führen, dass in Ihrem Netzwerk suboptimale Routingentscheidungen getroffen werden. Daraus können sich für verschiedene Dienste suboptimale Konnektivitätsleistungen ergeben.

Microsoft kennzeichnet Präfixe, die über das öffentliche Peering und Microsoft-Peering angekündigt werden, mit den passenden BGP-Communitywerten, um die Region anzugeben, in der die Präfixe gehostet werden. Sie können die Communitywerte nutzen, um die richtigen Routingentscheidungen zu treffen und Kunden optimales Routing zu bieten.

| **Geopolitische Region** | **Microsoft Azure-Region (dasselbe gilt für Office 365)** | **BGP-Communitywert** |
|---|---|---|
| **USA** |	USA (Ost) | 12076:3004 |
| | USA (Ost) 2 | 12076:3005 |
| | USA (West) | 12076:3006 |
| | USA (Mitte) | 12076:3009 |
| | USA (Mitte/Norden) | 12076:3007 |
| | USA (Mitte/Süden) | 12076:3008 |
| **Südamerika** | Brasilien Süd | 12076:3014 |
| **Europa** | Nordeuropa | 12076:3003 |
| | Westeuropa | 12076:3002 |
| **Asien-Pazifik** | Ostasien | 12076:3010 |
| | Südostasien | 12076:3011 |
| **Japan** | Japan Ost | 12076:3012 |
| | Japan (Westen) | 12076:3013 |
| **Australien** | Australien (Osten) | 12076:3015 |
| | Australien (Südost) | 12076:3016 |
| **Indien** | Indien, Süden | 12076:3019 |
| | Indien, Westen | 12076:3018 |
| | Indien, Mitte | 12076:3017 |

Alle Routen, die von Microsoft angekündigt werden, werden mit dem entsprechenden Communitywert gekennzeichnet.


Zusätzlich zu den obigen Kennzeichnungen versieht Microsoft Präfixe auch basierend auf dem zugehörigen Dienst mit einer Kennzeichnung. Dies gilt nur für das Microsoft-Peering. Die Tabelle unten enthält die Zuordnung des Diensts zum BGP-Communitywert.

| **Service** |	**BGP-Communitywert** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype for Business** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Andere Office 365-Dienste** | 12076:5100 |
| **Globale Präfixe/Anycast** | 12076:5200 |


### Ändern von Routingvoreinstellungen

Microsoft berücksichtigt keine BGP-Communitywerte, die von Ihnen festgelegt werden. Sie müssen ein BGP-Sitzungspaar pro Peering einrichten, um sicherzustellen, dass die Anforderungen für die [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/) erfüllt sind. Sie können Ihr Netzwerk aber so konfigurieren, dass ein bestimmter Link vorrangig behandelt wird, indem Sie standardmäßige Verfahren zur BGP-Routenänderung verwenden. Sie können unterschiedliche lokale BGP-Voreinstellungen auf jeden Link anwenden, damit ein Pfad von Ihrem Netzwerk zu Microsoft gegenüber dem anderen Pfad vorgezogen wird. Sie können den AS-PATH bei Routenankündigungen voranstellen, um den Datenverkehrsfluss von Microsoft in Ihr Netzwerk zu beeinflussen.

## Nächste Schritte

- Konfigurieren Sie Ihre ExpressRoute-Verbindung.

	- [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md)
	- [Konfigurieren des Routings](expressroute-howto-routing-classic.md)
	- [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Oct15_HO3-->