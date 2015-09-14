<properties
	pageTitle="Node.js-Web-App mit Azure-Tabellendienst"
	description="In diesem Tutorial erfahren Sie, wie Sie den Azure-Tabellendienst zum Speichern von Daten aus einer Node.js-Anwendung verwenden können, die in Azure App Service-Web-Apps gehostet wird."
	tags="azure-portal"
	services="app-service\web, storage"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="mwasson"/>



# Node.js-Web-App mit Azure-Tabellendienst

## Übersicht

In diesem Tutorial erfahren Sie, wie Sie den von der Azure-Datenverwaltung bereitgestellten Tabellendienst einsetzen können, um Daten von einer in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps gehosteten [Node]-Anwendung zu speichern und abzurufen. Bei diesem Tutorial wird davon ausgegangen, dass Sie bereits eine gewisse Erfahrung mit der Verwendung von Node und [Git] haben.

Sie erhalten Informationen zu folgenden Themen:

* Installieren von Node-Modulen über NPM (Node Package Manager = Paketverwaltung von Node.js)

* Arbeiten mit dem Azure-Tabellendienst

* Verwenden der Azure-Befehlszeilenschnittstelle zum Erstellen von Web-Apps.

In diesem Tutorial erstellen Sie eine einfache webbasierte Anwendung für Aufgabenlisten, mit der Sie Aufgaben erstellen, abrufen und ausführen können. Diese Aufgaben sind im Tabellendienst gespeichert.

Hier sehen Sie die erstellte Anwendung:

![Webseite mit einer leeren Aufgabenliste][node-table-finished]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.


## Voraussetzungen

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes installiert ist:

* [node], Version 0.10.24 oder höher

* [Git]


[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Erstellen Sie ein Speicherkonto.

Erstellen Sie ein Azure-Speicherkonto. Die App verwendet dieses Konto zum Speichern von Aufgaben.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie unten links im Portal auf das Symbol **Neu** und dann auf **Daten + Speicher** > **Speicher**. Geben Sie dem Speicherkonto einen eindeutigen Namen, und erstellen Sie eine neue [Ressourcengruppe](../resource-group-overview.md) dafür.

  	![Schaltfläche "Neu"](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)

	Wenn das Speicherkonto erstellt wurde, leuchtet auf der Schaltfläche **Benachrichtigungen** grün **ERFOLG**. Das Blatt des Speicherkontos wird geöffnet, und darauf wird angezeigt, dass es zu der neu erstellten Ressourcengruppe gehört.

5. Klicken Sie auf dem Blatt des Speicherkontos auf **Einstellungen** > **Schlüssel**. Kopieren Sie den primären Zugriffsschlüssel in die Zwischenablage.

    ![Zugriffsschüssel][portal-storage-access-keys]


##Installieren der Module und Generieren des Gerüsts

In diesem Abschnitt erstellen Sie eine neue Node-Anwendung und fügen Modulpakete mithilfe von npm hinzu. Für diese Anwendung verwenden Sie die Module [Express] und [Azure]. Das Express-Modul stellt ein Modellansichts-Controller-Framework für Node bereit, das Azure-Modul Konnektivität zum Tabellendienst.

### Installieren von Express und Generieren des Gerüsts

1. Erstellen Sie über die Befehlszeile ein neues Verzeichnis namens **tasklist**, und wechseln Sie zu diesem Verzeichnis.  

2. Geben Sie den folgenden Befehl ein, um das Express-Modul zu installieren.

		npm install express-generator@4.2.0 -g

    Je nach Betriebssystem müssen Sie vor dem Befehl möglicherweise "sudo" einfügen:

		sudo npm install express-generator@4.2.0 -g

    Die Ausgabe entspricht in etwa dem folgenden Beispiel:

		express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE]Mit dem Parameter "-g" wird das Modul global installiert. Auf diese Weise können wir mithilfe des Befehls **express** ein Web-App-Gerüst generieren, ohne zusätzliche Pfadinformationen angeben zu müssen.

