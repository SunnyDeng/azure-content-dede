<properties
	pageTitle="Sichern eines Exchange-Servers unter Azure Backup mit System Center 2012 R2 DPM | Microsoft Azure"
	description="Erfahren Sie, wie Sie einen Exchange-Server unter Azure Backup mit System Center 2012 R2 DPM sichern."
	services="backup"
	documentationCenter=""
	authors="AnuragMehrotra"
	manager="shivamg"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="anuragm;jimpark;delhan"/>


# Sichern eines Exchange-Servers unter Azure Backup mit System Center 2012 R2 DPM
In diesem Artikel wird beschrieben, wie Sie einen System Center 2012 R2 Data Protection Manager-Server (DPM) konfigurieren, um einen Microsoft Exchange-Server unter Azure Backup zu sichern.

## Aktualisierungen
Zum erfolgreichen Registrieren des DPM-Servers für Azure Backup müssen Sie das aktuelle Updaterollup für System Center 2012 R2 DPM und die aktuelle Version des Azure Backup-Agents installieren. Sie finden das aktuelle Updaterollup im [Microsoft-Katalog](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

>[AZURE.NOTE] Für die Beispiele in diesem Artikel wurde Version 2.0.8719.0 des Azure Backup-Agents installiert, und das Updaterollup 6 wurde unter System Center 2012 R2 DPM installiert.

## Voraussetzungen
Vergewissern Sie sich zunächst, dass für den Schutz von Workloads mit Microsoft Azure Backup alle [Voraussetzungen](backup-azure-dpm-introduction.md#prerequisites) erfüllt sind. Zu diesen Voraussetzungen gehört Folgendes:

- Auf der Azure-Website wurde ein Sicherungstresor erstellt.
- Die Anmeldeinformationen für den Agent und den Tresor wurden auf den DPM-Server heruntergeladen.
- Der Agent wurde auf dem DPM-Server installiert.
- Die Anmeldeinformationen für den Tresor wurden verwendet, um den DPM-Server zu registrieren.

## DPM-Schutz-Agent  
Führen Sie die folgenden Schritte aus, um den DPM-Schutz-Agent auf dem Exchange-Server zu installieren:

1. Stellen Sie sicher, dass die Firewalls richtig konfiguriert sind. Weitere Informationen finden Sie unter [Konfigurieren von Firewallausnahmen für den Agent](https://technet.microsoft.com/library/Hh758204.aspx).

2. Installieren Sie den Agent auf dem Exchange-Server, indem Sie in der DPM-Administratorkonsole auf **Verwaltung > Agents > Installieren** klicken. Ausführliche Schritte finden Sie unter [Installieren des DPM-Schutz-Agents](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396).

## Erstellen einer Schutzgruppe für den Exchange-Server

1. Klicken Sie in der DPM-Administratorkonsole auf **Schutz** und dann im Menüband des Tools auf **Neu**, um den Assistenten **Neue Schutzgruppe erstellen** zu öffnen.

2. Klicken Sie im Assistenten auf dem Bildschirm **Willkommen** auf **Weiter**.

3. Wählen Sie auf dem Bildschirm **Schutzgruppentyp auswählen** die Option **Server**, und klicken Sie auf **Weiter**.

4. Wählen Sie die Datenbank des Exchange Servers aus, die Sie schützen möchten, und klicken Sie auf **Weiter**.

    >[AZURE.NOTE] Wenn Sie Exchange 2013 schützen, aktivieren Sie die [Exchange 2013-Voraussetzungen](https://technet.microsoft.com/library/dn751029.aspx).

    Im folgenden Beispiel wird die Exchange 2010-Datenbank ausgewählt.

    ![Gruppenmitglieder auswählen](./media/backup-azure-backup-exchange-server/select-group-members.png)

5. Wählen Sie die Methoden zum Schützen der Daten aus.

    Geben Sie der Schutzgruppe einen Namen, und wählen Sie dann die beiden folgenden Optionen aus:

    - Kurzfristiger Schutz unter Verwendung eines Datenträgers
    - Onlineschutz

6. Klicken Sie auf **Weiter**.

7. Wählen Sie die Option **„Eseutil“ zum Prüfen der Datenintegrität ausführen** aus, wenn Sie die Integrität der Exchange-Server-Datenbanken überprüfen möchten.

    Nach dem Auswählen dieser Option wird für den DPM-Server die Backupkonsistenz überprüft, um den generierten E/A-Datenverkehr zu vermeiden, indem der Befehl **eseutil** auf dem Exchange-Server ausgeführt wird.

    >[AZURE.NOTE] Zum Verwenden dieser Option müssen Sie die Dateien „Ese.dll“ und „Eseutil.exe“ auf dem DPM-Server in das Verzeichnis „C:\\Programme\\Microsoft System Center 2012 R2\\DPM\\DPM\\bin“ kopieren. Andernfalls wird der folgende Fehler ausgelöst: ![eseutil-Fehler](./media/backup-azure-backup-exchange-server/eseutil-error.png)

8. Klicken Sie auf **Weiter**.

9. Wählen Sie die Datenbank für **Kopiesicherung** aus, und klicken Sie auf **Weiter**.

    >[AZURE.NOTE] Wenn Sie nicht für mindestens eine DAG-Kopie einer Datenbank die Option „Vollständige Sicherung“ auswählen, werden die Protokolle nicht abgeschnitten.

10. Konfigurieren Sie die Ziele für **Kurzfristige Sicherung**, und klicken Sie auf **Weiter**.

11. Überprüfen Sie den verfügbaren Speicherplatz, und klicken Sie auf **Weiter**.

12. Wählen Sie den Zeitpunkt aus, an dem der DPM-Server die erste Replikation erstellt, und klicken Sie auf **Weiter**.

13. Wählen Sie die Optionen für die Konsistenzüberprüfung aus, und klicken Sie auf **Weiter**.

14. Wählen Sie die Datenbank aus, die Sie unter Azure sichern möchten, und klicken Sie auf **Weiter**. Beispiel:

    ![Onlineschutzdaten angeben](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)

15. Definieren Sie den Zeitplan für **Azure Backup**, und klicken Sie auf **Weiter**. Beispiel:

    ![Zeitplan für Onlinesicherung angeben](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    >[AZURE.NOTE] Beachten Sie, dass die Online-Wiederherstellungspunkte auf den Wiederherstellungspunkten für die schnelle vollständige Wiederherstellung basieren. Daher müssen Sie den Online-Wiederherstellungspunkt nach dem Zeitpunkt einplanen, der für den Wiederherstellungspunkt für die schnelle vollständige Wiederherstellung angegeben wird.

16. Konfigurieren Sie die Aufbewahrungsrichtlinie für **Azure Backup**, und klicken Sie auf **Weiter**.

17. Wählen Sie eine Option für die Onlinereplikation aus, und klicken Sie auf **Weiter**.

    Wenn Sie über eine große Datenbank verfügen, kann es lange dauern, bis das erste Backup über das Netzwerk erstellt wird. Sie können ein Offlinebackup erstellen, um dieses Problem zu vermeiden.

    ![Online-Aufbewahrungsrichtlinie angeben](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)

18. Bestätigen Sie die Einstellungen, und klicken Sie anschließend auf **Gruppe erstellen**.

19. Klicken Sie auf **Schließen**.

## Wiederherstellen der Exchange-Datenbank

1. Klicken Sie zum Wiederherstellen einer Exchange-Datenbank in der DPM-Administratorkonsole auf **Wiederherstellung**.

2. Suchen Sie nach der Exchange-Datenbank, die Sie wiederherstellen möchten.

3. Wählen Sie in der Dropdownliste *Wiederherstellungszeit* einen Online-Wiederherstellungspunkt aus.

4. Klicken Sie auf **Wiederherstellen**, um den **Wiederherstellungs-Assistenten** zu starten.

Für Online-Wiederherstellungspunkte gibt es fünf Wiederherstellungstypen:

- **Am ursprünglichen Exchange Server-Speicherort wiederherstellen:** Die Daten werden auf dem ursprünglichen Exchange-Server wiederhergestellt.
- **In einer anderen Datenbank auf einem Exchange-Server wiederherstellen:** Die Daten werden in einer anderen Datenbank auf einem anderen Exchange-Server wiederhergestellt.
- **Zu einer Wiederherstellungsdatenbank wiederherstellen:** Die Daten werden in einer Exchange-Wiederherstellungsdatenbank wiederhergestellt.
- **In einen Netzwerkordner kopieren:** Die Daten werden in einem Netzwerkordner wiederhergestellt.
- **Auf Band kopieren:** Wenn Sie eine Bandbibliothek oder ein eigenständiges Bandlaufwerk angeschlossen und auf dem DPM-Server konfiguriert haben, wird der Wiederherstellungspunkt auf ein freies Band kopiert.

    ![Onlinereplikation auswählen](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## Nächste Schritte

- [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0316_2016-->