<properties
   pageTitle="Microsoft Cloud Services und Netzwerksicherheit | Microsoft Azure"
   description="Lernen Sie einige der wesentlichen Features von Azure zum Einrichten sicherer Netzwerkumgebungen kennen."
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="jonor;sivae"/>

# Microsoft Cloud Services und Netzwerksicherheit

Microsoft Cloud Services bieten hyperskalierbare Dienste und Infrastrukturen, Features auf Unternehmensniveau und vielfältige Auswahlmöglichkeiten für Hybridkonnektivität. Kunden können entweder über das Internet oder mit ExpressRoute auf diese Dienste zugreifen, das private Netzwerkkonnektivität bereitstellt. Microsoft Azure Platform ermöglicht Kunden eine nahtlose Erweiterung ihrer Infrastruktur auf die Cloud und den Aufbau mehrstufiger Architekturen. Die bereitgestellten Microsoft-Dienste können durch Sicherheitsdienste und virtuelle Geräte von Drittanbietern ergänzt werden. Dieses Whitepaper bietet einen Überblick über Aspekte zu Sicherheit und Architektur, die Kunden beim Einsatz von Microsoft Cloud Services über ExpressRoute sowie beim Erstellen sicherer Dienste in Microsoft Azure Virtual Network berücksichtigen sollten.

## Schnelleinstieg
Das nachstehende Diagramm bietet Hilfestellung bei der Auswahl aus den vielen Sicherheitsverfahren, die mit Microsoft Azure Platform zur Verfügung stehen. Finden Sie anhand dieses Diagramms schnell heraus, was Sie benötigen, oder lesen Sie das vollständige Dokument, um eine Erläuterung zu zahlreichen der verfügbaren Optionen zu erhalten. ![Flussdiagramm zu den Sicherheitsoptionen][0]

