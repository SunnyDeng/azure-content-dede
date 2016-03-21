<properties
	pageTitle="Vorbereiten der Site Recovery-Bereitstellung | Microsoft Azure"
	description="Dieser Artikel beschreibt, wie Sie die Bereitstellung der Replikation mit Azure Site Recovery vorbereiten."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/08/2016"
	ms.author="raynew"/>

# Vorbereiten der Azure Site Recovery-Bereitstellung

Der Dienst Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem sekundären lokalen Datencenter repliziert werden. Eine kurze Übersicht Eine kurze Übersicht über das Gesamtthema finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md).

## Übersicht

Azure Site Recovery unterstützt die Replikation von virtuellen VMware- und Hyper-V-Maschinen sowie physischen Servern in Azure oder einem sekundären Datencenter. In diesem Artikel wird beschrieben, wie Sie die Bereitstellung von Azure Site Recovery für diese Replikationsszenarien vorbereiten.

Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) veröffentlichen.

## Bereitstellungsanforderungen für die Hyper-V-Replikation

In der Tabelle sind die allgemeinen Bereitstellungsanforderungen für die Hyper-V-Replikation in Azure (mit und ohne VMM) und an einem sekundären Standort zusammengefasst. Anhand der Tabelle können Sie die allgemeinen Anforderungen für jedes Replikationsszenario nachvollziehen und vergleichen. Die Tabelle enthält darüber hinaus einen Link zu ausführlichen Bereitstellungsvoraussetzungen.

