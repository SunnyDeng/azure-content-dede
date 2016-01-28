<properties
	pageTitle="Replizieren von virtuellen VMware-Computern und physischen Servern in Azure mithilfe von Azure Site Recovery | Microsoft Azure" 
	description="Beschreibt, wie Azure Site Recovery bereitgestellt wird, um die Replikation, das Failover und die Wiederherstellung von lokalen virtuellen VMware-Computern und physischen Windows-/Linux-Servern in Azure aufeinander abzustimmen." 
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
	ms.topic="article"
	ms.date="01/14/2016"
	ms.author="raynew"/>

# Replizieren von virtuellen VMware-Computern und physischen Servern in Azure mithilfe von Azure Site Recovery

> [AZURE.SELECTOR]
- [Enhanced](site-recovery-vmware-to-azure-classic.md)
- [Legacy](site-recovery-vmware-to-azure-classic-legacy.md)

Der Dienst Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem sekundären lokalen Datencenter repliziert werden. Eine kurze Übersicht über das Gesamtthema finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md).

## Übersicht

Dieser Artikel beschreibt folgende Vorgehensweisen:

- **Replizieren virtueller VMware-Computer in Azure**: Sie stellen Site Recovery bereit, um die Replikation, das Failover und die Wiederherstellung von lokalen virtuellen VMware-Computern im Azure Speicher zu koordinieren.
- **Replizieren physischer Server in Azure**: Sie stellen Azure Site Recovery bereit, um die Replikation, das Failover und die Wiederherstellung von lokalen physischen Windows- und Linux-Servern in Azure zu koordinieren.

>[AZURE.NOTE]In diesem Artikel wird beschrieben, wie Sie eine Replikation zu Azure ausführen. Wenn Sie virtuelle VMware-Computer oder physische Windows- und Linux-Server in einem sekundären Datencenter replizieren möchten, befolgen Sie die Anweisungen in [diesem Artikel](site-recovery-vmware-to-vmware.md).

Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) veröffentlichen.

## Erweiterte Bereitstellung 

Dieser Artikel enthält Anweisungen für eine erweiterte Bereitstellung im klassischen Azure-Portal. Wir empfehlen, dass Sie diese Version für alle neuen Bereitstellungen verwenden. Wenn Ihre Bereitstellung mit der älteren Version durchgeführt wurde, empfehlen wir eine Migration zur neuen Version. Erfahren Sie [mehr](site-recovery-vmware-to-azure-classic-legacy.md) über die Migration.

Bei der erweiterten Bereitstellung handelt es sich um ein umfangreiches Update. Es folgt eine Zusammenfassung der Verbesserungen, die wir vorgenommen haben:

- **Keine Infrastruktur-VMs in Azure**: Die Daten werden direkt in einem Azure-Speicherkonto repliziert. Es ist nicht notwendig, in Azure Infrastruktur-VMs für Replikation und Failover einzurichten.  
- **Einheitliche Installation**: Ein einziger Installationsvorgang ermöglicht eine einfache Einrichtung mit Skalierbarkeit für lokale Komponenten.
- **Sichere Bereitstellung**: Der gesamte Datenverkehr wird verschlüsselt, und die Replikationsverwaltungskommunikation wird über HTTPS 443 übermittelt.
- **Wiederherstellungspunkte**: Unterstützung für absturz- und anwendungskonsistente Wiederherstellungspunkte in Windows-und Linux-Umgebungen sowie Unterstützung für Konfigurationen mit Einzel-VM- und Multi-VM-Konsistenz.
- **Testfailover**: Unterstützung für ein unterbrechungsfreies Testfailover zu Azure ohne Beeinträchtigung der Produktion oder Unterbrechung der Replikation.
- **Ungeplantes Failover**: Unterstützung für ein ungeplantes Failover zu Azure mit einer erweiterten Option zum automatischen Herunterfahren von virtuellen Computern (VMs, Virtual Machines) vor dem Failover.
- **Failback**: Das integrierte Failback repliziert nur die geänderten Daten (Deltaänderungen) zum lokalen Standort.
- **vSphere 6.0**: Eingeschränkte Unterstützung für VMware vSphere 6.0-Bereitstellungen.


## Wie trägt Site Recovery zum Schutz von virtuellen Computern und physischen Servern bei?


- VMware-Administratoren können für die auf virtuellen VMware-Computern ausgeführten geschäftlichen Workloads und Anwendungen einen externen Schutz in Azure konfigurieren. Servermanager können lokale physische Windows- und Linux-Server in Azure replizieren.
- Über die Azure Site Recovery-Konsole können Sie die Vorgänge für Replikation, Failover und Wiederherstellung einfach und zentral einrichten und verwalten.
- Wenn Sie virtuelle VMware-Computer replizieren, die von einem vCenter-Server verwaltet werden, kann Site Recovery diese virtuellen Computer automatisch ermitteln. Wenn sich Computer auf einem ESXi-Host befinden, ermittelt Site Recovery die virtuellen Computer auf dem Host.
- Führen Sie einfache Failovers von der lokalen Infrastruktur zu Azure aus, und führen Sie ein Failback (Wiederherstellung) von Azure zu VMware-VM-Servern am lokalen Standort durch. 
- Konfigurieren Sie Wiederherstellungspläne, die Anwendungsworkloads von verschiedenen Computern in Gruppen zusammenfassen. Sie können für diese Pläne ein Failover durchführen. Site Recovery sorgt dann für die Konsistenz zwischen den verschiedenen virtuellen Computern (Multi-VM-Konsistenz). Dadurch können die Computer, die die gleichen Workloads ausführen, gemeinsam an einem konsistenten Datenpunkt wiederhergestellt werden.

## Szenarioarchitektur

Komponenten des Szenarios:

- **Ein lokaler Verwaltungsserver**: Der Verwaltungsserver führt folgende Site Recovery-Komponenten aus:
	- **Konfigurationsserver**: Koordiniert die Kommunikation und verwaltet die Datenreplikations- und Wiederherstellungsvorgänge.
	- **Prozessserver**: Fungiert als Replikationsgateway. Er empfängt Daten aus geschützten Quellcomputern, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet die Replikationsdaten an den Azure-Speicher. Außerdem wickelt er die Pushinstallation des Mobilitätsdiensts auf geschützten Computern ab und führt die automatische Ermittlung von virtuellen VMware-Computern durch.
	- **Masterzielserver**: Behandelt die Replikationsdaten beim Failback von Azure. Sie können auch einen Verwaltungsserver bereitstellen, der ausschließlich als Prozessserver fungiert, um Ihre Bereitstellung zu skalieren.
- **Der Mobilitätsdienst**: Diese Komponente wird auf jedem Computer (VMware-VM oder physischer Server) bereitgestellt, den Sie in Azure replizieren möchten. Er erfasst die Datenschreibvorgänge auf dem Computer und sendet sie an den Prozessserver.
- **Azure**: Sie brauchen keine virtuellen Azure-Computer für Replikation und Failover zu erstellen. Der Site Recovery-Dienst übernimmt die Datenverwaltung, und die Daten werden direkt im Azure-Speicher repliziert. Die replizierten virtuellen Azure-Computer werden automatisch hochgefahren, aber nur, wenn ein Failover zu Azure stattfindet. Wenn Sie jedoch ein Failback von Azure zum lokalen Standort durchführen möchten, müssen Sie einen virtuellen Azure-Computer einrichten, der als Prozessserver fungiert.


Das Diagramm zeigt, wie diese Komponenten zusammenwirken.

![Architektur](./media/site-recovery-vmware-to-azure-classic/architecture.png)

## Kapazitätsplanung

Bei der Kapazitätsplanung sollten Sie folgende Aspekte berücksichtigen:

- **Die Quellumgebung**: Die Kapazitätsplanung oder die VMware-Infrastruktur und Anforderungen an die Quellcomputer.
- **Der Verwaltungsserver**: Die Planung für die lokalen Verwaltungsserver, die die Site Recovery-Komponenten ausführen.
- **Netzwerkbandbreite von der Quelle zum Ziel**: Die Planung für die Netzwerkbandbreite, die für die Replikation zwischen Quelle und Azure benötigt wird.

### Überlegungen zur Quellumgebung

- **Maximale tägliche Änderungsrate**: Ein geschützter Computer kann nur einen einzigen Prozessserver verwenden, und ein einzelner Prozessserver kann eine tägliche Änderungsrate von bis zu 2 TB verarbeiten. Daher beträgt die maximale Datenänderungsrate pro Tag, die für einen geschützten Computer unterstützt wird, 2 TB.
- **Maximaler Durchsatz**: Ein replizierter Computer kann zu einem einzigen Speicherkonto in Azure gehören. Ein Standardspeicherkonto kann maximal 20.000 Anforderungen pro Sekunde verarbeiten, und es wird empfohlen, dass Sie die Anzahl von IOPS über einen Quellcomputer hinweg bei 20.000 halten. Wenn Ihr Quellcomputer z. B. 5 Datenträger hat und jeder Datenträger 120 IOPS (mit einer Größe von 8 KB) für die Quelle generiert, wird das Azure-Limit von 500 IOPS pro Datenträger eingehalten. Erforderliche Anzahl von Speicherkonten = Quell-IOPS insgesamt/20000. 
 

### Überlegungen zu Verwaltungsservern

Auf dem Verwaltungsserver werden die Site Recovery-Komponenten ausgeführt, die die Datenoptimierung, die Replikation und die Verwaltung abwickeln. Der Verwaltungsserver muss in der Lage sein, die tägliche Änderungsrate für alle auf geschützten Computern ausgeführten Workloads zu verarbeiten, und er muss über genügend Bandbreite zum ständigen Replizieren der Daten im Azure-Speicher verfügen. Dies bedeutet Folgendes:

