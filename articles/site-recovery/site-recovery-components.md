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
	ms.date="03/27/2016"
	ms.author="raynew"/>

# Wie funktioniert Azure Site Recovery?

Lesen Sie diesen Artikel, um sich über die zugrunde liegende Architektur des Azure Site Recovery-Diensts und die dafür erforderlichen Komponenten zu informieren.

Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) veröffentlichen.


## Übersicht

Organisationen benötigen eine Strategie für die Bereiche Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR), mit der bestimmt wird, wie Apps, Workloads und Daten bei geplanten und ungeplanten Ausfallzeiten verfügbar bleiben und so schnell wie möglich die normalen Arbeitsbedingungen wiederhergestellt werden können.

Site Recovery ist ein Azure-Dienst, der einen Beitrag zu Ihrer BCDR-Strategie leistet, indem die Replikation von lokalen physischen Servern und virtuellen Computern in die Cloud (Azure) oder an einem sekundären Standort orchestriert wird. Wenn es an Ihrem primären Standort zu Ausfällen kommt, wird ein Failover zum sekundären Standort durchgeführt, um die Verfügbarkeit von Apps und Workloads zu erhalten. Wenn wieder Normalbetrieb herrscht, führen Sie das Failback zum primären Standort durch.

Site Recovery kann bereitgestellt werden, um die Replikation in unterschiedlichen Szenarien zu orchestrieren:

- **Replizieren von virtuellen VMware-Computern**: Sie können lokale virtuelle VMware-Computer in [Azure](site-recovery-vmware-to-azure-classic.md) oder einem [sekundären Rechenzentrum](site-recovery-vmware-to-vmware.md) replizieren.
- **Replizieren von physischen Computern**: Sie können physische Computer mit Windows oder Linux in [Azure](site-recovery-vmware-to-azure-classic.md) oder einem [sekundären Rechenzentrum](site-recovery-vmware-to-vmware.md) replizieren.
- **Replizieren von Hyper-V-VMs, die in System Center VMM-Clouds verwaltet werden**: Sie können lokale virtuelle Hyper-V-Computer in VMM-Clouds in [Azure](site-recovery-vmm-to-azure.md) oder einem [sekundären Rechenzentrum](site-recovery-vmm-to-vmm.md) replizieren. 
- **Replizieren von Hyper-V-VMs (ohne VMM)**: Sie können Hyper-V-VMs, die nicht per VMM verwaltet werden, in [Azure](site-recovery-hyper-v-site-to-azure.md) replizieren.
- **Migrieren von VMs**: Sie können Site Recovery verwenden, um [Azure-IaaS-VMs](site-recovery-migrate-azure-to-azure.md) zwischen Regionen zu migrieren oder um [AWS Windows-Instanzen](site-recovery-migrate-aws-to-azure.md) zu Azure-IaaS-VMs zu migrieren. Derzeit wird nur die Migration unterstützt. Dies bedeutet, dass Sie für diese VMs nur ein Failover durchführen können, aber kein Failback.

Site Recovery kann die meisten auf diesen virtuellen Maschinen und physischen Servern ausgeführten Apps replizieren. Eine vollständige Zusammenfassung der unterstützten Apps finden Sie unter [Welche Workloads können mit Azure Site Recovery geschützt werden?](site-recovery-workload.md).

## Replizieren lokaler virtueller VMware-Computer oder physischer Server in Azure

Derzeit sind zwei verschiedene Architekturen für die Replikation von VMware-VMs oder physischen Windows- oder Linux-Servern zu Azure verfügbar:

- [Legacy-Architektur](site-recovery-vmware-to-azure-classic-legacy.md): Diese Architektur sollte für neue Bereitstellungen nicht verwendet werden. 
- [Verbesserte Architektur](site-recovery-vmware-to-azure-classic.md): Dies ist die aktuelle Architektur, die für alle neuen Bereitstellungen empfohlen wird. Wenn Sie dieses Szenario bereits mit der Legacyarchitektur bereitgestellt haben, können Sie sich [über die Migration zur erweiterten Bereitstellung informieren](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment).

Bei der erweiterten Bereitstellung müssen Sie einen lokalen Verwaltungsserver mit allen Site Recovery-Komponenten einrichten. Auf jedem Computer, den Sie schützen möchten, führen Sie automatisch einen Pushvorgang (bzw. die manuelle Installation) des Mobilitätsdiensts durch. Nach der ersten Replikation sendet der Mobilitätsdienst auf dem Computer Deltareplikationsdaten an den Prozessserver, wo diese optimiert werden, bevor sie an den Azure-Speicher gesendet werden.

