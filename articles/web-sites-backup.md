<properties linkid="web-sites-backup" urlDisplayName="Azure Web Sites Backups" pageTitle="Azure Web Sites Backups" metaKeywords="Azure Web Sites, Backups" description="Learn how to create backups of your Azure web sites." metaCanonical="" services="web-sites" documentationCenter="" title="Azure Web Sites Backups" authors="timamm" solutions="" manager="paulettm" editor="mollybos" />

Sicherungen von Azure-Websites
==============================

Die Funktion zum Sichern und Wiederherstellen von Azure-Websites ermöglicht einfaches Erstellen von Website-Sicherungen - manuell oder automatisch. Sie können die Website in einem vorherigen Zustand wiederherstellen oder eine neue Website basierend auf einer Website-Sicherung erstellen.

Informationen zum Wiederherstellen einer Azure-Website aus einer Sicherung finden Sie unter [Wiederherstellen von Azure-Websites](http://www.windowsazure.com/de-de/documentation/articles/web-sites-restore/).

Themen in diesem Artikel
------------------------

-   [Was wird gesichert?](#whatsbackedup)
-   [Anforderungen und Einschränkungen](#requirements)
-   [Erstellen einer manuellen Sicherung](#manualbackup)
-   [Konfigurieren von automatisierten Sicherungen](#automatedbackups)
-   [Speichern von Sicherungen](#aboutbackups)
-   [Hinweise](#notes)
-   [Nächste Schritte](#nextsteps)
    -   [Weitere Informationen zu Speicherkonten](#moreaboutstorage)

## Was wird gesichert? Azure-Websites sichern folgende Informationen:

-   Website-Konfiguration
-   Dateiinhalte der Website
-   Jede SQL Server- und jede MySQL-Datenbank, die mit der Website verbunden ist (Sie können auswählen, welche gesichert werden sollen)

Diese Informationen werden in dem von Ihnen angegebenen Azure-Speicherkonto gesichert.

> [WACOM.NOTE] Jede Sicherung ist eine vollständige Offline-Kopie Ihrer Website, es gibt keine inkrementellen Updates.

## Anforderungen und Einschränkungen

-   Die Funktion zum Sichern und Wiederherstellen erfordert, dass sich die Website auf einer Standardebene befindet. Weitere Informationen zum Skalieren von Websites auf der Standardebene finden Sie unter [Skalieren von Websites](http://www.windowsazure.com/de-de/documentation/articles/web-sites-scale/).

-   Die Funktion zum Sichern und Wiederherstellen erfordert ein Azure-Speicherkonto, das zum selben Abonnement wie die Website gehört, die Sie sichern möchten. Falls Sie noch kein Speicherkonto haben, können Sie es erstellen, indem Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Speicher** (Rastersymbol) klicken und anschließend unten in der Befehlsleiste auf **Neu** klicken. Weitere Informationen zu Azure-Speicherkonten erhalten Sie unter den [Links](#moreaboutstorage) am Ende dieses Artikels.

## Erstellen einer manuellen Sicherung

1.  Wählen Sie im Azure-Portal für Ihre Website die Registerkarte **Backups** aus.

    ![Seite "Backups"](./media/web-sites-backup/01ChooseBackupsPage.png)

2.  Wählen Sie das Speicherkonto aus, in dem die Website gesichert werden soll. Das Speicherkonto muss zum selben Abonnement wie die Website gehört, die Sie sichern möchten.

    ![Speicherkonto auswählen](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  Wählen Sie unter der Option **Included Databases** die Datenbanken aus, die mit Ihrer Website verbunden sind (SQL Server oder MySQL), die gesichert werden soll.

    ![Einzuschließende Datenbanken auswählen](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] Damit eine Datenbank in dieser Liste angezeigt wird, muss ihre Verbindungszeichenfolge im Abschnitt **Verbindungszeichenfolgen** der Registerkarte "Konfigurieren" im Portal angegeben sein.

4.  Klicken Sie in der Befehlsleiste auf **Backup Now**.

    ![Schaltfläche "Backup Now"](./media/web-sites-backup/04BackUpNow.png)

    Während des Sicherungsvorgangs wird eine Fortschrittsmeldung angezeigt:

    ![Fortschrittsmeldung der Sicherung](./media/web-sites-backup/05BackupProgress.png)

Sie können jederzeit eine manuelle Sicherung vornehmen. Während der Vorschau können maximal 2 manuelle Sicherungen innerhalb von 24 erstellt werden (kann künftig geändert werden).

## Konfigurieren von automatisierten Sicherungen

1.  Stellen Sie auf der Seite "Backups" die Option **Automated Backup** auf ON.

    ![Automatisierte Sicherungen aktivieren](./media/web-sites-backup/06SetAutomatedBackupOn.png)

2.  Wählen Sie das Speicherkonto aus, in dem die Website gesichert werden soll. Das Speicherkonto muss zum selben Abonnement wie die Website gehört, die Sie sichern möchten.

    ![Speicherkonto auswählen](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  Geben Sie im Feld **Häufigkeit** an, wie oft automatisierte Sicherungen erstellt werden sollen. (Während der Vorschau ist nur die Anzahl von Tagen als Zeiteinheit verfügbar.)

    ![Sicherungshäufigkeit auswählen](./media/web-sites-backup/07Frequency.png)

    Die Anzahl der Tage muss zwischen 1 und 90 liegen (von einmal täglich bis einmal alle 90 Tage).

4.  Legen Sie mit der Option **Startdatum** Datum und Uhrzeit fest, ab wann automatisierte Sicherungen gestartet werden sollen.

    ![Startdatum auswählen](./media/web-sites-backup/08StartDate.png)

    Die Uhrzeit kann ich halbstündigen Schritten festgelegt werden.

    ![Startzeit auswählen](./media/web-sites-backup/09StartTime.png)

    > [WACOM.NOTE] Azure speichert die Sicherungszeit im UTC-Format, zeigt sie jedoch entsprechend der Systemzeit des Computers an, auf dem Sie das Portal anzeigen.

5.  Wählen Sie im Abschnitt **Included Databases** die Datenbanken aus, die mit Ihrer Website verbunden sind (SQL Server oder MySQL), die gesichert werden soll. Damit eine Datenbank in der Liste angezeigt wird, muss ihre Verbindungszeichenfolge im Abschnitt **Verbindungszeichenfolgen** der Registerkarte "Konfigurieren" im Portal angegeben sein.

    ![Einzuschließende Datenbanken auswählen](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] Wenn Sie eine oder mehrere Datenbanken in die Sicherung einschließen möchten und eine Häufigkeit von weniger als 7 Tagen angeben, werden Sie darauf hingewiesen, dass häufige Sicherungen die Kosten für die Datenbank erhöhen können.

6.  Klicken Sie in der Befehlsleiste auf **Speichern**, um die Konfigurationsänderungen zu speichern (oder wählen Sie **Verwerfen** aus, wenn Sie sie nicht speichern möchten).

    ![Schaltfläche "Save"](./media/web-sites-backup/10SaveIcon.png)

## Speichern von Sicherungen

Nachdem Sie eine oder mehrere Sicherungen erstellt haben, werden diese auf der Registerkarte "Container" Ihres Speicherkontos angezeigt. Die Sicherungen befinden sich in einem Container namens **websitebackups**. Jede Sicherung besteht aus einer ZIP-Datei, welche die gesicherten Daten enthält, und einer XML-Datei, die ein Manifest des ZIP-Dateiinhalts enthält.

Die Namen der ZIP- und der XML-Sicherungsdatei bestehen aus dem Namen der Website, gefolgt von einem Unterstrich und dem Zeitstempel der Sicherungserstellung. Dieser Zeitstempel gibt das Datum im Format JJJJMMTT (in Ziffern ohne Leerzeichen) sowie die UTC-Zeit im 24-Stunden-Format (z. B. fabrikam\_201402152300.zip) an. Der Inhalt dieser Dateien kann entpackt und durchsucht werden, falls Sie auf die Sicherungen zugreifen möchten, ohne eine Website-Wiederherstellung durchzuführen.

> [WACOM.NOTE] Wenn Sie die Dateien im Container **websitebackups** ändern, kann die Sicherung ungültig werden und nicht mehr wiederhergestellt werden.

## Hinweise

-   Stellen Sie sicher, dass Sie die Verbindungszeichenfolgen für jede Ihrer Datenbanken ordnungsgemäß auf der Registerkarte "Konfigurieren" der Website angeben, damit die Sicherungs- und Wiederherstellungsfunktion die Datenbanken einschließen kann.
-   Während der Vorschau sind Sie für das Verwalten des gesicherten Inhalts verantwortlich, der in Ihrem Speicherkonto gespeichert wurde. Wenn Sie eine Sicherung aus dem Speicherkonto löschen und keine Kopie davon gemacht haben, können Sie diese Sicherung später nicht wiederherstellen.
-   Auch wenn Sie mehrere Websites im selben Speicherkonto sichern können, sollten Sie aus Verwaltungsgründen für jede Website ein separates Speicherkonto erstellen.
-   Während der Vorschau sind die Sicherungs- und Wiederherstellungsvorgänge nur über das Azure-Verwaltungsportal verfügbar.

## Nächste Schritte 
Informationen zum Wiederherstellen einer Azure-Website aus einer Sicherung finden Sie unter [Wiederherstellen von Azure-Websites](http://www.windowsazure.com/de-de/documentation/articles/web-sites-restore/).

### Weitere Informationen zu Speicherkonten

[Was ist ein Speicherkonto?](http://www.windowsazure.com/de-de/documentation/articles/storage-whatis-account/)

[Gewusst wie: Speicherkonto erstellen](http://www.windowsazure.com/de-de/documentation/articles/storage-create-storage-account/)

[Überwachen eines Speicherkontos](http://www.windowsazure.com/de-de/documentation/articles/storage-monitor-storage-account/)

[Kosten des Windows Azure-Speichers](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

