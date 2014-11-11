<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on MongoLab - Azure" metaKeywords="" description="Learn how to create a Node.js Azure Website that connects to a MongoDB instance hosted on MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="eric@mongolab.com" />

# Erstellen einer "Node.js"-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-Ons

*Von Eric Sedor, MongoLab*

Grüße, Abenteurer! Willkommen bei MongoDB-as-a-Service. In diesem Lernprogramm führen Sie folgende Schritte aus:

1.  [Bereitstellen der Datenbank][Bereitstellen der Datenbank]: Das The Azure Store-Add-On [MongoLab][MongoLab] stellt Ihnen eine MongoDB-Datenbank bereit, die in der Azure-Cloud gehostet und durch die Clouddatenbankplattform von MongoLab verwaltet wird.
2.  [Erstellen der App][Erstellen der App]: Hierbei handelt es sich um eine einfache Node.js-App für die Verwaltung einer Aufgabenliste.
3.  [Bereitstellen der App][Bereitstellen der App]: Durch das Verknüpfen einiger Konfigurationshooks wird das Pushen unseres Codes sehr einfach.
4.  [Verwalten der Datenbank][Verwalten der Datenbank]: Schließlich zeigen wir Ihnen das webbasierte Datenbankverwaltungsportal von MongoLab, in dem Sie Daten problemlos suchen, visualisieren und ändern können.

Falls Sie Fragen haben, können Sie in diesem Lernprogramm jederzeit eine E-Mail an [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] senden.

Bevor Sie fortfahren, sollten Sie sicherstellen, dass Sie Folgendes installiert haben:

-   [Node.js][Node.js] Version 0.8.14+

-   [Git][Git]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Schnellstart

Wenn Sie den Azure Store bereits kennengelernt haben, finden Sie in diesem Abschnitt einen Schnellstart. Fahren Sie andernfalls mit [Bereitstellen der Datenbank][Bereitstellen der Datenbank] weiter unten fort.

1.  Öffnen Sie den Azure Store.
    ![Store][Store]
2.  Klicken Sie auf das MongoLab-Add-On.
    ![MongoLab][1]
3.  Klicken Sie in der Liste der Add-Ons auf das MongoLab-Add-On und dann auf **Verbindungsinformationen**.
    ![ConnectionInfoButton][ConnectionInfoButton]
4.  Kopieren Sie MONGOLAB\_URI in die Zwischenablage.
    ![ConnectionInfoScreen][ConnectionInfoScreen]
    **Dieser URI enthält Ihren Benutzernamen und Ihr Kennwort für die Datenbank. Behandeln Sie diese Angaben als kritische Informationen, und teilen Sie sie niemandem mit.**
5.  Fügen Sie den Wert der Liste mit den Verbindungszeichenfolgen im Konfigurationsmenü der Azure-Webanwendung hinzu:
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  Geben Sie unter **Name** MONGOLAB\_URI ein.
7.  Fügen Sie unter **Wert** die Verbindungszeichenfolge ein, die wir im vorhergehenden Abschnitt erzeugt haben.
8.  Wählen Sie im Dropdownfeld für den Typ die Option **Benutzerdefiniert** aus (anstelle des Standards **SQLAzure**).
9.  Führen Sie `npm install mongoose` aus, um Mongoose, einen MongoDB-Node-Treiber, abzurufen.
10. Richten Sie im Code einen Hook ein, um die MongoLab-Verbindungs-URI aus der Umgebungsvariablen abzurufen und eine Verbindung herzustellen:

        var mongoose = require('mongoose');  
        ...
        var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
        ...
        mongoose.connect(connectionString);

Hinweis: Azure fügt den Präfix **CUSTOMCONNSTR\_** zur ursprünglich deklarierten Verbindungszeichenfolge hinzu. Darum verweist der Code auf **CUSTOMCONNSTR\_MONGOLAB\_URI.**, nicht auf **MONGOLAB\_URI**.

Nun folgt das vollständige Lernprogramm ...

## <a name="provision"></a>Bereitstellen der Datenbank

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

## <a name="create"></a>Erstellen der App

In diesem Abschnitt richten Sie Ihre Entwicklungsumgebung ein und erstellen den Code für eine grundlegende Aufgabenlisten-Webanwendung mithilfe von Node.js, Express und MongoDB. [Express][Express] stellt ein Controlleranzeigeframework für den Knoten bereit, während [Mongoose][Mongoose] ein Treiber für die Kommunikation mit MongoDB in Node ist.

### Einrichtung

