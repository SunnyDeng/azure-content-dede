<properties umbracoNaviHide="0" pageTitle="Verwalten von SQL-Datenbank" metaKeywords="Azure SQL-Datenbank, SQL-Datenbank, Sql-Datenbank verwalten, Benutzerkonten hinzufügen, Verbinden mit Sql-Datenbank" description="Erfahren Sie mehr über die Verwalten von Azure SQL-Datenbank." urlDisplayName="Cloud Services" headerExpose="" footerExpose="" disqusComments="1" title="How to Manage SQL Database" authors="jeffreyg" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/31/2015" ms.author="jeffreyg" />


<h1><a id="swap"></a>Verwalten von SQL Database</h1>

In dieser Dokumentation erfahren Sie, wie Sie einfache Verwaltungsaufgaben in Azure SQL-Datenbank ausführen können. 

## Inhaltsverzeichnis

* [Gewusst wie: Verbinden mit der SQL-Datenbank in Azure über Management Studio](#connect)
* [Gewusst wie: Hinzufügen von Anmeldungen und Benutzern zur SQL-Datenbank in Azure](#addlogins)


<h2><a id="connect"></a>Gewusst wie: Verbinden mit der SQL-Datenbank in Azure über Management Studio</h2>

Management Studio ist ein Verwaltungstool, mit dem Sie mehrere SQL Server-Instanzen und -Server in einer einzigen Arbeitsumgebung verwalten können. Falls Sie bereits eine lokale SQL Server-Instanz haben, können Sie sowohl mit der lokalen Instanz als auch mit dem logischen Server in Azure eine Verbindung herstellen und Aufgaben parallel erledigen.

Management Studio enthält Features, die derzeit im Verwaltungsportal nicht verfügbar sind, wie z. B. Syntaxprüfung und die Möglichkeit, Skripts und benannte Abfragen zu speichern und wiederzuverwenden. Die SQL-Datenbank ist ein einfacher Tabular Data Stream (TDS)-Endpunkt. Alle mit TDS kompatiblen Tools inklusive Management Studio können Operationen in der SQL-Datenbank ausführen. Ihre für den lokalen Server entwickelten Skripts funktionieren auch auf einem logischen SQL-Datenbankserver. 

Im nächsten Schritt werden Sie Management Studio verwenden, um eine Verbindung mit einem logischen Server in Azure herzustellen. Für diesen Schritt benötigen Sie SQL Server Management Studio Version 2008 R2 oder 2012. Hilfe zum Download oder zu Verbindungen mit Management Studio finden Sie unter [Verwalten einer Azure SQL-Datenbank mit Management Studio][] auf dieser Website.

Bevor Sie sich verbinden können, müssen Sie unter Umständen eine Firewall-Ausnahme erstellen, die ausgehende Anfragen auf Port 1433 für Ihr lokales System erlaubt. Auf sicheren Computern ist der Port 1433 standardmäßig nicht geöffnet. 

## Konfigurieren der Firewall für einen lokalen Server

1. Erstellen Sie eine neue ausgehende Regel in der Windows-Firewall mit erweiterter Sicherheit.

2. Wählen Sie **Port**, geben Sie TCP 1433 und **Verbindung zulassen**, und vergewissern Sie sich, dass das Profil **Öffentlich** ausgewählt ist.

3. Geben Sie einen aussagekräftigen Namen ein, z. B. *WindowsAzureSQLDatenbank (tcp-out) Port 1433*. 


## Verbinden mit einem logischen Server

1. Vergewissern Sie sich in Management Studio unter "Verbindung mit dem Server herstellen", dass "Datenbankmodul" ausgewählt ist, und geben Sie den Namen des logischen Servers im folgenden Format ein: *servername*.database.widnows.net

	Sie können den vollqualifizierten Servernamen im Verwaltungsportal im Server-Dashboard unter MANAGE URL abrufen.

2. Wählen Sie unter "Authentifizierun" die Option **SQL Server-Authentifizierung**, und geben Sie die Administrator-Anmeldedaten ein, die Sie beim Konfigurieren des logischen Servers erstellt haben.

3. Klicken Sie auf **Optionen**. 

4. Geben Sie unter "Verbindung mit Datenbank herstellen" die Datenbank **master** ein.


## Verbinden mit einem lokalen Server

1. Vergewissern Sie sich in Management Studio unter "Verbindung mit Server herstellen", dass "Datenbankmodul" ausgewählt ist, und geben Sie den Namen der lokalen Instanz im folgenden Format ein: *servername*\\*instanzenname*. Für eine Standardinstanz auf dem lokalen Server können Sie *localhost* eingeben.

2. Wählen Sie unter "Authentifizierung" die Option **Windows-Authentifizierung** aus, und geben Sie ein Windows-Konto ein, das Mitglied der sysadmin-Rolle ist.


<h2><a id="addlogins"></a>Gewusst wie: Hinzufügen von Anmeldungen und Benutzern zur SQL-Datenbank in Azure</h2>

Nachdem Sie eine Datenbank bereitgestellt haben, müssen Sie Anmeldungen konfigurieren und Berechtigungen zuweisen. Im nächsten Schritt führen Sie zwei Skripts aus.

Für das erste Skript stellen Sie eine Verbindung mit master her und führen ein Skript zur Erstellung von Anmeldungen aus. Die Anmeldungen werden für Lese- und Schreibvorgänge und zum Delegieren von Betriebsaufgaben verwendet, wie z. B. der Möglichkeit zum Ausführen von Systemabfragen ohne SA-Berechtigungen.

Sie müssen Anmeldenamen mit SQL Server-Authentifizierung erstellen. Wenn Sie bereits vorgefertigte Skripts haben, die Windows-Benutzeridentitäten oder Anspruchsidentitäten verwenden, funktionieren diese Skripts unter SQL-Datenbank nicht.

Das zweite Skript vergibt Berechtigungen an Datenbankbenutzer. Für dieses Skript stellen Sie eine Verbindung mit einer bereits in Azure geladenen Datenbank her.

## Anmeldungen erstellen

1. Stellen Sie eine Verbindung in Management Studio mit einem logischen Server in Azure her, erweitern Sie den Ordner "Datenbanken", klicken Sie mit der rechten Maustaste auf **master**, und wählen Sie **Neue Abfrage** aus.

2. Verwenden Sie die folgenden Transact-SQL-Anweisungen zum Erstellen von Anmeldungen. Ersetzen Sie das Kennwort durch ein gültiges Kennwort. Wählen Sie die Anweisungen einzeln aus und klicken Sie auf **Execute**. Wiederholen Sie dies für die restlichen Anmeldungen.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='<ProvidePassword>'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='<ProvidePassword>';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


## Erstellen von Datenbankbenutzern

1. Erweitern Sie den Ordner "Datenbanken", klicken Sie mit der rechten Maustaste auf **school**, und wählen Sie **Neue Abfrage** aus.

2. Verwenden Sie die folgenden Transact-SQL-Anweisungen, um Datenbankbenutzer hinzuzufügen. Ersetzen Sie das Kennwort durch ein gültiges Kennwort. 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='<ProvidePassword>';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='<ProvidePassword>';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## Anmeldungen anzeigen und testen

1. Stellen Sie in einem neuen Abfragefenster eine Verbindung mit **master** her, und führen Sie die folgende Anweisung aus: 

        SELECT * from sys.sql_logins;

2. Klicken Sie in Management Studio mit der rechten Maustaste auf die Datenbank **school**, und wählen Sie **Neue Abfrage** aus.

3. Zeigen Sie im Menü "Abfrage" auf **Verbindung**, und klicken Sie dann auf **Verbindung ändern**.

4. Melden Sie sich als *sqlreader* an.

5. Kopieren Sie die folgende Anweisung, und versuchen Sie, diese auszuführen. Es sollte eine Fehlermeldung angezeigt werden, dass das Objekt nicht existiert.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. Öffnen Sie ein zweites Abfragefenster, und ändern Sie den Verbindungskontext in *sqlwriter*. Die gleiche Abfrage sollte sich nun ohne Fehler ausführen lassen.

Sie haben nun mehrere Anmeldungen erstellt und getestet. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][] und [Überwachen der Microsoft Azure SQL-Datenbank mit dynamischen Verwaltungssichten][].

[Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
[Überwachen der Microsoft Azure SQL-Datenbank mit dynamischen Verwaltungssichten]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
[Verwalten einer Azure SQL-Datenbank mit Management Studio]: http://azure.microsoft.com/develop/net/common-tasks/sql-azure-management/





