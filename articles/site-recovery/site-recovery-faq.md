<properties 
	pageTitle="Azure Site Recovery: häufig gestellte Fragen" 
	description="Dieser Artikel enthält häufig gestellte Fragen zur Verwendung von Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="08/26/2015" 
	ms.author="lauraa"/>


# Azure Site Recovery: häufig gestellte Fragen
## Informationen zum Artikel

Dieser Artikel enthält häufig gestellte Fragen zur Azure Site Recovery. Eine Einführung in Site Recovery und entsprechende Bereitstellungsszenarien finden Sie in der [Übersicht über Site Recovery](site-recovery-overview.md).

Sollten Sie nach der Lektüre dieses Artikels noch weitere Fragen haben, können Sie diese im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) stellen.


## Allgemein
### Ist ASR sicher? Welche Daten werden an Azure gesendet?

Ja, ASR ist sicher. ASR fängt keine Anwendungsdaten ab und hat auch keinerlei Informationen darüber, was auf Ihren virtuellen Computern ausgeführt wird. Weder die Hyper-V-Hosts noch die virtuellen Computer müssen mit dem Internet verbunden sein.

Da die Replikation zwischen Ihrem Unternehmen und dem Dienstanbieter stattfindet, werden keine Anwendungsdaten an Azure gesendet. An Azure werden nur Metadaten (etwa die Namen von virtuellen Computern oder Cloudnamen) gesendet, die für die Orchestrierung des Failovers erforderlich sind. ASR hat keine Möglichkeit, Ihre Anwendungsdaten abzufangen, und diese Daten bleiben immer lokal.

ASR ist ISO 27001:2005-zertifiziert und durchläuft gerade die Prüfungen für HIPAA, DPA und FedRAMP JAB.

### Wir müssen aus Compliance-Gründen sicherstellen, dass die Metadaten aus unseren lokalen Umgebungen die geografische Region nicht verlassen. Können wir diese Anforderung mit ASR erfüllen?

Ja. Durch die Erstellung eines Site Recovery-Tresors in einer Region Ihrer Wahl wird sichergestellt, dass alle Metadaten, die wir für Ihr Notfallwiederherstellungssetup benötigen, innerhalb der geografischen Grenzen der gewählten Region bleiben.

### Gibt es ein SDK, mit dem ich den ASR-Workflow automatisieren kann?

