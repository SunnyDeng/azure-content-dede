<properties linkid="web-sites-restore" urlDisplayName="Restore a Microsoft Azure website" pageTitle="Restore a Microsoft Azure website" metaKeywords="Azure Websites, Restore, restoring" description="Learn how to restore your Azure websites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure website" authors="cephalin"  solutions="" writer="cephalin" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Wiederherstellen einer Microsoft Azure-Website

In diesem Artikel erfahren Sie, wie Sie eine Website wiederherstellen, die zuvor mit der Funktion zur Sicherung von Azure-Websites gesichert wurde. Weitere Informationen finden Sie unter [Sicherungen von Microsoft Azure-Websites][Sicherungen von Microsoft Azure-Websites].

Mit der Wiederherstellungsfunktion für Azure-Websites können Sie Ihre Website bei Bedarf in einem vorherigen Zustand wiederherstellen oder eine neue Website basierend auf der Sicherung der ursprünglichen Website erstellen. Das Erstellen einer neuen Website, die parallel zur letzten Version ausgeführt wird, kann für A/B-Tests nützlich sein.

Die Wiederherstellungsfunktion auf der Registerkarte "Sicherungen" im Azure-Websites-Portal ist nur im Modus "Standard" verfügbar.

## Themen in diesem Artikel

-   [Wiederherstellen einer Azure-Website aus einer zuvor erstellten Sicherung][Wiederherstellen einer Azure-Website aus einer zuvor erstellten Sicherung]
-   [Wiederherstellen einer Azure-Website direkt aus einem Speicherkonto][Wiederherstellen einer Azure-Website direkt aus einem Speicherkonto]
-   [Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs][Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs]
-   [Anzeigen der Vorgangsprotokolle][Anzeigen der Vorgangsprotokolle]

<a name="PreviousBackup"></a>

## Wiederherstellen einer Azure-Website aus einer zuvor erstellten Sicherung

1.  Klicken Sie auf der Registerkarte **Sicherungen** in der Befehlsleiste unten auf der Portalseite auf **Jetzt wiederherstellen**. Das Dialogfeld **Jetzt wiederherstellen** wird angezeigt.

    ![Sicherungsquelle auswählen][Sicherungsquelle auswählen]

2.  Wählen Sie unter **Sicherungsquelle wählen** die Option **Vorherige Sicherung für diese Website** aus.
3.  Wählen Sie das Datum der Sicherung aus, die Sie wiederherstellen möchten, und klicken Sie dann auf den Pfeil nach rechts, um fortzufahren.
4.  Befolgen Sie die Schritte im Abschnitt [Auswählen der Wiederherstellungseinstellungen der Website][Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs] weiter unten in diesem Artikel.

<a name="StorageAccount"></a>

## Wiederherstellen einer Azure-Website direkt aus einem Speicherkonto

1.  Klicken Sie auf der Registerkarte **Sicherungen** in der Befehlsleiste unten auf der Portalseite auf **Jetzt wiederherstellen**. Das Dialogfeld **Jetzt wiederherstellen** wird angezeigt.

    ![Sicherungsquelle auswählen][Sicherungsquelle auswählen]

2.  Wählen Sie unter **Sicherungsquelle wählen** die Option **Speicherkontodatei** aus. Hier können Sie direkt die URL für die Speicherkontodatei auswählen oder auf das Ordnersymbol klicken, um zum Blob-Speicher zu navigieren und die Sicherungsdatei anzugeben. In diesem Beispiel wird das Ordnersymbol verwendet.

    ![Speicherkontodatei][Speicherkontodatei]

3.  Klicken Sie auf das Ordnersymbol, um das Dialogfeld **Im Cloud-Speicher navigieren** zu öffnen.

    ![Cloud-Speicher durchsuchen][Cloud-Speicher durchsuchen]

