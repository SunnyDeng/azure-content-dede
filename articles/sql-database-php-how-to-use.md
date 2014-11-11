<properties linkid="develop-php-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (PHP) - Azure feature guides" metaKeywords="Azure SQL Database PHP, SQL Database PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Zugreifen auf Azure SQL-Datenbank aus PHP

In diesem Leitfaden werden die Grundlagen der Verwendung von Azure SQL-Datenbank aus PHP erläutert. Die Beispiele sind in PHP geschrieben. Die behandelten Szenarien umfassen das **Erstellen einer SQL-Datenbank** und das **Herstellen einer Verbindung zu einer SQL-Datenbank**. Dieser Leitfaden beschreibt das Erstellen einer SQL-Datenbank im [Verwaltungsportal][Verwaltungsportal]. Informationen zum Durchführen dieser Aufgaben im Produktionsportal finden Sie unter [Getting Started with PHP and SQL Database][Getting Started with PHP and SQL Database] (Erste Schritte mit PHP und SQL-Datenbank, in englischer Sprache). Weitere Informationen finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Was ist Azure SQL-Datenbank?

Azure SQL-Datenbank bietet ein Verwaltungssystem für relationale Datenbanken für Azure und basiert auf SQL Server-Technologie. Mit SQL-Datenbank können Sie relationale Datenbanklösungen für die Cloud bereitstellen und implementieren, und Sie profitieren von einem verteilten Datencenter, das Verfügbarkeit, Skalierbarkeit und Sicherheit für Unternehmen mit den Vorteilen von integriertem Datenschutz und Selbstreparatur bietet.

## Inhaltsverzeichnis

-   [Konzepte][Konzepte]
-   [Gewusst wie: Einrichten Ihrer Umgebung][Gewusst wie: Einrichten Ihrer Umgebung]
-   [Gewusst wie: Erstellen einer SQL-Datenbank][Gewusst wie: Erstellen einer SQL-Datenbank]
-   [Gewusst wie: Abrufen von SQL-Datenbank-Verbindungsinformationen][Gewusst wie: Abrufen von SQL-Datenbank-Verbindungsinformationen]
-   [Gewusst wie: Herstellen einer Verbindung zu einer SQL-Datenbankinstanz][Gewusst wie: Herstellen einer Verbindung zu einer SQL-Datenbankinstanz]
-   [Nächste Schritte][Nächste Schritte]

## <span id="Concepts"></span></a>Konzepte

Da Azure SQL-Datenbank auf SQL Server-Technologien basiert, erfolgt der Zugriff auf SQL-Datenbank aus PHP ganz ähnlich wie der Zugriff auf SQL Server aus PHP. Sie können eine Anwendung lokal (mit SQL Server) entwickeln und dann eine Verbindung zu SQL-Datenbank herstellen, indem Sie lediglich die Verbindungszeichenfolge ändern. Es gibt jedoch einige Unterschiede zwischen SQL-Datenbank und SQL Server, die sich auf Ihre Anwendung auswirken können. Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen (SQL-Datenbanken)][Richtlinien für und Einschränkungen (SQL-Datenbanken)].

Der Zugriff auf SQL-Datenbank aus PHP sollte nach Möglichkeit unter Verwendung der [Microsoft-Treiber für PHP für SQL Server][Microsoft-Treiber für PHP für SQL Server] erfolgen. (In den Beispielen dieses Artikels werden diese Treiber verwendet.) Die Microsoft-Treiber für PHP für SQL Server funktionieren nur unter Windows.

## <span id="Setup"></span></a>Gewusst wie: Einrichten Ihrer Umgebung

Für die Einrichtung der Entwicklungsumgebung wird die Verwendung des [Microsoft-Webplattform-Installer][Microsoft-Webplattform-Installer] empfohlen. Mit dem Webplattform-Installer können Sie Elemente Ihrer Webentwicklungsplattform auswählen und diese automatisch installieren und konfigurieren. Wenn Sie den Webplattform-Installer herunterladen und WebMatrix, PHP für WebMatrix und SQL Server Express zur Installation auswählen, wird eine vollständige Entwicklungsumgebung für Sie eingerichtet.

Alternativ können Sie die Umgebung auch manuell einrichten:

-   Installieren Sie PHP, und konfigurieren Sie IIS: [][]<http://php.net/manual/en/install.windows.iis7.php></a>.
-   Laden Sie SQL Server Express herunter, und installieren Sie es: [][1]<http://www.microsoft.com/de-de/download/details.aspx?id=29062></a>
-   Laden Sie die Microsoft-Treiber für PHP für SQL Server herunter, und installieren Sie sie: [][2]<http://php.net/manual/en/sqlsrv.requirements.php></a>.

## <span id="CreateServer"></span></a>Gewusst wie: Erstellen einer SQL-Datenbank

Führen Sie folgende Schritte durch, um eine Azure SQL-Datenbank zu erstellen:

1.  Melden Sie sich am [Verwaltungsportal][Verwaltungsportal] an.
2.  Klicken Sie unten links im Portal auf **Neu**.

    ![Neue Azure-Website erstellen][Neue Azure-Website erstellen]

3.  Klicken Sie auf **DATA SERVICES**, **SQL DATABASE** und dann auf **CUSTOM CREATE**.

    ![Eine neue SQL-Datenbank benutzerdefiniert erstellen][Eine neue SQL-Datenbank benutzerdefiniert erstellen]

