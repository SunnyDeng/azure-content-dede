<properties
	pageTitle="Entwerfen Ihrer Netzwerkinfrastruktur für die Notfallwiederherstellung | Microsoft Azure"
	description="In diesem Artikel werden die Aspekte des Netzwerkentwurfs für Azure Site Recovery beschrieben."
	services="site-recovery"
	documentationCenter=""
	authors="prateek9us"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/22/2016"
	ms.author="pratshar"/>

#  Entwerfen Ihrer Netzwerkinfrastruktur für die Notfallwiederherstellung

Dieser Artikel richtet sich an IT-Experten, die für die Erstellung der Architektur, Implementierung und Unterstützung der Infrastruktur für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) verantwortlich sind und Microsoft Azure Site Recovery (ASR) einsetzen möchten, um ihre BCDR-Dienste zu unterstützen und zu verbessern. In diesem Artikel werden Praxisaspekte für die System Center Virtual Machine Manager-Serverbereitstellung, die Vor- und Nachteile von gestreckten Subnetzen im Vergleich mit dem Subnetzfailover und der Aufbau einer Notfallwiederherstellung für virtuelle Websites unter Microsoft Azure beschrieben.

## Übersicht

[Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) ist ein Microsoft Azure-Dienst, mit dem der Schutz und die Wiederherstellung Ihrer virtualisierten Anwendungen für die Geschäftskontinuität und Notfallwiederherstellung orchestriert werden. Mit diesem Dokument sollen Leser durch den Prozess zum Entwerfen von Netzwerken geführt werden. Der Schwerpunkt liegt hierbei auf der Einrichtung von IP-Bereichen und Subnetzen am Standort der Notfallwiederherstellung, wenn virtuelle Computer (VMs) per Site Recovery repliziert werden.

Außerdem wird in diesem Artikel veranschaulicht, wie Site Recovery das Entwerfen und Implementieren eines virtuellen Rechenzentrums mit mehreren Standorten ermöglicht, damit BCDR-Dienste für Tests oder im Notfall unterstützt werden.

In einer Welt, in der die Verbindung rund um die Uhr vorhanden sein muss, ist es wichtiger als jemals zuvor, dass Ihre Infrastruktur und Anwendungen immer betriebsbereit sind. Der Zweck der Geschäftskontinuität und Notfallwiederherstellung besteht darin, ausgefallene Komponenten wiederherzustellen, damit die Organisation schnell wieder den normalen Betrieb aufnehmen kann. Die Entwicklung von Strategien für die Notfallwiederherstellung bei wenig wahrscheinlichen, desaströsen Vorfällen ist sehr anspruchsvoll. Dies liegt an der damit verbundenen Schwierigkeit, die Zukunft vorherzusagen. Es hängt vor allem mit den unwahrscheinlichen Vorfällen und den hohen Kosten zusammen, die für die Bereitstellung geeigneter Maßnahmen zum Schutz vor weit reichenden Katastrophen anfallen.

Für die BCDR-Planung ist es von entscheidender Bedeutung, dass „Recovery Time Objective“ (RTO) und „Recovery Point Objective“ (RPO) im Rahmen eines Plans für die Notfallwiederherstellung definiert werden. Wenn das Rechenzentrum des Kunden von einem Notfall betroffen ist, können Kundenmitarbeiter mit Azure Site Recovery ihre replizierten virtuellen Computer, die sich entweder im sekundären Rechenzentrum oder unter Microsoft Azure befinden, schnell (niedriger RTO-Wert) und mit minimalem Datenverlust (niedriger RPO-Wert) online schalten.

Das Failover wird durch ASR ermöglicht. Zuerst werden die angegebenen virtuellen Computer aus dem primären Rechenzentrum in das sekundäre Rechenzentrum oder in Azure (je nach Szenario) kopiert, und anschließend werden die Replikate regelmäßig aktualisiert. Während der Infrastrukturplanung sollte der Netzwerkentwurf als potenzieller Engpass angesehen werden, der verhindern kann, dass Sie Ihre Unternehmensziele in Bezug auf die Werte für RTO und RPO erreichen.

