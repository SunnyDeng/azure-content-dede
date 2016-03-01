<properties
	pageTitle="Herstellen einer Verbindung mit Workloads nach einem Failover mit Azure Site Recovery | Microsoft Azure"
	description="In diesem Artikel werden Optionen für das Herstellen einer Verbindung mit Workloads nach einem Failover mit Azure Site Recovery erläutert."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  Herstellen einer Verbindung mit Workloads nach einem Failover mit Azure Site Recovery

Der Dienst Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem sekundären lokalen Datencenter repliziert werden. Eine kurze Übersicht Eine kurze Übersicht über das Gesamtthema finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md).


## Übersicht

Im Rahmen Ihrer BCDR-Strategie müssen Sie sicherstellen, dass Sie nach einem Failover eine Verbindung mit Workloads herstellen können. In diesem Artikel werden Strategien dafür beschrieben.

Moderne Anwendungen sind fast immer zu einem gewissen Grad von Netzwerken abhängig, also ist mit dem physischen Verschieben eines Diensts von einem Standort zu einem anderen fast immer die Herausforderung an das Netzwerk verbunden, die Erreichbarkeit der Anwendungen und Workloads nach dem Failover sicherzustellen. Es gibt zwei Hauptlösungen für dieses Problem:

- **Feste IP-Adressen**: Der erste Ansatz besteht in der Verwaltung fester IP-Adressen. Trotz der Verschiebung der Dienste und der Tatsache, dass sich die Hostserver an verschiedenen physischen Standorten befinden, nehmen die Anwendungen die IP-Adresskonfiguration mit sich an den neuen Speicherort.
- **Geänderte IP-Adressen**: Beim zweiten Ansatz ist eine Änderung der IP-Adresse während des Übergangs an den die wiederhergestellten Standort erforderlich.

## Option 1: Feste IP-Adressen

Hinsichtlich der Notfallwiederherstellung scheint die Verwendung einer festen IP-Adresse die am einfachsten zu implementierende Methode zu sein. Es gibt jedoch eine Reihe von möglichen Problemen, wodurch dieser Ansatz in der Praxis am wenigsten verbreitet ist. Azure Site Recovery bietet die Möglichkeit, die IP-Adressen in allen Szenarien beizubehalten. Bevor Sie sich für diesen Ansatz entscheiden, betrachten Sie die Kriterien für die wichtigsten Failoverszenarien (Failover bei Subnetzen und gestreckten Subnetzen).

- **Failover bei gestreckten Subnetzen:** In einem gestreckten Subnetz ist das Subnetz gleichzeitig am primären Standort und am Zielstandort verfügbar. Einfach ausgedrückt bedeutet dies, dass Sie einen Server und seine IP-Konfiguration (Layer 3) auf den sekundären Standort verschieben können, und der Datenverkehr im Netzwerk wird automatisch an den neuen Standort weitergeleitet. Dies ist sehr einfach in der Handhabung aus Sicht des Servers, es bestehen jedoch eine Reihe von Herausforderungen:
	- Aus Sicht von Layer 2 (Sicherungsschicht) sind Netzwerkgeräte erforderlich, die ein gestrecktes VLAN verwalten können. Diese Geräte sind jetzt allerdings allgemein verfügbar.
	- Das zweite und schwierigere Problem ist, dass sich die potenzielle Fehlerdomäne durch Strecken das VLANs auf beide Standorte ausweitet und im Grunde zu einer einzigen Fehlerquelle wird. Es ist zwar unwahrscheinlich, aber es könnte passieren, dass ein Broadcast-Storm gestartet wird und nicht isoliert werden kann. Wir kennen unterschiedliche Meinungen zu diesem Problem, von der erfolgreichen Implementierung bis hin zu "niemals".
	- Ein gestrecktes Subnetz ist nicht möglich, wenn ein Failover zu Azure als DR-Standort ausgeführt wird.
- **Subnetzfailover:** Es ist möglich, Subnetzfailover zu implementieren, um die Vorteile eines gestreckten Subnetzes ohne tatsächliche Streckung zu nutzen. In dieser Konfiguration ist ein Subnetz an Standort 1 oder 2, jedoch nie an beiden Standorten gleichzeitig vorhanden ist. Um den IP-Adressraum im Falle eines Failovers beizubehalten, können Sie programmgesteuert dafür sorgen, dass die Routerinfrastruktur die Subnetze von einem Standort an einen anderen verschiebt. In einem Failoverszenario werden die Subnetze mit den zugeordneten, geschützten virtuellen Computern verschoben. Der größte Nachteil dieses Ansatzes ist, dass Sie bei Auftreten eines Fehlers das gesamte Subnetz verschieben müssen, was möglicherweise eine vernünftige Lösung ist, sich jedoch auf Überlegungen im Hinblick auf die Failovergranularität auswirken kann.