4. Um das Gerüst für die Anwendung zu erstellen, geben Sie den Befehl **express** ein:

        express

	Die Ausgabe dieses Befehls entspricht in etwa dem folgenden Beispiel:

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www

		   install dependencies:
		     $ cd . && npm install

		   run the app:
		     $ DEBUG=my-application ./bin/www

	Im Verzeichnis **tasklist** sollten nun mehrere neue Verzeichnisse und Dateien vorhanden sein.

### Installieren zusätzlicher Module

**express** erstellt unter anderem die Datei **package.json**. Diese Datei enthält eine Liste der Modulabhängigkeiten. Wenn Sie die Anwendung später in App Service-Web-Apps bereitstellen, wird anhand dieser Datei bestimmt, welche Module für Azure installiert werden müssen.

Geben Sie in der Befehlszeile folgenden Befehl ein, um die in der Datei **package.json** beschriebenen Module zu installieren. Möglicherweise müssen Sie "sudo" einfügen.

    npm install

Die Ausgabe dieses Befehls entspricht in etwa dem folgenden Beispiel:

	debug@0.7.4 node_modules\debug

	cookie-parser@1.0.1 node_modules\cookie-parser
	├── cookie-signature@1.0.3
	└── cookie@0.1.0

    [...]


Geben Sie anschließend den folgenden Befehl zum Installieren der Module [azure], [node-uuid], [nconf] und [async] ein:

	npm install azure-storage node-uuid async nconf --save

Das Kennzeichen **--save** fügt der Datei **package.json** Einträge für diese Module hinzu.

Die Ausgabe dieses Befehls entspricht in etwa dem folgenden Beispiel:

	async@0.9.0 node_modules\async

	node-uuid@1.4.1 node_modules\node-uuid

	nconf@0.6.9 node_modules\nconf
	├── ini@1.2.1
	├── async@0.2.9
	└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

	[...]


## Erstellen der Anwendung

Jetzt können wir die Anwendung erstellen.

### Modellerstellung

Ein *Modell* ist ein Objekt, das für die Daten in Ihrer Anwendung steht. Für die Anwendung liegt als einziges Modell ein Task-Objekt vor, das ein Element in der Aufgabenliste darstellt. Aufgaben enthalten die folgenden Felder:

- Partitionsschlüssel
- Zeilenschlüssel
- Name (Zeichenfolge)
- Kategorie (Zeichenfolge)
- abgeschlossen (Boolescher Wert)

