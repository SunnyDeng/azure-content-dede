<properties umbracoNaviHide="0" pageTitle="How to Manage SQL Database" metaKeywords="Azure SQL database, SQL database, manage sql database, add logins, connect to sql database" description="Learn how to manage Azure SQL database." linkid="devnav-manage-services-cloud-services" urlDisplayName="Cloud Services" headerExpose="" footerExpose="" disqusComments="1" title="How to Manage SQL Database" authors="jeffreyg" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg" />

# <span id="swap"></span></a>Verwalten der SQL-Datenbank

In diesem Thema erfahren Sie, wie Sie einfache Verwaltungsaufgaben in einer Azure SQL-Datenbank ausführen können.

## Inhaltsverzeichnis

-   [Gewusst wie: Verbinden mit der SQL-Datenbank in Azure über Management Studio][Gewusst wie: Verbinden mit der SQL-Datenbank in Azure über Management Studio]
-   [Gewusst wie: Hinzufügen von Anmeldungen und Benutzern zur SQL-Datenbank in Azure][Gewusst wie: Hinzufügen von Anmeldungen und Benutzern zur SQL-Datenbank in Azure]

## <span id="connect"></span></a>Gewusst wie: Verbinden mit der SQL-Datenbank in Azure über Management Studio

Management Studio ist ein Administrations-Tool, mit dem Sie mehrere SQL Server-Instanzen und Server in einer einzigen Arbeitsumgebung verwalten können. Falls Sie bereits eine lokale SQL Server-Instanz haben, können Sie sich sowohl mit der lokalen Instanz als auch mit dem logischen Server in Azure verbinden und Aufgaben nebeneinander erledigen.

Management Studio enthält Features, die momentan im Verwaltungsportal nicht verfügbar sind, wie z. B. Syntaxprüfung und die Möglichkeit, Skripts und benannte Abfragen zu speichern und wiederzuverwenden. Die SQL-Datenbank ist ein einfacher Tabular Data Stream (TDS)-Endpunkt. Alle mit TDS kompatiblen Tools inklusive Management Studio können Operationen in der SQL-Datenbank ausführen. Ihre für den lokalen Server entwickelten Skripts funktionieren auch auf einem logischen SQL-Datenbankserver.

Im nächsten Schritt werden Sie Management Studio verwenden, um sich mit einem logischen Server in Azure zu verbinden. Für diesen Schritt benötigen Sie SQL Server Management Studio Version 2008 R2 oder 2012. Hilfe zum Download oder zu Verbindungen mit Management Studio finden Sie unter [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio][Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio] auf dieser Website.

Bevor Sie sich verbinden können, müssen Sie unter Umständen eine Firewall-Ausnahme erstellen, die ausgehende Anfragen auf Port 1433 für Ihr lokales System erlaubt. Auf sicheren Computern ist der Port 1433 standardmäßig nicht geöffnet.

## Konfigurieren der Firewall für einen lokalen Server

1.  Erstellen Sie eine neue ausgehende Regel in der Windows-Firewall mit erweiterter Sicherheit.

2.  Klicken Sie auf **Port**, geben Sie TCP 1433 und **Allow the connection** an und vergewissern Sie sich, dass Sie das Profil **Public** ausgewählt haben.

3.  Geben Sie einen aussagekräftigen Namen ein, z. B. *WindowsAzureSQLDatenbank (tcp-out) Port 1433*.

## Verbinden mit einem logischen Server

1.  Vergewissern Sie sich in Management Studio unter Connect to Server, dass Database Engine ausgewählt ist, und geben Sie den Namen des logischen Servers im folgenden Format ein: *servername*.database.widnows.net

    Sie können den vollqualifizierten Servernamen im Verwaltungsportal im Server-Dashboard unter MANAGE URL abrufen.

2.  Wählen Sie **SQL Server Authentication** unter Authentication und geben Sie die Administrator-Anmeldedaten ein, die Sie beim Konfigurieren des logischen Servers erstellt haben.

3.  Klicken Sie auf **Options**.

4.  Geben Sie unter Connect to database den Wert **master** ein.

## Verbinden mit einem lokalen Server

1.  Vergewissern Sie sich in Management Studio unter Connect to Server, dass Database Engine ausgewählt ist, und geben Sie den Namen der lokalen Instanz im folgenden Format ein: *servername*\\*instancename*. Für eine Standardinstanz auf dem lokalen Server können Sie *localhost* eingeben.

2.  Wählen Sie **Windows Authentication** unter Authentication aus und geben Sie ein Windows-Konto ein, das Mitglied der sysadmin-Rolle ist.