- Der Prozessserver empfängt Replikationsdaten aus geschützten Computern und optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung, bevor er sie an den Azure-Speicher sendet. Der Verwaltungsserver muss über genügend Ressourcen zum Ausführen dieser Aufgaben verfügen.
- Der Prozessserver verwendet einen datenträgerbasierten Cache. Wir empfehlen die Verwendung eines separaten Cachedatenträgers mit einer Größe von mindestens 600 GB, um Datenänderungen bei einem Netzwerkengpass oder -ausfall zwischenspeichern und verarbeiten zu können. Bei der Bereitstellung können Sie den Cache auf jedem Datenträger konfigurieren, auf dem mindestens 5 GB Speicher verfügbar sind, aber 600 GB werden als Minimum empfohlen.
- Als bewährte Methode wird empfohlen, den Verwaltungsserver im gleichen Netzwerk und in dem LAN-Segment unterzubringen, in dem sich auch die zu schützenden Computer befinden. Er kann in einem anderen Netzwerk untergebracht werden, aber die zu schützenden Computer sollten für ihn über L3-Netzwerksichtbarkeit verfügen. 

Die empfohlenen Größen für den Verwaltungsserver werden in der folgenden Tabelle zusammengefasst.

**CPU des Verwaltungsservers** | **Arbeitsspeicher** | **Größe des Cachedatenträgers** | **Datenänderungsrate** | **Geschützte Computer**
--- | --- | --- | --- | ---
8 vCPUs (2 Sockets * 4 Kerne mit 2,5 GHz) | 16 GB | 300 GB | 500 GB oder weniger | Stellen Sie einen Verwaltungsserver mit diesen Einstellungen bereit, um weniger als 100 Computer zu replizieren.
12 vCPUs (2 Sockets * 6 Kerne mit 2,5 GHz) | 18 GB | 600 GB | 500 GB bis 1 TB | Stellen Sie einen Verwaltungsserver mit diesen Einstellungen bereit, um 100 bis 150 Computer zu replizieren.
16 vCPUs (2 Sockets * 8 Kerne mit 2,5 GHz) | 32 GB | 1 TB | 1 TB bis 2 TB | Stellen Sie einen Verwaltungsserver mit diesen Einstellungen bereit, um 150 bis 200 Computer zu replizieren.
Bereitstellen eines weiteren Prozessservers | | | > 2 TB | Stellen Sie zusätzliche Prozessserver bereit, wenn Sie mehr als 200 Computer replizieren oder wenn die tägliche Änderungsrate 2 TB überschreitet.

Hierbei gilt:

- Jeder Quellcomputer ist mit 3 Datenträgern von jeweils 100 GB konfiguriert.
- Bei den Benchmarkingmessungen für den Cachedatenträger wurde ein Speicher aus 8 SAS-Laufwerken mit 10.000 U/min und RAID 10 verwendet.

### Netzwerkbandbreite von der Quelle zum Ziel
Berechnen Sie die notwendige Bandbreite für die Erstreplikation und die Deltareplikation mithilfe des [Kapazitätsplanungstools](site-recovery-capacity-planner.md).

#### Einschränken der Bandbreite für die Replikation

Der in Azure replizierte VMware-Datenverkehr wird durch einen bestimmten Prozessserver geleitet. Sie können die für die Site Recovery-Replikation verfügbare Bandbreite auf diesem Server wie folgt einschränken:

