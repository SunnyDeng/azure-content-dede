<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="Node.js" title="How to Access Azure SQL Database from Node.js" authors="" solutions="" manager="" editor="" />

Zugriff auf Azure SQL Database von Node.js
==========================================

In diesem Leitfaden lernen Sie die Grundlagen der Verwendung des Microsoft Driver for Node.JS for SQL Server zum Zugreifen auf eine Azure SQL-Datenbank kennen. Die behandelten Szenarien umfassen das **Erstellen einer SQL-Datenbank** und das Herstellen einer **Verbindung zu einer SQL-Datenbank**. Dieser Leitfaden beschreibt das Erstellen einer SQL-Datenbank im[Vorschau-Verwaltungsportal](https://manage.windowsazure.com).

Inhaltsverzeichnis
------------------

-   [Konzepte](#Concepts)
-   [Vorgehensweise: Einrichten Ihrer Umgebung](#Setup)
-   [Vorgehensweise: Erstellen einer SQL-Datenbank](#CreateServer)
-   [Vorgehensweise: Abrufen von SQL-Datenbank-Verbindungsinformationen](#ConnectionInfo)
-   [Vorgehensweise: Herstellen einer Verbindung zu einer SQL-Datenbankinstanz](#Connect)
-   [Berücksichtigungen für die Azure-Bereitstellung](#Deploy)
-   [Nächste Schritte](#NextSteps)

Konzepte
--------

### Was ist Azure SQL-Datenbank?

Azure SQL-Datenbank bietet ein Managementsystem für relationale Datenbanken für Azure und basiert auf SQL Server-Technologie. Mit SQL-Datenbank können Sie relationale Datenbanklösungen für die Cloud bereitstellen und implementieren, und Sie profitieren von einem verteilten Datacenter, das Verfügbarkeit, Skalierbarkeit und Sicherheit für Unternehmen mit den Vorteilen von integriertem Datenschutz und Selbstreparatur bietet.

Was ist der Microsoft Driver for Node.JS for SQL Server?
--------------------------------------------------------

Der Microsoft Driver for Node.JS for SQL Server ermöglicht Entwicklern von einer Node.js-Anwendung aus auf Daten zuzugreifen, die in einer Microsoft SQL Server- oder Azure SQL-Datenbank gespeichert sind. Der Treiber ist derzeit nur als Vorschauversion erhältlich. Weitere Funktionen werden in das Projekt integriert, sobald diese fertig sind. Weitere Informationen zu dem Treiber finden Sie auf der [GitHub-Projektseite](https://github.com/WindowsAzure/node-sqlserver) und in dem entsprechenden [Wiki](https://github.com/WindowsAzure/node-sqlserver/wiki).

**Hinweis**

Der Microsoft Driver for Node.JS for SQL Server ist derzeit als Vorschauversion verfügbar und basiert auf Laufzeitkomponenten, die nur in Microsoft Windows- und Azure-Betriebssystemen erhältlich sind.

Vorgehensweise: Einrichten Ihrer Umgebung
-----------------------------------------

### Installieren des SQL Server Native Client

Der Microsoft SQL Server Driver for Node.js basiert auf dem SQL Server Native Client. Obwohl der Native Client automatisch verfügbar ist, wenn die Anwendung in Azure bereitgestellt wird, ist er möglicherweise in Ihrer lokalen Entwicklungsumgebung nicht vorhanden. Sie können den SQL Server Native Client von der Downloadseite [Microsoft SQL Server 2012 Feature Pack](http://www.microsoft.com/en-us/download/details.aspx?id=29065) installieren.

**Hinweis**

Der SQL Server Native Client ist nur für das Microsoft Windows-Betriebssystem erhältlich. Obwohl der Treiber auf Azure verfügbar ist, können Sie Ihre Applikation anhand der in diesem Artikel zur Verfügung gestellten Informationen nicht testen, wenn Sie Ihre Entwicklungen auf einem anderen Betriebssystem als Microsoft Windows durchführen.

### Installieren von Node.js

Node.js kann unter <http://nodejs.org/#download> installiert werden. Wenn für Ihr Betriebssystem kein Installationspaket verfügbar ist, können Sie Node.js von der Quelle erstellen.

Vorgehensweise: Erstellen einer SQL-Datenbank
---------------------------------------------

Führen Sie folgende Schritte durch, um eine Azure SQL-Datenbank zu erstellen:

1.  Melden Sie sich beim [Vorschau-Verwaltungsportal](https://manage.windowsazure.com) an.
2.  Klicken Sie unten links im Portal auf das Symbol **+ New**.

    ![Neue Azure-Website erstellen](./media/sql-database-nodejs-how-to-use/new_website.jpg)

3.  Klicken Sie auf **SQL-Datenbank** und dann auf **Custom Create**.

    ![Eine neue SQL-Datenbank benutzerdefiniert erstellen](./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg)

4.  Geben Sie einen Wert für den **NAMEN** Ihrer Datenbank ein, wählen Sie die **EDITION** (WEB oder BUSINESS), wählen Sie die **MAX. GRÖSSE** für Ihre Datenbank, wählen Sie **SORTIERUNG**, und wählen Sie **NEW SQL Database Server**. Klicken Sie unten im Dialogfeld auf den Pfeil. (Falls Sie zuvor bereits eine SQL-Datenbank erstellt haben, können Sie aus der Dropdownliste **Choose a server** einen vorhandenen Server auswählen.)

    ![SQL-Datenbank-Einstellungen einfügen](./media/sql-database-nodejs-how-to-use/new-sql-db.png)

5.  Geben Sie einen Administratornamen und ein Kennwort ein (und bestätigen Sie das Kennwort), wählen Sie die Region aus, in der Ihre neue SQL-Datenbank erstellt wird, und markieren Sie das Kontrollkästchen `Allow Azure Services to access the server`.

    ![Neuen SQL-Datenbank-Server erstellen](./media/sql-database-nodejs-how-to-use/db-server-settings.png)

Klicken Sie auf **SQL-Datenbanken**, um im Vorschau-Verwaltungsportal Server- und Datenbankinformationen anzuzeigen. Sie können auf **DATENBANKEN** oder **SERVER** klicken, um die entsprechenden Informationen anzuzeigen.

![Server- und Datenbankinformationen anzeigen](./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png)

Vorgehensweise: Abrufen von SQL-Datenbank-Verbindungsinformationen
------------------------------------------------------------------

Klicken Sie im Portal auf **SQL-DATENBANKEN** und dann auf den Namen der Datenbank, um SQL-Datenbank-Verbindungsinformationen abzurufen.

![Datenbankinformationen anzeigen](./media/sql-database-nodejs-how-to-use/go-to-db-info.png)

Klicken Sie dann auf **Show connection strings**.

![Verbindungszeichenfolgen anzeigen](./media/sql-database-nodejs-how-to-use/show-connection-string.png)

Notieren Sie sich die Werte für die Verbindungszeichenfolge, die im ODBC-Bereich des Ergebnisfensters angezeigt werden. Diese Verbindungszeichenfolge verwenden Sie, wenn Sie von Ihrer Knotenanwendung aus eine Verbindung zur SQL-Datenbank herstellen. Ihr Kennwort ist das Kennwort, das Sie zum Erstellen der SQL-Datenbank verwendet haben.

Vorgehensweise: Herstellen einer Verbindung zu einer SQL-Datenbankinstanz
-------------------------------------------------------------------------

### Installieren von node-sqlserver

Der Microsoft Driver for Node.JS for SQL Server ist als das native Modul node-sqlserver erhältlich. Eine binäre Version dieses Moduls ist im [Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=29995) erhältlich. Führen Sie die folgenden Schritte aus, um die binäre Version zu verwenden:

1.  Extrahieren Sie das binäre Archiv in das Verzeichnis **node\_modules** für Ihre Anwendung.
2.  Führen Sie die aus dem Archiv extrahierte Datei **node-sqlserver-install.cmd** aus. Daraufhin wird unter **node\_modules** ein Unterverzeichnis **node-sqlserver** erstellt, und die Treiberdateien werden in diese neue Verzeichnisstruktur verschoben.
3.  Löschen Sie die Datei **node-sqlserver-install.cmd**, da Sie nicht mehr benötigt wird.

**Hinweis**

Sie können das Modul node-sqlserver auch mithilfe des npm-Dienstprogramms installieren. Dies führt allerdings dazu, dass node-gyp eine binäre Version des Moduls auf Ihrem System errichtet.

### Festlegen der Verbindungszeichenfolge

Sie müssen node-sqlserver in Ihrer Anwendung anfordern und eine Verbindungszeichenfolge festlegen, um es zu verwenden. Die Verbindungszeichenfolge sollte der ODBC-Wert sein, der im Bereich [Vorgehensweise: Abrufen von SQL-Datenbank-Verbindungsinformationen](#ConnectionInfo) dieses Artikels zurückgegeben wurde. Der Code sieht in etwa folgendermaßen aus:

    var sql = require('node-sqlserver');
    var sql = require('node-sqlserver'); var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### Abfragen der Datenbank

Abfragen können durch Festlegen eines Transact-SQL-Ausdrucks mit der **Abfrage** methode durchgeführt werden. Der folgende Code erstellt einen HTTP-Server und gibt Daten aus den Zeilen **ID**, **Spalte1** und **Spalte2** in der Tabelle **Test** zurück, wenn Sie die Webseite anzeigen:

    var http = require('http')
    var port = process.env.port||3000;
    http.createServer(function (req, res) {
        sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
            if (err) {
                res.writeHead(500, { 'Content-Type': 'text/plain' });
                res.write("Got error :-( " + err);
                res.end("");
                return;
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            for (var i = 0; i < results.length; i++) {
                res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
            }
            res.end("; Done.");
        });
    }).listen(port);

Das Beispiel oben zeigt, wie Sie alle Zeilen gleichzeitig in der Ergebniserfassung zurückgeben können. Sie können auch ein Anweisungsobjekt zurückgeben, mit dem Sie Ereignisse abonnieren können. So erhalten Sie einzelne Zeilen und Spalten, wenn diese zurückgegeben werden. Das folgende Beispiel zeigt die Vorgehensweise:

    var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
    stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
    stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
    stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
    stmt.on('done', function () { console.log("All done!"); });
    stmt.on('error', function (err) { console.log("We had an error: " + err); });

Berücksichtigungen für die Azure-Bereitstellung
-----------------------------------------------

**Hinweis**

Die folgenden Informationen basieren auf einer Vorschauversion von Microsoft Driver for Node.JS for SQL Server. Die Informationen in diesem Abschnitt sind möglicherweise im Hinblick auf die Verwendung des Moduls node-sqlserver mit Azure nicht auf dem neuesten Stand. Die aktuellsten Informationen erhalten Sie auf GitHub unter [Projektseite zu Microsoft Driver for Node.JS for SQL Server](https://github.com/WindowsAzure/node-sqlserver).

Azure installiert das Modul node-sqlserver während der Laufzeit nicht dynamisch. Deshalb müssen Sie sicherstellen, dass Ihre Anwendungsbereitstellung eine binäre Version des Moduls enthält. Wenn Sie überprüfen möchten, ob Ihre Bereitstellung eine binäre Version des Moduls enthält, stellen Sie sicher, dass die folgende Verzeichnisstruktur vorhanden ist und die unten beschriebenen Dateien enthält:

    application directory
        node_modules
            node-sqlserver
                lib

Das Verzeichnis **node-sqlserver** sollte eine Datei **package.json** enthalten. Das Verzeichnis **lib** sollte eine Datei **sql.js** und eine Datei **sqlserver.node** enthalten. Bei letzterer handelt es sich um die kompilierte Form des Moduls node-sqlserver.

Weitere Informationen zum Bereitstellen einer Node.js-Anwendung für Azure erhalten Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/) und [Node.js-Clouddienst](/en-us/develop/nodejs/tutorials/getting-started/).

Nächste Schritte
----------------

-   [Introducing the Microsoft Driver for Node.JS for SQL Server (Einführung des Microsoft Driver for Node.JS for SQL Server, in englischer Sprache)](http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx)
-   [Microsoft Driver for Node.js for SQL Server on Github.com (Microsoft Driver for Node.js for SQL Server auf Github.com, in englischer Sprache)](https://github.com/WindowsAzure/node-sqlserver)

