<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Website with SQL Database" pageTitle="Node.js website with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="nodejs" title="Node.js Web Application using the Azure SQL Database" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Node.js-Webanwendung mit Azure-SQL-Datenbank

In diesem Lernprogramm erfahren Sie, wie Sie die von der Azure-Datenverwaltung bereitgestellte SQL-Datenbank einsetzen können, um Daten von einer auf Azure gehosteten [Node][Node]-Anwendung zu speichern und abzurufen. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie bereits eine gewisse Erfahrung mit der Verwendung von Node und [Git][Git] haben.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Azure-Website und SQL-Datenbank über das Azure-Verwaltungsportal

-   Installieren von Node-Modulen über NPM (Node Package Manager = Paketverwaltung von Node.js)

-   Arbeiten mit einer SQL-Datenbank mithilfe des node-sqlserver-Moduls

-   Verwenden von App-Einstellungen zur Vorgabe von Laufzeitwerten für eine Anwendung

In diesem Lernprogramm erstellen Sie eine einfache webbasierte Anwendung zur Aufgabenverwaltung, mit der Sie Aufgaben erstellen, abrufen und vervollständigen können. Die Aufgaben werden in einer SQL-Datenbank gespeichert.

Die Projektdateien für dieses Lernprogramm werden in einem Verzeichnis namens **tasklist** gespeichert, und die fertige Anwendung sieht etwa wie folgt aus:

![Webseite mit einer leeren Aufgabenliste][Webseite mit einer leeren Aufgabenliste]

<div class="dev-callout">
<b>Hinweis</b>
<p>Der in diesem Lernprogramm verwendete Microsoft Driver for Node.JS for SQL Server ist zur Zeit als Vorschau-Version verf&uuml;gbar und ben&ouml;tigt Laufzeit-Komponenten, die nur in den Microsoft Windows- und Azure-Betriebssystemen enthalten sind.</p>
</div>

<div class="dev-callout">
<strong>Hinweis</strong>
<p>In diesem Lernprogramm wird auf den Ordner <strong>tasklist</strong> Bezug genommen. Der vollst&auml;ndige Ordnerpfad wird nicht angegeben, da die Pfadsemantik je nach Betriebssystem unterschiedlich ist. Sie sollten diesen Ordner in einem Verzeichnis erstellen, auf das Sie im lokalen Dateisystem einfach zugreifen k&ouml;nnen, z. B. <strong>~/node/tasklist</strong> oder <strong>c:\node\tasklist</strong>.</p>
</div>

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Im Folgenden wird &ouml;fters die Verwendung der Befehlszeile verlangt. In diesen F&auml;llen verwenden Sie die Befehlszeile f&uuml;r Ihr Betriebssystem, wie zum Beispiel <strong>cmd.exe</strong> (Windows) oder <strong>Bash</strong> (Unix Shell). Auf OS X-Systemen k&ouml;nnen Sie auf die Befehlszeile &uuml;ber die Terminal-Anwendung zugreifen.</p>
</div>

## Voraussetzungen

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes installiert ist:

-   [node][Node], Version 0.6.14 oder höher

-   [Git][Git]

-   Microsoft SQL Server Systemeigene Client-Bibliotheken - verfügbar als Teil von [Microsoft SQL Server 2012 Feature Pack][Microsoft SQL Server 2012 Feature Pack]

-   Ein Texteditor

-   Ein Webbrowser

<!--div chunk="../../Shared/Chunks/create-account-and-websites-note.md" /-->

## Erstellen einer Website mit Datenbank

Führen Sie die folgenden Schritte aus, um eine Azure-Website und eine SQL-Datenbank zu erstellen:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![Erstellen einer neuen Azure-Website][Erstellen einer neuen Azure-Website]

3.  Klicken Sie auf **WEBSITE** und dann auf **BENUTZERDEFINIERT ERSTELLEN**.

    ![Benutzerdefiniertes Erstellen einer neuen Website][Benutzerdefiniertes Erstellen einer neuen Website]

    Geben Sie einen Wert für **URL** ein, und wählen Sie **Neue SQL-Datenbank erstellen** in der Dropdownliste **DATENBANK** und das Rechenzentrum für Ihre Website in der Dropdownliste **REGION** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Angeben der Website-Details][Angeben der Website-Details]