1. Öffnen Sie das Microsoft Azure Backup-MMC-Snap-In auf dem Hauptverwaltungsserver oder auf einem Verwaltungsserver, auf dem zusätzlich bereitgestellte Prozessserver ausgeführt werden. Standardmäßig wird auf dem Desktop eine Verknüpfung zu Microsoft Azure Backup erstellt. Sie finden es auch unter: „C:\\Programme\\Microsoft Azure Recovery Services-Agent\\bin\\wabadmin“.
2. Klicken Sie im Snap-In auf **Eigenschaften ändern**.

	![Bandbreite einschränken](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Definieren Sie auf der Registerkarte **Bandbreiteneinschränkung** die Bandbreite, die für die Site Recovery-Replikation genutzt werden kann, sowie den anzuwendenden Zeitplan.

	![Bandbreite einschränken](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Sie können die Bandbreiteneinschränkung wahlweise auch mit PowerShell festlegen. Hier sehen Sie ein Beispiel:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024) 

#### Maximales Ausnutzen der Bandbreite 
Um die Bandbreite zu erhöhen, die von Azure Site Recovery für die Replikation genutzt wird, müssen Sie einen Registrierungsschlüssel ändern.

Der folgende Schlüssel steuert die Anzahl der Threads pro repliziertem Datenträger, die bei der Replikation genutzt werden:

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 In einem absichtlich mit großen Reserven ausgestatteten Netzwerk müssen die Standardwerte dieses Registrierungsschlüssels geändert werden. Wir unterstützen ein Maximum von 32.


Erfahren Sie mehr über die [die detaillierte Kapazitätsplanung](site-recovery-capacity-planner.md).

### Zusätzliche Prozessserver

Wenn Sie mehr als 200 Computer schützen müssen oder die tägliche Änderungsrate größer als 2 TB ist, können Sie zusätzliche Server zum Verarbeiten der Last hinzufügen. Zum horizontalen Hochskalieren haben Sie folgende Möglichkeiten:

- Erhöhen Sie die Anzahl von Verwaltungsservern. Beispielsweise können Sie mit zwei Verwaltungsservern bis zu 400 Computer schützen.
- Fügen Sie zusätzliche Prozessserver hinzu, und verwenden Sie diese Prozessserver anstelle des Verwaltungsservers (oder als Ergänzung zu diesem) für die Abwicklung des Datenverkehrs.

Die folgende Tabelle beschreibt dieses Szenario:

- Sie richten den ursprünglichen Verwaltungsserver so ein, dass er nur als Konfigurationsserver verwendet wird.
- Sie richten einen zusätzlichen Prozessserver ein.
- Sie konfigurieren die geschützten virtuellen Computer so, dass sie den zusätzlichen Prozessserver verwenden.
- Jeder geschützte Quellcomputer ist mit drei Datenträgern von jeweils 100 GB konfiguriert.

**Ursprünglicher Verwaltungsserver**<br/><br/>(Konfigurationsserver) | **Zusätzlicher Prozessserver**| **Größe des Cachedatenträgers** | **Datenänderungsrate** | **Geschützte Computer**
--- | --- | --- | --- | --- 
8 vCPUs (2 Sockets * 4 Kerne mit 2,5 GHz), 16 GB Speicher | 4 vCPUs (2 Sockets * 2 Kerne mit 2,5 GHz), 8 GB Speicher | 300 GB | 250 GB oder weniger | Sie können bis zu 85 Computer replizieren.
8 vCPUs (2 Sockets * 4 Kerne mit 2,5 GHz), 16 GB Speicher | 8 vCPUs (2 Sockets * 4 Kerne mit 2,5 GHz), 12 GB Speicher | 600 GB | 250 GB bis 1 TB | Sie können 85 bis 150 Computer replizieren.
12 vCPUs (2 Sockets * 6 Kerne mit 2,5 GHz), 18 GB Speicher | 12 vCPUs (2 Sockets * 6 Kerne mit 2,5 GHz), 24 GB Speicher | 1 TB | 1 TB bis 2 TB | Sie können 150 bis 225 Computer replizieren.


Wie Sie Ihre Server skalieren, hängt davon ab, ob Sie das zentrale Hochskalieren oder das horizontale Hochskalieren als Modell bevorzugen. Beim zentralen Hochskalieren stellen Sie wenige besonders leistungsstarke Verwaltungs- und Prozessserver bereit. Beim horizontalen Hochskalieren stellen Sie mehr Server mit geringeren Ressourcen bereit. Ein Beispiel: Wenn Sie 220 Computer schützen müssen, können Sie sich für eine der beiden folgenden Optionen entscheiden:

- Konfigurieren Sie den ursprünglichen Verwaltungsserver mit 12 vCPU und 18 GB Speicher, konfigurieren Sie einen zusätzlichen Prozessserver mit 12 vCPU und 24 GB Speicher, und konfigurieren Sie die geschützten Computer so, dass sie nur den zusätzlichen Prozessserver verwenden.
- Alternativ können Sie zwei Verwaltungsserver (2 x 8vCPU, 16 GB RAM) und zwei zusätzliche Prozessserver (1 x 8vCPU und 1 x 4vCPU zur Handhabung von 135 + 85 (220) Computern) konfigurieren. Auch hier konfigurieren Sie die geschützten Computer so, dass sie nur die zusätzlichen Prozessserver verwenden.
  

[Befolgen Sie diese Anweisungen](#deploy-additional-process-servers), wenn Sie zusätzliche Prozessserver einrichten.

## Vor der Bereitstellung

In den folgenden Tabellen werden die Voraussetzungen für die Bereitstellung dieses Szenarios zusammengefasst.


### Voraussetzungen für Azure

**Voraussetzung** | **Details**
--- | ---
**Azure-Konto**| Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Für den Einstieg steht eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) zur Verfügung. Erfahren Sie mehr über die [Preise für Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). 
**Azure-Speicher** | Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Computer werden bei einem Failover hochgefahren. <br/><br/>Je nach Ihren Anforderungen benötigen Sie ein [georedundantes Standardspeicherkonto](../storage/storage-redundancy.md#geo-redundant-storage) oder ein [Premium-Speicherkonto](../storage/storage-premium-storage-preview-portal.md). Das Konto muss sich in der gleichen Region wie der Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein. Beachten Sie, dass eine Replikation in Premium-Speicherkonten derzeit nicht unterstützt wird und nicht verwendet werden sollte.<br/><br/>Erfahren Sie mehr über [Azure-Speicher](../storage/storage-introduction.md).
**Azure-Netzwerk** | Sie benötigen ein virtuelles Azure-Netzwerk, mit dem die virtuellen Azure-Computer eine Verbindung herstellen, wenn ein Failover stattfindet. Das virtuelle Azure-Netzwerk muss sich in derselben Region befinden wie der Site Recovery-Tresor.<br/><br/>Beachten Sie: Nach einem Failover zu Azure benötigen zum Ausführen eines Failbacks eine VPN-Verbindung (oder Azure ExpressRoute) zwischen dem Azure-Netzwerk und dem lokalen Standort. 


### Lokale Voraussetzungen

**Voraussetzung** | **Details**
--- | ---
**Verwaltungsserver** | Sie benötigen einen lokalen Server mit Windows Server 2012 R2, der auf einem virtuellen Computer oder einem physischen Server ausgeführt wird. Alle lokalen Site Recovery-Komponenten werden auf diesem Verwaltungsserver installiert.<br/><br/>Es wird empfohlen, dass Sie diesen Server als hoch verfügbaren virtuellen VMware-Computer bereitstellen. Das Failback von Azure zum lokalen Standort erfolgt immer in virtuellen VMware-Computern, unabhängig davon, ob das Failover für virtuelle Computer (VMs) oder für physische Server durchgeführt wurde. Wenn Sie den Verwaltungsserver nicht als virtuellen VMware-Computer konfigurieren, müssen Sie einen separaten Masterzielserver als virtuellen VMware-Computer konfigurieren, der beim Failback den Datenverkehr empfängt.<br/><br/>Der Server muss eine statische IP-Adresse haben.<br/><br/>Der Hostname des Servers sollte nicht länger als 15 Zeichen sein.<br/><br/>Als Gebietsschema des Betriebssystems sollte nur Englisch verwendet werden.<br/><br/>Der Verwaltungsserver benötigt einen Internetzugang.<br/><br/>Auf dem Server wird ausgehender Zugriff wie folgt benötigt: temporärer Zugriff auf HTTP 80 beim Einrichten der Site Recovery-Komponenten (zum Herunterladen von MySQL); ständiger ausgehender Zugriff auf HTTPS 443 für die Replikationsverwaltung; ständiger ausgehender Zugriff auf HTTPS 9443 für den Replikationsdatenverkehr (dieser Port kann geändert werden). 
**VMware vCenter-/ESXi-Host**: | Sie benötigen mindestens einen VMware vSphere ESX/ESXi-Hypervisor, der Ihre virtuellen VMware-Computer verwaltet und auf dem ESX/ESXi in der Version 6.0, 5.5 oder 5.1 mit den neuesten Updates ausgeführt wird.<br/><br/> Es wird empfohlen, dass Sie einen VMware vCenter-Server zum Verwalten Ihrer ESXi-Hosts bereitstellen. Auf diesem sollte vCenter in der Version 6.0 oder 5.5 mit den neuesten Updates ausgeführt werden.<br/><br/>Beachten Sie, dass Site Recovery neue Features von vCenter und vSphere 6.0 nicht unterstützt, z. B. Cross vCenter vMotion, virtuelle Volumes und Storage DRS. Site Recovery unterstützt nur Features, die auch in Version 5.5 verfügbar waren.
**Geschützte Computer**: | **AZURE**<br/><br/>Die zu schützenden Computer müssen die [Voraussetzungen von Azure](site-recovery-best-practices.md) zum Erstellen virtueller Azure-Computer erfüllen.<br><br/>Wenn Sie nach dem Failover eine Verbindung zu den virtuellen Azure-Computern herstellen möchten, müssen Sie auf der lokalen Firewall Remotedesktopverbindungen aktivieren.<br/><br/>Die Kapazität einzelner Datenträger von geschützten Computern darf nicht größer als 1023 GB sein. Ein virtueller Computer kann bis zu 64 Datenträger haben (also bis zu 64 TB). Wenn Sie über Datenträger verfügen, die größer als 1 TB sind, sollten Sie die Nutzung einer Datenbank-Replikationslösung wie SQL Server AlwaysOn oder Oracle Data Guard in Erwägung ziehen.<br/><br/>Gastcluster mit freigegebenen Datenträgern werden nicht unterstützt. Wenn in Ihrer Bereitstellung Cluster vorhanden sind, sollten Sie die Nutzung einer Datenbank-Replikationslösung wie SQL Server AlwaysOn oder Oracle Data Guard in Erwägung ziehen.<br/><br/>Startvorgänge mit Unified Extensible Firmware Interface(UEFI)/Extensible Firmware Interface(EFI) werden nicht unterstützt.<br/><br/>Computernamen dürfen 1 bis 63 Zeichen enthalten (Buchstaben, Zahlen und Bindestriche). Der Name muss mit einem Buchstaben oder einer Zahl beginnen und mit einem Buchstaben oder einer Zahl enden. Nachdem ein Computer geschützt ist, können Sie den Azure-Namen ändern.<br/><br/>**Virtuelle VMware-Computer**<br/><br>Auf dem Verwaltungsserver (Konfigurationsserver) müssen Sie VMware vSphere PowerCLI 6.0 installieren.<br/><br/>Auf den zu schützenden virtuellen VMware-Computern müssen VMware-Tools installiert und ausgeführt sein.<br/><br/>Wenn auf den Quell-VMs ein NIC-Teamvorgang genutzt wird, wird dieses NIC-Teaming nach dem Failover zu Azure in eine einzige Netzwerkkarte (Network Interface Card, NIC) konvertiert.<br/><br/>Wenn geschützte virtuelle Computer (VMs) über einen iSCSI-Datenträger verfügen, konvertiert Site Recovery den geschützten VM-iSCSI-Datenträger beim Failover der VM zu Azure in eine VHD-Datei. Wenn ein iSCSI-Ziel vom virtuellen Azure-Computer erreicht werden kann, stellt er eine Verbindung zum ISCSI-Ziel her. Dadurch sind für ihn effektiv zwei Datenträger sichtbar – der VHD-Datenträger auf dem virtuellen Azure-Computer und der iSCSI-Quelldatenträger. In diesem Fall müssen Sie auf dem virtuellen Azure-Computer das nach dem Failover angezeigte iSCSI-Ziel trennen.<br/><br/>Informieren Sie sich über [VMware-Benutzerberechtigungen, die für Site Recovery erforderlich sind](#vmware-permissions-for-vcenter-access).<br/><br/> **WINDOWS SERVER-COMPUTER (auf einem virtuellem VMware-Computer oder einem physischem Server)**<br/><br/>Auf dem Server muss ein unterstütztes 64-Bit-Betriebssystem ausgeführt sein: Windows Server 2012 R2, Windows Server 2012 oder Windows Server 2008 R2 mit mindestens SP1.<br/><br/>Der Hostname, die Bereitstellungspunkte, die Gerätenamen und der Windows-Systempfad (z. B: „C:\\Windows“) müssen in englischer Sprache vorliegen.<br/><br/>Das Betriebssystem muss im Laufwerk „C:\\“ installiert werden, und der Betriebssystem-Datenträger muss ein Windows-Basisdatenträger sein (das Betriebssystem darf nicht auf einem dynamischen Windows-Datenträger installiert werden).<br/><br/>Sie müssen ein Administratorkonto für die Pushinstallation des Mobilitätsdiensts auf Windows-Servern bereitstellen (dies muss ein lokaler Administrator auf dem Windows-Computer sein). Wenn das bereitgestellte Konto kein Domänenkonto ist, müssen Sie die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer deaktivieren. [Erfahren Sie mehr](#install-the-mobility-service-with-the-process-server).<br/><br/>Site Recovery unterstützt virtuelle Computer mit RDM-Datenträger. Beim Failback wird der RDM-Datenträger wiederverwendet, sofern die ursprüngliche Quell-VM und der RDM-Datenträger verfügbar sind. Wenn sie nicht verfügbar sind, erstellt Site Recovery beim Failback für jeden Datenträger eine neue VMDK-Datei (Virtual Machine Disk) .<br/><br/>**LINUX-COMPUTER**<br/><br/>Sie benötigen ein unterstütztes 64-Bit-Betriebssystem: Red Hat Enterprise Linux 6.7; CentOS 6.5, 6.6, 6.7; Oracle Enterprise Linux 6.4, 6.5, auf dem der Red Hat-kompatible Kernel oder Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3 ausgeführt werden.<br/><br/>„/etc/hosts“-Dateien auf geschützten Computern müssen Einträge enthalten, die den Namen des lokalen Hosts den IP-Adressen aller Netzwerkkarten zuordnen. <br/><br/>Wenn Sie nach einem Failover eine Verbindung mit einem virtuellen Azure-Computer unter Linux über einen Secure-Shell-Client (SSH) herstellen möchten, müssen Sie sicherstellen, dass der Secure Shell-Dienst auf dem geschützten Computer beim Systemstart automatisch gestartet wird und dass die Firewallregeln eine SSH-Verbindung zulassen.<br/><br/>Der Hostname, die Bereitstellungspunkte, die Gerätenamen und die Linux-Systempfade und Dateinamen müssen in englischer Sprache vorliegen (z. B. „/etc/“; „/usr“).<br/><br/>Der Schutz kann nur für Linux-Computer mit folgendem Speicher aktiviert werden: Dateisystem (EXT3, ETX4, ReiserFS, XFS); Multipath-Softwaregeräte-Mapper (multipath); Volume-Manager: (LVM2). Physische Server mit HP CCISS-Controllerspeicher werden nicht unterstützt. Das Dateisystem „ReiserFS“ wird nur auf SUSE Linux Enterprise Server 11 SP3 unterstützt.<br/><br/>Site Recovery unterstützt virtuelle Computer mit RDM-Datenträger. Beim Failback für Linux wird der RDM-Datenträger nicht wiederverwendet. Anstelle dessen erstellt Site Recovery für jeden entsprechenden RDM-Datenträger eine neue VMDK-Datei. 


## Schritt 1: Erstellen eines Tresors

1. Melden Sie sich im [Verwaltungsportal](https://manage.windowsazure.com/) an.
2. Erweitern Sie **Data Services** > **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.
3. Klicken Sie auf **Neu erstellen** > **Schnellerfassung**.
4. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.
5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Sie finden eine Liste der unterstützten Regionen unter „Geografische Verfügbarkeit“ auf der Seite [Azure Site Recovery – Preisübersicht](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Klicken Sie auf **Tresor erstellen**. ![Neuer Tresor](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von **Recovery Services** als **Aktiv** angegeben.

## Schritt 2: Einrichten eines Azure-Netzwerks

Richten Sie ein Azure-Netzwerk ein, damit die virtuellen Azure-Computer nach dem Failover mit einem Netzwerk verbunden sind und damit das Failback zum lokalen Standort wie erwartet durchgeführt werden kann.

1. Klicken Sie im Azure-Portal auf **Virtuelles Netzwerk erstellen**, und geben Sie einen Namen für das Netzwerk an. Geben Sie einen IP-Adressbereich und einen Subnetznamen an.
2. Sie müssen dem Netzwerk ein VPN oder ExpressRoute hinzufügen, wenn Sie ein Failback ausführen. Das VPN oder ExpressRoute können dem Netzwerk auch nach dem Failover noch hinzugefügt werden. 

Erfahren Sie mehr über [Azure-Netzwerke](virtual-networks-overview.md).

## Schritt 3: Installieren der VMware-Komponenten

Wenn Sie virtuelle VMware-Computer replizieren möchten, installieren Sie die folgenden VMware-Komponenten auf dem Verwaltungsserver:

1. [Laden Sie VMware vSphere PowerCLI 6.0 herunter](https://developercenter.vmware.com/tool/vsphere_powercli/6.0), und installieren Sie diese Befehlszeilenschnittstelle (CLI, Command Line Interface).
2. Starten Sie den Server neu.


## Schritt 4: Herunterladen eines Tresorregistrierungsschlüssels

1. Öffnen Sie vom Verwaltungsserver aus die Site Recovery-Konsole in Azure. Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite „Schnellstart“ zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

	![Schnellstart-Symbol](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Klicken Sie auf der Seite **Schnellstart** auf **Zielressourcen vorbereiten** > **Registrierungsschlüssel herunterladen**. Die Registrierungsdatei wird automatisch generiert. Sie ist nach der Erstellung 5 Tage lang gültig.


## Schritt 5: Installieren des Verwaltungsservers

> [AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Laden Sie auf der Seite **Schnellstart** die einheitliche Installationsdatei auf den Server herunter.
2. Führen Sie die Installationsdatei aus, um die Einrichtung im Site Recovery-Assistenten für einheitliches Setup zu starten.
3. Wählen Sie in **Vorbereitung** die Option **Konfigurationsserver und Prozessserver installieren** aus. Je nach Größe Ihrer Bereitstellung benötigen Sie unter Umständen später zusätzliche Prozessserver. Das spielt aber bei der Ersteinrichtung Ihrer Bereitstellung zunächst keine Rolle.

	![Vorbereitung](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)

4. Klicken Sie unter **Installation von Drittanbietersoftware** auf **Ich stimme zu**, um MySQL herunterzuladen und zu installieren.

	![Drittanbietersoftware](./media/site-recovery-vmware-to-azure-classic/combined-wiz2.png)

5. Geben Sie in **Interneteinstellungen** an, wie der Anbieter, der auf dem Server installiert wird, eine Internetverbindung mit Azure Site Recovery herstellt.

- Wenn der Anbieter eine direkte Verbindung herstellt, wählen Sie **Ohne Proxy direkt verbinden** aus.
- Wenn die Verbindung über einen derzeit auf dem Server eingerichteten Proxy hergestellt werden soll, wählen Sie **Mit vorhandenen Proxyeinstellungen verbinden** aus.
- Wenn für den vorhandenen Proxy eine Authentifizierung erforderlich ist oder wenn Sie für den Anbieter einen benutzerdefinierten Proxy verwenden möchten, wählen Sie **Mit benutzerdefinierten Proxyeinstellungen verbinden** aus
- Bei einem benutzerdefinierten Proxy müssen Sie die Adresse, den Port und Anmeldeinformationen eingeben.
- Bei Verwendung eines Proxys müssen über ihn die folgenden URLs zugänglich sein:


	![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. Stellen Sie sicher, dass die folgenden URLs vom Verwaltungsserver aus zugänglich sind:

	- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net. Wenn Ihr Server auf IP-Adressen basierende Firewallregeln verwendet, vergewissern Sie sich, dass die Regeln die Kommunikation mit Azure zulassen. Sie müssen die [IP-Adressbereiche für das Azure-Datencenter](https://msdn.microsoft.com/library/azure/dn175718.aspx) und das HTTPS-Protokoll (Port 433) zulassen. Außerdem müssen Sie die IP-Adressbereiche für die Azure-Region Ihres Abonnements und für die Region „USA, Westen“ über eine Whitelist zulassen. Sie müssen auch die folgende URL für das Herunterladen von MySQL auf die Whitelist setzen: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.


7. In der **Voraussetzungsprüfung** führt das Setupprogramm eine Prüfung der erforderlichen Komponenten auf dem Server durch.

	![Voraussetzungen](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

8. Erstellen Sie unter **MySQL-Server-Konfigurationen** Anmeldeinformationen, um sich bei der MySQL Server-Instanz anzumelden. Sie können folgende Sonderzeichen verwenden: „\_“, „!“, „@“, „$“, „\\“, „%“.

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

9. Geben Sie unter **Umgebungsdetails** an, ob Sie virtuelle VMware-Computer replizieren. Wenn dies der Fall ist, überprüft das Setupprogramm, ob PowerCLI 6.0 installiert ist.

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

10. Wählen Sie unter **Installationsspeicherort** aus, wo die Binärdateien installiert und der Cache gespeichert werden soll. Es wird empfohlen, dass das Cachelaufwerk über mindestens 600 GB freien Speicherplatz verfügt.

	![Installationspfad](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

11. Geben Sie unter **Netzwerkauswahl** den Listener (Netzwerkkarte und SSL-Port) an, über den der Server Replikationsdaten sendet und empfängt. Sie können den Standardport (9443) ändern. Neben diesem Port wird auch Port 443 auf dem Server geöffnet, um Informationen zur Replikationsorchestrierung zu senden und zu empfangen. Port 443 sollte nicht für Replikationsdaten verwendet werden.


	![Netzwerkauswahl](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

12. Suchen Sie unter **Registrierung** den Registrierungsschlüssel, den Sie vom Tresor heruntergeladen haben, und wählen Sie ihn aus.

	![Registrierung](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)

13.  Überprüfen Sie unter **Zusammenfassung** die angezeigten Informationen.

	![Zusammenfassung](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

### Ausführen des Setupprogramms über die Befehlszeile

Sie können den Assistenten für einheitliches Setup auch wie folgt über die Befehlszeile ausführen:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Hierbei gilt:

- /ServerMode: Obligatorisch. Gibt an, ob bei der Installation der Konfigurationsserver und der Prozessserver oder nur der Prozessserver installiert werden soll (Letzteres wird zum Installieren zusätzlicher Prozessserver verwendet). Eingabewerte: CS, PS.
- InstallDrive: Obligatorisch. Gibt den Ordner an, in dem die Komponenten installiert werden.
- /MySQLCredFilePath. Obligatorisch. Gibt den Pfad zu einer Datei an, in der die Anmeldeinformationen für MySQL Server gespeichert sind. Rufen Sie die Vorlage zum Erstellen der Datei ab.
- /VaultCredFilePath. Obligatorisch. Speicherort der Datei mit Anmeldeinformationen für den Tresor.
- /EnvType. Obligatorisch. Typ der Installation. Werte: VMware, NonVMware
- /PSIP und /CSIP. Obligatorisch. Die IP-Adressen für den Prozessserver (/PSIP) und den Konfigurationsserver (/CSIP).
- /PassphraseFilePath. Obligatorisch. Speicherort der Datei mit der Passphrase.
- /ByPassProxy. Optional. Gibt an, dass der Verwaltungsserver die Verbindung mit Azure ohne Proxy herstellt.
- /ProxySettingsFilePath. Optional. Gibt die Einstellungen für einen benutzerdefinierten Proxy an (entweder für einen Standardproxy auf dem Server, der eine Authentifizierung erfordert, oder für einen benutzerdefinierten Proxy). 




## Schritt 6: Einrichten der Anmeldeinformationen für den vCenter-Server

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

Der Prozessserver kann automatisch virtuelle VMware-Computer ermitteln, die von einem vCenter-Server verwaltet werden. Für diese automatische Ermittlung benötigt Site Recovery ein Konto und Anmeldeinformationen, mit denen auf den vCenter-Server zugegriffen werden kann. Wenn Sie nur physische Server replizieren, ist dieser Abschnitt nicht relevant.

Gehen Sie hierzu wie folgt vor:

1. Erstellen Sie auf dem vCenter-Server auf vCenter-Ebene eine Rolle namens **Azure\_Site\_Recovery** mit den [erforderlichen Berechtigungen](#vmware-permissions-for-vcenter-access).
2. Weisen Sie die Rolle **Azure\_Site\_Recovery** einem vCenter-Benutzer zu.

	>[AZURE.NOTE]Ein vCenter-Benutzerkonto mit der nur zum Lesezugriff berechtigenden schreibgeschützten Rolle kann das Failover ausführen, ohne geschützte Quellcomputer herunterzufahren. Wenn Sie diese Computer herunterfahren möchten, benötigen Sie die Rolle „Azure\_Site\_Recovery“. Beachten Sie: Wenn Sie nur virtuelle Computer (VMs) von VMware zu Azure migrieren und kein Failback benötigen, ist die schreibgeschützte Rolle ausreichend.

3. Um ein Konto hinzuzufügen, öffnen Sie **cspsconfigtool**. Das Tool steht als Verknüpfung auf dem Desktop zur Verfügung und befindet sich im Ordner „[INSTALLATIONSPFAD]\\home\\svsystems\\bin“.
2. Klicken Sie auf der Registerkarte **Konten verwalten** auf **Konto hinzufügen**.

	![Konto hinzufügen](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. Fügen Sie unter **Kontodetails** Anmeldeinformationen hinzu, mit denen auf den vCenter-Server zugegriffen werden kann. Beachten Sie, dass es länger als 15 Minuten dauern kann, bis der Kontoname im Portal angezeigt wird. Zum sofortigen Aktualisieren klicken Sie auf der Registerkarte **Konfigurationsserver** auf „Aktualisieren“.

	![Details](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## Schritt 7: Hinzufügen von vCenter-Servern und ESXi-Hosts

Wenn Sie virtuelle VMware-Computer replizieren, müssen Sie einen vCenter-Server (oder einen ESXi-Host) hinzufügen.

1. Wählen Sie auf der Registerkarte **Server** > **Konfigurationsserver** den Konfigurationsserver aus, und klicken Sie auf **vCenter-Server hinzufügen**.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Fügen Sie folgende Informationen hinzu: die Details für den vCenter-Server oder den ESXi-Host, den Namen des Kontos, das Sie im vorherigen Schritt für den Zugriff auf den vCenter-Server festgelegt haben, und den Prozessserver, der zum Ermitteln der vom vCenter-Server verwalteten virtuellen VMware-Computer verwendet wird. Beachten Sie, dass sich der vCenter-Server oder der ESXi-Host im gleichen Netzwerk befinden sollte wie der Server, auf dem der Prozessserver installiert wird.

	>[AZURE.NOTE]Wenn Sie den vCenter-Server oder ESXi-Host mit einem Konto hinzufügen, das auf dem vCenter- oder Hostserver keine Administratorrechte besitzt, müssen Sie sicherstellen, dass für die vCenter- oder ESXi-Konten folgende Berechtigungen aktiviert sind: Datacenter, Datastore, Folder, Jost, Network, Resource, Virtual Machine, vSphere Distributed Switch. Außerdem benötigt der vCenter-Server die Anzeigeberechtigung für den Speicher.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Nach Abschluss der Ermittlung wird der vCenter-Server auf der Registerkarte **Konfigurationsserver** aufgeführt.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)
		

## Schritt 8: Erstellen einer Schutzgruppe

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Eine Schutzgruppe enthält virtuelle Computer oder physische Server, die die gleichen Replikationseinstellungen teilen.

1. Öffnen Sie **Geschützte Elemente** > **Schutzgruppe**, und klicken Sie, um eine Schutzgruppe hinzuzufügen.

	![Schutzgruppe erstellen](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Geben Sie auf der Seite **Schutzgruppeneinstellungen festlegen** einen Namen für die Gruppe an, und wählen Sie in **Von** den Konfigurationsserver aus, auf dem Sie die Gruppe erstellen möchten. **Ziel** ist Azure.

	![Schutzgruppeneinstellungen](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Konfigurieren Sie auf der Seite **Replikationseinstellungen festlegen** die Replikationseinstellungen, die für alle Computer in der Gruppe verwendet werden.

	![Schutzgruppenreplikation](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

	- **Multi-VM-Konsistenz:** Wenn Sie diese Option aktivieren, werden freigegebene anwendungskonsistente Wiederherstellungspunkte auf den Computern in der Schutzgruppe erstellt. Diese Einstellung ist besonders wichtig, wenn auf allen Computern in der Schutzgruppe dieselbe Workload ausgeführt wird. Alle Computer werden an demselben Datenpunkt wiederhergestellt. Diese Option ist beim Replizieren von virtuellen VMware-Computern genauso verfügbar wie beim Replizieren von physischen Windows-/Linux-Servern.
	- **RPO-Schwellenwert:** Legt die RPO (Recovery Point Objective) fest. Wenn bei der Replikation für den kontinuierlichen Datenschutz der konfigurierte RPO-Schwellenwert überschritten wird, werden Warnungen generiert.
	- **Aufbewahrungszeitraum des Wiederherstellungspunkts:** Gibt die Aufbewahrungsdauer an. Geschützte Computer können innerhalb dieses Zeitfensters an einem beliebigen Punkt wiederhergestellt werden.
	- **Häufigkeit von Momentaufnahmen für Anwendungskonsistenz:** Gibt an, wie oft Wiederherstellungspunkte erstellt werden, die anwendungskonsistente Momentaufnahmen enthalten.

Wenn Sie auf das Häkchen klicken, wird eine Schutzgruppe mit dem von Ihnen angegebenen Namen erstellt. Außerdem wird eine zweite Schutzgruppe mit dem Namen <Schutzgruppename-Failback> erstellt. Diese Schutzgruppe wird verwendet, wenn Sie nach einem Failover zu Azure das Failback zum lokalen Standort ausführen. Sie können die Erstellung der Schutzgruppen auf der Seite **Geschützte Elemente** überwachen.

## Schritt 9: Installieren des Mobilitätsdiensts

Der erste Schritt beim Aktivieren des Schutzes für virtuelle Computer und physische Server besteht im Installieren des Mobilitätsdiensts. Hierzu stehen zwei Möglichkeiten zur Verfügung:

- Automatische Pushinstallation des Diensts auf jedem Computer über den Prozessserver. Beachten Sie: Wenn Sie einer Schutzgruppe Computer hinzufügen, auf denen bereits eine geeignete Version des Mobilitätsdiensts ausgeführt wird, wird die Pushinstallation nicht ausgeführt.
- Automatische Installation des Diensts mithilfe der in Ihrem Unternehmen verwendeten Pushmethode, z. B. WSUS (Windows Server Update Services) oder System Center Configuration Manager. Stellen Sie sicher, dass Sie den Verwaltungsserver eingerichtet haben, bevor Sie diesen Schritt ausführen.
- Manuelle Installation auf jedem Computer, den Sie schützen möchten. Stellen Sie sicher, dass Sie den Verwaltungsserver eingerichtet haben, bevor Sie diesen Schritt ausführen.


### Installieren des Mobilitätsdiensts mit Pushinstallation

Beim Hinzufügen von Computern zu einer Schutzgruppe wird der Mobilitätsdienst vom Prozessserver automatisch per Pushfunktion auf jedem Computer installiert.


#### Vorbereiten des automatischen Pushs auf Windows-Computern 

Im Folgenden wird beschrieben, wie Sie Windows-Computer vorbereiten, damit der Prozessserver automatisch den Mobilitätsdienst darauf installieren kann.

1.  Erstellen Sie ein Konto, mit dem der Prozessserver auf den Computer zugreifen kann. Das Konto muss über Administratorrechte verfügen (lokal oder für die Domäne). Beachten Sie, dass diese Anmeldeinformationen nur für die Pushinstallation des Mobilitätsdiensts verwendet werden.

	>[AZURE.NOTE]Wenn Sie kein Domänenkonto verwenden, müssen Sie die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer deaktivieren. Zu diesem Zweck fügen Sie in der Registrierung unter „HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System“ den DWORD-Eintrag „LocalAccountTokenFilterPolicy“ mit dem Wert 1 hinzu. Um den Registrierungseintrag über eine Befehlszeilenschnittstelle (CLI) hinzuzufügen, öffnen Sie eine CLI oder verwenden PowerShell und geben **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`** ein.

2.  Wählen Sie in der Windows-Firewall des zu schützenden Computers **Eine App oder ein Feature durch die Windows-Firewall zulassen** aus, und aktivieren Sie **Datei- und Druckerfreigabe** und **Windows-Verwaltungsinstrumentation**. Für Computer, die einer Domäne angehören, können Sie die Firewallrichtlinie mit einem Gruppenrichtlinienobjekt konfigurieren.

	![Firewalleinstellungen](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Fügen Sie das von Ihnen erstellte Konto hinzu:

	- Öffnen Sie **cspsconfigtool**. Das Tool steht als Verknüpfung auf dem Desktop zur Verfügung und befindet sich im Ordner „[INSTALLATIONSPFAD]\\home\\svsystems\\bin“.
	- Klicken Sie auf der Registerkarte **Konten verwalten** auf **Konto hinzufügen**.
	- Fügen Sie das von Ihnen erstellte Konto hinzu. Nach dem Hinzufügen des Kontos müssen Sie die Anmeldeinformationen angeben, wenn Sie einen Computer einer Schutzgruppe hinzufügen.


#### Vorbereiten des automatischen Pushs auf Linux-Servern

1.	Vergewissern Sie sich, dass der zu schützende Linux-Computer unterstützt wird, wie unter [Lokale Voraussetzungen](#on-premises-prerequisites) beschrieben. Stellen Sie sicher, dass zwischen dem zu schützenden Computer und dem Verwaltungsserver, auf dem der Prozessserver ausgeführt wird, eine funktionierende Netzwerkverbindung besteht. 

2.	Erstellen Sie ein Konto, mit dem der Prozessserver auf den Computer zugreifen kann. Das Konto muss einem Root-Benutzer auf dem Linux-Quellserver entsprechen. Beachten Sie, dass diese Anmeldeinformationen nur für die Pushinstallation des Mobilitätsdiensts verwendet werden.

	- Öffnen Sie **cspsconfigtool**. Das Tool steht als Verknüpfung auf dem Desktop zur Verfügung und befindet sich im Ordner „[INSTALLATIONSPFAD]\\home\\svsystems\\bin“.
	- Klicken Sie auf der Registerkarte **Konten verwalten** auf **Konto hinzufügen**.
	- Fügen Sie das von Ihnen erstellte Konto hinzu. Nach dem Hinzufügen des Kontos müssen Sie die Anmeldeinformationen angeben, wenn Sie einen Computer einer Schutzgruppe hinzufügen.

3.	Vergewissern Sie sich, dass die Datei „/etc/hosts“ auf dem Linux-Quellserver Einträge enthält, die den Namen des lokalen Hosts den IP-Adressen zuordnen, die allen Netzwerkkarten zugewiesen sind.
4.	Installieren Sie die neuesten openssh-, openssh-server- und openssl-Pakete auf dem Computer, den Sie schützen möchten.
5.	Stellen Sie sicher, dass SSH auf Port 22 aktiviert ist und ausgeführt wird. 
6.	Aktivieren Sie das SFTP-Subsystem und die Kennwortauthentifizierung in der Datei „sshd\_config“ wie folgt: 

	- Melden Sie sich als Root-Benutzer an.
	- Suchen Sie in der Datei „/etc/ssh/sshd\_config“ die Zeile, die mit „PasswordAuthentication“ beginnt.
	- Heben Sie die Auskommentierung der Zeile auf, und ändern Sie den Wert von **no** in **yes**.
	- Suchen Sie die Zeile, die mit **Subsystem** beginnt, und heben Sie die Auskommentierung der Zeile auf.
 
		![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### Manuelles Installieren des Mobilitätsdiensts

Die benötigten Installationsprogramme können Sie von xxx herunterladen.

Quellbetriebssystem | Installationsdatei für den Mobilitätsdienst
--- | ---
Windows Server (nur 64 Bit) | Microsoft-ASR\_UA\_9.*.0.0\_Windows\_* release.exe
CentOS 6.4, 6.5, 6.6 (nur 64 Bit) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz SUSE Linux Enterprise Server 11 SP3 (nur 64 Bit) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (nur 64 Bit) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Manuelles Installieren auf einem Windows-Server


1. Laden Sie das entsprechende Installationsprogramm herunter, und führen Sie es aus.
2. Wählen Sie unter **Vorbereitung** die Option **Mobility Service** aus.

	![Mobilitätsdienst](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Geben Sie unter **Konfigurationsserverdetails** die IP-Adresse des Verwaltungsservers und die Passphrase ein, die beim Installieren der Verwaltungsserverkomponenten generiert wurde. Sie können die Passphrase abrufen, indem Sie auf dem Verwaltungsserver **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** ausführen.

	![Mobilitätsdienst](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. Behalten Sie unter **Installationsspeicherort** den Standardspeicherort bei, und klicken Sie auf **Weiter**, um mit der Installation zu beginnen.
5. Überwachen Sie unter **Installationsstatus** die Installation, und starten Sie den Computer neu, wenn Sie dazu aufgefordert werden.

Sie können die Installation auch über die Befehlszeile ausführen:

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installationsverzeichnis>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Pfad der Passphrasendatei>] [/LogFilePath <Log File Path>]

Hierbei gilt:

- /Role: Obligatorisch. Gibt an, ob der Mobilitätsdienst installiert werden soll.
- /InstallLocation: Obligatorisch. Gibt an, wo der Dienst installiert werden soll.
- /PassphraseFilePath: Obligatorisch. Gibt die Passphrase des Konfigurationsservers an.
- /LogFilePath: Obligatorisch. Gibt den Speicherort für die Setupprotokolldateien an. 

#### Ändern der IP-Adresse des Verwaltungsservers

Nach dem Ausführen des Assistenten können Sie die IP-Adresse des Verwaltungsservers wie folgt ändern:

1. Öffnen Sie die Datei „hostconfig.exe“ (sie befindet sich auf dem Desktop).
2. Auf der Registerkarte **Global** können Sie die IP-Adresse des Verwaltungsservers ändern.

	>[AZURE.NOTE]Sie sollten nur die IP-Adresse des Verwaltungsservers ändern. Die Portnummer für die Kommunikation des Verwaltungsservers muss 443 sein, und „HTTPS verwenden“ muss aktiviert bleiben. Die Passphrase sollte nicht geändert werden.

	![IP-Adresse des Verwaltungsservers](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### Manuelles Installieren auf einem Linux-Server

1. Kopieren Sie anhand der obigen Tabelle das geeignete TAR-Archiv auf den zu schützenden Linux-Computer.
2. Öffnen Sie ein Shellprogramm, und extrahieren Sie das gezippte TAR-Archiv in einen lokalen Pfad, indem Sie Folgendes ausführen: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Erstellen Sie in dem lokalen Verzeichnis, in das Sie den Inhalt des TAR-Archivs extrahiert haben, die Datei „passphrase.txt“. Hierzu kopieren Sie die Passphrase aus „C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase“ auf dem Verwaltungsserver, und speichern Sie sie dann in „passphrase.txt“, indem Sie in der Shell *`echo <passphrase> >passphrase.txt`* ausführen.
4. Installieren Sie den Mobilitätsdienst, indem Sie *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`* eingeben.
5. Geben Sie die interne IP-Adresse des Verwaltungsservers an, und stellen Sie sicher, dass Port 443 ausgewählt ist.

**Sie können die Installation auch über die Befehlszeile ausführen:**

1. Kopieren Sie die Passphrase aus „C:\\Program Files (x86)\\InMage Systems\\private\\connection“ auf dem Verwaltungsserver, und speichern Sie sie in „passphrase.txt“ auf dem Verwaltungsserver. Führen Sie anschließend die aufgeführten Befehle aus. In unserem Beispiel wird für den Verwaltungsserver die IP-Adresse 104.40.75.37 verwendet, und der HTTPS-Port muss 443 sein:

So installieren Sie den Dienst auf einem Produktionsserver:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt
 
So installieren Sie den Dienst auf dem Masterzielserver:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## Schritt 10: Aktivieren des Schutzes für einen Computer

Zum Aktivieren des Schutzes fügen Sie einer Schutzgruppe virtuelle Computer und physische Server hinzu. Wenn Sie virtuelle VMware-Computer schützen, sollten Sie vor Beginn Folgendes berücksichtigen:

- Virtuelle VMware-Computer werden alle 15 Minuten ermittelt, und es kann länger als 15 Minuten dauern, bis sie nach der Ermittlung im Site Recovery-Portal angezeigt werden.
- Es kann auch länger als 15 Minuten dauern, bis Umgebungsänderungen auf dem virtuellen Computer (z. B. die Installation von VMware-Tools) in Site Recovery aktualisiert werden.
- Sie können den Zeitpunkt der letzten Ermittlung für die virtuellen VMware-Computer auf der Registerkarte **Konfigurationsserver** im Feld **Letzter Kontakt um** für den vCenter-Server oder den ESXi-Host überprüfen.
- Wenn Sie bereits eine Schutzgruppe erstellt haben und anschließend einen vCenter-Server oder ESXi-Host hinzufügen, kann es länger als 15 Minuten dauern, bis das Azure Site Recovery-Portal aktualisiert wird und virtuelle Computer im Dialogfeld **Computer zu Schutzgruppe hinzufügen** angezeigt werden.
- Wenn Sie einer Schutzgruppe sofort Computer hinzufügen möchten, ohne auf die planmäßige Ermittlung zu warten, markieren Sie den Konfigurationsserver (klicken Sie nicht darauf), und klicken Sie auf die Schaltfläche **Aktualisieren**.

Beachten Sie außerdem Folgendes:

- Es wird empfohlen, Ihre Schutzgruppen so entwerfen, dass sie Ihre Workloads widerspiegeln. Fügen Sie z. B. Computer, auf denen eine bestimmte Anwendung ausgeführt wird, der gleichen Gruppe hinzu.
- Beim Hinzufügen von Computern zu einer Schutzgruppe installiert der Prozessserver den Mobilitätsdienst automatisch per Pushfunktion auf jedem Computer, sofern er nicht bereits vorhanden ist. Beachten Sie jedoch, dass Sie für den Pushmechanismus einige Vorbereitungen treffen müssen, wie im vorherigen Schritt beschrieben.


So fügen Sie Computer einer Schutzgruppe hinzu:

1. Klicken Sie auf **Geschützte Elemente** > **Schutzgruppen** > **Computer** > „Computer hinzufügen“. \\Als bewährte Methode 
2. Wenn Sie virtuelle VMware-Computer schützen, wählen Sie unter **Virtuelle Computer auswählen** einen vCenter-Server aus, der die virtuellen Computer verwaltet (oder den EXSi-Host, auf dem sie ausgeführt werden), und wählen Sie dann die Computer aus.

	![Schutz aktivieren](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Wenn Sie physische Server schützen, geben Sie im Assistenten **Physische Computer hinzufügen** unter **Virtuelle Computer auswählen** die IP-Adresse und den Anzeigenamen an. Wählen Sie dann die Betriebssystemfamilie aus.

	![Schutz aktivieren](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)
		
4. Wählen Sie unter **Zielressourcen angeben** das Speicherkonto aus, das für die Replikation verwendet werden soll, und wählen Sie aus, ob die Einstellungen für alle Workloads verwendet werden sollen. Beachten Sie, dass Premium-Speicherkonten derzeit nicht unterstützt werden.

	![Schutz aktivieren](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. Wählen Sie unter **Konten angeben** das Konto aus, das Sie zur Verwendung beim automatischen Installieren des Mobilitätsdiensts [konfiguriert](#install-the-mobility-service-with-the-process-server) haben.

	![Schutz aktivieren](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Klicken Sie auf das Häkchen, um das Hinzufügen von Computern zur Schutzgruppe abzuschließen und die erste Replikation für jeden Computer zu starten.

	>[AZURE.NOTE]Wenn die Pushinstallation vorbereitet wurde und Computer einer Schutzgruppe hinzugefügt werden, wird der Mobilitätsdienst automatisch auf diesen Computern installiert, sofern er dort noch nicht vorhanden ist. Nachdem der Dienst installiert wurde, wird ein Schutzauftrag gestartet, der aber mit einem Fehler abgebrochen wird. Nach dem Fehler müssen Sie jeden Computer, auf dem der Mobilitätsdienst installiert wurde, manuell neu starten. Nach dem Neustart wird der Schutzauftrag erneut gestartet, und die erste Replikation wird ausgeführt.

Sie können den Status auf der Seite **Aufträge** überwachen.

![Schutz aktivieren](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Außerdem können Sie den Schutzstatus unter **Geschützte Elemente** > <protection group name> > **Virtuelle Computer** überwachen. Nachdem die erste Replikation abgeschlossen ist und die Daten synchronisiert sind, ändert sich der Status des Computers in **Geschützt**.

![Schutz aktivieren](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## Schritt 11: Festlegen der Eigenschaften geschützter Computer

1. Sobald ein Computer den Status **Geschützt** aufweist, können Sie die Failovereigenschaften konfigurieren. Wählen Sie in den Schutzgruppendetails den Computer aus, und öffnen Sie die Registerkarte **Konfigurieren**.
2. Site Recovery schlägt automatisch Eigenschaften für einen virtuellen Azure-Computer vor und erkennt die lokalen Netzwerkeinstellungen. 

	![Eigenschaften für virtuelle Computer festlegen](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Sie können folgende Eigenschaften ändern:

	-  **Name des virtuellen Azure-Computers**: Dies ist der Name, der dem Computer in Azure nach dem Failover zugewiesen wird. Der Name muss den Azure-Anforderungen entsprechen.
	-  **Größe des virtuellen Azure-Computers**: Die Anzahl der Netzwerkkarten hängt von der Größe ab, die Sie für den virtuellen Zielcomputer angeben. Erfahren Sie mehr über [Größen und Adapter](virtual-machines-size-specs.md/#size-tables). Beachten Sie Folgendes:
		- Wenn Sie die Größe für einen virtuellen Computer ändern und die Einstellungen speichern, wird die Anzahl der Netzwerkkarten beim nächsten Öffnen der Registerkarte **Konfigurieren** geändert. Die Anzahl der Netzwerkkarten der virtuellen Zielcomputer entspricht mindestens der Anzahl der Netzwerkkarten auf virtuellen Quellcomputern und der maximalen Anzahl der Netzwerkkarten, die von der ausgewählten Größe des virtuellen Computers unterstützt werden. 
			- Wenn die Anzahl der Netzwerkkarten des Quellcomputers maximal der Anzahl der Netzwerkkarten entspricht, die für die Größe des Zielcomputers zulässig ist, hat der Zielcomputer die gleiche Anzahl von Netzwerkkarten wie der Quellcomputer.
			- Wenn die Anzahl der Netzwerkadapter für den virtuellen Quellcomputer die maximal zulässige Anzahl für die Größe des Zielcomputers übersteigt, wird die Anzahl verwendet, die maximal für die Größe des Zielcomputers zulässig ist.
			- Ein Beispiel: Wenn ein Quellcomputer zwei Netzwerkkarten besitzt und der Zielcomputer aufgrund seiner Größe vier Netzwerkkarten unterstützt, erhält der Zielcomputer zwei Netzwerkkarten. Wenn der Quellcomputer dagegen zwei Netzwerkadapter besitzt und der Zielcomputer aufgrund seiner Größe nur einen Adapter unterstützt, erhält der Zielcomputer nur einen Adapter.
		- Wenn der virtuelle Computer mehrere Netzwerkkarten besitzt, müssen alle Netzwerkkarten mit dem gleichen Azure-Netzwerk verbunden werden. 
	- **Azure-Netzwerk**: Sie müssen ein Azure-Netzwerk angeben, mit dem die virtuellen Azure-Computer nach dem Failover verbunden werden. Wenn Sie keines angeben, werden die virtuellen Azure-Computer mit keinem Netzwerk verbunden. Außerdem müssen Sie ein Azure-Netzwerk angeben, wenn Sie ein Failback von Azure zum lokalen Standort ausführen möchten. Für das Failback wird eine VPN-Verbindung zwischen einem Azure-Netzwerk und dem lokalen Netzwerk benötigt.	
	- **Azure-IP-Adresse/Subnetz**: Für jede Netzwerkkarte müssen Sie das Subnetz auswählen, mit dem der virtuelle Azure-Computer verbunden werden soll. Beachten Sie Folgendes:
		- Wenn die Netzwerkkarte des Quellcomputers für eine statische IP-Adresse konfiguriert wurde, können Sie für den virtuellen Azure-Computer eine statische IP-Adresse angeben. Wenn Sie keine statische IP-Adresse angeben, wird eine beliebige verfügbare IP-Adresse zugewiesen. Wenn die Ziel-IP-Adresse angegeben ist, sie aber bereits von einem anderen virtuellen Computer in Azure verwendet wird, tritt beim Failover ein Fehler auf. Wenn die Netzwerkkarte des Quellcomputers für DHCP konfiguriert ist, wird für Azure als Einstellung DHCP verwendet.

## Schritt 12: Erstellen eines Wiederherstellungsplans und Ausführen eines Failovers

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Sie können ein Failover für einen einzelnen Computer oder auch für mehrere virtuelle Computer durchführen, auf denen die gleiche Aufgabe oder die gleiche Workload ausgeführt wird. Um für mehrere Computer ein gleichzeitiges Failover durchzuführen, fügen Sie sie einem Wiederherstellungsplan hinzu.

### Erstellen eines Wiederherstellungsplans

1. Klicken Sie auf der Seite **Wiederherstellungspläne** auf **Wiederherstellungsplan hinzufügen**, und fügen Sie einen Wiederherstellungsplan hinzu. Geben Sie Details für den Plan an, und wählen Sie **Azure** als Ziel aus.

	![Wiederherstellungsplan konfigurieren](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. Wählen Sie unter **Virtuellen Computer auswählen** eine Schutzgruppe aus, und wählen Sie dann Computer in der Gruppe aus, die dem Wiederherstellungsplan hinzugefügt werden sollen.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Sie können den Plan anpassen und Gruppen erstellen sowie die Reihenfolge festlegen, in der für die Computer im Wiederherstellungsplan ein Failover durchgeführt wird. Sie können auch Skripts und Aufforderungen für manuelle Aktionen hinzufügen. Skripts können manuell oder mithilfe von [Azure Automation-Runbooks](site-recovery-runbook-automation.md) erstellt werden. Erfahren Sie mehr über das [Anpassen von Wiederherstellungsplänen](site-recovery-create-recovery-plans.md).

## Ausführen eines Failovers

Beachten Sie vor dem Ausführen eines Failovers Folgendes:


- Stellen Sie sicher, dass der Verwaltungsserver ausgeführt und verfügbar ist. Andernfalls tritt beim Failover ein Fehler auf.
- Bei einem ungeplanten Failover beachten Sie Folgendes:

	- Falls möglich, sollten Sie primäre Computer herunterfahren, bevor Sie ein ungeplantes Failover ausführen. Dadurch wird sichergestellt, dass die Quell- und Replikatcomputer nicht gleichzeitig ausgeführt werden. Wenn Sie virtuelle VMware-Computer replizieren, können Sie beim Ausführen eines ungeplanten Failovers festlegen, dass Site Recovery die Quellcomputer möglichst herunterfahren soll. Dies funktioniert abhängig vom Zustand am primären Standort. Wenn Sie physische Server replizieren, bietet Site Recovery diese Möglichkeit nicht. 
	- Beim Durchführen eines ungeplanten Failovers wird die Datenreplikation der primären Computer beendet. Das heißt, dass nach dem Beginn des Failovers keine Datenänderungen mehr übertragen werden.
	
- Wenn Sie nach dem Failover eine Verbindung mit dem virtuellen Replikatcomputer in Azure herstellen möchten, aktivieren Sie vor dem Ausführen des Failovers die Remotedesktopverbindung auf dem Quellcomputer, und lassen Sie die RDP-Verbindung in der Firewall zu. Außerdem müssen Sie RDP (Remote Desktop Protocol, Remotedesktopprotokoll) nach dem Failover auf dem öffentlichen Endpunkt des virtuellen Azure-Computers zulassen. Befolgen Sie diese [bewährten Methoden](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx), um sicherzustellen, dass RDP nach dem Failover funktioniert.


### Ausführen eines Testfailovers

Führen Sie ein Testfailover aus, um Ihre Prozesse für Failover und Wiederherstellung in einem isolierten Netzwerk zu simulieren, das keine Auswirkungen auf Ihre Produktionsumgebung hat. Bei dieser Simulation wird die reguläre Replikation ganz normal fortgesetzt. Das Testfailover wird an der Quelle initiiert, und Sie können es auf verschiedene Weise ausführen:

- **Geben Sie kein Azure-Netzwerk an**: Wenn Sie das Testfailover ohne ein Netzwerk ausführen, wird beim Test einfach überprüft, ob die virtuellen Computer starten und ordnungsgemäß in Azure angezeigt werden. Die virtuellen Computer werden nach einem Failover nicht mit einem Azure-Netzwerk verbunden.
- **Geben Sie ein Azure-Netzwerk an**: Bei dieser Art von Failover wird überprüft, ob die gesamte Replikationsumgebung wie erwartet hochgefahren wird und die betreffenden virtuellen Azure-Computer mit dem angegebenen Netzwerk verbunden werden. 


1. Wählen Sie auf der Seite **Wiederherstellungspläne** den Plan aus, und klicken Sie auf **Testfailover**.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. Wählen Sie unter **Testfailover bestätigen** den Eintrag **Keine** aus, wenn Sie für das Testfailover kein Azure-Netzwerk verwenden möchten, oder wählen Sie das Netzwerk aus, mit dem die Test-VMs nach dem Failover verbunden werden sollen. Klicken Sie auf das Häkchen, um das Failover zu starten.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Überwachen Sie den Verlauf des Failovers auf der Registerkarte **Aufträge**.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Nach Abschluss des Failovers sollte der Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt werden. Wenn Sie eine RDP-Verbindung mit dem virtuellen Azure-Computer herstellen möchten, müssen Sie auf dem VM-Endpunkt Port 3389 öffnen.

5. Wenn das Failover die Endphase des Tests erreicht und Sie fertig sind, klicken Sie auf „Test abschließen“, um den Test zu beenden. Erfassen und speichern Sie unter „Hinweise“ alle Beobachtungen im Zusammenhang mit dem Testfailover.

6. Klicken Sie auf **Das Testfailover ist abgeschlossen.**, um eine automatische Bereinigung der Testumgebung durchzuführen. Nach Abschluss dieses Vorgangs wird für das Testfailover der Status **Abgeschlossen** angezeigt. Alle Elemente und virtuellen Computer, die im Zuge des Testfailovers automatisch erstellt wurden, werden gelöscht. Beachten Sie: Sollte das Testfailover länger als zwei Wochen bestehen bleiben, wird sein Abschluss erzwungen.


	


### Ausführen eines ungeplanten Failovers

Ein ungeplantes Failover wird in Azure initiiert und kann auch ausgeführt werden, wenn der primäre Standort nicht verfügbar ist.


1. Wählen Sie auf der Seite **Wiederherstellungspläne** den Plan aus, und klicken Sie auf **Failover** > **Ungeplantes Failover**.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Wenn Sie virtuelle VMware-Computer replizieren, können Sie auswählen, dass versucht werden soll, die lokalen VMs herunterzufahren. Dieser Versuch wird mit den besten Kräften ausgeführt. Das Failover wird unabhängig von seinem Erfolg in jedem Fall fortgesetzt. Wenn er nicht erfolgreich ist, werden die Fehlerdetails auf der Registerkarte **Aufträge** unter **Ungeplante Failoveraufträge** angezeigt.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

	>[AZURE.NOTE]Wenn Sie physische Server replizieren, ist diese Option nicht verfügbar. Sie müssen diese nach Möglichkeit manuell herunterfahren.
	
3. Überprüfen Sie unter **Failover bestätigen** die Failoverrichtung (zu Azure), und wählen Sie den Wiederherstellungspunkt aus, der für das Failover verwendet werden soll. Wenn Sie beim Konfigurieren der Replikationseigenschaften die Option für Multi-VM aktiviert haben, können Sie zum letzten anwendungs- oder absturzkonsistenten Wiederherstellungspunkt wiederherstellen. Sie können auch **Benutzerdefinierter Wiederherstellungspunkt** auswählen, um den Zustand eines früheren Zeitpunkts wiederherzustellen. Klicken Sie auf das Häkchen, um das Failover zu starten.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Warten Sie, bis der ungeplante Failoverauftrag abgeschlossen ist. Sie können den Verlauf des Failovers auf der Registerkarte **Aufträge** überwachen. Beachten Sie, dass der Wiederherstellungsplan bei einem ungeplanten Failover auch dann bis zum Abschluss ausgeführt wird, wenn Fehler auftreten. Außerdem sollte der Azure-Replikatcomputer im Azure-Portal unter „Virtuelle Computer“ angezeigt werden.

### Herstellen einer Verbindung mit replizierten virtuellen Azure-Computern nach dem Failover

Um nach einem Failover eine Verbindung mit den replizierten virtuellen Computern in Azure herzustellen, sind folgende Schritte notwendig:

1. Auf dem primären Computer muss eine Remotedesktopverbindung aktiviert werden.
2. In der Windows-Firewall des primären Computers muss RDP zugelassen werden.
3. Nach dem Failover müssen Sie RDP zum öffentlichen Endpunkt für virtuelle Azure-Computer hinzufügen.

Erfahren Sie, [wie Sie dies einrichten](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## Bereitstellen zusätzlicher Prozessserver

Wenn Sie Ihre Bereitstellung über 200 Quellcomputer hinaus horizontal hochskalieren müssen oder die gesamte tägliche Änderungsrate 2 TB überschreitet, benötigen Sie zusätzliche Prozessserver zur Bewältigung des Datenverkehrsaufkommens. Zum Einrichten eines zusätzlichen Prozessservers überprüfen Sie zunächst die Anforderungen unter [Zusätzliche Prozessserver](#additional-process-servers) und befolgen dann die folgenden Anweisungen. Nachdem Sie den Prozessserver eingerichtet haben, können Sie die Quellcomputer so konfigurieren, dass sie diesen verwenden.

### Einrichten eines zusätzlichen Prozessservers

Gehen Sie beim Einrichten eines zusätzlichen Prozessservers wie folgt vor:

- Führen Sie den Assistenten für einheitliches Setup aus, und konfigurieren Sie einen Verwaltungsserver ausschließlich als Prozessserver.
- Wenn Sie die Datenreplikation nur mit dem neuen Prozessserver verwalten möchten, müssen Sie Ihre geschützten Computer entsprechend migrieren.

### Installieren des Prozessservers

1. Laden Sie auf der Seite „Schnellstart“ die einheitliche Installationsdatei für die Installation der Komponenten von Site Recovery herunter. Führen Sie das Setup aus.
2. Wählen Sie unter **Vorbereitung** die Option **Weitere Prozessserver zum horizontalen Skalieren der Bereitstellung hinzufügen** aus.

	![Prozessserver hinzufügen](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Beenden Sie den Assistenten genauso wie beim [Einrichten](#step-5:-install-the-management-server) des ersten Verwaltungsservers.
4. Geben Sie unter **Konfigurationsserverdetails** die IP-Adresse des ursprünglichen Verwaltungsservers ein, auf dem Sie den Konfigurationsserver installiert haben, und geben Sie die Passphrase an. Führen Sie auf dem ursprünglichen Verwaltungsserver **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** aus, um die Passphrase abzurufen.

	![Prozessserver hinzufügen](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### Migrieren der Computer für die Verwendung des neuen Prozessservers

1. Öffnen Sie **Konfigurationsserver** > **Server** > Name des ursprünglichen Verwaltungsservers > **Serverdetails**.

	![Prozessserver aktualisieren](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Klicken Sie in der Liste **Prozessserver** neben dem Server, den Sie ändern möchten, auf **Prozessserver ändern** .

	![Prozessserver aktualisieren](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. Wählen Sie unter **Prozessserver ändern** > **Zielprozessserver** den neuen Verwaltungsserver aus, und wählen Sie dann die virtuellen Computer aus, für die der neue Prozessserver verantwortlich sein soll. Klicken Sie auf das Informationssymbol, um Informationen über den Server zu erhalten. Um Sie bei Entscheidungen zur Last zu unterstützen, wird der durchschnittliche Speicherplatz angezeigt, der zum Replizieren jedes ausgewählten virtuellen Computers auf den neuen Prozessserver benötigt wird. Klicken Sie auf das Häkchen, um mit dem Replizieren auf den neuen Prozessserver zu beginnen.

	![Prozessserver aktualisieren](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)

	


## VMware-Berechtigungen für den vCenter-Zugriff

Der Prozessserver kann virtuelle Computer auf einem vCenter-Server automatisch ermitteln. Zum Ausführen dieser automatischen Ermittlung müssen Sie auf vCenter-Ebene eine Rolle namens „Azure\_Site\_Recovery“ definieren, die Site Recovery den Zugriff auf den vCenter-Server erlaubt. Beachten Sie: Wenn Sie VMware-Computer nur zu Azure migrieren müssen und kein Failback von Azure auszuführen brauchen, können Sie eine nur zum Lesezugriff berechtigende schreibgeschützte Rolle definieren – dies ist ausreichend. Richten Sie die Berechtigungen so ein wie in [Schritt 6: Einrichten der Anmeldeinformationen für den vCenter-Server](#step-6:-set-up-credentials-for-the-vcenter-server) beschrieben. Die Berechtigungen für die Rollen werden in der folgenden Tabelle zusammengefasst.

**Rolle** | **Details** | **Berechtigungen**
--- | --- | ---
Rolle „Azure\_Site\_Recovery“ | Ermittlung von virtuellen VMware-Computern |Weisen Sie diese Berechtigungen für den v-Center-Server zu:<br/><br/>Datastore -> Allocate space, Browse datastore, Low level file operations, Remove file, Update virtual machine files<br/><br/>Netzwerk -> Network assign<br/><br/>Ressource -> Assign virtual machine to resource pool, Migrate powered off virtual machine, Migrate powered on virtual machine<br/><br/>Aufgaben -> Create task, update task<br/><br/>Virtueller Computer -> Konfiguration<br/><br/>Virtueller Computer -> Interact -> Answer question, Device connection, Configure CD media, Configure floppy media, Power off, Power on, VMware tools install<br/><br/>Virtueller Computer -> Inventory -> Create, Register, Unregister<br/><br/>Virtueller Computer -> Bereitstellung -> Allow virtual machine download, Allow virtual machine files upload<br/><br/>Virtueller Computer -> Momentaufnahmen -> Remove snapshots
vCenter-Benutzerrolle | Ermittlung von virtuellen VMware-Computern/Failover ohne Herunterfahren der Quell-VM | Weisen Sie diese Berechtigungen für den vCenter-Server zu:<br/><br/>Data Center object –> Propagate to Child Object, Rolle = Read-only <br/><br/>Der Benutzer wird auf Datencenterebene zugewiesen und hat daher Zugriff auf alle Objekte im Datencenter. Wenn Sie den Zugriff einschränken möchten, weisen Sie die Rolle **No access** mit **Propagate to child object** den untergeordneten Objekten zu (ESX-Hosts, Datenspeicher, VMs und Netzwerke). 
vCenter-Benutzerrolle | Failover und Failback | Weisen Sie diese Berechtigungen für den vCenter-Server zu:<br/><br/>Datacenter object – Propagate to child object, Rolle = Azure\_Site\_Recovery<br/><br/>Der Benutzer wird auf Datencenterebene zugewiesen und hat daher Zugriff auf alle Objekte im Datencenter. Wenn Sie den Zugriff einschränken möchten, weisen Sie die Rolle **No access** mit dem Objekt **Propagate to child object** dem untergeordneten Objekt zu (ESX-Hosts, Datenspeicher, VMs und Netzwerke). 



## Hinweise und Informationen zu Drittanbietersoftware

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## Nächste Schritte

[Informieren Sie sich über das Failback](site-recovery-failback-azure-to-vmware-classic.md) von unter Azure ausgeführten Computern zur lokalen Umgebung.

<!---HONumber=AcomDC_0114_2016-->
