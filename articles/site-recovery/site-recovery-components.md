<properties
	pageTitle="Wie funktioniert Site Recovery? | Microsoft Azure"
	description="Dieser Artikel enthält eine Übersicht über die Site Recovery-Architektur."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/16/2016"
	ms.author="raynew"/>

# Wie funktioniert Azure Site Recovery?

In diesem Artikel werden die zugrunde liegende Architektur von Site Recovery und die dafür erforderlichen Komponenten beschrieben. Sollten Sie nach der Lektüre dieses Artikels Fragen haben, können Sie diese im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) stellen.

## Übersicht

Organisationen benötigen eine Strategie für die Bereiche Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR), mit der bestimmt wird, wie Apps, Workloads und Daten bei geplanten und ungeplanten Ausfallzeiten verfügbar bleiben und die normalen Arbeitsbedingungen so schnell wie möglich wiederhergestellt werden können. Bei Ihrer BCDR-Strategie geht es hauptsächlich um Lösungen, bei denen die Unternehmensdaten sicher sind und wiederhergestellt werden können und Workloads auch nach dem Eintreten eines Notfalls ständig verfügbar sind.

Site Recovery ist ein Azure-Dienst, der einen Beitrag zu Ihrer BCDR-Strategie leistet, indem die Replikation von lokalen physischen Servern und virtuellen Maschinen in die Cloud (Azure) oder in ein sekundäres Rechenzentrum orchestriert wird. Wenn es an Ihrem primären Standort zu Ausfällen kommt, wird ein Failover zum sekundären Standort durchgeführt, um die Verfügbarkeit von Apps und Workloads zu erhalten. Wenn wieder Normalbetrieb herrscht, führen Sie das Failback zum primären Standort durch.

Site Recovery kann in verschiedenen Szenarien verwendet werden und für den Schutz mehrerer Workloads sorgen.

- **Schützen virtueller VMware-Maschinen**: Sie können lokale virtuelle VMware-Maschinen schützen, indem Sie sie zu [Azure](site-recovery-vmware-to-azure-classic.md) oder in ein [sekundäres Datencenter](site-recovery-vmware-to-vmware.md) replizieren.
- **Schützen von Hyper-V-VMs**: Sie können lokale virtuelle Hyper-V-Maschinen in VMM-Clouds schützen, indem Sie sie zu [Azure](site-recovery-vmm-to-azure.md) oder in ein [sekundäres Datencenter](site-recovery-vmm-to-vmm.md) replizieren. Sie können Hyper-V-VMs, die nicht von VMM verwaltet werden, zu [Azure](site-recovery-hyper-v-site-to-azure.md) replizieren.
- **Schützen physischer Servern auf Azure**: Sie können physische Computer, auf denen Windows oder Linux ausgeführt wird, schützen, indem Sie sie zu [Azure](site-recovery-vmware-to-azure-classic.md) oder in ein [sekundäres Datencenter](site-recovery-vmware-to-vmware.md) replizieren.
- **Migrieren von VMs**: Sie können Site Recovery verwenden, um [Azure-IaaS-VMs](site-recovery-migrate-azure-to-azure.md) zwischen Regionen zu migrieren oder um [AWS Windows-Instanzen](site-recovery-migrate-aws-to-azure.md) zu Azure-IaaS-VMs zu migrieren.

Site Recovery kann die meisten auf diesen virtuellen Maschinen und physischen Servern ausgeführten Apps replizieren. Eine vollständige Zusammenfassung der unterstützten Apps finden Sie unter [Welche Workloads können mit Azure Site Recovery geschützt werden?](site-recovery-workload.md).


## Replizieren lokaler virtueller VMware-Maschinen oder physischer Server auf Azure

Wenn Sie VMware-VMs oder physische Windows/Linux-Computer per Replikation zu Azure schützen möchten, benötigen Sie die unten angegebenen Dinge.

Für diese Replikation stehen derzeit zwei verschiedene Architekturen zur Verfügung:

- **Legacy-Architektur**: Diese Architektur sollte für neue Bereitstellungen nicht verwendet werden. 
- **Verbesserte Architektur**: Dies ist die aktuelle Lösung, die für alle neuen Bereitstellungen empfohlen wird. Sie können [Ihre Legacy-Architektur](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) ebenfalls zu dieser neuen Lösung migrieren.

