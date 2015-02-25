<properties 
	pageTitle="Wiederherstellen einer Microsoft Azure-Website" 
	description="Erfahren Sie, wie Ihre Azure-Websites aus einer Sicherung wiederhergestellt werden." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Wiederherstellen einer Microsoft Azure-Website

In diesem Artikel erfahren Sie, wie Sie eine Website wiederherstellen, die zuvor mit der Funktion zur Sicherung von Azure-Websites gesichert wurde. Weitere Informationen finden Sie unter [Sicherungen von Microsoft Azure-Websites](http://www.windowsazure.com/de-de/documentation/articles/web-sites-backup/). 

Mit der Wiederherstellungsfunktion für Azure-Websites können Sie Ihre Website bei Bedarf in einem vorherigen Zustand wiederherstellen oder eine neue Website basierend auf der Sicherung der ursprünglichen Website erstellen. Das Erstellen einer neuen Website, die parallel zur letzten Version ausgeführt wird, kann für A/B-Tests nützlich sein.

Die Wiederherstellungsfunktion auf der Registerkarte "Sicherungen" im Azure-Websites-Portal ist nur im Modus "Standard" verfügbar.

##Themen in diesem Artikel
- [Wiederherstellen einer Azure-Website aus einer zuvor erstellten Sicherung](#PreviousBackup)
- [Wiederherstellen einer Azure-Website direkt aus einem Speicherkonto](#StorageAccount)
- [Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs](#RestoreSettings)
- [Anzeigen der Vorgangsprotokolle](#OperationLogs)


<a name="PreviousBackup"></a>
##Wiederherstellen einer Azure-Website aus einer zuvor erstellten Sicherung

1. Klicken Sie auf der Registerkarte **Sicherungen** auf der Befehlsleiste unten auf der Portalseite auf **Jetzt wiederherstellen**. Das Dialogfeld **Jetzt wiederherstellen** wird angezeigt.
	
	![Choose backup source][ChooseBackupSource]
	
2. Wählen Sie unter **Sicherungsquelle wählen** die Option **Vorherige Sicherung für diese Website** aus.
3. Wählen Sie das Datum der Sicherung aus, die Sie wiederherstellen möchten, und klicken Sie dann auf den Pfeil nach rechts, um fortzufahren.
4. Befolgen Sie die Schritte im Abschnitt [Auswählen der Wiederherstellungseinstellungen der Website](#RestoreSettings) weiter unten in diesem Artikel

<a name="StorageAccount"></a>
##Wiederherstellen einer Azure-Website direkt aus einem Speicherkonto

1. Klicken Sie auf der Registerkarte **Sicherungen** auf der Befehlsleiste unten auf der Portalseite auf **Jetzt wiederherstellen**. Das Dialogfeld **Jetzt wiederherstellen** wird angezeigt.
	
	![Choose backup source][ChooseBackupSource]
	
2. Wählen Sie unter **Sicherungsquelle wählen** die Option **Speicherkontodatei** aus. Hier können Sie direkt die URL für die Speicherkontodatei auswählen oder auf das Ordnersymbol klicken, um zum Blob-Speicher zu navigieren und die Sicherungsdatei anzugeben. In diesem Beispiel wird das Ordnersymbol verwendet.
	
	![Storage Account File][StorageAccountFile]
	
3. Klicken Sie auf das Ordnersymbol, um das Dialogfeld **Im Cloud-Speicher navigieren** zu öffnen.
	
	![Browse Cloud Storage][BrowseCloudStorage]
	

4. Erweitern Sie den Namen des Speicherkontos, das Sie verwenden möchten, und wählen Sie dann das Verzeichnis **websitebackups** aus, das Ihre Sicherungen enthält.
5. Wählen Sie die ZIP-Datei aus, welche die wiederherzustellende Sicherung enthält, und klicken Sie dann auf **Öffnen**.
6. Die Speicherkontodatei wurde ausgewählt und wird jetzt im Speicherkontofeld angezeigt. Klicken Sie auf den Pfeil nach rechts, um fortzufahren.
	
	![Storage Account File Selected][StorageAccountFileSelected]
	
7. Fahren Sie mit dem folgenden Abschnitt fort, [Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs](#RestoreSettings).

<a name="RestoreSettings"></a>
## Auswählen der Wiederherstellungseinstellungen der Website und Starten des Wiederherstellungsvorgangs

1. Wählen Sie unter **Wiederherstellungseinstellungen für Website wählen**, **Wiederherstellen auf** entweder **Aktuelle Website** oder **Neue Websiteinstanz** aus.
	
	![Choose your web site restore settings][ChooseRestoreSettings]
	
	Wenn Sie **Aktuelle Website** auswählen, wird Ihre vorhandene Website durch die Sicherung überschrieben, die Sie ausgewählt haben (destruktive Wiederherstellung). Alle seit der Sicherung an der Website vorgenommenen Änderungen werden dauerhaft entfernt, und der Wiederherstellungsvorgang kann nicht rückgängig gemacht werden. Während des Wiederherstellungsvorgangs ist die aktuelle Website kurzzeitig nicht erreichbar, und es wird eine Warnmeldung dazu angezeigt.
	
	Wenn Sie **Neue Websiteinstanz** auswählen, wird eine neue Website in derselben Region mit dem von Ihnen angegebenen Namen erstellt. (Standardmäßig lautet der neue Name **restored-***alterWebsitename*.) 
	
	Die von Ihnen wiederhergestellte Website enthält denselben Inhalt und die Konfiguration, die im Portal für die ursprüngliche Website festgelegt wurde. Außerdem umfasst sie alle Datenbanken, die Sie im nächsten Schritt einschließen.

2. Wenn Sie zusammen mit der Website eine Datenbank wiederherstellen möchten, wählen Sie unter **Enthaltene Datenbanken** mithilfe der Dropdownliste unter **Wiederherstellen auf** den Namen des Datenbankservers aus, in dem Sie die Datenbank wiederherstellen möchten. Sie können auch einen neuen Datenbankserver für die Wiederherstellung erstellen oder **Nicht wiederherstellen** angeben, um die Datenbank nicht wiederherzustellen (dies ist die Standardauswahl). 
	
	Nachdem Sie den Servernamen ausgewählt haben, geben Sie den Namen der Zieldatenbank für die Wiederherstellung im Feld **Datenbankname** an.
	
	Wenn die Wiederherstellung eine oder mehrere Datenbanken umfasst, können Sie **Verbindungszeichenfolgen automatisch anpassen** auswählen, um die in der Sicherung gespeicherten Verbindungszeichenfolgen zu aktualisieren, sodass sie auf die neue Datenbank bzw. den Datenbankserver verweisen. Vergewissern Sie sich, dass alle auf die Datenbank bezogenen Funktionen nach der Wiederherstellung wie erwartet funktionieren.
	
	![Choose database server host][ChooseDBServer]
	
	> [AZURE.NOTE] Sie können eine SQL-Datenbank mit demselben Namen nicht auf demselben SQL-Server wiederherstellen. Sie müssen entweder einen anderen Datenbanknamen oder einen anderen SQL-Serverhost auswählen, auf dem die Datenbank wiederhergestellt wird. 
	
	> [AZURE.NOTE] Sie können eine MySQL-Datenbank mit demselben Namen auf demselben Server wiederherstellen, beachten Sie jedoch, dass dabei der vorhandene Inhalt aus der MySQL-Datenbank gelöscht wird.	
	
3. Wenn Sie eine vorhandene Datenbank wiederherstellen möchten, müssen Sie den Benutzernamen und das Kennwort angeben. Wenn Sie in einer neuen Datenbank wiederherstellen möchten, müssen Sie einen neuen Datenbanknamen angeben:
	
	![Restore to a new SQL database][RestoreToNewSQLDB]
	
	Klicken Sie auf den Pfeil nach rechts, um fortzufahren.	
4. Falls Sie eine neue Datenbank erstellen, müssen Sie im nächsten Dialogfeld die Anmeldeinformationen und andere anfängliche Konfigurationsdaten für die Datenbank angeben. In diesem Beispiel wird eine neue SQL-Datenbank verwendet. (Die Optionen für eine neue MySQL-Datenbank sind etwas anders.)
	
	![New SQL database settings][NewSQLDBConfig]
	
5. Aktivieren Sie das Kontrollkästchen, um den Wiederherstellungsvorgang zu starten. Sobald er abgeschlossen ist, wird die neue Websiteinstanz (falls dies die gewählte Wiederherstellungsoption ist) in der Liste der Websites im Portal angezeigt.
	
	![Restored Contoso web site][RestoredContosoWebSite]

<a name="OperationLogs"></a>
##Anzeigen der Vorgangsprotokolle
	
1. Wenn Sie Details zur erfolgreichen oder fehlerhaften Ausführung der Websitewiederherstellung anzeigen möchten, wechseln Sie zur Registerkarte "Dashboard" Ihrer Website. Klicken Sie im Abschnitt **Auf einen Blick** unter **Verwaltungsdienste** auf **Vorgangsprotokolle**.
	
	![Dashboard - Operation Logs Link][DashboardOperationLogsLink]
	
2. Sie gelangen zur Seite **Vorgangsprotokolle** des Verwaltungsdienstportals, wo Sie das Protokoll für den Wiederherstellungsvorgang in der Liste der Vorgangsprotokolle anzeigen können:
	
	![Management Services Operation Logs page][ManagementServicesOperationLogsList]
	
3. Um ausführliche Informationen zum Vorgang anzuzeigen, wählen Sie den Vorgang in der Liste aus und klicken in der Befehlsleiste auf die Schaltfläche **Details**.
	
	![Details Button][DetailsButton]
	
	Daraufhin wird das Fenster **Vorgangsdetails** geöffnet und der kopierbare Inhalt der Protokolldatei angezeigt:
	
	![Operation Details][OperationDetails]
	

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




<!--HONumber=42-->