#### Generieren des Gerüsts und Installieren der Module

1.  Erstellen Sie an der Befehlszeile das Verzeichnis **tasklist** und navigieren Sie zu diesem Verzeichnis. Dies ist Ihr Projektverzeichnis.
2.  Geben Sie den folgenden Befehl für die Expressinstallation ein.

        npm install express -g

    `-g` gibt den globalen Modus an, der verwendet wird, damit das **express**-Modul ohne Angabe eines Verzeichnispfads verfügbar ist. Falls **Error: EPERM, chmod '/usr/local/bin/express'** angezeigt wird, verwenden Sie **sudo**, um npm auf einer höheren Berechtigungsstufe auszuführen.

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

        express@3.3.4 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
        ├── methods@0.0.1
        ├── fresh@0.1.0
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── debug@0.7.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── send@0.1.3 (mime@1.2.9)
        └── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

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

4.  Geben Sie Folgendes ein, um die in der Datei **package.json** beschriebenen Module zu installieren:

        npm install

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

        express@3.3.4 node_modules\express
        ├── methods@0.0.1
        ├── fresh@0.1.0
        ├── range-parser@0.0.4
        ├── cookie-signature@1.0.1
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── debug@0.7.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── send@0.1.3 (mime@1.2.9)
        └── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

        jade@0.33.0 node_modules\jade
        ├── character-parser@1.0.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── with@1.1.0 (uglify-js@2.3.6)
        ├── constantinople@1.0.1 (uglify-js@2.3.6)
        ├── transformers@2.0.1 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
        └── monocle@0.1.48 (readdirp@0.2.5)

    Die Datei **package.json** ist eine der durch den Befehl **express** erstellten Dateien. Diese Datei enthält eine Liste zusätzlicher Module, die für eine Express-Anwendung erforderlich sind. Wenn Sie später diese Anwendung auf einer Azure-Website bereitstellen, wird anhand dieser Datei bestimmt, welche Module auf Azure installiert werden müssen, um Ihre Anwendung zu unterstützen.

5.  Geben Sie anschließend den folgenden Befehl ein, um das Mongoose-Modul lokal zu installieren und einen Eintrag dafür in der Datei **package.json** zu speichern:

        npm install mongoose --save

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

        mongoose@3.6.15 node_modules\mongoose
        ├── regexp-clone@0.0.1
        ├── sliced@0.0.3
        ├── muri@0.3.1
        ├── hooks@0.2.1
        ├── mpath@0.1.1
        ├── ms@0.1.0
        ├── mpromise@0.2.1 (sliced@0.0.4)
        └── mongodb@1.3.11 (bson@0.1.9, kerberos@0.0.3)

    Sie können alle Meldungen zur Installation des C++-bson-Parsers ignorieren.

### Der Code

Da Ihre Umgebung und das Gerüst jetzt fertig sind, erweitern wir die grundlegende Anwendung, die durch den Befehl **express** erstellt wurde, durch Hinzufügen einer Datei **task.js**, die das Modell für Ihre Aufgaben enthält. Außerdem ändern Sie die vorhandene Datei **app.js** und erstellen eine neue Controllerdatei **tasklist.js**, um das Modell zu verwenden.

#### Erstellen des Modells

1.  Erstellen Sie im Verzeichnis **tasklist** ein neues Verzeichnis namens **models**.

2.  Erstellen Sie im Verzeichnis **models** eine neue Datei namens **task.js**. Diese Datei enthält das Modell für die von Ihrer Anwendung erstellten Aufgaben.

3.  Fügen Sie der Datei **task.js** den folgenden Code hinzu:

        var mongoose = require('mongoose')
          , Schema = mongoose.Schema;

        var TaskSchema = new Schema({
            itemName      : String
          , itemCategory  : String
          , itemCompleted : { type: Boolean, default: false }
          , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

4.  Speichern und schließen Sie die Datei **task.js**.

#### Erstellen des Controllers

1.  Erstellen Sie im Verzeichnis **tasklist/routes** eine neue Datei namens **tasklist.js**, und öffnen Sie sie in einem Texteditor.

2.  Fügen Sie **tasklist.js** den folgenden Code hinzu. Dadurch werden das Mongoose-Modul und das in **task.js** definierte Aufgabenmodell geladen. Die TaskList-Funktion wird verwendet, um die Verbindung zum MongoDB-Server basierend auf dem Wert **connection** herzustellen. Außerdem werden die Methoden **showTasks**, **addTask** und **completeTasks** bereitgestellt:

        var mongoose = require('mongoose')
          , task = require('../models/task.js');

        module.exports = TaskList;

        function TaskList(connection) {
          mongoose.connect(connection);
        }

        TaskList.prototype = {
          showTasks: function(req, res) {
            task.find({itemCompleted: false}, function foundTasks(err, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items})
            });
          },

          addTask: function(req,res) {
            var item = req.body.item;
            newTask = new task();
            newTask.itemName = item.name;
            newTask.itemCategory = item.category;
            newTask.save(function savedTask(err){
              if(err) {
                throw err;
              }
            });
            res.redirect('/');
          },


          completeTask: function(req,res) {
            var completedTasks = req.body;
            for(taskId in completedTasks) {
              if(completedTasks[taskId]=='true') {
                var conditions = { _id: taskId };
                var updates = { itemCompleted: completedTasks[taskId] };
                task.update(conditions, updates, function updatedTask(err) {
                  if(err) {
                    throw err;
                  }
                });
              }
            }
            res.redirect('/');
          }
        }