Im Folgenden sehen Sie die Architektur für die erweiterte Bereitstellung

![Verbessert](./media/site-recovery-components/arch-enhanced.png)

- **Lokal**: Wenn Sie die verbesserte Architektur bereitstellen, ist es nicht erforderlich, Infrastruktur-VMs in Azure bereitzustellen. Außerdem ist der gesamte Datenverkehr verschlüsselt, und die Replikationsverwaltungskommunikation wird über HTTPS 443 übermittelt. In Ihrer lokalen Infrastruktur benötigen Sie Folgendes:

	- **Verwaltungsserver**: ein einzelner Verwaltungsserver, der alle Site Recovery-Komponenten ausführt, darunter:

		- **Konfigurationsserver**: zum Koordinieren der Kommunikation zwischen Ihrer lokalen Umgebung und Azure sowie zum Verwalten von Datareplikations- und Wiederherstellungsprozessen.
		- **Prozessserver**: fungiert als Replikationsgateway. Er empfängt Daten aus geschützten Quellcomputern, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet die Replikationsdaten an den Azure-Speicher. Außerdem wickelt er die Pushinstallation des Mobilitätsdiensts auf geschützten Computern ab und führt die automatische Ermittlung von virtuellen VMware-Computern durch. Mit zunehmender Größe die Bereitstellung können Sie zusätzliche dedizierte Server hinzufügen, die als Prozessserver für größere Mengen an Replikationsdatenverkehr genutzt werden.
		- **Masterzielserver**: behandelt die Replikationsdaten beim Failback von Azure. 

	- **VMware ESX/ESXi-Host und vCenter-Server**: mindestens ein ESX/ESXi-Host-Server, auf dem sich VMware-VMs befinden. Zum Verwalten dieser Hosts empfiehlt sich ein vCenter-Server. Beachten Sie, dass Sie auch für den Schutz physischer Server eine VMware-Umgebung benötigen, damit ein Failback von Azure zum lokalen Standort möglich ist.
	
	- **Geschützte Computer**: Für jeden Computer, den Sie in Azure replizieren möchten, muss die Mobilitätsdienstkomponente installiert sein. Er erfasst die Datenschreibvorgänge auf dem Computer und sendet sie an den Prozessserver. Die Komponente kann manuell oder mittels Push automatisch durch den Prozessserver installiert werden, wenn der Schutz für den Computer aktiviert wird.

- **Azure**: In Ihrer Azure-Infrastruktur benötigen Sie Folgendes:
	- **Azure-Konto**: Sie benötigen ein Microsoft Azure-Konto.
	- **Azure-Speicher**: Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Computer werden bei einem Failover hochgefahren. 
	- **Azure-Netzwerk**: Sie benötigen ein virtuelles Azure-Netzwerk, mit dem die Azure-VMs bei einem Failover eine Verbindung herstellen, wenn ein Failover stattfindet. Sie benötigen außerdem eine VPN-Verbindung (oder Azure ExpressRoute) zwischen dem Azure-Netzwerk und dem lokalen Standort.

	![Verbessert](./media/site-recovery-components/arch-enhanced2.png)

Erfahren Sie mehr über die genauen [Voraussetzungen für die Bereitstellung](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).

### Failback-Architektur

- Ein Failback von Azure muss zu VMware-VMs ausgeführt werden. Ein Failback zu einem physischen Server ist derzeit nicht möglich.
- Für ein Failback benötigen Sie eine VPN-Verbindung (oder Azure ExpressRoute) zwischen dem Azure-Netzwerk und Ihrem lokalen Netzwerk.
- Sie benötigen einen Prozessserver in Azure, um das Failback auszuführen. Nach Beendigung des Failbacks können Sie diesen löschen.
- Sie benötigen einen lokalen Masterzielserver. Ein Masterzielserver ist bei lokaler Einrichtung standardmäßig auf dem Verwaltungsserver installiert. Für größere Mengen an Datenverkehr empfiehlt es sich jedoch, lokal einen separaten Masterzielserver für das Failback einzurichten.

![Verbessertes Failback](./media/site-recovery-components/enhanced-failback.png)

Weitere Informationen zum [Failback](site-recovery-failback-azure-to-vmware-classic.md).

### Legacy-Architektur