### Beispiel – Subnetzfailover in einem Unternehmen

Werfen wir einmal einen Blick darauf, wie ein fiktives Unternehmen (Contoso) seine virtuellen Computer während der Ausführung eines Failovers des gesamten Subnetzes an einen Wiederherstellungsstandort replizieren kann. Es wird beschrieben, wie Contoso seine Subnetze während der Replikation von VMs zwischen zwei lokalen Standorten verwaltet, und erläutert, wie ein Subnetzfailover funktioniert, wenn Azure als Standort für die Notfallwiederherstellung verwendet wird.

- Der primäre Standort verfügt über Anwendungen, die im Subnetz 192.168.1.0/24 ausgeführt werden.
- Für das gesamte Subnetz wird ein Failover ausgeführt, und alle virtuellen Computer im Subnetz werden per Failover an den Wiederherstellungsstandort repliziert und behalten ihre IP-Adressen bei.
- Wie im unten abgebildeten Diagramm dargestellt, müssen die Routen zwischen dem primären und dem Wiederherstellungsstandort, dem dritten und dem primären Standort sowie dem dritten und dem Wiederherstellungsstandort entsprechend geändert werden, um der Tatsache Rechnung zu tragen, dass alle virtuellen Computer, die zu Subnetz 192.168.1.0/24 gehören, zum Wiederherstellungsstandort verschoben wurden.
- In diesem Diagramm wird Folgendes vorausgesetzt:
	-  Jedes Rechenzentrum wird durch eine eigene Instanz von VMM bedient. Es erfolgt keine Replikation der System Center VMM-Datenbanken zwischen den Rechenzentren.
	-  Jedes Rechenzentrum verwendet statische IP-Adressen für die virtuellen Computer.
	-  Die Konnektivität zwischen den Rechenzentren erfolgt über einen dedizierten Kreis und nicht per VPN-Konnektivität über das Internet.

	**Vor dem Failover**

	![Vor dem Failover](./media/site-recovery-connect-after-failover/network-design2.png)

	**Nach dem Failover**

	![Nach dem Failover](./media/site-recovery-connect-after-failover/network-design3.png)


Wenn Sie den Schutz für einen bestimmten virtuellen Computer aktivieren, weist Site Recovery wie folgt Netzwerkressourcen zu:

1. Site Recovery weist jeder Netzwerkschnittstelle auf dem virtuellen Computer eine IP-Adresse aus dem statischen IP-Adresspool zu, der im relevanten Netzwerk für jede VMM-Instanz definiert ist.
2. Wenn der Administrator für das Netzwerk auf dem Wiederherstellungsstandort den gleichen IP-Adresspool definiert, der auf dem primären Standort verwendet wird, weist Site Recovery der Replikat-VM dieselbe IP-Adresse zu, die dem primären virtuellen Computer zugeordnet ist. Die IP-Adresse wird in VMM reserviert, aber nicht als die Failover-IP-Adresse festgelegt. Die Failover-IP-Adresse wird erst unmittelbar vor dem Failover festgelegt. Dieser Screenshot zeigt das Failover von TCP/IP-Einstellungen für die Replikat-VM (auf der Hyper-V-Konsole). Diese Einstellungen werden unmittelbar vor dem Start des Failovers für den virtuellen Computer repliziert.

	![Festlegen der IP-Adresse](./media/site-recovery-connect-after-failover/network-design4.png)

3. Wenn die gleiche IP-Adresse nicht verfügbar ist, wird von Site Recovery eine andere Adresse aus dem Pool zugeordnet.
4. Nachdem die virtuelle Maschine für den Schutz aktiviert wurde, können Sie das folgende Beispielskript verwenden, um die IP-Adresse zu überprüfen, die dem virtuellen Computer zugewiesen wurde. Die gleiche IP-Adresse würde als Failover-IP-Adresse festgelegt sein und dem virtuellen Computer zum Zeitpunkt der Failovers zugewiesen werden.

    $vm = Get-SCVirtualMachine -Name $na = $vm[0].VirtualNetworkAdapters $ip = Get-SCIPAddress -GrantToObjectID $na[0].id $ip.address

Beachten Sie, dass die IP-Adressverwaltung nicht durch Site Recovery erfolgt, wenn die virtuellen Computer DHCP verwenden. Sie müssen sicherstellen, dass der DHCP-Server, der die IP-Adressen auf dem Wiederherstellungsstandort zuweist, Adressen aus dem gleichen Bereich zuweisen kann, der dem Bereich auf dem primären Standort entspricht.