4.  Geben Sie im Feld **NAME** einen Namen für Ihre Datenbank ein, und wählen Sie die **EDITION** (WEB oder BUSINESS), die **MAXIMALE GRÖSSE** für Ihre Datenbank, die **SORTIERUNG** und anschließend **Neuer SQL Datenbankserver** aus. Klicken Sie unten im Dialogfeld auf den Pfeil. (Falls Sie zuvor bereits eine SQL-Datenbank erstellt haben, können Sie aus der Dropdownliste **Choose a server** einen vorhandenen Server auswählen.)

    ![SQL-Datenbankeinstellungen eingeben][SQL-Datenbankeinstellungen eingeben]

5.  Geben Sie einen Administratornamen und ein Kennwort ein (und bestätigen Sie das Kennwort), wählen Sie die Region aus, in der Ihre neue SQL-Datenbank erstellt wird, und aktivieren Sie das Kontrollkästchen `Allow Azure Services to access the server`.

    ![Einen neuen SQL-Datenbankserver erstellen][Einen neuen SQL-Datenbankserver erstellen]

Klicken Sie im Verwaltungsportal auf **SQL-Datenbanken**, um Server- und Datenbankinformationen anzuzeigen. Sie können dann auf **DATENBANKEN** oder **SERVER** klicken, um die entsprechenden Informationen anzuzeigen.

![Server- und Datenbankinformationen anzeigen][Server- und Datenbankinformationen anzeigen]

## <span id="ConnectionInfo"></span></a>Gewusst wie: Abrufen von SQL-Datenbank-Verbindungsinformationen

Klicken Sie im Portal auf **SQL-DATENBANKEN** und dann auf den Namen der Datenbank, um SQL-Datenbank-Verbindungsinformationen abzurufen.

![Datenbankinformationen anzeigen][Datenbankinformationen anzeigen]

Klicken Sie anschließend auf **View SQL Database connection strings for ADO.NET, ODBC, PHP, and JDBC**.

![Verbindungszeichenfolgen anzeigen][Verbindungszeichenfolgen anzeigen]

Notieren Sie im PHP-Abschnitt des Ergebnisfensters die Werte für **SERVER**, **DATENBANK** und **BENUTZERNAME**. Ihr Kennwort ist das Kennwort, das Sie zum Erstellen der SQL-Datenbank verwendet haben.

## <span id="Connect"></span></a>Gewusst wie: Herstellen einer Verbindung zu einer SQL-Datenbankinstanz

In den folgenden Beispielen wird gezeigt, wie Sie mit den Erweiterungen **SQLSRV** und **PDO\_SQLSRV** eine Verbindung mit einer SQL-Datenbank namens `testdb` herstellen. Sie benötigen die im vorstehenden Abschnitt abgerufenen Informationen. Ersetzen Sie `SERVER_ID` durch Ihre 10-stellige Server-ID (dies sind die ersten zehn Zeichen des im vorstehenden Abschnitt abgerufenen SERVER-Werts), und weisen Sie den Variablen `$user` und `$pwd` die entsprechenden Werte (Ihren Benutzernamen und Ihr Kennwort) zu.

##### SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    $conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

    if($conn === false){
        die(print_r(sqlsrv_errors()));
    }

##### PDO\_SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    try{
        $conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
        $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
    }
    catch(Exception $e){
        die(print_r($e));
    }

## <span id="NextSteps"></span></a>Nächste Schritte

Wie bereits erwähnt, ähnelt die Verwendung von SQL-Datenbank sehr der Verwendung von SQL Server. Nachdem Sie eine Verbindung zu einer SQL-Datenbank hergestellt haben (wie oben beschrieben), können Sie die **SQLSRV**- oder **PDO\_SQLSRV**-API zum Einfügen, Abrufen, Aktualisieren und Löschen von Daten verwenden. Informationen zur **SQLSRV**- und **PDO\_SQLSRV**-API finden Sie in der[Dokumentation für Microsoft-Treiber für PHP für SQL Server][Dokumentation für Microsoft-Treiber für PHP für SQL Server]. Es gibt jedoch einige Unterschiede zwischen SQL-Datenbank und SQL Server, die sich auf Ihre Anwendung auswirken können. Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen (SQL-Datenbanken)][Richtlinien für und Einschränkungen (SQL-Datenbanken)].

Ein Beispiel, das die Verwendung der SQL-Datenbank mit PHP auf Azure zeigt, ist auf <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure> verfügbar.

  [Verwaltungsportal]: https://manage.windowsazure.com
  [Getting Started with PHP and SQL Database]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
  [Nächste Schritte]: #NextSteps
  [Konzepte]: #Concepts
  [Gewusst wie: Einrichten Ihrer Umgebung]: #Setup
  [Gewusst wie: Erstellen einer SQL-Datenbank]: #CreateServer
  [Gewusst wie: Abrufen von SQL-Datenbank-Verbindungsinformationen]: #ConnectionInfo
  [Gewusst wie: Herstellen einer Verbindung zu einer SQL-Datenbankinstanz]: #Connect
  [Microsoft-Treiber für PHP für SQL Server]: http://www.microsoft.com/download/en/details.aspx?id=20098
  [Microsoft-Webplattform-Installer]: http://go.microsoft.com/fwlink/?LinkId=253447
  []: http://php.net/manual/en/install.windows.iis7.php
  [1]: http://www.microsoft.com/de-de/download/details.aspx?id=29062
  [2]: http://php.net/manual/en/sqlsrv.requirements.php
  [Neue Azure-Website erstellen]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
  [Eine neue SQL-Datenbank benutzerdefiniert erstellen]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
  [SQL-Datenbankeinstellungen eingeben]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
  [Einen neuen SQL-Datenbankserver erstellen]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
  [Server- und Datenbankinformationen anzeigen]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
  [Datenbankinformationen anzeigen]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
  [Verbindungszeichenfolgen anzeigen]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png
  [Dokumentation für Microsoft-Treiber für PHP für SQL Server]: http://msdn.microsoft.com/de-de/library/dd638075(SQL.10).aspx
