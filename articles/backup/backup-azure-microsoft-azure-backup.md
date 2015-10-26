<properties
  pageTitle="Vorbereiten der Sicherung von Workloads in Microsoft Azure | Microsoft Azure"
  description="Dieser Artikel bietet einen Überblick über die Funktionen für Anwendungsworkloads in Microsoft Azure Backup"
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/07/2015"
  ms.author="sammehta; jimpark"/>

# Vorbereiten der Sicherung von Workloads in Microsoft Azure

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup](backup-azure-microsoft-azure-backup.md)

Dieser Artikel bietet Informationen zum Vorbereiten Ihrer Umgebung für das Sichern von Workloads, damit Sie Ihre Daten in Azure sichern können. Mit einem Microsoft Azure Backup-Server können Sie Anwendungsworkloads wie Hyper-V-VMs, Microsoft SQL Server, SharePoint Server, Microsoft Exchange und Windows-Clients wie folgt schützen:

- **Datenträger** (D2D): Bietet hohe RTOs für Workloads der Ebene 1
- **Azure** (D2D2C): Für eine langfristige Aufbewahrung

Darüber hinaus können Sie den Schutz der verschiedenen geschützten Entitäten (Server und Clients) über eine einzelne lokale Benutzeroberfläche verwalten.

>[AZURE.NOTE]Der Microsoft Azure Backup-Server erbt die Funktionalität des Data Protection Manager (DPM) für die Workload-Sicherung. Sie finden bei einigen dieser Funktionen Verweise auf die DPM-Dokumentation. Microsoft Azure Backup Server stellt jedoch keinen Schutz für Bandspeicher bereit und kann nicht in System Center integriert werden.

Sie können einen Microsoft Azure Backup-Server wie folgt bereitstellen:

- Als physischen eigenständigen Server.
- Als virtuellen Hyper-V-Computer: Sie können einen Microsoft Azure Backup-Server als virtuellen Computer ausführen, der auf einem lokalen Hyper-V-Hostserver gehostet wird, um lokale Daten zu sichern. Eine Liste der Überlegungen in dieser Umgebung finden Sie unter [Installieren von DPM als virtuellen Computer auf einem lokalen Hyper-V-Server](https://technet.microsoft.com/library/dn440941.aspx).
- Als virtuellen Windows-Computer in VMWare: Sie können einen Microsoft Azure Backup-Server zum Schutz von Microsoft-Workloads bereitstellen, die auf virtuellen Windows-Computern in VMWare ausgeführt werden. In diesem Szenario kann der Microsoft Azure Backup-Server als physischer eigenständiger Server, als virtueller Hyper-V-Computer oder als virtueller Windows-Computer in VMWare bereitgestellt werden.
- Als virtueller Azure-Computer: Sie können den Microsoft Azure Backup-Server als virtuellen Computer in Azure ausführen, um Cloudworkloads zu sichern, die als virtuelle Azure-Computer ausgeführt werden. Weitere Informationen zu dieser Bereitstellung finden Sie unter [Installieren von DPM als virtuellen Azure-Computer](https://technet.microsoft.com/library/hh758176.aspx).

Weitere Informationen:

- Microsoft Azure Backup kann unter Windows Server 2008 R2 SP1, 2012 und 2012 R2 installiert werden.
- Der Microsoft Azure Backup-Server kann nicht auf einem Computer installiert werden, auf dem der SCDPM- oder der SCDPM RA-Agent installiert ist.
- Der Microsoft Azure Backup-Server kann nicht auf einem Computer installiert werden, auf dem der Microsoft Azure Backup-Agent installiert ist und der in einem Azure Backup-Tresor registriert wurde.



Nachdem Sie den Server ausgewählt haben, auf dem Sie Microsoft Azure Backup installieren möchten, müssen Sie folgende Schritte ausführen:

- Sicherstellen, dass die Voraussetzungen für den Microsoft Azure Backup-Server erfüllt sind
- Erstellen eines Sicherungstresors
- Herunterladen der Tresoranmeldedaten
- Herunterladen der Setupdateien für den Microsoft Azure Backup-Server

Jeder dieser Schritte wird nachfolgend ausführlich erläutert.

## Vorbereitung

Der Microsoft Azure Backup-Server ist heute nur für das englische Gebietsschema verfügbar. Derzeit ist Microsoft Azure Recovery Services in allen Regionen verfügbar, in denen Azure verfügbar ist, abgesehen vom Microsoft Azure-Datencenter für Behörden und Microsoft Azure in China.

Stellen Sie für eine reibungslose Installation sicher, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie Microsoft Azure Backup installieren.

- Der Server sollte über eine Verbindung mit dem Internet verfügen. Der Zugriff auf Microsoft Azure sollte durch den Server möglich sein.
- Die Serveranforderungen für die Installation von Microsoft Azure Backup sind identisch mit denen für DPM. Weitere Informationen finden Sie in den [Hardwareanforderungen](https://technet.microsoft.com/library/dn726764.aspx).
- Der Microsoft Azure Backup-Server muss zu einer Domäne gehören.
- Auf dem Microsoft Azure Backup-Server müssen die Funktionen von .NET  3.5, .NET 4.0 und .NET 3.5 SP1 installiert sein. Weitere Informationen finden Sie unter [Weitere Informationen zum Aktivieren von .NET Framework](https://technet.microsoft.com/library/dn482071.aspx).
- Auf dem Microsoft Azure Backup-Server sollte Windows Management Framework 4.0 installiert sein. Sie können es [hier](http://www.microsoft.com/download/details.aspx?id=40855) herunterladen.
- Für den Datenträger, der als dedizierter Speicherplatz für den Datenspeicher für den Microsoft Azure Backup-Server verwendet wird, ist die empfohlene Speicherpoolgröße das 1,5-fache der Größe der geschützten Daten. Weitere Informationen finden Sie im Abschnitt zu Datenträgern und Speicher in [diesem Thema](https://technet.microsoft.com/library/hh758176.aspx#DPM-Server).

Bereiten Sie den Microsoft Azure Backup-Server mit folgenden Schritten zum Sichern von Daten vor:

1. **Erstellen eines Sicherungstresors** – Erstellen Sie einen Tresor in der Azure Backup-Konsole.
2. **Herunterladen der Tresoranmeldedaten** – Laden Sie in Azure Backup das Verwaltungszertifikat hoch, das Sie für den Tresor erstellt haben.
3. **Herunterladen des Microsoft Azure Backup-Servers** – Sie können den Microsoft Azure Backup-Server für Anwendungsworkloads von der Schnellstartseite eines Sicherungstresors herunterladen.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

## Herunterladen des Microsoft Azure Backup-Servers
Ähnlich wie die Tresoranmeldedaten können Sie den Microsoft Azure Backup-Server für Anwendungsworkloads von der **Schnellstartseite** des Sicherungstresors herunterladen.

1. Klicken Sie auf **Für Anwendungsworkloads (Datenträger zu Datenträger zu Cloud)**.

    ![Willkommensseite von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. Klicken Sie auf **Download**.

    ![Download Center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. Wählen Sie alle Dateien aus, und klicken Sie auf **Weiter**. ![Download Center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE]Da die Downloadgröße aller Dateien zusammen mehr als 3 G beträgt, dauert es über einen Downloadlink mit 10 MBit/s bis zu 60 Minuten, bis der Download abgeschlossen ist.

4. Speichern Sie alle Dateien im gleichen Ordner.

5. Laden Sie alle Dateien von der Microsoft Azure Backup-Downloadseite herunter.

## Installieren des Microsoft Azure Backup-Servers
Stellen Sie sicher, dass die im vorherigen Abschnitt genannten Voraussetzungen erfüllt sind, bevor Sie das Setup starten.

1. Nachdem Sie alle Dateien heruntergeladen haben, klicken Sie auf **MicrosoftAzureBackupInstaller.exe**. Der **Setup-Assistent von Microsoft Azure Backup** wird angezeigt.

    ![Installationsprogramm von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. Geben Sie den Pfad auf dem Server an, in dem die Dateien extrahiert werden sollen, und klicken Sie auf **Weiter**.

    ![Setup-Assistent von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. Klicken Sie auf **Extrahieren**, um mit dem Extrahieren der Setupdateien zu beginnen.

    ![Setup-Assistent von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. Aktivieren Sie das Kontrollkästchen zum Starten von "setup.exe", um mit der Installation des Microsoft Azure Backup-Servers zu beginnen, und klicken Sie auf **Fertig stellen**.

    ![Setup-Assistent von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. Klicken Sie auf **Microsoft Azure Backup**, um den Setup-Assistenten zu starten.

    ![Setup-Assistent von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. Klicken Sie auf der Willkommensseite auf **Weiter**.

    ![Willkommensseite von Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. Klicken Sie auf **Überprüfen**, um zu bestimmen, ob die Hardware- und Softwarevoraussetzungen für den Microsoft Azure Backup-Server erfüllt sind.

    ![Voraussetzungen 1 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. Wenn alle Voraussetzungen erfolgreich erfüllt wurden, sehen Sie eine Meldung, die darauf hinweist, dass der Computer die Anforderungen erfüllt. Klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. Der Microsoft Azure Backup-Server erfordert SQL Server Standard. Wählen Sie eine vorhandene lokale SQL Server Standard-Instanz der Version 2014 aus, oder lassen Sie den Assistenten SQL Server Standard installieren. Klicken Sie auf **Prüfen und installieren**, um sicherzustellen, dass die erforderlichen SQL-Komponenten auf dem Server installiert sind.

    ![SQL1 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    Tritt ein Fehler mit der Empfehlung auf, den Computer neu zu starten, tun Sie dies, und klicken Sie auf **Erneut prüfen**.

    ![Fehler des SiS-Filters](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. Nachdem die erforderlichen Komponenten erfolgreich installiert wurden, klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. Geben Sie einen Speicherort für die Installation der Microsoft Azure Backup-Serverdateien an, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE]Ein Scratchverzeichnis ist eine Anforderung für die Sicherung in Azure. Stellen Sie sicher, dass das Scratchverzeichnis eine Größe von mindestens 5 % der Daten aufweist, die in der Cloud gesichert werden. Für den Datenträgerschutz müssen separate Datenträger nach Abschluss der Installation konfiguriert werden. Weitere Informationen zu Speicherpools finden Sie unter [Konfigurieren von Speicherpools und Datenträgerspeicher](https://technet.microsoft.com/library/hh758075.aspx).

12. Geben Sie ein sicheres Kennwort für eingeschränkte lokale Benutzerkonten an, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE]Das Festlegen von sicheren Kennwörtern ist unerlässlich für die Sicherheit Ihres Systems. Als sicheres Kennwort gilt ein Kennwort, das mindestens sechs Zeichen lang ist, das den Kontonamen des Benutzers nicht vollständig oder teilweise enthält und das mindestens drei der folgenden vier Zeichenkategorien aufweist: Großbuchstaben, Kleinbuchstaben, Ziffern (0 bis 9) und Symbole (wie !, @, #).

13. Bestimmen Sie, ob Sie mit *Microsoft Update* nach Updates suchen möchten, und klicken Sie auf **Weiter**.

    >[AZURE.NOTE]Es wird empfohlen, Windows Update an Microsoft Update umzuleiten, da Microsoft Update Sicherheit und wichtige Updates für Windows und andere Produkte wie den Microsoft Azure Backup-Server bietet.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. Überprüfen Sie die *Zusammenfassung der Einstellungen*, und klicken Sie auf **Installieren**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    Die Installation erfolgt in mehreren Phasen. In der ersten Phase wird ein Agent auf dem Server installiert, und Sie müssen den Server mit den gültigen Azure Backup-Tresoranmeldedaten registrieren, die Sie mit diesem Produkt heruntergeladen haben. Der Assistent sucht dann nach einer direkten Internetverbindung. Wenn eine Internetverbindung verfügbar ist, können Sie mit der Installation fortfahren, andernfalls müssen Sie Proxydetails angeben, um die Verbindung mit dem Internet herzustellen.

15. Klicken Sie auf **Weiter**, um die Installationsphase des Microsoft Azure Recovery Services-Agents zu starten.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/proxy.png)

    Der Assistent überprüft die erforderlichen Komponenten und installiert ggf.fehlende.

16. Klicken Sie auf **Installieren**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. Klicken Sie nach Abschluss der Installation des Agents auf **Weiter**, um diesen Server beim Azure Backup-Tresor zu registrieren.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. Geben Sie Ihre Azure Backup-Tresoranmeldedaten an, um den Server zu registrieren, und klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. Geben Sie eine Passphrase zum Verschlüsseln/Entschlüsseln von Daten an, die an Azure gesendet werden, und geben Sie einen Speicherort zum Speichern dieser Passphrase an. Sie können eine automatische Passphrase generieren oder eine eigene Passphrase mit mindestens 16 Zeichen angeben. Klicken Sie auf **Weiter**.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE]Um die Vertraulichkeit zu wahren, sendet der Microsoft Azure Backup-Server diese Passphrase nicht zusammen mit Daten an Azure. Sie müssen diese Passphrase an einem sicheren Ort speichern. Sie wird beim Wiederherstellen von Daten von Azure benötigt.

    Nachdem die Registrierung des Microsoft Azure Backup-Servers erfolgreich abgeschlossen wurde, wird die Installation mit der Installation und Konfiguration von SQL Server Standard 2014 (falls ausgewählt) fortgesetzt.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    Nach Abschluss der Installation von SQL sind die Microsoft Azure Backup-Serverkomponenten installiert.

    ![Voraussetzungen 2 für Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. Wenn das Produkt installiert ist und die Desktopsymbole erstellt wurden, doppelklicken Sie auf das Symbol, um das Produkt zu starten.

    ![Symbole](./media/backup-azure-microsoft-azure-backup/icons.png)

## Hinzufügen eines Datenträgers zum Speicherpool

Um Workloads auf Datenträgern und in Azure zu sichern, müssen Sie zuerst einen Datenträger zum Speicherpool hinzufügen. Weitere Informationen zum Hinzufügen von Datenträgern finden Sie unter [Konfigurieren von Speicherpools und Datenträgerspeicher](https://technet.microsoft.com/library/hh758075.aspx).

Nach der Konfiguration der Speicherpools können Sie Anwendungsworkloads auf Datenträgern und in Azure sichern.

## Problembehandlung

Wenn auf dem Microsoft Azure Backup-Server während der Installationsphase (oder bei der Sicherung oder der Wiederherstellung) Fehler auftreten, verwenden Sie dieses [Dokument mit Fehlercodes](https://support.microsoft.com/kb/3041338), um weitere Informationen zu erhalten. Sie können auch [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md) nutzen.

## Häufig gestellte Fragen

### Installation und Bereitstellung

F: Wird die NTFS-Komprimierung auf Microsoft Azure Backup-Serverdatenträgern/-volumes unterstützt, die für Datenträgersicherungen verwendet werden? <br>A: NTFS wird auf mit dem Microsoft Azure Backup-Server verbundenen Datenträgern/Volumes **nicht** unterstützt.

F: Kann ich den Microsoft Azure Backup-Server nach der Bereitstellung in eine neue Domäne verschieben? <br>A: Nein, das Verschieben des Microsoft Azure Backup-Servers nach der Bereitstellung in eine neue Domäne wird **nicht** unterstützt.

F: Welche Ressourcen kann ein Microsoft Azure Backup-Server schützen, wenn er auf einem Domänencontroller installiert wird? <br>A: Ein Microsoft Azure Backup-Server kann nur lokale Datenquellen schützen, wenn er auf demselben Server wie ein Domänencontroller installiert ist. Damit Microsoft Azure Backup-Server Workloads auf anderen Servern schützt, installieren Sie ihn nicht auf demselben Computer wie den Domänencontroller.

F: Kann ein Microsoft Azure Backup-Server eine Remoteinstanz von SQL Server verwenden? <br>A: Nein, ein Microsoft Azure Backup-Server kann nur eine lokale Instanz von SQL Server verwenden.

### Speicherpool des Microsoft Azure Backup-Servers

F: Kann ich einen virtuellen Datenträger (VHD/HDx) im Speicherpool des Microsoft Azure Backup-Servers bereitstellen? <br>A: Ja.

F: Kann ich Deduplizierung im Speicherpool des Microsoft Azure Backup-Servers nutzen? <br>A: Ja, Deduplizierung ist für einen Speicherpool des Microsoft Azure Backup-Servers verfügbar? Die Benutzerfunktionalität für den Microsoft Azure Backup-Server entspricht genau der, die in diesem [Blogbeitrag zu DPM](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx) (in englischer Sprache) beschrieben ist.

F: Können USB- oder Wechseldatenträger für einen Speicherpool des Microsoft Azure Backup-Servers verwendet werden? <br>A: Nein.

### Verbindung des Microsoft Azure Backup-Servers mit dem Azure-Dienst

F: Welchen Einfluss haben die Microsoft Azure Backup-Serververbindung mit dem Azure-Dienst und der Azure-Abonnementzustand auf Sicherungen und Wiederherstellungen? <br>A: Ein Microsoft Azure Backup-Server muss mit dem Azure-Dienst verbunden sein, damit er erfolgreich ausgeführt wird. Gleichzeitig muss das Azure-Abonnement einen fehlerfreien Zustand aufweisen.

In der folgenden Tabelle werden die Funktionen eines Microsoft Azure Backup-Servers in Bezug auf unterschiedliche Verbindungs- und Azure-Abonnementzustände erläutert.

| Verbindungszustand | Azure-Abonnement | Sicherung auf Azure| Sicherung auf einen Datenträger | Wiederherstellung von Azure | Wiederherstellung von einem Datenträger |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Verbunden | Aktiv | Zulässig | Zulässig | Zulässig | Zulässig |
| Verbunden | Abgelaufen | Beendet | Beendet | Zulässig | Zulässig |
| Normal | Bereitstellung aufgehoben | Beendet | Beendet | Beendet und Azure-Wiederherstellungspunkte gelöscht | Beendet |
| Verbindung vor mehr als 15 Tagen verloren | Aktiv | Beendet | Beendet | Zulässig | Zulässig |
| Verbindung vor mehr als 15 Tagen verloren | Abgelaufen | Beendet | Beendet | Zulässig | Zulässig |
| Verbindung vor mehr als 15 Tagen verloren | Bereitstellung aufgehoben | Beendet | Beendet | Beendet und Azure-Wiederherstellungspunkte gelöscht | Beendet |

F: Angenommen, der Abonnementzustand bleibt *Aktiv*, was geschieht, wenn die Microsoft Azure Backup-Serververbindung wiederhergestellt wird? <br>A: Wenn die Microsoft Azure Backup-Serververbindung mit dem Azure-Dienst normal fortgesetzt wird und der Abonnementzustand *Aktiv* ist, sind alle Vorgänge des Microsoft Azure Backup-Servers zulässig. Sicherungen auf den Datenträger und in Azure werden normal ausgeführt.

F: Was geschieht, wenn der Abonnementzustand von *Abgelaufen* wieder in *Aktiv* geändert wird? <br>A: Angenommen, die Microsoft Azure Backup-Serververbindung mit dem Azure-Dienst ist normal, dann sind alle Vorgänge des Microsoft Azure Backup-Servers zulässig, sobald der Abonnementzustand wieder *Aktiv* ist. Sicherungen auf den Datenträger und in Azure werden jetzt normal ausgeführt.

F: Was geschieht, wenn der Abonnementzustand von *Bereitstellung aufgehoben* wieder in *Aktiv* geändert wird? <br>A: Angenommen, die Microsoft Azure Backup-Serververbindung mit dem Azure-Dienst ist normal, dann sind alle Vorgänge des Microsoft Azure Backup-Servers zulässig, sobald der Abonnementzustand wieder *Aktiv* ist. Allerdings werden Azure-Wiederherstellungspunkte gelöscht und können nicht wiederhergestellt werden. Wiederherstellungspunkte von Datenträgern können wiederhergestellt werden, wenn die Datenträgersicherungen eine gültige Beibehaltungsdauer aufweisen.

F: Welche Ausnahmen müssen erstellt werden, damit eine Microsoft Azure Backup-Serververbindung mit einem öffentlichen Azure-Dienst zulässig ist? <br>A: Sie müssen die folgenden Domänenadressen im Firewallprofil zulassen: www.msftncsi.com, *.Microsoft.com, *.WindowsAzure.com, *.microsoftonline.com, *.windows.net.

F: Wie kann ich überprüfen, ob der Microsoft Azure Backup-Server mit der Azure-Dienst verbunden ist? <br>A: Führen Sie das folgende PowerShell-Cmdlet in der Konsole des Microsoft Azure Backup-Servers aus:

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

F: Warum ist mein Abonnementzustand nicht *Aktiv*? Wie ändere ich ihn in *Aktiv*? <br>A: Melden Sie sich im [Abonnementportal](https://account.windowsazure.com/Subscriptions) an, und verwalten Sie Ihr Abonnement.

### Abrechnung

F: Was sieht das Abrechnungsmodell für Microsoft Azure Backup-Server aus? <br>A: Die Abrechnung der Benutzer erfolgt über ein Modell geschützter Instanzen. Weitere Informationen finden Sie in den häufig gestellten Fragen auf der Seite zu [Preisen](http://azure.microsoft.com/pricing/details/backup/).

F: Wie sieht das Abrechnungsmodell aus, wenn nur Daten auf Datenträgern geschützt werden? <br>A: Das Abrechnungsmodell entspricht dem Modell geschützter Instanzen. Da diese Daten im lokalen Speicher geschützt werden, fallen bei reinen datenträgerbasierten Sicherungen keine Gebühren für den Azure-Speicher an. In diesem Fall würde dem Kunden nur die Gebühr für die geschützte Instanz berechnet werden. Weitere Informationen darüber, wie eine Instanz definiert ist und wie viel pro Instanz berechnet wird, finden Sie in den häufig gestellten Fragen auf der Seite zu [Preisen](http://azure.microsoft.com/pricing/details/backup/).

F: Was ist die Gebühr pro geschützter Instanz? <br>A: Informationen hierzu finden Sie auf der Seite zu [Preisen](http://azure.microsoft.com/pricing/details/backup/).

F: Wo finde ich Beispiele, in denen auf verschiedene Anwendungsworkloads und die dafür anfallenden Gebühren für geschützte Instanzen eingegangen wird? <br>A: Informationen finden Sie im Abschnitt mit Beispielen auf der Seite zu [Preisen](http://azure.microsoft.com/pricing/details/backup/).

F: Wie würde beim Microsoft Azure Backup-Server eine Datenquelle abgerechnet werden, die auf einem Datenträger und in der Cloud geschützt wird? <br>A: Unabhängig davon, ob die Daten auf einem Datenträger oder in der Cloud gesichert werden, erfolgt die Abrechnung beim Microsoft Azure Backup-Server anhand geschützter Instanzen. Die Größe der geschützten Instanz wird basierend auf der Front-End-Größe der Datenquelle berechnet. Für die Sicherungsdaten im Azure-Speicher fallen zudem Kosten für den Azure-Speicher an.

## Nächste Schritte

In den folgenden Artikeln finden Sie zusätzliche Informationen zum Workloadschutz mit einem Microsoft Azure Backup-Server.

- [SQL Server-Sicherung](backup-azure-backup-sql.md)
- [SharePoint Server-Sicherung](backup-azure-backup-sharepoint.md)
- [Sicherung eines anderen Servers](backup-azure-alternate-dpm-server.md)

<!---HONumber=Oct15_HO3-->