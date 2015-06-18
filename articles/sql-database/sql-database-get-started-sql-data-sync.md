<properties 
	pageTitle="Erste Schritte mit SQL-Datenbank-Datensynchronisierung" 
	description="Dieses Lernprogramm unterstützt Sie bei den ersten Schritten mit Azure SQL-Datensynchronisierung (Vorschau)." 
	services="sql-database" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2015" 
	ms.author="spelluru"/>


#Erste Schritte mit Azure SQL-Datensynchronisierung (Vorschauversion)
In diesem Lernprogramm lernen sie die Grundlagen von Azure SQL Data Sync mit dem Azure (Vorschau)-Portal kennen. 

Für dieses Lernprogramm wird minimale vorherige Erfahrung mit SQL Server und Azure SQL Database vorausgesetzt. In diesem Lernprogramm erstellen Sie eine hybride (aus SQL Server- und SQL Database-Instanzen bestehende) Synchronisierungsgruppe, die vollständig gemäß einem von Ihnen festgelegten Zeitplan konfiguriert und synchronisiert wird.


##Schritt 1: Verbindung mit der Azure SQL-Datenbank

1. Melden Sie sich im [Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie im linken Bereich auf **SQL-DATENBANKEN**.

3. Klicken Sie unten auf der Seite auf **SYNCHRONISIEREN**. Wenn Sie auf SYNCHRONISIEREN klicken, wird eine Liste mit den Dingen angezeigt, die Sie hinzufügen können: **Neue Synchronisierungsgruppe** und **Neuer Synchronisierungs-Agent**.

4. Um den Assistenten für den neuen SQL-Datensynchronisierungs-Agent zu starten, klicken Sie auf **Neuer Synchronisierungs-Agent**.

5. Wenn Sie bisher noch keinen Agent hinzugefügt haben, **klicken Sie hier, um ihn herunterzuladen**.

	![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


##Schritt 2: Hinzufügen eines Client-Agents
Dieser Schritt ist nur erforderlich, wenn Sie eine lokale SQL Server-Datenbank in Ihre Synchronisierungsgruppe integrieren möchten. Sie können auch zu Schritt 4 "Erstellen einer Synchronisierungsgruppe" springen, wenn Ihre Synchronisierungsgruppe nur über SQL Database-Instanzen verfügt.

<h3><a id="InstallRequiredSoftware"></a>Schritt 2a: Installieren der erforderlichen Software</h3>
Stellen Sie sicher, dass die folgende Software auf dem Computer installiert ist, auf dem Sie den Client-Agent installieren.

- **.NET Framework 4.0** 

 Sie können .NET Framework 4.0 [hier](http://go.microsoft.com/fwlink/?linkid=205836) installieren.

- **Microsoft SQL Server 2008 R2 SP1 System-CLR-Typen (x86)**

 Sie können die Microsoft SQL Server 2008 R2 SP1 System-CLR-Typen (x86) [hier](http://www.microsoft.com/download/en/details.aspx?id=26728) installieren.

- **Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86)**

 Sie können die Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86) [hier](http://www.microsoft.com/download/en/details.aspx?id=26728) installieren.


<h3><a id="InstallClient"></a>Schritt 2b: Installieren eines neuen Client-Agents</h3>

Folgen Sie den Anweisungen in [Installieren eines Client-Agents (SQL-Datensynchronisierung)](http://msdn.microsoft.com/library/jj823137.aspx), um den Agent zu installieren. 


<h3><a id="RegisterSSDb"></a>Schritt 2c: Beenden des Assistenten "New SQL Data Sync Agent"</h3> 

1. 	Kehren Sie zum Assistenten "New SQL Data Sync Agent" zurück.
2.	Geben Sie dem Agent einen aussagekräftigen Namen.
3.	Wählen Sie im Dropdownmenü **REGION** (Rechenzentrum) die Region aus, in der dieser Agent gehostet werden soll.
4.	Wählen Sie im Dropdownmenü **ABONNEMENT** das Abonnement zum Hosten dieses Agents aus.
5.	Klicken Sie auf den Pfeil nach rechts.



##Schritt 3: Registrieren einer SQL Server-Datenbank mit dem Client-Agent

Registrieren Sie nach der Installation des Client-Agents jede lokale SQL Server-Datenbank, die Sie mit dem Agent in eine Synchronisierungsgruppe integrieren möchten.
Wenn Sie mit dem Agent eine Datenbank registrieren möchten, folgen Sie den Anweisungen unter [Registrieren einer SQL Server-Datenbank mit dem Client-Agent](http://msdn.microsoft.com/library/jj823138.aspx).



##Schritt 4: Erstellen einer Synchronisierungsgruppe

<h3><a id="StartNewSGWizard"></a>Schritt 4a: Starten des Assistenten "Neue Synchronisierungsgruppe"</h3>
1.	Kehren Sie zum [Verwaltungsportal](http://manage.windowsazure.com) zurück.
2.	Klicken Sie auf **SQL-DATENBANKEN**.
3.	Klicken Sie unten auf der Seite auf ** SYNCHRONISIERUNG HINZUFÜGEN**, und wählen Sie dann in der Schublade "Neue Synchronisierungsgruppe" aus.

	![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)


<h3><a id=""></a>Schritt 4b: Eingeben der grundlegenden Einstellungen</h3>	
1.	Gegen Sie einen aussagekräftigen Namen für die Synchronisierungsgruppe ein.
2.	Wählen Sie Dropdownmenü **REGION** (Rechenzentrum) die Region aus, in der diese Synchronisierungsgruppe gehostet werden soll.
3. Klicken Sie auf den Pfeil nach rechts.

	![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)
 
<h3><a id="DefineHubDB"></a>Schritt 4c: Definieren des Synchronisierungs-Hub</h3>
1. Wählen Sie in der Dropdown-Liste die SQL Database-Instanz, die als Hub für die Synchronisierungsgruppe dienen soll.
2. Geben Sie die Anmeldeinformationen für diese SQL Database-Instanz ein: **HUB-BENUTZERNAME** und **HUB-KENNWORT**.
3. Warten Sie, bis SQL Data Sync den BENUTZERNAMEN und das KENNWORT bestätigt. Wenn die Anmeldeinformationen bestätigt sind, wird recht neben dem KENNWORT ein grünes Häkchen angezeigt.
4. Wählen Sie in der Dropdownliste die Richtlinie **KONFLIKTLÖSUNG** aus.

 **Hub gewinnt** - jede in die Hub-Datenbank geschriebene Änderung wird auch in die Referenzdatenbanken geschrieben. Änderungen im selben Referenzdatensatz werden überschrieben. Konkret bedeutet dies, dass die erste in den Hub geschriebene Änderung an die anderen Datenbanken weitergeleitet wird.


 **Client gewinnt** - in den Hub geschriebene Änderungen werden durch Änderungen in den Referenzdatenbanken überschrieben. Konkret bedeutet dies, dass die letzte in den Hub geschriebene Änderung beibehalten und an die anderen Datenbanken weitergeleitet wird.

5.	Klicken Sie auf den Pfeil nach rechts.

	![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<h3><a id="AddRefDB"></a>Schritt 4d: Hinzufügen einer Referenzdatenbank</h3>

Wiederholen Sie diesen Schritt für jede zusätzliche Datenbank, die Sie zur Synchronisierungsgruppe hinzufügen möchten.

1. Wählen Sie in der Dropdown-Liste die Datenbank aus, die hinzugefügt werden soll.

	Zu den Datenbanken in der Dropdown-Liste gehören sowohl mit dem Agent registrierte SQL Server-Datenbanken als auch SQL Database-Instanzen.
2.	Geben Sie die Anmeldeinformationen für diese Datenbank ein: **BENUTZERNAME** und **KENNWORT**.
3.	Wählen Sie in der Dropdownliste die **SYNCHRONISIERUNGSRICHTUNG** für diese Datenbank aus.

	**Bidirektional**: Änderungen in der Referenzdatenbank werden in die Hub-Datenbank geschrieben, und Änderungen in der Hub-Datenbank werden in die Referenzdatenbank geschrieben.

	**Synchronisierung vom Hub** - Die Datenbank empfängt Aktualisierungen vom Hub. Sie sendet keine Änderungen an den Hub.

	**Synchronisierung zum Hub** - Die Datenbank sendet Aktualisierungen an den Hub. Änderungen im Hub werden nicht in die Datenbank geschrieben.

4.	Klicken Sie auf das Häkchen in der rechten unteren Ecke des Assistenten, um die Erstellung der Synchronisierungsgruppe abzuschließen. Warten Sie, bis SQL Data Sync die Anmeldeinformationen bestätigt. Ein grünes Häkchen zeigt an, dass die Anmeldeinformationen bestätigt wurden.

5.	Klicken Sie erneut auf dieses Häkchen. Nun kehren Sie unter SQL Databases zur Seite **SYNC** zurück. Diese Synchronisierungsgruppe wird nun mit Ihren anderen Synchronisierungsgruppen und Agents gelistet.

	![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


##Schritt 5: Festlegen der zu synchronisierenden Daten

In Azure SQL Data Sync können Sie Tabellen und Spalten zur Synchronisierung auswählen. Wenn Sie eine Spalte so filtern möchten, dass nur Zeilen mit bestimmten Werten (z B. Alter>=65) synchronisiert werden, verwenden Sie das Portal SQL Data Sync in Azure und die Dokumentation zum Auswählen der zu synchronisierenden Tabellen, Spalten und Zeilen, um festzulegen, welche Daten synchronisiert werden sollen.

1.	Kehren Sie zum [Verwaltungsportal](http://manage.windowsazure.com) zurück.
2.	Klicken Sie auf **SQL-DATENBANKEN**.
3.	Klicken Sie auf die Registerkarte **SYNCHRONISIEREN**.
4.	Klicken Sie auf den Namen dieser Synchronisierungsgruppe.
5.	Klicken Sie auf der Registerkarte **SYNCHRONISIERUNGSREGELN**.
6.	Wählen Sie die Datenbank aus, auf die Sie das Synchronisierungsgruppenschema anwenden möchten.
7.	Klicken Sie auf den Pfeil nach rechts.
8.	Klicken Sie auf **SCHEMA AKTUALISIEREN**.
9.	Wählen Sie für jede Tabelle in der Datenbank die Spalten aus, die synchronisiert werden sollen. 
	- Spalten mit nicht unterstützen Datentypen können nicht ausgewählt werden. 
	- Wenn in einer Tabelle keine Spalten ausgewählt sind, wird die Tabelle nicht in die Synchronisierungsgruppe aufgenommen. 
	- Klicken Sie unten auf dem Bildschirm auf SELECT, um alle Tabellen auszuwählen oder die Auswahl aufzuheben.
10.	Klicken Sie auf **SPEICHERN**, und warten Sie, bis die Bereitstellung der Synchronisierungsgruppe abgeschlossen ist.
11.	Klicken Sie oben links auf dem Bildschirm (über dem Namen der Synchronisierungsgruppe) auf den Pfeil nach links, um zur Data Sync-Angebotsseite zurückzukehren.

	![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

##Schritt 6: Konfigurieren der Synchronisierungsgruppe

Sie können eine Synchronisierungsgruppe immer synchronisieren, indem Sie unten auf der Data Sync-Angebotsseite auf SYNC klicken.
Wenn Sie möchten, dass eine Synchronisierungsgruppe gemäß einem Zeitplan synchronisiert wird, konfigurieren Sie diese Synchronisierungsgruppe.

1.	Kehren Sie zum [Verwaltungsportal](http://manage.windowsazure.com) zurück.
2.	Klicken Sie auf **SQL-DATENBANKEN**.
3.	Klicken Sie auf die Registerkarte **SYNCHRONISIEREN**.
4.	Klicken Sie auf den Namen dieser Synchronisierungsgruppe.
5.	Klicken Sie auf die Registerkarte **KONFIGURIEREN**.
6.	**AUTOMATIC SYNC**
	- Klicken Sie auf **EIN**, um die Synchronisierungsgruppe für eine Synchronisierung in einer bestimmten Frequenz zu synchronisieren. Sie können weiterhin nach Bedarf direkte Synchronisierungen durchführen, indem Sie auf SYNC klicken.
	- Klicken Sie auf **AUS**, um die Synchronisierungsgruppe so zu konfigurieren, dass sie nur dann synchronisiert wird, wenn Sie auf SYNCHRONISIEREN klicken.
7.	**SYNC FREQUENCY**
	- Wenn AUTOMATIC SYNC eingeschaltet ist, legen Sie die Synchronisierungsfrequenz fest. Die Frequenz muss zwischen 5 Minuten und 1 Monat liegen.
8.	Klicken Sie auf **Speichern**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Herzlichen Glückwunsch. Sie haben nun eine Synchronisierungsgruppe erstellt, die sowohl eins SQL-Datenbank-Instanz als auch eine SQL Server-Datenbank enthält.

##Nächste Schritte
Zusätzliche Informationen über SQL Database und SQL Data Sync finden Sie hier:

* [Registrieren Sie sich für die Premium-Angebot für SQL-Datenbank] (sign-up-for-sql-database-premium.md)
* [SQL Data Sync (WA-Portal)](http://msdn.microsoft.com/library/windowsazure/jj856263.aspx)
* [Erste Schritte mit Azure SQL-Datenbank](../getting-started-w-sql-databases.md)
* [Lebenszyklus von SQL Server-Datenbanken](http://go.microsoft.com/fwlink/?LinkId=275193)










<!--HONumber=47-->
 