4.  Geben Sie im Feld **NAME** einen Namen für Ihre Datenbank ein, und wählen Sie die **EDITION** [(WEB oder BUSINESS)][(WEB oder BUSINESS)], die **MAXIMALE GRÖSSE** für Ihre Datenbank, die **SORTIERUNG** und schließlich **Neuer SQL-Datenbankserver** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![SQL-Datenbankeinstellungen eingeben][SQL-Datenbankeinstellungen eingeben]

5.  Geben Sie einen Administratornamen und ein Kennwort ein (bestätigen Sie das Kennwort anschließend), wählen Sie die Region aus, in der Ihr neuer SQL-Datenbankserver erstellt werden soll, und aktivieren Sie das Kontrollkästchen **Ermöglichen Sie Azure-Diensten den Zugriff auf den Server**.

    ![Einen neuen SQL-Datenbankserver erstellen][Einen neuen SQL-Datenbankserver erstellen]

    Nachdem die Website erstellt wurde, wird der Text **Die Erstellung der Website "[WEBSITENAME]" wurde erfolgreich abgeschlossen** angezeigt. Nun können Sie die Git-Veröffentlichung aktivieren.

6.  Klicken Sie auf den Namen der Website, der in der Liste der Websites aufgeführt ist, um das Schnellstart-Dashboard der Website zu öffnen.

    ![Öffnen des Dashboards der Website][Öffnen des Dashboards der Website]

7.  Klicken Sie unten auf der Schnellstart-Seite auf **Git-Veröffentlichen einrichten**.

    ![Git-Veröffentlichung einrichten][Git-Veröffentlichung einrichten]

8.  Um die Git-Veröffentlichung aktivieren zu können, müssen Sie einen Benutzernamen und ein Kennwort angeben. Notieren Sie sich den Benutzernamen und das Kennwort, die Sie erstellen. (Wenn Sie schon einmal ein Git-Verzeichnis eingerichtet haben, wird dieser Schritt übersprungen.)

    ![Anmeldedaten für die Veröffentlichung erstellen][Anmeldedaten für die Veröffentlichung erstellen]

    Das Einrichten des Verzeichnisses dauert ein paar Sekunden.

9.  Wenn Ihr Repository bereit ist, werden Anweisungen zum Pushen Ihrer Anwendungsdateien zum Repository angezeigt. Notieren Sie diese Anweisungen, da sie später benötigt werden.

    ![Git-Anweisungen][Git-Anweisungen]

## Abrufen von SQL-Datenbank-Verbindungsinformationen

Um eine Verbindung mit der auf Azure-Websites laufenden SQL-Datenbankinstanz herzustellen, benötigen Sie die Verbindungsinformationen. Um die SQL-Datenbankverbindungsinformationen abzurufen, führen Sie die folgenden Schritte durch:

1.  Klicken Sie im Azure-Verwaltungsportal auf **VERKNÜPFTE RESSOURCEN**, und klicken Sie dann auf den Datenbanknamen.

    ![Verknüpfte Ressourcen][Verknüpfte Ressourcen]

2.  Klicken Sie auf **Verbindungszeichenfolgen anzeigen**.

    ![Verbindungszeichenfolge][Verbindungszeichenfolge]

3.  Vom **ODBC**-Abschnitt des folgenden Dialogs notieren Sie die Verbindungszeichenfolge, da diese später benötigt wird.

## Gestalten der Aufgabentabelle

Um die Datenbanktabelle zum Speichern von Elementen für die Aufgabenlistenanwendung zu erstellen, führen Sie die folgenden Schritte durch:

1.  Im Azure-Verwaltungsportal wählen Sie Ihre SQL-Datenbank und klicken Sie dann unten auf der Seite auf **VERWALTEN**. Wenn eine Meldung angezeigt wird, die besagt, dass die aktuelle IP-Adresse nicht in den vorhandenen Firewallregeln enthalten ist, klicken Sie auf **JA**, um die IP-Adresse hinzuzufügen.

    ![Schaltfläche VERWALTEN][Schaltfläche VERWALTEN]