3.  Speichern Sie die Datei **tasklist.js**.

#### Ändern der Indexansicht

1.  Wechseln Sie zum Verzeichnis **views**, und öffnen Sie die Datei **index.jade** in einem Texteditor.

2.  Ersetzen Sie den Inhalt der Datei **index.jade** durch den folgenden Code. Damit werden die Ansicht zur Anzeige vorhandener Aufgaben sowie ein Formular für das Hinzufügen neuer Aufgaben und das Markieren von vorhandenen Aufgaben als abgeschlossen definiert.

        h1 #{title}
        form(action="/completetask", method="post")
          table(border="1")
            tr
              td Name
              td Category
              td Date
              td Complete
            each task in tasks
              tr
                td #{task.itemName}
                td #{task.itemCategory}
                - var day   = task.itemDate.getDate();
                - var month = task.itemDate.getMonth() + 1;
                - var year  = task.itemDate.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
          input(type="submit", value="Update tasks")
        hr
        form(action="/addtask", method="post")
          table(border="1") 
            tr
              td Item Name: 
              td 
                input(name="item[name]", type="textbox")
            tr
              td Item Category: 
              td 
                input(name="item[category]", type="textbox")
          input(type="submit", value="Add item")

3.  Speichern und schließen Sie die Datei **index.jade**.

#### Ersetzen von "app.js"

1.  Öffnen Sie im Verzeichnis **tasklist** die Datei **app.js** in einem Texteditor. Diese Datei wurde zuvor mit dem Befehl **express** erstellt.
2.  Fügen Sie den folgenden Code am Anfang der Datei **app.js** hinzu. Dadurch wird **TaskList** mit der Verbindungszeichenfolge für den MongoDB-Server initialisiert:

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

    Beachten Sie die zweite Zeile: Sie rufen eine Umgebungsvariable auf, die Sie später konfigurieren werden und die die Verbindungsinformationen für die Mongo-Instanz enthält. Falls Sie eine lokale Mongo-Instanz zu Entwicklungszwecken ausführen, können Sie diesen Wert temporär auf "localhost" statt auf `process.env.CUSTOMCONNSTR_MONGOLAB_URI` festlegen.

3.  Suchen Sie die Zeilen, die mit `app.get` beginnen, und ersetzen Sie sie durch die folgenden Zeilen:

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    Dadurch werden die in **tasklist.js** definierten Funktionen als Routen hinzugefügt.

4.  Speichern Sie die Datei **app.js**.

## <a name="deploy"></a>Bereitstellen der App

Da die Anwendung nun entwickelt wurde, sollten Sie eine Azure-Website zum Hosten der Anwendung erstellen und konfigurieren und den Code bereitstellen. In diesem Abschnitt ist die Verwendung der MongoDB-Verbindungszeichenfolge (URI) ein zentraler Aspekt. Sie konfigurieren auf Ihrer Website eine Umgebungsvariable mit diesem URI, um den URI vom Code zu trennen. Sie sollten die URI als vertrauliche Information behandeln, da sie Anmeldeinformationen für die Verbindung zur Datenbank enthält.

Die Schritte in diesem Abschnitt verwenden die Azure-Befehlszeilentools zum Erstellen einer neuen Azure-Website und stellen die Anwendung dann mit Git bereit. Zur Durchführung dieser Schritte benötigen Sie ein Azure-Abonnement.

### Installieren des Azure-Befehlszeilentools für Mac und Linux

Verwenden Sie den folgenden Befehl, um die Befehlszeilentools zu installieren:

    npm install azure-cli -g