## <span id="addlogins"></span></a>Gewusst wie: Hinzufügen von Anmeldungen und Benutzern zur SQL-Datenbank in Azure

Nachdem Sie eine Datenbank bereitgestellt haben, müssen Sie Anmeldungen konfigurieren und Berechtigungen zuweisen. In diesem Schritt führen Sie zwei Skripts aus.

Zunächst verbinden Sie sich mit einem Master und führen ein Skript zur Erstellung von Anmeldungen aus. Die Anmeldungen werden für Lese- und Schreiboperationen und zum Delegieren von Betriebsaufgaben verwendet, wie z. B. der Möglichkeit zum Ausführen von Systemabfragen ohne SA-Berechtigungen.

Sie müssen Anmeldenamen mit SQL Server-Authentifizierung erstellen. Falls Sie bereits vorgefertigte Skripts haben, die Windows-Benutzeridentitäten oder Anspruchsidentitäten verwenden, werden diese Skripts unter der SQL-Datenbank nicht funktionieren.

Das zweite Skript vergibt Berechtigungen an Datenbankbenutzer. Für dieses Skript verbinden Sie sich mit einer bereits in Azure geladenen Datenbank.

## Anmeldungen erstellen

1.  Verbinden Sie sich in Management Studio mit einem logischen Server in Azure, erweitern Sie den Ordner Databases, klicken Sie mit der rechten Maustaste auf **master** und wählen Sie **New Query** aus.

2.  Verwenden Sie die folgenden Transact-SQL-Anweisungen zur Erstellung von Anmeldungen. Ersetzen Sie das Kennwort durch ein gültiges Kennwort. Wählen Sie die Anweisungen einzeln aus und klicken Sie auf **Execute**. Wiederholen Sie den Vorgang für die verbleibenden Anmeldungen.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- Auf Master ausf&uuml;hren, jede Zeile einzeln ausf&uuml;hren
    -- Verwenden Sie diese Anmeldung zur Verwaltung anderer Anmeldungen auf diesem Server
    CREATE LOGIN sqladmin WITH password='&lt;Kennwort angeben&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- Verwenden Sie diese Anmeldung zum Erstellen und Kopieren von Datenbanken
    CREATE LOGIN sqlops WITH password='&lt;Kennwort angeben&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>

## Erstellen von Datenbankbenutzern

1.  Erweitern Sie den Ordner Databases, klicken Sie mit der rechten Maustaste auf **school** und wählen Sie **New Query** aus.

2.  Verwenden Sie die folgenden Transact-SQL-Anweisungen, um Datenbankbenutzer hinzuzufügen. Ersetzen Sie das Kennwort durch ein gültiges Kennwort.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- Auf normaler Datenbank ausf&uuml;hren, jede Zeile einzeln ausf&uuml;hren
    -- Verwenden Sie diese Anmeldung f&uuml;r Leseoperationen
    CREATE LOGIN sqlreader WITH password='&lt;Kennwort angeben&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- Verwenden Sie diese Anmeldung f&uuml;r Schreiboperationen
    CREATE LOGIN sqlwriter WITH password='&lt;Kennwort angeben&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- DMV-Berechtigungen f&uuml;r die Datenbank school an 'sqlops' vergeben
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## Anmeldungen anzeigen und testen

1.  Verbinden Sie sich in einem neuen Abfragefenster mit **master** und führen Sie die folgende Anweisung aus:

        SELECT * from sys.sql_logins;

2.  Klicken Sie in Management Studio mit der rechten Maustaste auf die Datenbank **school** und wählen Sie **New Query** aus.

3.  Zeigen Sie im Menü Query auf **Connection** und klicken Sie auf **Change Connection**.

4.  Melden Sie sich als *sqlreader* an.

5.  Kopieren Sie die folgende Anweisung und versuchen Sie, diese auszuführen. Sie sollten einen Fehler und eine Meldung erhalten, dass das Objekt nicht existiert.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6.  Öffnen Sie ein zweites Abfragefenster und ändern Sie den Verbindungskontext zu *sqlwriter*. Die gleiche Abfrage sollte sich nun ohne Fehler ausführen lassen.

Sie haben nun mehrere Anmeldungen erstellt und getestet. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank] und [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten][Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten].

  [Gewusst wie: Verbinden mit der SQL-Datenbank in Azure über Management Studio]: #connect
  [Gewusst wie: Hinzufügen von Anmeldungen und Benutzern zur SQL-Datenbank in Azure]: #addlogins
  [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio]: http://www.windowsazure.com/de-de/develop/net/common-tasks/sql-azure-management/
  [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336235.aspx
  [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394114.aspx
