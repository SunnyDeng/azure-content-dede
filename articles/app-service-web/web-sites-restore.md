<properties 
	pageTitle="Wiederherstellen einer App in Azure App Service" 
	description="Erfahren Sie, wie Sie Ihre App aus einer Sicherung wiederherstellen." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="cephalin"/>

# Wiederherstellen einer App in Azure App Service

In diesem Artikel erfahren Sie, wie Sie eine App Service-App wiederherstellen, die zuvor mit der [App Service](app-service-value-prop-what-is)-Sicherungsfunktion gesichert wurde. Weitere Informationen finden Sie unter [App Service-Sicherungen](web-sites-backup.md).

Mit dem App Service-Wiederherstellungsfeature können Sie Ihre App mit den zugehörigen verknüpften Datenbanken (SQL-Datenbank oder MySQL) bei Bedarf in einem vorherigen Zustand wiederherstellen oder basierend auf der Sicherung der ursprünglichen App eine neue App erstellen. Das Erstellen einer neuen App, die parallel zur letzten Version ausgeführt wird, kann für A/B-Tests nützlich sein.

Die App Service-Wiederherstellungsfunktion ist im [Azure-Portal](https://portal.azure.com) auf dem Blatt **Sicherungen** verfügbar, jedoch nur im Standard- oder Premium-Tarif. Weitere Informationen zum Skalieren Ihrer App im Standard- oder Premium-Tarif finden Sie unter [Skalieren einer App in Azure App Service](web-sites-scale.md). Beachten Sie, dass im Premium-Tarif eine größere Anzahl von täglichen Sicherungen ausgeführt werden kann als im Standard-Tarif.

<a name="PreviousBackup"></a>
## So stellen Sie eine App aus einer zuvor erstellten Sicherung wieder her

1. Klicken Sie auf dem Blatt **Einstellungen** Ihrer App im Azure-Portal auf **Sicherungen**, um das Blatt **Sicherungen** anzuzeigen. Klicken Sie dann in der Befehlsleiste auf **Jetzt wiederherstellen**. 
	
	!["Jetzt wiederherstellen" auswählen][ChooseRestoreNow]

3. Wählen Sie auf dem Blatt **Wiederherstellen** zuerst die Sicherungsquelle aus.

	![](./media/web-sites-restore/021ChooseSource.png)
	
	Die Option **App-Sicherung** zeigt alle Sicherungen, die direkt von der App selbst erstellt werden, da nur diese von den Apps erkannt werden. Sie können problemlos eine davon auswählen. Mit der Option **Speicher** können Sie die eigentliche ZIP-Datei der Sicherung aus dem Speicherkonto und dem Container auswählen, der auf Ihrem Blatt **Sicherungen** konfiguriert ist. Wenn Sicherungsdateien aus anderen Apps im Container enthalten sind, können Sie auch diese zur Wiederherstellung auswählen.

4. Geben Sie anschließend das Ziel für die App-Wiederherstellung unter **Wiederherstellungsziel** an.

	![](./media/web-sites-restore/022ChooseDestination.png)
	
	>[AZURE.WARNING] Wenn Sie **Überschreiben** wählen, werden alle Daten im Zusammenhang mit Ihrer vorhandenen App gelöscht. Bevor Sie auf **OK** klicken, stellen Sie sicher, dass alles genau Ihren Vorstellungen entspricht.
	
	Sie können **Vorhandene App** auswählen, um die App-Sicherung in einer anderen App in derselben Ressourcengruppe wiederherzustellen. Bevor Sie diese Option verwenden, sollten Sie bereits eine andere App in der Ressourcengruppe erstellt haben, deren Datenbankkonfiguration derjenigen entspricht, die in der App-Sicherung definiert ist.
	
5. Klicken Sie auf **OK**.

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
	
1. Um Details über den Erfolg oder Misserfolg des Wiederherstellungsvorgangs für die App anzuzeigen, wählen Sie den Bereich **Überwachungsprotokoll** auf dem Hauptblatt **Durchsuchen** aus. 
	
	Auf dem Blatt **Überwachungsprotokoll** werden alle Ihre Vorgänge mit Ebene, Status, Ressourcen und Zeitdetails angezeigt.
	
2. Scrollen Sie durch das Blatt, um Vorgänge im Zusammenhang mit Ihrer App zu suchen.
3. Um weitere Details zu einem Vorgang anzuzeigen, wählen Sie den Vorgang in der Liste aus.
	
Auf dem Blatt "Details" werden die verfügbaren Informationen im Zusammenhang mit dem Vorgang angezeigt.
	
>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Nächste Schritte

Sie können App Service-Apps auch mithilfe der REST-API sichern und wiederherstellen (siehe [Verwenden von REST zum Sichern und Wiederherstellen von App Service-Apps](websites-csm-backup.md)).

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
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
 

<!---HONumber=AcomDC_0128_2016-->