4.  Erweitern Sie den Namen des Speicherkontos, das Sie verwenden möchten, und wählen Sie dann **websitebackups** aus, das Ihre Sicherungen enthält.
5.  Wählen Sie die ZIP-Datei aus, welche die wiederherzustellende Sicherung enthält, und klicken Sie dann auf **Öffnen**.
6.  Die Speicherkontodatei wurde ausgewählt und wird jetzt im Speicherkontofeld angezeigt. Klicken Sie auf den Pfeil nach rechts, um fortzufahren.

    ![Ausgewählte Speicherkontodatei][Ausgewählte Speicherkontodatei]

7.  Fahren Sie mit dem folgenden Abschnitt fort, [Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs][Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs].

<a name="RestoreSettings"></a>

## Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs

1.  Wählen Sie unter **Wiederherstellungseinstellungen für Website wählen**, **Wiederherstellen auf** entweder **Aktuelle Website** oder **Neue Websiteinstanz** aus.

    ![Wiederherstellungseinstellungen der Website auswählen][Wiederherstellungseinstellungen der Website auswählen]

    Wenn Sie **Aktuelle Website** auswählen, wird Ihre vorhandene Website durch die Sicherung überschrieben, die Sie ausgewählt haben (destruktive Wiederherstellung). Alle seit der Sicherung an der Website vorgenommenen Änderungen werden dauerhaft entfernt, und der Wiederherstellungsvorgang kann nicht rückgängig gemacht werden. Während des Wiederherstellungsvorgangs ist die aktuelle Website kurzzeitig nicht erreichbar, und es wird eine Warnmeldung dazu angezeigt.

    Wenn Sie **Neue Websiteinstanz** auswählen, wird eine neue Website in derselben Region mit dem von Ihnen angegebenen Namen erstellt. (Standardmäßig lautet der neue Name **restored-***alterWebsitename*.)

    Die von Ihnen wiederhergestellte Website enthält denselben Inhalt und die Konfiguration, die im Portal für die ursprüngliche Website festgelegt wurde. Außerdem umfasst sie alle Datenbanken, die Sie im nächsten Schritt einschließen.

2.  Wenn Sie zusammen mit der Website eine Datenbank wiederherstellen möchten, wählen Sie unter **Enthaltene Datenbanken** mithilfe der Dropdownliste unter **Wiederherstellen auf** den Namen des Datenbankservers aus, in dem Sie die Datenbank wiederherstellen möchten. Sie können auch einen neuen Datenbankserver für die Wiederherstellung erstellen oder **Nicht wiederherstellen** angeben, um die Datenbank nicht wiederherzustellen (dies ist die Standardauswahl).

    Nachdem Sie den Servernamen ausgewählt haben, geben Sie den Namen der Zieldatenbank für die Wiederherstellung im Feld **Datenbankname** an.

    Wenn die Wiederherstellung eine oder mehrere Datenbanken umfasst, können Sie **Verbindungszeichenfolgen automatisch anpassen** auswählen, um die in der Sicherung gespeicherten Verbindungszeichenfolgen zu aktualisieren, sodass sie auf die neue Datenbank bzw. den Datenbankserver verweisen. Vergewissern Sie sich, dass alle auf die Datenbank bezogenen Funktionen nach der Wiederherstellung wie erwartet funktionieren.

    ![Datenbankserverhost auswählen][Datenbankserverhost auswählen]

    > [WACOM.NOTE] Sie können eine SQL-Datenbank mit demselben Namen nicht auf demselben SQL-Server wiederherstellen. Sie müssen entweder einen anderen Datenbanknamen oder einen anderen SQL-Serverhost auswählen, auf dem die Datenbank wiederhergestellt wird.

    > [WACOM.NOTE] Sie können eine MySQL-Datenbank mit demselben Namen auf demselben Server wiederherstellen, beachten Sie jedoch, dass dabei der vorhandene Inhalt aus der MySQL-Datenbank gelöscht wird.

3.  Wenn Sie eine vorhandene Datenbank wiederherstellen möchten, müssen Sie den Benutzernamen und das Kennwort angeben. Wenn Sie in einer neuen Datenbank wiederherstellen möchten, müssen Sie einen neuen Datenbanknamen angeben:

    ![In einer neuen SQL-Datenbank wiederherstellen][In einer neuen SQL-Datenbank wiederherstellen]

    Klicken Sie auf den Pfeil nach rechts, um fortzufahren.

