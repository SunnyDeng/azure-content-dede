<properties
   pageTitle="ExpressRoute – FAQ"
   description="Die FAQs zu ExpressRoute enthalten Informationen zu unterstützten Azure-Diensten, Kosten, Daten und Verbindungen, SLAs, Anbietern und Standorten, Bandbreite sowie zusätzliche technische Details."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/26/2015"
   ms.author="cherylmc"/>

# ExpressRoute – FAQ


## Was ist ExpressRoute?
ExpressRoute ist ein Azure-Dienst, der es Ihnen ermöglicht, private Verbindungen zwischen Microsoft-Datencentern und der Infrastruktur bei Ihnen vor Ort oder in einer Kollokationsumgebung zu erstellen. ExpressRoute-Verbindungen erfolgen nicht über das öffentliche Internet und bieten eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei weniger Latenz als herkömmliche Verbindungen über das Internet.

### Was sind die Vorteile von ExpressRoute und privaten Netzwerkverbindungen?
ExpressRoute-Verbindungen erfolgen nicht über das öffentliche Internet und bieten eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei konstanter Latenz als herkömmliche Verbindungen über das Internet. In einigen Fällen können durch die Verwendung von ExpressRoute-Verbindungen zum Übertragen von Daten zwischen lokalen Geräten und Azure auch drastische Kosteneinsparungen erzielt werden.

### Welche Microsoft-Clouddienste werden von ExpressRoute unterstützt?
ExpressRoute unterstützt die meisten der derzeit verfügbaren Microsoft Azure-Dienste. Demnächst sollen auch Office 365-Dienste über ExpressRoute unterstützt werden. Achten Sie in nächster Zeit auf Updates hinsichtlich der allgemeinen Verfügbarkeit.

### Wo ist der Dienst verfügbar?
Auf der folgenden Seite finden Sie Informationen bezüglich der Dienststandorte und Verfügbarkeit: [ExpressRoute-Partner und -Standorte](expressroute-locations.md).

### Wie kann ich ExpressRoute verwenden, um eine Verbindung mit Microsoft herzustellen, wenn ich keine Partnerschaft mit einem der ExpressRoute-Anbieterpartner habe?
Sie können einen regionalen Netzbetreiber auswählen und Ethernetverbindungen mit einem der unterstützten Exchange-Anbieterstandorte herstellen. Am EXP-Standort können Sie dann eine Peeringverbindung mit Microsoft herstellen. Überprüfen Sie den letzten Abschnitt von [ExpressRoute-Partner und -Standorte](expressroute-locations.md), um herauszufinden, ob Ihr Netzwerkanbieter an einem der Exchange-Standorte vorhanden ist. Sie können dann eine ExpressRoute-Verbindung über den Exchange-Anbieter anfordern, um eine Verbindung mit Azure herzustellen.

