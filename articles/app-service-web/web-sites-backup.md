<properties 
	pageTitle="Sichern von Web-Apps in Azure App Service" 
	description="Erfahren Sie, wie Sie Sicherungen Ihrer Web-Apps in Azure App Service erstellen." 
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
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Sichern von Web-Apps in Azure App Service


Die Funktion zum Sichern und Wiederherstellen von [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) ermöglicht die einfache Erstellung manueller oder automatischer Web-App-Sicherungen. Sie können die Web-App in einem früheren Zustand wiederherstellen oder eine neue Web-App basierend auf einer Web-App-Sicherung erstellen.

Informationen zum Wiederherstellen von Azure-Web-Apps aus einer Sicherung finden Sie unter [Wiederherstellen von Web-Apps](web-sites-restore.md).

<a name="whatsbackedup"></a>
## Was wird gesichert? 
Für Web-Apps können die folgenden Informationen gesichert werden:

* Web-App-Konfiguration
* Dateiinhalte der Web-App
* Jede SQL Server- und jede MySQL-Datenbank, die mit der Web-App verbunden ist (Sie können auswählen, welche gesichert werden sollen)

Diese Informationen werden in dem von Ihnen angegebenen Azure-Speicherkonto und -Container gesichert.

> [AZURE.NOTE]Jede Sicherung ist eine vollständige Offlinekopie Ihrer App. Es gibt keine inkrementellen Aktualisierungen.

<a name="requirements"></a>
## Anforderungen und Einschränkungen

* Die Funktion zum Sichern und Wiederherstellen erfordert, dass die Website im Standardmodus ist. Weitere Informationen zum Skalieren von Websites im Standardmodus finden Sie unter [Skalieren von Web-Apps in Azure App Service](web-sites-scale.md). Beachten Sie, dass im Premium-Modus eine größere Anzahl von täglichen Sicherungen als im Standardmodus ausgeführt werden kann.