[Beispiel 1: Erstellen einer einfachen DMZ mit Netzwerksicherheitsgruppen](#example-1-build-a-simple-dmz-with-nsgs)</br> [Beispiel 2: Erstellen einer DMZ zum Schutz von Anwendungen mit Firewall und NSGs](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br> [Beispiel 3: Erstellen einer DMZ zum Schutz von Netzwerken mit Firewall, UDR und NSG](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br> [Beispiel 4: Hinzufügen einer Hybridverbindung mit einem Site-to-Site-VPN über ein virtuelles Gerät](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br> [Beispiel 5: Hinzufügen einer Hybridverbindung mit einem Site-to-Site-VPN über ein Azure-Gateway](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br> [Beispiel 6: Hinzufügen einer Hybridverbindung mit ExpressRoute](#example-6-adding-a-hybrid-connection-with-expressroute)</br> In den nächsten Monaten wird das vorliegende Dokument um Beispiele zum Hinzufügen von VNet-zu-VNet-Verbindungen, Hochverfügbarkeit und Dienstverkettung ergänzt.

## Microsoft-Compliance und Infrastrukturschutz
Microsoft nimmt in Bezug auf Compliance-Initiativen zur Unterstützung von Unternehmenskunden eine führende Rolle ein. Nachfolgend sehen Sie einige der Compliancezertifizierungen für Azure: ![Azure-Compliancezertifizierungen][1]

Weitere Informationen finden Sie unter: [http://azure.microsoft.com/de-de/support/trust-center/compliance/](http://azure.microsoft.com/support/trust-center/compliance/)

Microsoft verfolgt einen umfassenden Ansatz zum Schutz der Cloudinfrastruktur, die zur Ausführung hyperskalierbarer globaler Dienste erforderlich ist. Die Microsoft-Cloudinfrastruktur umfasst Hardware, Software, Netzwerke, administratives und operatives Personal sowie die physischen Datencenter.

![Sicherheitsfeatures von Azure][2]

Der oben genannte Ansatz bietet Kunden eine sichere Grundlage für die Bereitstellung ihrer Dienste in der Microsoft-Cloud. Der nächste Schritt besteht für Kunden darin, eine sichere Architektur zum Schutz dieser Dienste zu entwerfen und einzurichten.

## Traditionelle Sicherheitsarchitekturen und demilitarisierte Zonen (DMZs)
Wenngleich Microsoft hohe Investitionen in den Schutz der Cloudinfrastruktur tätigt, müssen Kunden ebenfalls für den Schutz ihrer Clouddienste und Ressourcengruppen sorgen. Ein mehrstufiger Ansatz bei der Sicherheit bietet die beste Absicherung. Eine Netzwerksicherheit mit demilitarisierter Zone (DMZ) schützt die internen Netzwerkressourcen vor einem nicht vertrauenswürdigen Netzwerk. Der Einsatz von demilitarisierten Zonen (DMZ) ist ein bekanntes Konzept bei der IT-Netzwerksicherheitsarchitektur für Unternehmen. Hierbei bezeichnet DMZ den Rand oder Bereiche des Netzwerks zwischen dem Internet und der geschützten IT-Infrastruktur des Unternehmens.

In typischen Unternehmensnetzwerken wird der Kern der Infrastruktur im Umkreisnetzwerk durch mehrere Stufen von Sicherheitsgeräten stark geschützt. An den Grenzen jeder Stufe befinden sich Sicherheitsgeräte und Punkte zur Durchsetzung von Richtlinien. Hierzu gehören Firewalls, DDoS-Schutz (Distributed Denial of Service), IDS/IPS (Intrusion Detection or Protection Systems), Geräte für virtuelle private Netzwerke (VPNs) usw. Die Durchsetzung von Richtlinien kann über Firewallrichtlinien, Zugriffssteuerungslisten (Access Control Lists, ACLs) oder ein spezifisches Routing erfolgen. Die erste Schutzstufe im Netzwerk, die für den direkten Empfang von Datenverkehr aus dem Internet verantwortlich ist, nutzt eine Kombination dieser Mechanismen, um Angriffe und schädlichen Datenverkehr abzuwehren und legitime Anforderungen an das Netzwerk weiterzuleiten. Dieser Datenverkehr wird direkt an Ressourcen in der DMZ geroutet. Die Ressourcen können anschließend mit anderen Ressourcen kommunizieren, die sich weiter im Inneren des Netzwerks befinden. Für ein weiteres Routing in das Netzwerk muss die nächste Grenze der Validierung passiert werden. Die ganz außen liegende Schicht wird als DMZ bezeichnet, da dieser Teil des Netzwerks offen gegenüber dem Internet ist. Üblicherweise kommen verschiedene Formen des Schutzes auf beiden Seiten der DMZ zum Einsatz. Die nachfolgende Abbildung zeigt ein Beispiel einer DMZ mit einem einzigen Subnetz in einem Unternehmensnetzwerk mit zwei Sicherheitsgrenzen (DMZ-Internet und DMZ-Back-End-VLAN).

![Eine DMZ in einem Unternehmensnetzwerk][3]

Es gibt zahlreiche Architekturen zum Implementieren einer DMZ – diese reichen von einem einfachen Load Balancer, der einer Webfarm vorgeschaltet ist, bis hin zu einer DMZ mit mehreren Subnetzen und verschiedenen Mechanismen an jeder Grenze, um Datenverkehr zu blockieren und die tieferen Schichten des Unternehmensnetzwerks zu schützen. Der Aufbau der DMZ richtet sich nach den spezifischen Anforderungen der Organisation und der jeweiligen Risikotoleranz.

Wenn Kunden ihre Workloads in öffentliche Clouds verschieben, ist es von wesentlicher Bedeutung, ähnliche Funktionen für DMZ-Architekturen in Azure zu unterstützen, um Compliance- und Sicherheitsanforderungen zu erfüllen. In diesem Dokument werden Richtlinien bereitgestellt, anhand derer Kunden eine sichere Netzwerkumgebung in Azure aufbauen können. Hierbei wird besonderes Augenmerk auf die DMZ gelegt, es werden aber auch viele Aspekte der Netzwerksicherheit beleuchtet, u. a.:

1.	Wie kann in Azure eine DMZ eingerichtet werden?
2.	Welche Azure-Features stehen zum Erstellen einer DMZ zur Verfügung?
3.	Wie können Back-End-Workloads geschützt werden?
4.	Wie wird die Internetkommunikation mit Workloads in Azure gesteuert?
5.	Wie können lokale Netzwerke über Bereitstellungen in Azure geschützt werden?
6.	Wann sollten systemeigene Azure-Sicherheitsfeatures und wann Geräte oder Dienste von Drittanbietern genutzt werden?

Das folgende Diagramm zeigt verschiedene Sicherheitsstufen, die Azure sowohl systemeigen über Azure Platform selbst als auch über vom Kunden definierte Funktionen bereitstellt:

![Sicherheitsarchitektur von Azure][4]

Als erste Grenze gegenüber dem Internet bietet Azure DDoS Schutz vor groß angelegten Angriffen gegen Azure. Nach dem Passieren dieser Grenze gelangt der Datenverkehr zu den vom Kunden definierten öffentlichen Endpunkten, anhand derer ermittelt wird, welcher Datenverkehr den Clouddienst zum virtuellen Netzwerk passieren darf. Die systemeigene Azure Virtual Network-Isolation stellt eine vollständige Isolierung von allen anderen Netzwerken sicher und gewährleistet, dass der Datenverkehr nur über vom Benutzer konfigurierte Pfade und Methoden fließt. Diese Pfade und Methoden stellen die nächste Schicht dar, in der Netzwerksicherheitsgruppen (NSGs), benutzerdefiniertes Routing (User Defined Routing, UDR) und virtuelle Netzwerkgeräte dazu verwendet werden, Sicherheitsgrenzen (einschließlich von DMZs) einzurichten, um die Anwendungsbereitstellungen im Netzwerk zu schützen.

Im nächsten Abschnitt erhalten Sie einen Überblick über virtuelle Azure-Netzwerke. Virtuelle Azure-Netzwerke werden von den Kunden erstellt und sind die Netzwerke, mit denen die bereitgestellten Workloads eine Verbindung herstellen. Virtuelle Netzwerke sind die Grundlage aller Netzwerksicherheitsfeatures, die zum Einrichten einer DMZ für den Schutz von Kundenbereitstellungen in Azure erforderlich sind.

## Übersicht über Azure Virtual Networks
Bevor der Internetdatenverkehr Azure Virtual Networks erreicht, müssen zwei Sicherheitsstufen von Azure Platform passiert werden:

1.	**DDoS-Schutz**: DDoS (Distributed Denial of Service Protection, verteilter Denial-of-Service-Schutz) ist eine Schicht im physischen Azure-Netzwerk, die Azure Platform selbst vor groß angelegten Angriffen aus dem Internet schützt, bei denen Angreifer über mehrere "bot"-Knoten versuchen, einen Internetdienst zu überwinden. Azure verfügt über ein robustes DDoS-Schutzsystem für alle eingehenden Internetverbindungen. Diese DDoS-Sicherheitsebene umfasst keine durch den Benutzer konfigurierbaren Attribute und ist für den Kunden nicht zugänglich. Auf diese Weise wird Azure als Plattform vor groß angelegten Angriffen geschützt, ein direkter Schutz einzelner Kundenanwendungen ist jedoch nicht gegeben. Der Kunde kann zusätzliche Ausfallsicherheitslösungen konfigurieren, um sich vor örtlich begrenzten Angriffen zu schützen. Wenn beispielsweise Kunde A von einem groß angelegten DDoS-Angriff auf einen öffentlichen Endpunkt betroffen ist, blockiert Azure die Verbindungen mit diesem Dienst. Kunde A kann zur Dienstwiederherstellung ein Failover auf ein anderes virtuelles Netzwerk oder einen Dienstendpunkt durchführen, der nicht von dem Angriff betroffen ist. Hierbei ist zu beachten, dass Kunde A zwar auf diesem Endpunkt von dem Angriff betroffen ist, jedoch keinerlei Dienste außerhalb dieses Endpunkts. Andere Kunden und Diensten werden in keiner Weise durch den Angriff beeinträchtigt.
2.	**Dienstendpunkte**: Endpunkte ermöglichen Clouddiensten oder Ressourcengruppen die Verwendung öffentlicher (Internet-) IP-Adressen und Ports. Der Endpunkt leitet Datenverkehr per Netzwerkadressübersetzung (NAT) an die interne Adresse und den internen Port im virtuellen Azure-Netzwerk weiter. Dies ist der primäre Pfad, auf dem externer Datenverkehr in das virtuelle Azure-Netzwerk gelangt. Die Dienstendpunkte können durch den Benutzer konfiguriert werden und legen fest, welcher Datenverkehr zugelassen wird bzw. wie und wo eine Übersetzung in das virtuelle Netzwerk stattfindet. 

Sobald der Datenverkehr das virtuelle Netzwerk erreicht, kommen zahlreiche Features ins Spiel, da virtuelle Azure-Netzwerke die Grundlage darstellen, auf der Kunden ihre Workloads einbinden und die grundlegende Sicherheit auf Netzwerkebene anwenden. Es handelt sich um ein privates Netzwerk (ein virtuelles Netzwerkoverlay) in Azure, das den Kunden die folgenden Features und Merkmale bietet:
 
1.	**Isolation des Datenverkehrs**: Azure Platform verwendet virtuelle Netzwerke, um Datenverkehr zu isolieren. Virtuelle Computer in einem virtuellen Netzwerk können nicht direkt mit VMs in einem anderen virtuellen Netzwerk kommunizieren – selbst dann nicht, wenn beide virtuelle Netzwerke durch denselben Kunden erstellt werden. Dies ist eine wichtige Eigenschaft, mit der sichergestellt wird, dass Kunden-VMs und -kommunikation innerhalb eines virtuellen Netzwerks privat bleiben. 
2.	**Mehrstufige Topologie**: Virtuelle Netzwerke ermöglichen Kunden das Definieren einer mehrstufigen Topologie, indem unterschiedlichen Elementen oder "Stufen" ihrer Workloads Subnetze und separate Adressräume zugewiesen werden. Diese logischen Gruppierungen und Topologien ermöglichen es den Kunden, unterschiedliche Zugriffsrichtlinien basierend auf dem Workloadtyp zu definieren und außerdem den Datenverkehr zwischen den Stufen zu steuern. 
3.	**Standortübergreifende Konnektivität**: Kunden können mithilfe von Azure-VPN-Gateways oder virtuellen Netzwerkgeräten von Drittanbietern standortübergreifende Konnektivität zwischen einem virtuellen Netzwerk und mehreren lokalen Standorten oder anderen virtuellen Netzwerken in Azure einrichten. Azure unterstützt Site-to-Site-VPNs (S2S) unter Verwendung von IPsec/IKE-Standardprotokollen und privater ExpressRoute-Konnektivität. 
4.	**Netzwerksicherheitsgruppen (NSGs)**: Ermöglichen dem Kunden das Erstellen von Regeln (ACLs) auf der gewünschten Ebene: für Netzwerkschnittstellen, einzelne VMs oder virtuelle Subnetze. Kunden können den Zugriff steuern, indem sie die Kommunikation zwischen den Workloads innerhalb eines virtuellen Netzwerks, zwischen Systemen im Kundennetzwerk über standortübergreifende Verbindungen oder die direkte Internetkommunikation zulassen oder ablehnen. 
5.	**Benutzerdefinierte Routen (UDR)** und **IP-Weiterleitung**: Ermöglicht Kunden das Definieren der Kommunikationspfade zwischen unterschiedlichen Stufen innerhalb eines virtuellen Netzwerks. Kunden können eine Firewall, IDS/IPS und andere virtuelle Geräte bereitstellen und den Netzwerkdatenverkehr zum Erzwingen von Sicherheitsrichtlinien, zur Überprüfung und zu Überwachungszwecken über diese Sicherheitsgeräte routen.
6.	**Virtuelle Netzwerkgeräte** im Azure Marketplace: Sicherheitsgeräte wie z. B. Firewalls, Load Balancer und IDS/IPS (Intrusion Detection/Prevention Services) stehen im Azure Marketplace und im VM-Imagekatalog zur Verfügung. Kunden können diese Geräte in ihren virtuellen Netzwerken und insbesondere an ihren Sicherheitsgrenzen (einschließlich DMZ-Subnetzen) einsetzen, um ihre mehrstufige Sicherheitsumgebung zu vervollständigen.

Mithilfe dieser Features und Komponenten kann ein Beispiel einer DMZ-Architektur auf der Grundlage von Azure so aussehen:

![Ein DMZ in einem virtuellen Azure-Netzwerk][5]

## DMZ – Merkmale und Anforderungen
In diesem Abschnitt werden die Merkmale und Anforderungen für eine DMZ in Azure beschrieben. Wie bereits zuvor erwähnt, ist die DMZ als Front-End des Netzwerks konzipiert und stellt eine direkte Schnittstelle zur Kommunikation mit dem Internet dar. Die eingehenden Paket sollten über die Sicherheitsgeräte in der DMZ – Firewall, IDS, IPS usw. – geleitet werden, bevor sie die Back-End-Server erreichen. Von den Workloads an das Internet gesendete Pakete können zur Richtlinienerzwingung, zur Überprüfung und zu Überwachungszwecken ebenfalls über die Sicherheitsgeräte in der DMZ geleitet werden, bevor sie das Netzwerk verlassen. Zusätzlich kann die DMZ zum Hosten standortübergreifender VPN-Gateways zwischen virtuellen Kundennetzwerken und lokalen Netzwerken eingesetzt werden.

### DMZ-Merkmale
Mit Bezug auf die obige Abbildung sollte eine gut eingerichtete DMZ in einem virtuellen Azure-Netzwerk die folgenden Eigenschaften aufweisen:

- DMZ als Schnittstelle zum Internet (Front-End):
    - Das DMZ-Subnetz selbst ist eine Schnittstelle zur direkten Kommunikation mit dem Internet.
    - Öffentliche IPs, VIPs und/oder Dienstendpunkte leiten Internetdatenverkehr an das Front-End-Netzwerk und die zugehörigen Geräte.
    - Eingehender Datenverkehr aus dem Internet passiert die Sicherheitsgeräte, bevor andere Ressourcen im Front-End-Netzwerk erreicht werden.
    - Wenn die ausgehende Sicherheit aktiviert ist, passiert der Datenverkehr im letzten Schritt die Sicherheitsgeräte, bevor er an das Internet gesendet wird.
- "Geschütztes Netzwerk" (Back-End): Schnittstelle zur Kerninfrastruktur
    - Kein direkter Pfad vom Internet zur Kerninfrastruktur
    - Kanäle zur Kerninfrastruktur MÜSSEN die Sicherheitsgeräte passieren, z. B. NSGs, Firewalls oder VPN-Geräte in der DMZ.
    - Über andere Geräte in der DMZ DARF KEINE Überbrückung zwischen Internet und Kerninfrastruktur stattfinden.
    - Bei den Sicherheitsgeräten, die als Schnittstelle zum Internet und als Grenze zum geschützten Netzwerk der DMZ dienen (oben dargestellt durch die zwei Firewallsymbole), kann es sich tatsächlich um ein einziges virtuelles Gerät mit unterschiedlichen Regeln oder Schnittstellen für die Internetgrenze und die Back-End-Grenze handeln (d. h. ein Gerät, logisch getrennt, verarbeitet die Datenlast für beide DMZ-Grenzen).
- Weitere gängige Methoden und Einschränkungen
    - Workloads in der DMZ DÜRFEN KEINE kritischen Geschäftsinformationen speichern.
    - Der Zugriff auf und die Aktualisierung von DMZ-Konfigurationen und -Bereitstellungen ist ausschließlich auf autorisierte Administratoren beschränkt

### DMZ-Anforderungen
Die folgende Liste bietet Hilfestellung in Bezug auf die Anforderungen virtueller Netzwerke für eine erfolgreiche Implementierung einer DMZ:

- Subnetzarchitektur: Konfigurieren Sie das virtuelle Netzwerk so, dass ein gesamtes Subnetz als DMZ reserviert ist, getrennt von anderen Subnetzen im selben virtuellen Netzwerk. Auf diese Weise wird sichergestellt, dass der Datenverkehr zwischen der DMZ und anderen internen oder privaten Subnetzstufen über eine Firewall oder ein virtuelles IDS/IPS-Gerät mit benutzerdefiniertem Routing (UDR) an den Subnetzgrenzen fließt.
- Netzwerksicherheitsgruppen (NSGs) für die DMZ: Das Subnetz selbst sollte offen sein, um eine Kommunikation zwischen der DMZ und dem Internet zu ermöglichen, aber dies bedeutet nicht, dass Kunden NSGs umgehen sollten. Beachten Sie die folgenden Best Practices in Bezug auf die Sicherheit, damit die Netzwerkschnittstelle zum Internet möglichst wenig Angriffsfläche bietet. Schränken Sie beispielsweise die Remoteadressbereiche für den Zugriff auf die Bereitstellungen und/oder die spezifischen Anwendungsprotokolle und Ports ein, die geöffnet sind. Beachten Sie jedoch, dass dies nicht immer möglich ist. Wenn Kunden beispielsweise über eine externe Website in Azure verfügen, sollte die DMZ eingehende Webanforderungen von beliebigen öffentlichen IP-Adressen zulassen, jedoch nur die Webanwendungsports öffnen: TCP:80 und TCP:443.
- Routingtabelle für die DMZ: Das DMZ-Subnetz selbst sollte in der Lage sein, direkt mit dem Internet zu kommunizieren, es sollte jedoch keine direkte Kommunikation von und zum Back-End oder lokalen Netzwerken stattfinden, ohne den Datenverkehr über eine Firewall oder ein Sicherheitsgerät zu leiten.
- Konfiguration von DMZ-Sicherheitsgeräten: Um Pakete zwischen der DMZ und allen Komponenten des geschützten Netzwerks routen und überprüfen zu können, können Sicherheitsgeräte wie z. B. Firewall, IDS- und IPS-Geräte in der DMZ mehrfach vernetzt sein, mit separaten Netzwerkkarten (NICs) für die DMZ und die Back-End-Subnetze. Die NICs in der DMZ kommunizieren direkt mit dem Internet (eingehend und ausgehend), unter Verwendung entsprechender NSGs und der DMZ-Routingtabelle. Die NICs zur Verbindung mit den Back-End-Subnetzen verfügen über eingeschränktere NSGs und Routingtabellen für die entsprechenden Back-End-Subnetze.
- Funktionalität der DMZ-Sicherheitsgeräte: Die in der DMZ bereitgestellten Sicherheitsgeräte weisen typischerweise die folgende Funktionalität auf:
    - Firewall: Erzwingt Firewallregeln oder Richtlinien für die Zugriffssteuerung für eingehende Anforderungen.
    - Angriffserkennung und -abwehr: Erkennen und Abschwächen böswilliger Angriffe aus dem Internet.
    - Überwachung und Protokollierung: Speichern detaillierter Protokolle für Überwachung und Analyse.
    - Reverseproxy: Leitet die eingehenden Anforderungen an die entsprechenden Back-End-Server weiter, indem die Zieladressen auf den DMZ-Front-End-Geräten (typischerweise Firewalls) zugeordnet und in die tatsächlichen Back-End-Serveradressen übersetzt werden.
    - Weiterleitungsproxy: Sorgt für die Netzwerkadressübersetzung (NAT) und überwacht die Kommunikation aus dem virtuellen Netzwerk mit dem Internet.
    - Router: Fungiert als Router zur Weiterleitung von eingehendem und subnetzübergreifendem Datenverkehr im virtuellen Netzwerk.
    - VPN-Geräte: Fungieren als standortübergreifende VPN-Gateways zur Bereitstellung standortübergreifender VPN-Konnektivität zwischen lokalen Kundennetzwerken und virtuellen Azure-Netzwerken.
    - VPN-Server: VPN-Server akzeptieren VPN-Clientverbindungen mit virtuellen Azure-Netzwerken.

>[AZURE.TIP]Sorgen Sie für eine vollständige Trennung zwischen dem autorisierten Personal mit Zugriff auf die DMZ-Sicherheitsgeräte und den Mitarbeitern, die als Administratoren für Entwicklung/Bereitstellung/Betrieb von Anwendungen zuständig sind. Durch eine strikte Trennung dieser Gruppen können Sie eine klare Aufgabenverteilung erzielen und verhindern, dass einzelne Personen Kontrollmechanismen zur Anwendungs- und Netzwerksicherheit umgehen.

### Fragen in Bezug auf die Einrichtung von Netzwerkgrenzen
In sämtlichen Erläuterungen zu "Azure-Netzwerken" in diesem Abschnitt bezieht sich – sofern nicht ausdrücklich anders erwähnt – der Begriff "Netzwerke" (Netzwerke, virtuelle Netzwerke oder Subnetze) auf private virtuelle Azure-Netzwerke, die von einem Abonnementadministrator erstellt werden. Es werden nicht die zugrunde liegende physischen Netzwerke in Azure beschrieben.

Virtuelle Azure-Netzwerke werden auch häufig dazu verwendet, traditionelle lokale Netzwerke zu erweitern. Es ist möglich, entweder Site-to-Site- oder ExpressRoute-Hybridnetzwerklösungen in DMZ-Architekturen zu integrieren. Dies ist ein wichtiger Aspekt beim Aufbau von Netzwerksicherheitsgrenzen, der in den folgenden Fragen behandelt wird.

Wenn Sie ein Netzwerk mit einer DMZ und mehreren Sicherheitsgrenzen einrichten, müssen drei wichtige Fragen beantwortet werden.

#### 1) Wie viele Grenzen werden benötigt?
Im ersten Schritt muss entschieden werden, wie viele Sicherheitsgrenzen in einem vorgegebenen Szenario benötigt werden:

- Eine einzelne Grenze: Eine Grenze am Front-End-DMZ zwischen dem virtuellen Netzwerk und dem Internet.
- Zwei Grenzen: Eine Grenze auf der Internetseite des DMZ-Netzwerks, eine zweite Grenze zwischen dem DMZ-Subnetz und den Back-End-Subnetzen in den virtuellen Azure-Netzwerken.
- Drei Grenzen: Eine Grenze auf der Internetseite des DMZ-Netzwerks, eine zweite Grenze zwischen dem DMZ-Subnetz und den Back-End-Subnetzen, und eine dritte Grenze zwischen den Back-End-Subnetzen und dem lokalen Netzwerk.
- N Grenzen: Je nach geltenden Sicherheitsanforderungen können beliebig viele Sicherheitsgrenzen in einem Netzwerk eingerichtet werden.

Die Anzahl und Art der erforderlichen Grenzen variiert basierend darauf, wie hoch die Risikotoleranz eines Unternehmens liegt und welches Szenario implementiert wird. Diese Entscheidung wird häufig gemeinsam von verschiedenen Gruppen innerhalb einer Organisation getroffen – z. B. durch ein Team für Risiko und Compliance, ein Netzwerk-/Plattformteam und ein Team für die Anwendungsentwicklung. Bei dieser Entscheidung sollten Personen mit Einblick in die Sicherheit, relevante Daten und die eingesetzte Technologie einbezogen werden, um ein angemessenes Sicherheitsniveau für jede Implementierung zu gewährleisten.

>[AZURE.TIP]Verwenden Sie die kleinstmögliche Anzahl von Grenzen, mit denen die Sicherheitsanforderungen für ein vorgegebenes Szenario erfüllt werden können. Je mehr Grenzen implementiert werden, desto schwieriger können sich Betrieb und Problembehandlung gestalten. Gleichzeitig steigt der Aufwand für das Verwalten der verschiedenen Grenzrichtlinien. Durch eine unzureichende Anzahl von Grenzen steigt jedoch das Risiko. Entscheidend ist, die richtige Balance zu finden.

![Hybridnetzwerk mit drei Sicherheitsgrenzen][6]

Die Abbildung oben zeigt eine allgemeine Übersicht eines Netzwerks mit drei Sicherheitsgrenzen: eine Grenze zwischen DMZ und Internet, eine Grenze zwischen dem Azure-Front-End und den privaten Back-End-Subnetzen, eine Grenze zwischen Azure-Back-End-Subnetz und dem lokalen Unternehmensnetzwerk.

#### 2) Wo befinden sich die Grenzen?
Nachdem festgelegt wurde, wie viele Grenzen erforderlich sind, muss entschieden werden, wo diese implementiert werden. Es gibt im Allgemeinen drei Möglichkeiten: 1) Einsatz eines internetbasierten Diensts als Zwischenstufe (z. B. eine cloudbasierte WAF-Lösung, diese wird im Rahmen dieses Dokuments nicht besprochen), 2) Nutzung systemeigener Features und/oder virtueller Netzwerkgeräte in Azure, 3) Einsatz physischer Geräte im lokalen Netzwerk.

