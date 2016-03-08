<properties 
   pageTitle="Häufig gestellte Fragen zum VPN-Gateway von Virtual Network | Microsoft Azure"
   description="Häufig gestellte Fragen zum VPN-Gateway. Häufig gestellte Fragen zu standortübergreifenden Verbindungen, Verbindungen mit Hybridkonfiguration und VPN-Gateways von Microsoft Azure Virtual Network "
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/16/2015"
   ms.author="cherylmc" />

# Häufig gestellte Fragen zum VPN-Gateway

## Herstellen einer Verbindung mit virtuellen Netzwerken

### Kann ich Verbindungen zwischen virtuellen Netzwerken in verschiedenen Azure-Regionen herstellen?
Ja. Es gelten keine regionsbedingten Einschränkungen. Verbindungen können sowohl zwischen virtuellen Netzwerken in der gleichen Region als auch zwischen virtuellen Netzwerken in unterschiedlichen Azure-Regionen hergestellt werden.

### Kann ich Verbindungen zwischen virtuellen Netzwerken in unterschiedlichen Abonnements herstellen?
Ja.

### Kann ich eine Verbindung mit mehreren Standorten eines einzelnen virtuellen Netzwerks herstellen?

Verbindungen mit mehreren Standorten können mit Windows PowerShell und den REST-APIs von Azure hergestellt werden. Weitere Informationen finden Sie im Abschnitt [Mehrere Standorte und VNet-zu-VNet-Verbindungen](#multi-site-and-vnet-to-vnet-connectivity).
## Welche Optionen stehen mir bei standortübergreifenden Verbindungen zur Verfügung?

Die folgenden standortübergreifende Verbindungen werden unterstützt:

- [Standort zu Standort](vpn-gateway-site-to-site-create.md): VPN-Verbindung über IPsec (IKE v1 und IKE v2). Für diese Art von Verbindung wird ein VPN-Gerät oder RRAS benötigt.

- [Punkt zu Standort](vpn-gateway-point-to-site-create.md): VPN-Verbindung über SSTP (Secure Socket Tunneling-Protokoll). Für diese Verbindung wird kein VPN-Gerät benötigt.

- [VNet-zu-VNet](virtual-networks-configure-vnet-to-vnet-connection.md): Dieser Verbindungstyp entspricht der Standort-zu-Standort-Konfiguration. VNet zu VNet ist eine VPN-Verbindung über IPsec (IKE v1 und IKE v2). Für diese Verbindung wird kein VPN-Gerät benötigt.

- [Mehrere Standorte](vpn-gateway-multi-site.md): Hierbei handelt es sich um eine Variante der Standort-zu-Standort-Konfiguration, mit der Sie mehrere lokale Standorte mit einem virtuellen Netzwerk verbinden können.

- [ExpressRoute](../expressroute/expressroute-introduction.md): ExpressRoute ist eine Azure-Direktverbindung mit Ihrem WAN (nicht über das öffentliche Internet). Weitere Informationen finden Sie unter [ExpressRoute – Technische Übersicht](../expressroute/expressroute-introduction.md) sowie unter [ExpressRoute – FAQ](../expressroute/expressroute-faqs.md).

Weitere Informationen zu standortübergreifenden Verbindungen finden Sie unter [Informationen zu sicheren, standortübergreifenden Verbindungen](vpn-gateway-cross-premises-options.md).

### Was ist der Unterschied zwischen einer Standort-zu-Standort- und einer Punkt-zu-Standort-Verbindung?

**Standort zu Standort**: Bei diesem Verbindungstyp können Sie eine Verbindung zwischen lokalen Computern und virtuellen Computern oder Rolleninstanzen in Ihrem virtuellen Netzwerk herstellen (abhängig von der Routingkonfiguration). Diese Option eignet sich hervorragend für eine ständig verfügbare, standortübergreifende Verbindung sowie für Hybridkonfigurationen. Dieser Verbindungstyp basiert auf einer (hardware- oder softwarebasierten) IPsec-VPN-Appliance, die am Rand des Netzwerks bereitgestellt werden muss. Wenn Sie eine Verbindung dieses Typs erstellen möchten, benötigen Sie die erforderliche VPN-Hardware und eine extern ausgerichtete IPv4-Adresse.

**Punkt zu Standort**: Mit diesem Verbindungstyp können Sie ortsunabhängig eine Verbindung zwischen einem einzelnen Computer mit einer beliebigen Ressource in Ihrem virtuellen Netzwerk herstellen. Hierbei kommt der in Windows enthaltene VPN-Client zum Einsatz. Im Rahmen der Punkt-zu-Standort-Konfiguration installieren Sie ein Zertifikat und ein VPN-Clientkonfigurationspaket mit den Einstellungen, die es Ihrem Computer ermöglichen, eine Verbindung mit einem beliebigen virtuellen Computer oder einer beliebigen Rolleninstanz innerhalb des virtuellen Netzwerks herzustellen. Dieser Verbindungstyp eignet sich hervorragend, wenn Sie eine Verbindung mit einem virtuellen Netzwerk herstellen möchten, sich aber nicht vor Ort befinden. Er ist auch eine gute Wahl, wenn Ihnen keine VPN-Hardware oder keine extern ausgerichtete IPv4-Adresse zur Verfügung steht (beides Voraussetzungen für eine Standort-zu-Standort-Verbindung).

Hinweis: Sie können Ihr virtuelles Netzwerk für die parallele Verwendung von Standort-zu-Standort- und Punkt-zu-Standort-Verbindungen konfigurieren. Hierzu müssen Sie Ihre Standort-zu-Standort-Verbindung mit einem Gateway für dynamisches Routing erstellen.

Weitere Informationen finden Sie unter [Informationen zu sicheren, standortübergreifenden Verbindungen für virtuelle Netzwerke](vpn-gateway-cross-premises-options.md).

### Was ist ExpressRoute?

ExpressRoute ermöglicht es Ihnen, private Verbindungen zwischen Microsoft-Datencentern und einer Infrastruktur bei Ihnen vor Ort oder in einer Kollokationsumgebung zu erstellen. Mit ExpressRoute können Sie Verbindungen mit Microsoft-Clouddiensten wie Microsoft Azure und Office 365 an einem ExpressRoute-Partnerkollokationsstandort oder direkt von Ihrem vorhandenen WAN-Netzwerk (z. B. ein MPLS VPN eines Netzwerkdienstanbieters) aus herstellen.

ExpressRoute-Verbindungen sind sicherer, zuverlässiger und bieten eine höhere Bandbreite sowie eine geringere Latenz als herkömmliche Verbindungen über das Internet. In einigen Fällen können durch die Verwendung von ExpressRoute-Verbindungen zum Übertragen von Daten zwischen einem lokalen Netzwerk und Azure auch drastische Kosteneinsparungen erzielt werden. Wenn Sie aus Ihrem lokalen Netzwerk heraus bereits eine standortübergreifende Verbindung mit Azure hergestellt haben, können Sie eine Migration auf eine ExpressRoute-Verbindung durchführen, während das virtuelle Netzwerk intakt bleibt.

Weitere Informationen finden Sie unter [ExpressRoute – FAQ](../expressroute/expressroute-faqs.md).

## Standort-zu-Standort-Verbindungen und VPN-Geräte

### Was muss ich bei der Wahl eines VPN-Geräts berücksichtigen?

Wir haben in Zusammenarbeit mit Geräteherstellern eine Reihe von VPN-Geräten für standardmäßige Standort-zu-Standort-Verbindungen getestet. Eine Liste mit kompatiblen VPN-Geräten, entsprechenden Konfigurationsanweisungen oder -beispielen und Gerätespezifikationen finden Sie [hier](vpn-gateway-about-vpn-devices.md). Alle Geräte der als kompatibel angegebenen Gerätefamilien sollten mit Virtual Network verwendet werden können. Hilfreiche Informationen zur Konfiguration des VPN-Gerätes finden Sie im entsprechenden Konfigurationsbeispiel oder unter dem Link für die entsprechende Gerätefamilie.

### Was kann ich tun, wenn mein VPN-Gerät nicht in der Liste bekannter kompatibler Geräte enthalten ist?

Wenn Ihr Gerät nicht in der Liste bekannter kompatibler VPN-Geräte enthalten ist und Sie das Gerät für Ihre VPN-Verbindung verwenden möchten, vergewissern Sie sich [hier](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list), dass es für die unterstützten IPsec-/IKE-Konfigurationsoptionen und -parameter geeignet ist. Geräte, die die Mindestanforderungen erfüllen, sollten problemlos mit VPN-Gateways verwendet werden können. Zusätzliche Unterstützung und Konfigurationsanweisungen erhalten Sie vom Gerätehersteller.

### Warum fällt mein richtlinienbasierter VPN-Tunnel aus, wenn kein Datenverkehr stattfindet?

Dabei handelt es sich um einen Standardvorgang bei richtlinienbasierten (auch als „statisches Routing“ bezeichnet) VPN-Gateways. Wenn im Tunnel länger als 5 Minuten kein Datenverkehr stattfindet, wird der Tunnel geschlossen. Sobald jedoch der Datenverkehr wieder beginnt, egal, in welche Richtung, wird der Tunnel erneut hergestellt. Bei weiterleitungsbasierten (auch als „dynamisch“ bezeichnet) VPN-Gateways ist dies nicht der Fall.

### Kann ich VPN-Softwarelösungen verwenden, um eine Verbindung mit Azure herzustellen?

Für die standortübergreifende Standort-zu-Standort-Konfiguration werden Routing- und RAS-Server unter Windows Server 2012 unterstützt.

Auch andere VPN-Softwarelösungen können mit unserem Gateway verwendet werden, sofern sie über branchenübliche IPsec-Implementierungen verfügen. Konfigurations- und Supportinformationen erhalten Sie vom Anbieter der Software.

## Punkt-zu-Standort-Verbindungen

### Welche Betriebssysteme kann ich bei Punkt-zu-Standort-Verbindungen verwenden?

Folgende Betriebssysteme werden unterstützt:

- Windows 7 (nur 64-Bit-Version)

- Windows Server 2008 R2

- Windows 8 (nur 64-Bit-Version)

- Windows Server 2012

- Windows 10

### Kann ich für Punkt-zu-Standort-Verbindungen einen beliebigen VPN-Softwareclient mit SSTP-Unterstützung verwenden?

Nein. Nur die oben aufgeführten Windows-Betriebssystemversionen werden unterstützt.

### Wie viele VPN-Clientendpunkte kann meine Punkt-zu-Standort-Konfiguration umfassen?

Wir unterstützen bis zu 128 gleichzeitige VPN-Clientverbindungen mit einem virtuellen Netzwerk.

### Kann ich für Punkt-zu-Standort-Verbindungen meine eigene interne PKI-Stammzertifizierungsstelle verwenden?

Ja. Bisher konnten nur selbstsignierte Stammzertifikate verwendet werden. Sie können weiterhin 20 Stammzertifikate hochladen.

### Können Proxys und Firewalls mit der Punkt-zu-Standort-Funktion durchquert werden?

Ja. Wir verwenden das Secure Socket Tunneling-Protokoll (SSTP), um eine Tunnelverbindung durch Firewalls zu ermöglichen. Dieser Tunnel wird als HTTPS-Verbindung angezeigt.

### Wird die VPN-Verbindung eines für „Punkt zu Standort“ konfigurierten Clientcomputers nach einem Neustart automatisch wiederhergestellt?

Standardmäßig wird die VPN-Verbindung des Clientcomputers nicht automatisch wiederhergestellt.

### Werden automatische Verbindungswiederherstellung und DDNS bei Punkt-zu-Standort-Verbindungen auf den VPN-Clients unterstützt?

Automatische Verbindungswiederherstellung und DDNS werden in Punkt-zu-Standort-VPNs derzeit nicht unterstützt.

### Kann ich im gleichen virtuellen Netzwerk sowohl Standort-zu-Standort- als auch Punkt-zu-Standort-Konfigurationen verwenden?

Ja. Sie können beide Lösungen verwenden, wenn für Ihr virtuelles Netzwerk ein VPN-Gateway mit dynamischem Routing zur Verfügung steht. Bei VPN-Gateways mit statischem Routing werden keine Punkt-zu-Standort-Verbindungen unterstützt.

### Kann ich einen Punkt-zu-Standort-Client so konfigurieren, dass er gleichzeitig eine Verbindung mit mehreren virtuellen Netzwerken herstellt?

Ja, das ist möglich. Allerdings dürfen sich weder die IP-Präfixe noch die Punkt-zu-Standort-Adressräume der virtuellen Netzwerke überschneiden.

### Wie viel Durchsatz kann ich bei einer Standort-zu-Standort- oder bei einer Punkt-zu-Standort-Verbindung erwarten?

Der genaue Durchsatz der VPN-Tunnel lässt sich nur schwer ermitteln. IPsec und SSTP sind VPN-Protokolle mit hohem Kryptografieaufwand. Außerdem wird der Durchsatz durch die Latenz und die Bandbreite zwischen Ihrem Standort und dem Internet eingeschränkt.

## Gateways

### Was ist ein richtlinienbasiertes Gateway mit statischem Routing?

Gateways mit statischem Routing werden zur Implementierung richtlinienbasierter VPNs verwendet. Bei richtlinienbasierten VPNs werden Pakete verschlüsselt und durch IPsec-Tunnel geleitet. Grundlage hierfür sind Kombinationen aus Adresspräfixen zwischen Ihrem lokalen Netzwerk dem Azure-VNet. Die Richtlinie (auch Datenverkehrsselektor genannt) wird in der Regel als Zugriffsliste in der VPN-Konfiguration definiert.

### Was ist ein routebasiertes Gateway mit dynamischem Routing?

Gateways mit dynamischem Routing dienen zur Implementierung routingbasierter VPNs. Bei routingbasierten VPNs werden Pakete auf der Grundlage der Routen der IP-Weiterleitungs- oder -Routingtabelle an die entsprechenden VPN-Tunnelschnittstellen weitergeleitet. An den Tunnelschnittstellen werden die Pakete dann ver- bzw. entschlüsselt. Die Richtlinie bzw. der Datenverkehrsselektor für routingbasierte VPNs werden im Any-to-Any-Format (bzw. als Platzhalter) konfiguriert.

### Kann ich die IP-Adresse meines VPN-Gateways vor dessen Erstellung erhalten?

Nein. Die IP-Adresse kann erst nach der Erstellung des Gateways bezogen werden. Wenn Sie das VPN-Gateway löschen und anschließend neu erstellen, ändert sich die IP-Adresse.

### Wie wird mein VPN-Tunnel authentifiziert?

Beim Azure-VPN erfolgt die Authentifizierung mittels vorinstalliertem Schlüssel (Pre-Shared Key, PSK). Der vorinstallierte Schlüssel wird bei der Erstellung des VPN-Tunnels generiert. Der automatisch generierte PSK kann mit dem PowerShell-Cmdlet „Set Pre-Shared Key“ oder mithilfe der entsprechenden REST-API angepasst werden.

### Kann ich mit der API „Set Pre-Shared Key“ mein VPN mit statischem Routing-Gateway konfigurieren?

Ja. Mit der API „Set Pre-Shared Key“ und dem PowerShell-Cmdlet können Sie sowohl Azure-VPNs mit statischem Routing als auch VPNs mit dynamischem Routing konfigurieren.

### Kann ich andere Authentifizierungsoptionen verwenden?

Für die Authentifizierung können nur vorinstallierte Schlüssel (Pre-Shared Keys, PSKs) verwendet werden.

### Was ist das Gatewaysubnetz, und wozu wird es benötigt?

Es gibt einen Gatewaydienst, der ausgeführt wird, um standortübergreifende Verbindungen zu ermöglichen. Für das Routing zwischen Ihrem Standort und der Cloud benötigen wir zwei IP-Adressen aus Ihrer Routingdomäne. Sie müssen mindestens ein Subnetz vom Typ „/29“ angeben, aus dem wir IP-Adressen für die Einrichtung von Routen entnehmen können. Obwohl Sie ein /29-Subnetz erstellen können, berücksichtigen Sie, dass für einige Features eine bestimmten Gatewaygröße erforderlich ist. Halten Sie sich bitte an die Anforderungen an das Gatewaysubnetz für das Feature, das Sie konfigurieren möchten.

Beachten Sie, dass im Gatewaysubnetz keine virtuellen Computer oder Rolleninstanzen bereitgestellt werden dürfen.

### Wie kann ich angeben, welcher Datenverkehr über das VPN-Gateway abgewickelt werden soll?

Wenn Sie das klassische Azure-Portal verwenden, fügen Sie die einzelnen Bereiche, die über das Gateway für Ihr virtuelles Netzwerk gesendet werden sollen, auf der Seite "Netzwerke" unter "Lokale Netzwerke" hinzu.

### Kann ich eine erzwungene Tunnelung konfigurieren?

Ja. Weitere Informationen finden Sie unter [Konfigurieren der Tunnelerzwingung](vpn-gateway-about-forced-tunneling.md).

### Kann ich in Azure einen eigenen VPN-Server einrichten und damit eine Verbindung mit meinem lokalen Netzwerk herstellen?

Ja. Sie können in Azure eigene VPN-Gateways oder -Server bereitstellen (entweder über Azure Marketplace oder durch Erstellung eines eigenen VPN-Routers). Sie müssen in Ihrem virtuellen Netzwerk benutzerdefinierte Routen konfigurieren, um sicherzustellen, dass der Datenverkehr ordnungsgemäß zwischen Ihren lokalen Netzwerken und den Subnetzen Ihres virtuellen Netzwerks weitergeleitet wird.

### Warum werden auf meinem VPN-Gateway bestimmte Ports geöffnet?

Sie sind für die Kommunikation mit der Azure-Infrastruktur erforderlich. Sie werden von Azure-Zertifikaten geschützt (gesperrt). Ohne die richtigen Zertifikate werden externe Entitäten, einschließlich der Kunden dieser Gateways, sich nicht auf diese Endpunkte auswirken.

Ein VPN-Gateway ist im Grunde ein mehrfach vernetztes Gerät mit einer NIC, die das private Netzwerk des Kunden nutzt, und einer NIC für das öffentliche Netzwerk. Azure-Infrastrukturentitäten können aus Compliance-Gründen keine privaten Netzwerke von Kunden nutzen, sodass sie öffentliche Endpunkte für die Kommunikation der Infrastruktur nutzen müssen. Die öffentlichen Endpunkte werden in regelmäßigen Abständen mit der Azure-Sicherheitsüberwachung überprüft.


### Weitere Informationen zu Gatewaytypen, Anforderungen und Durchsatz

Weitere Informationen finden Sie unter[Informationen zu VPN-Gateways](vpn-gateway-about-vpngateways.md).

## Mehrere Standorte und VNet-zu-VNet-Verbindungen

### Welche Art von Gateway unterstützt mehrere Standorte sowie VNet-zu-VNet-Verbindungen?

Nur VPNs mit dynamischem Routing.

### Kann ich eine Verbindung zwischen einem virtuellen Netzwerk mit VPN und dynamischem Routing und einem anderen virtuellen Netzwerk mit VPN und statischem Routing herstellen?

Nein. Beide virtuellen Netzwerke müssen VPNs mit dynamischem Routing verwenden.

### Ist der Datenverkehr bei VNet-zu-VNet-Verbindungen sicher?

Ja, er wird mittels IPsec-/IKE-Verschlüsselung geschützt.

### Wird VNet-zu-VNet-Datenverkehr über den Azure-Backbone übertragen?

Ja.

### Mit wie vielen lokalen Standorten und virtuellen Netzwerken kann ein virtuelles Netzwerk verbunden werden?

Maximal zehn (kombiniert für Basic- und Standard-Gateways mit dynamischem Routing); 30 bei VPN-Hochleistungsgateways.

### Kann ich Punkt-zu-Standort-VPNs mit meinem virtuellen Netzwerk und mehreren VPN-Tunneln kombinieren?

Ja. VPNs vom Typ „Punkt zu Standort“ (P2S) können mit den VPN-Gateways kombiniert werden, die Verbindungen mit mehreren lokalen Standorten und anderen virtuellen Netzwerken herstellen.

### Kann ich mithilfe eines VPNs mit mehreren Standorten mehrere Tunnel zwischen meinem virtuellen Netzwerk und meinem lokalen Standort konfigurieren?

Nein. Redundante Tunnel zwischen einem virtuellen Netzwerk von Azure und einem lokalen Standort werden nicht unterstützt.

### Dürfen sich die Adressräume der verbundenen virtuellen Netzwerke und der lokalen Standorte überschneiden?

Nein. Bei einer Überschneidung der Adressräume kann die netcfg-Datei nicht hochgeladen und kein virtuelles Netzwerk erstellt werden.

### Erhalte ich durch mehr Standort-zu-Standort-VPNs mehr Bandbreite als bei einem einzelnen virtuellen Netzwerk?

Nein. Alle VPN-Tunnel (einschließlich Punkt-zu-Standort-VPNs) verwenden das gleiche Azure-VPN-Gateway und die gleiche verfügbare Bandbreite.

### Kann ich mit dem Azure-VPN-Gateway Datenverkehr zwischen meinen lokalen Standorten oder an ein anderes virtuelles Netzwerk übertragen?

Datenverkehr kann über das Azure-VPN-Gateway übertragen werden, die Übertragung basiert jedoch auf statisch definierten Adressräumen aus der netcfg-Konfigurationsdatei. BGP wird bei virtuellen Netzwerken von Azure und VPN-Gateways noch nicht unterstützt. Ohne BGP müssen die Adressräume für die Übertragung manuell in der netcfg-Datei definiert werden. Dies ist jedoch sehr fehleranfällig und wird daher nicht empfohlen.

### Generiert Azure für alle meine VPN-Verbindungen für das gleiche virtuelle Netzwerk den gleichen vorinstallierten IPsec-/IKE-Schlüssel?

Nein. Azure generiert für unterschiedliche VPN-Verbindungen standardmäßig unterschiedliche vorinstallierte Schlüssel. Mit der REST-API oder dem PowerShell-Cmdlet „Set VPN Gateway Key“ können Sie jedoch den Schlüsselwert nach Ihren Vorstellungen festlegen. Bei dem Schlüssel muss es sich zwingend um eine alphanumerische Zeichenfolge mit einer Länge zwischen einem und 128 Zeichen handeln.

### Fallen bei Azure Kosten für den Datenverkehr zwischen virtuellen Netzwerken an?

Beim Datenverkehr zwischen verschiedenen virtuellen Netzwerken fallen nur Kosten an, wenn sich die Daten zwischen verschiedenen Azure-Regionen bewegen. Die Höhe der Kosten finden Sie auf der Azure-Seite [VPN Gateway Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/).


### Kann ich eine Verbindung zwischen einem virtuellen Netzwerk mit IPsec-VPNs und meiner ExpressRoute-Verbindung herstellen?

Ja, diese Möglichkeit wird unterstützt. Weitere Informationen finden Sie unter [Konfigurieren von gleichzeitig vorhandenen ExpressRoute- und Standort-zu-Standort-VPN-Verbindungen](../expressroute/expressroute-howto-coexist-classic.md).

## Standortübergreifende Konnektivität und virtuelle Computer

### Wenn sich mein virtueller Computer in einem virtuellen Netzwerk befindet und ich über eine standortübergreifende Verbindung verfüge, wie sollte ich dann die Verbindung mit dem virtuellen Computer herstellen?

Hier haben Sie mehrere Möglichkeiten: Wenn Sie RDP aktiviert und einen Endpunkt erstellt haben, können Sie die Verbindung mit dem virtuellen Computer über die VIP-Adresse herstellen. Geben Sie in diesem Fall die VIP-Adresse und den Port an, mit dem Sie eine Verbindung herstellen möchten. Auf Ihrem virtuellen Computer müssen Sie den Port für den Datenverkehr konfigurieren. In der Regel speichern Sie hierzu die Einstellungen für die RDP-Verbindung mithilfe des klassischen Azure-Portals auf dem Computer. Die Einstellungen enthalten die erforderlichen Verbindungsinformationen.

Wenn Sie ein virtuelles Netzwerk mit standortübergreifender Verbindung konfiguriert haben, können Sie die Verbindung mit Ihrem virtuellen Computer über die interne DIP-Adresse oder über die private IP-Adresse herstellen. Außerdem kann die Verbindung mit dem virtuellen Computer von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk über die interne DIP-Adresse hergestellt werden. Wenn Sie die Verbindung an einem Standort außerhalb Ihres virtuellen Netzwerks herstellen möchten, kann über die DIP-Adresse keine RDP-Verbindung hergestellt werden. Wen Sie also beispielsweise ein virtuelles Netzwerk mit Punkt-zu-Standort-Verbindung konfiguriert haben und die Verbindung nicht von Ihrem Computer aus herstellen, ist keine DIP-basierte Verbindung mit dem virtuellen Computer möglich.

### Wenn sich mein virtueller Computer in einem virtuellen Netzwerk mit standortübergreifender Verbindung befindet, wird dann der gesamte Datenverkehr meines virtuellen Computers über diese Verbindung abgewickelt?

Nein. Nur der Datenverkehr mit einer IP-Zieladresse, die innerhalb der angegebenen lokalen Netzwerk-IP-Adressbereiche des virtuellen Netzwerks liegt, wird über das Gateway des virtuellen Netzwerks abgewickelt. Datenverkehr mit einer IP-Zieladresse im virtuellen Netzwerk bleibt im virtuellen Netzwerk. Anderer Datenverkehr wird über den Load Balancer an die öffentlichen Netzwerke oder (bei erzwungener Tunnelung) über das Azure-VPN-Gateway gesendet. Bei der Problembehandlung müssen Sie sich vergewissern, dass alle Bereiche, die über das Gateway gesendet werden sollen, in Ihrem lokalen Netzwerk aufgeführt sind. Vergewissern Sie sich, dass sich die Adressbereiche des lokalen Netzwerks nicht mit einem der Adressbereiche im virtuellen Netzwerk überschneiden. Vergewissern Sie sich außerdem, dass der verwendete DNS-Server den Namen zur korrekten IP-Adresse auflöst.


## FAQs zu virtuellen Netzwerken

Weitere Informationen zu virtuellen Netzwerken finden Sie in den [FAQs zu virtuellen Netzwerken](../virtual-network/virtual-networks-faq.md).

## Nächste Schritte

Weitere Informationen zu VPN-Gateways erhalten Sie auf der Seite [VPN Gateway-Dokumentation](https://azure.microsoft.com/documentation/services/vpn-gateway/).

 

<!---HONumber=AcomDC_0302_2016-->