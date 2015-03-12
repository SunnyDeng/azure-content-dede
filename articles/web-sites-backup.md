<properties 
	pageTitle="Sichern von Azure-Websites" 
	description="Erfahren Sie, wie Sie Sicherungen Ihrer Azure-Websites erstellen." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/19/2014" 
	ms.author="cephalin"/>

#Sichern von Azure-Websites

Die Funktion zum Sichern und Wiederherstellen von Azure-Websites ermöglicht die einfache Erstellung manueller oder automatischer Websitesicherungen. Sie können die Website in einem vorherigen Zustand wiederherstellen oder eine neue Website basierend auf einer Sicherung der Originalwebsite erstellen. 


Informationen zum Wiederherstellen einer Azure-Website aus einer Sicherung finden Sie unter [Wiederherstellen von Azure-Websites](http://azure.microsoft.com/documentation/articles/web-sites-restore/).

##Themen in diesem Artikel

- [Automatische und einfache Sicherung (Video)](#video)
- [Was wird gesichert?](#whatsbackedup)
- [Anforderungen und Einschränkungen](#requirements)
- [Erstellen einer manuellen Sicherung](#manualbackup)
- [Konfigurieren von automatisierten Sicherungen](#automatedbackups)
- [Speichern von Sicherungen](#aboutbackups)
- [Hinweise](#notes)
- [Nächste Schritte](#nextsteps)
	- [Weitere Informationen zu Speicherkonten](#moreaboutstorage)

<a name="video"></a>
##Automatische und einfache Sicherung (Video)

In diesem Video stellen Eduardo Laureano und Scott Hanselman die Sicherung von Azure-Websites vor. (Dauer: 11:43)  

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>
##Was wird gesichert? 
Für Azure-Websites werden die folgenden Informationen gesichert:

* Websitekonfiguration
* Websitedateiinhalt
* Jede SQL Server- und jede MySQL-Datenbank, die mit der Website verbunden ist (Sie können auswählen, welche gesichert werden sollen)

Diese Informationen werden in dem von Ihnen angegebenen Azure-Speicherkonto gesichert. 

> [AZURE.NOTE] Jede Sicherung ist eine vollständige Offlinekopie Ihrer Website. Es gibt keine inkrementellen Updates.

<a name="requirements"></a>
##Anforderungen und Einschränkungen

* Die Funktion zum Sichern und Wiederherstellen erfordert, dass sich die Website auf der Stufe "Standard" befindet. Weitere Informationen zum Skalieren von Websites auf der Stufe "Standard" finden Sie unter [Skalieren von Websites](http://azure.microsoft.com/documentation/articles/web-sites-scale/). 

* Die Funktion zum Sichern und Wiederherstellen setzt ein Azure-Speicherkonto voraus, das zum selben Abonnement wie die zu sichernde Website gehören muss. Falls Sie noch nicht über ein Speicherkonto verfügen, können Sie es erstellen, indem Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Speicher** (Rastersymbol) und anschließend unten in der Befehlsleiste auf **Neu** klicken. Weitere Informationen zu Azure-Speicherkonten erhalten Sie über die [Links](#moreaboutstorage) am Ende dieses Artikels.

<a name="manualbackup"></a>
## Erstellen einer manuellen Sicherung

1. Wählen Sie im Azure-Portal für Ihre Website die Registerkarte **Sicherungen** aus.
	
	![Seite "Backups"][ChooseBackupsPage]
	
2. Wählen Sie das Speicherkonto aus, in dem die Website gesichert werden soll. Das Speicherkonto muss zum selben Abonnement wie die Website gehören, die Sie sichern möchten.
	
	![Auswählen eines Speicherkontos][ChooseStorageAccount]
	
3. Wählen Sie mithilfe der Option **Enthaltene Datenbanken** die Datenbanken aus, die mit der zu sichernden Website verbunden sind (SQL Server oder MySQL). 
	
	![Auswählen der einzuschließenden Datenbanken][IncludedDatabases]

	> [AZURE.NOTE] 	Damit eine Datenbank in dieser Liste angezeigt wird, muss die zugehörige Verbindungszeichenfolge auf der Registerkarte "Konfigurieren" des Portals im Abschnitt **Verbindungszeichenfolgen** angegeben sein.
	
4. Klicken Sie in der Befehlsleiste auf **Jetzt sichern**.
	
	![Schaltfläche "Jetzt sichern"][BackUpNow]
	
	Während des Sicherungsvorgangs wird eine Fortschrittsmeldung angezeigt:
	
	![Fortschrittsmeldung bei der Sicherung][BackupProgress]
	
Sie können jederzeit eine manuelle Sicherung vornehmen. Während der Vorschau können maximal 2 manuelle Sicherungen innerhalb von 24 erstellt werden (kann künftig geändert werden).  

<a name="automatedbackups"></a>
## Konfigurieren von automatisierten Sicherungen

1. Legen Sie auf der Seite "Sicherungen" die Einstellung **Automatisierte Sicherung** auf EIN fest.
	
	![Aktivieren von automatisierten Sicherungen][SetAutomatedBackupOn]
	
2. Wählen Sie das Speicherkonto aus, in dem die Website gesichert werden soll. Das Speicherkonto muss zum selben Abonnement wie die Website gehören, die Sie sichern möchten.
	
	![Auswählen eines Speicherkontos][ChooseStorageAccount]
	
3. Geben Sie im Feld **Häufigkeit** an, wie oft automatisierte Sicherungen erstellt werden sollen. (Während der Vorschau ist nur die Anzahl von Tagen als Zeiteinheit verfügbar.)
	
	![Auswählen der Sicherungshäufigkeit][Frequency]
	
	Die Anzahl der Tage muss zwischen 1 und 90 liegen (von einmal täglich bis einmal alle 90 Tage).
	
4. Verwenden Sie die Option **Startdatum**, um das Startdatum und eine Uhrzeit für den Beginn der automatisierten Sicherungen festzulegen. 
	
	![Auswählen des Startdatums][StartDate]
	
	Die Uhrzeit kann ich halbstündigen Schritten festgelegt werden.
	
	![Auswählen der Startzeit][StartTime]
	
	> [AZURE.NOTE] Azure speichert die Sicherungszeit im UTC-Format, zeigt sie jedoch entsprechend der Systemzeit des Computers an, auf dem Sie das Portal anzeigen.
	
5. Wählen Sie im Abschnitt **Enthaltene Datenbanken** die Datenbanken aus, die mit der zu sichernden Website verbunden sind (SQL Server oder MySQL). Damit eine Datenbank in der Liste angezeigt wird, muss die zugehörige Verbindungszeichenfolge auf der Registerkarte "Konfigurieren" des Portals im Abschnitt **Verbindungszeichenfolgen** angegeben sein.
	
	![Auswählen der einzuschließenden Datenbanken][IncludedDatabases]
	
	> [AZURE.NOTE] Wenn Sie eine oder mehrere Datenbanken in die Sicherung einschließen möchten und eine Häufigkeit von weniger als 7 Tagen angeben, werden Sie darauf hingewiesen, dass häufige Sicherungen die Kosten für die Datenbank erhöhen können.
	
6. Klicken Sie in der Befehlsleiste auf **Speichern**, um die Konfigurationsänderungen zu speichern (oder wählen Sie **Verwerfen**, wenn Sie die Änderungen nicht speichern möchten).
	
	![Schaltfläche "Speichern"][SaveIcon]

<a name="aboutbackups"></a>
## Speichern von Sicherungen

Nachdem Sie eine oder mehrere Sicherungen erstellt haben, werden diese auf der Registerkarte "Container" Ihres Speicherkontos angezeigt. Die Sicherungen befinden sich in einem Container namens **websitebackups**. Jede Sicherung besteht aus einer ZIP-Datei, welche die gesicherten Daten enthält, und einer XML-Datei, die ein Manifest des ZIP-Dateiinhalts enthält. 

Die Namen der ZIP- und der XML-Sicherungsdatei bestehen aus dem Namen der Website, gefolgt von einem Unterstrich und dem Zeitstempel der Sicherungserstellung. Dieser Zeitstempel gibt das Datum im Format JJJJMMTT (in Ziffern ohne Leerzeichen) sowie die UTC-Zeit im 24-Stunden-Format (z. B. fabrikam_201402152300.zip) an. Der Inhalt dieser Dateien kann entpackt und durchsucht werden, falls Sie auf die Sicherungen zugreifen möchten, ohne eine Websitewiederherstellung durchzuführen.

Die mit der ZIP-Datei gespeicherte XML-Datei enthält den Datenbankdateinamen unter *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

Die Datenbank-Sicherungsdatei selbst ist im Stamm der ZIP-Datei gespeichert. Bei SQL-Datenbanken ist dies eine BACPAC-Datei (ohne Dateierweiterung), die importiert werden kann. Wenn Sie eine neue SQL-Datenbank erstellen möchten, die auf dem BACPAC-Export basiert, führen Sie die Schritte im Artikel [Importieren einer BACPAC-Datei zum Erstellen einer neuen Benutzerdatenbank](http://technet.microsoft.com/library/hh710052.aspx) aus.

Informationen zum Wiederherstellen einer Azure-Website (einschließlich Datenbank) mithilfe des Azure-Verwaltungsportals finden Sie unter [Wiederherstellen einer Microsoft Azure-Website]( http://azure.microsoft.com/documentation/articles/web-sites-restore/).

> [AZURE.NOTE] Wenn Sie die Dateien im Container **websitebackups** ändern, kann die Sicherung ungültig werden und nicht mehr wiederhergestellt werden.

<a name="notes"></a>
## Hinweise

* Stellen Sie sicher, dass Sie die Verbindungszeichenfolgen für jede Ihrer Datenbanken ordnungsgemäß auf der Registerkarte "Konfigurieren" der Website angeben, damit die Sicherungs- und Wiederherstellungsfunktion die Datenbanken einschließen kann.
* Während der Vorschau sind Sie für das Verwalten des gesicherten Inhalts verantwortlich, der in Ihrem Speicherkonto gespeichert wurde. Wenn Sie eine Sicherung aus dem Speicherkonto löschen und keine Kopie davon gemacht haben, können Sie diese Sicherung später nicht wiederherstellen. 
* Auch wenn Sie mehrere Websites im selben Speicherkonto sichern können, sollten Sie aus Verwaltungsgründen für jede Website ein separates Speicherkonto erstellen.
* Während der Vorschau sind die Sicherungs- und Wiederherstellungsvorgänge nur über das Azure-Verwaltungsportal verfügbar.

<a name="nextsteps"></a>
## Nächste Schritte
Informationen zum Wiederherstellen einer Azure-Website aus einer Sicherung finden Sie unter [Wiederherstellen von Azure-Websites](http://azure.microsoft.com/documentation/articles/web-sites-restore/).

Informationen zu den ersten Schritten in Azure finden Sie unter [Kostenlose Microsoft Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### Weitere Informationen zu Speicherkonten

[Was ist ein Speicherkonto?](http://azure.microsoft.com/documentation/articles/storage-whatis-account/)

[Vorgehensweise: Erstellen eines Speicherkontos](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/)

[Überwachen eines Speicherkontos](http://azure.microsoft.com/documentation/articles/storage-monitor-storage-account/)

[Grundlegendes zu den Kosten des Microsoft Azure-Speichers](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

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




<!--HONumber=42-->
