<properties linkid="dev-nodejs-tutorials-web-site-with-storage" urlDisplayName="Web Site with Storage" pageTitle="Node.js web site with table storage | Microsoft Azure" metaKeywords="Azure table storage Node.js, Azure Node.js application, Azure Node.js tutorial, Azure Node.js example" description="A tutorial that teaches you how to use the Azure Table service to store data from a Node application hosted on an Azure web site." metaCanonical="" services="web-sites,storage" documentationCenter="Node.js" title="Node.js Web Application using the Azure Table Service" authors="" solutions="" manager="" editor="" />

Node.js-Webanwendung mit Azure-Tabellendienst
=============================================

In diesem Lernprogramm erfahren Sie, wie Sie den von der Azure-Datenverwaltung bereitgestellten Tabellendienst einsetzen können, um Daten von einer auf Azure gehosteten [Node](http://nodejs.org)-Anwendung zu speichern und abzurufen. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie bereits eine gewisse Erfahrung mit der Verwendung von Node und [Git](http://git-scm.com) haben.

Sie erhalten Informationen zu folgenden Themen:

-   Installieren von Node-Modulen über NPM (Node Package Manager = Paketverwaltung von Node.js)

-   Arbeiten mit dem Azure-Tabellendienst

-   Erstellen einer Azure-Website mit dem Azure-Befehlszeilentool für Mac und Linux

Im Verlauf dieses Lernprogramms erstellen Sie eine einfache webbasierte Aufgabenverwaltungsanwendung, die das Erstellen, Abrufen und Durchführen von Aufgaben ermöglicht. Diese Aufgaben sind im Tabellendienst gespeichert.

Die Projektdateien für dieses Lernprogramm werden in einem Verzeichnis namens **tasklist** gespeichert, und die fertige Anwendung wird etwa wie folgt aussehen:

![Eine Webseite mit einer leeren Aufgabenliste](./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png)

**Hinweis**

In diesem Lernprogramm wird auf den Ordner **tasklist** Bezug genommen. Der volle Pfad zu diesem Ordner wird nicht angegeben, da die Pfadsyntax je nach Betriebssystem variiert. Es empfiehlt sich, diesen Ordner an einer im lokalen Dateisystem leicht zugänglichen Stelle zu erstellen, wie zum Beispiel **\~/node/tasklist** oder **c:\\node\\tasklist**

**Hinweis**

Im Folgenden wird öfters die Verwendung der Befehlszeile verlangt. In diesen Fällen verwenden Sie die Befehlszeile für Ihr Betriebssystem, wie zum Beispiel **cmd.exe** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die Terminal-Anwendung zugreifen.

Voraussetzungen
---------------

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes installiert ist:

-   [node](http://nodejs.org), Version 0.6.14 oder höher

-   [Git](http://git-scm.com)

-   Ein Texteditor

-   Ein Webbrowser

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Erstellen eines Speicherkontos
------------------------------

Führen Sie die folgenden Schritte aus, um ein Speicherkonto zu erstellen. Dieses Konto wird in den weiteren Anweisungen dieses Lernprogramms verwendet.

1.  Öffnen Sie den Webbrowser, und navigieren Sie zum [Azure-Portal](http://windowsazure.com). Melden Sie sich bei Aufforderung mit Ihren Azure-Abonnementinformationen an.

2.  Klicken Sie unten im Portal auf **+ NEU**, und wählen Sie **Speicherkonto** aus.

    ![+Neu](./media/storage-nodejs-use-table-storage-web-site/plus-new.png)

    ![Speicherkonto](./media/storage-nodejs-use-table-storage-web-site/new-storage.png)

3.  Wählen Sie **Schnellerfassung**, und geben Sie die URL und die Region/Affinität für dieses Speicherkonto ein. Da es sich hierbei um ein Lernprogamm handelt, das keine globale Replikation erfordert, deaktivieren Sie die Option **Geografische Replikation aktivieren**. Klicken Sie abschließend auf "Speicherkonto erstellen".

    ![Schnellerfassung](./media/storage-nodejs-use-table-storage-web-site/quick-storage.png)

    Notieren Sie sich die eingegebene URL, da sie in späteren Schritten als Kontoname verwendet wird.

4.  Nach der Erstellung des Speicherkontos klicken Sie unten auf der Seite auf **Schlüssel verwalten**. Daraufhin werden der primäre und sekundäre Zugriffsschlüssel für dieses Speicherkonto angezeigt. Kopieren und speichern Sie den primären Zugriffsschlüssel, und klicken Sie dann auf das Häkchen.

    ![Zugriffsschlüssel](./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png)

Installieren der Module und Erstellen des Gerüsts
-------------------------------------------------

In diesem Abschnitt erstellen Sie eine neue Node-Anwendung und fügen Modulpakete mithilfe von npm hinzu. Für die tasklist-Anwendung verwenden Sie die Module [Express](http://expressjs.com) und [Azure](https://github.com/WindowsAzure/azure-sdk-for-node). Das Express-Modul stellt ein Modellansichts-Controller-Framework für Node bereit, das Azure-Modul Konnektivität zum Tabellendienst.

### Installieren von Express und Erstellen des Gerüsts

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**. Wenn das Verzeichnis **tasklist** noch nicht besteht, erstellen Sie es.

2.  Geben Sie den folgenden Befehl ein, um Express zu installieren.

         npm install express -g

    **Hinweis**

    Bei Verwendung des Parameters "-g" wird in einigen Betriebssystemen eventuell ein Fehler ähnlich dem folgenden angezeigt: **Error: EPERM, chmod '/usr/local/bin/express'**, verbunden mit der Aufforderung, das Konto als Administrator auszuführen. In diesem Fall verwenden Sie den Befehl **sudo**, um npm auf einer höheren Berechtigungsstufe auszuführen.

    Die Ausgabe dieses Befehls sollte etwa wie folgt aussehen:

         express@3.4.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
         ├── methods@0.0.1
         ├── fresh@0.2.0
         ├── cookie-signature@1.0.1
         ├── range-parser@0.0.4
         ├── buffer-crc32@0.2.1
         ├── cookie@0.1.0
         ├── debug@0.7.2
         ├── mkdirp@0.3.5
         ├── commander@1.2.0 (keypress@0.1.0)
         ├── send@0.1.4 (mime@1.2.11)
         └── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, multiparty@2.1.8)

    **Hinweis**

    Wird der Parameter "-g" bei der Installation des Express-Moduls verwendet, so erfolgt die Installation global. Dies ist praktisch, weil der **express**-Befehl dann zur Website-Gerüsterstellung eingesetzt werden kann, ohne zusätzliche Pfadinformationen angeben zu müssen.

3.  Zum Erstellen des Gerüsts für diese Anwendung verwenden Sie den Befehl **express**:

         express

    Die Ausgabe dieses Befehls sollte etwa wie folgt aussehen:

         create : .
         create : ./package.json
         create : ./app.js
         create : ./public/javascripts
         create : ./public
         create : ./public/stylesheets
         create : ./public/stylesheets/style.css
         create : ./views
         create : ./views/layout.jade
         create : ./views/index.jade
         create : ./routes
         create : ./routes/index.js
         create : ./routes/user.js
         create : ./public/images

         install dependencies:
           $ cd . && npm install

         run the app:
           $ node app

Nach der Ausführung dieses Befehls sollte das Verzeichnis **tasklist** einige neue Verzeichnisse und Dateien enthalten.

### Installieren zusätzlicher Module

Die Datei **package.json** ist eine der durch den Befehl **express** erstellten Dateien. Diese Datei enthält eine Liste zusätzlicher Module, die für eine Express-Anwendung erforderlich sind. Wenn Sie später diese Anwendung auf einer Azure-Website bereitstellen, wird anhand dieser Datei bestimmt, welche Module auf Azure installiert werden müssen, um Ihre Anwendung zu unterstützen.

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**, und geben Sie Folgendes ein, um die in der Datei **package.json** beschriebenen Module zu installieren:

         npm install

    Die Ausgabe dieses Befehls sollte etwa wie folgt aussehen:

         express@3.4.0 node_modules\express
         ├── methods@0.0.1
         ├── range-parser@0.0.4
         ├── cookie-signature@1.0.1
         ├── fresh@0.2.0
         ├── buffer-crc32@0.2.1
         ├── cookie@0.1.0
         ├── debug@0.7.2
         ├── mkdirp@0.3.5
         ├── commander@1.2.0 (keypress@0.1.0)
         ├── send@0.1.4 (mime@1.2.11)
         └── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, bytes@0.2.0, qs@0.6.5, multiparty@2.1.8)

         jade@0.35.0 node_modules\jade
         ├── character-parser@1.2.0
         ├── commander@2.0.0
         ├── mkdirp@0.3.5
         ├── monocle@1.1.50 (readdirp@0.2.5)
         ├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
         ├── with@1.1.1 (uglify-js@2.4.0)
         └── constantinople@1.0.2 (uglify-js@2.4.0)

    Hierdurch werden alle von Express benötigten Standardmodule installiert.

2.  Geben Sie anschließend den folgenden Befehl ein, um die Module [azure](https://github.com/WindowsAzure/azure-sdk-for-node), [node-uuid], [nconf] und [async] lokal zu installieren und einen Eintrag für diese in der Datei **package.json** zu speichern:

         npm install azure node-uuid async nconf --save

    Die Ausgabe dieses Befehls sollte etwa wie folgt aussehen:

         async@0.2.9 node_modules\async

         node-uuid@1.4.1 node_modules\node-uuid

         nconf@0.6.7 node_modules\nconf
         ├── ini@1.1.0
         ├── async@0.1.22
         ├── pkginfo@0.2.3
         └── optimist@0.3.7 (wordwrap@0.0.2)

         azure@0.7.15 node_modules\azure
         ├── dateformat@1.0.2-1.2.3
         ├── xmlbuilder@0.4.2
         ├── envconf@0.0.4
         ├── node-uuid@1.2.0
         ├── mpns@2.0.1
         ├── underscore@1.5.2
         ├── mime@1.2.11
         ├── validator@1.5.1
         ├── tunnel@0.0.2
         ├── wns@0.5.3
         ├── xml2js@0.2.8 (sax@0.5.5)
         └── request@2.25.0 (json-stringify-safe@5.0.0, aws-sign@0.3.0, forever-agent@0.5.0, tunnel-agent@0.3.0, qs@0.6.5, oauth-sign@0.3.0, cookie-jar@0.3.0, node-uuid@1.4.1, http-signature@0.10.0, form-data@0.1.1, hawk@1.0.0)

Verwenden des Tabellendiensts in einer Node-Anwendung
-----------------------------------------------------

In diesem Abschnitt erweitern Sie die durch den Befehl **express** erstellte Grundanwendung, indem Sie die Datei **task.js** hinzufügen, die das Modell für Ihre Aufgaben enthält. Außerdem ändern Sie die vorhandene Datei **app.js** und erstellen eine neue Datei **tasklist.js**, die das Modell verwendet.

### Erstellen des Modells

1.  Erstellen Sie im Verzeichnis **tasklist** ein neues Verzeichnis namens **models**.

2.  Erstellen Sie im Verzeichnis **models** eine neue Datei namens **task.js**. Diese Datei enthält das Modell für die von Ihrer Anwendung erstellten Aufgaben.

3.  Fügen Sie am Anfang der Datei **task.js** den folgenden Code hinzu, um auf die erforderlichen Bibliotheken zu verweisen:

        var azure = require('azure');
		var uuid = require('node-uuid');

4.  Anschließend fügen Sie Code hinzu, um das Task-Objekt zu definieren und zu exportieren. Dies Objekt stellt die Verbindung zur Tabelle her.

module.exports = Task;

     function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

1.  Fügen Sie als Nächstes den folgenden Code hinzu, um zusätzliche Methoden für das Task-Objekt zu definieren, die Interaktionen mit in der Tabelle gespeicherten Daten ermöglichen:

         Task.prototype = {
           find: function(query, callback) {
             self = this;
             self.storageClient.queryEntities(query, function entitiesQueried(error, entities) {
               if(error) {
                 callback(error);
               } else {
                 callback(null, entities);
               }
             });
           },

           addItem: function(item, callback) {
             self = this;
             item.RowKey = uuid();
             item.PartitionKey = self.partitionKey;
             item.completed = false;
             self.storageClient.insertEntity(self.tableName, item, function entityInserted(error) {
               if(error){  
                 callback(error);
               }
               callback(null);
             });
           },

           updateItem: function(item, callback) {
             self = this;
             self.storageClient.queryEntity(self.tableName, self.partitionKey, item, function entityQueried(error, entity) {
               if(error) {
                 callback(error);
               }
               entity.completed = true;
               self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                 if(error) {
                   callback(error);
                 }
                 callback(null);
               });
             });
           }
         }

2.  Speichern und schließen Sie die Datei **task.js**.

### Erstellen des Controllers

1.  Erstellen Sie im Verzeichnis **tasklist/routes** eine neue Datei namens **tasklist.js**, und öffnen Sie sie in einem Texteditor.

2.  Fügen Sie den folgenden Code zu **tasklist.js** hinzu. Hierdurch werden die Module "azure" und "async" geladen, die von **tasklist.js** verwendet werden. Weiterhin wird hierdurch die Funktion **TaskList** definiert, an die eine Instanz des zuvor definierten **Task**-Objekts übergeben wird:

         var azure = require('azure');
         var async = require('async');

         module.exports = TaskList;

         function TaskList(task) {
           this.task = task;
         }

3.  Fügen Sie der Datei **tasklist.js** außerdem die Methoden **showTasks**, **addTask** und **completeTasks** hinzu:

         TaskList.prototype = {
           showTasks: function(req, res) {
             self = this;
             var query = azure.TableQuery
               .select()
               .from(self.task.tableName)
               .where('completed eq 
         ', false);
             self.task.find(query, function itemsFound(error, items) {
               res.render('index',{title: 'My ToDo List ', tasks: items});
             });
           },

           addTask: function(req,res) {
             var self = this      
             var item = req.body.item;
             self.task.addItem(item, function itemAdded(error) {
               if(error) {
                 throw error;
               }
               res.redirect('/');
             });
           },

           completeTask: function(req,res) {
             var self = this;
             var completedTasks = Object.keys(req.body);
             async.forEach(completedTasks, function taskIterator(completedTask, callback) {
               self.task.updateItem(completedTask, function itemsUpdated(error) {
                 if(error){
                   callback(error);
                 } else {
                   callback(null);
                 }
               });
             }, function goHome(error){
               if(error) {
                 throw error;
               } else {
                res.redirect('/');
               }
             });
           }
         }

4.  Speichern Sie die Datei **tasklist.js**.

### Modifizieren von app.js

1.  Öffnen Sie im Verzeichnis **tasklist** die Datei **app.js** in einem Texteditor. Diese Datei wurde zuvor durch Ausführung des Befehls **express** erstellt.

2.  Fügen Sie am Anfang der Datei den folgenden Code hinzu, um das azure-Modul zu laden und den Tabellennamen, den Partitionsschlüssel sowie die in diesem Beispiel verwendeten Speicheranmeldeinformationen hinzuzufügen:

         var azure = require('azure');
         var nconf = require('nconf');
         nconf.env()
              .file({ file: 'config.json'});
         var tableName = nconf.get("TABLE_NAME")
         var partitionKey = nconf.get("PARTITION_KEY")
         var accountName = nconf.get("STORAGE_NAME")
         var accountKey = nconf.get("STORAGE_KEY");

    **Hinweis**

    Nconf lädt die Konfigurationswerte entweder aus Umgebungsvariablen oder aus der Datei "config.json", die wir später erstellen.

3.  Blättern Sie in der Datei "app.js" nach unten, bis Sie die folgende Zeile sehen:

         app.get('/', routes.index);
         app.get('/users', user.list);

    Ersetzen Sie die vorstehenden Zeilen durch den nachfolgenden Code. Hierdurch wird eine Instanz von **Task** mit einer Verbindung zu Ihrem Speicherkonto initialisiert. Diese wird an die **TaskList** übergeben, die darüber mit dem Tabellendienst kommuniziert:

         var TaskList = require('./routes/tasklist');
         var Task = require('./models/task');
         var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
         var taskList = new TaskList(task);

         app.get('/', taskList.showTasks.bind(taskList));
         app.post('/addtask', taskList.addTask.bind(taskList));
         app.post('/completetask', taskList.completeTask.bind(taskList));

4.  Speichern Sie die Datei **app.js**.

### Modifizieren der Indexansicht

1.  Wechseln Sie das Verzeichnis zu **views**, und öffnen Sie die Datei **index.jade** in einem Texteditor.

2.  Ersetzen Sie den Inhalt der Datei **index.jade** durch den nachfolgenden Code. Hierdurch werden die Ansicht für die Anzeige vorhandener Aufgaben sowie ein Formular zum Hinzufügen neuer Aufgaben und Markieren vorhandener Aufgaben als abgeschlossen definiert.

         extends layout

         block content
           h1= title
           br

           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               each task in tasks
                 tr
                   td #{task.name}
                   td #{task.category}
               	   - var day   = task.Timestamp.getDate();
				   - var month = task.Timestamp.getMonth() + 1;
				   - var year  = task.Timestamp.getFullYear();
                   td #{month + "/" + day + "/" + year}
                   td
                     input(type="checkbox", name="#{task.RowKey}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
             button.btn(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             label Item Name: 
             input(name="item[name]", type="textbox")
             label Item Category: 
             input(name="item[category]", type="textbox")
             br
             button.btn(type="submit") Add item

3.  Speichern und schließen Sie die Datei **index.jade**.

### Modifizieren des globalen Layouts

Die Datei **layout.jade** im Verzeichnis **views** dient als globale Vorlage für andere **.jade**-Dateien. In diesem Schritt werden Sie sie modifizieren, um [Twitter Bootstrap](https://github.com/twbs/bootstrap) zu verwenden, ein Toolkit zum mühelosen Gestalten ansprechender Websites.

1.  Laden Sie die Dateien für [Twitter Bootstrap](http://getbootstrap.com/) herunter, und extrahieren Sie sie. Kopieren Sie die Datei **bootstrap.min.css** aus dem Ordner **bootstrap\\dist\\css** in das Verzeichnis **public\\stylesheets** Ihrer tasklist-Anwendung.

2.  Öffnen Sie im Ordner **views** die Datei **layout.jade** in einem Texteditor, und ersetzen Sie den Inhalt durch Folgendes:

         doctype 5
         html
           head
             title= title
             link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
             link(rel='stylesheet', href='/stylesheets/style.css')
           body.app
             nav.navbar.navbar-default
               div.navbar-header
                 a.navbar-brand(href='/') My Tasks
             block content

3.  Speichern Sie die Datei **layout.jade**.

### Erstellen der Konfigurationsdatei

Die Datei **config.json** enthält die Verbindungszeichenfolge, die zum Herstellen der Verbindung mit der SQL-Datenbank verwendet wird, und wird zur Laufzeit von der Anwendung gelesen. Zum Erstellen dieser Datei führen Sie die folgenden Schritte durch:

1.  Erstellen Sie im Verzeichnis **tasklist** eine neue Datei mit dem Namen **config.json**, und öffnen Sie diese in einem Texteditor.

2.  Der Inhalt der Datei **config.json** sollte etwa wie folgt aussehen:

         {
             "STORAGE_NAME": "storage account name",
             "STORAGE_KEY": "storage access key",
             "PARTITION_KEY": "mytasks",
             "TABLE_NAME": "tasks"
         }

    Ersetzen Sie **storage account name** durch den Namen des Speicherkontos, das Sie zuvor erstellt haben. Ersetzen Sie **storage access key** durch den primären Zugriffsschlüssel für Ihr Speicherkonto.

3.  Speichern Sie die Datei.

Lokales Ausführen der Anwendung
-------------------------------

Um die Anwendung lokal zu testen, führen Sie die folgenden Schritte durch:

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**.

2.  Verwenden Sie den folgenden Befehl, um die Anwendung lokal zu starten:

         node app.js

3.  Öffnen Sie einen Webbrowser, und navigieren Sie zu http://127.0.0.1:3000. Dies sollte zur Anzeige einer Webseite führen, die etwa wie folgt aussieht:

    ![Eine Webseite mit einer leeren Aufgabenliste](./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png)

4.  Füllen Sie die Felder für **Elementname** und **Elementkategorie** aus, und klicken Sie dann auf **Element hinzufügen**.

5.  Die Seite sollte nun das Element in der Aufgabenlistentabelle anzeigen.

    ![Ein Bild des neuen Elements in der Aufgabenliste](./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png)

6.  Zum Abschließen einer Aufgabe aktivieren Sie einfach das Kontrollkästchen in der entsprechenden Spalte, und klicken Sie dann auf **Aufgaben aktualisieren**.

7.  Zum Stoppen des Node-Prozesses wechseln Sie in die Befehlszeile, und drücken Sie gleichzeitig die Tasten **STRG** und **C**.

Bereitstellen der Anwendung in Azure
------------------------------------

Die Schritte in diesem Abschnitt verwenden die Azure-Befehlszeilentools zum Erstellen einer neuen Azure-Website und stellen die Anwendung dann mit Git bereit. Zur Durchführung dieser Schritte benötigen Sie ein Azure-Abonnement.

**Hinweis**

Diese Schritte können auch über das Azure-Portal ausgeführt werden. Informationen zur Verwendung des Azure-Portals zum Bereitstellen einer Node.js-Anwendung finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](http://content-ppe.windowsazure.com/de-de/develop/nodejs/tutorials/create-a-website-(mac)/).

**Hinweis**

Falls dies die erste von Ihnen erstellte Azure-Website ist, müssen Sie das Azure-Portal verwenden, um die Anwendung bereitzustellen.

### Aktivieren der Azure-Websitefunktion

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie sich [kostenlos](http://windowsazure.com) anmelden. Nach der Anmeldung führen Sie die folgenden Schritte aus, um die Azure-Websitefunktion zu aktivieren.

[WACOM.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

### Installieren des Azure-Befehlszeilentools für Mac und Linux

Verwenden Sie den folgenden Befehl, um die Befehlszeilentools zu installieren:

    npm install azure-cli -g

**Hinweis**

Wenn Sie das "Azure-SDK für Node.js" bereits aus dem [Azure-Entwicklercenter](/de-de/develop/nodejs/) installiert haben, sollten auch die Befehlszeilentools bereits installiert sein. Weitere Informationen finden Sie im Thema zum [Azure-Befehlszeilentool für Mac und Linux](/de-de/develop/nodejs/how-to-guides/command-line-tools/).

**Hinweis**

Obwohl die Befehlszeilentools vor allem für Mac-und Linux-Benutzer erstellt wurden, basieren sie auf "Node.js" und sollten auf allen Systemen funktionieren, die Node ausführen können.

### Importieren von Veröffentlichungseinstellungen

Bevor Sie die Befehlszeilentools mit Azure verwenden können, müssen Sie zunächst eine Datei mit Informationen zu Ihrem Abonnement herunterladen. Führen Sie die folgenden Schritte aus, um diese Datei herunterzuladen und zu importieren.

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**.

2.  Geben Sie den folgenden Befehl ein, um den Browser zu starten und zur Downloadseite zu navigieren. Melden Sie sich bei Aufforderung mit dem Konto an, das Ihrem Abonnement zugeordnet ist.

         azure account download

    ![Die Downloadseite](./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png)

    Der Dateidownload sollte automatisch beginnen. Klicken Sie andernfalls auf den Link oben auf der Seite, um die Datei manuell herunterzuladen.

3.  Nach Abschluss des Dateidownloads verwenden Sie den folgenden Befehl, um die Einstellungen zu importieren:

         azure account import <path-to-file>

    Geben Sie den Pfad und den Dateinamen der Datei mit den Veröffentlichungseinstellungen an, die Sie im vorherigen Schritt heruntergeladen haben. Nach Abschluss des Befehls sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

         info:   Executing command account import
         info:   Setting service endpoint to: management.core.windows.net
         info:   Setting service port to: 443
         info:   Found subscription: YourSubscription
         info:   Setting default subscription to: YourSubscription
         warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
         warn:   Remember to delete it now that it has been imported.
         info:   Account publish settings imported successfully
         info:   account import command OK

4.  Nach Abschluss des Imports sollten Sie die Datei mit den Veröffentlichungseinstellungen löschen, da sie nicht mehr benötigt wird und vertrauliche Informationen zu Ihrem Azure-Abonnement enthält.

### Erstellen einer Azure-Website

1.  Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**.

2.  Verwenden Sie den folgenden Befehl, um eine neue Azure-Website zu erstellen.

         azure site create --git

    Sie werden aufgefordert, den Websitenamen und das Datencenter einzugeben, in dem sich die Website befinden wird. Geben Sie einen eindeutigen Namen an, und wählen Sie ein Datencenter aus, das sich nahe an Ihrem Standort befindet.

    Der Parameter `--git` erstellt ein Git-Repository auf Azure für diese Website. Er initialisiert darüber hinaus ein Git-Repository im aktuellen Verzeichnis, sofern keines vorhanden ist. Außerdem erstellt er ein [Git remote](http://git-scm.com/docs/git-remote) namens "azure", das zum Veröffentlichen der Anwendung in Azure verwendet wird. Schließlich erstellt er eine Datei **web.config** mit Einstellungen, die von Azure zum Hosten von Node-Anwendungen verwendet werden.

    **Hinweis**

    Wenn dieser Befehl aus einem Verzeichnis ausgeführt wird, das bereits ein Git-Repository enthält, wird das Verzeichnis nicht erneut initialisiert.

    **Hinweis**

    Wenn der Parameter `--git` weggelassen wird, das Verzeichnis jedoch ein Git-Repository enthält, wird "azure" remote trotzdem erstellt.

    Nach Abschluss dieses Befehls wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass die Zeile, die mit **Web site created at** beginnt, die URL für die Website enthält.

         info:   Executing command site create
         help:   Need a site name
         Name: TableTasklist
         info:   Using location southcentraluswebspace
         info:   Executing `git init`
         info:   Creating default .gitignore file
         info:   Creating a new web site
         info:   Created web site at  tabletasklist.azurewebsites.net
         info:   Initializing repository
         info:   Repository initialized
         info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
         info:   site create command OK

    **Hinweis**

    Falls dies die erste Azure-Website für Ihr Abonnement ist, werden Sie angewiesen, das Portal zum Erstellen der Website zu verwenden. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/).

### Veröffentlichen der Anwendung

1.  Wechseln Sie im Terminalfenster zum Verzeichnis **tasklist**, falls Sie sich nicht bereits dort befinden.

2.  Verwenden Sie die folgenden Befehle, um Dateien hinzuzufügen und anschließend in das lokale Git-Repository zu übernehmen:

         git add .
         git commit -m "adding files"

3.  Wenn Sie die letzten Änderungen am Git-Repository per Push an die Azure-Website übertragen, müssen Sie angeben, dass die Zielstruktur **master** lautet, da diese für den Websiteinhalt verwendet wird.

         git push azure master

    Am Ende der Bereitstellung sollte eine Anweisung ähnlich der folgenden angezeigt werden:

         To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
		  * [new branch]	  master -> master

4.  Sobald der Push-Vorgang abgeschlossen ist, navigieren Sie zur Website-URL, die zuvor vom Befehl `azure create site` zurückgegeben wurde, um Ihre Anwendung anzuzeigen.

### Wechseln zu einer Umgebungsvariablen

Zuvor haben wir einen Code implementiert, der nach einer Umgebungsvariablen **SQL\_CONN** für die Verbindungszeichenfolge sucht oder den Wert aus der Datei **config.json** lädt. In den folgenden Schritten erstellen Sie ein Schlüssel/Wert-Paar in Ihrer Websitekonfiguration für echten Zugriff der Anwendung durch eine Umgebungsvariable.

1.  Klicken Sie im Verwaltungsportal auf **Websites**, und wählen Sie dann Ihre Website aus.

    ![Website-Dashboard öffnen](./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png)

2.  Klicken Sie auf **KONFIGURIEREN**, und suchen Sie den Abschnitt **app settings** der Seite.

    ![Konfigurationslink](./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png)

3.  Im Abschnitt **app settings** geben Sie **STORAGE\_NAME** in das Feld **SCHLÜSSEL** und den Namen Ihres Speicherkontos in das Feld **WERT** ein. Klicken Sie auf das Häkchen, um zum nächsten Feld zu wechseln. Wiederholen Sie diesen Vorgang für die folgenden Schlüssel und Werte:

    -   **STORAGE\_KEY** – der Zugriffsschlüssel für Ihr Speicherkonto

    -   **PARTITION\_KEY** – 'mytasks'

    -   **TABLE\_NAME** – 'tasks'

    ![app settings](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

4.  Zum Abschluss klicken Sie auf das Symbol **SPEICHERN** unten auf der Seite, um diese Änderung in die Laufzeitumgebung zu übernehmen.

    ![Speichern im Abschnitt "app settings"](./media/storage-nodejs-use-table-storage-web-site/savebutton.png)

5.  Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**, und entfernen Sie die Datei **config.json** mit dem folgenden Befehl:

         git rm config.json
         git commit -m "Removing config file"

6.  Führen Sie den folgenden Befehl aus, um die Änderungen in Azure bereitzustellen:

         git push azure master

Nachdem die Änderungen in Azure bereitgestellt wurden, sollte Ihre Webanwendung weiterhin funktionieren, da sie nun die Verbindungszeichenfolge aus dem Eintrag in **app settings** liest. Um dies zu überprüfen, ändern Sie den Wert für den Eintrag **STORAGE\_KEY** unter **app settings** in einen ungültigen Wert. Nachdem Sie diesen Wert gespeichert haben, sollte die Website aufgrund der ungültigen Einstellung für den Speicherzugriffsschlüssel nicht mehr funktionieren.

Nächste Schritte
----------------

Die Schritte in diesem Artikel beschreiben zwar die Verwendung des Tabellendienstes zum Speichern von Informationen, Sie können jedoch auch MongoDB verwenden. Weitere Informationen finden Sie unter [Node.js-Webanwendung mit MongoDB](/de-de/develop/nodejs/tutorials/website-with-mongodb-(Mac)/).

Zusätzliche Ressourcen
----------------------

[Azure-Befehlszeilentool für Mac und Linux]
[Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: /de-de/develop/nodejs/tutorials/create-a-website-(mac)/ 
[Veröffentlichen auf Azure-Websites mit Git](../CommonTasks/publishing-with-git): /de-de/develop/nodejs/common-tasks/publishing-with-git/ 
[Azure Developer Center]: /de-de/develop/nodejs/

[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git remote]: http://git-scm.com/docs/git-remote

[Node.js Web Application with MongoDB]: /de-de/develop/nodejs/tutorials/website-with-mongodb-(Mac)/
[Azure command-line tool for Mac and Linux]: /de-de/develop/nodejs/how-to-guides/command-line-tools/
[Create and deploy a Node.js application to an Azure Web Site]: ./web-site-with-mongodb-Mac
[Publishing to Azure Web Sites with Git]: ../CommonTasks/publishing-with-git
[azure]: https://github.com/WindowsAzure/azure-sdk-for-node


[Azure Portal]: http://windowsazure.com


[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png

[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png