### Beispiel – Subnetzfailover zu Azure

Wenn ein Failover zu Azure ausgeführt wird, gelten einigen weitere Einschränkungen. Betrachten wir ein fiktives Unternehmen (Woodgrove Bank), das seine LOB-Anwendungen (Line-of-Business) in einer lokalen Infrastruktur und seine mobilen Anwendung in Azure hostet.


- Die Konnektivität zwischen den VMs der Woodgrove Bank in Azure und den lokalen Servern erfolgt über ein Standort-zu-Standort-VPN, in dem das virtuelle Netzwerk in Azure als eine Erweiterung der des lokalen Netzwerks der Woodgrove Bank angezeigt wird.
- Die Woodgrove Bank möchte Site Recovery verwenden, um die lokalen Workloads zu Azure zu replizieren.
- Bei der Woodgrove Bank sind Anwendungen und Konfigurationen vorhanden, die von hartcodierten IP-Adressen abhängig sind, daher müssen die IP-Adressen der Anwendungen nach einem Failover zu Azure beibehalten werden.
- Die lokale Infrastruktur der Woodgrove Bank wird von einem VMM 2012 R2-Server verwaltet.
- Es gibt ein VLAN-basiertes logisches Netzwerk (Anwendungsnetzwerk) auf dem VMM-Server. ![Logisches Netzwerk](./media/site-recovery-connect-after-failover/network-design5.png)
- Ein VM-Netzwerk (Anwendungs-VM-Netzwerk) wurde mithilfe des logischen Netzwerks erstellt. ![VM-Netzwerk](./media/site-recovery-connect-after-failover/network-design6.png)
- Alle virtuellen Computer in der Anwendung verwenden statische IP-Adressen, daher wird auch ein statischer IP-Pool für das logische Netzwerk definiert.
- Die Woodgrove Bank weist ihren in Azure ausgeführten Ressourcen IP-Adressen aus dem IP-Adressbereich (172.16.1.0/24, 172.16.2.0/24) zu.


Damit die Woodgrove Bank die Replikation bereitstellen und IP-Adressen verwalten kann, ist Folgendes erforderlich:

- Ein virtueller Azure-Computer, der eine Erweiterung des lokalen Netzwerks ist, so dass für Anwendungen ein nahtloses Failover ausgeführt werden kann.
- Beachten Sie beim Einrichten der Standort-zu-Standort-Verbindung in Azure, dass Sie in einem Azure-Netzwerk nur dann Datenverkehr an den lokalen Standort (bei Azure als "local-network" oder "lokales Netzwerk" bezeichnet) weiterleiten können, wenn der IP-Adressbereich sich vom lokalen IP-Adressbereich unterscheidet, weil Azure das Strecken von Subnetzen nicht unterstützt. Daraus folgt, dass Sie im Azure-Netzwerk kein lokales Netzwerk 192.168.1.0/24 hinzufügen können, wenn Sie über ein lokales Subnetz 192.168.1.0/24 verfügen. Der Grund hierfür liegt darin, dass Azure nicht weiß, dass keine aktiven virtuellen Computer im Subnetz vorhanden sind und dass das Subnetz nur für die Notfallwiederherstellung erstellt wird. Um einen korrekten Netzwerkdatenverkehr aus einem Azure-Netzwerk zu gewährleisten, dürfen keine Konflikte zwischen den Subnetzen im Netzwerk und dem lokalen Netzwerk vorliegen.
- Es muss ein zusätzliches Netzwerk in Azure (Wiederherstellungsnetzwerk) erstellt werden, in dem die per Failover verschobenen virtuellen Computer erstellt werden.

	![Azure-Netzwerke](./media/site-recovery-connect-after-failover/network-design7.png)

- Um sicherzustellen, dass die IP-Adresse für den virtuellen Computer beibehalten wird, wird in den Eigenschaften des virtuellen Computers in Site Recovery festgelegt, dass dieselbe IP-Adresse verwendet werden soll. Nach dem Failover wird die angegebene IP-Adresse dann von Site Recovery dem virtuellen Computer zugewiesen. ![Azure-Netzwerke](./media/site-recovery-connect-after-failover/network-design8.png)


- Wenn das Failover ausgelöst wird und die virtuellen Computer im Wiederherstellungsnetzwerk mit der erforderlichen IP-Adresse erstellt werden, kann die Verbindung mit dem virtuellen Computer hergestellt werden. Diese Aktion kann per Skript erfolgen. Wie im vorherigen Abschnitt über das Subnetzfailover erläutert, müssten die Routen auf beim Failover zu Azure entsprechend geändert werden, um widerzuspiegeln, dass 192.168.1.0/24 jetzt zu Azure verschoben wurde. ![Azure-Netzwerke](./media/site-recovery-connect-after-failover/network-design9.png)