Wenn Administratoren die Bereitstellung einer Lösung für die Notfallwiederherstellung planen, ist eine der wichtigsten Fragen, wie der virtuelle Computer nach Abschluss des Failovers erreichbar ist. Indem Administratoren die Netzwerkzuordnung in ASR verwenden, können sie auswählen, welchem Netzwerk der virtuelle Computer nach dem Failover zugeordnet wird. Weitere Informationen zur Netzwerkzuordnung finden Sie unter [Vorbereiten der Netzwerkzuordnung](site-recovery-network-mapping.md).

Beim Entwerfen des Netzwerks für den Wiederherstellungsstandort haben Administratoren zwei Möglichkeiten:

- Verwenden eines anderen IP-Adressbereichs für das Netzwerk am Wiederherstellungsstandort. Bei diesem Szenario erhält der virtuelle Computer nach dem Failover eine neue IP-Adresse, und der Administrator muss ein DNS-Update durchführen. Wie Sie das DNS-Update durchführen, erfahren Sie hier:
- Verwenden des gleichen IP-Adressbereichs für das Netzwerk am Wiederherstellungsstandort. Bei bestimmten Szenarien ziehen es Administratoren vor, die IP-Adressen beizubehalten, die sie am primären Standort verwenden, und zwar auch nach dem Failover. Bei einem normalen Szenario muss ein Administrator die Routen aktualisieren, um den neuen Standort der IP-Adressen anzugeben. Aber bei diesem Szenario, bei dem ein gestrecktes VLAN zwischen dem primären und sekundären Standort bereitgestellt wird, ist das Beibehalten der IP-Adressen für die virtuellen Computer eine attraktive Option. Das Beibehalten der gleichen IP-Adressen vereinfacht den Wiederherstellungsprozess, da alle netzwerkbezogenen Schritte nach dem Failover entfallen.

In diesem Dokument werden zwei wichtige technische Aspekte zum Entwerfen Ihres Netzwerkadressbereichs für die Notfallwiederherstellung beschrieben:

- Entwerfen der Bereitstellungstopologie für den Fabric-Manager (System Center Virtual Machine Manager), um Workloads per Azure Site Recovery (ASR) zu schützen.
- IP-Adressverwaltung in einer Umgebung für die Notfallwiederherstellung.


## Entwerfen der System Center VMM-Bereitstellung für Azure Site Recovery

Topologieentscheidungen werden durch geschäftliche Anforderungen vorgegeben. Basierend auf diesen geschäftlichen Anforderungen kann sich eine Organisation für mehrere Rechenzentren entscheiden, die von einem einzelnen Verwaltungsleiter gemanagt werden, oder für mehrere Leiter, die unterschiedliche Rechenzentren managen. In diesem Abschnitt wird beschrieben, wie eine Organisation eine SCVMM-Bereitstellungstopologie (System Center Virtual Machine Manager) entwirft, um Workloads mit ASR zu schützen.

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden. Basierend auf dem Entwurfsprinzip der Einfachheit ist ASR so konzipiert, dass Administratoren eine Lösung für die Notfallwiederherstellung zusätzlich zu den vorhanden Rechenzentrumtopologien erstellen können. Bei ASR können Organisationen also eine Topologie wählen, mit der die geschäftlichen Anforderungen am besten erfüllt werden.

Eine vollständige Liste der Bereitstellungsszenarien finden Sie unter [Was ist Site Recovery?](site-recovery-overview.md)

Kunden können Workloads auf einem VMM-Server am primären Standort schützen, während der sekundäre Standort mit einem VMM-Server verwaltet wird. In einer Notfallsituation wird für alle Workloads ein nahtloses Failover vom primären Standort zum sekundären Standort durchgeführt, der mit dem sekundären VMM-Server verwaltet wird. Für einige Organisationen kann es aber ratsam sein, einen einzelnen VMM-Server einzusetzen, um alle Rechenzentren zu verwalten und so Mehraufwand bei der Verwaltung zu vermeiden. Für Kunden mit dieser Topologie ist es wichtig, dass der VMM-Server wiederhergestellt werden kann, bevor die Workloads wiederhergestellt werden können. Kunden müssen beim Entwerfen ihrer VMM-Topologien also mit Bedacht vorgehen, um ihre Workloads „notfallsicher“ zu machen.