Legacy-Architektur erfordert einen lokalen Konfigurationsserver sowie einen Prozesserver, VMware ESX/ESXi-Hosts und vCenter-Server. Auf den Computern, die Sie schützen möchten, muss der Mobilitätsdienst installiert sein. Richten Sie in Azure Azure-VMs in Azure für den Konfigurationsserver und den Masterzielserver ein. Außerdem benötigen Sie ein Azure-Abonnement,ein Speicherkonto und ein virtuelles Netzwerk.



## Replizieren Sie Hyper-V-VMs in VMM-Clouds zu Azure.

Wenn sich Ihre VMs auf einem Hyper-V-Host befinden, der sich in einer System Center-VMM-Cloud befindet, benötigen Sie für die Replikation zu Azure die folgenden Dinge.

- Lokal: 
	- **VMM-Server**: Mindestens einen VMM-Server, für den mindestens eine private VMM-Cloud eingerichtet ist. Der Server sollte auf System Center 2012 R2 ausgeführt werden. Der VMM-Server benötigt eine Internetverbindung. Richten Sie die Netzwerkzuordnung ein, damit Azure-VMs nach einem Failover mit einem Netzwerk verbunden sind. Dafür müssen Sie Quell-VMs mit einem VMM-VM-Netzwerk verbinden. Dieses Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist.
	- **Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver in der VMM-Cloud. Hyper-V-Hosts müssen Windows Server 2012 R2 ausführen.
	- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss eine mindestens eine zu schützende VM enthalten.
	
