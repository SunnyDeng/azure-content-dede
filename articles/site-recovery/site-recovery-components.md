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
	ms.date="12/07/2015"
	ms.author="raynew"/>

# Wie funktioniert Azure Site Recovery?

## Informationen zum Artikel

In diesem Artikel werden die zugrunde liegende Architektur von Site Recovery und die dafür erforderlichen Komponenten beschrieben. Sollten Sie nach der Lektüre dieses Artikels Fragen haben, können Sie diese im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) stellen.


## Übersicht

Organisationen benötigen eine Strategie für die Bereiche Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR), mit der bestimmt wird, wie Apps, Workloads und Daten bei geplanten und ungeplanten Ausfallzeiten verfügbar bleiben und so schnell wie möglich die normalen Arbeitsbedingungen wiederhergestellt werden können. Bei Ihrer BCDR-Strategie geht es hauptsächlich um Lösungen, bei denen die Unternehmensdaten sicher sind und wiederhergestellt werden können und Workloads auch nach dem Eintreten eines Notfalls ständig verfügbar sind.

Site Recovery ist ein Azure-Dienst, der einen Beitrag zu Ihrer BCDR-Strategie leistet, indem die Replikation von lokalen physischen Servern und virtuellen Maschinen in die Cloud (Azure) oder in ein sekundäres Rechenzentrum orchestriert wird. Wenn es an Ihrem primären Standort zu Ausfällen kommt, wird ein Failover zum sekundären Standort durchgeführt, um die Verfügbarkeit von Apps und Workloads zu erhalten. Wenn wieder Normalbetrieb herrscht, führen Sie das Failback zum primären Standort durch.

Site Recovery kann in verschiedenen Szenarien verwendet werden und für den Schutz mehrerer Workloads sorgen.

- **Schützen von virtuellen VMware-Maschinen**: Sie können lokale virtuelle VMware-Maschinen schützen, indem Sie sie zu Azure oder in ein sekundäres Rechenzentrum replizieren. **Schützen von Hyper-V-VMs**: Sie können lokale virtuelle Hyper-V-Maschinen schützen, indem Sie sie in die Cloud (Azure) oder in ein sekundäres Rechenzentrum replizieren.  
- **Schützen von physischen Servern**: Sie können physische Computer, auf denen Windows oder Linux ausgeführt wird, schützen, indem Sie sie zu Azure oder in ein sekundäres Rechenzentrum replizieren.
- **Migrieren von VMs**: Sie können Site Recovery verwenden, um Azure-IaaS-VMs zwischen Regionen zu migrieren oder AWS Windows-Instanzen zu Azure-IaaS-VMs migrieren.

Eine vollständige Zusammenfassung der unterstützten Bereitstellungen finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md) und [Welche Workloads können mit Azure Site Recovery geschützt werden?](site-recovery-workload.md).

## Replizieren zwischen einem lokalen physischen Server oder einer virtuellen VMware-Maschine und Azure

Wenn Sie VMware-VMs oder physische Windows/Linux-Computer per Replikation zu Azure schützen möchten, benötigen Sie die unten angegebenen Dinge.