In reinen Azure-Netzwerken können entweder systemeigene Azure-Features (z. B. Azure Load Balancer) oder virtuelle Netzwerkgeräte aus dem umfangreichen Partnerökosystem von Azure (z. B. Check Point-Firewalls) genutzt werden.

Wenn eine Grenze zwischen Azure und einem lokalen Netzwerk benötigt wird, können die Sicherheitsgeräte sich auf einer beliebigen Seite der Verbindung (oder auf beiden Seiten) befinden. Es muss also entschieden werden, wo die Sicherheitsgeräte platziert werden.

In der Abbildung oben sind die Grenzen zwischen Internet/DMZ und Front-End/Back-End vollständig in Azure enthalten, deshalb müssen entweder systemeigene Azure-Features oder virtuelle Netzwerkgeräte verwendet werden. Sicherheitsgeräte an der Grenze zwischen Azure (Back-End-Subnetz) und dem Unternehmensnetzwerk können entweder auf Azure-Seite oder auf lokaler Seite platziert werden. Es ist auch eine Kombination von Geräten auf beiden Seiten möglich. Da jede Option maßgebliche Vor- und Nachteile mit sich bringen kann, muss jede Option sorgfältig überdacht werden.

Beispiel: Einsatz vorhandener physischer Sicherheitsgeräte auf der Seite des lokalen Netzwerks. Pro: Es werden keine neuen Geräte benötigt, lediglich eine neue Konfiguration. Kontra: Der gesamte Datenverkehr muss von Azure zurück an das lokale Netzwerk geleitet werden, damit er die Sicherheitsgeräte passiert. Der Azure-zu-Azure-Datenverkehr könnte sich erheblich auf die Latenz und damit auf die Anwendungsleistung und das Benutzererlebnis auswirken, wenn der Datenverkehr zur Erzwingung von Sicherheitsrichtlinien zurück an das lokale Netzwerk geleitet werden muss.