2.  Melden Sie sich mit dem Benutzernamen und dem Kennwort an, den/das Sie zuvor beim Erstellen des Datenbankservers festgelegt hatten.

    ![Anmeldung zur Datenbankverwaltung][Anmeldung zur Datenbankverwaltung]

3.  Klicken Sie unten links auf der Seite auf **Entwerfen** und wählen Sie dann **Neue Tabelle**.

    ![Neue Tabelle][Neue Tabelle]

4.  Tragen Sie 'tasks' in das Feld **Tabellenname** ein und aktivieren Sie **Ist 'Identität'?** für die **ID**-Zeile.

    ![Tabellenname festgelegt auf "tasks" und "Ist 'Identität'" aktiviert][Tabellenname festgelegt auf "tasks" und "Ist 'Identität'" aktiviert]

5.  Ändern Sie **Spalte1** zu **name** und **Spalte2** zu **category**. Fügen Sie zwei neue Spalten hinzu, indem Sie auf die Schaltfläche **Spalte hinzufügen** klicken. Die erste neue Spalte sollte **created** genannt werden und vom Typ **date** sein. Die zweite neue Spalte sollte **completed** genannt werden und vom Typ **bit** sein. Beide neue Spalten sollten als **Ist 'Erforderlich'?** markiert werden.

    ![abgeschlossener Tabellenentwurf][abgeschlossener Tabellenentwurf]

6.  Klicken Sie auf die Schaltfläche **Speichern**, um die Tabellenänderungen zu sichern. Sie können nun die SQL-Datenbankverwaltungsseite schließen.

## Installieren der Module und Generieren des Gerüsts

In diesem Abschnitt erstellen Sie eine neue Node-Anwendung und fügen Modulpakete mithilfe von npm hinzu. Für die Aufgabenlistenanwendung verwenden Sie die Module [Express][Express] und [Node-sqlserver][Node-sqlserver]. Das Express-Modul stellt ein Modellansichts-Controller-Framework für Node bereit, das Modul Node-sqlserver Konnektivität zur Azure-SQL-Datenbank.

### Installieren von Express und Generieren des Gerüsts

1.  Wechseln Sie an der Befehlszeile zum Verzeichnis **tasklist**. Falls das Verzeichnis **tasklist** nicht vorhanden ist, erstellen Sie es.

2.  Geben Sie den folgenden Befehl für die Expressinstallation ein.

        npm install express -g

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Bei Verwendung des Parameters &quot;-g&quot; wird in einigen Betriebssystemen eventuell ein Fehler &auml;hnlich dem folgenden angezeigt: <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>, verbunden mit der Aufforderung, das Konto als Administrator auszuf&uuml;hren. Verwenden Sie in diesem Fall den Befehl <strong>sudo</strong>, um npm mit einer h&ouml;heren Berechtigungsstufe auszuf&uuml;hren.</p>
</div>

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

        express@2.5.9 /usr/local/lib/node_modules/express
        ├── mime@1.2.4
        ├── mkdirp@0.3.0
        ├── qs@0.4.2
        └── connect@1.8.7

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Wird der Parameter &quot;-g&quot; bei der Installation des Express-Moduls verwendet, so erfolgt die Installation global. Auf diese Weise kann der Befehl <strong>express</strong> aufgerufen werden, um das Websiteger&uuml;st zu generieren, ohne zus&auml;tzliche Pfadinformationen angeben zu m&uuml;ssen.</p>
</div>

3.  Verwenden Sie den Befehl **express**, um das Gerüst zu erstellen, das für diese Anwendung verwendet wird:

        express

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

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

    Nach Abschluss dieses Befehls sollten mehrere neue Verzeichnisse und Dateien im Verzeichnis **tasklist** vorhanden sein.

### Installieren zusätzlicher Module

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**, und geben Sie Folgendes ein, um die in der Datei **package.json** beschriebenen Module zu installieren:

        npm install

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

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

