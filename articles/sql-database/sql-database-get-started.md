<properties
	pageTitle="Erste Schritte mit SQL-Datenbank"
	description="Erstellen Sie Ihre erste Clouddatenbank in wenigen Minuten mit Azure SQL-Datenbank, dem relationalen Datenbankverwaltungsdienst (RDBMS) von Microsoft in der Cloud, mit dem Azure-Verwaltungsportal und der AdventureWorks-Beispieldatenbank."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/15/2015"
	ms.author="genemi"/>


# Erstellen einer ersten Azure SQL-Datenbank


Dieser Artikel zeigt, wie Sie in weniger als fünf Minuten eine Beispiel-Azure SQL-Datenbank erstellen. Folgendes wird vermittelt:


- Bereitstellen eines logischen Servers über das [Azure-Portal](http://portal.azure.com/).
- Erstellen einer mit Beispieldaten gefüllten Datenbank
- Festlegen einer Firewallregel für die Datenbank, um die IP-Adressen mit Zugriff auf die Datenbank zu konfigurieren


In diesem Lernprogramm wird davon ausgegangen, dass Sie ein Azure-Abonnement besitzen. Wenn Sie kein Abonnement haben, können Sie sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.


## Schritt 1: Anmelden


1. Melden Sie sich beim [Azure-Portal](http://portal.azure.com/) an.
2. Klicken Sie auf **Neu** > **Daten + Speicher** > **SQL-Datenbank**.


![Neue SQL-Datenbank][1]


## Schritt 2: Erstellen des logischen Servers



1. Wählen Sie auf dem Blatt "SQL-Datenbank" einen **Namen** für Ihre Datenbank aus, in diesem Beispiel **AdventureWorks**.
2. Um den logischen Server für die Datenbank zu erstellen, klicken Sie auf **Server** und dann auf **Create a new server**.


## Schritt 3: Konfigurieren des Servers


1. Geben Sie auf dem Blatt **Server** den **Servernamen** als eindeutigen Namen ein, der leicht zu merken ist.
2. Geben Sie für **Server Admin Login** den Namen **AdventureAdmin** ein.
3. Geben Sie die richtigen Werte für **Kennwort** und **Kennwort bestätigen** ein.
4. Wählen Sie den bevorzugten geografischen **Standort**. In der Regel sollte sich der Standort in der Nähe des Ausführungsorts der Anwendung befinden.
5. Klicken Sie auf **OK**.


![Server erstellen][2]


## Schritt 4: Erstellen der Datenbank


1. Geben Sie auf dem Blatt "SQL-Datenbank" die Quelle der Datenbank an, indem Sie auf **Quelle auswählen** klicken.
 - Wenn Sie diesen Schritt überspringen, wird eine leere Datenbank erstellt.
2. Wählen Sie **Beispiel** aus.
 - Dies erstellt eine Kopie der Standard-Beispieldatenbank mit dem Namen **AdventureWorks**.
 - Für die Azure SQL-Datenbank wird die *light schema*-Edition von AdventureWorks verwendet.
3. Klicken Sie unten auf dem Blatt auf **Erstellen**.


## Schritt 5: Konfigurieren der Firewall


Die folgenden Schritte veranschaulichen, wie Sie die IP-Adressbereiche mit Zugriff auf die Datenbank festlegen.


![Server durchsuchen][3]


1. Klicken Sie im Menüband auf der linken Seite des Bildschirms auf **Durchsuchen** und dann auf **SQL Server**.
2. Wählen Sie aus den verfügbaren Optionen den SQL Server aus, den Sie zuvor erstellt haben.
3. Klicken Sie auf **Einstellungen** und dann auf **Firewall**.
4. Klicken Sie auf diesen Link, um Ihre aktuelle IP-Adresse von [Bing](http://www.bing.com/search?q=my%20ip%20address) abzurufen.
5. Geben Sie in den Firewalleinstellungen einen **Regelnamen** ein, und fügen Sie die öffentliche IP-Adresse aus dem vorherigen Schritt in die Felder **Start-IP** und **End-IP** ein.
6. Klicken Sie anschließend oben auf der Seite auf **Speichern**.


![Firewall][4]


## Nächste Schritte


Sie können nun ein kleines Clientprogramm schreiben, das eine Verbindung mit der Datenbank herstellen kann. Für ein kurzes Codebeispiel klicken Sie auf einen der folgenden Links:


- [Verbinden mit und Abfragen der SQL-Datenbank mit C#](sql-database-connect-query.md)
- *In Kürze verfügbar*: Codebeispiele für die Cliententwicklung und erste Schritte mit SQL-Datenbank


<!-- Media references. -->
[1]: ./media/sql-database-get-started/GettingStarted_NewDB.PNG
[2]: ./media/sql-database-get-started/GettingStarted_CreateServer.png
[3]: ./media/sql-database-get-started/GettingStarted_BrowseServer.png
[4]: ./media/sql-database-get-started/GettingStarted_FireWall.png
 

<!---HONumber=July15_HO3-->