* Die Funktion zum Sichern und Wiederherstellen erfordert ein Azure-Speicherkonto und einen -Container, die zum selben Abonnement wie die Web-App gehören, die Sie sichern möchten. Falls Sie noch nicht über ein Speicherkonto verfügen, können Sie es erstellen, indem Sie im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) auf dem Blatt **Sicherungen** auf die Schaltfläche **Speicherkonto** klicken. Klicken Sie anschließend auf dem Blatt **Ziel** auf das **Speicherkonto** und den **Container**. Weitere Informationen zu Azure-Speicherkonten erhalten Sie unter den [Links](#moreaboutstorage) am Ende dieses Artikels.

* Das Feature zum Sichern und Wiederherstellen unterstützt bis zu 10 GB an Website- und Datenbankinhalt. In die Operationsprotokolle wird ein Fehler ausgegeben, wenn das Sicherungsfeature nicht fortgesetzt werden kann, weil die Nutzlast diesen Grenzwert überschreitet.

<a name="manualbackup"></a>
## Erstellen einer manuellen Sicherung

1. Wählen Sie im Azure-Portal auf dem Blatt "Web-Apps" Ihre Web-App aus. Damit zeigen Sie Details der Web-App auf einem neuen Blatt an.
2. Wählen Sie die Option **Einstellungen** aus. Das Blatt **Einstellungen** wird angezeigt, auf dem Sie Ihre Web-App ändern können.
	
	![Seite 'Sicherungen'][ChooseBackupsPage]

3. Wählen Sie auf dem Blatt **Einstellungen** die Option **Sicherungen** aus. Das Blatt **Sicherungen** wird angezeigt.
	
4. Wählen Sie auf dem Blatt **Sicherungen** das Sicherungsziel durch Auswahl von **Speicherkonto** und **Container** aus. Das Speicherkonto muss zu demselben Abonnement wie die Web-App gehören, die Sie sichern möchten.
	
	![Speicherkonto auswählen][ChooseStorageAccount]
	
5. Wählen Sie mithilfe der Option **Enthaltene Datenbanken** auf dem Blatt **Sicherungen** die Datenbanken aus, die mit der zu sichernden Web-App verbunden sind (SQL Server oder MySQL).

	> [AZURE.NOTE]Damit eine Datenbank in dieser Liste angezeigt wird, muss die zugehörige Verbindungszeichenfolge im Portal auf dem Blatt **Web-App-Einstellungen** im Abschnitt **Verbindungszeichenfolgen** angegeben sein.
	
6. Wählen Sie auf dem Blatt **Sicherungen** das **Sicherungsziel** aus. Sie müssen ein vorhandenes Speicherkonto und einen Container auswählen.
7. Klicken Sie in der Befehlsleiste auf **Backup Now**.
	
	![Schaltfläche "Backup Now"][BackUpNow]
	
	Während des Sicherungsvorgangs wird eine Fortschrittsmeldung angezeigt.
	

Sie können jederzeit eine manuelle Sicherung vornehmen.

<a name="automatedbackups"></a>
## Konfigurieren automatischer Sicherungen

1. Legen Sie auf dem Blatt **Sicherungen** die Option **Geplante Sicherung** auf „EIN“ fest.
	
	![Automatisierte Sicherungen aktivieren][SetAutomatedBackupOn]
	
2. Wählen Sie das Speicherkonto aus, in dem die Web-App gesichert werden soll. Das Speicherkonto muss zu demselben Abonnement wie die Web-App gehören, die Sie sichern möchten.
	
	![Speicherkonto auswählen][ChooseStorageAccount]
	
3. Geben Sie im Feld **Häufigkeit** an, wie oft automatisierte Sicherungen erstellt werden sollen. Die Anzahl der Tage muss zwischen 1 und 90 liegen (von einmal täglich bis einmal alle 90 Tage).
	
4. Verwenden Sie die Option **Startdatum**, um das Startdatum und eine Uhrzeit für den Beginn der automatisierten Sicherungen festzulegen.
	
	> [AZURE.NOTE]Azure speichert die Sicherungszeit im UTC-Format, zeigt sie jedoch entsprechend der Systemzeit des Computers an, auf dem Sie das Portal anzeigen.
	
5. Wählen Sie im Abschnitt **Enthaltene Datenbanken** die Datenbanken aus, die mit der zu sichernden Web-App verbunden sind (SQL Server oder MySQL). Damit eine Datenbank in der Liste angezeigt wird, muss die zugehörige Verbindungszeichenfolge im Portal auf dem Blatt **Web-App-Einstellungen** im Abschnitt **Verbindungszeichenfolgen** angegeben sein.
	
	> [AZURE.NOTE]Wenn Sie eine oder mehrere Datenbanken in die Sicherung einschließen möchten und eine Häufigkeit von weniger als 7 Tagen angeben, werden Sie darauf hingewiesen, dass häufige Sicherungen die Kosten für die Datenbank erhöhen können.
	
6. Legen Sie außerdem die **Aufbewahrung in Tagen** auf die Anzahl der Tage fest, die die Sicherung beibehalten werden soll.
7. Klicken Sie in der Befehlsleiste auf **Speichern**, um die Konfigurationsänderungen zu speichern (oder wählen Sie **Verwerfen** aus, wenn Sie sie nicht speichern möchten).
	
	![Schaltfläche "Save"][SaveIcon]

<a name="notes"></a>
## Hinweise

* Stellen Sie sicher, dass Sie die Verbindungszeichenfolgen für jede Ihrer Datenbanken ordnungsgemäß auf dem Blatt **Web-App-Einstellungen** in **Einstellungen** angeben, damit die Sicherungs- und Wiederherstellungsfunktion die Datenbanken einschließen kann.
* Auch wenn Sie mehrere Web-Apps in demselben Speicherkonto sichern können, sollten Sie aus Verwaltungsgründen für jede Web-App ein separates Speicherkonto erstellen.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

<a name="partialbackups"></a>
## Sicherung nur ein Teil der site

Manchmal möchten Sie alle Dateien auf Ihrer Website sichern, insbesondere dann, wenn Sie Ihre Website regelmäßig sichern, oder wenn Ihr Standort mehr als 10 GB Inhalt hat (das ist der maximale Betrag, die zu einem Zeitpunkt gesichert werden kann).

Beispielsweise empfiehlt nicht, um die Protokolldateien zu sichern. Oder, wenn Sie [setup wöchentliche Sicherungen](https://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/#configure-automated-backups) sollten Sie nicht das Speicherkonto mit statischem Inhalt füllen, niemals Änderungen alte Blogbeiträge oder Bilder wie.

Teilsicherungen können Sie genau die Dateien auswählen, die Sie sichern möchten.

###Geben Sie die Dateien nicht gesichert
Sie können eine Liste der Dateien und Ordner von der Sicherung ausschließen erstellen.

Speichern Sie die Liste als eine Textdatei namens _backup.filter im Ordner "Wwwroot" der Website. Eine einfache Möglichkeit, den Zugriff darauf erfolgt über die [Kudu-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console) am `http://{yoursite}.scm.azurewebsites.net/DebugConsole`. 

Die folgenden Anweisungen verwenden die Kudu-Konsole zum Erstellen der _backup.filter-Datei, aber Sie können Ihre bevorzugte Bereitstellungsmethode verwenden, um die Datei dort ablegen.

###Was zu tun ist
Ich habe eine Website, die enthält, Protokolldateien und statische Bilder aus vergangenen Jahren, die nie ändern möchten.

Ich habe bereits eine vollständige Sicherung der Website, die die alte Bilder enthält. Jetzt möchte ich die Website täglich sichern, aber nicht viel Geld für das Speichern von Protokolldateien oder statische Bilddateien, die sich nie ändern soll.

![Ordner "Protokolle"][LogsFolder] ![Ordner "Bilder"][ImagesFolder]
	
Die unten aufgeführten Schritten anzeigen, wie ich diese Dateien aus der Sicherung ausschließen möchten.

####Identifizieren Sie die Dateien und Ordner, die Sie nicht möchten, Sicherung
Dies ist einfach. Ich bereits weiß ich will keine Protokolldateien zu sichern, sollten ausgeschlossen `D:\home\site\wwwroot\Logs`.

Es gibt einen anderen Protokolldateiordner, die alle Azure Web-Apps unter `D:\home\LogFiles`. Lassen Sie uns ausschließen, zu.

Ich möchte auch die Bilder aus früheren Jahren immer wieder zu sichern. Hinzufügen von lassen `D:\home\site\wwwroot\Images\2013` und `D:\home\site\wwwroot\Images\2014` sowie der Liste.

Abschließend sehen wir keine Sicherung der brand.png-Datei im Ordner "Bilder" entweder, nur um zu zeigen, dass wir auch einzelne Dateien in der schwarzen Liste können. Es befindet sich unter `D:\home\site\wwwroot\Images\brand.png`

Damit erhalten wir die folgenden Ordner, die wir nicht möchten, um die Sicherung:

* D:\\home\\site\\wwwroot\\Logs
* D:\\home\\LogFiles
* D:\\home\\site\\wwwroot\\Images\\2013
* D:\\home\\site\\wwwroot\\Images\\2014
* D:\\home\\site\\wwwroot\\Images\\brand.PNG

#### Erstellen der Ausschlussliste
Sie speichern die schwarze Liste von Dateien und Ordnern, die Sie nicht möchten Sicherung in eine Datei namens _backup.filter. Erstellen Sie die Datei, und platzieren Sie es unter `D:\home\site\wwwroot_backup.filter`.

Listen Sie alle Dateien und Ordner, die Sie nicht möchten backup in der _backup.filter-Datei. Sie fügen den vollständigen Pfad relativ zum D:\\home des Ordners oder der Datei, die Sie aus der Sicherung ein Pfadname pro Zeile ausschließen möchten.

Für Meine Website `D:\home\site\wwwroot\Logs` wird `\site\wwwroot\Logs`, `D:\home\LogFiles` wird `\LogFiles`, usw. usw., was in den folgenden Inhalt für meine _backup.filter:

    \site\wwwroot\Logs
    \LogFiles
    \site\wwwroot\Images\2013
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\brand.png

Beachten Sie die Start- `\` am Anfang jeder Zeile. Dies ist wichtig.

###Führen Sie eine Sicherung
Nachdem Sie Sicherungen genauso ausführen können würden Sie normalerweise tun. [Manuell](https://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/#create-a-manual-backup), [automatisch](https://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/#configure-automated-backups), in beiden Fällen ist in Ordnung.

Alle Dateien und Ordner, die in die Filter in aufgeführten fallen die _backup.filter werden aus der Sicherung ausgeschlossen werden. Dies bedeutet, dass jetzt die Protokolldateien und die Bilddateien 2013 und 2014 nicht mehr gesichert werden soll.

###Wiederherstellen der gesicherten Website
Sie wiederherstellen Teilsicherungen Ihrer Website auf die gleiche Weise [eine regelmäßige Sicherung wiederherstellen](https://azure.microsoft.com/de-de/documentation/articles/web-sites-restore/). Sie müssen das richtige tun.

####Technische details
Mit voller (nicht teilweise) Sicherungen normalerweise alle Inhalte auf der Website werden mit den in der Sicherung wird ersetzt. Wenn eine Datei auf der Website, aber nicht in der Sicherung gelöscht wird.

Aber wenn Teilsicherungen wiederherstellen, obwohl jeder, der Inhalt befindet sich in einem Ordner auf schwarzer Liste (z. B. `D:\home\site\wwwroot\images\2014` für meine Site) unverändert ist. Und Schwarz wären einzelne Dateien aufgeführt, dann werde auch gelassen werden nur während der Wiederherstellung.

<a name="aboutbackups"></a>
## Speichern von Sicherungen

Nachdem Sie eine oder mehrere Sicherungen vorgenommen haben, werden die Sicherungen auf dem Blatt **Container** zu Ihrem **Speicherkonto** sowie in Ihrer Web-App angezeigt. Im **Speicherkonto** besteht jede Sicherung aus einer ZIP-Datei mit den gesicherten Daten und einer XML-Datei, die ein Manifest des ZIP-Dateiinhalts enthält.

Die Namen der ZIP- und der XML-Sicherungsdatei bestehen aus dem Namen der Web-App, gefolgt von einem Unterstrich und dem Zeitstempel der Sicherungserstellung. Dieser Zeitstempel gibt das Datum im Format JJJJMMTT (in Ziffern ohne Leerzeichen) sowie die UTC-Zeit im 24-Stunden-Format (z. B. fabrikam_201402152300.zip) an. Der Inhalt dieser Dateien kann entpackt und durchsucht werden, falls Sie auf die Sicherungen zugreifen möchten, ohne eine Web-App-Wiederherstellung durchzuführen.

Die mit der ZIP-Datei gespeicherte XML-Datei enthält den Datenbank-Dateinamen unter *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Die Datenbank-Sicherungsdatei selbst ist im Stamm der ZIP-Datei gespeichert. Bei SQL-Datenbanken ist dies eine BACPAC-Datei (ohne Dateierweiterung), die importiert werden kann. Schritte zum Erstellen einer neuen SQL-Datenbank auf Basis des BACPAC-Exports finden Sie im Artikel [Importieren einer BACPAC-Datei zum Erstellen einer neuen Benutzerdatenbank](http://technet.microsoft.com/library/hh710052.aspx).

Informationen zum Wiederherstellen einer Azure-Web-App (einschließlich Datenbanken) mithilfe des Azure-Verwaltungsportals finden Sie unter  [Wiederherstellen einer Web-App in Azure App Service](web-sites-restore.md).

> [AZURE.NOTE]Wenn Sie die Dateien im Container **websitebackups** ändern, kann die Sicherung ungültig werden und nicht mehr wiederhergestellt werden.

<a name="bestpractices"></a>
##Bewährte Methoden
Was Sie tun, wenn im Notfall Strikes und Sie müssen der Wiederherstellung Ihres Standorts? Stellen Sie sicher, dass Sie vorbereitet sind.

Ja, Sie können Teilsicherungen, aber nehmen Sie mindestens eine vollständige Sicherung der Website zunächst so, dass Sie Ihrer Website Inhalte gesichert haben (Dies ist im schlimmsten Fall Szenario planen). Beim Wiederherstellen von Sicherungen können Sie dann zuerst die vollständige Sicherung des Standorts wiederherstellen und dann die neueste Teilsicherung darauf wiederherstellen.

Erläuterung: sie verwenden können [Bereitstellungsplätze](https://azure.microsoft.com/de-de/documentation/articles/web-sites-staged-publishing/) zum Testen von wiederhergestellten Sites. Sie können sogar den Wiederherstellungsvorgang testen, ohne je die Produktions-Website. Und das Testen der Wiederherstellungsvorgang ist ein [sehr gut](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/). Man weiß nie beim möglicherweise in einige kleinere Problem, wie ich hier beim Wiederherstellen von meinem Blog ausführen und letztendlich verlieren die Hälfte Ihrer Inhalte.

###Eine Allan

Mein Blog beruht die [Ghost](https://ghost.org/) blogplattform. Wie eine verantwortlichen Entwickler habe ich eine Sicherung meiner Site erstellt haben, und alles war. Eines Tages erhielt ich dann eine Meldung, dass eine neue Version von Ghost verfügbar war und konnte ich mein Blog zu aktualisieren. Prima.

Ich eine weitere Sicherung meiner Site zum Sichern der neuesten Blogbeiträge erstellt und zum Aktualisieren von Ghost fortgesetzt.

Auf meinem Produktions-Website.

Ungültige Fehler.

Ist ein Fehler aufgetreten mit der Aktualisierung meiner Startseite zeigte nur einen leeren Bildschirm. "Kein Problem" Ich dachte, "Ich werde einfach die Sicherung wiederherstellen, die ich einfach habe."

Ich das Upgrade wiederhergestellt, gesehen haben alles... außer Blogbeiträge zurückkehren.

WAS???

Stellt sich heraus, in der [Ghost-Upgrade-Hinweise](http://support.ghost.org/how-to-upgrade/) diese Warnung vorhanden ist:

![Können Sie eine Kopie der Datenbank von Content-Daten nutzen, aber Sie sollten keine dies zwar Ghost ausführen. Stoppen Sie es zuerst][GhostUpgradeWarning]

Wenn Sie versuchen, die Daten zu sichern, während Ghost läuft... die Daten nicht tatsächlich gesichert werden.

Wie schade.

Wenn ich die Wiederherstellung auf einem Test-Steckplatz versucht hätte haben zuerst ich dieses Problem betrachtet und nicht verloren gehen alle meine Beiträge.

Dies ist die Lebensdauer. Es kann vorkommen [den besten von uns](http://blog.codinghorror.com/international-backup-awareness-day/).

Testen Sie Ihre Sicherungen.

<a name="nextsteps"></a>
## Nächste Schritte
Informationen zum Wiederherstellen von Azure-Web-Apps aus einer Sicherung finden Sie unter [Wiederherstellen von Web-Apps in Azure App Service](web-sites-restore.md).

Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### Weitere Informationen zu Speicherkonten

[Was ist ein Speicherkonto?](../storage-whatis-account.md)

[Erstellen eines Speicherkontos](../storage-create-storage-account/)

[Überwachen eines Speicherkontos](../storage-monitor-storage-account.md)

[Understanding Azure Storage Billing](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx) (Grundlagen zur Abrechnung von Azure-Speicher, in englischer Sprache)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

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
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->