3.  Als Nächstes laden wir die binäre Version von Microsoft Driver for Node.JS for SQL Server vom [Download Center][Download Center] herunter.

4.  Entpacken Sie das Archiv in das Verzeichnis **tasklist\\node\_modules**.

5.  Führen Sie die Datei **msnodesql-install.cmd** im Verzeichnis **tasklist\\node\_modules** aus. Hierdurch wird das Unterverzeichnis **msnodesql** unter **node\_modules** erstellt, und die Treiberdateien werden in diese neue Verzeichnisstruktur verschoben.

6.  Löschen Sie die Datei **msnodesql-install.cmd**, da sie nicht mehr benötigt wird.

## Verwenden der SQL-Datenbank in einer Node-Anwendung

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
                var insert = "insert into tasks (name, category, created, completed) values (?, ?, GETDATE(), 0)";
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

### Ändern von app.js

1.  Öffnen Sie im Verzeichnis **tasklist** die Datei **app.js** in einem Texteditor. Diese Datei wurde zuvor mit dem Befehl **express** erstellt.

2.  Scrollen Sie in der Datei app.js nach unten, bis Sie den folgenden Code finden.

        app.configure('development', function(){
        app.use(express.errorHandler());
        });

3.  Fügen Sie nun den folgenden Code ein.

        app.get('/', routes.index);
        app.post('/', routes.updateItem);

Hierdurch erhält die **updateItem**-Methode, die Sie zuvor in der Datei **index.js** hinzugefügt haben, eine neue Route.

1.  Speichern Sie die Datei **app.js**.

### Ändern der Indexansicht

1.  Wechseln Sie zum Verzeichnis **views**, und öffnen Sie die Datei **index.jade** in einem Texteditor.

2.  Ersetzen Sie den Inhalt der Datei **index.jade** durch den folgenden Code. Damit werden die Ansicht zur Anzeige vorhandener Aufgaben sowie ein Formular für das Hinzufügen neuer Aufgaben und das Markieren von vorhandenen Aufgaben als abgeschlossen definiert.

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

Die Datei **layout.jade** im Verzeichnis **views** dient als globale Vorlage für andere **.jade**-Dateien. In diesem Schritt werden Sie sie modifizieren, um [Twitter Bootstrap][Twitter Bootstrap] zu verwenden, ein Toolkit zum mühelosen Gestalten ansprechender Websites.

1.  Laden Sie die Dateien für [Twitter Bootstrap][1] herunter, und extrahieren Sie sie. Kopieren Sie die Datei **bootstrap.min.css** vom **bootstrap\\css**-Ordner in das Verzeichnis **public\\stylesheets** Ihrer Aufgabenlistenanwendung.

2.  Öffnen Sie im Ordner **views** die Datei **layout.jade** in einem Texteditor, und ersetzen Sie den Inhalt durch Folgendes:

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

1.  Erstellen Sie im Verzeichnis **tasklist** eine neue Datei mit dem Namen **config.json**, und öffnen Sie diese in einem Texteditor.

2.  Der Inhalt der Datei **config.json** sollte etwa wie folgt aussehen:

        {
          "SQL_CONN" : "connection_string"
        }

    Ersetzen Sie **connection\_string** durch den zuvor erhaltenen ODBC-Verbindungszeichenfolgewert.

3.  Speichern Sie die Datei.

## Lokales Ausführen der Anwendung

Um die Anwendung lokal zu testen, führen Sie die folgenden Schritte durch:

1.  Wechseln Sie an der Befehlszeile zum Verzeichnis **tasklist**.

2.  Verwenden Sie den folgenden Befehl, um die Anwendung lokal zu starten:

        node app.js

3.  Öffnen Sie einen Webbrowser, und navigieren Sie zu <http://127.0.0.1:3000>. Eine Webseite ähnlich der folgenden sollte angezeigt werden:

    ![Eine Webseite mit einer leeren Aufgabenliste][Eine Webseite mit einer leeren Aufgabenliste]

4.  Füllen Sie die Felder für **Elementname** und **Elementkategorie** aus, und klicken Sie dann auf **Element hinzufügen**.

