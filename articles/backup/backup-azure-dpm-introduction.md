<properties
	pageTitle="Einführung in Azure DPM Backup | Microsoft Azure"
	description="Eine Einführung in die Sicherung von DPM-Servern mithilfe des Azure Backup-Diensts"
	services="backup"
	documentationCenter=""
	authors="SamirMehta"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/05/2015" ms.author="sammehta"; "jimpark"/>

# Vorbereiten der Sicherung von Workloads in Azure mit DPM

> [AZURE.SELECTOR]
- [Azure Backup](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)

Dieser Artikel enthält eine Einführung zur Verwendung von Microsoft Azure Backup zum Schutz von DPM-Servern und -Workloads (System Center Data Protection Manager). In diesem Artikel erfahren Sie Folgendes:

- Funktionsweise der Azure DPM-Serversicherung
- Voraussetzungen für eine reibungslose Sicherung
- Typische Fehler und ihre Behandlung
- Unterstützte Szenarien

System Center DPM sichert Datei- und Anwendungsdaten. Daten, die in DPM gesichert werden, können auf Band, auf Festplatte gespeichert oder in Azure mit Microsoft Azure Backup gesichert werden. DPM interagiert mit Azure Backup wie folgt:

- **DPM als physischer Server oder auf einem lokalen virtuellen Computer bereitgestellt** – Wenn DPM als physischer Server oder als lokaler Hyper-V-Computer bereitgestellt wird, können Sie Daten im Azure Backup-Tresor zusätzlich zur Festplatten- und Bandsicherung sichern.
- **DPM als virtueller Azure-Computer bereitgestellt** – Ab System Center 2012 R2 mit Update 3 kann DPM als virtueller Azure-Computer bereitgestellt werden. Wenn DPM als virtueller Azure-Computer bereitgestellt wird, können Sie Daten auf Azure-Festplatten sichern, die an den virtuellen Azure DPM-Computer angeschlossen sind, oder Sie können die Datenspeicherung durch Sichern in einem Azure Backup-Tresor auslagern.

## Warum sollten Sie DPM-Server sichern?

Vorteile der Verwendung von Azure Backup für die Sicherung von DPM-Servern:

- Für die lokale DPM-Bereitstellung können Sie Azure Backup als Alternative zur langfristigen Bereitstellung auf Band verwenden.
- Bei DPM-Bereitstellungen in Azure ermöglicht Azure Backup das Auslagern des Speichers vom Azure-Datenträger, sodass Sie vertikal skalieren können, indem Sie ältere Daten in Azure Backup und neue Daten auf dem Datenträger speichern.

## Funktionsweise der DPM-Serversicherung
Um einen virtuellen Computer zu sichern, wird zuerst eine Zeitpunkt-Momentaufnahme der Daten benötigt. Der Azure Backup-Dienst initiiert den Sicherungsauftrag zur geplanten Zeit und löst die Sicherungserweiterung zum Erstellen einer Momentaufnahme aus. Die Sicherungserweiterung wird zum Zweck der Konsistenz mit dem VSS-Gastdienst abgestimmt und ruft die Blob-Momentaufnahme-API des Azure Storage-Diensts auf, wenn Konsistenz erzielt wurde. Dadurch kann eine konsistente Momentaufnahme der Datenträger des virtuellen Computers erstellt werden, ohne dass dieser heruntergefahren werden muss.

Nachdem die Momentaufnahme erstellt wurde, werden die Daten vom Azure Backup-Dienst in den Sicherungstresor übertragen. Der Dienst identifiziert und überträgt nur die Datenblöcke, die sich seit der letzten Sicherung geändert haben, sodass die Sicherungen effizient gespeichert und das Netzwerk effizient genutzt werden. Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt. Dieser Wiederherstellungspunkt kann im Azure-Verwaltungsportal angezeigt werden.

>[AZURE.NOTE]Für virtuelle Linux-Computer können nur dateikonsistente Sicherungen durchgeführt werden.

## Voraussetzungen
Bereiten Sie Azure Backup wie folgt zum Sichern von DPM-Daten vor:

1. **Erstellen eines Sicherungstresors** – Erstellen Sie einen Tresors in der Azure Backup-Konsole.
2. **Herunterladen von Tresoranmeldedaten** – Laden Sie in Azure Backup das Verwaltungszertifikat hoch, das Sie für den Tresor erstellt haben.
3. **Installieren des Azure Backup-Agents und Registrieren des Servers** – Installieren Sie von Azure Backup aus, den Agent auf jedem DPM-Server, und registrieren Sie den DPM-Server im Sicherungstresor.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## Anforderungen (und Einschränkungen)

- DPM kann als physischer Server oder als virtueller Hyper-V-Computer, der auf System Center 2012 SP1 oder System Center 2012 R2 installiert ist, ausgeführt werden. DPM kann auch als virtueller Azure-Computer unter System Center 2012 R2 mit mindestens DPM 2012 R2 Update Rollup 3 oder auf einem virtuellen Windows-Computer in VMWare in System Center 2012 R2 mit mindestens Update Rollup 5 ausgeführt werden.
- Wenn Sie DPM mit System Center 2012 SP1 ausführen, sollten Sie Update Rollup 2 für System Center Data Protection Manager SP1 installieren. Dies ist erforderlich, bevor Sie den Azure Backup-Agent installieren können.
- Auf dem DPM-Server muss Azure PowerShell und .Net Framework 4.5 installiert sein.
- DPM kann die meisten Workloads in Azure Backup sichern. Eine vollständige Liste der Unterstützungen finden Sie in den Azure Backup-Elementen weiter unten.
- Mit der Option "Kopieren auf Band" können Daten, die in Azure Backup gespeichert sind, nicht wiederhergestellt werden.
- Sie benötigen ein Azure-Konto mit aktivierter Azure Backup-Funktion. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Erfahren Sie mehr über [Preisgestaltung von Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
- Für das Verwenden von Azure Backup muss der Azure Backup-Agent auf den Servern installiert werden, die Sie sichern möchten. Jeder Server muss über mindestens 2,5 GB lokalen freien Speicherplatz für den Cachespeicherort verfügen, obwohl 15 GB freier lokaler Speicherplatz für den Cachespeicherort empfohlen wird.
- Daten werden im Azure-Tresorspeicher gespeichert. Es gibt keine Beschränkung der Datenmenge, die Sie in einem Azure Backup-Tresor sichern, aber die Größe einer Datenquelle (z. B. ein virtueller Computer oder eine Datenbank) sollte 54400 GB nicht überschreiten.

Diese Dateitypen werden für die Sicherung in Azure unterstützt:

- Verschlüsselt (nur vollständige Sicherungen)
- Komprimiert (inkrementelle Sicherungen werden unterstützt)
- Platzsparend (inkrementelle Sicherungen werden unterstützt)
- Komprimiert und platzsparend (als platzsparend behandelt)

Diese werden nicht unterstützt:

- Server auf Dateisystemen, die nach Groß-/Kleinschreibung unterscheiden, werden nicht unterstützt.
- Feste Links (übersprungen)
- Analysepunkte (übersprungen)
- Verschlüsselt und komprimiert (übersprungen)
- Verschlüsselt und platzsparend (übersprungen)
- Komprimierter Stream
- Platzsparender Stream

>[AZURE.NOTE]Ab System Center 2012 DPM mit SP1 können Sie Workloads, die von DPM geschützt werden, in Azure mithilfe von Microsoft Azure Backup sichern.

<!---HONumber=Oct15_HO2-->