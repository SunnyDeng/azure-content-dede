<properties linkid="web-sites-restore" urlDisplayName="Restore a Microsoft Azure web site" pageTitle="Restore a Microsoft Azure web site" metaKeywords="Azure Web Sites, Restore, restoring" description="Learn how to restore your Azure web sites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure web site" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Wiederherstellen einer Microsoft Azure-Website
==============================================

In diesem Artikel erfahren Sie, wie Sie eine Website wiederherstellen, die zuvor mit der Funktion zur Sicherung von Azure-Websites gesichert wurde. Weitere Informationen finden Sie unter [Sicherungen von Microsoft Azure-Websites](http://www.windowsazure.com/de-de/documentation/articles/web-sites-backup/).

Mit der Wiederherstellungsfunktion für Azure-Websites können Sie Ihre Website bei Bedarf in einem vorherigen Zustand wiederherstellen oder eine neue Website basierend auf der Website-Sicherung erstellen. Das Erstellen einer neuen Website, die parallel zur letzten Version ausgeführt wird, kann für A/B-Tests nützlich sein.

Die Wiederherstellungsfunktion auf der Registerkarte "Backups" im Azure-Websites-Portal ist nur im Standardmodus verfügbar.

Themen in diesem Artikel
------------------------

-   [Wiederherstellen einer Azure-Website aus einer zuvor erstellten Sicherung](#PreviousBackup)
-   [Wiederherstellen einer Azure-Website direkt aus einem Speicherkonto](#StorageAccount)
-   [Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs](#RestoreSettings)
-   [Anzeigen der Vorgangsprotokolle](#OperationLogs)

## Wiederherstellen einer Azure-Website aus einer zuvor erstellten Sicherung

1.  Klicken Sie auf der Registerkarte **Backups** in der Befehlsleiste unten auf der Portalseite auf **Restore Now**. Das Dialogfeld **Restore Now** wird angezeigt.

    ![Sicherungsquelle auswählen](./media/web-sites-restore/01ChooseBackupSource.png)

2.  Wählen Sie unter **Choose backup source** die Option **Previous Backup for this Web Site** aus.
3.  Wählen Sie das Datum der Sicherung aus, die Sie wiederherstellen möchten, und klicken Sie dann auf den Pfeil nach rechts, um fortzufahren.
4.  Befolgen Sie die Schritte im Abschnitt [Auswählen der Wiederherstellungseinstellungen der Website](#RestoreSettings) weiter unten in diesem Artikel.

Wiederherstellen einer Azure-Website direkt aus einem Speicherkonto
------------------------
1.  Klicken Sie auf der Registerkarte **Backups** in der Befehlsleiste unten auf der Portalseite auf **Restore Now**. Das Dialogfeld **Restore Now** wird angezeigt.

    ![Sicherungsquelle auswählen](./media/web-sites-restore/01ChooseBackupSource.png)

2.  Wählen Sie unter **Choose backup source** die Option **Storage Account File** aus. Hier können Sie direkt die URL für die Speicherkontodatei auswählen oder auf das Ordnersymbol klicken, um zum Blob-Speicher zu navigieren und die Sicherungsdatei anzugeben. In diesem Beispiel wird das Ordnersymbol verwendet.

    ![Speicherkontodatei](./media/web-sites-restore/02StorageAccountFile.png)

3.  Klicken Sie auf das Ordnersymbol, um das Dialogfeld **Browse Cloud Storage** zu öffnen.

    ![Cloud-Speicher durchsuchen](./media/web-sites-restore/03BrowseCloudStorage.png)

4.  Erweitern Sie den Namen des Speicherkontos, das Sie verwenden möchten, und wählen Sie dann **websitebackups** aus, das Ihre Sicherungen enthält.
5.  Wählen Sie die ZIP-Datei aus, welche die wiederherzustellende Sicherung enthält, und klicken Sie dann auf **Öffnen**.
6.  Die Speicherkontodatei wurde ausgewählt und wird jetzt im Speicherkontofeld angezeigt. Klicken Sie auf den Pfeil nach rechts, um fortzufahren.

    ![Ausgewählte Speicherkontodatei](./media/web-sites-restore/04StorageAccountFileSelected.png)

7.  Fahren Sie mit dem folgenden Abschnitt fort, [Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs](#RestoreSettings).

Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs 
------------------------
1. Wählen Sie unter **Choose your web site restore settings**, **Restore To** entweder **Current web site** oder **New web site instance** aus.

   ![Wiederherstellungseinstellungen der Website auswählen][ChooseRestoreSettings]

   Wenn Sie **Current web site** auswählen, wird Ihre vorhandene Website durch die Sicherung überschrieben, die Sie ausgewählt haben (destruktive Wiederherstellung). Alle seit der Sicherung an der Website vorgenommenen Änderungen werden dauerhaft verworfen, und der Wiederherstellungsvorgang kann nicht rückgängig gemacht werden. Während des Wiederherstellungsvorgangs ist die aktuelle Website kurzzeitig nicht erreichbar, und es wird eine Warnmeldung dazu angezeigt.

   Wenn Sie **New web site instance** auswählen, wird eine neue Website in derselben Region mit dem von Ihnen angegebenen Namen erstellt. (Standardmäßig lautet der neue Name **restored-***alterWebsiteName*.) 

   Die von Ihnen wiederhergestellte Website enthält denselben Inhalt und die Konfiguration, die im Portal für die ursprüngliche Website festgelegt wurde. Außerdem umfasst sie alle Datenbanken, die Sie im nächsten Schritt einschließen.

1. Wenn Sie zusammen mit der Website eine Datenbank wiederherstellen möchten, wählen Sie unter **Included Databases** mithilfe der Dropdown-Liste unter **Restore To** den Namen des Datenbankservers aus, in dem Sie die Datenbank wiederherstellen möchten. Sie können auch einen neuen Datenbankserver für die Wiederherstellung erstellen oder **Don't Restore** angeben, um die Datenbank nicht wiederherzustellen (dies ist die Standardauswahl).

    Nachdem Sie den Servernamen ausgewählt haben, geben Sie den Namen der Zieldatenbank für die Wiederherstellung im Feld **Database Name** an.

    Wenn die Wiederherstellung eine oder mehrere Datenbanken umfasst, können Sie **Automatically adjust connection strings** auswählen, um die in der Sicherung gespeicherten Verbindungszeichenfolgen zu aktualisieren, sodass sie auf die neue Datenbank bzw. den Datenbankserver verweisen. Vergewissern Sie sich, dass alle auf die Datenbank bezogenen Funktionen nach der Wiederherstellung wie erwartet funktionieren.

    ![Datenbankserverhost auswählen](./media/web-sites-restore/06ChooseDBServer.png)

    > [WACOM.NOTE] Sie können eine SQL-Datenbank mit demselben Namen nicht auf demselben SQL-Server wiederherstellen. Sie müssen entweder einen anderen Datenbanknamen oder einen anderen SQL-Serverhost auswählen, auf dem die Datenbank wiederhergestellt wird.

    > [WACOM.NOTE] Sie können eine MySQL-Datenbank mit demselben Namen auf demselben Server wiederherstellen, beachten Sie jedoch, dass dabei der vorhandene Inhalt aus der MySQL-Datenbank gelöscht wird.

2.  Wenn Sie eine vorhandene Datenbank wiederherstellen möchten, müssen Sie den Benutzernamen und das Kennwort angeben. Wenn Sie in einer neuen Datenbank wiederherstellen möchten, müssen Sie einen neuen Datenbanknamen angeben:

    ![In einer neuen SQL-Datenbank wiederherstellen](./media/web-sites-restore/07RestoreToNewSQLDB.png)

    Klicken Sie auf den Pfeil nach rechts, um fortzufahren.

3.  Falls Sie eine neue Datenbank erstellen, müssen Sie im nächsten Dialogfeld die Anmeldeinformationen und andere anfängliche Konfigurationsdaten für die Datenbank angeben. In diesem Beispiel wird eine neue SQL-Datenbank verwendet. (Die Optionen für eine neue MySQL-Datenbank sind etwas anders.)

    ![Einstellungen der neuen SQL-Datenbank](./media/web-sites-restore/08NewSQLDBConfig.png)

4.  Aktivieren Sie das Kontrollkästchen, um den Wiederherstellungsvorgang zu starten. Sobald er abgeschlossen ist, wird die neue Website-Instanz (falls dies die gewählte Wiederherstellungsoption ist) in der Liste der Websites im Portal angezeigt.

    ![Wiederhergestellte Contoso-Website](./media/web-sites-restore/09RestoredContosoWebSite.png)

Anzeigen der Vorgangsprotokolle
------------------------
1.  Wenn Sie Details zum Erfolg oder Fehlschlagen der Website-Wiederherstellung anzeigen möchten, wechseln Sie zur Registerkarte "Dashboard" Ihrer Website. Klicken Sie im Abschnitt **Quick Glance** unter **Management Services** auf **Operation Logs**.

    ![Dashboard - Link zu den Vorgangsprotokollen](./media/web-sites-restore/10DashboardOperationLogsLink.png)

2.  Sie gelangen zur Management Services-Portalseite **Operation Logs**, wo Sie das Protokoll für den Wiederherstellungsvorgang in der Liste der Vorgangsprotokolle anzeigen können:

    ![Management Services-Seite mit Vorgangsprotokollen](./media/web-sites-restore/11ManagementServicesOperationLogsList.png)

3.  Um ausführliche Informationen zum Vorgang anzuzeigen, wählen Sie den Vorgang in der Liste aus und klicken in der Befehlsleiste auf die Schaltfläche **Details**.

    ![Detailschaltfläche ](./media/web-sites-restore/12DetailsButton.png)

    Daraufhin wird das Fenster **Operations Details** geöffnet und der kopierbare Inhalt der Protokolldatei angezeigt:

    ![Vorgangsdetails](./media/web-sites-restore/13OperationDetails.png)

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png