5.  Die Seite sollte nun das Element in der Aufgabenliste anzeigen.

    ![Ein Bild des neuen Elements in der Aufgabenliste][Ein Bild des neuen Elements in der Aufgabenliste]

6.  Zum Abschließen einer Aufgabe aktivieren Sie einfach das Kontrollkästchen in der entsprechenden Spalte, und klicken Sie dann auf **Aufgaben aktualisieren**.

7.  Zum Stoppen des Node-Prozesses wechseln Sie in die Befehlszeile, und drücken Sie gleichzeitig die Tasten **STRG** und **C**.

## Bereitstellen der Anwendung in Azure

In diesem Abschnitt führen Sie die Bereitstellungsschritte durch, die Sie nach dem Erstellen der Website erhalten haben, um die Anwendung für Azure zu veröffentlichen.

### Veröffentlichen der Anwendung

1.  Wechseln Sie das Verzeichnis von der Befehlszeile aus zu **tasklist**, falls dieses nicht schon gewählt ist.

2.  Verwenden Sie die folgenden Befehle, um ein lokales Git-Repository für Ihre Anwendung zu initialisieren, fügen Sie diesem die Anwendungsdateien hinzu, und pushen Sie die Dateien dann zu Azure

        git init
        git add .
        git commit -m "adding files"
        git remote add azure [URL for remote repository]
        git push azure master

    Am Ende der Bereitstellung sollte eine Anweisung ähnlich der folgenden angezeigt werden:

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

3.  Nachdem der Pushvorgang abgeschlossen ist, browsen Sie zu **http://[Websitename].azurewebsites.net/**, um Ihre Anwendung anzuzeigen.

### Wechseln zu einer Umgebungsvariablen

Zuvor haben wir einen Code implementiert, der nach einer Umgebungsvariablen **SQL\_CONN** für die Verbindungszeichenfolge sucht oder den Wert aus der Datei **config.json** lädt. In den folgenden Schritten erstellen Sie das Schlüssel/Wert-Paar in Ihrer Websitekonfiguration für echten Zugriff der Anwendung über eine Umgebungsvariable.

1.  Klicken Sie im Azure-Verwaltungsportal auf **WEBSITES**, und wählen Sie dann Ihre Website aus.

    ![Öffnen des Dashboards der Website][Öffnen des Dashboards der Website]

2.  Klicken Sie auf **KONFIGURIEREN**, und suchen Sie den Abschnitt **App-Einstellungen** der Seite.

    ![Konfigurationslink][Konfigurationslink]

3.  Im Abschnitt **App-Einstellungen** geben Sie **SQL\_CONN** im Feld **SCHLÜSSEL** ein, und die ODBC-Verbindungszeichenfolge im Feld **WERT**. Klicken Sie schließlich auf das Häkchen.

    ![App-Einstellungen][App-Einstellungen]

4.  Zum Abschluss klicken Sie auf das Symbol **SPEICHERN** unten auf der Seite, um diese Änderung in die Laufzeitumgebung zu übernehmen.

    ![Speichern im Abschnitt 'App-Einstellungen'][Speichern im Abschnitt 'App-Einstellungen']

5.  Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**, und entfernen Sie die Datei **config.json** mit dem folgenden Befehl:

        git rm config.json
        git commit -m "Removing config file"

6.  Führen Sie den folgenden Befehl aus, um die Änderungen in Azure bereitzustellen:

        git push azure master

Nachdem die Änderungen in Azure bereitgestellt wurden, sollte Ihre Webanwendung weiterhin funktionieren, da sie nun die Verbindungszeichenfolge aus dem Eintrag in **App-Einstellungen** liest. Um dies zu bestätigen, ändern Sie den Wert für den Eintrag **SQL\_CONN** unter **App-Einstellungen** in einen ungültigen Wert. Nachdem Sie diesen Wert gespeichert haben, sollte die Website wegen der ungültigen Verbindungszeichenfolge nicht mehr funktionieren.

## Nächste Schritte