![Verbessert](./media/site-recovery-components/arch-enhanced.png) ![Verbessert](./media/site-recovery-components/arch-enhanced2.png)

### Lokal
Lokal benötigen Sie Folgendes:

- **Verwaltungsserver**: Sie benötigen einen Windows Server 2012 R2-Computer, der als Verwaltungsserver dient. Auf diesem Server installieren Sie alle unten angegebenen Site Recovery-Komponenten mithilfe einer einzigen Installationsdatei:

	- **Konfigurationsserverkomponente**: Koordiniert die Kommunikation zwischen Ihrer lokalen Umgebung und Azure und verwaltet die Datenreplikation und Wiederherstellung.
	- **Prozessserverkomponente**: Fungiert als Replikationsgateway. Sie empfängt Replikationsdaten aus geschützten Quellcomputern, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet die Daten an den Azure-Speicher. Außerdem wickelt er die Pushinstallation des Mobilitätsdiensts auf geschützten Computern ab und führt die automatische Ermittlung von virtuellen VMware-Computern durch. Bei zunehmender Größe der Bereitstellung können Sie zusätzlich separate dedizierte Prozessserver hinzufügen, um steigende Mengen von Replikationsdatenverkehr zu bewältigen.
	- **Masterzielserver-Komponente**: Behandelt die Replikationsdaten beim Failback von Azure. 
- **VMware ESX/ESXi-Hosts und vCenter-Server**: Sie benötigen mindestens einen ESX/ESXi-Hostserver, auf dem VMware-VMs ausgeführt werden. Zum Verwalten dieser Hosts empfiehlt sich die Bereitstellung eines vCenter-Servers. **Hinweis:** **Auch beim Replizieren von physischen Servern müssen Sie dafür ein Failback auf VMware durchführen**. Wenn Sie einen physischen Server replizieren, wird er bei einem Failover in Azure als Azure-VM ausgeführt. Das Failback zum lokalen Standort erfolgt als VMware-VM. 
	
- **VMs/physische Server**: Für jeden Computer, den Sie in Azure replizieren möchten, muss die Mobilitätsdienstkomponente installiert sein. Dieser Dienst erfasst die Datenschreibvorgänge auf dem Computer und sendet sie an den Prozessserver. Die Komponente kann manuell oder mittels Pushvorgang automatisch durch den Prozessserver installiert werden, wenn die Replikation für den Computer aktiviert wird.

### Azure

In der Azure-Infrastruktur benötigen Sie Folgendes: - **Azure-Konto**: Sie benötigen ein Microsoft Azure-Konto. - **Azure-Speicher**: Sie benötigen ein Azure-Speicherkonto zum Speichern von replizierten Daten. Replizierte Daten werden im Azure-Speicher gespeichert, und Azure-VMs werden bereitgestellt, wenn ein Failover auftritt. - **Azure-Netzwerk**: Sie benötigen ein virtuelles Azure-Netzwerk, mit dem bereitgestellte Azure-VMs bei einem Failover eine Verbindung herstellen.
	
	
### Failback

Das Failback an lokale Standorte erfolgt immer auf VMware-VMs. Dies gilt auch, wenn das Failover für einen physischen Server durchgeführt wurde. Sie benötigen Folgendes:

- **Temporärer Prozessserver in Azure**: Wenn Sie nach einem Failover ein Failback aus Azure durchführen möchten, müssen Sie eine als Prozessserver konfigurierte Azure-VM für die Replikation aus Azure einrichten. Nach Beendigung des Failbacks können Sie diese VM löschen.
- **VPN-Verbindung**: Für das Failback benötigen Sie eine VPN-Verbindung (oder Azure ExpressRoute) zwischen dem Azure-Netzwerk und dem lokalen Standort.
- **Separater lokaler Masterzielserver**: Der lokale Masterzielserver führt das Failback durch. Der Masterzielserver wird standardmäßig auf dem Verwaltungsserver installiert, aber wenn Sie Failbacks für größere Mengen von Datenverkehr durchführen, sollten Sie für diese Zwecke einen separaten lokalen Masterzielserver einrichten. 

![Verbessertes Failback](./media/site-recovery-components/enhanced-failback.png)