Ja. ASR-Workflows können mittels REST-API, PowerShell oder Azure SDK automatisiert werden. Ausführlichere Informationen finden Sie im Blogbeitrag [Introducing PowerShell support for Azure Site Recovery](http://azure.microsoft.com/blog/2014/11/05/introducing-powershell-support-for-azure-site-recovery/) (Einführung der PowerShell-Unterstützung für Azure Site Recovery; in englischer Sprache).

### Verschlüsselt ASR die Replikation? 
Die Replikation von lokalen Daten zu Azure und von lokalen Daten vor Ort unterstützt die Verschlüsselung bei der Übertragung für *Szenarios für Hyper-V- und VMM-Schutz*. Beim *Hyper-V- und VMM-Schutz* in Azure wird die Verschlüsselung im Ruhezustand ebenfalls unterstützt. Weitere Informationen finden Sie in [diesem Artikel](https://azure.microsoft.com/blog/2014/09/02/azure-site-recovery-privacy-security-part1/).

### Kann ich für die Repliaktions-/Kopierhäufigkeit einen höheren Wert als 15 Minuten angeben?
* **Hyper-V- und VMM-Szenarios**: Nein, die Replikation virtueller Hyper-V-Computer mithilfe der hostbasierten Replikation kann nur für 30 Sekunden, 5 Minuten und 15 Minuten konfiguriert werden.
* **Szenarios mit VMware-Geräten/physischen Geräten**: Dies gilt nicht für die gastbasierte Replikation, da die Technologie den kontinuierlichen Datenschutz nutzt.

### Können bestimmte Datenträger von der Replikation mithilfe von ASR ausgeschlossen werden?
Dies wird nicht unterstützt. Senden Sie uns Ihr Feedback über das [Azure Site Recovery-Feedbackforum – Ausschließen von Datenträgern aus der Replikation](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication).

### Können auf dynamischen Datenträgern basierende virtuelle Computer repliziert werden?
In *Hyper-V- und VMM-Szenarios* werden dynamische Datenträger unterstützt. In *Szenarios mit virtuellen VMware-Computern oder physischen Computern* werden keine dynamischen Datenträger unterstützt. Senden Sie uns Ihr Feedback über das [Azure Site Recovery-Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery).

### Welche Art von Speicherkonten werden unterstützt?
[Georedundanter Standard-Speicher](../storage/storage-redundancy.md#geo-redundant-storage) wird unterstützt. [Premium-Speicherkonten]((../storage/storage-premium-storage-preview-portal/) werden nur für [Szenarios mit virtuellen VMware-Computern oder physischen Computern](site-recovery-vmware-to-azure.md) unterstützt. Lokal redundanter Standard-Speicher wird noch nicht unterstützt. Senden Sie uns Ihr Feedback über [Support für die Unterstützung lokaler redundanter Speicher](http://feedback.azure.com/forums/256299-site-recovery/suggestions/7204469-local-redundant-type-azure-storage-support).

### Kann die Replikation von vorhandenen Wiederherstellungsstandorten auf tertiäre Standorte erweitert werden?
Dies wird nicht unterstützt. Senden Sie uns Ihr Feedback über das [Azure Site Recovery-Feedbackforum – Support für das Erweitern der Replikation](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### Kann für die ersten Datenträger mithilfe des Offlinemechanismus Seeding zu Azure ausgeführt werden?
Dies wird nicht unterstützt. Senden Sie uns Ihr Feedback über das [Azure Site Recovery-Feedbackforum – Support für die Offlinereplikation](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### Kann ich die für den Replikationsdatenverkehr zugewiesene Bandbreite einschränken, wenn Hyper-V als Quelle verwendet wird?
- Bei lokalen Replikation zwischen zwei lokalen Standorten können Sie Windows-QoS dafür verwenden. Im Folgenden finden Sie ein Beispielskript: 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Wenn Sie die Replikation nach Azure durchführen, können Sie es mithilfe des folgenden PowerShell-Beispiel-Cmdlets konfigurieren:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)


## Versionsunterstützung

### Welche Versionen von Windows Server-Hosts und -Clustern werden unterstützt?
Wenn Sie sich bei der Replikation und dem Schutz zwischen Hyper-V-Standorten für das Hyper-V-Replikat entscheiden, können Sie Windows Server 2012 und Windows Server 2012 R2 verwenden.


### Welche Versionen von Hyper-V-Gastbetriebssystemen werden unterstützt?
Die aktuelle Liste der unterstützten Gastbetriebssysteme finden Sie unter [Grundlegendes zu virtuellen Computern und Gastbetriebssystemen](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### Kann ich den Schutz für virtuelle Computer konfigurieren, wenn Hyper-V unter einem Clientbetriebssystem ausgeführt wird?

Sie können Hyper-V, das unter einem Clientbetriebssystem ausgeführt wird, nicht zum Replizieren von virtuellen Computern in Azure konfigurieren. Hyper-V-Replikat wird nur unter Serverbetriebssystemen unterstützt. Sie können Ihre physischen Clientcomputer entsprechend den Anweisungen in [diesem Artikel](site-recovery-vmware-to-azure.md) in Azure replizieren.

### Unterstützt ASR Computer der 2. Generation?

Ja, ASR unterstützt die Replikation von virtuellen Computern der 2. Generation in Hyper-V in Azure. Während des Failovers konvertiert ASR Computer der 2. Generation in die 1. Generation. Beim Failback werden die Computer wieder in die 2. Generation zurückkonvertiert. [Hier](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) finden Sie weitere Informationen.


## Bereitstellung zwischen Dienstanbieterstandorten: 

### Ist diese Lösung für dedizierte oder gemeinsam genutzte Infrastrukturmodelle geeignet?
Ja. ASR unterstützt sowohl dedizierte als auch gemeinsam genutzte Infrastrukturmodelle.

### Wird die Identität meines Mandanten an Azure weitergegeben?
Nein. Ihre Mandanten müssen überhaupt nicht auf das ASR-Portal zugreifen. Das ASR-Portal in Azure wird nur vom Administrator des Dienstanbieters verwendet.

### Erhalten meine Mandanten eine Rechnung von Azure?
Nein. Microsoft Azure rechnet ausschließlich mit dem Dienstanbieter ab. Mandantenspezifische Rechnungen müssen von den Dienstanbietern selbst erstellt werden.

### Werden die Anwendungsdaten meiner Mandanten jemals an Azure übermittelt?
Wenn Sie für die Notfallwiederherstellung den Standort eines Dienstanbieters verwenden, werden die Anwendungsdaten niemals an Azure übermittelt. Die Daten werden verschlüsselt und direkt zwischen den Standorten des Dienstanbieters repliziert. Wenn Sie Azure als Standort für die Notfallwiederherstellung verwenden, werden die Anwendungsdaten an Azure gesendet. Ruhende und übertragene Daten bleiben allerdings verschlüsselt. Die Verbindung mit Azure kann auch per VPN, ExpressRoute oder über das öffentliche Internet hergestellt werden.

### Wenn wir Azure als Standort für die Notfallwiederherstellung verwenden, müssen wir dann ständig virtuelle Computer in Azure ausführen?
Nein. Bei ASR handelt es sich um eine erstklassige Public Cloud-Servicelösung für die Notfallwiederherstellung. Die Daten werden an ein georedundantes Azure-Speicherkonto in Ihrem eigenen Abonnement repliziert. Bei einer Notfallwiederherstellungsübung oder einem tatsächlichen Failover erstellt ASR automatisch virtuelle Computer in Ihrem Abonnement.

### Wird eine Isolierung auf Mandantenebene gewährleistet, wenn ich Azure als Standort für die Notfallwiederherstellung verwende?
Ja.

### Welche Plattformen werden derzeit unterstützt?
Wir unterstützen Azure Pack, Cloud Platform System und System Center auf der Grundlage von Hyper 2012 und höheren Bereitstellungen. Weitere Informationen zur Integration von ASR und Azure Pack finden Sie unter [Konfigurieren des Schutzes für virtuelle Computer](https://technet.microsoft.com/library/dn850370.aspx).

### Werden auch einzelne Azure Pack- und VMM-Server-Bereitstellungen unterstützt?
Ja. Einzelne SCVMM-Bereitstellungen werden für beide Szenarien (Notfallwiederherstellungen zwischen Dienstanbieterstandorten und Notfallwiederherstellung zwischen einem Dienstanbieterstandort und Azure) unterstützt.

Die einzelne Azure Pack-Bereitstellung wird auch unterstützt, wenn Sie die Notfallwiederherstellung zwischen Dienstanbieterstandorten aktivieren. Für diese Topologie steht jedoch keine ASR-Runbook-Integration zur Verfügung.

## Bereitstellung zwischen Hyper-V-Standorten und Azure (ohne VMM)

### Muss für ASR eine VPN-Verbindung zwischen den Standorten eingerichtet werden?
Nein. ASR kann auch über das öffentliche Internet verwendet werden. Bei Verwendung einer VPN-Verbindung zwischen den Standorten können Sie nach einem Failover jedoch auf die gleiche Weise auf die virtuellen Computer zugreifen wie zuvor. Weitere Informationen zu Netzwerküberlegungen bei der Aktivierung der Notfallwiederherstellung in Azure finden Sie im folgenden Blog: [Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) (Einrichten der Netzwerkinfrastruktur für die Verwendung von Microsoft Azure als Notfallwiederherstellungsstandort; in englischer Sprache).

### Kommen neben der Gebühr für den ASR-Schutz noch andere Kosten auf mich zu?
Im stabilen Zustand replizieren wir Änderungen an das georedundante Azure Storage. Dabei fallen keinerlei Gebühren für virtuelle Azure-IaaS-Computer an. Dies ist ein großer Vorteil. Bei einem Failover zu Azure erstellt ASR automatisch virtuelle Azure-IaaS-Computer, und Ihnen werden die in Azure genutzten Serverressourcen in Rechnung gestellt.

### In einer Zweigstelle ist VMM nicht installiert. Kann ich die Workloads der Zweigstelle mit Azure schützen? Welche zentralen Vorteile bietet mir ASR dabei?
Ja, mit ASR können Sie virtuelle Computer auf Hyper-V-Hosts schützen, die nicht von SCVMM verwaltet werden.

Wenn Sie die Notfallwiederherstellung aller Zweigstellen mithilfe von ASR verwalten, können Sie sich an einem zentralen Ort einen Überblick über alle Zweigstellen-Workloads verschaffen. Außerdem können Sie Failover und Notfallwiederherstellung für alle Zweigstellen schnell und komfortabel in Ihrer Zentrale verwalten. Netzwerkzuordnung und die Verschlüsselung ruhender Daten werden bei der Notfallwiederherstellung für Zweigstellen derzeit nicht unterstützt.

### Kann ich bei Zweigstellen nur eine begrenzte Anzahl von virtuellen Computern schützen?
Nein. Die Unterstützung ist die gleiche wie in unserem Unternehmensszenario.

### Muss ich auf den zu schützenden virtuellen Computern einen Agent installieren?

Auf den virtuellen Computern wird kein Agent benötigt. Sie müssen auf jedem Hyper-V-Server, der über zu schützende virtuelle Computer verfügt, den Site Recovery-Anbieter und den Recovery Services-Agent installieren. Diese beiden Komponenten werden mit einer Anbieterdatei installiert, die während der Bereitstellung von Site Recovery heruntergeladen werden kann.

### Sind für den Anbieter auf dem Hyper-V Server spezielle Firewalleinstellungen erforderlich?

Es ist keine bestimmte Einstellung erforderlich. Die Anbieterkomponenten auf dem Server kommunizieren über eine HTTPS-Verbindung mit Azure und verwenden,sofern nicht anders angegeben, die Standardinterneteinstellung auf dem Hyper-V-Hostserver.

### Muss der Hyper-V-Server Mitglied einer Domäne sein?

Nein, der Server muss sich nicht in einer Domäne befinden.

### Welche Versionen von Windows Server-Hosts und -Clustern werden unterstützt?
Bei Verwendung der ASR- und Hyper-V-Replikation zwischen Hyper-V-Standorten und Azure können Windows Server 2012 und Windows Server 2012 R2 verwendet werden.

### Welche Versionen von Gastbetriebssystemen werden unterstützt?
Die aktuelle Liste der unterstützten Gastbetriebssysteme finden Sie unter [Grundlegendes zu virtuellen Computern und Gastbetriebssystemen](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

## Bereitstellung zwischen zwei VMM-Rechenzentren

### Kann ich einen virtuellen Computer replizieren, der in einen sekundären Standort in Azure repliziert wurde? 

Nein, diese Art von verketteter Replikation wird nicht unterstützt.

### Benötige ich Zertifikate zum Konfigurieren des Schutzes zwischen zwei VMM-Rechenzentren?

Nein. Beim Konfigurieren des Schutzes zwischen VMM-Clouds in ASR geben Sie den Authentifizierungstyp an. Wählen Sie HTTPS, sofern Sie keine funktionierende Kerberos-Umgebung konfiguriert haben. Azure Site Recovery konfiguriert automatisch Zertifikate für die HTTPS-Authentifizierung. Es ist keine manuelle Konfiguration notwendig. Wenn Sie Kerberos auswählen, wird ein Kerberos-Ticket für die gegenseitige Authentifizierung der Hostserver verwendet. Standardmäßig werden die Ports 8083 (für Kerberos) und 8084 (für Zertifikate) in der Windows-Firewall auf den Hyper-V-Hostservern geöffnet. Bitte beachten Sie, dass diese Einstellung nur für Hyper-V-Hostserver relevant ist, die unter Windows Server 2012 R2 laufen.



## Bereitstellung zwischen zwei VMM-Rechenzentren mit SAN

### Was, wenn bereits eine SAN-basierte Replikation eingerichtet ist und ich diese nicht ändern möchte?
Kein Problem: ASR unterstützt Szenarien mit bereits eingerichteter Replikation. In einem solchen Fall können Sie ASR zur Orchestrierung der VM-orientierten Notfallwiederherstellung, für die Konfiguration des Notfallwiederherstellungsnetzwerks, für anwendungsorientierte Wiederherstellungspläne, für Notfallwiederherstellungsübungen sowie für die Erfüllung von Compliance- und Auditanforderungen nutzen.


### Benötige ich VMM? Muss VMM auf beiden Seiten vorhanden sein?
Ja. Das SAN-Array muss der Verwaltung durch VMM unter Verwendung eines Array-spezifischen SMI-S-Anbieters unterstellt werden.

Wir unterstützen zwar auch einzelne VMM-HA-Bereitstellungen auf der Grundlage des Array-Typs, empfohlen wird jedoch, die Standorte mit separaten VMM-Servern zu verwalten.


### Welche Speicherarrays werden unterstützt?

NetApp, EMC und HP bieten Unterstützung für die Azure Site Recovery-SAN-Replikation mit Updates für ihre SMI-S-Anbieter. Weitere Informationen erhalten Sie unter den folgenden Links:

- [NetApp Clustered Data ONTAP 8.2](http://community.netapp.com/t5/Technology/NetApp-Unveils-Support-for-Microsoft-Azure-SAN-Replication-with-SMI-S-and/ba-p/94483)
- [EMC VMAX-Serie](https://thecoreblog.emc.com/high-end-storage/microsoft-azure-site-recovery-now-generally-available-vmax-srdf-integration-pack-ready-for-public-review/)    
- [HP 3PAR](http://h20195.www2.hp.com/V2/GetDocument.aspx?docname=4AA5-7068ENW&cc=us&lc=en)


### Was, wenn ich hinsichtlich meines Speicheradministrators Zweifel habe?
Wir arbeiten mit dem vorhandenen Replikationssetup Ihres Speicheradministrators. Das bedeutet, dass dieser keinerlei Änderungen an seinen Arrays vornehmen muss. Organisationen, die ihre Speicherverwaltung über VMM automatisieren möchten, können Speicher allerdings auch unter Verwendung von ASR und VMM bereitstellen.

### Kann ich eine synchrone Replikation unterstützen? Wie sieht es mit Gastclustern aus? Und was ist mit freigegebenem Speicher?
Alles kein Problem.

### Was muss ich lokal installieren, damit das funktioniert?
Wenn Sie für die Replikation und den Schutz zwischen Hyper-V-Standorten die (SAN-)Array-basierte Replikation verwenden, müssen Sie lediglich den ASR-Notfallwiederherstellungsanbieter auf dem SCVMM-Server installieren. Zur Erinnerung: Hierbei handelt es sich um den *einzigen* Host, der mit dem Internet verbunden sein muss.


Darüber hinaus muss SCVMM Ihre Arrays mithilfe eines aktualisierten SMI-S-Anbieters erkennen, der von Ihren jeweiligen Speicheranbietern bereitgestellt wird.

## Bereitstellung zwischen physischen Servern und Azure

### Kann ich meine physischen lokalen Server in Azure schützen?

Ja, Sie können einen physischen Server mithilfe von ASR replizieren und als virtuellen Computer in Azure ausführen, falls der lokale Server ausfällt. Das Failback zu einem lokalen physischen Server wird derzeit nicht unterstützt. Sie können ein Failback zu einem virtuellen Computer unter Hyper-V oder VMware ausführen.


## Failover

### Was passiert bei einem Ausfall von Azure? Kann ich ein Failover über VMM auslösen?

Azure ist zwar auf Resilienz ausgelegt, wir sind jedoch auf den schlimmsten Fall vorbereitet. ASR ist bereits für ein Failover zu einem sekundären Azure-Datencenter (gemäß Azure-SLA) konzipiert. Aber selbst in diesem Fall ist gewährleistet, dass Ihre Metadaten und Ihr ASR-Tresor innerhalb der geografischen Region bleiben, die Sie für Ihren Tresor ausgewählt haben.


### Was passiert, wenn es aufgrund eines Notfalls bei meinem Internetdienstanbieter für das primäre Datencenter ebenfalls zu einem Ausfall kommt? Was, wenn es auch bei meinem Internetdienstanbieter für das sekundäre Datencenter zu einem Ausfall kommt?

Über das ASR-Portal können Sie mit nur einem Klick ein nicht geplantes Failover durchführen. Für das Failover ist keine Verbindung mit dem primären Datencenter erforderlich.

Anwendungen, die nach einem Failover im sekundären Datencenter ausgeführt werden müssen, benötigen wahrscheinlich eine Internetverbindung. ASR geht davon aus, dass der SCVMM-Server des sekundären Standorts trotz einer möglichen notfallbedingten Beeinträchtigung des primären Standorts und Internetdienstanbieters weiterhin mit ASR verbunden ist.

### Unterstützt ASR Failbacks?
Ja. Ein Failback von Azure zu Ihrem lokalen Standort kann problemlos mit nur einem Klick im ASR-Portal durchgeführt werden.

### Ist ein automatisches Failover möglich?

Es gibt keine Option für ein automatisches Failover. Sie können ein Failover entweder über eine Schaltfläche im Portal oder mit [Site Recovery-PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) auslösen. Sie verwenden den lokalen Orchestrator oder Operations Manager, um einen Fehler bei virtuellen Computern zu erkennen und dann das Failover mithilfe des SDK auszulösen. Dadurch wird es ein automatischer Prozess.




## Überwachung

### Welche Lösungen stehen für die Überwachung meines Setups und der laufenden Replikation zur Verfügung?
Sie können E-Mail-Benachrichtigungen des ASR-Diensts abonnieren, um direkt über mögliche Probleme informiert zu werden, die Ihre Aufmerksamkeit erfordern. Der Status einer laufenden Replikation kann auch mithilfe von SCOM überwacht werden.

## Nächste Schritte

So stellen Sie ASR bereit:

- [Einrichten von Schutz zwischen einem lokalen VMM-Standort und Azure](site-recovery-vmm-to-azure.md) 
- [Einrichten von Schutz zwischen einem lokalen Hyper-V-Standort und Azure](site-recovery-hyper-v-site-to-azure.md) 
- [Einrichten von Schutz zwischen zwei lokalen VMM-Standorten](site-recovery-vmm-to-vmm.md) 
- [Einrichten von Schutz zwischen lokalen VMM-Standorten mit einem SAN](site-recovery-vmm-san.md) 
- [Einrichten von Schutz mit einem einzelnen VMM-Server](site-recovery-single-vmm.md) 

 

<!---HONumber=Sept15_HO2-->