#### 3) Wie werden die Grenzen implementiert?
Jede Sicherheitsgrenze hat andere Funktionsanforderungen (z. B. IDS- und Firewallregeln auf der Internetseite der DMZ, aber lediglich ACLs zwischen DMZ und Back-End-Subnetz). Die Entscheidung, welche Geräte zum Einsatz kommen, richtet sich nach dem Szenario und den Sicherheitsanforderungen. In den nachfolgend beschriebenen DMZ-Beispielen 1, 2 und 3 werden einige der Optionen erläutert, die zur Verfügung stehen. Die systemeigenen Netzwerkfeatures von Azure und die über das Partnerökosystem in Azure erhältlichen Geräte bieten unzählige Optionen, um praktisch jedes Szenario abzudecken.

Ein weiterer wichtiger Aspekt bei der Implementierung ist, auf welche Weise das lokale Netzwerk mit Azure verbunden wird: über das virtuelle Azure-Gateway oder über ein virtuelles Netzwerkgerät. Diese Optionen werden in den Beispielen 4, 5 und 6 ausführlicher erläutert.

Zusätzlich ist möglicherweise ein VNet-zu-VNet-Datenverkehr innerhalb von Azure erforderlich. Diese Szenarien werden in den Beispielen 7 und 8 näher beschrieben.