[Erfahren Sie mehr](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) über die erweiterten Voraussetzungen für die Bereitstellung. [Erfahren Sie mehr](site-recovery-failback-azure-to-vmware-classic.md) über das Failback für die erweiterte Bereitstellung.




## Replizieren Sie Hyper-V-VMs in VMM-Clouds zu Azure.

Zum Bereitstellen dieses Szenarios während der Site Recovery-Bereitstellung installieren Sie den Azure Site Recovery-Anbieter auf dem VMM-Server. Der Anbieter koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Der Azure Recovery Services-Agent ist während der Bereitstellung von Site Recovery auf dem Hyper-V-Hostserver installiert, und die Daten werden über HTTPS 443 zwischen ihm und dem dem Azure-Speicher repliziert. Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt. Die replizierten Daten im Azure-Speicher werden ebenfalls verschlüsselt.

- Lokal: 
	- **VMM-Server**: Mindestens einen VMM-Server, für den mindestens eine private VMM-Cloud eingerichtet ist. Der Server sollte unter System Center 2012 R2 ausgeführt werden und über eine Internetverbindung verfügen. Richten Sie die Netzwerkzuordnung ein, damit Azure-VMs nach einem Failover mit einem Netzwerk verbunden sind. Hierfür sollten Ihre Quell-VMs mit einem VMM-VM-Netzwerk verbunden sein. Dieses VM-Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist.
	- **Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver in der VMM-Cloud. Hyper-V-Hosts müssen Windows Server 2012 R2 ausführen.
	- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss mindestens eine zu schützende VM enthalten.
	
