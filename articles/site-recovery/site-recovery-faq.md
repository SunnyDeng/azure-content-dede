<properties 
	pageTitle="Häufig gestellte Fragen (FAQ) zu Site Recovery | Microsoft Azure" 
	description="Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Site Recovery."
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="03/27/2016"
	ms.author="raynew"/>


# Häufig gestellte Fragen (FAQ) zu Azure Site Recovery

Lesen Sie diesen Artikel, um sich über die häufig gestellten Fragen zum Azure Site Recovery-Dienst zu informieren.

Falls Sie nach der Lektüre dieses Artikels weitere Fragen haben, können Sie diese am Ende des Artikels oder im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) posten.

## Allgemein

### Welche Funktion hat Site Recovery?

Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem die Replikation von lokalen virtuellen Computern und physischen Servern in Azure oder ein sekundäres Datencenter aufeinander abgestimmt werden. [Weitere Informationen](site-recovery-overview.md).

### Was kann mit Site Recovery repliziert werden?

- **Virtuelle Hyper-V-Computer**: Mit Site Recovery kann jede Workload, die auf einer unterstützten Hyper-V-VM ausgeführt wird, repliziert werden. 
- **Physische Server**: Mit Site Recovery kann jede Workload repliziert werden, die auf einem unterstützten physischen Windows- oder Linux-Server ausgeführt wird.
- **Virtuelle VMware-Computer**: Mit Site Recovery kann jede Workload, die auf einer unterstützten VMware-VM ausgeführt wird, repliziert werden.


### Was benötige ich in Hyper-V?

Was Sie für Hyper-V-Hostserver benötigen, richtet sich nach dem Bereitstellungsszenario. Sehen Sie sich die Voraussetzungen für Hyper-V an:

