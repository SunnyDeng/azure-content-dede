<properties
  pageTitle="Vorbereiten Ihrer Umgebung für die Sicherung von Workloads mithilfe von Azure Backup Server | Microsoft Azure"
  description="Vergewissern Sie sich, dass Ihre Umgebung ordnungsgemäß für die Sicherung von Workloads mithilfe von Azure Backup Server vorbereitet ist."
  services="backup"
  documentationCenter=""
  authors="Jim-Parker"
  manager="jwhit"
  editor=""
  keywords="Azure Backup Server: Sicherungstresor"/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="02/04/2016"
  ms.author="jimpark; trinadhk;"/>

# Vorbereiten der Sicherung von Workloads per Azure Backup Server

> [AZURE.SELECTOR]
- [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [System Center DPM](backup-azure-dpm-introduction.md)

In diesem Artikel geht es um das Vorbereiten Ihrer Umgebung für die Sicherung von Workloads per Azure Backup Server. Mit Azure Backup Server können Sie Anwendungsworkloads wie Hyper-V-VMs, Microsoft SQL Server, SharePoint Server, Microsoft Exchange und Windows-Clients über eine zentrale Konsole schützen.

>[AZURE.WARNING] Azure Backup Server erbt die Funktionalität des Data Protection Manager (DPM) für die Workloadsicherung. Sie finden bei einigen dieser Funktionen Verweise auf die DPM-Dokumentation. Azure Backup Server stellt jedoch keinen Schutz für Bandspeicher bereit und kann nicht in System Center integriert werden.

## 1\. Windows Server-Computer

![Schritt 1](./media/backup-azure-microsoft-azure-backup/step1.png)

Der erste Schritt zum Einrichten von Azure Backup Server ist die Bereitstellung eines Windows Server-Computers.

| Standort | Mindestanforderungen | Zusätzliche Anweisungen |
| -------- | -------------------- | ----------------------- |
| Azure | Virtueller Azure-IaaS-Computer<br><br>A2-Standard: 2 Kerne, 3,5 GB RAM | Sie können mit einem einfachen Katalogimage von Windows Server 2012 R2 Datacenter beginnen. [Das Schützen von IaaS-Workloads mit Azure Backup Server (DPM)](https://technet.microsoft.com/library/jj852163.aspx) ist relativ komplex. Lesen Sie sich den Artikel ganz durch, bevor Sie die Maschine bereitstellen. |
| Lokal | Hyper-V-VM, <br>VMWare-VM<br> oder physischer Host<br><br>2 Kerne und 4 GB RAM | Sie können die DPM-Speicherung deduplizieren, indem Sie die Windows Server-Deduplizierung verwenden. Lesen Sie die weiteren Informationen zur Zusammenarbeit von [DPM und Deduplizierung](https://technet.microsoft.com/library/dn891438.aspx) bei Bereitstellung auf Hyper-V-VMs. |

> [AZURE.NOTE] Es wird empfohlen, Azure Backup Server auf einem Computer mit Windows Server 2012 R2 Datacenter zu installieren. Viele erforderliche Komponenten werden durch die neueste Version des Windows-Betriebssystems automatisch abgedeckt.

Wenn Sie diesen Server zu einem späteren Zeitpunkt mit einer Domäne verknüpfen möchten, ist es ratsam, den Beitritt zur Domäne vor der Installation von Azure Backup Server durchzuführen. Das Verschieben eines vorhandenen Azure Backup Server-Computers in eine neue Domäne nach der Bereitstellung wird *nicht unterstützt*.

## 2\. Sicherungstresor

![Schritt 2](./media/backup-azure-microsoft-azure-backup/step2.png)

Die Software muss unabhängig davon über eine Verbindung mit Azure verfügen, ob Sie Sicherungsdaten an Azure senden oder lokal verwalten. Genauer gesagt: Der Azure Backup Server-Computer muss für einen Sicherungstresor registriert sein.

So erstellen Sie einen Sicherungstresor

1. Melden Sie sich beim [Verwaltungsportal](http://manage.windowsazure.com/) an.

2. Klicken Sie auf **Neu** > **Data Services** > **Recovery Services** > **Sicherungstresor** > **Schnellerfassung**. Wenn mit Ihrem Organisationskonto mehrere Abonnements verknüpft sind, wählen Sie das Abonnement aus, das mit dem Sicherungstresor verknüpft werden soll.

3. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird. Dieser muss für jedes Abonnement eindeutig sein.

4. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Die Region des Tresors wird normalerweise basierend auf der Datensouveränität oder auf Einschränkungen der Netzwerklatenz ausgewählt.

    ![Erstellen des Sicherungstresors](./media/backup-azure-microsoft-azure-backup/backup_vaultcreate.png)

5. Klicken Sie auf **Tresor erstellen**. Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Unten im Portal können Sie anhand der Benachrichtigungen den Status prüfen.

    ![Popupbenachrichtigung zur Erstellung des Tresors](./media/backup-azure-microsoft-azure-backup/creating-vault.png)

6. In einer Meldung wird bestätigt, dass der Tresor erfolgreich erstellt wurde. Er wird dann auf der Seite "Recovery Services" als aktiv aufgeführt. ![Liste der Sicherungstresore](./media/backup-azure-microsoft-azure-backup/backup_vaultslist.png)

  > [AZURE.IMPORTANT] Vergewissern Sie sich nach der Erstellung des Tresors, dass die entsprechende Speicherredundanzoption ausgewählt ist. Erfahren Sie mehr über die Optionen für [georedundanten](../storage/storage-redundancy.md#geo-redundant-storage) und [lokal redundanten](../storage/storage-redundancy.md#locally-redundant-storage) Speicher in dieser [Übersicht](../storage/storage-redundancy.md).


## 3\. Softwarepaket

![Schritt 3](./media/backup-azure-microsoft-azure-backup/step3.png)

### Herunterladen des Softwarepakets

Ähnlich wie die Tresoranmeldedaten können Sie den Microsoft Azure Backup-Server für Anwendungsworkloads von der **Schnellstartseite** des Sicherungstresors herunterladen.

1. Klicken Sie auf **Für Anwendungsworkloads (Datenträger zu Datenträger zu Cloud)**. Sie gelangen auf die Download Center-Seite, von der Sie das Softwarepaket herunterladen können.

    ![Willkommensseite von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. Klicken Sie auf **Download**.

    ![Download Center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. Wählen Sie alle Dateien aus, und klicken Sie auf **Weiter**. Laden Sie alle Dateien herunter, die von der Microsoft Azure Backup-Downloadseite eingehen, und speichern Sie alle Dateien in demselben Ordner. ![Download Center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Da die Downloadgröße aller Dateien zusammen mehr als 3 G beträgt, dauert es über einen Downloadlink mit 10 MBit/s bis zu 60 Minuten, bis der Download abgeschlossen ist.


### Extrahieren des Softwarepakets

Nachdem Sie alle Dateien heruntergeladen haben, klicken Sie auf **MicrosoftAzureBackupInstaller.exe**. Der **Setup-Assistent von Microsoft Azure Backup** wird gestartet, um die Setupdateien an einem angegebenen Speicherort zu extrahieren. Fahren Sie mit dem Assistenten fort, und klicken Sie auf die Schaltfläche **Extrahieren**, um den Extrahierungsprozess zu starten.

> [AZURE.WARNING] Zum Extrahieren der Setupdateien sind mindestens 4 GB freier Speicherplatz erforderlich.


![Setup-Assistent von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Aktivieren Sie nach Abschluss der Extrahierung das Kontrollkästchen, um die gerade extrahierte Datei *setup.exe* zu starten und mit der Installation von Microsoft Azure Backup Server zu beginnen. Klicken Sie dann auf die Schaltfläche **Fertig stellen**.

### Installieren des Softwarepakets

1. Klicken Sie auf **Microsoft Azure Backup**, um den Setup-Assistenten zu starten.

    ![Setup-Assistent von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

2. Klicken Sie auf der Willkommensseite auf die Schaltfläche **Weiter**. Sie gelangen zum Abschnitt *Voraussetzungsüberprüfungen*. Klicken Sie auf diesem Bildschirm auf die Schaltfläche **Überprüfen**, um zu ermitteln, ob die Hardware- und Softwarevoraussetzungen für Azure Backup erfüllt sind. Wenn alle Voraussetzungen erfolgreich erfüllt wurden, sehen Sie eine Meldung, die darauf hinweist, dass der Computer die Anforderungen erfüllt. Klicken Sie auf die Schaltfläche **Weiter**.

    ![Azure Backup Server – Willkommen und Voraussetzungsüberprüfung](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

3. Für Microsoft Azure Backup Server ist SQL Server Standard erforderlich, und im Azure Backup Server-Installationspaket sind die passenden SQL Server-Binärdaten enthalten. Beim Starten einer neuen Azure Backup Server-Installation sollten Sie die Option **Neue Instanz von SQL Server bei diesem Setup installieren** auswählen und auf die Schaltfläche **Prüfen und installieren** klicken. Nachdem die erforderlichen Komponenten erfolgreich installiert wurden, klicken Sie auf **Weiter**.

    ![Azure Backup Server – SQL-Überprüfung](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Tritt ein Fehler mit der Empfehlung auf, den Computer neu zu starten, tun Sie dies, und klicken Sie auf **Erneut prüfen**.

    > [AZURE.NOTE] Azure Backup Server funktioniert nicht mit einer Remoteinstanz von SQL Server. Die von Azure Backup Server verwendete Instanz muss lokal vorliegen.

4. Geben Sie einen Speicherort für die Installation der Microsoft Azure Backup-Serverdateien an, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Das Scratchverzeichnis ist eine Anforderung für die Sicherung in Azure. Stellen Sie sicher, dass das Scratchverzeichnis eine Größe von mindestens 5 % der Daten aufweist, die in der Cloud gesichert werden. Für den Datenträgerschutz müssen separate Datenträger nach Abschluss der Installation konfiguriert werden. Weitere Informationen zu Speicherpools finden Sie unter [Konfigurieren von Speicherpools und Datenträgerspeicher](https://technet.microsoft.com/library/hh758075.aspx).

5. Geben Sie ein sicheres Kennwort für eingeschränkte lokale Benutzerkonten an, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/security-screen.png)

6. Bestimmen Sie, ob Sie mit *Microsoft Update* nach Updates suchen möchten, und klicken Sie auf **Weiter**.

    >[AZURE.NOTE] Es wird empfohlen, Windows Update an Microsoft Update umzuleiten, da Microsoft Update Sicherheit und wichtige Updates für Windows und andere Produkte wie Microsoft Azure Backup Server bietet.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

7. Überprüfen Sie die *Zusammenfassung der Einstellungen*, und klicken Sie auf **Installieren**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

8. Die Installation erfolgt in mehreren Phasen. In der ersten Phase wird der Microsoft Azure Recovery Services-Agent auf dem Server installiert. Der Assistent überprüft auch das Vorhandensein der Internetverbindung. Wenn eine Internetverbindung verfügbar ist, können Sie mit der Installation fortfahren, andernfalls müssen Sie Proxydetails angeben, um die Verbindung mit dem Internet herzustellen.

    Der nächste Schritt ist die Konfiguration des Microsoft Azure Recovery Services-Agents. Bei der Konfiguration müssen Sie Ihre Tresoranmeldeinformationen zum Registrieren des Computers für den Sicherungstresor angeben. Sie geben auch eine Passphrase zum Verschlüsseln bzw. Entschlüsseln von Daten an, die zwischen Azure und Ihrem lokalen Standort ausgetauscht werden. Sie können eine automatische Passphrase generieren oder eine eigene Passphrase mit mindestens 16 Zeichen angeben. Fahren Sie mit dem Assistenten fort, bis der Agent konfiguriert wurde.

    ![Voraussetzungen 2 für Azure Backup Server](./media/backup-azure-microsoft-azure-backup/mars/04.png)

9. Nach Abschluss der Registrierung von Microsoft Azure Backup Server fährt der übergreifende Setup-Assistent mit der Installation und Konfiguration von SQL Server und der Azure Backup Server-Komponenten fort. Nachdem die Installation der SQL Server-Komponenten abgeschlossen ist, werden die Azure Backup Server-Komponenten installiert.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)


Wenn der Installationsschritt abgeschlossen ist, wurden auch die Desktopsymbole des Produkts bereits erstellt. Doppelklicken Sie einfach auf das Symbol, um das Produkt zu starten.

### Hinzufügen von Backup Storage

Die erste Sicherungskopie wird in einem Speicherbereich vorgehalten, der dem Azure Backup Server-Computer zugeordnet ist. Weitere Informationen zum Hinzufügen von Datenträgern finden Sie unter [Konfigurieren von Speicherpools und Datenträgerspeicher](https://technet.microsoft.com/library/hh758075.aspx).

> [AZURE.NOTE] Sie müssen auch dann Backup Storage hinzufügen, wenn Sie Daten an Azure senden möchten. Bei der aktuellen Architektur von Azure Backup Server enthält der Azure Backup-Tresor die *zweite* Kopie der Daten, und der lokale Speicher enthält die erste (obligatorische) Sicherungskopie.

## 4\. Netzwerkverbindung

![Schritt 4](./media/backup-azure-microsoft-azure-backup/step4.png)

Azure Backup Server muss mit dem Azure Backup-Dienst verbunden sein, um erfolgreich ausgeführt werden zu können. Verwenden Sie zum Überprüfen, ob der Computer über eine Verbindung mit Azure verfügt, das Cmdlet ```Get-DPMCloudConnection``` in der Azure Backup Server-PowerShell-Konsole. Wenn die Ausgabe des Cmdlets „TRUE“ lautet, besteht eine Verbindung. Andernfalls besteht keine Verbindung.

Gleichzeitig muss das Azure-Abonnement einen fehlerfreien Zustand aufweisen. Um den Status Ihres Abonnements zu ermitteln und es zu verwalten, melden Sie sich beim [Abonnementportal](https://account.windowsazure.com/Subscriptions) an.

Nachdem Sie den Status der Azure-Verbindung und des Azure-Abonnements kennen, können Sie anhand der Tabelle unten ermitteln, welche Auswirkungen mit einer Sicherungs-/Wiederherstellungsfunktion verbunden sind.

| Verbindungszustand | Azure-Abonnement | Sicherung auf Azure| Sicherung auf einen Datenträger | Wiederherstellung von Azure | Wiederherstellung von einem Datenträger |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Verbunden | Aktiv | Zulässig | Zulässig | Zulässig | Zulässig |
| Verbunden | Abgelaufen | Beendet | Beendet | Zulässig | Zulässig |
| Verbunden | Bereitstellung aufgehoben | Beendet | Beendet | Beendet und Azure-Wiederherstellungspunkte gelöscht | Beendet |
| Verbindung vor mehr als 15 Tagen verloren | Aktiv | Beendet | Beendet | Zulässig | Zulässig |
| Verbindung vor mehr als 15 Tagen verloren | Abgelaufen | Beendet | Beendet | Zulässig | Zulässig |
| Verbindung vor mehr als 15 Tagen verloren | Bereitstellung aufgehoben | Beendet | Beendet | Beendet und Azure-Wiederherstellungspunkte gelöscht | Beendet |

### Wiederherstellung nach Verbindungsverlust
Wenn Sie über eine Firewall oder einen Proxy verfügen, die bzw. der den Zugriff auf Azure verhindert, müssen Sie im Profil der Firewall bzw. des Proxys die folgenden Domänenadressen auf die Positivliste setzen:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

Nach dem Wiederherstellen der Verbindung mit Azure für den Azure Backup Server-Computer wird anhand des Azure-Abonnementstatus ermittelt, welche Vorgänge durchgeführt werden können. Die obige Tabelle enthält Details zu den Vorgängen, die nach dem „Verbinden“ des Computers zulässig sind.

### Behandeln von Abonnementstatus

Es ist möglich, ein Azure-Abonnement von *Abgelaufen* oder *Bereitstellung aufgehoben* in den Status *Aktiv* zu ändern. Dies ist aber mit Auswirkungen auf das Produktverhalten verbunden, solange der Status nicht *Aktiv* lautet:

- Ein Abonnement mit dem Status *Bereitstellung aufgehoben* verliert für den Zeitraum der Aufhebung die Funktionalität. Beim Festlegen auf *Aktiv* wird die Produktfunktionalität für Sicherung/Wiederherstellung wieder aktiviert. Die Sicherungsdaten auf der lokalen Festplatte können auch abgerufen werden, sofern sie mit einer ausreichend langen Beibehaltungsdauer versehen sind. Die Sicherungsdaten in Azure gehen aber unwiederbringlich verloren, wenn das Abonnement in den Status *Bereitstellung aufgehoben* versetzt wird.
- Für ein Abonnement mit dem Status *Abgelaufen* geht die Funktionalität nur so lange verloren, bis es wieder in den Status *Aktiv* versetzt wird. Alle Sicherungen für den Zeitraum, in dem sich das Abonnement im Status *Abgelaufen* befindet, werden nicht ausgeführt.


## Problembehandlung

Wenn auf dem Microsoft Azure Backup-Server während der Installationsphase (oder bei der Sicherung oder der Wiederherstellung) Fehler auftreten, verwenden Sie dieses [Dokument mit Fehlercodes](https://support.microsoft.com/kb/3041338), um weitere Informationen zu erhalten. Sie können auch [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md) nutzen.


## Nächste Schritte

Ausführliche Informationen zum [Vorbereiten der Umgebung für DPM](https://technet.microsoft.com/library/hh758176.aspx) finden Sie auf der Microsoft TechNet-Website. Sie finden dort auch Informationen zu den unterstützten Konfigurationen, unter denen Azure Backup Server bereitgestellt und verwendet werden kann.

In den folgenden Artikeln finden Sie zusätzliche Informationen zum Workloadschutz mit einem Microsoft Azure Backup-Server.

- [SQL Server-Sicherung](backup-azure-backup-sql.md)
- [SharePoint Server-Sicherung](backup-azure-backup-sharepoint.md)
- [Sicherung eines anderen Servers](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0302_2016-->