-   [Node.js-Webanwendung mit MongoDB][Node.js-Webanwendung mit MongoDB]

-   [Node.js-Webanwendung mit Tabellenspeicher]

## Zusätzliche Ressourcen

[Azure-Befehlszeilentool für Mac und Linux] [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: /de-de/develop/nodejs/tutorials/create-a-website-(mac)/
[Veröffentlichen auf Azure-Websites mit Git][Veröffentlichen auf Azure-Websites mit Git]: /de-de/develop/nodejs/common-tasks/publishing-with-git/
[Azure Developer Center]: /de-de/develop/nodejs/
[Node.js-Webanwendung mit Tabellenspeicher]: /de-de/develop/nodejs/tutorials/web-site-with-storage/

  [Node]: http://nodejs.org
  [Git]: http://git-scm.com
  [Webseite mit einer leeren Aufgabenliste]: ./media/sql-database-nodejs-use-web-site/sql_todo_final.png
  [Microsoft SQL Server 2012 Feature Pack]: http://www.microsoft.com/de-de/download/details.aspx?id=29065
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Erstellen einer neuen Azure-Website]: ./media/sql-database-nodejs-use-web-site/new_website.jpg
  [Benutzerdefiniertes Erstellen einer neuen Website]: ./media/sql-database-nodejs-use-web-site/custom_create.png
  [Angeben der Website-Details]: ./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg
  [SQL-Datenbankeinstellungen eingeben]: ./media/sql-database-nodejs-use-web-site/database_settings.jpg
  [Einen neuen SQL-Datenbankserver erstellen]: ./media/sql-database-nodejs-use-web-site/create_server.jpg
  [Öffnen des Dashboards der Website]: ./media/sql-database-nodejs-use-web-site/go_to_dashboard.png
  [Git-Veröffentlichung einrichten]: ./media/sql-database-nodejs-use-web-site/setup_git_publishing.png
  [Anmeldedaten für die Veröffentlichung erstellen]: ./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png
  [Git-Anweisungen]: ./media/sql-database-nodejs-use-web-site/git-instructions.png
  [Verknüpfte Ressourcen]: ./media/sql-database-nodejs-use-web-site/linked_resources.jpg
  [Verbindungszeichenfolge]: ./media/sql-database-nodejs-use-web-site/connection_string.jpg
  [Schaltfläche VERWALTEN]: ./media/sql-database-nodejs-use-web-site/sql-manage.png
  [Anmeldung zur Datenbankverwaltung]: ./media/sql-database-nodejs-use-web-site/sqlazurelogin.png
  [Neue Tabelle]: ./media/sql-database-nodejs-use-web-site/new-table.png
  [Tabellenname festgelegt auf "tasks" und "Ist 'Identität'" aktiviert]: ./media/sql-database-nodejs-use-web-site/table-name-identity.png
  [abgeschlossener Tabellenentwurf]: ./media/sql-database-nodejs-use-web-site/table-columns.png
  [Express]: http://expressjs.com
  [Node-sqlserver]: https://github.com/WindowsAzure/node-sqlserver
  [Download Center]: http://www.microsoft.com/de-de/download/details.aspx?id=29995
  [Twitter Bootstrap]: https://github.com/twbs/bootstrap
  [1]: http://getbootstrap.com/
  [Eine Webseite mit einer leeren Aufgabenliste]: ./media/sql-database-nodejs-use-web-site/sql_todo_empty.png
  [Ein Bild des neuen Elements in der Aufgabenliste]: ./media/sql-database-nodejs-use-web-site/sql_todo_list.png
  [Konfigurationslink]: ./media/sql-database-nodejs-use-web-site/sql-task-configure.png
  [App-Einstellungen]: ./media/sql-database-nodejs-use-web-site/appsettings.png
  [Speichern im Abschnitt 'App-Einstellungen']: ./media/sql-database-nodejs-use-web-site/savebutton.png
  [Node.js-Webanwendung mit MongoDB]: ../store-mongolab-web-sites-nodejs-store-data-mongodb/
  [Veröffentlichen auf Azure-Websites mit Git]: ../CommonTasks/publishing-with-git