## Option 2: Geändert von IP-Adressen

Dieser Ansatz scheint am häufigsten gewählt zu werden und bedeutet, dass sich die IP-Adresse jedes virtuellen Computers, für den ein Failover erfolgt, ändert. Größter Nachteil dieses Ansatzes: Die Netzwerkinfrastruktur muss erkennen können, wenn sich IP-Adressen geändert haben, und muss wissen, dass DNS-Einträge in der Regel im gesamten Netzwerk geändert oder gelöscht werden müssen, ebenso wie zwischengespeicherte Einträge in Netzwerktabellen. Dies könnte zu Ausfallzeiten führen, je nachdem, wie die DNS-Infrastruktur eingerichtet ist. Sie können diese Probleme mindern, indem Sie für Intranetanwendungen niedrige TTL-Werte und für internetbasierte Anwendungen [Azure Traffic Manager mit Site Recovery](https://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) verwenden.

### Beispiel 2 – Geänderte IP-Adressen

Betrachten wir dieses Szenario mit einem Beispiel, bei dem es einen dritten Standort gibt, von dem aus auf die auf dem primären oder Wiederherstellungsstandort gehosteten Anwendungen zugegriffen werden kann.

![Azure-Netzwerke](./media/site-recovery-connect-after-failover/network-design10.png)

- Einige Programme werden im Subnetz 192.168.1.0/24 am primären Standort gehostet und wurden so konfiguriert, dass sie nach einem Failover am Wiederherstelllungsstandort im Subnetz 172.16.1.0/24 wieder hochgefahren werden.
- VPN-Verbindungen/Netzwerkrouten wurden entsprechend konfiguriert, damit alle drei Standorte aufeinander zugreifen können.
- Nach einem Failover einer oder mehrerer Anwendungen, werden diese im Wiederherstellungssubnetz wiederhergestellt. In diesem Fall ist kein gleichzeitiges Failover des gesamten Subnetzes erforderlich, und es müssen keine Änderungen vorgenommen werden, um VPN- oder Netzwerkrouten neu zu konfigurieren.
- Ein Failover und einige DNS-Updates sorgen dafür, dass die Anwendungen stets verfügbar sind. Wenn der DNS-Server so konfiguriert ist, dass er dynamische Updates zulässt, registrieren die virtuellen Computer sich unter Verwendung der neuen IP-Adresse selbst, sobald sie nach einem Failover gestartet werden.

	![Azure-Netzwerke](./media/site-recovery-connect-after-failover/network-design11.png)

- Nach dem Failover weist die Replikat-VM möglicherweise eine IP-Adresse auf, die nicht der IP-Adresse des primären virtuellen Computers entspricht.
- Virtuelle Computer aktualisieren den DNS-Server, den sie nach dem Start verwenden. DNS-Einträge müssen in der Regel im gesamten Netzwerk geändert oder gelöscht werden, und zwischengespeicherte Einträge in Netzwerktabellen müssen aktualisiert oder gelöscht werden, daher ist es nicht ungewöhnlich, dass es während dieser Zustandsänderungen zu Ausfallzeiten kommt. Die Auswirkungen können auf folgende Weise gemindert werden:

	- Durch Verwenden niedrige TTL-Werte für Intranetanwendungen.
	- Durch Verwenden von [Azure Traffic Manager mit Site Recovery](https://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) für internetbasierte Anwendungen.
	- Durch Verwenden des folgende Skripts in Ihrem Wiederherstellungsplan zum Aktualisieren der DNS-Server, um ein rechtzeitiges Update sicherzustellen. (Das Skript ist nicht erforderlich, wenn die dynamische DNS-Registrierung konfiguriert ist wird.)

    [string]$Zone, [string]$name, [string]$IP ) $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name $newrecord = $record.clone() $newrecord.RecordData[0].IPv4Address = $IP Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## Nächste Schritte

[Dieser Blogbeitrag](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) enthält eine Anleitung zum Einrichten der erforderlichen Azure-Netzwerkinfrastruktur, wenn das Beibehalten der IP-Adressen keine Voraussetzung ist. Im Beitrag wird erläutert, wie Sie Netzwerke lokal und in Azure einrichten. Abschließend werden Anweisungen zum Ausführen eines Testfailovers und eines geplanten Failover bereitgestellt.

<!---HONumber=AcomDC_0218_2016-->