Wenn Sie bereits das **Azure-SDK für Node.js** aus dem [Azure-Entwicklercenter][Azure-Entwicklercenter] installiert haben, sollten auch die Befehlszeilentools bereits installiert sein. Weitere Informationen finden Sie im Thema zum [Azure-Befehlszeilentool für Mac und Linux][Azure-Befehlszeilentool für Mac und Linux].

Obwohl die Azure-Befehlszeilentools vor allem für Mac-und Linux-Benutzer erstellt wurden, basieren sie auf "Node.js" und sollten auf allen Systemen funktionieren, die Node ausführen können.

### Importieren von Veröffentlichungseinstellungen

Bevor Sie die Befehlszeilentools mit Azure verwenden können, müssen Sie zunächst eine Datei mit Informationen zu Ihrem Abonnement herunterladen. Führen Sie die folgenden Schritte aus, um diese Datei herunterzuladen und zu importieren.

1.  Geben Sie an der Befehlszeile den folgenden Befehl ein, um den Browser zu starten, und navigieren Sie zur Downloadseite. Melden Sie sich bei Aufforderung mit dem Konto an, dass Ihrem Abonnement zugeordnet ist.

        azure account download

    ![Die Downloadseite][Die Downloadseite]

    Der Dateidownload sollte automatisch beginnen. Klicken Sie andernfalls auf den Link oben auf der Seite, um die Datei manuell herunterzuladen.

2.  Nach Abschluss des Dateidownloads verwenden Sie den folgenden Befehl, um die Einstellungen zu importieren:

        azure account import <path-to-file>

    Geben Sie den Pfad und den Dateinamen der Datei mit den Veröffentlichungseinstellungen an, die Sie im vorherigen Schritt heruntergeladen haben. Nach Abschluss des Befehls sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

        info:   Executing command account import
        info:   Found subscription: subscriptionname
        info:   Setting default subscription to: subscriptionname
        warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
        warn:   Remember to delete it now that it has been imported.
        info:   Account publish settings imported successfully
        info:   account import command OK

3.  Nach Abschluss des Imports sollten Sie die Datei mit den Veröffentlichungseinstellungen löschen, da sie nicht mehr benötigt wird und vertrauliche Informationen zu Ihrem Azure-Abonnement enthält.

### Erstellen einer neuen Website und Übertragen des Codes mithilfe von Push

Das Erstellen einer Website in Azure ist sehr einfach. Wenn dies Ihre erste Azure-Website ist, müssen Sie das Portal verwenden. Wenn Sie bereits mindestens eine Website erstellt haben, fahren Sie mit Schritt 7 fort.

1.  Klicken Sie im Azure-Portal auf **Neu**.
    ![New][New]
2.  Wählen Sie **Berechnen \> Website \> Schnellerfassung** aus.
    ![CreateSite][CreateSite]
3.  Geben Sie einen URL-Präfix ein. Wählen Sie einen Namen aus, aber denken Sie daran, dass er eindeutig sein muss ("mymongoapp" ist wahrscheinlich nicht verfügbar).
4.  Klicken Sie auf **Website erstellen**.
5.  Klicken Sie nach Abschluss der Websiteerstellung auf den Namen der Website in der Websiteliste. Das Website-Dashboard wird angezeigt.
    ![WebSiteDashboard][WebSiteDashboard]
6.  Klicken Sie auf **Git-Veröffentlichung einrichten** unter **Schnellansicht**. Geben Sie dann den gewünschten Benutzernamen und das gewünschte Kennwort für Git ein. Sie verwenden dieses Kennwort für Pushvorgänge auf Ihre Website (in Schritt 9).
7.  Wenn Sie Ihre Website mit den oben genannten Schritten erstellt haben, wird der Prozess durch den folgenden Befehl abgeschlossen. Wenn Sie allerdings bereits über mehr als eine Azure-Website verfügen, können Sie die oben genannten Schritte überspringen und eine neue Website mithilfe desselben Befehls erstellen. Im Projektverzeichnis **tasklist**:

        azure site create myuniquesitename --git  

    Ersetzen Sie "myuniquesitename" durch den eindeutigen Sitenamen für Ihre Website. Wenn die Website als Teil dieses Befehls erstellt wird, werden Sie nach dem Rechenzentrum gefragt, in dem sich die Website befinden wird. Wählen Sie das Rechenzentrum aus, das sich geographisch in der Nähe Ihrer MongoLab-Datenbank befindet.

    Durch den `--git`-Parameter wird Folgendes erstellt:
    A. Ein lokales Git-Repository im Ordner **tasklist**, falls keins vorhanden ist.
    A. Ein [Git-Remoterepository][Git-Remoterepository] namens "azure", das verwendet wird, um die Anwendung in Azure zu veröffentlichen.
    A. Eine [iisnode.yml][iisnode.yml]-Datei, die Einstellungen enthält, die von Azure verwendet werden, um Node-Anwendungen zu hosten.
    A. Eine .gitignore-Datei, um zu verhindern, dass der Ordner mit den Node-Modulen auf .git veröffentlicht wird.

    Nach Abschluss dieses Befehls wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass die Zeile, die mit **Created website at** beginnt, die URL für die Website enthält.

        info:   Executing command site create
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default web.config file
        info:   Creating a new web site
        info:   Created web site at  mongodbtasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
        info:   site create command OK

