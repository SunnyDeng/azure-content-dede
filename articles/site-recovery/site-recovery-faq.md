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
	ms.date="02/14/2016"
	ms.author="raynew"/>


# Häufig gestellte Fragen (FAQ) zu Azure Site Recovery
## Informationen zum Artikel

Dieser Artikel enthält häufig gestellte Fragen zu Site Recovery. Sollten Sie nach der Lektüre dieser FAQ noch Fragen haben, stellen Sie diese bitte im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Support

###Welche Funktion hat Site Recovery?

Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem die Replikation von lokalen virtuellen Computern und physischen Servern in Azure oder ein sekundäres Datencenter aufeinander abgestimmt werden. [Weitere Informationen](site-recovery-overview.md).


### Was kann mit Site Recovery geschützt werden?

- **Virtuelle Hyper-V-Computer**: Mit Site Recovery kann jede Workload, die auf einer Hyper-V-VM ausgeführt wird, geschützt werden. 
- **Physische Server**: Mit Site Recovery können physische Server unter Windows oder Linux geschützt werden.
- **Virtuelle VMware-Computer**: Mit Site Recovery kann jede Workload, die auf einer VMware-VM ausgeführt wird, geschützt werden.


###Welche Hyper-V-VMs können werden geschützt?

Dies hängt vom Bereitstellungsszenario ab.

Überprüfen Sie die Voraussetzungen für Hyper-V-Hostserver unter:

- [Replizieren von Hyper-V-VMs (ohne VMM) in Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replizieren von Hyper-V-VMs (mit VMM) in Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Replizieren von Hyper-V-VMs in ein sekundäres Datencenter](site-recovery-vmm-to-vmm.md#before-you-start)

Informationen zu Gastbetriebssystemen:

- Wenn Sie in ein sekundäres Datencenter replizieren, finden Sie eine Liste der unterstützten Gastbetriebssysteme für VMs, die auf Hyper-V-Hostservern ausgeführt werden, unter [Unterstützte Gastbetriebssysteme für Hyper-V-VMs](https://technet.microsoft.com/library/mt126277.aspx).
- Wenn Sie in Azure replizieren, unterstützt Site Recovery alle Gastbetriebssysteme, die [von Azure unterstützt werden](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

Site Recovery kann alle Workloads schützen, die auf einem unterstützten virtuellen Computer ausgeführt werden.

### Kann ich virtuelle Computer schützen, wenn Hyper-V auf einem Clientbetriebssystem ausgeführt wird?

Nein, dies wird nicht unterstützt. Um dieses Problem zu umgehen, müssen Sie den Computer als physische Maschine [in Azure](site-recovery-vmware-to-azure-classic.md) oder einem [sekundären Datencenter](site-recovery-vmware-to-vmware.md) replizieren.


### Welche Workloads können mit Site Recovery geschützt werden?

Mit Site Recovery können die meisten Workloads geschützt werden, die auf einem virtuellen Computer oder physischen Server ausgeführt werden. Mit Site Recovery können Sie anwendungsorientierte Notfallwiederherstellung bereitstellen. Eine Integration in Microsoft-Anwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory ist möglich. Zudem kann Site Recovery eng in die Produkte führender Anbieter, einschließlich Oracle, SAP, IBM und Red Hat, eingebunden werden. Sie können Ihre Lösung für die Notfallwiederherstellung für jede einzelne Anwendung anpassen. [Erfahren Sie mehr](site-recovery-workload.md) über den Schutz von Workloads.


### Benötige ich immer einen System Center VMM-Server, um virtuelle Hyper-V-Computer zu schützen? 

Nein. Neben virtuellen Hyper-V-VMs in VMM-Clouds können Sie auch Hyper-V-VMs in einer Umgebung replizieren, in der VMM nicht bereitgestellt wird. [Weitere Informationen](site-recovery-hyper-v-site-to-azure.md). Hinweis: Wenn Sie in ein sekundäres Datencenter replizieren möchten, müssen Hyper-V-Hostserver in VMM-Clouds verwaltet werden.

### Kann ich Site Recovery mit VMM bereitstellen, wenn ich nur über einen VMM-Server verfüge? 

Ja. Sie können Hyper-V-VMs in der Cloud auf dem VMM-Server in Azure replizieren, oder Sie können zwischen VMM-Clouds auf dem gleichen Server replizieren. Beachten Sie, dass wir empfehlen, dass Sie für die Replikation lokaler Ressourcen in lokalen Ressourcen über einen VMM-Server an den primären und sekundären Standorten verfügen. [Weitere Informationen](site-recovery-single-vmm.md)

### Welche physischen Server können geschützt werden?

Sie können physische Server unter Windows und Linux in Azure oder einen sekundären Standort schützen. [Informationen](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) zu Betriebssystemanforderugen: Unabhängig davon, ob Sie physische Server in Azure oder einen sekundären Standort replizieren, gelten die gleichen Einschränkungen.

Beachten Sie, dass physische Server als VMs in Azure ausgeführt werden, wenn der lokale Server ausfällt. Das Failback zu einem lokalen physischen Server wird derzeit nicht unterstützt. Sie können nur ein Failback zu einer virtuellen Maschine unter VMware ausführen.

### Welche VMware-VMs können geschützt werden?

Für dieses Szenario benötigen Sie einen VMware vCenter-Server, einen vSphere-Hypervisor und virtuelle Maschinen mit aktiven VMware-Tools. Hier finden Sie [Informationen](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) zu den genauen Anforderungen. Unabhängig davon, ob Sie physische Server in Azure oder einen sekundären Standort replizieren, gelten die gleichen Einschränkungen.

### Gelten Voraussetzungen für die Replikation von virtuellen Computern in Azure?

Virtuelle Computer, die Sie in Azure replizieren möchten, sollten den [Azure-Anforderungen](site-recovery-best-practices.md#azure-virtual-machine-requirements) entsprechen.

### Kann ich virtuelle Hyper-V-Computer der 2. Generation in Azure replizieren?

Ja. Während des Failovers konvertiert Site Recovery Computer der 2. Generation in die 1. Generation. Beim Failback werden die Computer wieder in die 2. Generation zurückkonvertiert. [Weitere Informationen](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)

### Wenn ich in Azure repliziere, wie bezahle ich für Azure-VMs? 

Während der regulären Replikation werden Daten in den georedundanten Azure-Speicher repliziert. Dabei fallen keinerlei Gebühren für virtuelle Azure-IaaS-Computer an. Dies ist ein großer Vorteil. Bei einem Failover zu Azure erstellt Site Recovery automatisch virtuelle Azure-IaaS-Computer, und Ihnen werden die in Azure genutzten Computeressourcen in Rechnung gestellt.

### Kann ich mit Site Recovery die Notfallwiederherstellung für meine Zweigstellen verwalten?

Ja. Wenn Sie Site Recovery zum Orchestrieren von Replikation und Failover in Zweigstellen verwenden, erhalten Sie eine einheitliche Orchestrierung und Anzeige der Workloads aller Zweigstellen an einer zentralen Stelle. Sie können problemlos über Ihren Hauptsitz für alle Zweigstellen Failover ausführen und die Notfallwiederherstellung verwalten, ohne die Zweigstellen zu besuchen.

### Gibt es ein SDK, mit dem ich den Site Recovery-Workflow automatisieren kann?

Ja. Sie können Site Recovery-Workflows mithilfe der REST-API, PowerShell oder Azure SDK automatisieren. Erfahren Sie mehr über das [Bereitstellen von Site Recovery mit PowerShell und Azure-Ressourcen-Manager](site-recovery-deploy-with-powershell-resource-manager.md).


## Sicherheit und Compliance

### Werden meine Daten an den Site Recovery-Dienst gesendet?

Nein, Site Recovery fängt Ihre Anwendungsdaten nicht ab und hat keine Informationen darüber, was auf Ihren virtuellen Computern oder physischen Servern ausgeführt wird.

Replikationsdaten werden zwischen Hyper-V-Hosts, VMware-Hypervisoren oder physischen Servern in Ihren primären und sekundären Datencentern oder zwischen Ihrem Datencenter und Ihrem Azure-Speicher ausgetauscht. Site Recovery hat keine Möglichkeit, diese Daten abzufangen. Nur die Metadaten, die zum Orchestrieren von Replikation und Failover erforderlich sind, werden an den Site Recovery-Dienst gesendet.

Site Recovery ist nach ISO 27001:2005 zertifiziert und durchläuft gerade die Prüfungen für HIPAA, DPA und FedRAMP JAB.

### Aus Gründen der Compliance müssen sogar die Metadaten aus unseren lokalen Umgebungen innerhalb der geografischen Region bleiben. Kann Site Recovery uns helfen?

Ja. Durch die Erstellung eines Site Recovery-Tresors in einer Region wird sichergestellt, dass alle Metadaten, die wir zum Ermöglichen und Orchestrieren von Replikation und Failover benötigen, innerhalb der geografischen Grenzen dieser Region bleiben.

### Verschlüsselt Site Recovery die Replikation?
Beim Replizieren von virtuellen Computern und physischen Servern zwischen lokalen Standorten wird Verschlüsselung während der Übertragung unterstützt. Beim Replizieren von virtuellen Computern und physischen Servern zwischen lokalen Standorten und Azure wird Verschlüsselung während der Übertragung und in Azure unterstützt.




## Replikation und Failover

### Wenn ich in Azure repliziere, welche Art von Speicherkonto benötige ich?

Sie benötigen ein Speicherkonto mit [georedundantem Standardspeicher](../storage/storage-introduction.md#replication-for-durability-and-high-availability). Storage Premium wird derzeit nicht unterstützt.

### Wie oft kann ich Daten replizieren?
- **Hyper-V:** Hyper-V-VMs, die unter Windows Server 2012 R2 ausgeführt werden, können alle 30 Sekunden, 5 Minuten oder 15 Minuten repliziert werden. Wenn Sie SAN-Replikation eingerichtet haben, erfolgt die Replikation synchron.
- **VMware- und physische Server:** Hier ist eine Replikationshäufigkeit irrelevant. Die Replikation erfolgt fortlaufend. 

### Kann die Replikation von vorhandenen Wiederherstellungsstandorten auf einen weiteren tertiären Standort erweitert werden?
Eine erweiterte oder verkettete Replikation wird nicht unterstützt. Senden Sie Feedback zu dieser Funktion über das [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/).


### Kann ich eine Offlinereplikation durchführen, wenn ich zum ersten Mal in Azure repliziere? 

Dies wird nicht unterstützt. Senden Sie uns Feedback zu dieser Funktion über das [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/).


### Können bestimmte Datenträger von der Replikation ausgeschlossen werden?

Dies wird nicht unterstützt. Senden Sie uns Feedback zu dieser Funktion über das [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/).

### Können virtuelle Computer mit dynamischen Datenträgern repliziert werden?

Dynamische Datenträger werden unterstützt, wenn virtuelle Hyper-V-Computer repliziert werden. Sie werden nicht unterstützt, wenn virtuelle VMware-Computer oder physische Server repliziert. Senden Sie uns Feedback zu dieser Funktion über das [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/).

### Wie greife ich nach einem Failover auf Azure auf die virtuellen Azure-Computer zu? 

Sie können auf die Azure-VMs über eine sichere Internetverbindung oder über ein Standort-zu-Standort-VPN (oder Azure ExpressRoute) zugreifen, falls vorhanden. Die Kommunikation über eine VPN-Verbindung erfolgt über interne Ports in dem Azure-Netzwerk, in dem sich die VM befindet. Die Kommunikation über das Internet wird den öffentlichen Endpunkten im Azure-Clouddienst für VMs zugeordnet.

### Wie wird bei einem Failover auf Azure in Azure sichergestellt, dass die Daten stabil sind?

Azure ist auf Dienstresilienz ausgelegt. Site Recovery ist bereits für ein Failover auf ein sekundäres Azure-Datencenter (gemäß Azure-SLA) konzipiert, falls sich die Notwendigkeit ergibt. Wenn dies der Fall ist, stellen wir sicher, dass Ihre Metadaten und Tresore innerhalb der gleichen geografischen Region bleiben, die Sie für Ihren Tresor ausgewählt haben.

### Wenn ich eine Replikation zwischen zwei Datencentern ausführe, was geschieht, wenn in meinem primären Datencenter ein unerwarteter Ausfall auftritt?

Sie können ein ungeplantes Failover vom sekundären Standort auslösen. Site Recovery benötigt keine Verbindung mit dem primären Standort, um das Failover auszuführen.


### Erfolgt ein Failover automatisch?

Ein Failover erfolgt nicht automatisch. Sie initiieren Failover mit einem Mausklick im Portal, oder Sie können die [Site Recovery-PowerShell-Cmdlets](https://msdn.microsoft.com/library/dn850420.aspx) verwenden, um ein Failover auslösen. Ein Failback ist auch eine einfache Aktion im Site Recovery-Portal. Zum Automatisieren könnten Sie den lokalen Orchestrator oder Operations Manager verwenden, um einen Fehler bei virtuellen Computern zu erkennen und dann das Failover mithilfe des SDK auszulösen.

### Kann ich die für den Replikationsdatenverkehr zugewiesene Bandbreite drosseln, wenn ich Hyper-V-VMs repliziere?
- Bei der Replikation zwischen Hyper-V-VMs an zwei lokalen Standorten können Sie Windows-QoS dafür verwenden. Hier sehen Sie ein Beispielskript: 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Wenn Sie Hyper-V-VMs in Azure replizieren, können Sie mit dem folgenden Beispiel-PowerShell-Cmdlet eine Drosselung konfigurieren:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)



## Dienstanbieterbereitstellung

### Ist Site Recovery für dedizierte oder gemeinsam genutzte Infrastrukturmodelle geeignet?
Ja, Site Recovery unterstützt dedizierte und gemeinsam genutzte Infrastrukturmodelle.

### Wird die Identität meines Mandanten an den Site Recovery-Dienst weitergegeben?
Nein. Ihre Mandanten müssen überhaupt nicht auf das Site Recovery-Portal zugreifen. Das Portal wird nur vom Administrator des Dienstanbieters verwendet.


### Werden die Anwendungsdaten meiner Mandanten jemals an Azure übermittelt?
Bei der Replikation zwischen den Standorten von Dienstanbietern werden die Anwendungsdaten niemals an Azure übermittelt. Die Daten werden während der Übertragung verschlüsselt und direkt zwischen den Standorten des Dienstanbieters repliziert.

Wenn Sie in Azure replizieren, werden Anwendungsdaten an den Azure-Speicher gesendet, jedoch nicht an den Site Recovery-Dienst. Daten werden während der Übertragung verschlüsselt und bleiben in Azure verschlüsselt.

### Erhalten meine Mandanten eine Rechnung über Azure-Dienste?

Nein. Azure rechnet direkt mit dem Dienstanbieter ab. Mandantenspezifische Rechnungen müssen von den Dienstanbietern selbst erstellt werden.

### Wenn ich in Azure repliziere, müssen wir dann ständig virtuelle Computer in Azure ausführen?

Nein, die Daten werden in ein georedundantes Azure-Speicherkonto in Ihrem Abonnement repliziert. Bei einem Testfailover (Notfallwiederherstellungsübung) oder einem tatsächlichen Failover erstellt Site Recovery automatisch virtuelle Computer in Ihrem Abonnement.

### Stellen Sie die Isolierung auf Mandantenebene sicher, wenn ich in Azure repliziere?

Ja.

### Welche Plattformen werden derzeit unterstützt?

Wir unterstützen Bereitstellungen auf der Grundlage von Azure Pack, Cloud Platform System und System Center (2012 und höher). Weitere Informationen zur Integration von Azure Pack und Site Recovery finden Sie unter [Konfigurieren des Schutzes für virtuelle Computer](https://technet.microsoft.com/library/dn850370.aspx).

### Werden einzelne Bereitstellungen von Azure Pack und VMM-Servern unterstützt?

Ja, Sie können virtuelle Hyper-V-Computer in Azure oder zwischen Dienstanbieterstandorten replizieren. Beachten Sie, dass für die Replikation zwischen Dienstanbieterstandorten die Azure-Runbookintegration nicht verfügbar ist.


## Nächste Schritte

- Lesen Sie die [Site Recovery-Übersicht](site-recovery-overview.md).
- Erfahren Sie mehr über die [Site Recovery-Architektur](site-recovery-components.md).  

<!---HONumber=AcomDC_0309_2016-->