**PartitionKey** und **RowKey** werden vom Tabellendienst als Schlüssel verwendet. Weitere Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](https://msdn.microsoft.com/library/azure/dd179338.aspx).


1. Erstellen Sie im Verzeichnis **tasklist** ein neues Verzeichnis namens **models**.

2. Erstellen Sie im Verzeichnis **models** eine neue Datei namens **task.js**. Diese Datei enthält das Modell für die von Ihrer Anwendung erstellten Aufgaben.

3. Fügen Sie am Anfang der Datei **task.js** den folgenden Code hinzu, um auf die erforderlichen Bibliotheken zu verweisen:

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. Fügen Sie den folgenden Code hinzu, um das Task-Objekt zu definieren und zu exportieren. Dies Objekt stellt die Verbindung zur Tabelle her.

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

5. Fügen Sie den folgenden Code hinzu, um zusätzliche Methoden für das Task-Objekt zu definieren, die Interaktionen mit in der Tabelle gespeicherten Daten ermöglichen:

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    // use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };
		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. Speichern und schließen Sie die Datei **task.js**.

### Erstellen eines Controllers

Ein *Controller* verarbeitet HTTP-Anforderungen und rendert die HTML-Antwort.

1. Erstellen Sie im Verzeichnis **tasklist/routes** eine neue Datei namens **tasklist.js**, und öffnen Sie sie in einem Texteditor.

2. Fügen Sie **tasklist.js** den folgenden Code hinzu. Hierdurch werden die Module "azure" und "async" geladen, die von **tasklist.js** verwendet werden. Weiterhin wird hierdurch die Funktion **TaskList** definiert, an die eine Instanz des zuvor definierten **Task**-Objekts übergeben wird:

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

3. Definieren Sie ein **TaskList**-Objekt.

		function TaskList(task) {
		  this.task = task;
		}


4. Fügen Sie die folgenden Methoden zu **TaskList** hinzu:

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = new azure.TableQuery()
		      .where('completed eq ?', false);
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


### Ändern von app.js

1. Öffnen Sie im Verzeichnis **tasklist** die Datei **app.js**. Diese Datei wurde zuvor mit dem Befehl **express** erstellt.

2. Fügen Sie am Anfang der Datei den folgenden Code hinzu, um das azure-Modul zu laden und den Tabellennamen, den Partitionsschlüssel sowie die in diesem Beispiel verwendeten Speicheranmeldeinformationen festzulegen:

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json', search: true });
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [AZURE.NOTE]Nconf lädt die Konfigurationswerte entweder aus Umgebungsvariablen oder aus der Datei **config.json**, die wir später erstellen.

3. Blättern Sie in der Datei "app.js" nach unten, bis Sie die folgende Zeile sehen:

		app.use('/', routes);
		app.use('/users', users);

	Ersetzen Sie die vorstehenden Zeilen durch den nachfolgenden Code. Hierdurch wird eine Instanz von <strong>Task</strong> mit einer Verbindung zu Ihrem Speicherkonto initialisiert. Diese wird an die <strong>TaskList</strong> übergeben, die darüber mit dem Tabellendienst kommuniziert:

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

4. Speichern Sie die Datei **app.js**.

### Ändern der Indexansicht

1. Öffnen Sie die Datei **tasklist/views/index.jade** in einem Text-Editor.

2. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code. Damit werden die Ansicht zum Anzeigen vorhandener Aufgaben sowie ein Formular zum Hinzufügen neuer Aufgaben und zum Markieren von vorhandenen Aufgaben als abgeschlossen definiert.

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
		      if (typeof tasks === "undefined")
		        tr
		          td
		      else
		        each task in tasks
		          tr
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="item[name]", type="textbox")
		    label Item Category:
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. Speichern und schließen Sie die Datei **index.jade**.

### Modifizieren des globalen Layouts