- [Replizieren von Hyper-V-VMs (ohne VMM) in Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replizieren von Hyper-V-VMs (mit VMM) in Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Replizieren von Hyper-V-VMs in ein sekundäres Datencenter](site-recovery-vmm-to-vmm.md#before-you-start)

Für Gastsysteme, die auf dem Hyper-V-Hostserver ausgeführt werden:

- Wenn Sie die Replikation an einem sekundären Standort durchführen, helfen Ihnen die Informationen unter [Unterstützte Gastbetriebssysteme für Hyper-V-VMs](https://technet.microsoft.com/library/mt126277.aspx) weiter.
- Wenn Sie in Azure replizieren, unterstützt Site Recovery dieselben Gastbetriebssysteme, die [von Azure unterstützt werden](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).


### Kann ich virtuelle Computer schützen, wenn Hyper-V auf einem Clientbetriebssystem ausgeführt wird?

Nein. VMs müssen sich auf einem Hyper-V-Hostserver befinden, der auf einem unterstützten Windows-Servercomputer ausgeführt wird. Wenn Sie einen Clientcomputer schützen müssen, können Sie diesen als physischen Computer [in Azure](site-recovery-vmware-to-azure-classic.md) oder einem [sekundären Rechenzentrum](site-recovery-vmware-to-vmware.md) replizieren.


### Welche Workloads können mit Site Recovery geschützt werden?

Site Recovery kann alle Workloads replizieren, die auf einer unterstützten VM oder einem physischen Server ausgeführt werden. Site Recovery bietet Unterstützung für die anwendungsorientierte Replikation, sodass für Apps ein „intelligenter Zustand“ wiederhergestellt werden kann. Eine Integration in Microsoft-Anwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory ist möglich. Zudem kann Site Recovery eng in die Produkte führender Anbieter, z.B. Oracle, SAP, IBM und Red Hat, eingebunden werden. [Erfahren Sie mehr](site-recovery-workload.md) über den Schutz von Workloads.


### Müssen sich Hyper-V-Hosts für die Replikation mit Site Recovery in System Center VMM-Clouds befinden? 

Wenn Sie die Replikation zu einem sekundären Rechenzentrum durchführen möchten, müssen Hyper-V-VMs auf Hyper-V-Hostservern in einer VMM-Cloud angeordnet sein. Falls Sie zu Azure replizieren möchten, können Sie VMs mit oder ohne VMM-Clouds auf Hyper-V-Hostservern replizieren. [Weitere Informationen](site-recovery-hyper-v-site-to-azure.md).

### Kann ich Site Recovery mit VMM bereitstellen, wenn ich nur über einen VMM-Server verfüge? 

Ja. Sie können VMs auf Hyper-V-Servern in der VMM-Cloud in Azure replizieren, oder Sie können zwischen VMM-Clouds auf demselben Server replizieren. Für die Replikation „lokal zu lokal“ empfehlen wir Ihnen, sowohl am primären Standort als auch am sekundären Standort jeweils einen VMM-Server zu verwenden. [Weitere Informationen](site-recovery-single-vmm.md)

### Welche physischen Server können geschützt werden?

Sie können physische Server mit unterstützten Windows- und Linux-Betriebssystemen in Azure oder an einem sekundären Standort schützen. [Erfahren Sie mehr](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) zu den Betriebssystemanforderungen für die Replikation in Azure oder an einem sekundären Standort. Beachten Sie, dass physische Server, die in den Azure-Speicher repliziert werden, bei einem Failover als VMs in Azure ausgeführt werden. Beim Durchführen eines Failbacks von Azure zu Ihrem lokalen Standort wird der Failbackvorgang auf einen physischen Server derzeit nicht unterstützt. Sie können nur ein Failback zu einer virtuellen Maschine unter VMware ausführen.

### Welche VMware-VMs können geschützt werden?

Zum Schützen von VMware-VMs benötigen Sie einen vSphere-Hypervisor und virtuelle Computer, auf denen VMware-Tools ausgeführt werden. Außerdem wird empfohlen, einen VMware vCenter-Server zum Verwalten der Hypervisoren einzusetzen. [Erfahren Sie mehr](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) zu den genauen Anforderungen für die Replikation von VMware-Servern und VMs in Azure oder an einem sekundären Standort.

### Kann ich mit Site Recovery die Notfallwiederherstellung für meine Zweigstellen verwalten?

Ja. Wenn Sie Site Recovery zum Orchestrieren von Replikation und Failover in Zweigstellen verwenden, können Sie alle Workloads der Zweigstellen an einem zentralen Ort orchestrieren und anzeigen. Sie können problemlos über Ihren Hauptsitz für alle Zweigstellen Failover ausführen und die Notfallwiederherstellung verwalten, ohne die Zweigstellen zu besuchen.

## Sicherheit


### Werden Replikationsdaten an den Site Recovery-Dienst gesendet?

Nein. Site Recovery fängt replizierte Daten nicht ab und besitzt keine Informationen dazu, was auf Ihren virtuellen Computern oder physischen Servern ausgeführt wird.

Replikationsdaten werden zwischen lokalen Hyper-V-Hosts, VMware-Hypervisoren oder physischen Servern und Azure-Speicher an Ihrem sekundären Standort ausgetauscht. Site Recovery hat keine Möglichkeit, diese Daten abzufangen. Nur die Metadaten, die zum Orchestrieren von Replikation und Failover erforderlich sind, werden an den Site Recovery-Dienst gesendet.

Site Recovery ist nach ISO 27001:2005 zertifiziert und durchläuft gerade die Prüfungen für HIPAA, DPA und FedRAMP JAB.

### Aus Gründen der Compliance müssen sogar die Metadaten aus unseren lokalen Umgebungen innerhalb derselben geografischen Region bleiben. Ist dies mit Site Recovery möglich?

Ja. Durch die Erstellung eines Site Recovery-Tresors in einer Region wird sichergestellt, dass alle Metadaten, die wir zum Ermöglichen und Orchestrieren von Replikation und Failover benötigen, innerhalb der geografischen Grenzen dieser Region bleiben.

### Verschlüsselt Site Recovery die Replikation?
Für virtuelle Computer und physische Server wird bei der Replikation zwischen lokalen Standorten die Verschlüsselung während der Übertragung unterstützt. Für virtuelle Computer und physische Server wird bei der Replikation zu Azure sowohl die Verschlüsselung während der Übertragung als auch die Verschlüsselung ruhender Daten (in Azure) unterstützt.



## Replikation

### Gelten Voraussetzungen für die Replikation von virtuellen Computern in Azure?

Virtuelle Computer, die Sie in Azure replizieren möchten, sollten den [Azure-Anforderungen](site-recovery-best-practices.md#azure-virtual-machine-requirements) entsprechen.

### Kann ich virtuelle Hyper-V-Computer der 2. Generation in Azure replizieren?

Ja. Während des Failovers konvertiert Site Recovery VMs der 2. Generation in die 1. Generation. Beim Failback wird die VM wieder zurück in die 2. Generation konvertiert. [Weitere Informationen](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)

### Wenn ich in Azure repliziere, wie bezahle ich für Azure-VMs? 

Während der regulären Replikation werden Daten in den georedundanten Azure-Speicher repliziert. Dabei fallen keinerlei Gebühren für virtuelle Azure-IaaS-Computer an. Bei einem Failover zu Azure erstellt Site Recovery automatisch virtuelle Azure-IaaS-Computer, und Ihnen werden die in Azure genutzten Computeressourcen in Rechnung gestellt.

### Gibt es ein SDK, mit dem ich Site Recovery-Workflows automatisieren kann?

Ja. Sie können Site Recovery-Workflows mithilfe der REST-API, PowerShell oder Azure SDK automatisieren. Erfahren Sie mehr über das [Bereitstellen von Site Recovery mit PowerShell und Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).

### Wenn ich in Azure repliziere, welche Art von Speicherkonto benötige ich?

Sie benötigen ein Speicherkonto mit [georedundantem Standardspeicher](../storage/storage-introduction.md#replication-for-durability-and-high-availability). Storage Premium wird derzeit nicht unterstützt.

### Wie oft kann ich Daten replizieren?

- **Hyper-V:** Hyper-V-VMs, die unter Windows Server 2012 R2 ausgeführt werden, können alle 30 Sekunden, 5 Minuten oder 15 Minuten repliziert werden. Wenn Sie die SAN-Replikation eingerichtet haben, erfolgt die Replikation synchron.
- **VMware- und physische Server:** Hier ist eine Replikationshäufigkeit irrelevant. Die Replikation erfolgt fortlaufend. 

### Kann die Replikation von einem vorhandenen Wiederherstellungsstandort auf einen anderen Wiederherstellungsstandort erweitert werden?

Eine erweiterte oder verkettete Replikation wird nicht unterstützt. [Senden Sie uns Feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/) zu diesem Feature.


### Kann ich eine Offlinereplikation durchführen, wenn ich zum ersten Mal in Azure repliziere? 

Dies wird nicht unterstützt. [Senden Sie uns Feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/) zu diesem Feature.

### Können bestimmte Datenträger von der Replikation ausgeschlossen werden?

Dies wird nicht unterstützt. [Senden Sie uns Feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/) zu diesem Feature.

### Können virtuelle Computer mit dynamischen Datenträgern repliziert werden?

Dynamische Datenträger werden unterstützt, wenn virtuelle Hyper-V-Computer repliziert werden. Diese werden auch beim Replizieren von VMware-VMs und physischen Computern unterstützt, wenn Sie die [erweiterte Bereitstellung](site-recovery-vmware-to-azure-classic.md) verwenden. Beachten Sie, dass es sich beim Betriebssystem-Datenträger um einen Basisdatenträger handeln muss.

### Kann ich die Bandbreite drosseln, die für den Hyper-V-Replikationsdatenverkehr zugeordnet ist?
- Bei der Replikation zwischen Hyper-V-VMs an zwei lokalen Standorten können Sie Windows-QoS dafür verwenden. Hier sehen Sie ein Beispielskript: 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Wenn Sie Hyper-V-VMs in Azure replizieren, können Sie mit dem folgenden Beispiel-PowerShell-Cmdlet eine Drosselung konfigurieren:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- [Erfahren Sie mehr](https://support.microsoft.com/de-DE/kb/3056159) über die Drosselung von Hyper-V-Datenverkehr.
- [Erfahren Sie mehr](site-recovery-vmware-to-azure-classic.md#capacity-planning) über die Drosselung der VMware-Replikation in Azure.


## Failover

### Wie greife ich nach einem Failover zu Azure auf virtuelle Azure-Computer zu? 

Sie können auf die Azure-VMs über eine sichere Internetverbindung, über eine Site-to-Site-VPN-Verbindung oder über Azure ExpressRoute zugreifen. Die Kommunikation über eine VPN-Verbindung erfolgt über interne Ports in dem Azure-Netzwerk, in dem sich die VM befindet. Die Kommunikation über das Internet wird den öffentlichen Endpunkten im Azure-Clouddienst für die VMs zugeordnet.

### Wie wird bei einem Failover zu Azure in Azure sichergestellt, dass die Daten stabil sind?

Azure ist auf Resilienz ausgelegt. Site Recovery ist bereits für ein Failover auf ein sekundäres Azure-Datencenter (gemäß Azure-SLA) konzipiert, falls sich die Notwendigkeit ergibt. Wenn dies der Fall ist, stellen wir sicher, dass Ihre Metadaten und Tresore innerhalb der gleichen geografischen Region bleiben, die Sie für Ihren Tresor ausgewählt haben.

### Was passiert, wenn ich eine Replikation zwischen zwei Standorten durchführe und mein primäres Rechenzentrum unerwartet ausfällt?

Sie können ein ungeplantes Failover vom sekundären Standort auslösen. Site Recovery benötigt keine Verbindung mit dem primären Standort, um das Failover auszuführen.

### Erfolgt ein Failover automatisch?

Ein Failover erfolgt nicht automatisch. Sie initiieren Failover mit einem einzelnen Klick im Portal, oder Sie können [Site Recovery PowerShell-Cmdlets](https://msdn.microsoft.com/library/dn850420.aspx) verwenden, um ein Failover auszulösen. Ein Failback umfasst ein Reihe von Aktionen im Site Recovery-Portal.

Zum Automatisieren des Failovers können Sie den lokalen Orchestrator oder Operations Manager verwenden, um einen Fehler bei virtuellen Computern zu erkennen und dann das Failover mithilfe des SDK auszulösen.


##
### Ich bin ein Dienstanbieter. Ist Site Recovery für dedizierte oder gemeinsam genutzte Infrastrukturmodelle geeignet?
Ja, Site Recovery unterstützt dedizierte und gemeinsam genutzte Infrastrukturmodelle.

### Für Dienstanbieter wird die Identität meines Mandanten an den Site Recovery-Dienst weitergegeben?
Nein. Ihre Mandanten benötigen keinen Zugriff auf das Site Recovery-Portal. Nur der Administrator des Dienstanbieters interagiert mit dem Portal.


### Werden die Anwendungsdaten meiner Mandanten jemals an Azure übermittelt?

Bei der Replikation zwischen den Standorten von Dienstanbietern werden die Anwendungsdaten niemals an Azure übermittelt. Die Daten werden während der Übertragung verschlüsselt und direkt zwischen den Standorten des Dienstanbieters repliziert.

Wenn Sie in Azure replizieren, werden Anwendungsdaten an den Azure-Speicher gesendet, jedoch nicht an den Site Recovery-Dienst. Daten werden während der Übertragung verschlüsselt und bleiben in Azure verschlüsselt.

### Erhalten meine Mandanten eine Rechnung über Azure-Dienste?

Nein. Azure rechnet direkt mit dem Dienstanbieter ab. Mandantenspezifische Rechnungen müssen von den Dienstanbietern selbst erstellt werden.

### Wenn ich in Azure repliziere, müssen wir dann ständig virtuelle Computer in Azure ausführen?

Nein. Die Daten werden in ein georedundantes Azure-Speicherkonto in Ihrem Abonnement repliziert. Bei einem Testfailover (Notfallwiederherstellungsübung) oder einem tatsächlichen Failover erstellt Site Recovery automatisch virtuelle Computer in Ihrem Abonnement.

### Stellen Sie die Isolierung auf Mandantenebene sicher, wenn ich in Azure repliziere?

Ja.

### Welche Plattformen werden derzeit unterstützt?

Wir unterstützen Bereitstellungen von Azure Pack, Cloud Platform System und System Center (2012 und höher). Weitere Informationen zur Integration von Azure Pack finden Sie unter [Konfigurieren des Schutzes für virtuelle Computer](https://technet.microsoft.com/library/dn850370.aspx).

### Werden einzelne Bereitstellungen von Azure Pack und VMM-Servern unterstützt?

Ja, Sie können virtuelle Hyper-V-Computer in Azure oder zwischen Dienstanbieterstandorten replizieren. Beachten Sie, dass für die Replikation zwischen Dienstanbieterstandorten die Azure-Runbookintegration nicht verfügbar ist.


## Nächste Schritte

- Lesen Sie die [Site Recovery-Übersicht](site-recovery-overview.md).
- Erfahren Sie mehr über die [Site Recovery-Architektur](site-recovery-components.md).  

<!---HONumber=AcomDC_0330_2016-->