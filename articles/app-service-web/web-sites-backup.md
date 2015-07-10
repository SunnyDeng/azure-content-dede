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

1. Legen Sie auf dem Blatt **Sicherungen** die Option **Geplante Sicherung** auf "EIN" fest.
	
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
## Sichern eines Teils Ihrer Website

Es kann vorkommen, dass Sie nicht alle Daten Ihrer Website sichern möchten. Dies gilt besonders, wenn Sie Ihre Website regelmäßig sichern oder wenn die Website über Daten von mehr als 10 GB verfügt (maximale Datenmenge, die auf einmal gesichert werden kann).

Beispielsweise möchten Sie wahrscheinlich keine Protokolldateien sichern. Oder wenn Sie [wöchentliche Sicherungen einrichten](https://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/#configure-automated-backups), möchten Sie Ihr Speicherkonto nicht mit statischem Inhalt füllen, der sich niemals ändert, z. B. alten Blogbeiträgen oder Bildern.

Bei Teilsicherungen können Sie genau auswählen, welche Dateien gesichert werden sollen.

###Angeben von Dateien, die nicht gesichert werden sollen
Sie können eine Liste mit Dateien und Ordnern erstellen, die von der Sicherung ausgeschlossen werden sollen.

Speichern Sie die Liste als Textdatei unter dem Namen _„backup.filter“ im Ordner „wwwroot“ Ihrer Website. Eine einfache Zugriffsmöglichkeit ist die [Kudu-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console) unter `http://{yoursite}.scm.azurewebsites.net/DebugConsole`. 

In der folgenden Anleitung wird die Kudu-Konsole zum Erstellen der Datei _„backup.filter“ verwendet, aber Sie können auch Ihre bevorzugte Bereitstellungsmethode nutzen.

###Vorgehensweise
Ich habe eine Website mit Protokolldateien und statischen Bildern aus den vorhergehenden Jahren, die sich nicht ändern.

Ich verfüge bereits über eine vollständige Sicherung der Website, in der die alten Bilder enthalten sind. Jetzt möchte ich die Website täglich sichern, aber ich möchte nicht für die Speicherung von Protokolldateien oder statischen Bilddateien bezahlen, die sich nie ändern.

![Ordner „Logs“][LogsFolder]
![Ordner „Images“][ImagesFolder]
	
Die unten angegebenen Schritte zeigen, wie ich diese Dateien aus dem Backup ausschließe.

####Identifizieren der Dateien und Ordner, die nicht gesichert werden sollen
Dies ist einfach. Ich weiß bereits, dass ich keine Protokolldateien sichern möchte, und kann `D:\home\site\wwwroot\Logs` daher ausschließen.

Unter `D:\home\LogFiles` ist ein weiterer Ordner mit Protokolldateien vorhanden, der für alle Azure Web-Apps angelegt wird. Diesen Ordner schließen wir auch aus.

Außerdem sollen die Bilder aus den vorherigen Jahren nicht immer wieder gesichert werden. Also fügen wir `D:\home\site\wwwroot\Images\2013` und `D:\home\site\wwwroot\Images\2014` ebenfalls der Liste hinzu.

Zuletzt schließen wir auch die Datei „brand.png“ im Ordner „Images“ aus der Sicherung aus, um zu zeigen, dass dies auch für einzelne Dateien möglich ist. Sie befindet sich unter `D:\home\site\wwwroot\Images\brand.png`.

Wir erhalten also die folgenden Ordner, die nicht gesichert werden sollen:

* D:\\home\\site\\wwwroot\\Logs
* D:\\home\\LogFiles
* D:\\home\\site\\wwwroot\\Images\\2013
* D:\\home\\site\\wwwroot\\Images\\2014
* D:\\home\\site\\wwwroot\\Images\\brand.png

#### Erstellen der Ausschlussliste
Sie speichern die Liste mit den Dateien und Ordnern, die nicht gesichert werden sollen, in einer speziellen Datei mit dem Namen _„backup.filter“. Erstellen Sie die Datei, und legen Sie sie unter `D:\home\site\wwwroot_backup.filter` ab.

Listen Sie alle Dateien und Ordner, die nicht gesichert werden sollen, in der Datei _„backup.filter“ auf. Hierzu fügen Sie den vollständigen Pfad relativ zu „D:\\home“ des Ordners bzw. der Datei hinzu, der bzw. die von der Sicherung ausgeschlossen werden soll. Ein Pfad wird jeweils in eine Zeile eingefügt.

Für meine Website wird `D:\home\site\wwwroot\Logs` also zu `\site\wwwroot\Logs`, `D:\home\LogFiles` wird zu `\LogFiles` usw. Dies führt zu folgendem Inhalt der Datei _„backup.filter“:

    \site\wwwroot\Logs
    \LogFiles
    \site\wwwroot\Images\2013
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\brand.png

Beachten Sie jeweils das Zeichen `` am Anfang jeder Zeile. Dies ist wichtig.

###Durchführen einer Sicherung
Nun können Sie die Sicherungen durchführen, wie Sie es gewohnt sind. Dies ist sowohl [manuell](https://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/#create-a-manual-backup) als auch [automatisch](https://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/#configure-automated-backups) möglich.

Alle Dateien und Ordner, die unter die Filter in der Datei _„backup.filter“ fallen, werden von der Sicherung ausgeschlossen. Dies bedeutet, dass die Protokolldateien und die Bilddateien für 2013 und 2014 nicht mehr gesichert werden.

###Wiederherstellen der gesicherten Website
Sie stellen Teilsicherungen Ihrer Website genauso wieder her, wie Sie eine [normale Sicherung wiederherstellen](https://azure.microsoft.com/de-de/documentation/articles/web-sites-restore/) würden. Der Vorgang wird korrekt ausgeführt.

####Technische Details
Bei vollständigen Sicherungen (keine Teilsicherungen) wird normalerweise der gesamte Inhalt der Website durch den Inhalt der Sicherung ersetzt. Wenn eine Datei auf der Website vorhanden ist, aber nicht in der Sicherung, wird sie gelöscht.

Beim Wiederherstellen von Teilsicherungen bleiben alle Inhalte, die sich in einem der ausgeschlossenen Ordner befinden (z. B. `D:\home\site\wwwroot\images\2014` für meine Website) unverändert. Und falls einzelne Dateien ausgeschlossen wurden, bleiben sie bei der Wiederherstellung ebenfalls unverändert.

<a name="aboutbackups"></a>
## Speichern von Sicherungen

Nachdem Sie eine oder mehrere Sicherungen vorgenommen haben, werden die Sicherungen auf dem Blatt **Container** zu Ihrem **Speicherkonto** sowie in Ihrer Web-App angezeigt. Im **Speicherkonto** besteht jede Sicherung aus einer ZIP-Datei mit den gesicherten Daten und einer XML-Datei, die ein Manifest des ZIP-Dateiinhalts enthält.

Die Namen der ZIP- und der XML-Sicherungsdatei bestehen aus dem Namen der Web-App, gefolgt von einem Unterstrich und dem Zeitstempel der Sicherungserstellung. Dieser Zeitstempel gibt das Datum im Format JJJJMMTT (in Ziffern ohne Leerzeichen) sowie die UTC-Zeit im 24-Stunden-Format (z. B. fabrikam_201402152300.zip) an. Der Inhalt dieser Dateien kann entpackt und durchsucht werden, falls Sie auf die Sicherungen zugreifen möchten, ohne eine Web-App-Wiederherstellung durchzuführen.

Die mit der ZIP-Datei gespeicherte XML-Datei enthält den Datenbank-Dateinamen unter *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Die Datenbank-Sicherungsdatei selbst ist im Stamm der ZIP-Datei gespeichert. Bei SQL-Datenbanken ist dies eine BACPAC-Datei (ohne Dateierweiterung), die importiert werden kann. Schritte zum Erstellen einer neuen SQL-Datenbank auf Basis des BACPAC-Exports finden Sie im Artikel [Importieren einer BACPAC-Datei zum Erstellen einer neuen Benutzerdatenbank](http://technet.microsoft.com/library/hh710052.aspx).

Informationen zum Wiederherstellen einer Azure-Web-App (einschließlich Datenbanken) mithilfe des Azure-Verwaltungsportals finden Sie unter [Wiederherstellen einer Web-App in Azure App Service](web-sites-restore.md).

> [AZURE.NOTE]Wenn Sie die Dateien im Container **websitebackups** ändern, kann die Sicherung ungültig werden und nicht mehr wiederhergestellt werden.

<a name="bestpractices"></a>
##Bewährte Methoden
Wie gehen Sie vor, wenn ein Notfall eintritt und Sie Ihre Website wiederherstellen müssen? Stellen Sie sicher, dass Sie darauf vorbereitet sind.

Sie können Teilsicherungen erstellen, aber Sie sollten zuerst mindestens eine vollständige Sicherung der Website durchführen, damit der gesamte Inhalt Ihrer Website gesichert wird (Planung für das Worst-Case-Szenario). Beim Wiederherstellen von Sicherungen können Sie dann zuerst die vollständige Sicherung der Website wiederherstellen und dann zusätzlich die letzte Teilsicherung wiederherstellen.

Dafür gibt es folgenden Grund: Sie können [Bereitstellungsslots](https://azure.microsoft.com/de-de/documentation/articles/web-sites-staged-publishing/) nutzen, um die wiederhergestellte Website zu testen. Sie können den Wiederherstellungsvorgang sogar testen, ohne dabei die Produktionswebsite einzubeziehen. Und das Testen Ihres Wiederherstellungsvorgangs ist eine [sehr gute Sache](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/). Sie können nie wissen, wann ein kleines Problem auftritt. Dies ist mir passiert, als ich mein Blog wiederherstellen wollte und die Hälfte meiner Daten verloren habe.

###Eine Horror-Story

Mein Blog basiert auf der Blogplattform [Ghost](https://ghost.org/). Wie jeder verantwortungsvolle Entwickler habe ich eine Sicherung meiner Website erstellt, und alles war in Ordnung. Eines Tages erhielt ich dann eine Nachricht, dass eine neue Version von Ghost verfügbar ist und ich ein Upgrade für mein Blog durchführen kann. Prima.

Ich habe eine weitere Sicherung meiner Website erstellt, um die neuesten Blogbeiträge zu sichern, und habe dann das Ghost-Upgrade durchgeführt.

Und zwar für meine Produktionswebsite.

Ein schlimmer Fehler.

Während des Upgrades ist ein Fehler aufgetreten, und meine Startseite war nur noch ein leerer Bildschirm. „Kein Problem“, dachte ich. „Ich stelle einfach die eben angelegte Sicherung wieder her.“

Ich habe das Upgrade wiederhergestellt, und alles war wieder da... mit Ausnahme der Blogbeiträge.

WAS WAR DA LOS?

Es stellte sich heraus, dass in den [Hinweisen zum Ghost-Upgrade](http://support.ghost.org/how-to-upgrade/) die folgende Warnung enthalten ist:

![Sie können eine Kopie Ihrer Datenbank für Inhalte/Daten erstellen, aber Sie sollten dies nicht tun, während Ghost ausgeführt wird. Beenden Sie zuerst die Anwendung.][GhostUpgradeWarning]

Wenn Sie versuchen, die Daten zu sichern, während Ghost ausgeführt wird, ... werden sie nicht gesichert.

Dumm gelaufen.

Wenn ich die Wiederherstellung zuerst für einen Testslot durchgeführt hätte, hätte ich dieses Problem erkannt und nicht alle meine Beiträge verloren.

So ist das Leben. Es kann auch [erfahrenen Benutzern](http://blog.codinghorror.com/international-backup-awareness-day/) passieren.

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
 

<!----HONumber=62-->