4.  Falls Sie eine neue Datenbank erstellen, müssen Sie im nächsten Dialogfeld die Anmeldeinformationen und andere anfängliche Konfigurationsdaten für die Datenbank angeben. In diesem Beispiel wird eine neue SQL-Datenbank verwendet. (Die Optionen für eine neue MySQL-Datenbank sind etwas anders.)

    ![Einstellungen der neuen SQL-Datenbank][Einstellungen der neuen SQL-Datenbank]

5.  Aktivieren Sie das Kontrollkästchen, um den Wiederherstellungsvorgang zu starten. Sobald er abgeschlossen ist, wird die neue Websiteinstanz (falls dies die gewählte Wiederherstellungsoption ist) in der Liste der Websites im Portal angezeigt.

    ![Wiederhergestellte Contoso-Website][Wiederhergestellte Contoso-Website]

<a name="OperationLogs"></a>

## Anzeigen der Vorgangsprotokolle

1.  Wenn Sie Details zur erfolgreichen oder fehlerhaften Ausführung der Websitewiederherstellung anzeigen möchten, wechseln Sie zur Registerkarte "Dashboard" Ihrer Website. Klicken Sie im Abschnitt **Auf einen Blick** unter **Verwaltungsdienste** auf **Vorgangsprotokolle**.

    ![Dashboard - Link zu den Vorgangsprotokollen][Dashboard - Link zu den Vorgangsprotokollen]

2.  Sie gelangen zur Seite **Vorgangsprotokolle** des Verwaltungsdienstportals, wo Sie das Protokoll für den Wiederherstellungsvorgang in der Liste der Vorgangsprotokolle anzeigen können:

    ![Verwaltungsdienstseite mit Vorgangsprotokollen][Verwaltungsdienstseite mit Vorgangsprotokollen]

3.  Um ausführliche Informationen zum Vorgang anzuzeigen, wählen Sie den Vorgang in der Liste aus und klicken in der Befehlsleiste auf die Schaltfläche **Details**.

    ![Detailschaltfläche ][Detailschaltfläche ]

    Daraufhin wird das Fenster **Vorgangsdetails** geöffnet und der kopierbare Inhalt der Protokolldatei angezeigt:

    ![Vorgangsdetails][Vorgangsdetails]

<!-- IMAGES -->

  [Sicherungen von Microsoft Azure-Websites]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-backup/
  [Wiederherstellen einer Azure-Website aus einer zuvor erstellten Sicherung]: #PreviousBackup
  [Wiederherstellen einer Azure-Website direkt aus einem Speicherkonto]: #StorageAccount
  [Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs]: #RestoreSettings
  [Anzeigen der Vorgangsprotokolle]: #OperationLogs
  [Sicherungsquelle auswählen]: ./media/web-sites-restore/01ChooseBackupSource.png
  [Speicherkontodatei]: ./media/web-sites-restore/02StorageAccountFile.png
  [Cloud-Speicher durchsuchen]: ./media/web-sites-restore/03BrowseCloudStorage.png
  [Ausgewählte Speicherkontodatei]: ./media/web-sites-restore/04StorageAccountFileSelected.png
  [Wiederherstellungseinstellungen der Website auswählen]: ./media/web-sites-restore/05ChooseRestoreSettings.png
  [Datenbankserverhost auswählen]: ./media/web-sites-restore/06ChooseDBServer.png
  [In einer neuen SQL-Datenbank wiederherstellen]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
  [Einstellungen der neuen SQL-Datenbank]: ./media/web-sites-restore/08NewSQLDBConfig.png
  [Wiederhergestellte Contoso-Website]: ./media/web-sites-restore/09RestoredContosoWebSite.png
  [Dashboard - Link zu den Vorgangsprotokollen]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
  [Verwaltungsdienstseite mit Vorgangsprotokollen]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
  [Detailschaltfläche ]: ./media/web-sites-restore/12DetailsButton.png
  [Vorgangsdetails]: ./media/web-sites-restore/13OperationDetails.png