Wenn Ihre Infrastruktur nur einen einzelnen VMM-Server enthält, können Sie Azure Site Recovery bereitstellen, um virtuelle Computer in VMM-Clouds zu Azure zu replizieren oder eine Replikation zwischen Clouds auf einem einzelnen VMM-Server durchzuführen. Für die Wiederherstellung müssen Sie von außerhalb der Azure Site Recovery-Konsole mit Hyper-V-Replikat in der Hyper-V-Manager-Konsole ein manuelles Failover des VMM-Servers durchführen. Es wird empfohlen, den VMM-Server in einer der folgenden Topologien bereitzustellen, um die Workloads mit den bestmöglichen RTO- (Recovery Time Objective) und RPO-Werten (Recovery Point Objective) wiederherstellen zu können.

### Eigenständige Bereitstellung

In dieser Topologie stellen Sie einen eigenständigen VMM-Server auf einem virtuellen Computer am primären Standort bereit und replizieren den virtuellen Computer dann per Azure Site Recovery und Hyper-V-Replikat an einen sekundären Standort. In einigen Fällen kann das Installieren des VMM-Servers und der unterstützenden SQL Server-Instanz auf demselben virtuellen Computer dazu führen, dass die Ausfallzeiten reduziert werden, da nur ein virtueller Computer instanziiert werden muss. Wenn der VMM-Dienst einen Remotecomputer mit SQL Server verwendet, müssen Sie die SQL Server-Instanz vor dem VMM-Server wiederherstellen.

Die Schritte zum Bereitstellen eines einzelnen VMM-Servers auf einem virtuellen Computer mit Hyper-V-Replikat lauten:

1. Richten Sie VMM auf einem virtuellen Computer ein, auf dem SQL Server installiert ist.
2. Fügen Sie die zu verwaltenden Hosts zu Clouds auf diesem VMM-Server hinzu.
3. Melden Sie sich beim Azure-Portal an, und konfigurieren Sie Clouds für den Schutz.
4. Aktivieren Sie die Replikation für alle virtuellen Computer, die vom VMM-Server geschützt werden müssen.
5. Navigieren Sie zur Hyper-V-Manager-Konsole, wählen Sie Hyper-V-Replikat aus, und aktivieren Sie dann die Replikation auf dem virtuellen VMM-Computer. Stellen Sie sicher, dass der virtuelle VMM-Computer nicht zu den Clouds hinzugefügt wird, die vom ASR-Dienst geschützt werden, damit die Einstellungen der Hyper-V-Replikation nicht von ASR überschrieben werden.

Bei einem Notfall können Workloads auf folgende Weise wiederhergestellt werden:

1. Failover der VMM-Replikat-VM an den Wiederherstellungsstandort mit Hyper-V-Manager.
2. Nach Wiederherstellung der VMM-VM kann der Benutzer sich vom sekundären Standort beim Hyper-V-Wiederherstellungs-Manager anmelden.
3. Nach Abschluss des nicht geplanten Failovers können die Benutzer auf sämtliche Ressourcen am primären Standort zugreifen.


Für diese Topologie ist es erforderlich, dass der Benutzer das manuelle Failover seiner VMM-VMs an den sekundären Standort durchführt, bevor das Failover für die Workloads erfolgen kann.

![Eigenständig](./media/site-recovery-network-design/standalone.png)

### Clusterbereitstellung


Das Bereitstellen eines hoch verfügbaren VMM-Servers ist ein Verfahren, um den VMM-Dienst selbst clusterfähig zu machen. Dies ist hilfreich, wenn kritische Workloads per VMM verwaltet werden, da so die Verfügbarkeit der Workloads gewährleistet und ein Schutz vor einem Ausfall des VMM-Servers vorhanden ist. Es ist äußerst wichtig, dass der VMM-Server jederzeit verfügbar ist. Indem der Dienst clusterfähig gemacht wird, ist der VMM-Server vor dem Hardware-Failover des Hosts geschützt, auf dem der VMM-Server ausgeführt wird. Außerdem besteht ein Schutz vor Problemen, die auf dem virtuellen Computer auftreten können, auf dem VMM ausgeführt wird. Für die Bereitstellung eines hoch verfügbaren VMM-Servers ist es erforderlich, dass der VMM-Server in einem Windows Server-Failovercluster vorhanden ist. Weitere Informationen zum Bereitstellen eines hoch verfügbaren VMM-Servers finden Sie im System Center-Blogbeitrag hier:

Beim Schützen von Workloads mit ASR sollte der VMM-Server in einem gestreckten Cluster über geografisch verteilte Standorte hinweg bereitgestellt werden. Dies ist in Abbildung 2 dargestellt. Die von VMM verwendete SQL Server-Datenbank muss mithilfe von AlwaysOn-Verfügbarkeitsgruppen (SQL Server) mit einem Replikat am sekundären Standort geschützt werden. In einem Notfall wird für den VMM-Server und die dazugehörige SQL Server-Datenbank ein automatisches Failover zum Wiederherstellungsstandort durchgeführt, wonach dann für alle Workloads das Failover per ASR erfolgen kann.

![Gestreckte VMM-Cluster](./media/site-recovery-network-design/network-design1.png)

Abbildung 2

## Entwerfen des Netzwerkadressbereichs in einer Umgebung für die Notfallwiederherstellung

Wenn Administratoren die Bereitstellung einer Lösung für die Notfallwiederherstellung planen, ist eine der wichtigsten Fragen, wie die Anwendungen nach Abschluss des Failovers erreichbar sind. Moderne Anwendungen sind fast immer zu einem gewissen Grad von Netzwerken abhängig, sodass mit dem physischen Verschieben eines Diensts von einem Standort zum anderen fast immer besondere Netzwerkanforderungen verbunden sind. Es gibt zwei Hauptverfahren, wie dieses Problem bei Lösungen für die Notfallwiederherstellung gelöst wird. Der erste Ansatz besteht in der Verwaltung fester IP-Adressen. Trotz der Verschiebung der Dienste und der Tatsache, dass sich die Hostserver an verschiedenen physischen Standorten befinden, nehmen die Anwendungen die IP-Adresskonfiguration mit sich an den neuen Speicherort. Beim zweiten Ansatz ist eine vollständige Änderung der IP-Adresse während des Übergangs auf den wiederhergestellten Standort erforderlich. Jeder Ansatz verfügt über mehrere Implementierungsvarianten, die unten zusammengefasst sind.

Beim Entwerfen des Netzwerks für den Wiederherstellungsstandort haben Administratoren zwei Möglichkeiten:

### Option 1: Beibehalten der IP-Adressen 

Aus Sicht des Prozesses für die Notfallwiederherstellung ist die Verwendung feststehender IP-Adressen scheinbar die am einfachsten zu implementierende Methode. Sie ist aber mit einer Reihe potenzieller Herausforderungen verbunden, sodass es in der Praxis der am wenigsten beliebte Ansatz ist. Azure Site Recovery bietet die Möglichkeit, die IP-Adressen in allen Szenarien beizubehalten. Bevor Sie sich für die Beibehaltung der IP-Adressen entscheiden, sollten Sie sich über die Einschränkungen im Klaren sein, die sich daraus für die Failoverfunktionen ergeben. Wir sehen uns nun die Faktoren an, die Ihnen bei der Entscheidungsfindung in Bezug auf die Beibehaltung von IP-Adressen behilflich sein können. In dieser Kategorie gibt es zwei Hauptuntertypen: das gestreckte Subnetz und das Subnetzfailover. Es werden separat Szenarien beschrieben, in denen ein Subnetzfailover oder ein gestrecktes Subnetz über zwei Standorte hinweg verwendet wird.

#### Gestrecktes Subnetz

Hier wird das Subnetz gleichzeitig sowohl am primären Standort als auch am Standort für die Notfallwiederherstellung verfügbar gemacht. Einfach ausgedrückt bedeutet dies, dass Sie einen Server und seine IP-Konfiguration (Layer 3) an den zweiten Standort verschieben können und der Datenverkehr im Netzwerk automatisch an den neuen Standort weitergeleitet wird. Dies ist sehr einfach in der Handhabung aus Sicht des Servers, es bestehen jedoch eine Reihe von Herausforderungen:

- Aus Sicht von Layer 2 (Sicherungsschicht) sind Netzwerkgeräte erforderlich, die ein so genanntes „gestrecktes VLAN“ verwalten können. Dies stellt allerdings kein größeres Problem mehr dar, da diese Geräte jetzt allgemein verfügbar sind. Das zweite und schwierigere Problem ist, dass sich die potenzielle Fehlerdomäne durch Strecken das VLANs auf beide Standorte ausweitet und im Grunde zu einer einzigen Fehlerquelle wird. Dies ist zwar ein relativ unwahrscheinlicher Fall, aber es ist schon passiert, dass ein Broadcast-Storm gestartet wurde und nicht isoliert werden konnte. Wir haben zu diesem letzten Problem gemischte Rückmeldungen erhalten. Es gibt viele erfolgreiche Implementierungen, aber auch Aussagen der Art „Wir werden diese Technologie bei uns niemals implementieren“.
- Gestreckte Subnetze sind nicht möglich, wenn Sie Microsoft Azure als Standort für die Notfallwiederherstellung verwenden.