8.  Verwenden Sie die folgenden Befehle, um Dateien zu Ihrem lokalen Git-Repository hinzuzufügen und dann zu übernehmen:

        git add .
        git commit -m "adding files"

9.  Pushvorgang für den Code:

        git push azure master  

    Wenn Sie die letzten Änderungen am Git-Repository per Push an die Azure-Website übertragen, müssen Sie angeben, dass die Zielstruktur **master** lautet, da dies für den Websiteinhalt verwendet wird. Wenn Sie nach einem Kennwort gefragt werden, geben Sie das Kennwort ein, das Sie beim Einrichten der Git-Veröffentlichung für Ihre Website oben erstellt haben.

    Eine Ausgabe ähnlich der folgenden wird angezeigt. Während der Bereitstellung lädt Azure alle npm-Module herunter.

        Counting objects: 17, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (13/13), done.
        Writing objects: 100% (17/17), 3.21 KiB, done.
        Total 17 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id 'ef276f3042'.
        remote: Preparing files for deployment.
        remote: Running NPM.
        ...
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
         * [new branch]      master -> master

Sie sind fast fertig!

### Konfigurieren der Umgebung

Erinnern Sie sich an "process.env.CUSTOMCONNSTR\_MONGOLAB\_URI" im Code? Wir möchten diese Umgebungsvariable mit dem Wert auffüllen, der Azure während der MongoLab-Datenbankbereitstellung bereitgestellt wird.

#### Abrufen der MongoLab-Verbindungszeichenfolge

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Hinzufügen der Verbindungszeichenfolge zu den Umgebungsvariablen der Website

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## Erfolg!

Führen Sie `azure site browse` in Ihrem Projektverzeichnis aus, um automatisch einen Browser zu öffnen, oder öffnen Sie einen Browser und navigieren Sie manuell zur URL Ihrer Website ("myuniquesite.azurewebsites.net"):

![Eine Webseite mit einer leeren Aufgabenliste][Eine Webseite mit einer leeren Aufgabenliste]

## <a name="manage"></a>Verwalten der Datenbank

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Glückwunsch! Sie haben gerade eine "Node.js"-Anwendung mit einer von MongoLab gehosteten MongoDB-Datenbank gestartet! Das Sie jetzt über eine MongoLab-Datenbank verfügen, können Sie sich mit allen Fragen oder Bedenken bezüglich Ihrer Datenbank sowie mit Hilfeanfragen zu MongoDB oder dem Node-Treiber selbst an [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] wenden. Viel Glück!

  [Bereitstellen der Datenbank]: #provision
  [MongoLab]: http://mongolab.com
  [Erstellen der App]: #create
  [Bereitstellen der App]: #deploy
  [Verwalten der Datenbank]: #manage
  [Node.js]: http://nodejs.org
  [Git]: http://git-scm.com
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
  [1]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
  [ConnectionInfoButton]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
  [howto-provision-mongolab]: ../includes/howto-provision-mongolab.md
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Azure-Entwicklercenter]: /de-de/develop/nodejs/
  [Azure-Befehlszeilentool für Mac und Linux]: /de-de/develop/nodejs/how-to-guides/command-line-tools/
  [Die Downloadseite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
  [New]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
  [CreateSite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
  [Git-Remoterepository]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [howto-get-connectioninfo-mongolab]: ../includes/howto-get-connectioninfo-mongolab.md
  [howto-save-connectioninfo-mongolab]: ../includes/howto-save-connectioninfo-mongolab.md
  [Eine Webseite mit einer leeren Aufgabenliste]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
  [howto-access-mongolab-ui]: ../includes/howto-access-mongolab-ui.md
