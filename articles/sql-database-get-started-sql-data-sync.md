<properties linkid="manage-services-sql-databases-datasync" urlDisplayName="How to sync data" pageTitle="Getting started with SQL Databases Data Sync" metaKeywords="" description="" metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Data Sync" authors="" solutions="" manager="" editor="" />

Erste Schritte mit Azure SQL Data Sync
======================================

In diesem Lernprogramm lernen sie die Grundlagen von Azure SQL Data Sync mit dem Azure (Vorschau)-Portal kennen.

Für dieses Lernprogramm wird minimale vorherige Erfahrung mit SQL Server und Azure SQL Database vorausgesetzt. In diesem Lernprogramm erstellen Sie eine hybride (aus SQL Server- und SQL Database-Instanzen bestehende) Synchronisierungsgruppe, die vollständig gemäß einem von Ihnen festgelegten Zeitplan konfiguriert und synchronisiert wird.

Inhaltsverzeichnis
------------------

-   [Schritt 1: Verbindung mit der Azure SQL-Datenbank](#Connect)
-   [Schritt 2: Hinzufügen eines Client-Agents ()](#AddAgent)
-   [Schritt 3: Registrieren einer SQL Server-Datenbank mit dem Client-Agent](#RegisterSSDB)
-   [Schritt 4: Erstellen einer Synchronisierungsgruppe](#CreateSG)
-   [Schritt 5: Festlegen der zu synchronisierenden Daten](#SyncRules)
-   [Schritt 6: Konfigurieren der Synchronisierungsgruppe](#Configure)

Schritt 1: Verbindung mit der Azure SQL-Datenbank
-------------------------------------------------

1.  Melden Sie sich beim [Verwaltungsportal](http://manage.windowsazure.com) an.

2.  Klicken Sie im linken Bereich auf **SQL DATABASES**.

3.  Klicken Sie unten auf der Seite auf **SYNC**. Wenn Sie auf SYNC klicken, wird eine Liste mit den Elementen geöffnet, die Sie hinzufügen können - **New Sync Group** und **New Sync Agent**.

4.  Klicken Sie auf **New Sync Agent**, um den Assistenten "New SQL Data Sync Agent" zu öffnen.

5.  Wenn Sie bisher noch keinen Agent hinzugefügt haben, **klicken Sie hier, um ihn herunterzuladen**.

    ![Bild1](./media/sql-database-get-started-data-sync/SQLDatabaseScreen-Figure1.PNG)

Schritt 2: Hinzufügen eines Client-Agents
-----------------------------------------

Dieser Schritt ist nur erforderlich, wenn Sie eine lokale SQL Server-Datenbank in Ihre Synchronisierungsgruppe integrieren möchten. Sie können auch zu Schritt 4 "Erstellen einer Synchronisierungsgruppe" springen, wenn Ihre Synchronisierungsgruppe nur über SQL Database-Instanzen verfügt.

### Schritt 2a: Installieren der erforderlichen Software

Stellen Sie sicher, dass die folgende Software auf dem Computer installiert ist, auf dem Sie den Client-Agent installieren.

-   **.NET Framework 4.0**

  Sie können .NET Framework 4.0 [hier](http://go.microsoft.com/fwlink/?linkid=205836) installieren.

-   **Microsoft SQL Server 2008 R2 SP1 System-CLR-Typen (x86)**

  Sie können die Microsoft SQL Server 2008 R2 SP1 System-CLR-Typen (x86) [hier](http://www.microsoft.com/download/en/details.aspx?id=26728) installieren.

-   **Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86)**

  Sie können die Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86) [hier](http://www.microsoft.com/download/en/details.aspx?id=26728) installieren.

### Schritt 2b: Installieren eines neuen Client-Agents

Folgen Sie den Anweisungen in [Installieren eines Client-Agents (SQL Data Sync)](http://msdn.microsoft.com/de-de/library/jj823137.aspx), um den Agent zu installieren.

### Schritt 2c: Beenden des Assistenten "New SQL Data Sync Agent"

1.  Kehren Sie zum Assistenten "New SQL Data Sync Agent" zurück.
2.  Geben Sie dem Agent einen aussagekräftigen Namen.
3.  Wählen Sie in der Dropdown-Liste die **REGION** (Datencenter), in der dieser Agent gehostet werden soll.
4.  Wählen Sie in der Dropdown-Liste das **ABONNEMENT**, in dessen Rahmen dieser Agent gehostet werden soll.
5.  Klicken Sie auf den Pfeil nach rechts.

Schritt 3: Registrieren einer SQL Server-Datenbank mit dem Client-Agent
-----------------------------------------------------------------------

Registrieren Sie nach der Installation des Client-Agents jede lokale SQL Server-Datenbank, die Sie mit dem Agent in eine Synchronisierungsgruppe integrieren möchten. Wenn Sie mit dem Agent eine Datenbank registrieren möchten, folgen Sie den Anweisungen unter [Registrieren einer SQL Server-Datenbank mit dem Client-Agent](http://msdn.microsoft.com/de-de/library/jj823138.aspx).

Schritt 4: Erstellen einer Synchronisierungsgruppe
--------------------------------------------------

### Schritt 4a: Starten des Assistenten "Neue Synchronisierungsgruppe"

1.  Kehren sie zum [Verwaltungsportal](http://manage.windowsazure.com) zurück.
2.  Klicken Sie auf **SQL DATABASES**.
3.  Klicken Sie unten auf der Seite auf **ADD SYNC**, und wählen Sie dann in der Schublade "Neue Synchronisierungsgruppe" aus.

    ![Bild2](./media/sql-database-get-started-data-sync/NewSyncGroup-Figure2.png)

### Schritt 4b: Eingeben der grundlegenden Einstellungen

1.  Gegen Sie einen aussagekräftigen Namen für die Synchronisierungsgruppe ein.
2.  Wählen Sie in der Dropdown-Liste die **REGION** (Datencenter), in der diese Gruppe gehostet werden soll.
3.  Klicken Sie auf den Pfeil nach rechts.

    ![Bild3](./media/sql-database-get-started-data-sync/NewSyncGroupName-Figure3.PNG)

### Schritt 4c: Definieren des Synchronisierungs-Hub

1.  Wählen Sie in der Dropdown-Liste die SQL Database-Instanz, die als Hub für die Synchronisierungsgruppe dienen soll.
2.  Geben Sie die Anmeldeinformationen für diese SQL Database-Instanz ein - **HUB-BENUTZERNAME** und **HUB-KENNWORT**.
3.  Warten Sie, bis SQL Data Sync den BENUTZERNAMEN und das KENNWORT bestätigt. Wenn die Anmeldeinformationen bestätigt sind, wird recht neben dem KENNWORT ein grünes Häkchen angezeigt.
4.  Wählen Sie in der Dropdown-Liste die Richtlinie **KONFLIKTLÖSUNG**.

  **Hub gewinnt** - jede in die Hub-Datenbank geschriebene Änderung wird auch in die Referenzdatenbanken geschrieben. Änderungen im selben Referenzdatensatz werden überschrieben. Konkret bedeutet dies, dass die erste in den Hub geschriebene Änderung an die anderen Datenbanken weitergeleitet wird.

  **Client gewinnt** - in den Hub geschriebene Änderungen werden durch Änderungen in den Referenzdatenbanken überschrieben. Konkret bedeutet dies, dass die letzte in den Hub geschriebene Änderung beibehalten und an die anderen Datenbanken weitergeleitet wird.

1.  Klicken Sie auf den Pfeil nach rechts.

    ![Bild4](./media/sql-database-get-started-data-sync/NewSyncGroupHub-Figure4.PNG)

### Schritt 4d: Hinzufügen einer Referenzdatenbank

Wiederholen Sie diesen Schritt für jede zusätzliche Datenbank, die Sie zur Synchronisierungsgruppe hinzufügen möchten.

1.  Wählen Sie in der Dropdown-Liste die Datenbank aus, die hinzugefügt werden soll.

    Zu den Datenbanken in der Dropdown-Liste gehören sowohl mit dem Agent registrierte SQL Server-Datenbanken als auch SQL Database-Instanzen.

2.  Geben Sie die Anmeldeinformationen für diese Datenbank ein - **BENUTZERNAME** und **KENNWORT**.
3.  Wählen Sie in der Dropdown-Liste die **SYNCHRONISIERUNGSRICHTUNG** für diese Datenbank aus.

    **Bidirektional** - Änderungen in der Referenzdatenbank werden in die Hub-Datenbank geschrieben, und Änderungen in der Hub-Datenbank werden in die Referenzdatenbank geschrieben.

    **Synchronisierung vom Hub** - Die Datenbank empfängt Aktualisierungen vom Hub. Sie sendet keine Änderungen an den Hub.

    **Synchronisierung zum Hub** - Die Datenbank sendet Aktualisierungen an den Hub. Änderungen im Hub werden nicht in die Datenbank geschrieben.

4.  Klicken Sie auf das Häkchen in der rechten unteren Ecke des Assistenten, um die Erstellung der Synchronisierungsgruppe abzuschließen. Warten Sie, bis SQL Data Sync die Anmeldeinformationen bestätigt. Ein grünes Häkchen zeigt an, dass die Anmeldeinformationen bestätigt wurden.

5.  Klicken Sie erneut auf dieses Häkchen. Nun kehren Sie unter SQL Databases zur Seite **SYNC** zurück. Diese Synchronisierungsgruppe wird nun mit Ihren anderen Synchronisierungsgruppen und Agents gelistet.

    ![Bild5](./media/sql-database-get-started-data-sync/NewSyncGroupReference-Figure5.PNG)

Schritt 5: Festlegen der zu synchronisierenden Daten
----------------------------------------------------

In Azure SQL Data Sync können Sie Tabellen und Spalten zur Synchronisierung auswählen. Wenn Sie eine Spalte so filtern möchten, dass nur Zeilen mit bestimmten Werten (z B. Alter\>=65) synchronisiert werden, verwenden Sie das Portal SQL Data Sync in Azure und die Dokumentation zum Auswählen der zu synchronisierenden Tabellen, Spalten und Zeilen, um festzulegen, welche Daten synchronisiert werden sollen.

1.  Kehren sie zum [Verwaltungsportal](http://manage.windowsazure.com) zurück.
2.  Klicken Sie auf **SQL DATABASES**.
3.  Klicken Sie auf die Registerkarte **SYNC**.
4.  Klicken Sie auf den Namen dieser Synchronisierungsgruppe.
5.  Klicken Sie auf die Registerkarte **SYNC RULES**.
6.  Wählen Sie die Datenbank aus, auf die Sie das Synchronisierungsgruppenschema anwenden möchten.
7.  Klicken Sie auf den Pfeil nach rechts.
8.  Klicken Sie auf **REFRESH SCHEMA**.
9.  Wählen Sie für jede Tabelle in der Datenbank die Spalten aus, die synchronisiert werden sollen.
    -   Spalten mit nicht unterstützen Datentypen können nicht ausgewählt werden.
    -   Wenn in einer Tabelle keine Spalten ausgewählt sind, wird die Tabelle nicht in die Synchronisierungsgruppe aufgenommen.
    -   Klicken Sie unten auf dem Bildschirm auf SELECT, um alle Tabellen auszuwählen oder die Auswahl aufzuheben.
10. Klicken Sie auf **SPEICHERN**, und warten Sie, bis die Bereitstellung der Synchronisierungsgruppe abgeschlossen ist.
11. Klicken Sie oben links auf dem Bildschirm (über dem Namen der Synchronisierungsgruppe) auf den Pfeil nach links, um zur Data Sync-Angebotsseite zurückzukehren.

    ![Bild6](./media/sql-database-get-started-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

Schritt 6: Konfigurieren der Synchronisierungsgruppe
----------------------------------------------------

Sie können eine Synchronisierungsgruppe immer synchronisieren, indem Sie unten auf der Data Sync-Angebotsseite auf SYNC klicken. Wenn Sie möchten, dass eine Synchronisierungsgruppe gemäß einem Zeitplan synchronisiert wird, konfigurieren Sie diese Synchronisierungsgruppe.

1.  Kehren sie zum [Verwaltungsportal](http://manage.windowsazure.com) zurück.
2.  Klicken Sie auf **SQL DATABASES**.
3.  Klicken Sie auf die Registerkarte **SYNC**.
4.  Klicken Sie auf den Namen dieser Synchronisierungsgruppe.
5.  Klicken Sie auf die Registerkarte **KONFIGURIEREN**.
6.  **AUTOMATIC SYNC**
    -   Klicken Sie auf **EIN**, um die Synchronisierungsgruppe für eine Synchronisierung in einer bestimmten Frequenz zu synchronisieren. Sie können weiterhin nach Bedarf direkte Synchronisierungen durchführen, indem Sie auf SYNC klicken.
    -   Klicken Sie auf **OFF**, um die Synchronisierungsgruppe so zu konfigurieren, dass sie nur synchronisiert wird, wenn Sie auf SYNC klicken.
7.  **SYNC FREQUENCY**
    -   Wenn AUTOMATIC SYNC eingeschaltet ist, legen Sie die Synchronisierungsfrequenz fest. Die Frequenz muss zwischen 5 Minuten und 1 Monat liegen.
8.  Klicken Sie auf **SPEICHERN**.

![Bild7](./media/sql-database-get-started-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Herzlichen Glückwunsch. Sie haben nun eine Synchronisierungsgruppe erstellt, die sowohl eins SQL-Datenbank-Instanz als auch eine SQL Server-Datenbank enthält.

Nächste Schritte
----------------

Zusätzliche Informationen über SQL Database und SQL Data Sync finden Sie hier:

-   [Melden Sie sich für das Premium-Angebot für SQL Database an] (../sign-up-for-sql-database-premium/)
-   [SQL Data Sync (WA-Portal)](http://msdn.microsoft.com/de-de/library/windowsazure/jj856263.aspx)
-   [Erste Schritte mit Azure SQL Database](../getting-started-w-sql-databases/)
-   [SQL Server Database Lifecycle](http://go.microsoft.com/fwlink/?LinkId=275193)