- Azure:
	- **Azure-Konto**: Sie benötigen ein Microsoft Azure-Konto.
	- **Azure-Speicher**: Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Computer werden bei einem Failover hochgefahren.
	- **Azure-Netzwerk**: Wenn Sie die Netzwerkzuordnung so einrichten möchten, dass Azure-VMs nach einem Failover mit einem Netzwerk verbunden werden, müssen Sie ein Azure-Netzwerk einrichten.

	![VMM zu Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

Erfahren Sie mehr über die genauen [Voraussetzungen für die Bereitstellung](site-recovery-vmm-to-azure.md#before-you-start).

## Replizieren virtueller VMware-Maschinen oder physischer Server an einem sekundären Standort

Zum Replizieren von VMware-VMs oder physischen Windows/Linux-Servern an einem sekundären Standort laden Sie die Anwendung InMage Scout herunter, die im Azure Site Recovery-Abonnement enthalten ist. Sie richten die Komponentenserver an jedem Standort ein (Konfiguration, Prozess, Masterziel) und installieren den vereinheitlichten Agent auf Computern, die Sie replizieren möchten. Nach der ersten Replikation sendet der Agent auf jedem Computer die Deltareplikationsänderungen an den Prozessserver. Der Prozessserver optimiert die Daten und überträgt sie an den Masterzielserver am sekundären Standort. Der Konfigurationsserver verwaltet den Replikationsprozess.

![VMware zu VMware](./media/site-recovery-components/vmware-to-vmware.png)

### Lokaler primärer Standort

- **Prozessserver**: Richten Sie die Prozessserverkomponente an Ihrem primären Standort ein, um das Caching, die Komprimierung und die Datenoptimierung zu behandeln. Außerdem erfolgt hierüber die Pushinstallation des vereinheitlichten Agents auf Maschinen, die Sie schützen möchten. 
- **VMware ESX/ESXi und vCenter Server**: Zum Schutz von VMware-VMs benötigen Sie einen VMware EXS/ESXi-Hypervisor und optional einen VMware vCenter-Server zum Verwalten von Hypervisoren.
- **VMs/physische Server**: Auf VMware-VMs oder physischen Windows/Linux-Servern, die geschützt werden sollen, muss der vereinheitlichte Agent installiert sein. Der vereinheitlichte Agent wird auch auf Computern installiert, die als Masterzielserver dienen. Der Agent fungiert als Kommunikationsanbieter zwischen allen Komponenten. 
	
### Lokaler sekundärer Standort
 
- **Konfigurationsserver**: Der Konfigurationsserver ist die erste Komponente, die Sie installieren. Er wird am sekundären Standort installiert, um Ihre Bereitstellung zu verwalten, zu konfigurieren und zu überwachen, und zwar entweder mit der Verwaltungswebsite oder der vContinuum-Konsole. Es gibt nur einen Konfigurationsserver in einer Bereitstellung, der auf einem Computer mit Windows Server 2012 R2 installiert sein muss.
- **vContinuum-Server**: Der Server ist an demselben Ort (sekundärer Standort) wie der Konfigurationsserver installiert. Er enthält eine Konsole zum Verwalten und Überwachen Ihrer geschützten Umgebung. Bei einer standardmäßigen Installation ist der vContinuum-Server der erste Masterzielserver und verfügt über eine Installation des vereinheitlichten Agents.
- **Masterzielserver**: Der Masterzielserver enthält replizierte Daten. Er empfängt Daten vom Prozessserver und erstellt einen Replikatcomputer am sekundären Standort. Außerdem enthält er die Punkte für die Beibehaltung der Daten. Die Anzahl der benötigten Masterzielserver richtet sich nach der Anzahl der Computer, die Sie schützen. Wenn Sie ein Failback zum primären Standort durchführen möchten, benötigen Sie dort ebenfalls einen Masterzielserver. 

### Azure

Sie stellen dieses Szenario mit InMage Scout bereit. Hierfür benötigen Sie ein Azure-Abonnement. Nachdem Sie einen Site Recovery-Tresor erstellt haben, laden Sie InMage Scout herunter und installieren die aktuellen Updates, um die Bereitstellung einzurichten.


## Replizieren von Hyper-V-VMs zu Azure (ohne VMM)

Zum Replizieren von nicht in VMM-Clouds verwalteten Hyper-V-VMs in Azure installieren Sie den Azure Site Recovery-Anbieter und den Azure Recovery Services-Agent während der Site Recovery-Bereitstellung auf dem Hyper-V-Host. Der Anbieter koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Der-Agent verarbeitet die Replikation von Daten über HTTPS 443. Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt. Die replizierten Daten im Azure-Speicher werden ebenfalls verschlüsselt.

![Hyper-V-Standort zu Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

### Lokal

- **Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver. Hyper-V-Hosts müssen Windows Server 2012 R2 ausführen.
- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss mindestens eine zu schützende VM enthalten.
	
### Azure

- **Azure-Konto**: Sie benötigen ein Microsoft Azure-Konto.
- **Azure-Speicher**: Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Computer werden bei einem Failover hochgefahren.

[Erfahren Sie mehr](site-recovery-hyper-v-site-to-azure.md#before-you-start) über die Voraussetzungen für die Bereitstellung.


## Replizieren Sie Hyper-V-VMs in VMM-Clouds zu Azure.

Zum Bereitstellen dieses Szenarios installieren Sie während der Site Recovery-Bereitstellung den Azure Site Recovery-Anbieter auf dem VMM-Server und den Azure Recovery Services-Agent auf dem Hyper-V-Host. Der Anbieter koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Der-Agent verarbeitet die Replikation von Daten über HTTPS 443. Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt. Replizierte Daten im Azure-Speicher (im Ruhezustand) werden ebenfalls verschlüsselt.

![VMM zu Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

### Lokal

- **VMM-Server**: Mindestens einen VMM-Server, für den mindestens eine private VMM-Cloud eingerichtet ist. Der Server sollte unter System Center 2012 R2 ausgeführt werden und über eine Internetverbindung verfügen. Richten Sie die Netzwerkzuordnung ein, damit Azure-VMs nach einem Failover mit einem Netzwerk verbunden sind. Dafür müssen Sie Quell-VMs mit einem VMM-VM-Netzwerk verbinden. Dieses Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist.
- **Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver in der VMM-Cloud. Hyper-V-Hosts müssen Windows Server 2012 R2 ausführen.
- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss mindestens eine zu schützende VM enthalten.
	
### Azure

- **Azure-Konto**: Sie benötigen ein Microsoft Azure-Konto.
- **Azure-Speicher**: Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Computer werden bei einem Failover hochgefahren.
- **Azure-Netzwerk**: Richten Sie die Netzwerkzuordnung ein, damit Azure-VMs nach einem Failover mit einem Netzwerk verbunden sind. Hierfür benötigen Sie ein Azure-Netzwerk.

[Erfahren Sie mehr](site-recovery-vmm-to-azure.md#before-you-start) über die Voraussetzungen für die Bereitstellung.

## Replizieren von Hyper-V-VMs in ein sekundäres Rechenzentrum

Zum Bereitstellen dieses Szenarios während der Site Recovery-Bereitstellung installieren Sie den Azure Site Recovery-Anbieter auf dem VMM-Server. Der Anbieter koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Die Daten werden zwischen dem primären und sekundären Hyper-V-Hostserver über das LAN oder VPN per Kerberos- oder Zertifikatauthentifizierung repliziert. Die Kommunikation vom Anbieter und zwischen den Hyper-V-Hostservern ist jeweils sicher und verschlüsselt.

![Lokal zu lokal](./media/site-recovery-components/arch-onprem-onprem.png)

### Lokal

- **VMM-Server**: Es wird empfohlen, einen VMM-Server am primären Standort und einen am sekundären Standort zu verwenden, die jeweils mindestens eine private VMM-Cloud enthalten. Auf dem Server sollte mindestens System Center 2012 SP1 mit den neuesten Updates und einer Internetverbindung ausgeführt werden. Für die Clouds sollte das Hyper-V-Funktionsprofil eingestellt sein.
- **Hyper-V Server**: Hyper-V-Hostserver sollten in der primären sowie in der sekundären VMM-Cloud angeordnet sein. Die Hostserver müssen mindestens mit Windows Server 2012 mit den neuesten Updates ausgeführt werden und über eine Internetverbindung verfügen.
- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss mindestens eine zu schützende VM enthalten.
	
### Azure

Sie benötigen ein Azure-Abonnement.

[Erfahren Sie mehr](site-recovery-vmm-to-vmm.md#before-you-start) über die Voraussetzungen für die Bereitstellung.


## Replizieren von Hyper-V-VMs in ein sekundäres Rechenzentrum per SAN-Replikation

Bei diesem Szenario installieren Sie den Azure Site Recovery-Anbieter während der Bereitstellung von Site Recovery auf den VMM-Servern. Der Anbieter koordiniert und orchestriert die Replikation mit dem Site Recovery-Dienst über das Internet. Die Daten werden zwischen den primären und sekundären Speicherarrays per synchroner SAN-Replikation repliziert.

![SAN-Replikation](./media/site-recovery-components/arch-onprem-onprem-san.png)

### Lokal

- **SAN-Array**: Ein [unterstütztes SAN-Array](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), das vom primären VMM-Server verwaltet wird. Das SAN teilt eine Netzwerkinfrastruktur mit einem anderen SAN-Array am sekundären Standort.
- **VMM-Server**: Es wird empfohlen, einen VMM-Server am primären Standort und einen am sekundären Standort zu verwenden, die jeweils mindestens eine private VMM-Cloud enthalten. Auf dem Server sollte mindestens System Center 2012 SP1 mit den neuesten Updates und einer Internetverbindung ausgeführt werden. Für die Clouds sollte das Hyper-V-Funktionsprofil eingestellt sein.
- **Hyper-V Server**: Hyper-V-Hostserver in der primären sowie in der sekundären VMM-Cloud. Die Hostserver müssen mindestens mit Windows Server 2012 mit den neuesten Updates ausgeführt werden und über eine Internetverbindung verfügen.
- **Geschützte Computer**: Der Quell-Hyper-V-Hostserver muss mindestens eine zu schützende VM enthalten.
	
### Azure

Sie benötigen ein Azure-Abonnement.

[Erfahren Sie mehr](site-recovery-vmm-san.md#before-you-start) über die Voraussetzungen für die Bereitstellung.


## Lebenszyklus des Hyper-V-Schutzes

Dieser Workflow verdeutlicht den Prozess zum Schützen, Replizieren und Durchführen des Failovers für virtuelle Hyper-V-Maschinen.

1. **Aktivieren des Schutzes**: Sie richten den Site Recovery-Tresor ein, konfigurieren die Replikationseinstellungen für eine VMM-Cloud oder eine Hyper-V-Site und aktivieren den Schutz für die VMs. Es wird ein Auftrag mit der Bezeichnung **Schutz aktivieren** initiiert, und er kann auf der Registerkarte **Aufträge** überwacht werden. Im Rahmen des Auftrags wird überprüft, ob die Maschine die Voraussetzungen erfüllt. Anschließend wird die [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)-Methode aufgerufen, mit der die Replikation zu Azure mit den von Ihnen konfigurierten Einstellungen durchgeführt wird. Im Rahmen des Auftrags **Schutz aktivieren** wird auch die [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx)-Methode aufgerufen, um eine vollständige VM-Replikation zu initialisieren.
2. **Anfängliche Replikation**: Es wird eine Momentaufnahme der virtuellen Maschine erstellt, und die virtuellen Festplatten werden einzeln nacheinander repliziert, bis sie alle zu Azure oder in das sekundäre Rechenzentrum kopiert werden. Wie lange dieser Vorgang dauert, richtet sich nach der VM-Größe, der Netzwerkbandbreite und der Methode für die anfängliche Replikation. Falls es während der anfänglichen Replikation zu Festplattenänderungen kommt, werden diese Änderungen mit dem Replication Tracker für Hyper-V-Replikate in Form von Hyper-V-Replikationsprotokollen (.hrl) nachverfolgt, die sich in demselben Ordner wie die Festplatten befinden. Jeder Datenträger verfügt über eine zugeordnete HRL-Datei, die an den sekundären Speicher gesendet wird. Beachten Sie, dass die Momentaufnahme- und Protokolldateien Festplattenressourcen belegen, während die Replikation durchgeführt wird. Nach Abschluss der anfänglichen Replikation wird die VM-Momentaufnahme gelöscht, und die Festplatten-Deltaänderungen im Protokoll werden synchronisiert und zusammengeführt.
3. **Fertigstellen des Schutzes**: Nachdem die anfängliche Replikation abgeschlossen ist, werden mit dem Auftrag zum **Fertigstellen des Schutzes** das Netzwerk und andere Einstellungen nach der Replikation konfiguriert, damit der virtuelle Computer geschützt ist. Wenn Sie eine Replikation zu Azure durchführen, müssen Sie die Einstellungen für die virtuelle Maschine unter Umständen so optimieren, dass sie bereit für das Failover ist. An diesem Punkt können Sie ein Testfailover durchführen, um zu überprüfen, ob alles wie erwartet funktioniert.
4. **Replikation**: Nach der ersten Replikation beginnt die Deltasynchronisierung gemäß den Replikationseinstellungen. 
	- **Replikationsfehler**: Wenn die Deltareplikation fehlschlägt und eine vollständige Replikation einen hohen Aufwand in Bezug auf die Bandbreite oder Dauer bedeuten würde, wird eine Neusynchronisierung durchgeführt. Wenn die HRL-Dateien beispielsweise 50% des Festplattenspeichers füllen, wird die VM für die Neusynchronisierung gekennzeichnet. Bei der Neusynchronisierung wird die Menge der gesendeten Daten verringert, indem Prüfsummen für die virtuellen Quell- und Zielmaschinen berechnet werden und nur das Delta gesendet wird. Nach Abschluss der Neusynchronisierung wird die Deltareplikation fortgesetzt. Standardmäßig ist die Neusynchronisierung so geplant, dass sie automatisch außerhalb der Geschäftszeiten durchgeführt wird, aber Sie können eine virtuelle Maschine auch manuell neu synchronisieren.
	- **Replikationsfehler**: Wenn ein Replikationsfehler auftritt, wird die integrierte Wiederholungsfunktion verwendet. Bei Auftreten eines Fehlers, für den keine Wiederherstellung möglich ist, z.B. ein Authentifizierungs- oder Autorisierungsfehler oder ein Replikatcomputer in einem ungültigen Zustand, wird nicht versucht, den Vorgang zu wiederholen. Bei einem Fehler, bei dem eine Wiederherstellung möglich ist, z.B. einem Netzwerkfehler oder einer geringen Menge an verbleibendem Festplattenspeicher/Arbeitsspeicher, wird eine Wiederholung mit zunehmenden Intervallen durchgeführt (1, 2, 4, 8, 10 und dann alle 30 Minuten).
4. **Geplante/ungeplante Failover**: Sie können geplante oder ungeplante Failover je nach Bedarf durchführen. Wenn Sie ein geplantes Failover durchführen, werden die Quell-VMs heruntergefahren, um sicherzustellen, dass kein Datenverlust auftritt. Nach der Erstellung der Replikat-VMs werden diese in den Zustand „Commit ausstehend“ versetzt. Sie müssen den Commit-Vorgang durchführen, um das Failover abzuschließen, es sei denn, Sie verwenden SAN. In diesem Fall wird der Commit-Vorgang automatisch durchgeführt. Nachdem der primäre Standort betriebsbereit ist, kann ein Failback durchgeführt werden. Wenn Sie zu Azure repliziert haben, erfolgt die umgekehrte Replikation automatisch. Starten Sie andernfalls manuell eine umgekehrte Replikation.
 

![Workflow](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Nächste Schritte

[Vorbereiten der Bereitstellung](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0330_2016-->