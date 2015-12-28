<properties 
	pageTitle="Sichern von Web-Apps in Azure App Service" 
	description="Erfahren Sie, wie Sie Sicherungen Ihrer Web-Apps in Azure App Service erstellen." 
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
	ms.date="12/10/2015" 
	ms.author="cephalin"/>

# Sichern von Web-Apps in Azure App Service


Die Funktion zum Sichern und Wiederherstellen von [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) ermöglicht die einfache Erstellung manueller oder automatischer Web-App-Sicherungen. Sie können die Web-App in einem früheren Zustand wiederherstellen oder eine neue Web-App basierend auf einer Web-App-Sicherung erstellen.

Informationen zum Wiederherstellen von Azure-Web-Apps aus einer Sicherung finden Sie unter [Wiederherstellen von Web-Apps](web-sites-restore.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="whatsbackedup"></a>
## Was wird gesichert? 
Für Web-Apps können die folgenden Informationen gesichert werden:

* Web-App-Konfiguration
* Dateiinhalte der Web-App
* Jede Azure SQL- und jede MySQL-Datenbank, die mit der Web-App verbunden ist (Sie können auswählen, welche gesichert werden sollen.)

Diese Informationen werden in dem von Ihnen angegebenen Azure-Speicherkonto und -Container gesichert.

> [AZURE.NOTE]Jede Sicherung ist eine vollständige Offlinekopie Ihrer App. Es gibt keine inkrementellen Aktualisierungen.

<a name="requirements"></a>
## Anforderungen und Einschränkungen

* Die Funktion zum Sichern und Wiederherstellen erfordert, dass sich der App Service-Plan im Tarif „Standard“ oder einem höheren Tarif angehört. Weitere Informationen zum Skalieren des App Service-Plans zur Verwendung in einem höheren Tarif finden Sie unter [Skalieren einer Web-App in Azure App Service](web-sites-scale.md). Beachten Sie, dass im Tarif „Premium“ eine größere Anzahl täglicher Sicherungen zulässig ist als im Tarif „Standard“.

* Die Funktion zum Sichern und Wiederherstellen erfordert ein Azure-Speicherkonto und einen -Container, die zum selben Abonnement wie die Web-App gehören, die Sie sichern möchten. Falls Sie noch nicht über ein Speicherkonto verfügen, können Sie es erstellen, indem Sie im [Azure-Portal](http://portal.azure.com) auf dem Blatt **Sicherungen** auf die Schaltfläche **Speicherkonto** klicken. Klicken Sie anschließend auf dem Blatt **Ziel** auf das **Speicherkonto** und den **Container**. Weitere Informationen zu Azure-Speicherkonten erhalten Sie unter den [Links](#moreaboutstorage) am Ende dieses Artikels.

* Das Feature zum Sichern und Wiederherstellen unterstützt bis zu 10 GB an Website- und Datenbankinhalt. Es wird eine Fehlermeldung angezeigt, wenn die Sicherungsfunktion nicht fortgesetzt werden kann, weil die Nutzlast diesen Grenzwert überschreitet.

<a name="manualbackup"></a>
## Erstellen einer manuellen Sicherung

1. Wählen Sie im Azure-Portal auf dem Blatt „Web-Apps“ Ihre Web-App aus. Damit zeigen Sie Details der Web-App auf einem neuen Blatt an.
2. Wählen Sie auf dem Blatt für Ihre App die Option **Einstellungen** und dann **Sicherungen** aus. Das Blatt **Sicherungen** wird angezeigt.
	
	![Seite 'Sicherungen'][ChooseBackupsPage]

3. Klicken Sie auf dem Blatt **Sicherungen** auf **Speicher: Nicht konfiguriert**, um ein Speicherkonto zu konfigurieren.

	![Speicherkonto auswählen][ChooseStorageAccount]
	
4. Wählen Sie das Sicherungsziel durch Auswahl eines **Speicherkontos** und **Containers** aus. Das Speicherkonto muss zu demselben Abonnement wie die Web-App gehören, die Sie sichern möchten. Bei Bedarf können Sie auf den entsprechenden Blättern ein neues Speicherkonto oder einen neuen Container erstellen. Wenn Sie fertig sind, klicken Sie auf **Auswählen**.
	
	![Speicherkonto auswählen](./media/web-sites-backup/02ChooseStorageAccount1.png)
	
5. Klicken Sie auf dem geöffneten Blatt **Sicherungseinstellungen konfigurieren** auf **Datenbankeinstellungen**. Wählen Sie dann die Datenbanken aus, die in die Sicherungen einbezogen werden sollen (SQL-Datenbank oder MySQL), und klicken Sie auf **OK**.

	![Speicherkonto auswählen](./media/web-sites-backup/03ConfigureDatabase.png)

	> [AZURE.NOTE]Damit eine Datenbank in dieser Liste angezeigt wird, muss die zugehörige Verbindungszeichenfolge im Portal auf dem Blatt **Web-App-Einstellungen** im Abschnitt **Verbindungszeichenfolgen** angegeben sein.

6. Klicken Sie auf dem Blatt **Sicherungseinstellungen konfigurieren** auf **Speichern**.
6. Wählen Sie auf dem Blatt **Sicherungen** das **Sicherungsziel** aus. Sie müssen ein vorhandenes Speicherkonto und einen Container auswählen.
7. Klicken Sie in der Befehlsleiste des Blatts **Sicherungen** auf **Jetzt sichern**.
	
	![Schaltfläche "Backup Now"][BackUpNow]
	
	Während des Sicherungsvorgangs wird eine Fortschrittsmeldung angezeigt.
	

Sie können jederzeit eine manuelle Sicherung vornehmen.

<a name="automatedbackups"></a>
## Konfigurieren automatischer Sicherungen

1. Klicken Sie im Blatt **Sicherungen** auf **Zeitplan: Nicht konfiguriert**. 

	![Speicherkonto auswählen](./media/web-sites-backup/05ScheduleBackup.png)
	
1. Legen Sie auf dem Blatt **Backup Schedule Settings** die Option **Geplante Sicherung** auf **Ein** fest. Konfigurieren Sie dann den Sicherungszeitplan wie gewünscht, und klicken Sie auf **OK**.
	
	![Automatisierte Sicherungen aktivieren][SetAutomatedBackupOn]
	
4. Klicken Sie auf dem geöffneten Blatt **Sicherungseinstellungen konfigurieren** auf **Speichereinstellungen**, wählen Sie dann das Sicherungsziel durch Auswahl eines **Speicherkontos** und **Containers** aus. Das Speicherkonto muss zu demselben Abonnement wie die Web-App gehören, die Sie sichern möchten. Bei Bedarf können Sie auf den entsprechenden Blättern ein neues Speicherkonto oder einen neuen Container erstellen. Wenn Sie fertig sind, klicken Sie auf **Auswählen**.
	
	![Speicherkonto auswählen](./media/web-sites-backup/02ChooseStorageAccount1.png)
	
5. Klicken Sie auf dem Blatt **Sicherungseinstellungen konfigurieren** auf **Datenbankeinstellungen**. Wählen Sie dann die Datenbanken aus, die in die Sicherungen einbezogen werden sollen (SQL-Datenbank oder MySQL), und klicken Sie auf **OK**.

	![Speicherkonto auswählen](./media/web-sites-backup/03ConfigureDatabase.png)

	> [AZURE.NOTE]Damit eine Datenbank in dieser Liste angezeigt wird, muss die zugehörige Verbindungszeichenfolge im Portal auf dem Blatt **Web-App-Einstellungen** im Abschnitt **Verbindungszeichenfolgen** angegeben sein.

6. Klicken Sie auf dem Blatt **Sicherungseinstellungen konfigurieren** auf **Speichern**.

<a name="notes"></a>
## Hinweise

* Stellen Sie sicher, dass Sie die Verbindungszeichenfolgen für jede Ihrer Datenbanken ordnungsgemäß auf dem Blatt **Web-App-Einstellungen** in **Einstellungen** angeben, damit die Sicherungs- und Wiederherstellungsfunktion die Datenbanken einschließen kann.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

<a name="partialbackups"></a>
## Sichern eines Teils Ihrer Web-App

Manchmal möchten Sie nicht alles in Ihrer Web-App sichern. Hier sind einige Beispiele:

-	Sie [richten wöchentliche Sicherungen](web-sites-backup.md#configure-automated-backups) der Web-App ein, die statische Inhalte enthält, die sich nie ändern, z. B. alte Blogbeiträge oder Bilder.
-	Ihre Web-App verfügt über mehr als 10 GB an Inhalten (der maximale Umfang, der auf einmal gesichert werden kann).
-	Sie möchten die Protokolldateien nicht sichern.

Bei Teilsicherungen können Sie genau auswählen, welche Dateien gesichert werden sollen.

### Ausschließen von Dateien aus der Sicherung

Erstellen Sie zum Ausschließen von Dateien und Ordnern aus der Sicherung eine `_backup.filter`-Datei im Ordner "wwwroot" der Web-App, und geben Sie die Liste der Dateien und Ordner an, die Sie dort ausschließen möchten. Eine einfache Zugriffsmöglichkeit ist die [Kudu-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console).

Stellen Sie sich vor, Sie verfügen über eine Website mit Protokolldateien und statischen Bildern aus früheren Jahren, die sich nicht ändern. Sie haben bereits eine vollständige Sicherung der Web-App erstellt, die die alten Bilder enthält. Jetzt möchten Sie die Web-App täglich sichern, aber Sie möchten nicht für die Speicherung von Protokolldateien oder statischen Bilddateien bezahlen, die sich nie ändern.

![Ordner "Logs"][LogsFolder] ![Ordner "Images"][ImagesFolder]
	
Die unten angegebenen Schritte zeigen, wie Sie diese Dateien aus der Sicherung ausschließen.

1. Wechseln Sie zu `http://{yourapp}.scm.azurewebsites.net/DebugConsole`, und identifizieren Sie die Ordner, die Sie von Ihren Sicherungen ausschließen möchten. In diesem Beispiel würden Sie die folgenden Dateien und Ordner ausschließen, die in dieser Benutzeroberfläche dargestellt werden:

		D:\home\site\wwwroot\Logs
		D:\home\LogFiles
		D:\home\site\wwwroot\Images\2013
		D:\home\site\wwwroot\Images\2014
		D:\home\site\wwwroot\Images\brand.png

	[AZURE.NOTE]Die letzte Zeile zeigt, dass Sie einzelne Dateien und Ordner ausschließen können.

2. Erstellen Sie eine Datei namens `_backup.filter`, und fügen Sie die oben aufgeführte Liste in die Datei ein, aber entfernen Sie `D:\home`. Geben Sie ein Verzeichnis oder eine Datei pro Zeile an. Der Inhalt der Datei sollte folgendermaßen aussehen:

    \\site\\wwwroot\\Logs \\LogFiles \\site\\wwwroot\\Images\\2013 \\site\\wwwroot\\Images\\2014 \\site\\wwwroot\\Images\\brand.png

3. Laden Sie diese Datei in das Verzeichnis `D:\home\site\wwwroot` Ihrer Website hoch. Verwenden Sie dazu [ftp](web-sites-deploy.md#ftp) oder eine andere Methode. Wenn Sie möchten, können Sie die Datei direkt in `http://{yourapp}.scm.azurewebsites.net/DebugConsole` erstellen und den Inhalt dort einfügen.

4. Führen Sie die Sicherungen aus, wie Sie es gewohnt sind: [manuell](#create-a-manual-backup) oder [automatisch](#configure-automated-backups).

Alle Dateien und Ordner, die in `_backup.filter` angegeben sind, werden jetzt aus der Sicherung ausgeschlossen. In diesem Beispiel werden die Protokolldateien und die Bilddateien "2013" und "2014" sowie "brand.png" nicht mehr gesichert.

>[AZURE.NOTE]Sie stellen Teilsicherungen Ihrer Website genauso wieder her, wie Sie eine [normale Sicherung wiederherstellen](web-sites-restore.md) würden. Der Wiederherstellungsvorgang wird richtig ausgeführt.
>
>Bei der Wiederherstellung einer vollständigen Sicherung wird der gesamte Inhalt der Website durch den Inhalt der Sicherung ersetzt. Wenn eine Datei auf der Website vorhanden ist, aber nicht in der Sicherung, wird sie gelöscht. Wenn aber eine Teilsicherung wiederhergestellt wird, bleiben alle Inhalte von ausgeschlossenen Verzeichnissen bzw. alle ausgeschlossenen Dateien unverändert.

<a name="aboutbackups"></a>

## Speichern von Sicherungen

Nachdem Sie eine oder mehrere Sicherungen für Ihre Web-App vorgenommen haben, werden die Sicherungen auf dem Blatt **Container** in Ihrem Speicherkonto sowie in Ihrer Web-App angezeigt. Im Speicherkonto besteht jede Sicherung aus einer ZIP-Datei mit den gesicherten Daten und einer XML-Datei, die ein Manifest des ZIP-Dateiinhalts enthält. Sie können diese Dateien extrahieren und durchsuchen, wenn Sie auf die Sicherungen zugreifen möchten, ohne eine Web-App-Wiederherstellung auszuführen.

Die Datenbanksicherung für die Web-App wird im Stammverzeichnis der ZIP-Datei gespeichert. Bei SQL-Datenbanken ist dies eine BACPAC-Datei (ohne Dateierweiterung), die importiert werden kann. Schritte zum Erstellen einer neuen SQL-Datenbank auf Basis des BACPAC-Exports finden Sie im Artikel [Importieren einer BACPAC-Datei zum Erstellen einer neuen Benutzerdatenbank](http://technet.microsoft.com/library/hh710052.aspx).

> [AZURE.WARNING]Wenn Sie die Dateien im Container **websitebackups** ändern, kann die Sicherung ungültig werden und nicht mehr wiederhergestellt werden.

<a name="nextsteps"></a>
## Nächste Schritte
Informationen zum Wiederherstellen von Web-Apps aus einer Sicherung finden Sie unter [Wiederherstellen von Web-Apps in Azure App Service](web-sites-restore.md).

Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](/pricing/free-trial/).

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!---HONumber=AcomDC_1217_2015-->