**Standort** | **Erforderliches Element** 
--- | --- 
 Lokal | **Prozessserver**: Mit diesem Server werden Daten von geschützten virtuellen VMware-Maschinen oder physischen Windows/Linux-Computern optimiert, bevor sie an Azure gesendet werden. Außerdem werden hiermit die Pushinstallation der Mobilitätsdienstkomponente auf dem geschützten Computer und die automatische Ermittlung von virtuellen VMware-Maschinen durchgeführt. <br/><br/> **VMware vCenter-Server**: Beim Schützen von VMware-VMs benötigen Sie einen VMwave vCenter-Server zum Verwalten der vSphere-Hypervisors.<br/><br/> **ESX-Server**: Beim Schützen von VMware-VMs benötigen Sie einen Server, auf dem ESX/ESXi Version 5.1 oder 5.5 mit den neuesten Updates ausgeführt wird.<br/><br/> **Maschinen**: Beim Schützen von VMware sollten Sie über VMware-VMs verfügen, auf den die VMware-Tools installiert sind und ausgeführt werden. Wenn Sie physische Computer schützen, sollte darauf ein unterstütztes Windows- oder Linux-Betriebssystem ausgeführt werden. Weitere Informationen finden Sie unter [Unterstützung](site-recovery-vmware-to-azure.md/#before-you-start). <br/><br/> **Mobilitätsdienst**: Wird auf Maschinen installiert, die Sie schützen möchten, um Änderungen zu erfassen und an den Prozessserver zu übermitteln. <br/><br/>Drittanbieterkomponenten: Diese Bereitstellung ist von einigen [Drittanbieterkomponenten](http://download.microsoft.com/download/C/D/7/CD79E327-BF5A-4026-8FF4-9EB990F9CEE2/Third-Party_Notices.txt) abhängig.
Azure | **Konfigurationsserver**: Standardmäßige A3 Azure-VM zum Koordinieren der Kommunikation zwischen geschützten Computern, dem Prozessserver und den Masterzielservern in Azure. Dient zum Einrichten der Replikation und Koordinieren der Wiederherstellung, wenn ein Failover eintritt. <br/><br/>**Masterzielserver**: Azure-VM mit replizierten Daten von geschützten Computern, indem angefügte VHDs verwendet werden, die in der Blob Storage-Komponente Ihres Azure-Speicherkontos erstellt wurden. Ein Failback-Masterzielserver wird lokal ausgeführt, damit Sie für Azure-VMs das Failback auf VMware-VMs durchführen können. <br/><br/> **Site Recovery-Tresor**: Mindestens ein Azure Site Recovery-Tresor (Einrichtung mit einem Abonnement des Site Recovery-Diensts). <br/><br/> **Virtual Network**: Ein Azure-Netzwerk, in dem sich der Konfigurationsserver und die Masterzielserver befinden (unter demselben Abonnement und in derselben Region wie der Site Recovery-Dienst. <br/><br/> **Azure-Speicher**: Azure-Speicherkonto zum Speichern der replizierten Daten. Hierbei sollte es sich um ein standardmäßiges georedundantes Konto oder Premium-Konto in derselben Region wie das Site Recovery-Abonnement handeln.


Bei diesem Szenario kann die Kommunikation über eine VPN-Verbindung mit internen Ports im Azure-Netzwerk (per Azure ExpressRoute oder einem Site-to-Site-VPN) erfolgen, oder es kann eine sichere Internetverbindung mit den zugeordneten öffentlichen Endpunkten im Azure-Clouddienst für die Konfigurations- und Masterzielserver-VMs verwendet werden.

Der Mobilitätsdienst auf geschützten Computern sendet die Replikationsdaten an den Prozessserver und die Replikationsmetadaten an den Konfigurationsserver. Der Prozessserver kommuniziert mit dem Konfigurationsserver, um Informationen zur Verwaltung und Steuerung auszutauschen. Er sendet Replikationsinformationen an den Masterzielserver und optimiert und sendet replizierte Daten an den Masterzielserver.

## Replizieren von Hyper-V-VMs zu Azure (mit VMM)

Wenn sich Ihre VMs auf einem Hyper-V-Host befinden, der sich in einer System Center-VMM-Cloud befindet, benötigen Sie für die Replikation zu Azure die folgenden Dinge.

**Standort** | **Erforderliches Element** 
--- | --- 
Lokal | **VMM-Server**: Mindestens einen VMM-Server, für den mindestens eine private VMM-Cloud eingerichtet ist. Der Azure Site Recovery-Anbieter wird auf jedem VMM-Server installiert.<br/><br/>**Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver in der VMM-Cloud. Der Microsoft Recovery Services-Agent wird auf jedem Hyper-V-Server installiert. <br/><br/> **Virtuelle Maschinen**: Mindestens eine virtuelle Maschine, die auf dem Hyper-V-Server ausgeführt wird. Auf der virtuellen Maschine wird nichts installiert.
Azure | **Site Recovery-Tresor**: Mindestens ein Azure Site Recovery-Tresor (Einrichtung mit einem Abonnement des Site Recovery-Diensts). <br/><br/>**Speicherkonto**: Ein Azure-Speicherkonto unter demselben Abonnement wie der Site Recovery-Dienst. Replizierte Computer werden im Azure-Speicher gespeichert. 

Bei diesem Szenario koordiniert und orchestriert der auf dem VMM-Server ausgeführte Anbieter die Replikation mit dem Site Recovery-Dienst über das Internet. Die Daten werden zwischen dem Recovery Services-Agent, der auf dem lokalen Hyper-V-Server ausgeführt wird, und dem Azure-Speicher über HTTPS 443 repliziert. Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt. Die replizierten Daten im Azure-Speicher werden ebenfalls verschlüsselt.

![Lokaler VMM zu Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

## Replizieren von Hyper-V-VMs zu Azure (ohne VMM)

Wenn Sie Ihre VMs nicht über einen System Center-VMM-Server verwalten, müssen Sie die Replikation zu Azure wie folgt durchführen:

**Standort** | **Erforderliches Element**
--- | --- 
 Lokal | **Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver. Der Azure Site Recovery-Anbieter und der Microsoft Recovery Services-Agent werden auf jedem Hyper-V-Server installiert. <br/><br/>**Virtuelle Maschinen**: Mindestens eine virtuelle Maschine, die auf dem Hyper-V-Server ausgeführt wird. Auf der virtuellen Maschine wird nichts installiert.
Azure | **Site Recovery-Tresor**: Mindestens ein Azure Site Recovery-Tresor (Einrichtung mit einem Abonnement des Site Recovery-Diensts). <br/><br/>**Speicherkonto**: Ein Azure-Speicherkonto unter demselben Abonnement wie der Site Recovery-Dienst. Replizierte Computer werden im Azure-Speicher gespeichert.

Bei diesem Szenario koordiniert und orchestriert der auf dem Hyper-V-Server ausgeführte Anbieter die Replikation mit dem Site Recovery-Dienst über das Internet. Die Daten werden zwischen dem Recovery Services-Agent, der auf dem lokalen Hyper-V-Server ausgeführt wird, und dem Azure-Speicher über HTTPS 443 repliziert. Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt. Die replizierten Daten im Azure-Speicher werden ebenfalls verschlüsselt.

![Lokaler VMM zu Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)



## Replizieren von Hyper-V-VMs in ein sekundäres Rechenzentrum

Wenn Sie Ihre Hyper-V-VMs schützen möchten, indem Sie sie in ein sekundäres Rechenzentrum replizieren, gehen Sie wie unten angegeben vor. Beachten Sie, dass dies nur möglich ist, wenn Ihr Hyper-V-Hostserver in einer System Center-VMM-Cloud verwaltet wird.

**Standort** | **Erforderliches Element** 
--- | --- 
 Lokal | **VMM-Server**: Ein VMM-Server am primären Standort und ein VMM-Server am sekundären Standort. Der Azure Site Recovery-Anbieter wird auf beiden VMM-Servern installiert.<br/><br/>**Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver in einer VMM-Cloud am primären Standort und sekundären Standort. Auf den Hyper-V-Servern wird nichts installiert. <br/><br/> **Virtuelle Maschinen**: Mindestens eine virtuelle Maschine, die auf dem Hyper-V-Server ausgeführt wird. Auf der virtuellen Maschine wird nichts installiert.
Azure | **Site Recovery-Tresor**: Mindestens ein Azure Site Recovery-Tresor (Einrichtung mit einem Abonnement des Site Recovery-Diensts). 

Bei diesem Szenario koordiniert und orchestriert der Anbieter auf dem VMM-Server die Replikation mit dem Site Recovery-Dienst über das Internet. Die Daten werden zwischen dem primären und sekundären Hyper-V-Hostserver über das Internet per Kerberos- oder Zertifikatauthentifizierung repliziert. Die Kommunikation vom Anbieter und zwischen den Hyper-V-Hostservern ist jeweils sicher und verschlüsselt.

![Lokal zu lokal](./media/site-recovery-components/arch-onprem-onprem.png)

## Replizieren von Hyper-V-VMs in ein sekundäres Rechenzentrum per SAN-Replikation

Wenn sich Ihre VMs auf einem Hyper-V-Host befinden, der in einer System Center-VMM-Cloud verwaltet wird, und wenn Sie einen SAN-Speicher verwenden, müssen Sie bei der Replikation zwischen zwei Rechenzentren wie unten angegeben vorgehen.

**Standort** | **Erforderliches Element** 
--- | --- 
 Primäres Rechenzentrum | **SAN-Array**: Ein [unterstütztes SAN-Array](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), das vom primären VMM-Server verwaltet wird. Das SAN verfügt über eine gemeinsame Netzwerkinfrastruktur mit einem anderen SAN-Array am sekundären Standort. <br/><br/> **VMM-Server**: Mindestens ein VMM-Server, für den eine oder mehrere VMM-Clouds und Replikationsgruppen eingerichtet wurden. Der Azure Site Recovery-Anbieter wird auf allen VMM-Servern installiert. <br/><br/> **Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver mit virtuellen Maschinen, die in der Replikationsgruppe angeordnet sind. Auf den Hyper-V-Hostservern wird nichts installiert.<br/><br/> **Virtuelle Maschinen**: Mindestens eine virtuelle Maschine, die auf dem Hyper-V-Hostserver ausgeführt wird. Auf der virtuellen Maschine wird nichts installiert. 
Sekundäres Datencenter | **SAN-Array**: Ein [unterstütztes SAN-Array](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), das vom sekundären VMM-Server verwaltet wird. <br/><br/>**VMM-Server**: Mindestens ein VMM-Server mit einer oder mehreren VMM-Clouds.<br/><br/> **Hyper-V-Server**: Mindestens ein Hyper-V-Hostserver. 
Azure | **Site Recovery-Tresor**: Mindestens ein Azure Site Recovery-Tresor (Einrichtung mit einem Abonnement des Site Recovery-Diensts).

Bei diesem Szenario koordiniert und orchestriert der Anbieter auf dem VMM-Server die Replikation mit dem Site Recovery-Dienst über das Internet. Die Daten werden zwischen den primären und sekundären Speicherarrays per synchroner SAN-Replikation repliziert.

![Lokal zu lokal](./media/site-recovery-components/arch-onprem-onprem-san.png)



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

## Replizieren von virtuellen VMware-Maschinen und physischen Servern zu Azure

Sie können virtuelle VMware-Maschinen und physische Server (Windows/Linux) über eine Site-to-Site-VPN-Verbindung oder das Internet zu Azure replizieren.

### Replizieren über eine Site-to-Site-VPN-Verbindung (oder ExpressRoute) zu Azure

![VMware- oder physischer Computer zu Azure über das Internet](./media/site-recovery-components/arch-onprem-azure-vmware-vpn.png)

#### Replizieren über das Internet

![VMware- oder physischer Computer zu Azure über das Internet](./media/site-recovery-components/arch-onprem-azure-vmware-internet.png)

## Replizieren zwischen lokalen physischen Servern oder virtuellen VMware-Maschinen in primären und sekundären Rechenzentren

Wenn Sie VMware-VMs oder physische Windows/Linux-Computer per Replikation zwischen zwei lokalen Rechenzentren schützen möchten, benötigen Sie die unten angegebenen Dinge.

**Standort** | **Erforderliches Element** 
--- | --- 
 Lokaler primären Standort | **Prozessserver**: Richten Sie die Prozessserverkomponente an Ihrem primären Standort ein, um das Caching, die Komprimierung und die Datenoptimierung zu behandeln. Außerdem erfolgt hierüber die Pushinstallation des vereinheitlichten Agents auf Maschinen, die Sie schützen möchten. <br/><br/> **VMware-Schutz**: Beim Schützen von VMware-VMs benötigen Sie einen VMware EXS/ESXi-Hypervisor oder einen VMware vCenter-Server zum Verwalten mehrerer Hypervisors.<br/><br/> **Schutz physischer Server**: Wenn Sie physische Computer schützen, sollte darauf Windows oder Linux ausgeführt werden. <br/><br/> **Vereinheitlichter Agent**: Wird auf Maschinen installiert, die Sie schützen möchten, sowie auf der Maschine, die als Masterzielserver dient. Der Agent fungiert als Kommunikationsanbieter zwischen allen InMage-Komponenten.
Lokaler sekundärer Standort | **Konfigurationsserver**: Der Konfigurationsserver ist die erste Komponente, die Sie installieren. Er wird am sekundären Standort installiert, um Ihre Bereitstellung zu verwalten, zu konfigurieren und zu überwachen, und zwar entweder mit der Verwaltungswebsite oder der vContinuum-Konsole. Der Konfigurationsserver enthält auch den Pushmechanismus für die Remotebereitstellung des vereinheitlichten Agents. Eine Bereitstellung enthält nur einen Konfigurationsserver. Er muss auf einem Computer installiert sein, auf dem Windows Server 2012 R2 ausgeführt wird. <br/><br/> **vContinuum-Server**: Installieren Sie diesen Server an demselben Ort (sekundärer Standort) wie den Konfigurationsserver. Er enthält eine Konsole zum Verwalten und Überwachen Ihrer geschützten Umgebung. Bei einer standardmäßigen Installation ist der vContinuum-Server der erste Masterzielserver und verfügt über eine Installation des vereinheitlichten Agents. <br/><br/> **Masterzielserver**: Der Masterzielserver enthält replizierte Daten. Er empfängt Daten vom Prozessserver und erstellt einen Replikatcomputer am sekundären Standort. Außerdem enthält er die Punkte für die Beibehaltung der Daten. Die Anzahl der benötigten Masterzielserver richtet sich nach der Anzahl der Computer, die Sie schützen. Wenn Sie ein Failback zum primären Standort durchführen möchten, benötigen Sie dort ebenfalls einen Masterzielserver. 
Azure | **Site Recovery-Tresor**: Mindestens ein Azure Site Recovery-Tresor (Einrichtung mit einem Abonnement des Site Recovery-Diensts). Sie laden InMage Scout herunter, um die Bereitstellung nach dem Erstellen des Tresors einzurichten. Außerdem installieren Sie das neueste Update für alle InMage-Komponentenserver.


In diesem Szenario werden Deltareplikationsänderungen vom vereinheitlichten Agent, der auf dem geschützten Computer ausgeführt wird, an den Prozessserver gesendet. Der Prozessserver optimiert diese Daten und überträgt sie an den Masterzielserver am sekundären Standort. Der Konfigurationsserver verwaltet den Replikationsprozess.




## Nächste Schritte

[Bereiten Sie sich auf die Bereitstellung vor](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0128_2016-->