Sobald sämtliche der oben aufgeführten Fragen beantwortet wurden, können Sie mithilfe des Abschnitts [Schnelleinstieg](#fast-start) ermitteln, welche Beispiele für ein bestimmtes Szenario am besten geeignet sind.

## Einrichten von Sicherheitsgrenzen mit virtuellen Azure-Netzwerken
### Beispiel 1: Erstellen einer einfachen DMZ mit Netzwerksicherheitsgruppen
[Zurück zum Schnelleinstieg](#fast-start) | [Detaillierte Einrichtungsanweisungen zu diesem Beispiel][Example1]

![Eingehende DMZ mit NSG][7]

#### Beschreibung der Umgebung
Dieses Beispiel umfasst ein Abonnement, das Folgendes enthält:

- Zwei Clouddienste: "FrontEnd001" und "BackEnd001"
- Ein virtuelles Netzwerk, "CorpNetwork", mit zwei Subnetzen: "FrontEnd" und "BackEnd"
- Eine Netzwerksicherheitsgruppe, die auf beide Subnetze angewendet wird
- Eine Windows Server-Instanz, die einen Anwendungswebserver darstellt ("IIS01")
- Zwei Windows Server-Instanzen, die Back-End-Anwendungsserver darstellen ("AppVM01", "AppVM02")
- Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")

Weitere Details zum Erstellen dieses Beispiels (sowohl mithilfe von Skripts als auch mit einer ARM-Vorlage) finden Sie auf der Seite [Detaillierte Einrichtungsanweisungen][Example1].

#### Beschreibung der Netzwerksicherheitsgruppe (NSG)
In diesem Beispiel wird eine Netzwerksicherheitsgruppe erstellt und dann mit sechs Regeln geladen.

>[AZURE.TIP]Im Allgemeinen sollten Sie zuerst die spezifischeren Regeln zum Zulassen von Aktionen und danach die allgemeineren Regeln zum Ablehnen von Aktionen erstellen. Die zugewiesene Priorität bestimmt, welche Regeln zuerst ausgewertet werden. Sobald eine Regel auf den Datenverkehr zutrifft, werden keine weiteren Regeln ausgewertet. NSG-Regeln können sowohl in eingehender als auch in ausgehender Richtung zutreffen (aus Perspektive des Subnetzes).

Folgende Regeln werden deklarativ für eingehenden Datenverkehr erstellt:

1.	Interner DNS-Datenverkehr (Port 53) wird zugelassen.
2.	RDP-Datenverkehr (Port 3389) aus dem Internet zu jedem virtuellen Computer wird zugelassen.
3.	HTTP-Datenverkehr (Port 80) aus dem Internet zum Webserver (IIS01) wird zugelassen.
4.	Jeglicher Datenverkehr (alle Ports) von IIS01 zu AppVM1 wird zugelassen.
5.	Jeglicher Datenverkehr (alle Ports) aus dem Internet in das gesamte VNet (beide Subnetze) wird abgelehnt.
6.	Jeglicher Datenverkehr (alle Ports) vom Front-End-Subnetz zum Back-End-Subnetz wird abgelehnt.

Diese Regeln sind an jedes Subnetz gebunden. Wenn also eine HTTP-Anforderung aus dem Internet an den Webserver gesendet wird, gilt sowohl Regel 3 (Zulassen) als auch Regel 5 (Ablehnen). Da jedoch Regel 3 eine höhere Priorität besitzt, wird nur diese angewendet, und Regel 5 wird nicht berücksichtigt. Aus diesem Grund wird die HTTP-Anforderung für den Webserver als zulässig eingestuft. Falls versucht würde, denselben Datenverkehr an den DNS01-Server zu senden, würde Regel 5 (Ablehnen) zuerst angewendet, der Datenverkehr also nicht an den Server übergeben. Mit Regel 6 (Ablehnen) wird die Kommunikation des Front-End-Subnetzes mit dem Back-End-Subnetz blockiert (mit Ausnahme von zulässigem Datenverkehr gemäß Regeln 1 und 4). Dies dient dem Schutz des Back-End-Netzwerks, falls die Webanwendung im Front-End durch einen Angriff gefährdet würde. Der Angreifer hätte dann nur eingeschränkten Zugriff auf das "geschützte" Back-End-Netzwerk (nur auf Ressourcen, die auf dem Server AppVM01 verfügbar gemacht wurden).

Es gibt eine Standardregel für ausgehenden Datenverkehr, die das Senden von Datenverkehr an das Internet zulässt. In diesem Beispiel wird ausgehender Datenverkehr zugelassen, und es werden keine ausgehenden Regeln geändert. Um den Datenverkehr in beide Richtungen zu sperren, ist benutzerdefiniertes Routing (User Defined Routing, UDR) erforderlich. Dies wird in Beispiel 3 erläutert.

#### Zusammenfassung
Dies ist eine relativ einfache und direkte Möglichkeit, das Back-End-Subnetz von eingehendem Datenverkehr zu isolieren. Weitere Informationen zu diesem Beispiel, darunter etwa:

- Erstellen dieser DMZ mithilfe von PowerShell-Skripts
- Erstellen dieser DMZ mit einer ARM-Vorlage
- Ausführliche Beschreibungen für jeden NSG-Befehl
- Ausführliche Szenarien zum Datenverkehrsfluss, die zeigen, wie der Datenverkehr auf jeder Stufe zugelassen oder abgelehnt wird

finden Sie auf der Seite [Detaillierte Einrichtungsanweisungen][Example1].

### Beispiel 2: Erstellen einer DMZ zum Schutz von Anwendungen mit einer Firewall und NSGs
[Zurück zum Schnelleinstieg](#fast-start) | [Detaillierte Einrichtungsanweisungen zu diesem Beispiel][Example2]

![Eingehende DMZ mit NVA und NSG][8]

#### Beschreibung der Umgebung
Dieses Beispiel umfasst ein Abonnement, das Folgendes enthält:

- Zwei Clouddienste: "FrontEnd001" und "BackEnd001"
- Ein virtuelles Netzwerk, "CorpNetwork", mit zwei Subnetzen: "FrontEnd" und "BackEnd"
- Eine einzelne Netzwerksicherheitsgruppe, die auf beide Subnetze angewendet wird
- Ein virtuelles Netzwerkgerät, in diesem Beispiel eine Firewall, das mit dem Front-End-Subnetz verbunden ist
- Eine Windows Server-Instanz, die einen Anwendungswebserver darstellt ("IIS01")
- Zwei Windows Server-Instanzen, die Back-End-Anwendungsserver darstellen ("AppVM01", "AppVM02")
- Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")

Weitere Details zum Erstellen dieses Beispiels (sowohl mithilfe von Skripts als auch mit einer ARM-Vorlage) finden Sie auf der Seite [Detaillierte Einrichtungsanweisungen][Example2].

#### Beschreibung der Netzwerksicherheitsgruppe (NSG)
In diesem Beispiel wird eine Netzwerksicherheitsgruppe erstellt und dann mit sechs Regeln geladen.

>[AZURE.TIP]Im Allgemeinen sollten Sie zuerst die spezifischeren Regeln zum Zulassen von Aktionen und danach die allgemeineren Regeln zum Ablehnen von Aktionen erstellen. Die zugewiesene Priorität bestimmt, welche Regeln zuerst ausgewertet werden. Sobald eine Regel auf den Datenverkehr zutrifft, werden keine weiteren Regeln ausgewertet. NSG-Regeln können sowohl in eingehender als auch in ausgehender Richtung zutreffen (aus Perspektive des Subnetzes).

Folgende Regeln werden deklarativ für eingehenden Datenverkehr erstellt:

1.	Interner DNS-Datenverkehr (Port 53) wird zugelassen.
2.	RDP-Datenverkehr (Port 3389) aus dem Internet zu jedem virtuellen Computer wird zugelassen.
3.	Jeglicher Internetdatenverkehr (alle Ports) zum virtuellen Netzwerkgerät (Firewall) wird zugelassen.
4.	Jeglicher Datenverkehr (alle Ports) von IIS01 zu AppVM1 wird zugelassen.
5.	Jeglicher Datenverkehr (alle Ports) aus dem Internet in das gesamte VNet (beide Subnetze) wird abgelehnt.
6.	Jeglicher Datenverkehr (alle Ports) vom Front-End-Subnetz zum Back-End-Subnetz wird abgelehnt.

Diese Regeln sind an jedes Subnetz gebunden. Wenn also eine HTTP-Anforderung aus dem Internet an die Firewall gesendet wird, gilt sowohl Regel 3 (Zulassen) als auch Regel 5 (Ablehnen). Da jedoch Regel 3 eine höhere Priorität besitzt, wird nur diese angewendet, und Regel 5 wird nicht berücksichtigt. Aus diesem Grund würde die HTTP-Anforderung für die Firewall als zulässig eingestuft werden. Falls versucht würde, denselben Datenverkehr an den IIS01-Server zu senden, würde Regel 5 (Ablehnen) – obwohl der Server sich im Front-End-Subnetz befindet – zuerst angewendet, der Datenverkehr also nicht an den Server übergeben. Mit Regel 6 (Ablehnen) wird die Kommunikation des Front-End-Subnetzes mit dem Back-End-Subnetz blockiert (mit Ausnahme von zulässigem Datenverkehr gemäß Regeln 1 und 4). Dies dient dem Schutz des Back-End-Netzwerks, falls die Webanwendung im Front-End durch einen Angriff gefährdet würde. Der Angreifer hätte dann nur eingeschränkten Zugriff auf das "geschützte" Back-End-Netzwerk (nur auf Ressourcen, die auf dem Server AppVM01 verfügbar gemacht wurden).

Es gibt eine Standardregel für ausgehenden Datenverkehr, die das Senden von Datenverkehr an das Internet zulässt. In diesem Beispiel wird ausgehender Datenverkehr zugelassen, und es werden keine ausgehenden Regeln geändert. Um den Datenverkehr in beide Richtungen zu sperren, ist benutzerdefiniertes Routing (User Defined Routing, UDR) erforderlich. Dies wird in Beispiel 3 erläutert.

#### Beschreibung der Firewallregel
In der Firewall müssen Weiterleitungsregeln erstellt werden. Da in diesem Beispiel nur eingehender Internetdatenverkehr an die Firewall und dann an den Webserver geleitet wird, ist nur eine NAT-Weiterleitungsregel erforderlich.

Die Weiterleitungsregel akzeptiert alle eingehenden Quelladressen, die auf die Firewall treffen und für HTTP (Port 80 oder 443 für HTTPS) bestimmt sind. Diese Anforderungen werden an die lokale Schnittstelle der Firewall gesendet und anschließend an den Webserver mit der IP-Adresse 10.0.1.5 umgeleitet.

#### Zusammenfassung
Dies ist eine relativ einfache Möglichkeit zum Schützen Ihrer Anwendung mit einer Firewall und zum Isolieren des Back-End-Subnetzes vor eingehendem Datenverkehr. Weitere Informationen zu diesem Beispiel, darunter etwa:

- Erstellen dieser Beispiel-DMZ mithilfe von PowerShell-Skripts
- Erstellen dieses Beispiels mit einer ARM-Vorlage
- Ausführliche Beschreibungen für jeden NSG-Befehl und jede Firewallregel
- Ausführliche Szenarien zum Datenverkehrsfluss, die zeigen, wie der Datenverkehr auf jeder Stufe zugelassen oder abgelehnt wird

finden Sie auf der Seite [Detaillierte Einrichtungsanweisungen][Example2].

### Beispiel 3: Erstellen einer DMZ zum Schutz von Netzwerken mit Firewall, UDR und NSG
[Zurück zum Schnelleinstieg](#fast-start) | [Detaillierte Einrichtungsanweisungen zu diesem Beispiel][Example3]

![Bidirektionale DMZ mit virtuellem Netzwerkgerät, Netzwerksicherheitsgruppe und benutzerdefiniertem Routing][9]

#### Einrichten der Umgebung
Dieses Beispiel umfasst ein Abonnement, das Folgendes enthält:

- Drei Clouddienste: "SecSvc001", "FrontEnd001" und "BackEnd001"
- Ein virtuelles Netzwerk, "CorpNetwork", mit drei Subnetzen: "SecNet", "FrontEnd" und "BackEnd"
- Ein virtuelles Netzwerkgerät, in diesem Beispiel eine Firewall, das mit dem Subnetz "SecNet" verbunden ist
- Eine Windows Server-Instanz, die einen Anwendungswebserver darstellt ("IIS01")
- Zwei Windows Server-Instanzen, die Back-End-Anwendungsserver darstellen ("AppVM01", "AppVM02")
- Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")

Weitere Details zum Erstellen dieses Beispiels (sowohl mithilfe von Skripts als auch mit einer ARM-Vorlage) finden Sie auf der Seite [Detaillierte Einrichtungsanweisungen][Example3].

#### Beschreibung des benutzerdefinierten Routings (UDR)
Standardmäßig sind folgende Systemrouten definiert:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

"VNETLocal" ist immer das definierte Adresspräfix (bzw. die definierten Adresspräfixe) des VNets für dieses spezifische Netzwerk (d. h. diese Angabe ändert sich je nach VNet und je nachdem, wie jedes VNet definiert ist). Die anderen Systemrouten sind statisch und standardmäßig wie oben angegeben.

In diesem Beispiel werden zwei Routingtabellen erstellt, jeweils eine für das Front-End- und das Back-End-Subnetz. Jede Tabelle enthält statische Routen, die sich für das jeweilige Subnetz eignen. Im Rahmen dieses Beispiels enthält jede Tabelle drei Routen, die den gesamten Datenverkehr (0.0.0.0/0) über die Firewall (nächster Hop = IP-Adresse des virtuellen Geräts) leiten:

1. Datenverkehr im lokalen Subnetz ohne definierten nächsten Hop, sodass der Datenverkehr im lokalen Subnetz die Firewall umgehen kann.
2. Virtueller Netzwerkdatenverkehr mit einem als Firewall definierten nächsten Hop. Dies setzt die Standardregel außer Kraft, die ein direktes Routing des lokalen VNet-Datenverkehrs ermöglicht.
3. Der gesamte verbleibende Datenverkehr (0/0) mit einem als Firewall definierten nächsten Hop.

Sobald die Routingtabellen erstellt wurden, sind sie an ihre Subnetze gebunden. Die Routingtabelle für das Front-End-Subnetz sieht nach Erstellung und Bindung an das Subnetz wie folgt aus:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE]Derzeit gelten für UDR und Hybridnetzwerke Einschränkungen. Dies wird in einer künftigen Version gelöst. Beispiele zum Konfigurieren Ihrer DMZ mit ExpressRoute- oder Site-to-Site-Netzwerkverbindungen werden in den Beispielen 3 und 4 erläutert.

#### Beschreibung der IP-Weiterleitung
Die IP-Weiterleitung ist eine begleitende Funktion für das benutzerdefinierte Routing (User Defined Routing, UDR). Es handelt sich um eine Einstellung in einem virtuellen Gerät, das diesem ermöglicht, nicht speziell an dieses Gerät adressierten Datenverkehr zu empfangen und diesen an das endgültige Ziel weiterzuleiten.

Ein Beispiel: Wenn Datenverkehr aus AppVM01 eine Anforderung an den Server DNS01 sendet, leitet UDR diese Anforderung an die Firewall weiter. Wenn die IP-Weiterleitung aktiviert ist, wird Datenverkehr für das Ziel DNS01 (10.0.2.4) vom Gerät (10.0.0.4) akzeptiert und dann an das endgültige Ziel (10.0.2.4) weitergeleitet. Ist die IP-Weiterleitung in der Firewall nicht aktiviert, wird der Datenverkehr vom Gerät nicht akzeptiert, selbst wenn in der Routingtabelle die Firewall als nächster Hop angegeben ist. Um ein virtuelles Gerät verwenden zu können ist es wichtig, die IP-Weiterleitung zusammen mit dem benutzerdefinierten Routing zu aktivieren.

#### Beschreibung der Netzwerksicherheitsgruppe (NSG)
In diesem Beispiel wird eine Netzwerksicherheitsgruppe erstellt und dann mit einer einzigen Regel geladen. Diese Gruppe wird dann nur an die Front-End- und Back-End-Subnetze (nicht an SecNet) gebunden. Die folgende Regel wird deklarativ erstellt:

1.	Jeglicher Datenverkehr (alle Ports) aus dem Internet an das gesamte VNet (alle Subnetze) wird abgelehnt.

Obwohl Netzwerksicherheitsgruppen in diesem Beispiel verwendet werden, dienen sie hauptsächlich als zweite Stufe zum Schutz gegen manuelle Fehlkonfigurationen. Es soll der gesamte eingehende Datenverkehr aus dem Internet an das Front-End- oder das Back-End-Subnetz blockiert werden. Der Datenverkehr soll nur durch das SecNet-Subnetz an die Firewall weitergeleitet werden (und danach ggf. an die Front-End- und Back-End-Subnetze). Darüber hinaus soll – wenn die UDR-Regeln eingerichtet sind – jeglicher Datenverkehr, der an die Front-End- oder Back-End-Subnetze weitergeleitet wurde, nach außen an die Firewall geleitet werden (dank UDR). Die Firewall erkennt diesen Datenverkehr als asymmetrisch und verwirft den ausgehenden Datenverkehr. Es gibt daher drei Sicherheitsebenen, die das Front-End- und das Back-End-Subnetz schützen: 1. keine offenen Endpunkte in den Clouddiensten FrontEnd001 und BackEnd001. 2. Netzwerksicherheitsgruppen lehnen Datenverkehr aus dem Internet ab. 3. Die Firewall verwirft asymmetrischen Datenverkehr.

Ein interessanter Aspekt in Bezug auf die Netzwerksicherheitsgruppe in diesem Beispiel ist, dass sie nur eine Regel enthält, mit der Internetdatenverkehr im gesamten virtuellen Netzwerk abgelehnt wird, einschließlich des Sicherheitssubnetzes. Da jedoch die Netzwerksicherheitsgruppe nur an die Front-End- und Back-End-Subnetze gebunden ist, wird die Regel bei eingehendem Datenverkehr an das Sicherheitssubnetz nicht angewendet. Obwohl also die Netzwerksicherheitsgruppe festlegt, dass kein Internetdatenverkehr an eine beliebige Adresse im VNet übertragen werden darf, wird der Datenverkehr dennoch an das Sicherheitssubnetz weitergeleitet, da die Netzwerksicherheitsgruppe nicht an das Sicherheitssubnetz gebunden wurde.

#### Firewallregeln
In der Firewall müssen Weiterleitungsregeln erstellt werden. Da die Firewall sämtlichen eingehenden und ausgehenden Datenverkehr sowie den Datenverkehr innerhalb des VNets blockiert oder weiterleitet, müssen zahlreiche Firewallregeln festgelegt werden. Darüber hinaus wird jeglicher eingehender Datenverkehr über die öffentliche IP-Adresse des Sicherheitsdiensts geleitet (über unterschiedliche Ports) und muss von der Firewall verarbeitet werden. Eine bewährte Methode besteht darin, die logischen Datenflüsse in Diagrammen abzubilden, bevor Subnetze und Firewallregeln eingerichtet werden, um spätere Überarbeitungen zu vermeiden. Die folgende Abbildung zeigt eine logische Ansicht der Firewallregeln für dieses Beispiel:
 
![Logische Ansicht der Firewallregeln][10]

>[AZURE.NOTE]Je nach verwendetem virtuellem Netzwerkgerät variieren die Verwaltungsports. In diesem Beispiel wird auf eine Barracuda NG Firewall verwiesen, die die Ports 22, 801 und 807 verwendet. Informationen dazu, über welche Ports das verwendete Gerät genau verwaltet wird, finden Sie in der Dokumentation des Geräteanbieters.

#### Beschreibung der Firewallregeln
Im oben gezeigten logischen Diagramm wird das Sicherheitssubnetz nicht angezeigt, da die Firewall die einzige Ressource in diesem Subnetz ist. Dieses Diagramm zeigt die Firewallregeln und die Art und Weise, wie diese Datenverkehrsflüsse logisch zulassen oder ablehnen. Das Diagramm zeigt nicht den tatsächlichen Weiterleitungspfad. Die für den RDP-Datenverkehr ausgewählten externen Ports liegen in einem höheren Bereich (8014-8026) und wurden zur Anpassung an die letzten beiden Oktette der lokalen IP-Adresse ausgewählt, um die Lesbarkeit zu vereinfachen (die lokale Serveradresse 10.0.1.4 ist beispielsweise Port 8014 zugeordnet). Es können jedoch auch Ports mit einer höheren Nummer ausgewählt werden, wenn sie keine Konflikte verursachen.

In diesem Beispiel werden 7 Arten von Regeln benötigt, die wie folgt beschrieben werden:

- Externe Regeln (für eingehenden Datenverkehr):
  1.	Firewallverwaltungsregel: Diese Regel zur Anwendungsumleitung ermöglicht die Weiterleitung von Datenverkehr an die Verwaltungsports des virtuellen Netzwerkgeräts.
  2.	RDP-Regeln (für jeden Windows-Server): Diese vier Regeln (eine für jeden Server) ermöglichen die Verwaltung der einzelnen Server über RDP. Diese können je nach Funktionsumfang des verwendeten virtuellen Netzwerkgeräts in eine Regel gebündelt werden.
  3.	Regeln für den Anwendungsdatenverkehr: Es gibt zwei Regeln für den Anwendungsdatenverkehr, eine für den Datenverkehr im Front-End, eine für den Datenverkehr im Back-End (z. B. vom Webserver zur Datenebene). Die Konfiguration dieser Regeln richtet sich nach der Netzwerkarchitektur (der Platzierung Ihrer Server) und dem Datenverkehrsfluss (Richtung des Datenverkehrs und verwendete Ports).
      - Mit der ersten Regel gelangt der tatsächliche Anwendungsdatenverkehr zum Anwendungsserver. Während die anderen Regeln für Sicherheit, Verwaltung usw. sorgen, ermöglichen Anwendungsregeln es Benutzern oder Diensten, auf die Anwendung(en) zuzugreifen. In diesem Beispiel befindet sich ein einziger Webserver an Port 80. Daher leitet eine einzige Firewallanwendungsregel den eingehenden Datenverkehr an die externe IP-Adresse, also die interne IP-Adresse der Webserver um. Die umgeleitete Datenverkehrssitzung wird per Netzwerkadressübersetzung (Network Address Translation, NAT) an den internen Server weitergeleitet.
      - Bei der zweiten Regel für Anwendungsdatenverkehr handelt es sich um die Back-End-Regel, dank derer der Webserver über jeden Port mit dem Server AppVM01 (nicht jedoch mit dem Server AppVM02) kommunizieren kann.
- Interne Regeln (für VNet-internen Datenverkehr)
  4.	Regel für ausgehenden Datenverkehr zum Internet: Mit dieser Regel kann Datenverkehr aus allen Netzwerken in die ausgewählten Netzwerke übergeben werden. Diese Regel ist üblicherweise standardmäßig in der Firewall vorhanden, jedoch deaktiviert. Diese Regel sollte für dieses Beispiel aktiviert werden.
  5.	DNS-Regel: Mit dieser Regel kann nur DNS-Datenverkehr (Port 53) an den DNS-Server übergeben werden. Für diese Umgebung wird der größte Teil des Datenverkehrs aus dem Front-End an das Back-End blockiert. Diese Regel lässt DNS-Datenverkehr aus allen lokalen Subnetzen explizit zu.
  6.	Subnetz-zu-Subnetz-Regel: Mit dieser Regel kann jeder Server im Back-End-Subnetz eine Verbindung mit jedem Server im Front-End-Subnetz herstellen (nicht jedoch umgekehrt).
- Ausfallsicherheitsregel (für Datenverkehr, für den keine der obigen Regeln gilt):
  7.	Regel zum Ablehnen jeglichen Datenverkehrs: Dies sollte (hinsichtlich der Priorität) die letzte Regel sein. Sollte ein Datenverkehrsfluss keiner der vorherigen Regeln entsprechen, wird er durch diese Regel verworfen. Dies ist eine Standardregel, die üblicherweise aktiviert ist. Im Allgemeinen sind keine Änderungen erforderlich.

>[AZURE.TIP]In Bezug auf die zweite Anwendungsdatenverkehrsregel ist in diesem Beispiel einfachheitshalber jeder Port zulässig. In einem realen Szenario sollten die spezifischsten Port- und Adressbereiche verwendet werden, um die Angriffsfläche dieser Regel zu reduzieren.

Nachdem alle oben genannten Regeln erstellt wurden, ist es wichtig, die Priorität jeder einzelnen Regel zu prüfen, um sicherzustellen, dass der Datenverkehr wie gewünscht zugelassen oder abgelehnt wird. In diesem Beispiel wurden die Regeln nach Priorität geordnet.

#### Zusammenfassung
Dies ist eine komplexere, aber auch umfassendere Möglichkeit zum Schützen und Isolieren des Netzwerks (In Beispiel 2 wurde lediglich die Anwendung geschützt, in Beispiel 1 wurden nur die Subnetze isoliert). Dieser Entwurf ermöglicht eine Überwachung des Datenverkehrs in beide Richtungen, nicht lediglich des eingehenden Anwendungsservers. Es werden Netzwerksicherheitsrichtlinien für alle Server im Netzwerk erzwungen. Je nach verwendeten Geräten kann außerdem der gesamte Datenverkehr überwacht werden, um vollständigen Einblick zu erhalten. Weitere Informationen zu diesem Beispiel, darunter etwa:

- Erstellen dieser Beispiel-DMZ mithilfe von PowerShell-Skripts
- Erstellen dieses Beispiels mit einer ARM-Vorlage
- Ausführliche Beschreibungen für jeden UDR- und NSG-Befehl und jede Firewallregel
- Ausführliche Szenarien zum Datenverkehrsfluss, die zeigen, wie der Datenverkehr auf jeder Stufe zugelassen oder abgelehnt wird

finden Sie auf der Seite [Detaillierte Einrichtungsanweisungen][Example3].

### Beispiel 4: Hinzufügen einer Hybridverbindung mit einem Site-to-Site-VPN über ein virtuelles Gerät
[Zurück zum Schnelleinstieg](#fast-start) | Detaillierte Einrichtungsanweisungen zu diesem Beispiel stehen in Kürze zur Verfügung

![DMZ mit einem über ein virtuelles Netzwerkgerät verbundenen Hybridnetzwerk][11]

#### Einrichten der Umgebung
Ein Hybridnetzwerk unter Verwendung eines virtuellen Netzwerkgeräts kann einem beliebigen der DMZ-Typen hinzugefügt werden, die in den Beispielen 1, 2 oder 3 beschrieben werden.

Wie in der Abbildung oben dargestellt, wird eine VPN-Verbindung über das Internet (Site-to-Site) dazu verwendet, ein lokales Netzwerk über ein virtuelles Netzwerkgerät mit einem virtuellen Azure-Netzwerk zu verbinden.

>[AZURE.NOTE]Wenn Sie ExpressRoute mit aktivierter Azure Public Peering-Option verwenden, muss eine statische Route zum Routing über die VPN-IP-Adresse des virtuellen Netzwerkgeräts erstellt werden, damit ausgehender Datenverkehr über die Unternehmensgrenze zum Internet und nicht über die ExpressRoute-WAN-Grenze fließt. Dies ist nötig, da für die ExpressRoute Azure Public Peering-Option eine Netzwerkadressübersetzung (NAT) erforderlich ist, die höchstwahrscheinlich zu einer Unterbrechung der VPN-Sitzung führt (IPSec verträgt sich in der Regel nicht mit NAT).

Nachdem das VPN eingerichtet ist, wird das virtuelle Netzwerkgerät zum zentralen "Hub" für alle Netzwerke und Subnetze. Die Weiterleitungsregeln der Firewall legen fest, welcher Datenverkehr zulässig ist, wann eine Netzwerkadressübersetzung durchgeführt wird, welche Daten umgeleitet oder verworfen werden (selbst für Datenströme zwischen dem lokalen Netzwerk und Azure, wenn die Datenflüsse so eingerichtet sind).

Der Fluss des Datenverkehrs muss sorgfältig geplant werden, da er durch dieses Entwurfsmuster je nach Anwendungsfall optimiert oder beeinträchtigt werden kann.

Eine Umgebung, die auf Beispiel 3 basiert, "Erstellen einer DMZ zum Schutz von Netzwerken mit Firewall, UDR und NSG", und der anschließend eine Site-to-Site-VPN-Hybridnetzwerkverbindung hinzugefügt wird, führt zu dem folgenden Entwurf:

![DMZ mit einem über ein Site-to-Site-VPN verbundenen virtuellen Netzwerkgerät][12]

Im oben gezeigten Entwurf fungiert der lokale Router – oder ein beliebiges anderes Netzwerkgerät, das mit Ihrem virtuellen Netzwerkgerät für das VPN kompatibel ist – als VPN-Client. Dieses physische Gerät ist für die Initialisierung und die Aufrechterhaltung der VPN-Verbindung mit Ihrem virtuellen Netzwerkgerät verantwortlich.

Aus logischer Sicht des virtuellen Netzwerkgeräts sind die Netzwerke vier separate "Sicherheitszonen", wobei die Regeln auf dem virtuellen Netzwerkgerät festlegen, wie der Datenverkehr zwischen diesen Zonen abgewickelt wird. Der logische Aufbau sieht so aus:

![Logisches Netzwerk aus NVA-Perspektive][13]

#### Zusammenfassung
Durch das Hinzufügen einer Site-to-Site-VPN-Hybridnetzwerkverbindung zu einem virtuellen Azure-Netzwerks kann das lokale Netzwerk auf sichere Weise auf Azure erweitert werden. Durch die Verwendung einer VPN-Verbindung wird Ihr Datenverkehr verschlüsselt und über das Internet geroutet. Das virtuelle Netzwerkgerät in diesem Beispiel bietet eine zentrale Möglichkeit zum Erzwingen und Verwalten der Sicherheitsrichtlinie. Weitere Informationen zu diesem Beispiel, darunter etwa:

- Erstellen dieser Beispiel-DMZ mithilfe von PowerShell-Skripts
- Erstellen dieses Beispiels mit einer ARM-Vorlage
- Detaillierte Szenarien zum Datenfluss in diesem Entwurf

stehen in Kürze zur Verfügung und werden über diese Seite verlinkt.

### Beispiel 5: Hinzufügen einer Hybridverbindung mit einem Site-to-Site-VPN über ein Azure-Gateway
[Zurück zum Schnelleinstieg](#fast-start) | Detaillierte Einrichtungsanweisungen zu diesem Beispiel stehen in Kürze zur Verfügung

![DMZ mit einem über ein Gateway verbundenen Hybridnetzwerk][14]

#### Einrichten der Umgebung
Eine Hybridnetzwerkverbindung über ein Azure-VPN-Gateway kann einem beliebigen der DMZ-Typen hinzugefügt werden, die in den Beispielen 1 und 2 beschrieben werden.

Wie in der Abbildung oben dargestellt, wird eine VPN-Verbindung über das Internet (Site-to-Site) dazu verwendet, ein lokales Netzwerk über ein Azure-VPN-Gateway mit einem virtuellen Azure-Netzwerk zu verbinden.

>[AZURE.NOTE]Wenn Sie ExpressRoute mit aktivierter Azure Public Peering-Option verwenden, muss eine statische Route zum Routing über die IP-Adresse des Azure-VPN-Gateways erstellt werden, damit ausgehender Datenverkehr über die Unternehmensgrenze zum Internet und nicht über die ExpressRoute-WAN-Grenze fließt. Dies ist nötig, da für die ExpressRoute Azure Public Peering-Option eine Netzwerkadressübersetzung (NAT) erforderlich ist, die höchstwahrscheinlich zu einer Unterbrechung der VPN-Sitzung führt (IPSec verträgt sich in der Regel nicht mit NAT).

Wie unten gezeigt, weist die Umgebung mit dieser Option zwei Netzwerkgrenzen auf. An der ersten Netzwerkgrenze steuern das virtuelle Netzwerkgerät und Netzwerksicherheitsgruppen (NSGs) den Fluss des Datenverkehrs für interne Azure-Netzwerke und zwischen Azure und dem Internet. Das Azure-VPN-Gateway bildet die zweite Netzwerkgrenze für eine vollständige Trennung und Isolierung zwischen dem lokalen Netzwerk und Azure.

Der Fluss des Datenverkehrs muss sorgfältig geplant werden, da er durch dieses Entwurfsmuster je nach Anwendungsfall optimiert oder beeinträchtigt werden kann.

Eine Umgebung, die auf Beispiel 1 basiert, "Erstellen einer einfachen DMZ mit Netzwerksicherheitsgruppen", und der anschließend eine Site-to-Site-VPN-Hybridnetzwerkverbindung hinzugefügt wird, führt zu dem folgenden Entwurf:

![DMZ mit einem über eine ExpressRoute-Verbindung verbundenen Gateway][15]

#### Zusammenfassung
Durch das Hinzufügen einer Site-to-Site-VPN-Hybridnetzwerkverbindung zu einem virtuellen Azure-Netzwerks kann das lokale Netzwerk auf sichere Weise auf Azure erweitert werden. Durch Verwendung des systemeigenen Azure-VPN-Gateways wird Ihr Datenverkehr mit IPSec verschlüsselt und über das Internet geroutet. Durch Einsatz des Azure-VPN-Gateways können außerdem die Kosten gesenkt werden (da im Gegensatz zu virtuellen Netzwerkgeräten von Drittanbietern keine zusätzlichen Lizenzierungskosten anfallen). Besonders wirtschaftlich ist dies in Beispiel 1, in dem kein virtuelles Netzwerkgerät verwendet wird. Weitere Informationen zu diesem Beispiel, darunter etwa:

- Erstellen dieser Beispiel-DMZ mithilfe von PowerShell-Skripts
- Erstellen dieses Beispiels mit einer ARM-Vorlage
- Detaillierte Szenarien zum Datenfluss in diesem Entwurf

stehen in Kürze zur Verfügung und werden über diese Seite verlinkt.

### Beispiel 6: Hinzufügen einer Hybridverbindung mit ExpressRoute
[Zurück zum Schnelleinstieg](#fast-start) | Detaillierte Einrichtungsanweisungen zu diesem Beispiel stehen in Kürze zur Verfügung

![DMZ mit einem über ein Gateway verbundenen Hybridnetzwerk][16]

#### Einrichten der Umgebung
Ein Hybridnetzwerk mit einer privaten ExpressRoute-Peeringverbindung kann einem beliebigen der DMZ-Typen hinzugefügt werden, die in Beispiel 1 oder 2 beschrieben werden.

Wie in der Abbildung oben gezeigt wird, bietet das private ExpressRoute-Peering eine direkte Verbindung zwischen Ihrem lokalen Netzwerk und dem virtuellen Azure-Netzwerk. Der Datenverkehr passiert nur das Dienstanbieternetzwerk und das Microsoft-/Azure-Netzwerk, ohne jeglichen Berührungspunkt mit dem Internet.

>[AZURE.NOTE]Aufgrund der Komplexität des dynamischen Routings im Azure-Gateway gelten für das benutzerdefinierte Routing (User Defined Routing, UDR) und ExpressRoute Einschränkungen. Auf Subnetze, die mit dem Azure-Gateway kommunizieren, das die ExpressRoute-Verbindung bereitstellt, sollte kein UDR angewendet werden. Darüber hinaus kann das Azure-Gateway nicht als NextHop-Gerät für andere UDR-gebundene Subnetze fungieren. Die Möglichkeit zur vollständigen Integration von UDR und ExpressRoute ist für eine spätere Azure-Version geplant.

</br>
>[AZURE.TIP]Durch den Einsatz von ExpressRoute wird der Unternehmensnetzwerkverkehr vollständig vom Internet getrennt. Diese Implementierung bietet höhere Sicherheit, eine erheblich gesteigerte Leistung und ermöglicht das Bereitstellen von SLAs durch Ihren ExpressRoute-Anbieter. Mit ExpressRoute kann das Azure-Gateway eine Leistung von bis zu 2 GBit/s erzielen, während das Azure-Gateway bei Site-to-Site-VPNs einen maximalen Durchsatz von 200 MBit/s erreicht.

Wie aus dem nachstehenden Diagramm hervorgeht, verfügt die Umgebung bei dieser Option über zwei Netzwerkgrenzen. Das virtuelle Netzwerkgerät und Netzwerksicherheitsgruppen (NSGs) steuern den Fluss des Datenverkehrs für interne Azure-Netzwerke und zwischen Azure und dem Internet, während das Gateway eine zweite Netzwerkgrenze bildet und für eine vollständige Trennung und Isolierung zwischen dem lokalen Netzwerk und Azure sorgt.

Der Fluss des Datenverkehrs muss sorgfältig geplant werden, da er durch dieses Entwurfsmuster je nach Anwendungsfall optimiert oder beeinträchtigt werden kann.

Eine Umgebung, die auf Beispiel 1 basiert, "Erstellen einer einfachen DMZ mit Netzwerksicherheitsgruppen", und der anschließend eine ExpressRoute-Hybridnetzwerkverbindung hinzugefügt wird, führt zu dem folgenden Entwurf:

![DMZ mit einem über eine ExpressRoute-Verbindung verbundenen Gateway][17]

#### Zusammenfassung
Durch das Hinzufügen einer privaten ExpressRoute-Peering-Netzwerkverbindung kann das lokale Netzwerk auf sichere Weise auf Azure erweitert werden und bietet gleichzeitig niedrige Latenz und hohe Leistung. Durch Einsatz des systemeigenen Azure-Gateways können außerdem die Kosten gesenkt werden (da im Gegensatz zu virtuellen Netzwerkgeräten von Drittanbietern keine zusätzlichen Lizenzierungskosten anfallen). Weitere Informationen zu diesem Beispiel, darunter etwa:

- Erstellen dieser Beispiel-DMZ mithilfe von PowerShell-Skripts
- Erstellen dieses Beispiels mit einer ARM-Vorlage
- Detaillierte Szenarien zum Datenfluss in diesem Entwurf

stehen in Kürze zur Verfügung und werden über diese Seite verlinkt.

## Referenzen
### Hilfreiche Websites und Dokumentation
- Zugriff auf Azure mit ARM: 
- Zugriff auf Azure mit PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Dokumentation zu virtuellen Netzwerken: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Dokumentation zu Netzwerksicherheitsgruppen: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Dokumentation zum benutzerdefinierten Routing (UDR): [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Virtuelle Azure-Gateways: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- Site-to-Site-VPNs: [https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/](./virtual-network/vpn-gateway-site-to-site-create.md)
- ExpressRoute-Dokumentation (lesen Sie unbedingt die Abschnitte "Erste Schritte" und "Vorgehensweise"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Flussdiagramm zu den Sicherheitsoptionen"
[1]: ./media/best-practices-network-security/compliancebadges.png "Azure-Compliancezertifizierungen"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Sicherheitsfeatures von Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Eine DMZ in einem Unternehmensnetzwerk"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Sicherheitsarchitektur von Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Ein DMZ in einem virtuellen Azure-Netzwerk"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Hybridnetzwerk mit drei Sicherheitsgrenzen"
[7]: ./media/best-practices-network-security/example1design.png "Eingehende DMZ mit NSG"
[8]: ./media/best-practices-network-security/example2design.png "Eingehende DMZ mit NVA und NSG"
[9]: ./media/best-practices-network-security/example3design.png "Bidirektionale DMZ mit virtuellem Netzwerkgerät, Netzwerksicherheitsgruppe und benutzerdefiniertem Routing"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Logische Ansicht der Firewallregeln"
[11]: ./media/best-practices-network-security/example4designoptions.png "DMZ mit einem über ein virtuelles Netzwerkgerät verbundenen Hybridnetzwerk"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ mit einem über ein Site-to-Site-VPN verbundenen virtuellen Netzwerkgerät"
[13]: ./media/best-practices-network-security/example4networklogical.png "Logisches Netzwerk aus NVA-Perspektive"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ mit einem über ein Site-to-Site-Hybridnetzwerk verbundenen Azure-Gateway"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ mit Azure-Gateway und Verwendung eines Site-to-Site-VPN"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ mit einem über ExpressRoute verbundenen Azure-Gateway"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ mit Azure-Gateway und Verwendung einer ExpressRoute-Verbindung"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

<!---HONumber=Sept15_HO4-->