#### Subnetzfailover

Die Implementierung des Subnetzfailovers ist möglich, um von den oben beschriebenen Vorteilen der Lösung mit gestrecktem Subnetz zu profitieren, ohne dass das Subnetz über mehrere Standorte gestreckt wird. Hierbei ist ein Subnetz an Standort 1 oder 2 vorhanden, aber nie an beiden Standorten gleichzeitig. Um den IP-Adressraum im Falle eines Failovers beizubehalten, können Sie programmgesteuert dafür sorgen, dass die Routerinfrastruktur die Subnetze von einem Standort an einen anderen verschiebt. In einem Failoverszenario werden die Subnetze mit den zugeordneten, geschützten virtuellen Computern verschoben. Der größte Nachteil dieses Ansatzes ist, dass Sie bei Auftreten eines Fehlers das gesamte Subnetz verschieben müssen. Dies ist unter Umständen kein Problem, kann sich aber auf Überlegungen im Hinblick auf die Failovergranularität auswirken.

Wir untersuchen nun, wie ein fiktives Unternehmen mit dem Namen Contoso seine virtuellen Computer während der Ausführung eines Failovers des gesamten Subnetzes an einen Wiederherstellungsstandort replizieren kann. Es wird zuerst beschrieben, wie Contoso seine Subnetze während der Replikation von VMs zwischen zwei lokalen Standorten verwaltet. Anschließend wird erläutert, wie ein Subnetzfailover funktioniert, wenn Azure als Standort für die Notfallwiederherstellung verwendet wird.

##### Subnetzfailover – Für große Unternehmen geeignete Notfallwiederherstellung

Wir sehen uns nun ein Szenario an, bei dem wir die IP-Adresse aller VMs beibehalten und das Failover für das gesamte Subnetz zusammen durchführen möchten. Der primäre Standort verfügt über Anwendungen, die im Subnetz 192.168.1.0/24 ausgeführt werden. Wenn das Failover erfolgt, wird für alle virtuellen Computer, die Teil dieses Subnetzes sind, der Failoverschritt zum Wiederherstellungsstandort durchgeführt, und ihre IP-Adressen werden beibehalten. Die Routen müssen entsprechend geändert werden, um die Tatsache widerzuspiegeln, dass alle virtuellen Computer, die zum Subnetz 192.168.1.0/24 gehören, jetzt an den Wiederherstellungsstandort verschoben wurden.

In der folgenden Abbildung müssen die Routen zwischen primärem Standort und Wiederherstellungsstandort, drittem Standort und primärem Standort und drittem Standort und Wiederherstellungsstandort entsprechend geändert werden. Für die anfängliche Version dieses Dokuments wurden die folgenden Annahmen getroffen:

- Für jedes Rechenzentrum wird eine eigene Instanz von System Center VMM verwendet. Es erfolgt keine Replikation der System Center VMM-Datenbanken zwischen den Rechenzentren.
- Für jedes Rechenzentrum werden statische IP-Adressen für die virtuellen Computer verwendet.
- Die Konnektivität zwischen den Rechenzentren erfolgt über einen dedizierten Kreis und nicht per VPN-Konnektivität über das Internet.

	![IP-Adresse beibehalten](./media/site-recovery-network-design/network-design3.png)

	Abbildung 3

	![IP-Adresse beibehalten](./media/site-recovery-network-design/network-design2.png)
	
	Abbildung 4

Beim Aktivieren des Schutzes für einen bestimmten virtuellen Computer ordnet ASR die Netzwerkressourcen gemäß dem folgenden Workflow zu:

- ASR weist jeder Netzwerkschnittstelle auf dem virtuellen Computer eine IP-Adresse aus dem statischen IP-Adresspool zu, der im relevanten Netzwerk für jede System Center VMM-Instanz definiert ist.
- Wenn der Administrator für das Netzwerk am Wiederherstellungsstandort den gleichen IP-Adresspool definiert, der für das Netzwerk am primären Standort festgelegt wurde, würde ASR beim Zuweisen der IP-Adresse zum virtuellen Replikatcomputer die gleiche IP-Adresse wie für den primären virtuellen Computer zuordnen. Die IP-Adresse ist in VMM reserviert, aber nicht als Failover-IP-Adresse festgelegt. Die Failover-IP-Adresse wird erst unmittelbar vor dem Failover festgelegt.

	![IP-Adresse beibehalten](./media/site-recovery-network-design/network-design4.png)
	
	Abbildung 5