**Replizieren in Azure (mit VMM)** | **Replizieren in Azure (ohne VMM)** | **Replizieren an einem sekundären Standort (mit VMM)**
---|---|---
**VMM:** In System Center 2012 R2 wird mindestens ein VMM-Server ausgeführt. Der VMM-Server muss mindestens eine Cloud mit einer VMM-Hostgruppe oder mehreren VMM-Hostgruppen enthalten.<br/><br/> **Hyper-V:** Mindestens ein Hyper-V-Hostserver im lokalen Datencenter mit Windows Server 2012 R2 oder höher. Der Hyper-V-Server muss sich in einer Hostgruppe in einer VM-Cloud befinden.<br/><br/> **Virtuelle Maschinen:** Sie benötigen mindestens eine VM auf dem Hyper-V-Quellserver. VMs, für die eine Replikation in Azure ausgeführt wird, müssen die [Anforderungen für virtuelle Azure-Maschinen](#azure-virtual-machine-requirements) erfüllen.<br/><br/> **Azure-Konto:** Sie benötigen ein [Azure](https://azure.microsoft.com/)-Konto und ein Azure-Abonnement.<br/><br/> **Azure-Speicher**: Sie benötigen ein [Azure-Speicherkonto](../storage/storage-redundancy.md#geo-redundant-storage), um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Maschinen werden bei einem Failover hochgefahren.<br/><br/> **Netzwerkzuordnung:** Richten Sie eine Netzwerkzuordnung ein, sodass alle Maschinen, die im gleichen Azure-Netzwerk ein Failover ausführen, eine Verbindung miteinander herstellen können, und zwar unabhängig vom Wiederherstellungsplan, zu dem sie gehören. Wenn ein Netzwerkgateway im Azure-Zielnetzwerk vorhanden ist, können die virtuellen Maschinen zudem eine Verbindung mit den lokalen virtuellen Maschinen herstellen. Wenn Sie keine Netzwerkzuordnung einrichten, können nur Computer Verbindungen miteinander herstellen, für die das Failover in demselben Wiederherstellungsplan festgelegt ist.<br/><br/> **Anbieter/Agents:** Während der Bereitstellung installieren Sie den Azure Site Recovery-Anbieter auf VMM-Servern und den Azure Recovery Services-Agent auf Hyper-V-Hostservern. Der Anbieter kommuniziert mit Azure Site Recovery. Der Agent verarbeitet die Datenreplikation zwischen Quell- und Zielservern mit Hyper-V. Auf VMs wird nichts installiert.<br/><br/> **Internetkonnektivität:** Über den VMM-Server und die Hyper-V-Hosts<br/><br/> **Anbieterkonnektivität:** Wenn der Anbieter über einen Proxy eine Verbindung mit Site Recovery herstellt, müssen Sie sicherstellen, dass der Proxy auf die Site Recovery-URLs zugreifen kann.<br/><br/> [Ausführliche Bereitstellungsvoraussetzungen](site-recovery-vmm-to-azure.md#before-you-start) | **Hyper-V:** Mindestens ein Hyper-V-Server am Quell- und Zielstandort mit Windows Server 2012 R2 oder höher.<br/><br/> **Virtuelle Maschinen:** Mindestens eine VM auf dem Hyper-V-Quellserver. VMs, für die eine Replikation in Azure ausgeführt wird, müssen die [Voraussetzungen für virtuelle Azure-Maschinen](#azure-virtual-machine-requirements)<br/><br/> erfüllen.**Azure-Konto:** Sie benötigen ein [Azure](https://azure.microsoft.com/)-Konto und ein Azure-Abonnement.<br/><br/> **Azure-Speicher:** Sie benötigen ein [Azure-Speicherkonto](../storage/storage-redundancy.md#geo-redundant-storage), um replizierte Daten zu speichern.<br/><br/> **Anbieter/Agents:** Während der Bereitstellung installieren Sie sowohl den Azure Site Recovery-Anbieter als auch den Azure Recovery Services-Agent auf Hyper-V-Hostservern oder -Clustern. Auf VMs wird nichts installiert.<br/><br/> **Internetkonnektivität:** Über die Hyper-V-Hosts<br/><br/> **Anbieterkonnektivität:** Wenn der Anbieter über einen Proxy eine Verbindung herstellt, müssen Sie sicherstellen, dass der Proxy auf die Site Recovery-URLs zugreifen kann.<br/><br/> [Ausführliche Bereitstellungsvoraussetzungen](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | **VMM:** Der VMM-Quellserver muss mindestens eine Cloud mit einer VMM-Hostgruppe oder mehreren VMM-Hostgruppen enthalten. Für Clouds sollte das Hyper-V-Funktionsprofil eingestellt sein. <br/><br/>**Hyper-V**: Mindestens ein Hyper-V-Server am Quell- und Zielstandort mit Windows Server 2012 oder höher und den neuesten Updates. Der Hyper-V-Server muss sich in einer Hostgruppe in einer VMM-Cloud befinden.<br/><br/> **Virtuelle Maschinen:** Mindestens eine VM in der VMM-Quellcloud <br/><br/> **Netzwerkzuordnung:** Richten Sie eine Netzwerkzuordnung ein, sodass virtuelle Maschinen nach einem Failover eine Verbindung mit den entsprechenden Netzwerken herstellen können und replizierte virtuelle Maschinen optimal auf Hyper-V-Zielhostservern platziert werden. Wenn Sie die Netzwerkzuordnung nicht konfigurieren, werden für replizierte Maschinen nach einem Failover keine Verbindungen mit einem VM-Netzwerk hergestellt.<br/><br/> **Speicherzuordnung:** Optional können Sie mit der Speicherzuordnung sicherstellen, dass virtuelle Maschinen nach dem Failover optimal mit dem Speicher verbunden sind (die replizierte VM wird standardmäßig in dem auf dem Hyper-V-Zielserver angegebenen Speicherort gespeichert).<br/><br/> **SAN-Replikation:** Wenn Sie zwischen zwei lokalen VMM-Standorten eine SAN-Replikation ausführen möchten, können Sie Ihre vorhandene SAN-Umgebung dazu nutzen. Sehen Sie sich die [unterstützten SAN-Arrays](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) an.<br/><br/> **Anbieter/Agents:** Während der Bereitstellung installieren Sie den Azure Site Recovery-Anbieter auf VMM-Servern für die Kommunikation mit Azure Site Recovery. Replikation zwischen Quell- und Zielservern mit Hyper-V über LAN/VPN.<br/><br/> **Internetkonnektivität:** Ausschließlich auf dem VMM-Server<br/><br/> **Anbieterkonnektivität:** Wenn der Anbieter über einen Proxy eine Verbindung herstellt, müssen Sie sicherstellen, dass auf die Site Recovery-URLs zugegriffen werden kann.<br/><br/> [Ausführliche Bereitstellungsvoraussetzungen](site-recovery-vmm-to-vmm.md#before-you-start)


## Bereitstellungsanforderungen für die Replikation von VMware-VMs und physischen Servern

In der Tabelle werden die Anforderungen für die Replikation von VMware-VMs und physischen Windows-/Linux-Servern in Azure und an sekundären Standorten zusammengefasst.

>[AZURE.NOTE] Sie können VMware-VMs und physische Server mit einem [erweiterten](site-recovery-vmware-to-azure-classic.md) Bereitstellungsmodell in Azure replizieren. Alternativ können Sie ein [Legacymodell](site-recovery-vmware-to-azure-classic-legacy.md) nutzen, das bei älteren Bereitstellungen verwendet wurde. Die folgende Tabelle enthält die Bereitstellungsanforderungen für jedes Modell.

**Replizieren in Azure (erweitert)** | **Replizieren in Azure (Legacymodell)** | **Replizieren an einem sekundären Standort**
---|---|---
**Lokaler Verwaltungsserver:** An Ihrem lokalen Standort benötigen Sie einen dedizierten Server, der als Verwaltungsserver fungiert. Alle Site Recovery-Komponenten werden auf diesem Server installiert.<br/><br/> **Zusätzliche Prozessserver:** Auf dem Verwaltungsserver wird standardmäßig ein Prozessserver installiert. Sie können jedoch zum Skalieren Ihrer Bereitstellung optional zusätzliche lokale Prozessserver installieren.<br/><br/> **VMware vCenter/ESXi**: Für die Replikation von VMware-VMs (oder das Failback physischer Server) benötigen Sie einen vSphere ESX/ESXi-Hypervisor, auf dem sich die VMs befinden. Zum Verwalten der ESXi-Hosts wird ein vCenter-Server empfohlen.</br><br/> **Failback:** Sie benötigen eine VMware-Umgebung, um ein Failback von Azure auszuführen. Dies gilt auch für die Replikation von physischen Servern. Darüber hinaus müssen Sie einen Prozessserver als Azure-VM einrichten. Falls Sie ein Failback bei hohem Datenverkehrsaufkommen ausführen, sollten Sie unter Umständen einen zusätzlichen lokalen Masterzielserver einrichten. [Weitere Informationen](site-recovery-failback-azure-to-vmware-classic.md)<br/><br/> **Azure-Konto:** Sie benötigen ein [Azure](https://azure.microsoft.com/)-Konto und ein Azure-Abonnement.<br/><br/> **Azure-Speicher:** Sie benötigen ein [Azure-Speicherkonto](../storage/storage-redundancy.md#geo-redundant-storage), um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Maschinen werden bei einem Failover hochgefahren.<br/><br/> **Virtuelles Azure-Netzwerk:** Sie benötigen ein virtuelles Azure-Netzwerk, mit dem die Azure-VMs bei einem Failover eine Verbindung herstellen. Sie benötigen außerdem eine VPN-Verbindung (oder Azure ExpressRoute) zwischen dem Azure-Netzwerk und dem lokalen Standort, um nach dem Failover ein Failback auszuführen.<br/><br/> **Geschützte Computer:** Mindestens eine virtuelle VMware-Maschine oder ein physischer Windows-/Linux-Server. Während der Bereitstellung wird der Mobilitätsdienst auf jedem Computer installiert, den Sie replizieren möchten.<br/><br/> **Konnektivität:** Wenn der Verwaltungsserver über einen Proxy eine Verbindung mit Site Recovery herstellt, müssen Sie sicherstellen, dass der Proxyserver eine Verbindung mit spezifischen URLs herstellen kann.<br/><br/> [Ausführliche Bereitstellungsvoraussetzungen](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | **Primärer Standort:** Sie müssen einen Prozessserver einrichten.<br/><br/> **Failback:** Sie benötigen eine VMware-Umgebung, um ein Failback von Azure auszuführen. Dies gilt auch für die Replikation von physischen Servern. Am lokalen Standort müssen Sie einen vContinuum-Server und einen Masterzielserver einrichten. In Azure müssen Sie einen Prozessserver einrichten. [Weitere Informationen](site-recovery-failback-azure-to-vmware-classic-legacy.md)<br/><br/> **Azure-Konto:** Sie benötigen ein [Azure](https://azure.microsoft.com/)-Konto und ein Azure-Abonnement.<br/><br/> **Azure-Speicher:** Sie benötigen ein [Azure-Speicherkonto](../storage/storage-redundancy.md#geo-redundant-storage), um replizierte Daten zu speichern. Replizierte Daten werden im Azure-Speicher gespeichert, und virtuelle Azure-Maschinen werden bei einem Failover hochgefahren.<br/><br/> **VMs mit Azure-Infrastruktur**: Sie müssen einen Konfigurationsserver und einen Masterzielserver als Azure-VMs einrichten.<br/><br/> **Virtuelles Azure-Netzwerk**: Sie benötigen ein virtuelles Azure-Netzwerk, in dem der Konfigurationsserver und der Masterzielserver bereitgestellt werden. Azure-VMs werden nach einem Failover mit diesem Netzwerk verbunden.<br/><br/> **Geschützte Computer:** Mindestens eine virtuelle VMware-Maschine oder ein physischer Windows-/Linux-Server. Während der Bereitstellung wird der Mobilitätsdienst auf jedem Computer installiert, den Sie replizieren möchten.<br/><br/> **Konnektivität:** Wenn der Verwaltungsserver über einen Proxy eine Verbindung mit Site Recovery herstellt, müssen Sie sicherstellen, dass der Proxyserver eine Verbindung mit spezifischen URLs herstellen kann.<br/><br/> [Ausführliche Bereitstellungsvoraussetzungen](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start) | **Primärer Standort:** Ein dedizierter Windows-Server (physisch oder eine virtuelle VMware-Maschine)<br/><br/> **Sekundärer Standort:** Dedizierte Konfigurations- und Masterzielserver<br/><br/> **Geschützte Computer:** Mindestens eine virtuelle VMware-Maschine oder ein physischer Windows-/Linux-Server. Während der Bereitstellung wird der vereinheitlichte Agent auf jeder Maschine installiert.





## Anforderungen für virtuelle Azure-Maschinen

Sie können Site Recovery zum Replizieren virtueller Maschinen und physischer Server bereitstellen, auf denen ein von Azure unterstütztes Betriebssystem ausgeführt wird. Dies gilt für die meisten Versionen von Windows und Linux. Sie müssen sicherstellen, dass lokale virtuelle Maschinen, die Sie schützen möchten, den Azure-Anforderungen entsprechen.


**Feature** | **Unterstützung** | **Details**
---|---|---
Betriebssystem des Hyper-V-Hosts | Windows Server 2012 R2 | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Betriebssystem des VMware-Hypervisors | Unterstütztes Betriebssystem | [Details](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Gastbetriebssystem | Bei der Replikation von Hyper-V zu Azure unterstützt Site Recovery alle Betriebssysteme, die [von Azure unterstützt werden](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Lesen Sie bei der Replikation von VMware und physischen Servern die [Voraussetzungen](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) für Windows und Linux. | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Architektur des Gastbetriebssystems | 64 Bit | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des Betriebssystem-Datenträgers | Bis zu 1.023 GB | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Betriebssystem-Datenträgern | 1 | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Datenträgern für Daten | Maximal 16 (der Höchstwert ist eine Funktion der Größe des erstellten virtuellen Computers: 16 = XL) | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des VHD-Datenträgers | Maximal 1.023 GB | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Netzwerkadapter | Es werden mehrere Adapter unterstützt. |
Statische IP-Adresse | Unterstützt | Wenn für den primären virtuellen Computer eine statische IP-Adresse verwendet wird, können Sie die statische IP-Adresse für den virtuellen Computer angeben, der in Azure erstellt wird. Beachten Sie, dass statische IP-Adressen für einen virtuellen Linux-Computer unter Hyper-V nicht unterstützt werden. 
iSCSI-Datenträger | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Freigegebene VHD | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Fiber-Channel-Datenträger | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Festplattenformat| VHD <br/><br/> VHDX | Obwohl VHDX in Azure derzeit nicht unterstützt wird, führt Site Recovery automatisch die Konvertierung von VHDX in VHD durch, wenn Sie das Failover auf Azure anstoßen. Wenn Sie das Failover in den lokalen Speicher durchführen, wird für die virtuellen Computer weiterhin das VHDX-Format verwendet.
Name des virtuellen Computers| Zwischen 1 und 63 Zeichen. Ist auf Buchstaben, Zahlen und Bindestriche beschränkt. Muss mit einem Buchstaben oder einer Zahl beginnen und enden. | Aktualisieren Sie den Wert in den Eigenschaften für virtuelle Computer in Site Recovery.
Typ des virtuellen Computers | <p>Generation 1</p> <p>Generation 2 – Windows</p> | Virtuelle Maschine der 2. Generation mit Betriebssystem-Datenträgertyp Basisfestplatte enthält 1 oder 2 Datenvolumes mit dem Datenträgerformat VHDX, das maximal 300 GB unterstützt. Virtuelle Computer der 2. Generation mit Linux werden nicht unterstützt. [Weitere Informationen](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Optimieren der Bereitstellung

Mit den folgenden Tipps können Sie Ihre Bereitstellung optimieren und skalieren.

- **Volumegröße des Betriebssystems**: Wenn Sie einen virtuellen Computer zu Azure replizieren, muss das Volume des Betriebssystems kleiner als 1 TB sein. Falls Sie mehr als ein Volume nutzen, können Sie diese manuell auf einen anderen Datenträger verschieben, bevor Sie mit der Bereitstellung beginnen.
- **Größe des Datenträgers für Daten**: Wenn Sie zu Azure replizieren, können Sie auf einem virtuellen Computer bis zu 32 Datenträger für Daten nutzen, die jeweils maximal 1 TB groß sind. Für einen virtuellen Computer mit ~32 TB können Sie die Replikation und das Failover effektiv durchführen.
- **Beschränkungen des Wiederherstellungsplans**: Für Site Recovery ist eine Skalierung auf Tausende von virtuellen Computern möglich. Wiederherstellungspläne wurden als Modell für Anwendungen entwickelt, für die das Failover zusammen erfolgen soll. Die Anzahl von Computern in einem Wiederherstellungsplan wird daher auf 50 beschränkt.
- **Beschränkungen für Azure-Dienste**: Jedes Azure-Abonnement verfügt über eine Reihe von standardmäßigen Beschränkungen für Kerne, Clouddienste usw. Wir empfehlen Ihnen, ein Testfailover auszuführen, um die Verfügbarkeit von Ressourcen in Ihrem Abonnement zu überprüfen. Sie können diese Beschränkungen über den Azure-Support ändern.
- **Kapazitätsplanung**: Erfahren Sie mehr über die [Kapazitätsplanung](site-recovery-capacity-planner.md) für Site Recovery.
- **Replikationsbandbreite**: Beachten Sie Folgendes, falls die Replikationsbandbreite knapp ist:
	- **ExpressRoute**: Für Site Recovery können Azure ExpressRoute und WAN-Optimierer wie Riverbed verwendet werden. [Erfahren Sie mehr](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) zu ExpressRoute.
	- **Replikationsdatenverkehr**: Site Recovery führt eine intelligente anfängliche Replikation durch, bei der nur Datenblöcke verwendet werden, und nicht die gesamte VHD. Während der laufenden Replikation werden nur Änderungen repliziert.
	- **Netzwerkdatenverkehr**: Sie können Netzwerkdatenverkehr steuern, der für die Replikation verwendet wird, indem Sie [Windows-Dienstqualität](https://technet.microsoft.com/library/hh967468.aspx) mit einer Richtlinie nutzen, die auf der IP-Zieladresse und dem Port basiert. Falls Sie die Replikation zu Azure Site Recovery mit dem Azure Backup-Agent durchführen, können Sie die Drosselung für den Agent konfigurieren. [Weitere Informationen](https://support.microsoft.com/kb/3056159)
- **RTO**: Wenn Sie die Recovery Time Objective (RTO) messen möchten, die mit Site Recovery zu erwarten ist, sollten Sie ein Testfailover ausführen und die Site Recovery-Aufträge anzeigen, um zu analysieren, wie lange die Durchführung der Vorgänge dauert. Falls Sie das Failover zu Azure nutzen, erzielen Sie den besten RTO-Wert, wenn Sie alle manuellen Aktionen automatisieren. Integrieren Sie hierfür die Azure-Automatisierung und Wiederherstellungspläne.
- **RPO**: Site Recovery unterstützt beim Replizieren zu Azure eine nahezu synchrone Recovery Point Objective (RPO). So wird für eine ausreichende Bandbreite zwischen Ihrem Rechenzentrum und Azure gesorgt.





## Nächste Schritte

Nachdem Sie die allgemeinen Bereitstellungsanforderungen kennengelernt und miteinander verglichen haben, können Sie die ausführlichen Voraussetzungen lesen und mit der Bereitstellung der einzelnen Szenarien beginnen.

- [Repliziert virtuelle VMware-Computer zu Azure](site-recovery-vmware-to-azure-classic.md)
- [Replizieren physischer Server in Azure](site-recovery-vmware-to-azure-classic.md)
- [Replizieren von Hyper-V-Servern in VMM-Clouds in Azure](site-recovery-vmm-to-azure.md)
- [Replizieren virtueller Hyper-V-Maschinen (ohne VMM) in Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replizieren von Hyper-V-VMs an einem sekundären Standort](site-recovery-vmm-to-vmm.md)
- [Replizieren von Hyper-V-VMs an einem sekundären Standort mit SAN](site-recovery-vmm-san.md)
- [Replizieren von Hyper-V-VMs mit einem einzelnen VMM-Server](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0309_2016-->