- Azure:
	- **Azure-Konto**: Sie benötigen ein Microsoft Azure-Konto.
	- **Azure-Speicher**: Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Computer werden bei einem Failover hochgefahren.
	- **Azure-Netzwerk**: Richten Sie die Netzwerkzuordnung ein, damit Azure-VMs nach einem Failover mit einem Netzwerk verbunden sind. Hierzu müssen Sie ein Azure-Netzwerk einrichten.

	![VMM zu Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

In diesem Szenario ist der Azure Site Recovery-Anbieter auf dem VMM-Server während der Bereitstellung von Site Recovery installiert. Dieser koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Der Azure Recovery Services-Agent ist während der Bereitstellung von Site Recovery auf dem Hyper-V-Hostserver installiert, und die Daten werden über HTTPS 443 zwischen ihm und dem dem Azure-Speicher repliziert. Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt. Die replizierten Daten im Azure-Speicher werden ebenfalls verschlüsselt.

Erfahren Sie mehr über die genauen [Voraussetzungen für die Bereitstellung](site-recovery-vmm-to-azure.md#before-you-start).

## Replizieren von Hyper-V-VMs zu Azure (ohne VMM)

Wenn Sie Ihre VMs nicht über einen System Center-VMM-Server verwalten, müssen Sie die Replikation zu Azure wie folgt durchführen:

- Lokal: 
	- **Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver. Hyper-V-Hosts müssen Windows Server 2012 R2 ausführen.
	- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss eine mindestens eine zu schützende VM enthalten.
	
- Azure:
	- **Azure-Konto**: Sie benötigen ein Microsoft Azure-Konto.
	- **Azure-Speicher**: Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Computer werden bei einem Failover hochgefahren.

	![Hyper-V-Standort zu Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

In diesem Szenario sind der Azure Site Recovery-Anbieter und derAzure Recovery Services-Agent auf dem VMM-Server während der Bereitstellung von Site Recovery installiert. Der Anbieter koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Der-Agent verarbeitet die Replikation von Daten über HTTPS 443. Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt. Die replizierten Daten im Azure-Speicher werden ebenfalls verschlüsselt.

Erfahren Sie mehr über die genauen [Voraussetzungen für die Bereitstellung](site-recovery-hyper-v-site-to-azure.md#before-you-start).

## Replizieren von Hyper-V-VMs in ein sekundäres Rechenzentrum

Wenn Sie Ihre Hyper-V-VMs schützen möchten, indem Sie sie in ein sekundäres Rechenzentrum replizieren, gehen Sie wie unten angegeben vor. Beachten Sie, dass dies nur möglich ist, wenn Ihr Hyper-V-Hostserver in einer System Center-VMM-Cloud verwaltet wird.

- **Lokal**: 
	- **VMM-Server**: Wir empfehlen einen VMM-Server am primären Standort sowie einen am sekundären Standort, von denen jeder mindestens eine private VMM-Cloud enthält. Der Server sollte mindestens mit System Center 2012 SP1 mit neuesten Updates ausgeführt werden und mit dem Internet verbunden sein. Für die Clouds sollte das Hyper-V-Funktionsprofil eingestellt sein.
	- **Hyper-V Server**: Hyper-V-Hostserver in der primären sowie in der sekundären VMM-Cloud. Die Hostserver müssen mindestens mit Windows Server 2012 mit den neuesten Updates ausgeführt werden und über eine Internetverbindung verfügen.
	- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss eine mindestens eine zu schützende VM enthalten.
	
- **Azure**: Sie benötigen auch ein Azure-Abonnement.

	![Lokal zu lokal](./media/site-recovery-components/arch-onprem-onprem.png)

In diesem Szenario ist der Azure Site Recovery-Anbieter auf dem VMM-Server während der Bereitstellung von Site Recovery installiert. Dieser koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Die Daten werden zwischen dem primären und sekundären Hyper-V-Hostserver über das LAN oder VPN per Kerberos- oder Zertifikatauthentifizierung repliziert. Die Kommunikation vom Anbieter und zwischen den Hyper-V-Hostservern ist jeweils sicher und verschlüsselt.

Erfahren Sie mehr über die genauen [Voraussetzungen für die Bereitstellung](site-recovery-vmm-to-vmm.md#before-you-start).



## Replizieren von Hyper-V-VMs in ein sekundäres Rechenzentrum per SAN-Replikation

Wenn sich Ihre VMs auf einem Hyper-V-Host befinden, der in einer System Center-VMM-Cloud verwaltet wird, und wenn Sie einen SAN-Speicher verwenden, müssen Sie bei der Replikation zwischen zwei Rechenzentren wie unten angegeben vorgehen.

- **Lokal**: 
	- **SAN-Array**: Ein [unterstütztes SAN-Array](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), das vom primären VMM-Server verwaltet wird. Das SAN teilt eine Netzwerkinfrastruktur mit einem anderen SAN-Array am sekundären Standort.
	- **VMM-Server**: Wir empfehlen einen VMM-Server am primären Standort sowie einen am sekundären Standort, von denen jeder mindestens eine private VMM-Cloud enthält. Der Server sollte mindestens mit System Center 2012 SP1 mit neuesten Updates ausgeführt werden und mit dem Internet verbunden sein. Für die Clouds sollte das Hyper-V-Funktionsprofil eingestellt sein.
	- **Hyper-V Server**: Hyper-V-Hostserver in der primären sowie in der sekundären VMM-Cloud. Die Hostserver müssen mindestens mit Windows Server 2012 mit den neuesten Updates ausgeführt werden und über eine Internetverbindung verfügen.
	- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss eine mindestens eine zu schützende VM enthalten.
	
- **Azure**: Sie benötigen auch ein Azure-Abonnement.

	![SAN-Replikation](./media/site-recovery-components/arch-onprem-onprem-san.png)

In diesem Szenario ist der Azure Site Recovery-Anbieter auf dem VMM-Server während der Bereitstellung von Site Recovery installiert. Dieser koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Die Daten werden zwischen den primären und sekundären Speicherarrays per synchroner SAN-Replikation repliziert.

Erfahren Sie mehr über die genauen [Voraussetzungen für die Bereitstellung](site-recovery-vmm-san.md#before-you-start).


## Replizieren virtueller VMware-Maschinen oder physischer Server an einem sekundären Standort

Wenn Sie VMware-VMs oder physische Windows/Linux-Computer per Replikation zwischen zwei lokalen Rechenzentren schützen möchten, benötigen Sie die unten angegebenen Dinge.

- **Lokaler primären Standort**: 
	- **Prozessserver**: Richten Sie die Prozessserverkomponente an Ihrem primären Standort ein, um das Caching, die Komprimierung und die Datenoptimierung zu behandeln. Außerdem erfolgt hierüber die Pushinstallation des vereinheitlichten Agents auf Maschinen, die Sie schützen möchten.
	- **VMware ESX/ESXi und vCenter Server**: Zum Schutz von VMware-VMs benötigen Sie einen VMware EXS/ESXi-Hypervisor oder einen VMware vCenter-Server, der mehrere Hypervisoren verwaltet.
	- Geschützte Computer: Auf VMware-VMs oder physischen Windows/Linux-Server, die geschützt werden sollen, muss der vereinheitlichte Agent installiert sein. Der vereinheitlichte Agent wird auch auf Computern installiert, die als Masterzielserver dienen. Der Agent fungiert als Kommunikationsanbieter zwischen allen InMage-Komponenten.
	
- **Lokaler sekundärer Standort**:
	- **Konfigurationsserver**: Der Konfigurationsserver ist die erste Komponente, die Sie installieren. Er wird am sekundären Standort installiert, um Ihre Bereitstellung zu verwalten, zu konfigurieren und zu überwachen, und zwar entweder mit der Verwaltungswebsite oder der vContinuum-Konsole. Der Konfigurationsserver enthält auch den Pushmechanismus für die Remotebereitstellung des vereinheitlichten Agents. Es gibt nur einen Konfigurationsserver in einer Bereitstellung, der auf einem Computer mit Windows Server 2012 R2 installiert sein muss.
	- **vContinuum-Server**: Es handelt sich um eine Installation am gleichen Speicherort (sekundärer Standort) wie der Konfigurationsserver. Er enthält eine Konsole zum Verwalten und Überwachen Ihrer geschützten Umgebung. Bei einer standardmäßigen Installation ist der vContinuum-Server der erste Masterzielserver und verfügt über eine Installation des vereinheitlichten Agents.
	- **Masterzielserver**: Der Masterzielserver enthält replizierte Daten. Er empfängt Daten vom Prozessserver und erstellt einen Replikatcomputer am sekundären Standort. Außerdem enthält er die Punkte für die Beibehaltung der Daten. Die Anzahl der benötigten Masterzielserver richtet sich nach der Anzahl der Computer, die Sie schützen. Wenn Sie ein Failback zum primären Standort durchführen möchten, benötigen Sie dort ebenfalls einen Masterzielserver. 

- **Azure**: Sie benötigen auch ein Azure-Abonnement. Laden Sie InMage Scout herunter, um die Bereitstellung nach dem Erstellen des Site Recovery-Tresors einzurichten. Außerdem installieren Sie das neueste Update für alle InMage-Komponentenserver.


	![VMware zu VMware](./media/site-recovery-components/vmware-to-vmware.png)

In diesem Szenario werden Deltareplikationsänderungen vom vereinheitlichten Agent, der auf dem geschützten Computer ausgeführt wird, an den Prozessserver gesendet. Der Prozessserver optimiert diese Daten und überträgt sie an den Masterzielserver am sekundären Standort. Der Konfigurationsserver verwaltet den Replikationsprozess.


## Lebenszyklus des Hyper-V-Schutzes

Dieser Workflow verdeutlicht den Prozess zum Schützen, Replizieren und Durchführen des Failovers für virtuelle Hyper-V-Maschinen.

1. **Aktivieren des Schutzes**: Sie richten den Site Recovery-Tresor ein, konfigurieren die Replikationseinstellungen für eine VMM-Cloud oder eine Hyper-V-Site und aktivieren den Schutz für die VMs. Es wird ein Auftrag mit der Bezeichnung **Schutz aktivieren** initiiert, und er kann auf der Registerkarte **Aufträge** überwacht werden. Im Rahmen des Auftrags wird überprüft, ob die Maschine die Voraussetzungen erfüllt. Anschließend wird die [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)-Methode aufgerufen, mit der die Replikation zu Azure mit den von Ihnen konfigurierten Einstellungen durchgeführt wird. Im Rahmen des Auftrags **Schutz aktivieren** wird auch die [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx)-Methode aufgerufen, um eine vollständige VM-Replikation zu initialisieren.
2. **Anfängliche Replikation**: Es wird eine Momentaufnahme der virtuellen Maschine erstellt, und die virtuellen Festplatten werden einzeln nacheinander repliziert, bis sie alle zu Azure oder in das sekundäre Rechenzentrum kopiert werden. Wie lange dieser Vorgang dauert, richtet sich nach der Größe und Netzwerkbandbreite und der von Ihnen gewählten Methode für die anfängliche Replikation. Falls es während der anfänglichen Replikation zu Festplattenänderungen kommt, werden diese Änderungen mit dem Replication Tracker für Hyper-V-Replikate in Form von Hyper-V-Replikationsprotokollen (.hrl) nachverfolgt, die sich in demselben Ordner wie die Festplatten befinden. Jeder Datenträger verfügt über eine zugeordnete HRL-Datei, die an den sekundären Speicher gesendet wird. Beachten Sie, dass die Momentaufnahme- und Protokolldateien Festplattenressourcen belegen, während die Replikation durchgeführt wird. Nach Abschluss der anfänglichen Replikation wird die VM-Momentaufnahme gelöscht, und die Festplatten-Deltaänderungen im Protokoll werden synchronisiert und zusammengeführt.
3. **Fertigstellen des Schutzes**: Nach die anfängliche Replikation abgeschlossen ist, werden mit dem Auftrag zum **Fertigstellen des Schutzes** das Netzwerk und andere Einstellungen nach der Replikation konfiguriert, und die virtuelle Maschine ist geschützt. Wenn Sie eine Replikation zu Azure durchführen, müssen Sie die Einstellungen für die virtuelle Maschine unter Umständen so optimieren, dass sie bereit für das Failover ist. An diesem Punkt können Sie ein Testfailover durchführen, um zu überprüfen, ob alles wie erwartet funktioniert.
4. **Replikation**: Nach der anfänglichen Replikation wird die Deltasynchronisierung gemäß den Replikationseinstellungen und der dazugehörigen Methode durchgeführt. 
	- **Replikationsfehler**: Wenn die Deltareplikation fehlschlägt und eine vollständige Replikation einen hohen Aufwand in Bezug auf die Bandbreite oder Dauer bedeuten würde, wird eine Neusynchronisierung durchgeführt. Wenn die HRL-Dateien beispielsweise 50 % des Festplattenspeichers füllen, wird die virtuelle Maschine für die Neusynchronisierung gekennzeichnet. Bei der Neusynchronisierung wird die Menge der gesendeten Daten verringert, indem Prüfsummen für die virtuellen Quell- und Zielmaschinen berechnet werden und nur das Delta gesendet wird. Nach Abschluss der Neusynchronisierung sollte die Deltareplikation fortgesetzt werden. Standardmäßig ist die Neusynchronisierung so geplant, dass sie automatisch außerhalb der Geschäftszeiten durchgeführt wird, aber Sie können eine virtuelle Maschine auch manuell neu synchronisieren.
	- **Replikationsfehler**: Wenn ein Replikationsfehler auftritt, wird die integrierte Wiederholungsfunktion verwendet. Bei Auftreten eines Fehlers, für den keine Wiederherstellung möglich ist, z. B. ein Authentifizierungs- oder Autorisierungsfehler oder eine Replikatmaschine in einem ungültigen Zustand, wird nicht versucht, den Vorgang zu wiederholen. Bei einem Fehler, bei dem eine Wiederherstellung möglich ist, z. B. einem Netzwerkfehler oder einer geringen Menge an verbleibendem Festplattenspeicher/Arbeitsspeicher, wird eine Wiederholung mit zunehmenden Intervallen durchgeführt (1, 2, 4, 8, 10 und dann alle 30 Minuten).
4. **Geplante/ungeplante Failover**: Sie führen geplante/ungeplante Failover aus, wenn dies erforderlich ist. Wenn Sie ein geplantes Failover ausführen, werden die Quell-VMs heruntergefahren, um sicherzustellen, dass kein Datenverlust auftritt. Nach der Erstellung der Replikat-VMs weist deren Status darauf hin, dass ein Commit aussteht. Sie müssen den Commit-Vorgang durchführen, um das Failover abzuschließen, es sei denn, Sie verwenden SAN. In diesem Fall wird der Commit-Vorgang automatisch durchgeführt. Nachdem der primäre Standort betriebsbereit ist, kann ein Failback durchgeführt werden. Wenn Sie zu Azure repliziert haben, erfolgt die umgekehrte Replikation automatisch. Starten Sie andernfalls selbst eine umgekehrte Replikation.
 

![Workflow](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Nächste Schritte

[Bereiten Sie sich auf die Bereitstellung vor](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0218_2016-->