Abbildung 5 zeigt die TCP/IP-Failovereinstellungen für den virtuellen Replikatcomputer (auf der Hyper-V-Konsole). Diese Einstellungen werden unmittelbar vor dem Starten des virtuellen Computers nach einem Failover aufgefüllt.

Falls die gleiche IP-Adresse nicht verfügbar ist, ordnet ASR eine andere verfügbare IP-Adresse aus dem definierten IP-Adresspool zu.

Nachdem der virtuelle Computer für den Schutz aktiviert wurde, können Sie das folgende Beispielskript verwenden, um die IP-Adresse zu überprüfen, die dem virtuellen Computer zugewiesen wurde. Die gleiche IP-Adresse würde als Failover-IP-Adresse festgelegt und dem virtuellen Computer zum Zeitpunkt des Failovers zugewiesen werden:

![IP-Adresse beibehalten](./media/site-recovery-network-design/script.png)

>[AZURE.NOTE] Bei diesem Szenario, bei dem virtuelle Computer DHCP verwenden, liegt die Verwaltung von IP-Adressen vollständig außerhalb der Kontrolle von ASR. Ein Administrator muss sicherstellen, dass der DHCP-Server, über den die IP-Adressen am Wiederherstellungsstandort bereitgestellt werden, den gleichen Bereich wie für den primären Standort nutzen kann.

##### Lokales Subnetzfailover – Notfallwiederherstellung zu Azure

Mit Azure Site Recovery (ASR) kann Microsoft Azure für virtuelle Computer als Standort für die Notfallwiederherstellung genutzt werden. In diesem Fall müssen weitere Einschränkungen berücksichtigt werden.

Wir sehen uns ein Szenario an, bei dem das fiktive Unternehmen Woodgrove Bank über eine lokale Infrastruktur zum Hosten seiner Branchenanwendungen verfügt und die mobilen Anwendungen unter Azure gehostet werden. Die Verbindung zwischen den VMs der Woodgrove Bank in Azure und den lokalen Servern wird mit einem virtuellen privaten Netzwerk (Virtual Private Network, VPN) vom Typ „Standort-zu-Standort“ (S2S) sichergestellt. Dank S2S-VPN kann das virtuelle Netzwerk der Woodgrove Bank in Azure als Erweiterung des lokalen Netzwerks der Woodgrove Bank angesehen werden. Diese Kommunikation wird per S2S-VPN zwischen dem Woodgrove Bank-Edgenetzwerk und dem virtuellen Azure-Netzwerk ermöglicht. Woodgrove möchte nun ASR einsetzen, um seine Workloads, die im eigenen Rechenzentrum ausgeführt werden, zu Azure zu replizieren. Diese Option erfüllt die Anforderungen von Woodgrove, nämlich eine wirtschaftliche Option für die Notfallwiederherstellung und die Speicherung von Daten in Public Cloud-Umgebungen. Bei der Woodgrove Bank sind Anwendungen und Konfigurationen vorhanden, die von hartcodierten IP-Adressen abhängig sind. Daher besteht die Anforderung, dass IP-Adressen der Anwendungen nach einem Failover zu Azure beibehalten werden müssen.

Die lokale Infrastruktur der Woodgrove Bank wird von einem VMM 2012 R2-Server verwaltet. Auf dem System Center VMM-Server wurde ein VLAN-basiertes logisches Netzwerk mit dem Namen „Application Network“ erstellt. Ein VM-Netzwerk mit dem Namen „Application VM Network“ wurde mithilfe des logischen Netzwerks erstellt. Alle virtuellen Computer in der Anwendung verwenden statische IP-Adressen. Daher wird auch für das logische Netzwerk ein statischer IP-Pool definiert.

**Logisches Netzwerk**

![Logisches Netzwerk](./media/site-recovery-network-design/network-design5.png)

Abbildung 6

**VM-Netzwerk**

![VM-Netzwerk](./media/site-recovery-network-design/network-design6.png)

Abbildung 7

