<properties
	pageTitle="Azure Backup für SQL-Workloads mit DPM | Microsoft Azure"
	description="Eine Einführung in die Sicherung von SQL-Datenbanken mithilfe des Azure Backup-Diensts."
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/09/2015" ms.author="giridham"; "jimpark"/>


# Azure Backup für SQL-Workloads mit DPM

In diesem Artikel werden Sie durch die Konfiguration von SQL-Sicherungen mithilfe von Azure Backup geleitet. Für die SQL-Sicherung in Azure benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

Die Verwaltung der Sicherung und Wiederherstellung von SQL-Datenbanken umfasst drei Schritte:

1. Erstellen einer Sicherungsrichtlinie zum Schutz von SQL Server-Datenbanken mithilfe von Azure
2. Bedarfsgesteuertes Erstellen von Sicherungskopien in Azure
3. Wiederherstellen der Datenbank aus Azure

## Vorbereitung
Vergewissern Sie sich zunächst, dass für den Schutz von Workloads mit Microsoft Azure Backup alle [Voraussetzungen](../backup-azure-dpm-introduction/#prerequisites) erfüllt sind. Die Voraussetzungen umfassen Aufgaben wie das Erstellen eines Sicherungstresors, das Herunterladen von Tresoranmeldedaten, das Installieren des Azure Backup-Agents und das Registrieren des Servers beim Tresor.

## Erstellen einer Sicherungsrichtlinie zum Schutz von SQL-Datenbanken mithilfe von Azure

1. Konfigurieren Sie auf dem DPM-Server eine neue Sicherungsrichtlinie für SQL-Datenbanken, indem Sie eine neue **Schutzgruppe** erstellen. Klicken Sie auf den Arbeitsbereich **Schutz**.

2. Klicken Sie auf **Neu**, um eine neue Schutzgruppe zu erstellen.

    ![Erstellen einer Schutzgruppe](./media/backup-azure-backup-sql/protection-group.png)

3. DPM zeigt den Startbildschirm mit Hinweisen zum Erstellen einer **Schutzgruppe** an. Klicken Sie auf **Weiter**.

4. Wählen Sie **Server**.

    ![Auswählen des Schutzgruppentyp – "Server"](./media/backup-azure-backup-sql/pg-servers.png)

5. Erweitern Sie die SQL Server-Instanz, auf der die zu sichernden SQL-Datenbanken installiert sind. DPM zeigt verschiedene Datenquellen, die auf diesem Server gesichert werden können. Erweitern Sie **Alle SQL-Freigaben**, und wählen Sie die SQL-Datenbanken aus, die Sie sichern möchten (in diesem Fall wurden die Datenbanken "ReportServer$MSDPM2012" und "ReportServer$MSDPM2012TempDB" ausgewählt). Klicken Sie auf **Weiter**.

    Es wird ein ähnlicher Bildschirm wie der folgende angezeigt.

    ![Auswählen einer SQL-Datenbank](./media/backup-azure-backup-sql/pg-databases.png)

6. Geben Sie den Namen der Schutzgruppe ein, die Sie erstellen möchten. Stellen Sie sicher, dass Sie die Option "Ich möchte Onlineschutz" aktivieren.

    ![Datenschutzmethode – kurzfristig auf Datenträger & online in Azure](./media/backup-azure-backup-sql/pg-name.png)

7. Geben Sie auf dem Bildschirm **Kurzfristige Ziele angeben** alle erforderlichen Informationen ein, um Sicherungspunkte auf dem Datenträger zu erstellen.

    In diesem Beispiel wurde die **Beibehaltungsdauer** auf *5 Tage* und die **Synchronisierungshäufigkeit** auf einmal alle *15 Minuten* festgelegt. Dieser Wert gibt die Häufigkeit der Sicherung an. Der Wert für **Schnelle vollständige Sicherung** ist auf *20:00* festgelegt.

    ![Kurzfristige Ziele](./media/backup-azure-backup-sql/pg-shortterm.png)

    >[AZURE.NOTE]Jeden Tag um 20:00 Uhr wird (in diesem Beispiel) ein Sicherungspunkt erstellt, indem die seit dem Sicherungspunkt am Vortag um 20:00 Uhr geänderten Daten übertragen werden. Dieser Vorgang wird als **Schnelle vollständige Sicherung** bezeichnet. Die SQL-Transaktionsprotokolle werden alle 15 Minuten synchronisiert. Falls um 21:00 Uhr die Datenbank wiederhergestellt werden muss, wird der Punkt erstellt, indem die Protokolle seit dem letzten vollständigen Sicherungspunkt (in diesem Fall 20:00 Uhr) wiedergegeben werden.

8. Klicken Sie auf **Weiter**.

    DPM zeigt den verfügbaren Gesamtspeicherplatz sowie die wahrscheinliche Festplattenspeicherbelegung an.

    ![Datenträgerzuordnung](./media/backup-azure-backup-sql/pg-storage.png)

    >[AZURE.NOTE]DPM erstellt ein Volume pro Datenquelle (SQL-Datenbank) zum Erstellen der anfänglichen Sicherungskopie. Bei diesem Ansatz ist DPM durch die Verwaltung logischer Datenträger auf den Schutz von maximal 300 Datenquellen (SQL-Datenbanken) beschränkt. Um dies zu vermeiden, nutzt DPM einen anderen Ansatz, bei dem ein einziges Volume für mehrere Datenquellen verwendet wird. Dies wird über die Option **Daten im DPM-Speicherpool zusammenstellen** erreicht. Mit diesem Ansatz kann DPM bis zu 2.000 SQL-Datenbanken schützen.

    DPM kann der Anforderung nach größeren Sicherungsvolumes aufgrund steigender Produktionsdaten Rechnung tragen, wenn die Option **Volumes automatisch erweitern** ausgewählt wird. Wenn Sie die Option **Volumes automatisch erweitern** deaktivieren, wird der Sicherungsspeicher zum Sichern der Datenquellen in der Schutzgruppe beschränkt.

9. Administratoren können diese anfängliche Sicherung manuell (nicht über das Netzwerk) übertragen, um nicht zu viel Bandbreite zu belegen, oder die Daten über das Netzwerk senden. Sie können auch konfigurieren, zu welchem Zeitpunkt die anfängliche Übertragung stattfinden kann. Klicken Sie auf **Weiter**.

    ![Methode für die anfängliche Replikation](./media/backup-azure-backup-sql/pg-manual.png)

    >[AZURE.NOTE]Die anfängliche Sicherungskopie erfordert eine Übertragung der gesamten Datenquelle (SQL-Datenbank) vom Produktionsserver (SQL Server) zum DPM-Server. Diese Daten können mitunter sehr umfangreich sein, und ihre Übertragung über das Netzwerk kann zu einer Überschreitung der Bandbreite führen. Deshalb können Administratoren sich diese anfängliche Sicherung entweder **Manuell** durchführen, um eine Netzwerküberlastung zu vermeiden, oder die Daten **Automatisch** über das Netzwerk senden. Wenn Administratoren die Option **Netzwerk** auswählen, können Sie die anfängliche Sicherungskopie entweder **Jetzt** oder **Später** zu einem festgelegten Zeitpunkt durchführen.

    Sobald die anfängliche Sicherung abgeschlossen wurde, werden nur noch inkrementelle Sicherungen erstellt, die die anfängliche Sicherungskopie ergänzen. Diese inkrementellen Sicherungskopien sind im Allgemeinen sehr klein und werden über das Netzwerk übertragen.

10. Wählen Sie aus, wann die Konsistenzprüfung ausgeführt werden soll, und klicken Sie auf **Weiter**.

    ![Konsistenzprüfung](./media/backup-azure-backup-sql/pg-consistent.png)

    >[AZURE.NOTE]DPM kann eine Konsistenzprüfung durchführen, um die Integrität des Sicherungspunkts zu prüfen. Hierbei wird die Prüfsumme der Sicherungsdatei auf dem Produktionsserver (in diesem Szenario SQL Server) und der gesicherten Daten für diese Datei auf dem DPM-Server berechnet. Im Falle eines Konflikts wird davon ausgegangen, dass die gesicherte Datei auf dem DPM-Server beschädigt ist. DPM korrigiert die gesicherten Daten, indem die Datenblöcke gesendet werden, die nicht der Prüfsumme entsprechen. Da die Konsistenzprüfung ein ressourcenintensiver Vorgang ist, haben Administratoren die Möglichkeit, diese zeitlich zu planen oder automatisch auszuführen.

11. Um Onlineschutz für die Datenquellen festzulegen, wählen Sie die Datenbanken aus, die Sie mit Azure schützen möchten. Klicken Sie dann auf **Weiter**.

    ![Auswählen der Datenquellen](./media/backup-azure-backup-sql/pg-sqldatabases.png)

12. Administratoren können Sicherungszeitpläne und Aufbewahrungsrichtlinien auswählen, die den Richtlinien ihrer Organisation entsprechen.

    ![Planung und Aufbewahrung](./media/backup-azure-backup-sql/pg-schedule.png)

    In diesem Beispiel werden Sicherungen einmal täglich um 12:00 Uhr und um 20:00 Uhr ausgeführt (unterer Bildschirmbereich).

    >[AZURE.NOTE]Es ist eine bewährte Methode, einige Wiederherstellungspunkte kurzfristig auf dem Datenträger beizubehalten, um eine schnelle Wiederherstellung zu ermöglichen. Dies wird als "operative Wiederherstellung" bezeichnet. Azure ist dank hoher SLAs und garantierter Verfügbarkeit eine gute Wahl als Offsitestandort. Für Mitte 2015 ist für DPM und Azure Backup ein zusätzliches Feature vorgesehen, mit dem mehrere "Generationen" (Großvater-Vater-Sohn-Prinzip) von Sicherungen angegeben werden können.

    **Best Practice**: Stellen Sie sicher, dass Azure Backup-Sicherungen so geplant sind, dass sie nach Abschluss der lokalen Datenträgersicherungen mithilfe von DPM ausgeführt werden. Auf diese Weise wird sichergestellt, dass die neueste Datenträgersicherung nach Azure kopiert wird.

13. Wählen Sie den Zeitplan für die Aufbewahrungsrichtlinie. Ausführliche Informationen zur Funktionsweise der Aufbewahrungsrichtlinie finden Sie im Artikel [Verwenden von Azure Backup als Ersatz für Ihre Bandinfrastruktur](backup-azure-backup-cloud-as-tape.md).

    ![Aufbewahrungsrichtlinie](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In diesem Beispiel:

    - Sicherungen einmal täglich um 12:00 Uhr und um 20:00 Uhr ausgeführt (unterer Bildschirmbereich) und für 180 Tage beibehalten.
    - Die am Samstag um 12:00 Uhr durchgeführte Sicherung wird für 104 Wochen beibehalten.
    - Die am letzten Samstag um 12:00 Uhr durchgeführte Sicherung wird für 60 Monate beibehalten.
    - Die am letzten Samstag im März um 12:00 Uhr durchgeführte Sicherung wird für 10 Jahre beibehalten.

14. Klicken Sie auf **Weiter**, und wählen Sie die geeignete Option zum Übertragen der anfänglichen Sicherung nach Azure aus. Sie können zwischen den Optionen **Automatisch über das Netzwerk** oder **Offlinesicherung** auswählen.

    - Bei Auswahl von **Automatisch über das Netzwerk** werden die Sicherungsdaten nach dem für die Sicherung ausgewählten Zeitplan nach Azure übertragen.
    - Die Funktionsweise der Option **Offlinesicherung** wird unter [Workflow zur Offlinesicherung in Azure Backup](backup-azure-backup-import-export.md) beschrieben.

    Wählen Sie die geeignete Übertragungsmethode zum Senden der anfänglichen Sicherungskopie an Azure, und klicken Sie auf **Weiter**.

15. Nachdem Sie die Richtliniendetails im Bildschirm **Zusammenfassung** überprüft haben, klicken Sie auf die Schaltfläche **Gruppe erstellen**, um den Workflow abzuschließen. Sie können anschließend auf die Schaltfläche **Schließen** klicken und den Auftragsfortschritt im Arbeitsbereich "Überwachung" verfolgen.

    ![Erstellen der Schutzgruppe – in Bearbeitung](./media/backup-azure-backup-sql/pg-summary.png)

## Bedarfsgesteuerte Sicherung einer SQL-Datenbank
Anhand der zuvor beschriebenen Schritte wurde eine Sicherungsrichtlinie eingerichtet, ein "Wiederherstellungspunkt" wird jedoch erst bei Ausführung der ersten Sicherung erstellt. Statt darauf zu warten, dass die erste Sicherung durch den Scheduler ausgelöst wird, erstellen wir mithilfe der nachstehenden Schritte manuell einen Wiederherstellungspunkt.

1. Warten Sie, bis der Status der Datenbank für die Schutzgruppe als **OK** angezeigt wird, bevor Sie den Wiederherstellungspunkt erstellen.

    ![Schutzgruppenmitglieder](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)

2. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Wiederherstellungspunkt erstellen**.

    ![Erstellen eines Onlinewiederherstellungspunkts](./media/backup-azure-backup-sql/sqlbackup-createrp.png)

3. Wählen Sie in der Dropdownliste die Einstellung **Onlineschutz**, und klicken Sie auf **OK**. Dies löst die Erstellung eines Wiederherstellungspunkts in Azure aus.

    ![Erstellen eines Wiederherstellungspunkts](./media/backup-azure-backup-sql/sqlbackup-azure.png)

4. Sie können den Auftragsfortschritt im Arbeitsbereich **Überwachung** verfolgen. Dort sollte ein in Bearbeitung befindlicher Auftrag angezeigt werden, ähnlich wie im nachstehenden Screenshot.

    ![Konsole für die Überwachung](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## Wiederherstellen einer SQL-Datenbank aus Azure
Die folgenden Schritte sind erforderlich, um eine geschützte Entität (SQL-Datenbank) aus Azure wiederherzustellen.

1. Öffnen Sie die Verwaltungskonsole auf dem DPM-Server. Navigieren Sie zum Arbeitsbereich **Wiederherstellung**, in dem die mit DPM gesicherten Server angezeigt werden. Suchen Sie nach der erforderlichen Datenbank (in diesem Fall "ReportServer$MSDPM2012"). Wählen Sie unter **Wiederherstellung aus** eine Uhrzeit aus, die auf **Online** endet.

    ![Auswählen eines Wiederherstellungspunkts](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)

2. Klicken Sie mit der rechten Maustaste auf den Datenbanknamen und anschließend auf **Wiederherstellen**.

    ![Wiederherstellen aus Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)

3. DPM zeigt die Details zum Wiederherstellungspunkt an. Klicken Sie auf **Weiter**. Wählen Sie den Wiederherstellungstyp **In ursprünglicher Instanz von SQL Server wiederherstellen** aus. Hierbei wird die Datenbank überschrieben. Klicken Sie auf **Weiter**.

    ![Wiederherstellung am ursprünglichen Speicherort](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In diesem Beispiel erlaubt DPM die Wiederherstellung der Datenbank auf eine andere SQL Server-Instanz oder in einen eigenständigen Netzwerkordner.

4. Im Bildschirm **Wiederherstellungsoptionen angeben** können Sie Optionen wie z. B. "Netzwerk-Bandbreiteneinschränkung" auswählen, um die Bandbreitenbelegung bei der Wiederherstellung zu drosseln. Klicken Sie auf **Weiter**.

5. Im Bildschirm **Zusammenfassung** werden alle bisher konfigurierten Wiederherstellungsoptionen angezeigt. Klicken Sie auf **Wiederherstellen**.

    Der Wiederherstellungsstatus zeigt an, dass die Datenbank wiederhergestellt wird. Sie können auf **Schließen** klicken, um den Assistenten zu schließen und den Fortschritt im Arbeitsbereich **Überwachung** zu verfolgen.

    ![Auslösen der Wiederherstellung](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Nach Abschluss der Wiederherstellung befindet sich die Datenbankkopie in einem anwendungskonsistenten Zustand.

### Nächste Schritte:

• [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO3-->