<properties 
	pageTitle="Wiederherstellen einer Web-App in Azure App Service" 
	description="Erfahren Sie, wie Sie Ihre Web-App aus einer Sicherung wiederherstellen." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/03/2015" 
	ms.author="cephalin"/>

# Wiederherstellen einer Web-App in Azure App Service

In diesem Artikel erfahren Sie, wie Sie eine Web-App wiederherstellen, die zuvor mit der Funktion zur Sicherung von [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) gesichert wurde. Weitere Informationen finden Sie unter [Sichern von App Service-Web-Apps](web-sites-backup.md).

Mit der Wiederherstellungsfunktion für Web-Apps können Sie Ihre Web-App bei Bedarf in einem vorherigen Zustand wiederherstellen oder eine neue Web-App auf der Grundlage einer Sicherung Ihrer ursprünglichen Web-App erstellen. Das Erstellen einer neuen Web-App, die parallel zur letzten Version ausgeführt wird, kann für A/B-Tests nützlich sein.

Die Wiederherstellungsfunktion für Web-Apps ist auf dem Blatt **Sicherungen** im [Azure-Vorschauportal](http://portal.azure.com) verfügbar, jedoch nur im Standard- oder Premium-Modus. Weitere Informationen zum Skalieren Ihrer App im Standard- oder Premium-Modus finden Sie unter [Skalieren von Web-Apps in Azure App Service](web-sites-scale.md). Beachten Sie, dass im Premium-Modus eine größere Anzahl von täglichen Sicherungen als im Standardmodus ausgeführt werden kann.

<a name="PreviousBackup"></a>
## So stellen Sie eine Web-App aus einer zuvor erstellten Sicherung wieder her

1. Klicken Sie auf dem Blatt **Einstellungen** Ihrer Web-App im Azure-Portal auf die Option **Sicherungen**, um das Blatt **Sicherungen** anzuzeigen. Führen Sie auf diesem Blatt einen Bildlauf aus, und wählen Sie in der Sicherungsliste eine der Sicherungsoptionen anhand der Angaben für **SICHERUNGSZEIT** und **STATUS** aus.
	
	![Sicherungsquelle auswählen][ChooseBackupSource]
	
2. Wählen Sie im oberen Bereich des Blatts **Sicherungen** die Option **Jetzt wiederherstellen** aus.

	!["Jetzt wiederherstellen" auswählen][ChooseRestoreNow]

3. Um die vorhandene Web-App wiederherzustellen, überprüfen Sie auf dem Blatt **Wiederherstellen** alle angegebenen Details, und klicken Sie dann auf **OK**.
	
Sie können Ihre Web-App auch als neue Web-App wiederherstellen, indem Sie den Bereich **WEB-APP** des Blatts **Wiederherstellen** auswählen und den Bereich **Eine neue Web-App erstellen** auswählen.
	
<a name="StorageAccount"></a>
## Herunterladen oder Löschen einer Sicherung aus einem Speicherkonto
	
1. Wählen Sie auf dem Hauptblatt **Durchsuchen** des Azure-Portals die Option **Speicherkonten** aus.
	
	Eine Liste Ihrer vorhandenen Speicherkonten wird angezeigt.
	
2. Wählen Sie das Speicherkonto aus, das die herunterzuladende oder zu löschende Sicherung enthält.
	
	Das Blatt **SPEICHER** wird angezeigt.

3. Wählen Sie den Bereich **Containers** im Blatt **SPEICHER** aus, um das Blatt **Container** anzuzeigen.
	
	Eine Liste der Container wird angezeigt. In dieser Liste werden außerdem die URL und das Datum der letzten Änderung dieses Containers angezeigt.
	
	![Container anzeigen][ViewContainers]

4. Wählen Sie in der Liste den Container aus, um das Blatt mit einer Liste der Dateinamen und der Größe der einzelnen Dateien anzuzeigen.
	
5. Wenn Sie eine Datei auswählen, können Sie sie wahlweise **herunterladen** oder **löschen**. Beachten Sie, dass es zwei primäre Dateitypen gibt, ZIP-Dateien und XML-Dateien.

<a name="OperationLogs"></a>
## Anzeigen der Überwachungsprotokolle
	
1. Um Details über den Erfolg oder Misserfolg des Wiederherstellungsvorgangs für die Web-App anzuzeigen, wählen Sie den Bereich **Überwachungsprotokoll** auf dem Hauptblatt **Durchsuchen** aus. 
	
	Auf dem Blatt **Audio-Protokoll** werden alle Ihre Vorgänge mit Ebene, Status, Ressourcen und Zeitdetails angezeigt.
	
2. Führen Sie einen Bildlauf im Blatt aus, um Vorgänge im Zusammenhang mit Ihrer Web-App zu suchen.
3. Um weitere Details zu einem Vorgang anzuzeigen, wählen Sie den Vorgang in der Liste aus.
	
Auf dem Blatt "Details" werden die verfügbaren Informationen im Zusammenhang mit dem Vorgang angezeigt.
	
>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.
	
## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
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
 

<!---HONumber=July15_HO5-->