Die Woodgrove Bank hat sich dafür entschieden, ihren in Azure ausgeführten Ressourcen IP-Adressen aus dem IP-Adressbereich (172.16.1.0/24, 172.16.2.0/24) zuzuweisen.

Damit die Woodgrove Bank ihre virtuellen Computer zu Azure replizieren kann, während die IP-Adressen beibehalten werden, muss ein Azure Virtual Network erstellt werden. Es sollte sich hierbei um eine Erweiterung des lokalen Netzwerks handeln, damit Anwendungen das Failover vom lokalen Standort zu Azure nahtlos durchführen können. Azure ermöglicht Ihnen sowohl das Hinzufügen von Standort-zu-Standort- als auch von Punkt-zu-Standort-VPN-Verbindungen mit den virtuellen Netzwerken, die in Azure erstellt werden. Beim Einrichten der Standort-zu-Standort-Verbindung können Sie in einem Azure-Netzwerk nur dann Datenverkehr an den lokalen Standort (bei Azure als „local-network“ oder „lokales Netzwerk“ bezeichnet) weiterleiten, wenn der IP-Adressbereich sich vom lokalen IP-Adressbereich unterscheidet, weil Azure das Strecken von Subnetzen nicht unterstützt. Daraus folgt, dass Sie im Azure-Netzwerk kein lokales Netzwerk 192.168.1.0/24 hinzufügen können, wenn Sie über ein lokales Subnetz 192.168.1.0/24 verfügen. Der Grund hierfür liegt darin, dass Azure nicht weiß, dass keine aktiven virtuellen Computer im Subnetz vorhanden sind und dass das Subnetz nur für die Notfallwiederherstellung erstellt wird. Um einen korrekten Netzwerkdatenverkehr aus einem Azure-Netzwerk zu gewährleisten, dürfen keine Konflikte zwischen den Subnetzen im Netzwerk und dem lokalen Netzwerk vorliegen.

![Subnetzfailover](./media/site-recovery-network-design/network-design7.png)

Abbildung 8

Wir müssen die folgenden Workflows implementieren, um für Woodgrove die geschäftlichen Anforderungen zu erfüllen:

- Erstellen Sie ein zusätzliches Netzwerk (mit dem Namen „Recovery Netzwerk“), in dem die virtuellen Computer nach dem Failover erstellt werden.
- Gehen Sie wie folgt vor, um sicherzustellen, dass die IP-Adresse für einen virtuellen Computer nach einem Failover beibehalten wird: Navigieren Sie in den VM-Eigenschaften zur Registerkarte „Konfigurieren“, geben Sie die gleiche IP-Adresse an, die für den virtuellen Computer lokal verwendet wird, und klicken Sie dann auf „Speichern“. Wenn das Failover für den virtuellen Computer erfolgt, weist Azure Site Recovery die bereitgestellte IP-Adresse dem virtuellen Computer zu. 

	![Netzwerkeigenschaften](./media/site-recovery-network-design/network-design8.png)

	Abbildung 9

Nach das Failover ausgelöst wurde und die virtuellen Computer im „Recovery Network“ mit der gewünschten IP-Adresse erstellt wurden, kann die Verbindung mit diesem Netzwerk mit einer VNET-zu-VNET-Verbindung hergestellt werden. Falls erforderlich, kann diese Aktion in Form eines Skripts erstellt werden. Wie im vorherigen Abschnitt über das Subnetzfailover erläutert, müssten die Routen auch beim Failover zu Azure entsprechend geändert werden, um widerzuspiegeln, dass 192.168.1.0/24 jetzt zu Azure verschoben wurde.

![Netzwerkeigenschaften](./media/site-recovery-network-design/network-design9.png)

Abbildung 10

### Option 2: Ändern der IP-Adressen

Dieser Ansatz scheint nach dem Informationsstand der Autoren am weitesten verbreitet zu sein. Hierbei wird die IP-Adresse jedes virtuellen Computers geändert, der am Failover beteiligt ist. Ein Nachteil bei diesem Ansatz ist, dass das eingehende Netzwerk „lernen“ muss, dass sich die Anwendung, die sich unter IPx befunden hat, jetzt unter IPy befindet. Auch wenn IPx und IPy logische Namen sind, müssen DNS-Einträge normalerweise im gesamten Netzwerk geändert oder gelöscht werden, und zwischengespeicherte Einträge in Netzwerktabellen müssen aktualisiert oder gelöscht werden. Aus diesem Grund kann es je nach Einrichtung der DNS-Infrastruktur zu Ausfallzeiten kommen. Sie können diese Probleme abmildern, indem Sie für Intranetanwendungen niedrige TTL-Werte und für internetbasierte Anwendungen [Azure Traffic Manager mit ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) verwenden.

