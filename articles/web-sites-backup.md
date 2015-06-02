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

<a name="aboutbackups"></a>
## Speichern von Sicherungen

Nachdem Sie eine oder mehrere Sicherungen vorgenommen haben, werden die Sicherungen auf dem Blatt **Container** zu Ihrem **Speicherkonto** sowie in Ihrer Web-App angezeigt. Im **Speicherkonto** besteht jede Sicherung aus einer ZIP-Datei mit den gesicherten Daten und einer XML-Datei, die ein Manifest des ZIP-Dateiinhalts enthält.

Die Namen der ZIP- und der XML-Sicherungsdatei bestehen aus dem Namen der Web-App, gefolgt von einem Unterstrich und dem Zeitstempel der Sicherungserstellung. Dieser Zeitstempel gibt das Datum im Format JJJJMMTT (in Ziffern ohne Leerzeichen) sowie die UTC-Zeit im 24-Stunden-Format (z. B. fabrikam_201402152300.zip) an. Der Inhalt dieser Dateien kann entpackt und durchsucht werden, falls Sie auf die Sicherungen zugreifen möchten, ohne eine Web-App-Wiederherstellung durchzuführen.

Die mit der ZIP-Datei gespeicherte XML-Datei enthält den Datenbank-Dateinamen unter *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Die Datenbank-Sicherungsdatei selbst ist im Stamm der ZIP-Datei gespeichert. Bei SQL-Datenbanken ist dies eine BACPAC-Datei (ohne Dateierweiterung), die importiert werden kann. Schritte zum Erstellen einer neuen SQL-Datenbank auf Basis des BACPAC-Exports finden Sie im Artikel [Importieren einer BACPAC-Datei zum Erstellen einer neuen Benutzerdatenbank](http://technet.microsoft.com/library/hh710052.aspx).

Informationen zum Wiederherstellen einer Azure-Web-App (einschließlich Datenbanken) mithilfe des Azure-Verwaltungsportals finden Sie unter  [Wiederherstellen einer Web-App in Azure App Service](web-sites-restore.md).

> [AZURE.NOTE]Wenn Sie die Dateien im Container **websitebackups** ändern, kann die Sicherung ungültig werden und nicht mehr wiederhergestellt werden.

<a name="notes"></a>
## Hinweise

* Stellen Sie sicher, dass Sie die Verbindungszeichenfolgen für jede Ihrer Datenbanken ordnungsgemäß auf dem Blatt **Web-App-Einstellungen** in **Einstellungen** angeben, damit die Sicherungs- und Wiederherstellungsfunktion die Datenbanken einschließen kann.
* Auch wenn Sie mehrere Web-Apps in demselben Speicherkonto sichern können, sollten Sie aus Verwaltungsgründen für jede Web-App ein separates Speicherkonto erstellen.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

<a name="nextsteps"></a>
## Nächste Schritte
Informationen zum Wiederherstellen von Azure-Web-Apps aus einer Sicherung finden Sie unter [Wiederherstellen von Web-Apps in Azure App Service](web-sites-restore.md).

Informationen zu den ersten Schritten mit Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### Weitere Informationen zu Speicherkonten

[Was ist ein Speicherkonto?](storage-whatis-account.md)

[Erstellen eines Speicherkontos](../storage-create-storage-account/)

[Überwachen eines Speicherkontos](storage-monitor-storage-account.md)

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

<!--HONumber=54-->