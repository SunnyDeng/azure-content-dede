<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Web site with SQL Database" pageTitle="Node.js web site with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="Node.js" title="Node.js Web Application using the Azure SQL Database" authors="" solutions="" manager="" editor="" />

Node.js-Webanwendung mit Azure-SQL-Datenbank
============================================

In diesem Lernprogramm erfahren Sie, wie Sie die von der Azure-Datenverwaltung bereitgestellte SQL-Datenbank einsetzen können, um Daten von einer auf Azure gehosteten [Node](http://nodejs.org)-Anwendung zu speichern und abzurufen. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie bereits eine gewisse Erfahrung mit der Verwendung von Node und [Git](http://git-scm.com) haben.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Azure-Website und SQL-Datenbank über das Azure-Verwaltungsportal

-   Installieren von Node-Modulen über NPM (Node Package Manager = Paketverwaltung von Node.js)

-   Arbeiten mit einer SQL-Datenbank mithilfe des node-sqlserver-Moduls

-   Verwenden von App-Einstellungen zur Vorgabe von Laufzeitwerten für eine Anwendung

Im Verlauf dieses Lernprogramms erstellen wir eine einfache webbasierte Aufgabenverwaltungsanwendung, die das Erstellen, Abrufen und Abschließen von Aufgaben ermöglicht. Die Aufgaben werden in einer SQL-Datenbank gespeichert.

Die Projektdateien für dieses Lernprogramm werden in einem Verzeichnis namens **tasklist** gespeichert, und die fertige Anwendung wird etwa wie folgt aussehen:

![Eine Webseite mit einer leeren Aufgabenliste](./media/sql-database-nodejs-use-web-site/sql_todo_final.png)

**Hinweis**

Der in diesem Lernprogramm verwendete Microsoft Driver for Node.JS for SQL Server ist zur Zeit als Vorschau-Version verfügbar und benötigt Laufzeit-Komponenten, die nur in den Microsoft Windows- und Azure-Betriebssystemen enthalten sind.

**Hinweis**

In diesem Lernprogramm wird auf den Ordner **tasklist** Bezug genommen. Der volle Pfad zu diesem Ordner wird nicht angegeben, da die Pfad-Syntax je nach Betriebssystem variiert. Es empfiehlt sich, diesen Ordner an einer im lokalen Dateisystem leicht zugänglichen Stelle zu erstellen, wie zum Beispiel **\~/node/tasklist** oder **c:\\node\\tasklist**

**Hinweis**

Im Folgenden wird öfters Verwenden der Befehlszeile verlangt. In diesen Fällen verwenden Sie die Befehlszeile für Ihr Betriebssystem, wie zum Beispiel **cmd.exe** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die Terminal-Anwendung zugreifen.

Voraussetzungen
---------------

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes installiert ist:

-   [node](http://nodejs.org), Version 0.6.14 oder höher

-   [Git](http://git-scm.com)

-   Microsoft SQL Server Systemeigene Client-Bibliotheken - verfügbar als Teil von [Microsoft SQL Server 2012 Feature Pack](http://www.microsoft.com/en-us/download/details.aspx?id=29065)

-   Ein Texteditor

-   Ein Webbrowser

Erstellen einer Website mit Datenbank
-------------------------------------

Führen Sie die folgenden Schritte aus, um eine Azure-Website und eine SQL-Datenbank zu erstellen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2.  Klicken Sie unten links im Portal auf das Symbol **+ New**.

    ![Erstellen einer neuen Azure-Website](./media/sql-database-nodejs-use-web-site/new_website.jpg)

3.  Klicken Sie auf **WEBSITE** und dann auf **CUSTOM CREATE**.

    ![Benutzerdefiniertes Erstellen einer neuen Website](./media/sql-database-nodejs-use-web-site/custom_create.png)

    Geben Sie einen Wert für **URL** ein, wählen Sie **Create a New SQL Database** (Neue SQL-Datenbank erstellen) aus der Dropdown-Liste **DATENBANK** aus, und wählen Sie das Datencenter für Ihre Website aus der Dropdown-Liste **REGION** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Angeben der Website-Details](./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg)

4.  Geben Sie im Feld **NAME** einen Namen für Ihre Datenbank ein, wählen Sie die **EDITION** [(WEB oder BUSINESS)](http://msdn.microsoft.com/de-de/library/windowsazure/ee621788.aspx), die **MAXIMALE GRÖSSE** für Ihre Datenbank, die **SORTIERUNG** und schließlich **Neuer SQL Datenbankserver**. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Angeben der SQL-Datenbankeinstellungen](./media/sql-database-nodejs-use-web-site/database_settings.jpg)

5.  Geben Sie einen Administratornamen und ein Kennwort ein (bestätigen Sie das Kennwort anschließend), wählen Sie die Region, in der Ihr neuer SQL-Datenbankserver erstellt werden soll, und aktivieren Sie das Kontrollkästchen **Ermöglichen Sie Azure-Diensten den Zugriff auf den Server.**.

    ![Erstellen eines neuen SQL-Datenbankservers](./media/sql-database-nodejs-use-web-site/create_server.jpg)

    Nach Erstellung der Website wird der Text **Die Website '[SITENAME]' wurde erfolgreich erstellt.** angezeigt. Nun können Sie Git-Veröffentlichen aktivieren.

6.  Klicken Sie auf den Namen der Website, der in der Liste der Websites aufgeführt wird, um das Schnellstart-Dashboard der Website zu öffnen.

    ![Öffnen des Dashboards der Website](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

7.  Klicken Sie unten auf der Schnellstart-Seite auf **Git-Veröffentlichen einrichten**.

    ![Einrichten von Git-Veröffentlichen](./media/sql-database-nodejs-use-web-site/setup_git_publishing.png)

8.  Um Git-Veröffentlichen zu aktivieren, müssen Sie einen Benutzernamen und ein Kennwort angeben. Notieren Sie sich den erstellten Benutzernamen und das Kennwort. (Wenn Sie zuvor bereits ein Git-Repository eingerichtet haben, wird dieser Schritt übersprungen.)

    ![Erstellen von Anmeldeinformationen für Veröffentlichen](./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png)

    Das Einrichten Ihres Repositorys nimmt einige Sekunden in Anspruch.

9.  Wenn Ihr Repository bereit ist, werden Anweisungen zum Pushen Ihrer Anwendungsdateien zum Repository angezeigt. Notieren Sie diese Anweisungen, da sie später benötigt werden.

    ![Git-Anweisungen](./media/sql-database-nodejs-use-web-site/git-instructions.png)

Erhalten von SQL-Datenbankverbindungsinformationen
--------------------------------------------------

Um eine Verbindung zur auf Azure-Websites laufenden SQL-Datenbankinstanz herzustellen, benötigen Sie die Verbindungsinformationen. Um die SQL-Datenbankverbindungsinformationen abzurufen, führen Sie die folgenden Schritte durch:

1.  Klicken Sie im Azure-Verwaltungsportal auf **VERKNÜPFTE RESSOURCEN**, und klicken Sie dann auf den Datenbanknamen.

    ![Verknüpfte Ressourcen](./media/sql-database-nodejs-use-web-site/linked_resources.jpg)

2.  Klicken Sie auf **View connection strings** (Verbindungszeichenfolgen anzeigen).

    ![Verbindungszeichenfolge](./media/sql-database-nodejs-use-web-site/connection_string.jpg)

3.  Vom **ODBC**-Abschnitt des folgenden Dialogs notieren Sie die Verbindungszeichenfolge, da diese später benötigt wird.

Gestalten der Aufgabentabelle
-----------------------------

Um die Datenbanktabelle zum Speichern von Elementen für die Aufgabenlistenanwendung zu erstellen, führen Sie die folgenden Schritte durch:

1.  Im Azure-Verwaltungsportal wählen Sie Ihre SQL-Datenbank und klicken Sie dann unten auf der Seite auf **VERWALTEN**. Wenn eine Meldung angezeigt wird, die besagt, dass die aktuelle IP-Adresse nicht in den vorhandenen Firewallregeln enthalten ist, klicken Sie auf **JA**, um die IP-Adresse hinzuzufügen.

    ![Schaltfläche VERWALTEN](./media/sql-database-nodejs-use-web-site/sql-manage.png)

2.  Melden Sie sich mit dem Benutzernamen und dem Kennwort an, den/das Sie zuvor beim Erstellen des Datenbankservers festgelegt hatten.

    ![Anmeldung zur Datenbankverwaltung](./media/sql-database-nodejs-use-web-site/sqlazurelogin.png)

3.  Klicken Sie unten links auf der Seite auf **Entwerfen** und wählen Sie dann **Neue Tabelle**.

    ![Neue Tabelle](./media/sql-database-nodejs-use-web-site/new-table.png)

4.  Tragen Sie 'tasks' in das Feld **Tabellenname** ein und aktivieren Sie **Ist 'Identität'?** für die **ID**-Zeile.

    ![Tabellenname festgelegt auf "tasks" und "Ist 'Identität'" aktiviert](./media/sql-database-nodejs-use-web-site/table-name-identity.png)

5.  Ändern Sie **Spalte1** zu **name** und **Spalte2** zu **category**. Fügen Sie zwei neue Spalten hinzu, indem Sie auf die Schaltfläche **Spalte hinzufügen** klicken. Die erste neue Spalte sollte **created** genannt werden und vom Typ **date** sein. Die zweite neue Spalte sollte **completed** genannt werden und vom Typ **bit** sein. Beide neue Spalten sollten als **Ist 'Erforderlich'?** markiert werden.

    ![abgeschlossener Tabellenentwurf](./media/sql-database-nodejs-use-web-site/table-columns.png)

6.  Klicken Sie auf die Schaltfläche **Speichern**, um die Tabellenänderungen zu sichern. Sie können nun die SQL-Datenbankverwaltungsseite schließen.

Installieren der Module und Erstellen des Gerüsts
-------------------------------------------------

In diesem Abschnitt erstellen Sie eine neue Node-Anwendung und fügen Sie Modulpakete mithilfe von npm hinzu. Für die Aufgabenlistenanwendung verwenden Sie die Module [Express](http://expressjs.com) und [Node-sqlserver](https://github.com/WindowsAzure/node-sqlserver). Das Express-Modul stellt ein Modellansichts-Controller-Framework für Node bereit, das Modul Node-sqlserver Konnektivität zur Azure-SQL-Datenbank.

### Installieren von Express und Erstellen des Gerüsts

1.  Wechseln Sie das Verzeichnis von der Befehlszeile aus zu **tasklist**. Wenn das Verzeichnis **tasklist** noch nicht besteht, erstellen Sie es.

2.  Geben Sie den folgenden Befehl ein, um Express zu installieren.

         npm install express -g

    **Hinweis**

    Gebrauch des Parameters '-g' kann bei manchen Betriebssystemen einen Fehler **Error: EPERM, chmod '/usr/local/bin/express'** verursachen und zu einer Aufforderung führen, das Konto als Administrator zu betreiben versuchen. In diesem Fall verwenden Sie den Befehl **sudo**, um npm auf einer höheren Berechtigungsstufe auszuführen.

    Die Ausgabe dieses Befehls sollte etwa wie folgt aussehen:

         express@2.5.9 /usr/local/lib/node_modules/express
		├── mime@1.2.4
		├── mkdirp@0.3.0
		├── qs@0.4.2
		└── connect@1.8.7

    **Hinweis**

    Wird der Parameter '-g' bei der Installation des Moduls Express verwendet, so erfolgt die Installation global. Dies ist praktisch, weil der **express**-Befehl dann zur Website-Gerüsterstellung eingesetzt werden kann, ohne zusätzliche Pfadinformationen angeben zu müssen.

3.  Zum Erstellen des Gerüsts, das wir für diese Anwendung heranziehen werden, verwenden Sie den **express**-Befehl:

         express

    Die Ausgabe dieses Befehls sollte etwa wie folgt aussehen:

         create : .
            create : ./package.json
            create : ./app.js
            create : ./public
            create : ./public/javascripts
            create : ./public/images
            create : ./public/stylesheets
            create : ./public/stylesheets/style.css
            create : ./routes
            create : ./routes/index.js
            create : ./views
            create : ./views/layout.jade
            create : ./views/index.jade
            
            dont forget to install dependencies:
            $ cd . && npm install

    Nach der Ausführung dieses Befehls sollten sich einige neue Verzeichnisse und Dateien im **tasklist**-Verzeichnis befinden.

### Installieren zusätzlicher Module

1.  Wechseln Sie das Verzeichnis von der Befehlszeile aus zum Ordner **tasklist**, und geben Sie Folgendes ein, um die in der Datei **package.json** beschriebenen Module zu installieren.

         npm install

    Die Ausgabe dieses Befehls sollte etwa wie folgt aussehen:

         express@2.5.8 ./node_modules/express
		├── mime@1.2.4
		├── qs@0.4.2
		├── mkdirp@0.3.0
		└── connect@1.8.7
		jade@0.26.0 ./node_modules/jade
		├── commander@0.5.2
		└── mkdirp@0.3.0

    Hierdurch werden alle von Express benötigten Standardmodule installiert.

2.  Nun wollen wir das nconf-Modul hinzufügen. Die Anwendung benötigt dieses Modul zum Lesen der Datenbank-Verbindungszeichenfolge von einer Konfigurationsdatei.

    npm install nconf -save

3.  Als Nächstes laden wir die binäre Version von Microsoft Driver for Node.JS for SQL Server vom [Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=29995) herunter.

4.  Entpacken Sie das Archiv in das Verzeichnis **tasklist\\node\_modules**.

5.  Führen Sie die Datei **msnodesql-install.cmd** im Verzeichnis **tasklist\\node\_modules** aus. Hierdurch wird das Unterverzeichnis **msnodesql** unter **node\_modules** erstellt, und die Treiberdateien werden in diese neue Verzeichnisstruktur verschoben.

6.  Löschen Sie die Datei **msnodesql-install.cmd**, da sie nicht mehr benötigt wird.

Verwenden der SQL-Datenbank in einer Node-Anwendung
---------------------------------------------------

In diesem Abschnitt erweitern Sie die mit dem **express**-Befehl erstellte Grundanwendung, indem Sie die bestehende Datei **app.js** modifizieren und Sie eine neue Datei **index.js** erstellen, um die zuvor erstellte Datenbank zu verwenden.

### Modifizieren des Controllers

1.  Im Verzeichnis **tasklist/routes** öffnen Sie die Datei **index.js** mit einem Texteditor.

2.  Ersetzen Sie den bestehenden Code in der Datei **index.js** durch den folgenden Code. Hierdurch werden die Module msnodesql und nconf geladen, und dann nconf ausgeführt, um die Verbindungszeichenfolge entweder von einer Umgebungsvariablen namens **SQL\_CONN** oder einem **SQL\_CONN**-Wert in der **config.json**-Datei zu beziehen.

         var sql = require('msnodesql')
             , nconf = require('nconf');

         nconf.env()
              .file({ file: 'config.json' });
         var conn = nconf.get("SQL_CONN");

3.  Weiterhin fügen Sie der **index.js**-Datei die Methoden **index** und **updateItem** hinzu. Die Methode **index** gibt alle unvollendeten Aufgaben von der Datenbank zurück, während **updateItem** ausgewählte Aufgaben als abgeschlossen markiert.

         exports.index = function(req, res) {
             var select = "select * from tasks where completed = 0";
             sql.query(conn, select, function(err, items) {
                 if(err)
                     throw err;
                 res.render('index', { title: 'My ToDo List ', tasks: items });
             });
         };

         exports.updateItem = function(req, res) {
             var item = req.body.item;
             if(item) {
                 var insert = "insert into tasks (name, category, created, completed) values (
         , 
         , GETDATE(), 0)";
                 sql.query(conn, insert, [item.name, item.category], function(err) {
                     if(err)
                         throw err;
                     res.redirect('/');
                 });
             } else {
                 var completed = req.body.completed;
                 if(!completed.forEach)
                     completed = [completed];
                 var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
                 sql.query(conn, update, function(err) {
                     if(err)
                         throw err;
                     res.redirect('/');
                 });
             }
         }

4.  Speichern Sie die Datei **index.js**.

### Modifizieren von app.js

1.  Im Verzeichnis **tasklist** öffnen Sie die Datei **app.js** mit einem Texteditor. Diese Datei wurde zuvor durch Ausführung des **express**-Befehls erstellt.

2.  Scrollen Sie in der Datei app.js nach unten, bis Sie den folgenden Code finden.

        app.configure('development', function(){
    	app.use(express.errorHandler());
        });

3.  Fügen Sie nun den folgenden Code ein.

         app.get('/', routes.index);
         app.post('/', routes.updateItem);

Hierdurch erhält die **updateItem**-Methode, die Sie zuvor in der Datei **index.js** hinzugefügt haben, eine neue Route.

1.  Speichern Sie die Datei **app.js**.

### Modifizieren der Indexansicht

1.  Wechseln Sie das Verzeichnis zu **views** und öffnen Sie die Datei **index.jade** mit einem Texteditor.

2.  Ersetzen Sie den Inhalt der Datei **index.jade** durch den untenstehenden Code. Hierdurch wird die Ansicht sowohl für das Anzeigen bestehender Aufgaben als auch als Form für das Hinzufügen neuer Aufgaben und Markieren bestehender Aufgaben als abgeschlossen definiert.

         h1= title
         br

         form(action="/", method="post")
           table(class="table table-striped table-bordered")
             thead
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
             tbody
             each task in tasks
               tr
                 td #{task.name}
                 td #{task.category}
                 td #{task.created}
                 td
                   input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
           button(type="submit", class="btn") Update tasks
         hr

         form(action="/", method="post", class="well")
           label Item Name:
           input(name="item[name]", type="textbox")
           label Item Category:
           input(name="item[category]", type="textbox")
           br
           button(type="submit", class="btn") Add Item

3.  Speichern und schließen Sie die Datei **index.jade**.

### Modifizieren des globalen Layouts

Die Datei **layout.jade** im **views**-Verzeichnis dient als globale Vorlage für andere **.jade**-Dateien. In diesem Schritt werden wir sie modifizieren, um [Twitter Bootstrap](https://github.com/twbs/bootstrap) zu verwenden, ein Toolkit zum mühelosen Gestalten gut aussehender Websites.

1.  Laden Sie die Dateien für [Twitter Bootstrap](http://getbootstrap.com/) herunter und entpacken Sie sie. Kopieren Sie die Datei **bootstrap.min.css** vom **bootstrap\\css**-Ordner in das Verzeichnis **public\\stylesheets** Ihrer Aufgabenlistenanwendung.

2.  Im **views**-Ordner öffnen Sie die Datei **layout.jade** mit einem Texteditor und ersetzen Sie den Inhalt durch Folgendes:

         !!!html
         html
           head
             title= title
             meta(http-equiv='X-UA-Compatible', content='IE=10')
             link(rel='stylesheet', href='/stylesheets/style.css')
             link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
           body(class='app')
             div(class='navbar navbar-fixed-top')
               .navbar-inner
                 .container
                   a(class='brand', href='/') My Tasks
             .container!= body

3.  Speichern Sie die Datei **layout.jade**.

### Erstellen der Konfigurationsdatei

Die Datei **config.json** enthält die Verbindungszeichenfolge, die zur Verbindung mit der SQL-Datenbank verwendet und zu Laufzeit von der Datei **index.js** gelesen wird. Zum Erstellen dieser Datei führen Sie die folgenden Schritte durch:

1.  Im Verzeichnis **tasklist** erstellen Sie eine neue Datei mit dem Namen **config.json**, und öffnen Sie diese mit einem Texteditor.

2.  Der Inhalt der Datei **config.json** sollte etwa wie folgt aussehen:

         {
           "SQL_CONN" : "connection_string"
         }

    Ersetzen Sie **connection\_string** durch den zuvor erhaltenen ODBC-Verbindungszeichenfolgewert.

3.  Speichern Sie die Datei.

Lokales Ausführen der Anwendung
-------------------------------

Um die Anwendung lokal zu testen, führen Sie die folgenden Schritte durch:

1.  Wechseln Sie das Verzeichnis von der Befehlszeile aus zu **tasklist**.

2.  Verwenden Sie den folgenden Befehl, um die Anwendung lokal zu starten:

         node app.js

3.  Öffnen Sie einen Webbrowser und navigieren Sie zu http://127.0.0.1:3000. Dies sollte zur Anzeige einer Webseite führen, die etwa wie folgt aussieht:

    ![Eine Webseite mit einer leeren Aufgabenliste](./media/sql-database-nodejs-use-web-site/sql_todo_empty.png)

4.  Füllen Sie die Felder für **Elementname** und **Elementkategorie** aus, und klicken Sie dann auf **Element hinzufügen**.

5.  Die Seite sollte nun das Element in der Aufgabenliste anzeigen.

    ![Ein Bild des neuen Elements in der Aufgabenliste](./media/sql-database-nodejs-use-web-site/sql_todo_list.png)

6.  Zum Abschließen einer Aufgabe aktivieren Sie einfach das Kontrollkästchen in der Spalte Abschließen, und klicken Sie dann auf **Aufgaben aktualisieren**.

7.  Zum Stoppen des Node-Prozesses betätigen Sie von der Befehlszeile aus gleichzeitig die Tasten **CTRL** und **C**.

Bereitstellen der Anwendung für Azure
-------------------------------------

In diesem Abschnitt führen Sie die Bereitstellungsschritte durch, die Sie nach dem Erstellen der Website erhalten haben, um die Anwendung für Azure zu veröffentlichen.

### Veröffentlichen der Anwendung

1.  Wechseln Sie das Verzeichnis von der Befehlszeile aus zu **tasklist**, falls dieses nicht schon gewählt ist.

2.  Verwenden Sie die folgenden Befehle, um ein lokales Git-Repository für Ihre Anwendung zu initialisieren, fügen Sie diesem die Anwendungsdateien hinzu, und pushen Sie die Dateien dann zu Azure

         git init
         git add .
         git commit -m "adding files"
         git remote add azure [URL for remote repository]
         git push azure master

    Bei Ende der Bereitstellung sollte ein Statement angezeigt werden, das etwa wie folgt aussieht:

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
	    \* [new branch] master -\> master

3.  Nachdem der Pushvorgang abgeschlossen ist, browsen Sie zu **http://[site name].azurewebsites.net/**, um Ihre Anwendung anzuzeigen.

### Wechseln zu einer Umgebungsvariablen

Zuvor haben wir einen Code implementiert, der nach einer **SQL\_CONN**-Umgebungsvariablen für die Verbindungszeichenfolge sucht oder den Wert von der **config.json**-Datei lädt. In den folgenden Schritten erstellen Sie ein Schlüssel/Wert-Paar in Ihrer Website-Konfiguration für echten Zugriff der Anwendung durch eine Umgebungsvariable.

1.  Klicken Sie im Azure-Verwaltungsportal auf **WEBSITES** und wählen Sie dann Ihre Website aus.

    ![Öffnen des Dashboards der Website](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

2.  Klicken Sie auf **KONFIGURIEREN** und suchen Sie den Abschnitt **app-einstellungen** der Seite auf.

    ![Konfigurationslink](./media/sql-database-nodejs-use-web-site/sql-task-configure.png)

3.  Im Abschnitt **app-einstellungen** geben Sie **SQL\_CONN** im Feld **SCHLÜSSEL** ein, und die ODBC-Verbindungszeichenfolge im Feld **WERT**. Klicken Sie schließlich auf das Häkchen.

    ![app-einstellungen](./media/sql-database-nodejs-use-web-site/appsettings.png)

4.  Zum Abschluss klicken Sie auf das Symbol **SPEICHERN** unten auf der Seite, um diese Änderung in die Laufzeitumgebung zu übernehmen.

    ![app-einstellungen speichern](./media/sql-database-nodejs-use-web-site/savebutton.png)

5.  Wechseln Sie das Verzeichnis von der Befehlszeile aus zu **tasklist**, und entfernen Sie die **config.json**-Datei mit dem folgenden Befehl:

         git rm config.json
         git commit -m "Removing config file"

6.  Führen Sie den folgenden Befehl aus, um die Änderungen für Azure bereitzustellen:

         git push azure master

Nachdem die Änderungen für Azure bereitgestellt worden sind, sollte Ihre Webanwendung weiterhin funktionieren, da sie nun die Verbindungszeichenfolge vom Eintrag in den **app-einstellungen** bezieht. Um dies zu bestätigen, ändern Sie den Wert für den Eintrag **SQL\_CONN** unter **app-einstellungen** zu einem ungültigen Wert. Nachdem Sie diesen Wert gespeichert haben, sollte die Website wegen der ungültigen Verbindungszeichenfolge nicht mehr funktionieren.

Nächste Schritte
----------------

-   [Node.js-Webanwendung mit MongoDB](../store-mongolab-web-sites-nodejs-store-data-mongodb/)

-   [Node.js-Webanwendung mit Tabellenspeicher](/de-de/develop/nodejs/tutorials/web-site-with-storage/)

Zusätzliche Ressourcen
----------------------

[Azure-Befehlszeilentool für Mac und Linux](/de-de/develop/nodejs/how-to-guides/command-line-tools/)
