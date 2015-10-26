<properties
	pageTitle="Grundlegendes zum Schutz von Standort auf Azure" 
	description="Verwenden Sie diesen Artikel, um die technischen Konzepte zu verstehen, mit denen Sie Azure Site Recovery erfolgreich installieren, konfigurieren und verwalten können." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/01/2015" 
	ms.author="anbacker"/>


# Grundlegendes zum Schutz von Hyper-V- oder VMM-Standorten auf Azure

Dieser Artikel beschreibt die technischen Konzepte, die Ihnen beim erfolgreichen Konfigurieren und Verwalten des Schutzes von Hyper-V- oder VMM-Standorten auf Azure mithilfe von Azure Site Recovery helfen.

## Grundlegendes zu den Komponenten

### Bereitstellung des Hyper-V-Standorts oder VMM-Standorts für die Replikation zwischen lokalen Standorten und Azure.

Als Bestandteil der Einrichtung der Notfallwiederherstellung zwischen lokalen Standorten und Azure muss Azure Site Recovery Provider heruntergeladen und auf dem VMM-Server installiert werden. Zudem muss der Azure Recovery Services-Agent auf jedem Hyper-V-Host installiert werden.

![Bereitstellung des VMM-Standorts für die Replikation zwischen lokalen Standorten und Azure.](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Diese Hyper-V-Standortbereitstellung entspricht der Bereitstellung des VMM-Standorts mit der einzigen Ausnahme, dass der Anbieter und die Agents auf dem Hyper-V-Host selbst installiert werden.

## Grundlegendes zu den Workflows

### Schutz aktivieren
Nachdem Sie einen virtuellen Computer aus dem Portal oder lokalen geschützt haben, wird ein ASR-Auftrag mit dem Namen *Schutz aktivieren* initiiert, und kann unter der Registerkarte "Aufträge" überwacht werden.

![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-understanding-site-to-azure-protection/image01.png)

Der Auftrag *Schutz aktivieren* überprüft die erforderlichen Komponenten, bevor [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) aufgerufen wird, wodurch die Replikation nach Azure mithilfe von Eingaben erstellt wird, die während des Schutzes konfiguriert wurden. Der Auftrag *Schutz aktivieren* startet die erste Replikation von einem lokalen Standort durch Aufrufen von [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), wodurch der virtuelle Datenträger des virtuellen Computers an Azure gesendet wird.

![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-understanding-site-to-azure-protection/image02.png)

### Abschließen des Schutzes
Eine [Hyper-V-VM-Momentaufnahme](https://technet.microsoft.com/library/dd560637.aspx) wird ausgeführt, wenn die erste Replikation ausgelöst wird. Virtueller Festplatten werden nacheinander verarbeitet, bis alle Datenträger in Azure hochgeladen werden. Dieser Vorgang dauert normalerweise eine Weile, je nach Größe des Datenträgers und der Bandbreite. Unter [How to manage on-premises to Azure protection network bandwidth usage](https://support.microsoft.com/kb/3056159) (auf Englisch; Verwalten der geschützten Nutzung der Netzwerkbandbreite „von lokal an Azure“) finden Sie Informationen zum Optimieren der Netzwerknutzung. Nach Abschluss der Erstreplikation konfiguriert der Auftrag *Schutz auf dem virtuellen Computer abschließen* die Netzwerk- und Nach-Replikationseinstellungen. Während der Erstreplikation in Bearbeitung ist, werden alle Änderungen an den Datenträgern überwacht, wie im Abschnitt Deltareplikation weiter unten erwähnt. Zusätzliche Speicherplatz wird für die Momentaufnahmen- und HRL-Dateien verbraucht, während die Erstreplikation im Gange ist. Nach Abschluss der Erstreplikation wird die Hyper-V-VM-Momentaufnahme gelöscht, was zur Zusammenführung der Datenänderungen nach der Erstreplikation auf dem übergeordneten Datenträger führt.

![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### Delta-Replikation
Der Hyper-V-Replikat Replication Tracker, der Teil des Hyper-V-Replikat-Replikationsmoduls ist, verfolgt die Änderungen auf einer virtuellen Festplatte in Form von Hyper-V-Replikationsprotokollen (*.hrl). HRL-Dateien werden im selben Verzeichnis wie die zugeordneten Datenträgern abgelegt. Jeder Datenträger für die Replikation verfügt über eine zugeordnete HRL-Datei. Diese Protokolle werden an das Speicherkonto des Kunden gesendet, nachdem die Erstreplikation abgeschlossen ist. Wenn ein Protokoll an Azure übertragen wird, werden Änderungen der primären Datei in einer anderen Protokolldatei im selben Verzeichnis nachverfolgt.

Die VM-Replikationsintegrität während der Erstreplikation oder der Deltareplikation kann in der Ansicht des virtuellen Computers überwacht werden, und zwar unter [Replikationsintegrität für den virtuellen Computer überwachen](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).

### Erneute Synchronisierung 
Eine virtuelle Maschine wird für die erneute Synchronisierung markiert, wenn die Deltareplikation fehlschlägt und die vollständige Erstreplikation die Netzwerkbandbreite zu stark beansprucht oder die Zeit zu lange ist, die bis zum Abschluss einer vollständigen anfänglichen Replikation nötig ist. Wenn eine HRL-Datei beispielsweise bis zu 50 % der Gesamtgröße des Datenträgers beansprucht, wird die virtuelle Maschine für die erneute Synchronisierung markiert. Die erneute Synchronisierung minimiert die Menge der Daten, die über das Netzwerk gesendet werden, indem Prüfsummen von den Datenträgern der virtuellen Quell- und Zielmaschine berechnet werden, und sendet nur die Differenz.

Nach Abschluss der erneuten Synchronisierung sollte die normale Deltareplikation fortgesetzt werden. Eine Erneute Synchronisierung kann bei einem Ausfall (z. B. Netzwerkausfall, VMMS-Absturz usw.) fortgesetzt werden.

Standardmäßig wird die *automatisch geplante erneute Synchronisierung* nicht während der Arbeitszeit konfiguriert. Wenn der virtuelle Computer manuell neu synchronisiert werden muss, wählen Sie den virtuellen Computer aus dem Portal und klicken Sie auf „ERNEUT SYNCHRONISIEREN“. ![Behandlung von lokalen Hyper-V-Problemen](media/site-recovery-understanding-site-to-azure-protection/image04.png)

Die erneute Synchronisierung verwendet einen Chunking-Blockalgorithmus, in dem Quell-und Zieldateien in feste Blöcke unterteilt sind. Prüfsummen werden für jedes Segment generiert und dann verglichen, um zu bestimmen, welche Speicherblöcke aus der Quelle auf den Zielcomputer angewendet werden müssen.

### Wiederholungslogik
Es gibt eine integrierte Wiederholungslogik, wenn Replikationsfehler auftreten. Dies kann in zwei Kategorien wie folgt klassifiziert werden.

| Kategorie | Szenarios |
|---------------------------|----------------------------------------------|
| Nicht behebbarer Fehler | Keine Wiederholung wird versucht. Der Replikationsstatus der virtuellen Maschine wird als "Kritisch" angezeigt, und ein Eingreifen des Administrators ist erforderlich. Zu den Beispielen gehören <ul><li>Eine unterbrochene VHD-Kette</li><li>Virtueller Replikacomputer befindet sich in einem ungültigen Zustand</li><li>Fehler bei der Netzwerkauthentifizierung</li><li>Autorisierungsfehler</li><li>Wenn ein virtueller Computer bei einem eigenständigen Hyper-V-Server nicht gefunden wird</li></ul>|
| Behebbarer Fehler | Wiederholungen treten jedes Replikationsintervall mit exponentiellem Backoff auf, was das Wiederholungsintervall vom Beginn des ersten Versuchs an erhöht (1, 2, 4, 8, 10 Minuten). Wenn ein Fehler auftritt, versuchen Sie es jede halbe Stunde erneut. Zu den Beispielen gehören <ul><li>Netzwerkfehler</li><li>Wenig Speicherplatz</li><li>Nicht genügend Arbeitsspeicher</li></ul>|

## Grundlegendes zum Schutz- und Wiederherstellungslebenszyklus für virtuelle Hyper-V-Computer

![Grundlegendes zum Schutz- und Wiederherstellungslebenszyklus für virtuelle Hyper-V-Computer](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## Andere Referenzen

- [Überwachung und Problembehandlung für den Schutz von VMware, VMM, Hyper-V und physischen Standorten](./site-recovery-monitoring-and-troubleshooting.md)
- [Microsoft-Support](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Häufige Fehler bei der automatischen Systemwiederherstellung und deren Lösungen](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!---HONumber=Oct15_HO3-->