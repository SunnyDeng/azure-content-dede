<properties
	pageTitle="Vorbereiten der Site Recovery-Bereitstellung | Microsoft Azure"
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Maschinen und physischer Server auf lokalen Servern in Azure oder an einem sekundären Datencenter."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/07/2015"
	ms.author="raynew"/>

# Vorbereiten der Azure Site Recovery-Bereitstellung

## Informationen zum Artikel

In diesem Artikel wird beschrieben, wie Sie die Bereitstellung von Azure Site Recovery vorbereiten. Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese bitte im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Schutz für virtuelle Hyper-V-Maschinen

Für den Schutz von virtuellen Hyper-V-Maschinen stehen mehrere Bereitstellungsoptionen zur Verfügung. Sie können lokale Hyper-VMs in Azure oder einem sekundären Datencenter replizieren. Bei jeder Bereitstellung sind verschiedene Anforderungen zu beachten.

**Anforderung** | **Replizieren in Azure (mit VMM)** | **Replizieren von Hyper-V-VMs in Azure (ohne VMM)** | **Replizieren von Hyper-V-VMs an einem sekundären Standort (mit VMM)** | **Details**
---|---|---|---|---
**VMM** | VMM unter System Center 2012 R2 <br/><br/>Mindestens eine VMM-Cloud, die mindestens eine VMM-Hostgruppe enthält | – | VMM-Server am primären und am sekundären Standort mit mindestens System Center 2012 SP1 und aktuellen Updates <br/><br/> Mindestens eine Cloud auf jedem VMM-Server. Für die Clouds sollte das Hyper-V-Kapazitätsprofil eingestellt sein.<br/><br/> Die Quellcloud muss mindestens eine VMM-Hostgruppe enthalten. | Optional. System Center VMM muss zum Replizieren virtueller Hyper-V-Maschinen in Azure nicht bereitgestellt werden. Falls Sie die Lösung dennoch bereitstellen, müssen Sie ihre richtige Einrichtung sicherstellen. Dazu gehören u. a. die Ausführung der richtigen VMM-Version und die Einrichtung von Clouds.
**Hyper-V** | Mindestens ein Hyper-V-Hostserver im lokalen Datencenter mit Windows Server 2012 R2 oder höher | Mindestens ein Hyper-V-Server am Quell- und Zielstandort mit mindestens Windows Server 2012 R2 mit Internetverbindung.<br/><br/> Die Hyper-V-Server müssen sich in einer Hostgruppe in einer VMM-Cloud befinden. | Mindestens ein Hyper-V-Server am Quell- und Zielstandort mit mindestens Windows Server 2012 mit den neuesten Updates und Internetverbindung.<br/><br/> Die Hyper-V-Server müssen sich in einer Hostgruppe in einer VMM-Cloud befinden. | 
**Virtuelle Computer** | Mindestens eine VM auf dem Hyper-V-Quellhostserver | Mindestens ein VM auf dem Hyper-V-Hostserver in der VMM-Quellcloud | Mindestens ein VM auf dem Hyper-V-Hostserver in der VMM-Quellcloud | VMs, für die eine Replikation zu Azure ausgeführt wird, müssen die [Voraussetzungen für virtuelle Azure-Computer](site-recovery-best-practices.md/#virtual-machines) erfüllen.
**Azure-Konto** | Sie benötigen ein [Azure](https://azure.microsoft.com/)-Konto und ein Abonnement für den Site Recovery-Dienst. | Sie benötigen ein [Azure](https://azure.microsoft.com/)-Konto und ein Abonnement für den Site Recovery-Dienst. | – | Beginnen Sie mit einer [kostenlosen Testversion](pricing/free-trial/), falls Sie kein Konto haben. Erfahren Sie mehr über die [Preise](pricing/details/site-recovery/) für den Dienst.
**Azure-Speicher** | Sie benötigen ein Abonnement für ein Azure-Speicherkonto, für das die Georeplikation aktiviert wurde. | Sie benötigen ein Abonnement für ein Azure-Speicherkonto, für das die Georeplikation aktiviert wurde. | – | Das Konto muss sich in der gleichen Region befinden wie der Azure Site Recovery-Tresor und dem gleichen Abonnement zugeordnet sein. [Weitere Informationen zu Speicher](../storage/storage-introduction.md)
**Speicherzuordnung** | – | – | Optional können Sie mit der Speicherzuordnung sicherstellen, dass virtuelle Maschinen nach dem Failover optimal mit dem Speicher verbunden sind. Wenn Sie zwischen zwei lokalen VMM-Standorten replizieren, wird der virtuelle Replikatcomputer standardmäßig an dem Ort gespeichert, der auf dem Hyper-V-Hostzielserver angegeben ist. Sie können die Zuordnung zwischen VMM-Speicherklassifizierungen auf VMM-Ausgangs- und -Zielservern konfigurieren. | Zur Verwendung dieses Features müssen Speicherklassifizierungen eingerichtet werden, bevor Sie mit der Bereitstellung beginnen. [Weitere Informationen](site-recovery-storage-mapping.md)
**SAN-Replikation** | – | – | Wenn Sie zwischen zwei lokalen VMM-Standorten eine SAN-Replikation ausführen möchten, können Sie Ihre vorhandene SAN-Umgebung dazu nutzen. | Sie benötigen ein [unterstütztes SAN-Array](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), und der SAN-Speicher muss in VMM ermittelt und klassifiziert werden.<br/><br/>Falls Sie derzeit keine Replikation ausführen, müssen Sie in der VMM-Konsole LUNs erstellen und Speicher zuweisen. Falls Sie bereits replizieren, können Sie diesen Schritt überspringen.
**Netzwerk** | Richten Sie eine Netzwerkzuordnung ein, um sicherzustellen, dass alle Maschinen, die im gleichen Azure-Netzwerk ein Failover ausführen, eine Verbindung miteinander herstellen, und zwar unabhängig vom Wiederherstellungsplan, zu dem sie gehören. Wenn im Azure-Zielnetzwerk ein Netzwerkgateway konfiguriert wurde, können die virtuellen Maschinen auch eine Verbindung mit anderen lokalen virtuellen Maschinen herstellen. Wenn Sie keine Netzwerkzuordnung einrichten, können nur Computer Verbindungen miteinander herstellen, für die das Failover in demselben Wiederherstellungsplan festgelegt ist. | – | <br/><br/>Richten Sie eine Netzwerkzuordnung ein, um sicherzustellen, dass virtuelle Maschinen nach einem Failover eine Verbindung mit den entsprechenden Netzwerken herstellen können und replizierte virtuelle Maschinen optimal auf Hyper-V-Hostservern platziert werden. Wenn Sie die Netzwerkzuordnung nicht konfigurieren, werden für replizierte Maschinen nach einem Failover keine Verbindungen mit einem VM-Netzwerk hergestellt. | [Erfahren Sie mehr](site-recovery-network-mapping) zur Netzwerkzuordnung. <br/><br/> Zum Einrichten einer Netzwerkzuordnung mit VMM müssen logische Netzwerke und VM-Netzwerke in VMM ordnungsgemäß konfiguriert werden. [Weitere Informationen](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) und [VM-Netzwerke](https://technet.microsoft.com/library/jj721575.aspx). Lesen Sie darüber hinaus die [Netzwerküberlegungen für VMM](site-recovery-network-design.md/#vmm-design).  
**Anbieter und Agents** | Während der Bereitstellung installieren Sie den Azure Site Recovery-Anbieter auf VMM-Servern. Auf Hyper-V-Servern in VMM-Clouds installieren Sie den Azure Recovery Services-Agent. | Während der Bereitstellung installieren Sie den Azure Site Recovery-Anbieter und den Azure Recovery Services-Agent auf dem Hyper-V-Hostserver oder -Cluster.| Während der Bereitstellung installieren Sie den Azure Site Recovery-Anbieter auf VMM-Servern. Auf Hyper-V-Servern in VMM-Clouds installieren Sie den Azure Recovery Services-Agent. | Anbieter und Agents stellen über das Internet eine verschlüsselte HTTPS-Verbindung mit Site Recovery her. Sie müssen weder Firewallausnahmen hinzufügen noch einen speziellen Proxy für die Anbieterverbindung erstellen. Falls Sie jedoch einen benutzerdefinierten Proxy verwenden möchten, müssen seine URLs vor der Bereitstellung für die Firewall zugelassen werden: <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net<br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/>*.store.core.windows.net
**Internetverbindung** | Nur für die VMM-Server ist eine Internetverbindung erforderlich. | Nur für die Hyper-V-Hostserver ist eine Internetverbindung erforderlich. | Nur für VMM-Server ist eine Internetverbindung erforderlich. | Auf virtuellen Maschinen müssen keine Komponenten installiert werden, und sie benötigen keine direkte Internetverbindung.



## Schützen virtueller VMware-Maschinen oder physischer Server

Für den Schutz virtueller VMware-Maschinen oder physischer Windows-/Linux-Server stehen mehrere Bereitstellungsoptionen zur Verfügung. Sie können sie in Azure oder einem sekundären Datencenter replizieren. Bei jeder Bereitstellung sind verschiedene Anforderungen zu beachten.

**Anforderung** | **Replizieren von VMware-VMs/physischen Servern in Azure** | * **Replizieren von VMware-VMs/physischen Servern an einem sekundären Standort**  
---|---|--- 
**Primärer Standort** | **Prozessserver**: ein dedizierter Windows-Server (physisch oder virtuell) | **Prozessserver**: ein dedizierter Windows-Server (physisch oder eine virtuelle VMware-Maschine)<br/><br/>  
**Lokaler sekundärer Standort** | – | **Konfigurationsserver**: ein dedizierter Windows-Server (physisch oder virtuell) <br/><br/> **Masterzielserver**: ein dedizierter Server (physisch oder virtuell). Konfiguration mit Windows zum Schutz von Windows-Maschinen bzw. mit Linux zum Schutz von Linux-Maschinen
**Azure** | **Abonnement**: Sie benötigen ein Abonnement für den Site Recovery-Dienst. Weitere Informationen zu [Preisen](pricing/details/site-recovery/) <br/><br/> **Speicherkonto**: Sie benötigen ein Speicherkonto, für das die Georeplikation aktiviert ist. Das Konto muss sich in der gleichen Region befinden wie der Site Recovery-Tresor und dem gleichen Abonnement zugeordnet sein. [Weitere Informationen](../storage/storage-introduction.md)<br/><br/> **Konfigurationsserver**: Der Konfigurationsserver muss als Azure-VM eingerichtet werden.<br/><br/> **Masterzielserver**: Der Masterzielserver muss als Azure-VM eingerichtet werden. <br/><br/> Konfiguration mit Windows zum Schutz von Windows-Maschinen bzw. mit Linux zum Schutz von Linux-Maschinen<br/><br/> **Virtuelles Azure-Netzwerk**: Sie benötigen ein virtuelles Azure-Netzwerk, in dem der Konfigurationsserver und der Masterzielserver bereitgestellt werden. Es muss in demselben Abonnement und in derselben Region enthalten sein wie der Azure Site Recovery-Tresor. | –  
**Virtuelle Maschinen/physische Server** | Mindestens eine virtuelle VMware-Maschine oder ein physischer Windows-/Linux-Server.<br/><br/>Während der Bereitstellung wird der Mobilitätsdienst auf jeder Maschine installiert.| Mindestens ein VMware-VM oder physischer Windows-/Linux-Server<br/><br/> Während der Bereitstellung wird der vereinheitlichte Agent auf jeder Maschine installiert.




## Anforderungen für virtuelle Azure-Maschinen

Sie können Site Recovery zum Replizieren virtueller Maschinen und physischer Server bereitstellen, auf denen ein von Azure unterstütztes Betriebssystem ausgeführt wird. Dies gilt für die meisten Versionen von Windows und Linux. Sie müssen sicherstellen, dass lokale virtuelle Maschinen, die Sie schützen möchten, den Azure-Anforderungen entsprechen.


**Feature** | **Unterstützung** | **Details**
---|---|---
Betriebssystem des Hyper-V-Hosts | Windows Server 2012 R2 | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Betriebssystem des VMware-Hypervisors | Unterstütztes Betriebssystem | [Details](site-recovery-vmware-to-azure.md/#before-you-start) 
Gastbetriebssystem | Bei der Replikation von Hyper-V zu Azure unterstützt Site Recovery alle Betriebssysteme, die [von Azure unterstützt werden](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Lesen Sie bei der Replikation von VMware und physischen Servern die [Voraussetzungen](site-recovery-vmware-to-azure.md/#before-you-start) für Windows und Linux. | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist. 
Architektur des Gastbetriebssystems | 64 Bit | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des Betriebssystem-Datenträgers | Bis zu 1.023 GB | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Betriebssystem-Datenträgern | 1 | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist. 
Anzahl von Datenträgern für Daten | Maximal 16 (der Höchstwert ist eine Funktion der Größe des erstellten virtuellen Computers: 16 = XL) | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des VHD-Datenträgers | Maximal 1.023 GB | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Netzwerkadapter | Es werden mehrere Adapter unterstützt. |
Statische IP-Adresse | Unterstützt | Wenn für die primäre virtuelle Maschine eine statische IP-Adresse verwendet wird, können Sie die statische IP-Adresse für die virtuelle Maschine angeben, die unter Azure erstellt wird.
iSCSI-Datenträger | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Freigegebene VHD | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Fiber-Channel-Datenträger | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Festplattenformat| VHD <br/><br/> VHDX | Obwohl VHDX in Azure derzeit nicht unterstützt wird, führt Site Recovery automatisch die Konvertierung von VHDX in VHD durch, wenn Sie das Failover auf Azure anstoßen. Wenn Sie das Failover in den lokalen Speicher durchführen, wird für die virtuellen Computer weiterhin das VHDX-Format verwendet.
Name des virtuellen Computers| Zwischen 1 und 63 Zeichen. Ist auf Buchstaben, Zahlen und Bindestriche beschränkt. Muss mit einem Buchstaben oder einer Zahl beginnen und enden. | Aktualisieren Sie den Wert in den Eigenschaften für virtuelle Computer in Site Recovery.
Typ des virtuellen Computers | <p>Generation 1</p> <p>Generation 2 – Windows</p> | Virtuelle Maschine der 2. Generation mit Betriebssystem-Datenträgertyp Basisfestplatte enthält 1 oder 2 Datenvolumes mit dem Datenträgerformat VHDX, das maximal 300 GB unterstützt. Virtuelle Computer der 2. Generation mit Linux werden nicht unterstützt. [Weitere Informationen](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Optimieren der Bereitstellung

Mit den folgenden Tipps können Sie Ihre Bereitstellung optimieren und skalieren.

- **Volumegröße des Betriebssystems**: Wenn Sie einen virtuellen Computer zu Azure replizieren, muss das Volume des Betriebssystems kleiner als 1 TB sein. Falls Sie mehr als ein Volume nutzen, können Sie diese manuell auf einen anderen Datenträger verschieben, bevor Sie mit der Bereitstellung beginnen.
- **Größe des Datenträgers für Daten**: Wenn Sie zu Azure replizieren, können Sie auf einem virtuellen Computer bis zu 32 Datenträger für Daten nutzen, die jeweils maximal 1 TB groß sind. Für einen virtuellen Computer mit ~32 TB können Sie die Replikation und das Failover effektiv durchführen.
- **Beschränkungen des Wiederherstellungsplans**: Für Site Recovery ist eine Skalierung auf Tausende von virtuellen Computern möglich. Wiederherstellungspläne wurden als Modell für Anwendungen entwickelt, für die das Failover zusammen erfolgen soll. Die Anzahl von Computern in einem Wiederherstellungsplan wird daher auf 50 beschränkt.
- **Beschränkungen für Azure-Dienste**: Jedes Azure-Abonnement verfügt über eine Reihe von standardmäßigen Beschränkungen für Kerne, Clouddienste usw. Wir empfehlen Ihnen, ein Testfailover auszuführen, um die Verfügbarkeit von Ressourcen in Ihrem Abonnement zu überprüfen. Sie können diese Beschränkungen über den Azure-Support ändern.
- **Kapazitätsplanung**: Lesen Sie im [Kapazitätsplaner](http://www.microsoft.com/download/details.aspx?id=39057) nach, wenn Sie virtuelle Hyper-V-Maschinen replizieren.
- **Replikationsbandbreite**: Beachten Sie Folgendes, falls die Replikationsbandbreite knapp ist:
	- **ExpressRoute**: Für Site Recovery können Azure ExpressRoute und WAN-Optimierer wie Riverbed verwendet werden. [Erfahren Sie mehr](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) zu ExpressRoute.
	- **Replikationsdatenverkehr**: Site Recovery führt eine intelligente anfängliche Replikation durch, bei der nur Datenblöcke verwendet werden, und nicht die gesamte VHD. Während der laufenden Replikation werden nur Änderungen repliziert.
	- **Netzwerkdatenverkehr**: Sie können Netzwerkdatenverkehr steuern, der für die Replikation verwendet wird, indem Sie [Windows-Dienstqualität](https://technet.microsoft.com/library/hh967468.aspx) mit einer Richtlinie nutzen, die auf der IP-Zieladresse und dem Port basiert. Falls Sie die Replikation zu Azure Site Recovery mit dem Azure Backup-Agent durchführen, können Sie die Drosselung für den Agent konfigurieren. [Weitere Informationen](https://support.microsoft.com/kb/3056159)
- **RTO**: Wenn Sie die Recovery Time Objective (RTO) messen möchten, die mit Site Recovery zu erwarten ist, sollten Sie ein Testfailover ausführen und die Site Recovery-Aufträge anzeigen, um zu analysieren, wie lange die Durchführung der Vorgänge dauert. Falls Sie das Failover zu Azure nutzen, erzielen Sie den besten RTO-Wert, wenn Sie alle manuellen Aktionen automatisieren. Integrieren Sie hierfür die Azure-Automatisierung und Wiederherstellungspläne.
- **RPO**: Site Recovery unterstützt beim Replizieren zu Azure eine nahezu synchrone Recovery Point Objective (RPO). So wird für eine ausreichende Bandbreite zwischen Ihrem Rechenzentrum und Azure gesorgt.





## Nächste Schritte

Nachdem Sie diese bewährten Methoden überprüft haben, können Sie mit der Bereitstellung von Site Recovery beginnen:

- [Einrichten von Schutz zwischen einem lokalen VMM-Standort und Azure](site-recovery-vmm-to-azure.md)
- [Einrichten von Schutz zwischen einem lokalen Hyper-V-Standort und Azure](site-recovery-hyper-v-site-to-azure.md)
- [Einrichten von Schutz zwischen zwei lokalen VMM-Standorten](site-recovery-vmm-to-vmm.md)
- [Einrichten von Schutz zwischen lokalen VMM-Standorten mit einem SAN](site-recovery-vmm-san.md)
- [Einrichten von Schutz mit einem einzelnen VMM-Server](site-recovery-single-vmm.md)
 

<!----HONumber=AcomDC_0128_2016-->