Die Datei **layout.jade** im Verzeichnis **Ansichten** dient als globale Vorlage für andere **.jade**-Dateien. In diesem Schritt werden Sie sie modifizieren, um [Twitter Bootstrap](https://github.com/twbs/bootstrap) zu verwenden, ein Toolkit zum mühelosen Gestalten ansprechender Web-Apps.

Laden Sie die Dateien für [Twitter Bootstrap](http://getbootstrap.com/) herunter, und extrahieren Sie sie. Kopieren Sie die Datei **bootstrap.min.css** aus dem Bootstrap-Ordner **css** in das Verzeichnis **public/stylesheets** Ihrer Anwendung.

Öffnen Sie im Ordner **Ansichten** die Datei **layout.jade**, und ersetzen Sie den gesamten Inhalt durch Folgendes:

	doctype html
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

### Erstellen einer Konfigurationsdatei

Zum lokalen Ausführen der App fügen wir Azure Storage-Anmeldeinformationen in eine Konfigurationsdatei ein. Erstellen Sie eine Datei namens **config.json** mit folgendem JSON-Inhalt:

	{
		"STORAGE_NAME": "<storage account name>",
		"STORAGE_KEY": "<storage access key>",
		"PARTITION_KEY": "mytasks",
		"TABLE_NAME": "tasks"
	}

Ersetzen Sie **Speicherkontoname** durch den Namen des zuvor erstellten Speicherkontos und **Speicherzugriffsschlüssel** durch den primären Zugriffsschlüssel für Ihr Speicherkonto. Beispiel:

	{
	    "STORAGE_NAME": "nodejsappstorage",
	    "STORAGE_KEY": "KG0oDd..."
	    "PARTITION_KEY": "mytasks",
	    "TABLE_NAME": "tasks"
	}

Speichern Sie diese Datei *eine Verzeichnisebene höher* als das Verzeichnis **tasklist**:

	parent/
	  |-- config.json
	  |-- tasklist/

Dies ist erforderlich, damit die Konfigurationsdatei nicht in die Quellcodeverwaltung eingecheckt wird, wo sie unter Umständen öffentlich wird. Bei der Bereitstellung der App in Azure verwenden wir Umgebungsvariablen anstelle einer Konfigurationsdatei.


## Lokales Ausführen der Anwendung

Um die Anwendung lokal zu testen, führen Sie die folgenden Schritte durch:

1. Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**.

2. Verwenden Sie den folgenden Befehl, um die Anwendung lokal zu starten:

        npm start

3. Öffnen Sie einen Webbrowser, und navigieren Sie zu http://127.0.0.1:3000.

	Es wird eine Webseite angezeigt, die dem folgenden Beispiel entspricht:

	![Eine Webseite mit einer leeren Aufgabenliste][node-table-finished]

4. Um ein neues Aufgabenelement zu erstellen, geben Sie einen Namen und eine Kategorie ein, und klicken Sie auf **Element hinzufügen**.

6. Wenn Sie eine Aufgabe als abgeschlossen markieren möchten, aktivieren Sie **Abgeschlossen**, und klicken Sie auf **Aufgaben aktualisieren**.

	![Ein Bild des neuen Elements in der Aufgabenliste][node-table-list-items]

Obwohl die Anwendung lokal ausgeführt wird, werden die Daten im Azure-Tabellendienst gespeichert.

## Bereitstellen der Anwendung in Azure

Die Schritte in diesem Abschnitt verwenden die Azure-Befehlszeilentools zum Erstellen einer neuen Web-App in App Service und verwenden dann Git zum Bereitstellen der Anwendung. Zur Durchführung dieser Schritte benötigen Sie ein Azure-Abonnement.

> [AZURE.NOTE]Diese Schritte können auch über das [Azure-Portal](https://portal.azure.com) ausgeführt werden. Informationen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Web-App in Azure App Service].
>
> Falls dies die erste von Ihnen erstellte Web-App ist, müssen Sie die Anwendung über das Azure-Portal bereitstellen.

Installieren Sie zunächst die [Azure-CLI], indem Sie an der Befehlszeile den folgenden Befehl eingeben:

	npm install azure-cli -g

### Importieren von Veröffentlichungseinstellungen

In diesem Schritt laden Sie eine Datei mit Informationen zu Ihrem Abonnement herunter.

1. Geben Sie den folgenden Befehl ein:

		azure account download

	Dieser Befehl startet einen Browser und navigiert zur Downloadseite. Melden Sie sich bei entsprechender Aufforderung mit dem Konto an, dass Ihrem Azure-Abonnement zugeordnet ist.

	<!-- ![The download page][download-publishing-settings] -->Der Dateidownload beginnt automatisch. Klicken Sie andernfalls auf den Link oben auf der Seite, um die Datei manuell herunterzuladen. Speichern Sie die Datei, und notieren Sie den Dateipfad.

2. Geben Sie den folgenden Befehl ein, um die Einstellungen zu importieren:

		azure account import <path-to-file>

	Geben Sie den Pfad und den Dateinamen der Datei mit den Veröffentlichungseinstellungen an, die Sie im vorherigen Schritt heruntergeladen haben.

3. Nachdem die Einstellungen importiert wurden, löschen Sie die Datei mit den Veröffentlichungseinstellungen. Sie wird nicht mehr benötigt und enthält vertrauliche Informationen zu Ihrem Azure-Abonnement.

### Erstellen einer App-Service-Web-App

1. Wechseln Sie an der Befehlszeile zum Verzeichnis **tasklist**.

2. Verwenden Sie den folgenden Befehl, um eine neue Web-App zu erstellen.

		azure site create --git

	Sie werden zur Eingabe des Namens und des Speicherorts der Web-App aufgefordert. Geben Sie einen eindeutigen Namen ein, und wählen Sie denselben geografischen Standort wie für Ihr Azure-Speicherkonto aus.

	Der `--git`-Parameter erstellt ein Git-Repository in Azure für diese Web-App. Er initialisiert zudem ein Git-Repository im aktuellen Verzeichnis, sofern noch keins vorhanden ist, und fügt ein [Git-Remoterepository] namens "azure" hinzu, mit dem die Anwendung in Azure veröffentlicht wird. Schließlich erstellt er die Datei **web.config** mit Einstellungen, die von Azure zum Hosten von Node-Anwendungen verwendet werden. Wenn der `--git`-Parameter weggelassen wird, das Verzeichnis jedoch trotzdem ein Git-Repository enthält, wird das Remoterepository "azure" erstellt.

	Nach Abschluss dieses Befehls wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass die Zeile, die mit **Website erstellt am** beginnt, die URL für die Web-App enthält.

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

	> [AZURE.NOTE]Falls dies die erste App Service-Web-App für Ihr Abonnement ist, werden Sie angewiesen, das Azure-Portal zum Erstellen der Web-App zu verwenden. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Web-App in Azure App Service].

### Festlegen von Umgebungsvariablen

In diesem Schritt fügen Sie Ihrer Web-App-Konfiguration für Azure Umgebungsvariablen hinzu. Geben Sie in der Befehlszeile Folgendes ein:

	azure site appsetting add
		STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


Ersetzen Sie **<storage account name>** durch den Namen des zuvor erstellten Speicherkontos und **<storage access key>** durch den primären Zugriffsschlüssel für Ihr Speicherkonto. (Verwenden Sie die gleichen Werte wie die Datei "config.json", die Sie zuvor erstellt haben.)

Alternativ können Sie Umgebungsvariablen im [Azure-Portal](https://portal.azure.com) festlegen:

1.  Öffnen Sie das Blatt der Web-App, indem Sie auf **Durchsuchen** > **Web-Apps** > "Name Ihrer Web-App" klicken.

1.  Klicken Sie auf dem Blatt Ihrer Web-App auf **Alle Einstellungen** > **Anwendungseinstellungen**.

  	<!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->

1.  Führen Sie einen Bildlauf nach unten zum Abschnitt **App-Einstellungen** aus, und fügen Sie die Schlüssel/Wert-Paare hinzu.

  	![App-Einstellungen](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

1. Klicken Sie auf **SPEICHERN**.


### Veröffentlichen der Anwendung

Führen Sie zum Veröffentlichen der App einen Commit für die Codedateien zu Git aus, und übertragen Sie sie anschließend mithilfe von Push an "azure/master".

1. Legen Sie Ihre Anmeldeinformationen für die Bereitstellung fest.

		azure site deployment user set <name> <password>

2. Fügen Sie die Anwendungsdateien hinzu, und führen Sie einen Commit für sie aus.

		git add .
		git commit -m "adding files"

3. Übertragen Sie den Commit mithilfe von Push an die App Service-Web-App:

		git push azure master

	Verwenden Sie **master** als Zielstruktur. Am Ende der Bereitstellung wird eine Anweisung angezeigt, die in etwa dem folgenden Beispiel entspricht:

		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. Sobald der Push-Vorgang abgeschlossen ist, navigieren Sie zur Web-App-URL, die zuvor vom Befehl `azure create site` zurückgegeben wurde, um Ihre Anwendung anzuzeigen.


## Nächste Schritte

Die Schritte in diesem Artikel beschreiben zwar die Verwendung des Tabellendienstes zum Speichern von Informationen, Sie können jedoch auch MongoDB verwenden. Weitere Informationen finden Sie unter [Node.js-Web-App mit MongoDB].

## Zusätzliche Ressourcen

[Azure-Befehlszeilenschnittstelle]

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Azure-Portals gegenüber dem Azure-Vorschauportal finden Sie unter [Referenz zur Navigation im Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715)

[Erstellen und Bereitstellen einer Node.js-Web-App in Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[Azure Developer Center]: /develop/nodejs/


[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git-Remoterepository]: http://git-scm.com/docs/git-remote

[Node.js-Web-App mit MongoDB]: web-sites-nodejs-store-data-mongodb.md
[Azure-Befehlszeilenschnittstelle]: ../xplat-cli.md
[Azure-CLI]: ../xplat-cli.md

[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[azure]: https://github.com/Azure/azure-sdk-for-node
[node-uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[async]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com


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

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md
 

<!---HONumber=September15_HO1-->