#### Ändern der IP-Adressen – Für große Unternehmen geeignete Notfallwiederherstellung

Wir sehen uns nun das Szenario an, bei dem Sie die Verwendung von unterschiedlichen IP-Adressen übergreifend für primäre Standorte und Wiederherstellungsstandorte planen. Im folgenden Beispiel verwenden wir zusätzlich einen dritten Standort, von dem aus auf die Anwendungen zugegriffen werden kann, die am primären Standort oder Wiederherstellungsstandort gehostet werden.

![Andere IP-Adresse](./media/site-recovery-network-design/network-design10.png)

Abbildung 11

In Abbildung 11 werden einige Anwendungen im Subnetz 192.168.1.0/24 am primären Standort gehostet und wurden so konfiguriert, dass sie nach einem Failover am Wiederherstellungsstandort im Subnetz 172.16.1.0/24 wieder hochgefahren werden. VPN-Verbindungen/Netzwerkrouten wurden entsprechend konfiguriert, damit alle drei Standorte aufeinander zugreifen können.
 
In Abbildung 12 ist zu sehen, dass Anwendungen nach einem Failover einer oder mehrerer Anwendungen im Wiederherstellungssubnetz wiederhergestellt werden. In diesem Fall besteht nicht die Einschränkung, das Failover für das gesamte Subnetz zur gleichen Zeit durchführen zu müssen. Es sind keine Änderungen erforderlich, um VPN- oder Netzwerkrouten neu zu konfigurieren. Mit einem Failover und einigen DNS-Updates wird sichergestellt, dass Zugriff auf die Anwendungen besteht. Wenn der DNS so konfiguriert ist, dass er dynamische Updates zulässt, registrieren die virtuellen Computer sich unter Verwendung der neuen IP-Adresse selbst, sobald sie nach einem Failover gestartet werden.

![Andere IP-Adresse](./media/site-recovery-network-design/network-design11.png)

Abbildung 12

Nach dem Failover weist die Replikat-VM möglicherweise eine IP-Adresse auf, die nicht der IP-Adresse des primären virtuellen Computers entspricht. Virtuelle Computer aktualisieren den DNS-Server, den sie nach dem Start verwenden. DNS-Einträge müssen in der Regel im gesamten Netzwerk geändert oder gelöscht werden, und zwischengespeicherte Einträge in Netzwerktabellen müssen aktualisiert oder gelöscht werden, daher ist es nicht ungewöhnlich, dass es während dieser Zustandsänderungen zu Ausfallzeiten kommt. Dieses Problem kann mit folgenden Maßnahmen vermieden werden:

- Durch Verwenden niedrige TTL-Werte für Intranetanwendungen.
- Durch Verwenden von Azure Traffic Manager mit ASR für internetbasierte Anwendungen.
- Durch Verwenden des folgende Skripts in Ihrem Wiederherstellungsplan zum Aktualisieren der DNS-Server, um ein rechtzeitiges Update sicherzustellen. (Das Skript ist nicht erforderlich, wenn die dynamische DNS-Registrierung konfiguriert ist wird.)

![Andere IP-Adresse](./media/site-recovery-network-design/script2.png)

#### Ändern der IP-Adressen – Notfallwiederherstellung in Azure

Im Blogbeitrag [Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) (Netzwerkinfrastruktur-Einrichtung für Microsoft Azure als Standort für die Notfallwiederherstellung) wird erläutert, wie die erforderliche Azure-Netzwerkinfrastruktur konfiguriert wird, wenn das Beibehalten der IP-Adressen keine Voraussetzung ist. Zuerst wird die Anwendung beschrieben, und anschließend wird erläutert, wie Sie das Netzwerk lokal und unter Azure einrichten. Am Ende geht es um die Durchführung eines Testfailovers und eines geplanten Failovers.




## Nächste Schritte

[Hier](site-recovery-network-mapping.md) erfahren Sie, wie Site Recovery Quell- und Zielnetzwerke zuordnet.

<!---HONumber=AcomDC_0224_2016-->