### Wie viel kostet ExpressRoute?
Preisinformationen finden Sie in der [Preisübersicht](http://azure.microsoft.com/pricing/details/expressroute/).

### Wenn ich für eine ExpressRoute-Verbindung mit einer bestimmten Bandbreite bezahle, muss die VPN-Verbindung, die ich von meinem Netzwerkdienstanbieter erworben habe, die gleiche Geschwindigkeit haben?
Nein. Sie können eine VPN-Verbindung beliebiger Geschwindigkeit bei Ihrem Dienstanbieter erwerben. Die Verbindung mit Azure wird jedoch auf die von Ihnen erworbene Bandbreite der ExpressRoute-Verbindung begrenzt.

### Wenn ich für eine ExpressRoute-Verbindung mit einer bestimmten Bandbreite bezahle, habe ich die Möglichkeit, bei Bedarf höhere Geschwindigkeiten zu nutzen?
Ja. ExpressRoute-Verbindungen sind so konfiguriert, dass sie Situationen unterstützen, in denen Sie das erworbene Bandbreitenlimit um das Doppelte überschreiten können, ohne dass zusätzliche Kosten anfallen. Fragen Sie bei Ihrem Dienstanbieter an, ob er diese Funktion unterstützt.

### Kann ich die gleiche private Netzwerkverbindung gleichzeitig mit Virtual Network und anderen Azure-Diensten verwenden?
Ja. Sobald eine ExpressRoute-Verbindung eingerichtet ist, können Sie gleichzeitig auf Dienste in einem virtuellen Netzwerk und in anderen Azure-Diensten zugreifen. Über einen privaten Peeringpfad können Sie eine Verbindung mit virtuellen Netzwerken herstellen, und über den öffentlichen Peeringpfad stellen Sie die Verbindung mit anderen Diensten her.

### Bietet ExpressRoute eine Service Level Agreement (SLA)?
Auf der Seite [Vereinbarungen zum Servicelevel (SLAs) für ExpressRoute](http://azure.microsoft.com/support/legal/sla/) finden Sie dazu weitere Informationen.

## Unterstützte Azure-Dienste
ExpressRoute unterstützt die meisten Azure-Dienste.

Verbindungen mit virtuellen Computern und Clouddiensten, die in virtuellen Netzwerken bereitgestellt werden, werden über den privaten Peeringpfad unterstützt.

Azure Websites werden über den öffentlichen Peeringpfad unterstützt.

Alle anderen Dienste sind über den öffentlichen Peeringpfad zugänglich. Ausnahmen sind wie folgt:

**Folgendes wird nicht unterstützt:**

- CDN
- Auslastungstests für Visual Studio Online
- Multi-Factor Authentication

## Daten und Verbindungen

### Gibt es Limits hinsichtlich der Datenmenge, die ich mithilfe von ExpressRoute übertragen kann?
Wir haben keine Limits hinsichtlich der übertragenen Datenmenge festgelegt. In der [Preisübersicht](http://azure.microsoft.com/pricing/details/expressroute/) finden Sie Informationen zu Bandbreitentarifen.

### Welche Verbindungsgeschwindigkeiten werden von ExpressRoute unterstützt?
Unterstützte Bandbreiten:

|**Anbieter**|**Bandbreite**|
|---|---|
|**Netzwerkanbieter**|10 Mbit/s, 50 Mbit/s, 100 Mbit/s, 500 Mbit/s, 1 Gbit/s|
|**Exchange-Anbieter**|200 Mbit/s, 500 Mbit/s, 1 Gbit/s, 10 Gbit/s|

### Welche Dienstanbieter sind verfügbar?
Eine Liste der Dienstanbieter und Standorte finden Sie unter [ExpressRoute-Partner und -Standorte](expressroute-locations.md).

## Technische Details

### Was sind die technischen Voraussetzungen für die Verbindung meines lokalen Standorts mit Azure?
Die Voraussetzungen finden Sie auf der Seite [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

### Sind Verbindungen mit ExpressRoute redundant?
Ja. Jede ExpressRoute-Verbindung verfügt über ein redundantes Paar von Querverbindungen mit hoher Verfügbarkeit.

### Geht die Konnektivität verloren, wenn einer meiner ExpressRoute-Links ausfällt?
Wenn eine der Querverbindungen ausfällt, bleibt die Konnektivität bestehen. Es steht eine redundante Verbindung zur Verfügung, um die Auslastung des Netzwerks zu unterstützen. Sie können zudem mehrere Verbindungen an einem anderen Peeringstandort erstellen, um die Ausfallsicherheit bei einem Fehler sicherzustellen.

### Muss ich beide Links konfigurieren, um den Dienst nutzen zu können?
Wenn Sie eine Verbindung über einen Netzwerkdienstanbieter herstellen, konfiguriert der Netzwerkdienstanbieter redundante Links für Sie. Wenn Sie eine Verbindung über einen EXP herstellen, müssen Sie beide Links konfigurieren. Unsere SLA wird ungültig, wenn die Verbindungskonfiguration nicht auf Redundanz ausgelegt ist.

### Kann ich eines meiner VLANs mithilfe von ExpressRoute auf Azure erweitern?
Nein. Wir unterstützen keine Layer 2-Konnektivitätserweiterungen in Azure.

### Kann mein Abonnement mehr als eine ExpressRoute-Verbindung umfassen?
Ja. Ihr Abonnement kann mehr als eine ExpressRoute-Verbindung enthalten. Das Limit für die Anzahl dedizierter Verbindungen ist standardmäßig auf 10 festgelegt. Wenden Sie sich an Microsoft Support, um das Limit bei Bedarf zu erhöhen.

### Kann ich ExpressRoute-Verbindungen von verschiedenen Dienstanbietern haben?
Ja. Sie können ExpressRoute-Verbindungen von verschiedenen Dienstanbietern verwenden. Jede ExpressRoute-Verbindung wird nur einem Dienstanbieter zugeordnet.

### Wie kann ich meine Virtual Networks mit einer ExpressRoute-Verbindung verbinden?
Die grundlegenden Schritte werden im Folgenden beschrieben.

- Sie müssen eine ExpressRoute-Verbindung herstellen, die vom Dienstanbieter aktiviert werden muss.
- Bei Verwendung eines Exchange-Anbieters müssen Sie das BGP für privates Peering konfigurieren.
- Sie müssen das virtuelle Netzwerk mit der ExpressRoute-Verbindung verknüpfen.

Die folgenden Lernprogramme helfen Ihnen bei den folgenden Aufgaben:

- [Konfigurieren einer ExpressRoute-Verbindung über einen Netzwerkdienstanbieter](expressroute-configuring-nsps.md)
- [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md)
- [Konfigurieren eines virtuellen Netzwerks und Gateways für ExpressRoute](expressroute-configuring-vnet-gateway.md)

### Gibt es Konnektivitätsgrenzen für meine ExpressRoute-Verbindung?
Ja. Die Seite [ExpressRoute-Partner und -Standorte](expressroute-locations.md) bietet eine Übersicht über die Konnektivitätsgrenzen für eine ExpressRoute-Verbindung. Die Konnektivität für eine ExpressRoute-Verbindung ist auf eine einzelne geopolitische Region beschränkt. Die Konnektivität kann auf verschiedene geopolitische Regionen erweitert werden, wenn Sie das ExpressRoute Premium-Feature aktivieren.

### Kann ich mehr als ein virtuelles Netzwerk mit einer ExpressRoute-Verbindung verknüpfen?
Ja. Sie können bis zu 10 virtuelle Netzwerke mit einer ExpressRoute-Verbindung verknüpfen.

### Ich habe mehrere Azure-Abonnements, die Virtual Networks enthalten. Kann ich Virtual Networks, die sich in unterschiedlichen Abonnements befinden, mit einer ExpressRoute-Verbindung verbinden?
Ja. Sie können bis zu 10 andere Azure-Abonnements autorisieren, eine einzige ExpressRoute-Verbindung zu verwenden. Dieses Limit kann durch das Aktivieren des ExpressRoute Premium-Features erhöht werden.

Weitere Informationen finden Sie unter [Gemeinsames Verwenden einer ExpressRoute-Verbindung für mehrere Abonnements](https://msdn.microsoft.com/library/azure/dn835110.aspx).

### Sind virtuelle Netzwerke, die mit der gleichen Verbindung verbunden sind, voneinander isoliert?
Nein. Alle virtuellen Netzwerke, die mit der gleichen ExpressRoute-Verbindung verknüpft sind, sind Teil der gleichen Routingdomäne und aus Routingsicht nicht voneinander isoliert. Wenn Sie eine Isolierung der Routen benötigen, müssen Sie zuerst eine separate ExpressRoute-Verbindung erstellen.

### Kann mehr als ein virtuelles Netzwerk mit mehr als einer ExpressRoute-Verbindung verbunden sein?
Ja. Sie können ein einzelnes virtuelles Netzwerk mit bis zu 4 ExpressRoute-Verbindungen verknüpfen. Alle ExpressRoute-Verbindungen müssen sich auf demselben Kontinent befinden. Sie können bei verschiedenen Dienstanbietern angefordert werden und sich an verschiedenen Standorten befinden.

### Kann ich von meinen virtuellen Netzwerken aus, die mit ExpressRoute-Verbindungen verbunden sind, ins Internet gehen?
Ja. Wenn Sie über die BGP-Sitzung keine Standardrouten (0.0.0.0/0) oder Internet-Routepräfixe angekündigt haben, können Sie von einem virtuellen Netzwerk aus, das mit einer ExpressRoute-Verbindung verknüpft ist, eine Verbindung zum Internet herstellen.

### Kann ich die Verbindung zum Internet für virtuelle Netzwerke blockieren, die mit ExpressRoute-Verbindungen verbunden sind?
Ja. Sie können Standardrouten (0.0.0.0/0) ankündigen, um die Internetkonnektivität von virtuellen Computern in einem virtuellen Netzwerk zu blockieren und den gesamten Datenverkehr über die ExpressRoute-Verbindung zu leiten. Beachten Sie, dass wir den Datenverkehr an Dienste, die über öffentliches Peering (z. B. Azure Storage und SQL-Datenbank) angeboten werden, wieder an Ihren Standort zurückleiten, wenn Sie keine Standardrouten ankündigen. Sie müssen Ihre Router so konfigurieren, dass der Datenverkehr über den öffentlichen Peeringpfad oder über das Internet an Azure zurückgegeben wird.

### Können virtuelle Netzwerke mit der gleichen ExpressRoute-Verbindung verknüpft sein und miteinander kommunizieren?
Ja. Virtuelle Maschinen in virtuellen Netzwerken, die mit der gleichen ExpressRoute-Verbindung verbunden sind, können miteinander kommunizieren.

### Kann ich für Virtual Networks Standort-zu-Standort-Konnektivität zusammen mit ExpressRoute verwenden?
Ja. ExpressRoute kann zusammen mit Standort-zu-Standort-VPNs verwendet werden.

### Kann ich ein virtuelles Netzwerk mit einer Standort-zu-Standort-/Punkt-zu-Standort-Konfiguration so konfigurieren, dass es ExpressRoute verwendet?
Ja. Sie müssen in Ihrem virtuellen Netzwerk ein ExpressRoute-Gateway erstellen. Bei diesem Vorgang kommt es zu einer kurzen Dienstunterbrechung.

### Was brauche ich, um über ExpressRoute eine Verbindung mit Azure Storage herzustellen?
Sie müssen eine ExpressRoute-Verbindung einrichten und Routen für öffentliches Peering konfigurieren.

### Gibt es Limits hinsichtlich der Anzahl von Routen, die ich ankündigen kann?
Ja. Wir akzeptieren bis zu 4.000 Routenpräfixe für privates Peering und öffentliches Peering. Sie können diesen Wert auf 10.000 Routen erhöhen, wenn Sie für ExpressRoute das Premium-Feature aktivieren.

### Gibt es Einschränkungen hinsichtlich der IP-Adressbereiche, die ich über die BGP-Sitzung ankündigen kann?
Über BGP angekündigte Präfixe müssen "/29" oder größer (/28 bis /8) sein.

Wir filtern private Präfixe (RFC1918) in der öffentlichen BGP-Peeringsitzung heraus.

### Was geschieht, wenn ich die BGP-Limits überschreite?
BGP-Sitzungen werden abgebrochen. Sie werden zurückgesetzt, sobald die Präfixanzahl unter das Limit fällt.

### Nachdem ich die Standardroute (0.0.0.0/0) für meine Virtual Networks angekündigt habe, kann ich Windows, das auf meinen Azure-VMs ausgeführt wird, nicht mehr aktivieren. Wie kann ich dieses Problem beheben?
Die folgenden Schritte helfen Azure dabei, die Aktivierungsanforderung zu erkennen:

1. Richten Sie für die ExpressRoute-Verbindung öffentliches Peering ein.
2. Führen Sie ein DNS-Lookup durch, und suchen Sie die IP-Adresse von **kms.core.windows.net**.
3. Führen Sie eines der beiden folgenden Elemente aus, damit der Schlüsselverwaltungsdienst erkennt, dass die Aktivierungsanforderung von Azure stammt und die Anforderung berücksichtigt.
	- Leiten Sie in Ihrem lokalen Netzwerk den Datenverkehr für die IP-Adresse (abgerufen in Schritt 2) über das öffentliche Peering zurück an Azure.
	- Fordern Sie Ihren Netzwerkdienstanbieter auf, den Datenverkehr über das öffentliche Peering wieder an Azure zurückzuleiten.

### Kann ich die Bandbreite einer ExpressRoute-Verbindung ändern?
Ja. Die Bandbreite einer ExpressRoute-Verbindung kann erhöht werden, ohne sie beenden zu müssen. Wenden Sie sich an Ihren Konnektivitätsanbieter, um sicherzustellen, dass er die Drosselungen in seinen Netzwerken aktualisiert, um die Erhöhung der Bandbreite zu unterstützen. Sie können die Bandbreite einer ExpressRoute-Verbindung jedoch nicht reduzieren. Wenn Sie die Bandbreite verringern müssen, wird die ExpressRoute-Verbindung beendet und anschließend neu erstellt.

### Wie kann ich die Bandbreite einer ExpressRoute-Verbindung ändern?
Sie können die Bandbreite der ExpressRoute-Verbindung mithilfe der API zum Aktualisieren der dedizierten Verbindung und des PowerShell-Cmdlets aktualisieren.

## ExpressRoute Premium

### Was ist ExpressRoute Premium?
ExpressRoute Premium ist eine Sammlung der unten aufgeführten Features.

 - Höheres Routingtabellen-Limit von 4000 Routen auf 10.000 Routen für öffentliches und privates Peering.
 - Größere Anzahl an VNets, die mit der ExpressRoute-Verbindung verbunden werden können. (Der Standardwert ist 10.) Weitere Details finden Sie in der Tabelle unten.
 - Globale Konnektivität über das Microsoft-Kernnetzwerk. Jetzt können Sie ein VNet in einer geopolitischen Region mit einer ExpressRoute-Verbindung in einer anderen Region verknüpfen. **Beispiel:** Sie können ein in Westeuropa erstelltes VNet mit einer ExpressRoute-Verbindung verknüpfen, die im Silicon Valley erstellt wurde.

### Wie viele VNets kann ich mit einer ExpressRoute-Verbindung verknüpfen, wenn ich ExpressRoute Premium aktiviert habe?
Die folgende Tabelle enthält die erhöhten Limits für die Anzahl der VNets, die Sie mit einer ExpressRoute-Verbindung verknüpfen können. Das Standardlimit ist 10.

**Limits für über NSPs erstellte Verbindungen**

| **Verbindungsgröße** | **Anzahl der VNet-Links für das Standardsetup** | **Anzahl der VNet-Links mit ExpressRoute Premium** |
|--------------|----------------------------------------|-----------------------------------------------|
| 10 MBit/s | 10 | Nicht unterstützt |
| 50 MBit/s | 10 | 20 |
| 100 MBit/s | 10 | 25 |
| 500 MBit/s | 10 | 40 |
| 1 Gbit/s | 10 | 50
|


**Limits für über EXPs erstellte Verbindungen**

| **Verbindungsgröße** | **Anzahl der VNet-Links für das Standardsetup** | **Anzahl der VNet-Links mit ExpressRoute Premium** |
|--------------|-----------------------------------|------------------------------------------------|
| 200 MBit/s | 10 | 25 |
| 500 MBit/s | 10 | 40 |
| 1 GBit/s | 10 | 50 |
| 10 GBit/s | 10 | 100 |



### Wie aktiviere ich ExpressRoute Premium?
Die Features von ExpressRoute Premium können aktiviert werden, sobald Sie das Feature selbst aktiviert haben. Durch Aktualisieren des Verbindungsstatus können sie beendet werden. Sie können ExpressRoute Premium zum Zeitpunkt der Verbindungserstellung aktivieren, oder Sie können die API zum Aktualisieren der dedizierten Verbindung bzw. das PowerShell-Cmdlet aufrufen, um ExpressRoute Premium zu aktivieren.

### Wie deaktiviere ich ExpressRoute Premium?
Sie können ExpressRoute Premium deaktivieren, indem Sie die API zum Aktualisieren der dedizierten Verbindung/das PowerShell-Cmdlet aufrufen. Stellen Sie sicher, dass Sie Ihre Konnektivitätsanforderungen skaliert haben, um die Standardlimits zu erfüllen, bevor Sie ExpressRoute Premium deaktivieren. Die Anforderung ExpressRoute Premium zu deaktivieren wird storniert, wenn Ihre Nutzung über dem Standardlimit liegt.

### Kann ich die Features der Premium-Features auswählen, die ich nutzen möchte?
Nein. Sie können keine einzelnen Features auswählen. Wenn Sie ExpressRoute Premium aktivieren, werden alle Features aktiviert.

### Wie viel kostet ExpressRoute Premium?
Angaben zu den Preisen finden Sie in der [Preisübersicht](http://azure.microsoft.com/pricing/details/expressroute/).

### Fallen für ExpressRoute Premium neben dem standardmäßigen ExpressRoute-Tarif zusätzliche Gebühren an?
Ja. Für ExpressRoute Premium werden neben den ExpressRoute-Verbindungsgebühren und den Gebühren, die vom Konnektivitätsanbieter erhoben werden, zusätzliche Gebühren berechnet.

### Funktioniert ExpressRoute Premium sowohl für das EXP- als auch das NSP-Modell?
Ja. ExpressRoute Premium wird für ExpressRoute-Verbindungen unterstützt, die über EXPs und NSPs erstellt wurden.


## ExpressRoute und Office 365

### Wie erstelle ich eine ExpressRoute-Verbindung mit den Office 365-Diensten?

1. Überprüfen Sie die Anforderungen auf der Seite mit den [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md), um sicherzustellen, dass Sie die Anforderungen erfüllen.
2. Überprüfen Sie die Liste der Dienstanbieter und Standorte unter [ExpressRoute-Partner und -Standorte](expressroute-locations.md), um sicherzustellen, dass Ihre Konnektivitätsanforderungen erfüllt werden.
3. Planen Sie Ihre Kapazitätsanforderungen mithilfe der Informationen unter [Netzwerkplanung und Leistungsoptimierung für Office 365](http://aka.ms/tune/).
4. Befolgen Sie die Schritte in den unten stehenden Workflows, um eine Verbindung einzurichten.

	- [Konfigurieren einer ExpressRoute-Verbindung über einen Netzwerkdienstanbieter](expressroute-configuring-nsps.md)
	- [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md)

### Unterstützen meine vorhandenen ExpressRoute-Verbindungen die Konnektivität mit Office 365-Diensten?
Ja. Ihre bestehende ExpressRoute-Verbindung kann so konfiguriert werden, dass sie eine Konnektivität mit Office 365-Diensten unterstützt. Stellen Sie sicher, dass Sie ausreichend Kapazität für die Verbindung mit Office 365-Diensten haben. Die Informationen unter [Netzwerkplanung und Leistungsoptimierung für Office 365](http://aka.ms/tune/) helfen Ihnen dabei, Ihre Konnektivitätsanforderungen zu planen.

Die folgenden Lernprogramme helfen Ihnen bei den folgenden Aufgaben:

- [Konfigurieren einer ExpressRoute-Verbindung über einen Netzwerkdienstanbieter](expressroute-configuring-nsps.md)
- [Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter](expressroute-configuring-exps.md)

### Welche Office 365-Dienste sind über eine ExpressRoute-Verbindung zugänglich?

**Die folgenden Office 365-Dienste werden unterstützt:**

- Exchange Online und Exchange Online Protection
- SharePoint Online
- Skype for Business Online
- Office Online
- Azure AD und Azure AD Sync
- Office 365 Video
- Power BI
- Delve
- Project Online

**Die folgenden Office 365-Dienste werden nicht unterstützt:**

- Yammer
- Office 365 ProPlus-Clientdownloads
- Lokale Identitätsanbieter-Anmeldung
- Office 365 (über 21 Vianet)-Dienst in China

Mit diesen Diensten können Sie eine Verbindung über das Internet herstellen.

### Wie viel kostet ExpressRoute für Office 365?
Für die Verbindung mit Office 365 über ExpressRoute fallen keine zusätzlichen Gebühren an. In der [Preisübersicht](http://azure.microsoft.com/pricing/details/expressroute/) finden Sie Details zu den Kosten für ExpressRoute.

### In welchen Regionen wird ExpressRoute für Office 365 unterstützt?
Weitere Informationen zur Liste der Partner und Standorte, die ExpressRoute unterstützen, finden Sie unter [ExpressRoute-Partner und -Standorte](expressroute-locations.md).

### Kann ich die Verbindung mit Office 365-Diensten über NSPs und EXPs herstellen?
Die Verbindung mit Office 365-Diensten kann sowohl über NSPs als auch EXPs hergestellt werden. Weitere Informationen zur Liste der Partner und Standorte finden Sie unter [ExpressRoute-Partner und -Standorte](expressroute-locations.md).

### Kann ich über das Internet auf Office 365 zugreifen, selbst wenn für meine Organisation ExpressRoute konfiguriert wurde?
Ja. Office 365-Dienstendpunkte sind über das Internet erreichbar, selbst wenn ExpressRoute für Ihr Netzwerk konfiguriert wurde. Wenn Sie sich an einem Ort befinden, an dem die Verbindung mit Office 365-Diensten über ExpressRoute konfiguriert wurde, wird die Verbindung über ExpressRoute hergestellt.
 

<!---HONumber=July15_HO2-->