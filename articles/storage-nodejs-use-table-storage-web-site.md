<properties 
	pageTitle="Node.js-Website mit Tabellenspeicher | Microsoft Azure" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie den Tabellendienst zum Speichern von Daten aus einer Node-Anwendung, die auf einer Azure-Website gehostet wird, verwenden können." 
	services="web-sites, storage" 
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
	ms.date="09/17/2014" 
	ms.author="mwasson"/>





# Node.js-Webanwendung mit Azure-Tabellendienst

In diesem Lernprogramm erfahren Sie, wie Sie den von der Azure-Datenverwaltung bereitgestellten Tabellendienst einsetzen können, um Daten von einer auf Azure gehosteten [Node]-Anwendung zu speichern und abzurufen. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie bereits eine gewisse Erfahrung mit der Verwendung von Node und [Git] haben.

Sie erhalten Informationen zu folgenden Themen:

* Installieren von Node-Modulen über NPM (Node Package Manager = Paketverwaltung von Node.js)

* Arbeiten mit dem Azure-Tabellendienst

* Erstellen einer Azure-Website mit dem Azure-Befehlszeilentool für Mac und Linux

In diesem Lernprogramm erstellen Sie eine einfache webbasierte Anwendung zur Aufgabenverwaltung, mit der Sie Aufgaben erstellen, abrufen und vervollständigen können. Diese Aufgaben sind im Tabellendienst gespeichert.

Die Projektdateien für dieses Lernprogramm werden in einem Verzeichnis namens **tasklist** gespeichert, und die fertige Anwendung sieht etwa wie folgt aus:

![A web page displaying an empty tasklist][node-table-finished]

> [AZURE.NOTE] In diesem Lernprogramm wird auf den Ordner **tasklist** Bezug genommen. Der vollständige Ordnerpfad wird nicht angegeben, da die Pfadsemantik je nach Betriebssystem unterschiedlich ist. Sie sollten diesen Ordner in einem Verzeichnis erstellen, auf das Sie im lokalen Dateisystem einfach zugreifen können, z. B. **~/node/tasklist** oder **c:\node\tasklist**.

> [AZURE.NOTE] Im Folgenden wird öfters die Verwendung der Befehlszeile verlangt. In diesen Fällen verwenden Sie die Befehlszeile für Ihr Betriebssystem, wie zum Beispiel **cmd.exe** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die Terminal-Anwendung zugreifen.

##Voraussetzungen

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes installiert ist:

* [node], Version 0.10.24 oder höher

* [Git]

* Ein Texteditor

* Ein Webbrowser

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Speicherkonto erstellen

Führen Sie die folgenden Schritte aus, um ein Speicherkonto zu erstellen. Dieses Konto wird in den weiteren Anweisungen dieses Lernprogramms verwendet.

1. Öffnen Sie den Webbrowser, und navigieren Sie zum [Azure-Portal]. Melden Sie sich bei Aufforderung mit Ihren Azure-Abonnementinformationen an.

2. Klicken Sie unten im Portal auf **+ NEU**, und wählen Sie **Speicherkonto** aus.

	![+new][portal-new]

	![storage account][portal-storage-account]

3. Wählen Sie **Schnellerfassung**, und geben Sie die URL und die Region/Affinität für dieses Speicherkonto ein. Da es sich hierbei um ein Lernprogramm handelt, das keine globale Replikation erfordert, deaktivieren Sie die Option **Geografische Replikation aktivieren**. Klicken Sie abschließend auf "Speicherkonto erstellen".

	![quick create][portal-quick-create-storage]

	Notieren Sie sich die eingegebene URL, da sie in späteren Schritten als Kontoname verwendet wird.

4. Nach der Erstellung des Speicherkontos klicken Sie unten auf der Seite auf **Schlüssel verwalten**. Daraufhin werden der primäre und sekundäre Zugriffsschlüssel für dieses Speicherkonto angezeigt. Kopieren und speichern Sie den primären Zugriffsschlüssel, und klicken Sie dann auf das Häkchen.

	![access keys][portal-storage-access-keys]

##Installieren der Module und Generieren des Gerüsts

In diesem Abschnitt erstellen Sie eine neue Node-Anwendung und fügen Modulpakete mithilfe von npm hinzu. Für die task-list-Anwendung verwenden Sie die Module[Express] und [Azure]. Das Express-Modul stellt ein Modellansichts-Controller-Framework für Node bereit, das Azure-Modul Konnektivität zum Tabellendienst.

###Installieren von Express und Generieren des Gerüsts

1. Wechseln Sie in der Befehlszeile zum Verzeichnis **tasklist**. Falls das Verzeichnis **tasklist** nicht vorhanden ist, erstellen Sie es.

2. Geben Sie Folgendes ein, um den Befehl "Express" zu installieren.

		npm install express-generator@4.2.0 -g

    > [AZURE.NOTE] Bei Verwendung des Parameters "-g" wird in einigen Betriebssystemen eventuell ein Fehler ähnlich dem folgenden angezeigt: **Error: EPERM, chmod '/usr/local/bin/express'**, verbunden mit der Aufforderung, das Konto als Administrator auszuführen. Verwenden Sie in diesem Fall den Befehl **sudo**, um npm mit einer höheren Berechtigungsstufe auszuführen.

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		express-generator@4.2.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE] Wird der Parameter "-g" bei der Installation des Express-Moduls verwendet, so erfolgt die Installation global. Auf diese Weise kann der Befehl **Express** aufgerufen werden, um das Websitegerüst zu generieren, ohne zusätzliche Pfadinformationen angeben zu müssen.**

4. Verwenden Sie den Befehl **Express**, um das Gerüst zu erstellen, das für diese Anwendung verwendet wird:

        express

	Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

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

	Nach Abschluss dieses Befehls sollten mehrere neue Verzeichnisse und Dateien im Verzeichnis **tasklist** vorhanden sein.

###Installieren zusätzlicher Module

Die Datei **package.json** ist eine der durch den Befehl **Express** erstellten Dateien. Diese Datei enthält eine Liste zusätzlicher Module, die für eine Express-Anwendung erforderlich sind. Wenn Sie später diese Anwendung auf einer Azure-Website bereitstellen, wird anhand dieser Datei bestimmt, welche Module auf Azure installiert werden müssen, um Ihre Anwendung zu unterstützen.

1. Wechseln Sie über die Befehlszeile in den Ordner **tasklist**, und geben Sie Folgendes ein, um die in der Datei **package.json** beschriebenen Module zu installieren:

        npm install

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		debug@0.7.4 node_modules\debug
		
		static-favicon@1.0.2 node_modules\static-favicon
		
		morgan@1.0.1 node_modules\morgan
		└── bytes@0.3.0
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.7 (string_decoder@0.10.25-1, bytes@1.0.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.2.0 node_modules\express
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── cookie@0.1.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── debug@0.8.1
		├── fresh@0.2.2
		├── qs@0.6.6
		├── range-parser@1.0.0
		├── methods@1.0.0
		├── buffer-crc32@0.2.1
		├── serve-static@1.1.0
		├── path-to-regexp@0.1.2
		├── send@0.3.0 (debug@0.8.0, mime@1.2.11)
		├── type-is@1.1.0 (mime@1.2.11)
		└── accepts@1.0.1 (negotiator@0.4.7, mime@1.2.11)
		
		jade@1.3.1 node_modules\jade
		├── commander@2.1.0
		├── character-parser@1.2.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── with@3.0.0 (uglify-js@2.4.15)


	Hierdurch werden alle von Express benötigten Standardmodule installiert.

2. Geben Sie nun den folgenden Befehl ein zum lokalen Installieren der [azure]-, [node-uuid]-, [nconf] und [async]-Module, und um für diese einen Eintrag zur **package.json**-Datei zu speichern:

		npm install azure-storage node-uuid async nconf --save

	Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		async@0.9.0 node_modules\async

		node-uuid@1.4.1 node_modules\node-uuid
		
		nconf@0.6.9 node_modules\nconf
		├── ini@1.2.1
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)
		
		azure-storage@0.3.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── validator@3.1.0
		├── underscore@1.4.4
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (forever-agent@0.5.2, aws-sign@0.3.0, json-stringify-safe@5.0.0, tunnel-agent@0.3.0, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, form-data@0.1.4, hawk@1.0.0, http-signature@0.10.0)

##Verwenden des Tabellendiensts in einer Node-Anwendung

In diesem Abschnitt erweitern Sie die durch den Befehl **Express** erstellte Grundanwendung, indem Sie die Datei **task.js** hinzufügen, die das Modell für Ihre Aufgaben enthält. Außerdem ändern Sie die vorhandene **app.js**, und erstellen Sie eine neue **tasklist.js**-Datei, die das Modell verwendet.

### Erstellen des Modells

1. Erstellen Sie im Verzeichnis **tasklist** ein neues Verzeichnis namens **models**.

2. Erstellen Sie im Verzeichnis **models** eine neue Datei namens **task.js**. Diese Datei enthält das Modell für die von Ihrer Anwendung erstellten Aufgaben.

3. Fügen Sie am Anfang der **task.js**-Datei den folgenden Code hinzu, um auf die erforderlichen Bibliotheken zu verweisen:

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. Anschließend fügen Sie Code hinzu, um das Task-Objekt zu definieren und zu exportieren. Dies Objekt stellt die Verbindung zur Tabelle her.

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

5. Fügen Sie als Nächstes den folgenden Code hinzu, um zusätzliche Methoden für das Task-Objekt zu definieren, die Interaktionen mit in der Tabelle gespeicherten Daten ermöglichen:

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

###Erstellen des Controllers

1. Erstellen Sie im Verzeichnis **tasklist/routes** eine neue Datei namens **tasklist.js**, und öffnen Sie sie in einem Texteditor.

2. Fügen Sie **tasklist.js** den folgenden Code hinzu. Hierdurch werden die Module "azure" und "async" geladen, die von **tasklist.js** verwendet werden. Weiterhin wird hierdurch die Funktion **TaskList** definiert, an die eine Instanz des zuvor definierten **Task**-Objekts übergeben wird:

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

		function TaskList(task) {
		  this.task = task;
		}

2. Fügen Sie außerdem zur Datei **tasklist.js** die Methoden **showTasks****, addTask** und **completeTasks** hinzu:

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

3. Speichern Sie die Datei **tasklist.js**.

### Ändern von app.js

1. Öffnen Sie im Verzeichnis **tasklist** die Datei **app.js** in einem Texteditor. Diese Datei wurde zuvor mit dem Befehl **Express** erstellt.

2. Fügen Sie am Anfang der Datei den folgenden Code hinzu, um das azure-Modul zu laden und den Tabellennamen, den partitionKey sowie die in diesem Beispiel verwendeten Speicheranmeldeinformationen hinzuzufügen:

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json'});
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [AZURE.NOTE] Nconf lädt die Konfigurationswerte entweder aus Umgebungsvariablen oder aus der Datei **config.json**, die wir später erstellen.

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

###Ändern der Indexansicht

1. Wechseln Sie zum Verzeichnis **Ansichten**, und öffnen Sie die Datei **index.jade** in einem Texteditor.

2. Ersetzen Sie den Inhalt der **index.jade** Datei durch den folgenden Code. Damit werden die Ansicht zur Anzeige vorhandener Aufgaben sowie ein Formular für das Hinzufügen neuer Aufgaben und das Markieren von vorhandenen Aufgaben als abgeschlossen definiert.

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

###Modifizieren des globalen Layouts

Die Datei **layout.jade** im Verzeichnis **Ansichten** dient als globale Vorlage für andere **.jade**-Dateien. In diesem Schritt werden Sie sie modifizieren, um [Twitter Bootstrap](https://github.com/twbs/bootstrap) zu verwenden, ein Toolkit zum mühelosen Gestalten ansprechender Websites.

1. Laden Sie die Dateien für [Twitter Bootstrap](http://getbootstrap.com/) herunter, und extrahieren Sie sie. Kopieren Sie die Datei **bootstrap.min.css** aus dem Ordner **bootstrap\\dist\\css** in das Verzeichnis **public\\stylesheets** Ihrer tasklist-Anwendung.

2. Öffnen Sie im Ordner **Ansichten** die Datei **layout.jade** in einem Texteditor, und ersetzen Sie den Inhalt durch Folgendes:

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

3. Speichern Sie die Datei **layout.jade**.

###Erstellen der Konfigurationsdatei

Die Datei **config.json** enthält die Verbindungszeichenfolge, die zum Herstellen der Verbindung mit der SQL-Datenbank verwendet wird, und wird zur Laufzeit von der Anwendung gelesen. Zum Erstellen dieser Datei führen Sie die folgenden Schritte durch:

1. Erstellen Sie im Verzeichnis **tasklist** eine neue Datei mit dem Namen **config.json**, und öffnen Sie diese in einem Texteditor.

2. Der Inhalt der Datei **config.json** sollte etwa wie folgt aussehen:

		{
			"STORAGE_NAME": "storage account name",
			"STORAGE_KEY": "storage access key",
			"PARTITION_KEY": "mytasks",
			"TABLE_NAME": "tasks"
		}

	Ersetzen Sie **Speicherkontoname** durch den Namen des Speicherkontos, das Sie zuvor erstellt haben. Ersetzen Sie **storage access key** durch den primären Zugriffsschlüssel für Ihr Speicherkonto.

3. Speichern Sie die Datei.

##Lokales Ausführen der Anwendung

Um die Anwendung lokal zu testen, führen Sie die folgenden Schritte durch:

1. Wechseln Sie in der Befehlszeile zum Verzeichnis **tasklist**.

2. Verwenden Sie den folgenden Befehl, um die Anwendung lokal zu starten:

        npm start

3. Öffnen Sie einen Webbrowser und navigieren Sie zu http://127.0.0.1:3000. Eine Webseite ähnlich der folgenden sollte angezeigt werden:

    ![A webpage displaying an empty tasklist][node-table-finished]

4. Füllen Sie die Felder für **Elementname** und **Elementkategorie** aus, und klicken Sie dann auf **Element hinzufügen**.

5. Die Seite sollte nun das Element in der Aufgabenlistentabelle anzeigen.

    ![An image of the new item in the list of tasks][node-table-list-items]

6. Zum Abschließen einer Aufgabe aktivieren Sie einfach das Kontrollkästchen in der entsprechenden Spalte, und klicken Sie dann auf **Aufgaben aktualisieren**.

7. Zum Stoppen des Node-Prozesses wechseln Sie in die Befehlszeile, und drücken Sie gleichzeitig die Tasten **STRG** und **C**.

##Bereitstellen der Anwendung in Azure

Die Schritte in diesem Abschnitt verwenden die Azure-Befehlszeilentools zum Erstellen einer neuen Azure-Website und stellen die Anwendung dann mit Git bereit. Zur Durchführung dieser Schritte benötigen Sie ein Azure-Abonnement.

> [AZURE.NOTE] Diese Schritte können auch über das Azure-Portal ausgeführt werden. Möchten Sie das Azure-Portal nutzen, um eine Node.js-Anwendung bereitzustellen, siehe [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website].

> [AZURE.NOTE] Falls dies die erste von Ihnen erstellte Azure-Website ist, müssen Sie das Azure-Portal verwenden, um die Anwendung bereitzustellen.

###Erstellen eines Azure-Abonnements

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie sich [kostenlos] anmelden. Führen Sie nach der Anmeldung die folgenden Schritte aus, um das Lernprogramm fortzusetzen.

[AZURE.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

###Installieren des Azure-Befehlszeilentools für Mac und Linux

Verwenden Sie den folgenden Befehl, um die Befehlszeilentools zu installieren:
	
	npm install azure-cli -g

> [AZURE.NOTE] Weitere Informationen finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](/de-de/documentation/articles/xplat-cli/);

> [AZURE.NOTE] Obwohl die Befehlszeilentools vor allem für Mac-und Linux-Benutzer erstellt wurden, basieren sie auf "Node.js" und sollten auf allen Systemen funktionieren, die Node ausführen können.

###Importieren von Veröffentlichungseinstellungen

Bevor Sie die Befehlszeilentools mit Azure verwenden können, müssen Sie zunächst eine Datei mit Informationen zu Ihrem Abonnement herunterladen. Führen Sie die folgenden Schritte aus, um diese Datei herunterzuladen und zu importieren.

1. Wechseln Sie an der Befehlszeile zum Verzeichnis **tasklist**.

2. Geben Sie den folgenden Befehl ein, um den Browser zu starten und zur Downloadseite zu navigieren. Melden Sie sich bei Aufforderung mit dem Konto an, dass Ihrem Abonnement zugeordnet ist.

		azure account download
	
	![The download page][download-publishing-settings]
	
	Der Dateidownload sollte automatisch beginnen. Klicken Sie andernfalls auf den Link oben auf der Seite, um die Datei manuell herunterzuladen.

3. Nach Abschluss des Dateidownloads verwenden Sie den folgenden Befehl, um die Einstellungen zu importieren:

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

4. Nach Abschluss des Imports sollten Sie die Datei mit den Veröffentlichungseinstellungen löschen, da sie nicht mehr benötigt wird und vertrauliche Informationen zu Ihrem Azure-Abonnement enthält.

###Erstellen einer Azure-Website

1. Wechseln Sie in der Befehlszeile zum Verzeichnis **tasklist**.

2. Verwenden Sie den folgenden Befehl, um eine neue Azure-Website zu erstellen.

		azure site create --git
		
	Sie werden aufgefordert, den Websitenamen und das Datencenter einzugeben, in dem sich die Website befinden wird. Geben Sie einen eindeutigen Namen an, und wählen Sie ein Datencenter aus, das sich nahe an Ihrem Standort befindet.
	
	Der `--git` -Parameter erstellt ein Git-Repository auf Azure für diese Website. Er initialisiert darüber hinaus ein Git-Repository im aktuellen Verzeichnis, sofern keines vorhanden ist. Er erstellt auch eine [Git Remote] mit dem Namen 'azure', die verwendet werden soll, um die Anwendung auf Azure zu veröffentlichen. Schließlich erstellt er eine Datei **web.config** mit Einstellungen, die von Azure zum Hosten von Node-Anwendungen verwendet werden.
	
	> [AZURE.NOTE] Wenn dieser Befehl aus einem Verzeichnis ausgeführt wird, das bereits ein Git-Repository enthält, wird das Verzeichnis nicht erneut initialisiert.
	
	> [AZURE.NOTE] Wenn der `--git`-Parameter weggelassen wird, aber das Verzeichnis trotzdem noch ein Git-Repository enthält, wird das "Azure"-Remote trotzdem erstellt.
	
	Nach Abschluss dieses Befehls wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass die Zeile, die mit **Website erstellt am** beginnt, die URL für die Website enthält.
	
		info:   Executing command site create
		help:   Need a site name
		Name:   TableTasklist
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default .gitignore file
		info:   Creating a new web site
		info:   Created web site at  tabletasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
		info:   site create command OK

	> [AZURE.NOTE] Falls dies die erste Azure-Website für Ihr Abonnement ist, werden Sie angewiesen, das Portal zum Erstellen der Website zu verwenden. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website].

###Veröffentlichen der Anwendung

1. Wechseln Sie im Terminalfenster zum Verzeichnis **tasklist**, falls Sie sich nicht bereits dort befinden.

2. Verwenden Sie die folgenden Befehle, um Dateien hinzuzufügen und anschließend in das lokale Git-Repository zu übernehmen:

		git add .
		git commit -m "adding files"

3. Wenn Sie die letzten Änderungen am Git-Repository per Push an die Azure-Website übertragen, müssen Sie angeben, dass die Zielstruktur **master** lautet, da dies für den Websiteinhalt verwendet wird.

		git push azure master
	
	Am Ende der Bereitstellung sollte eine Anweisung ähnlich der folgenden angezeigt werden:
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. Sobald der Push-Vorgang abgeschlossen ist, navigieren Sie zur Website-URL, die zuvor vom Befehl  `azure create site` zurückgegeben wurde, um Ihre Anwendung anzuzeigen.

###Wechseln zu einer Umgebungsvariablen

Zuvor haben wir einen Code implementiert, der nach einer Umgebungsvariablen sucht, oder den Wert aus der Datei **config.json** lädt. In den folgenden Schritten erstellen Sie Schlüssel/Wert-Paare in Ihrer Websitekonfiguration für echten Zugriff der Anwendung durch eine Umgebungsvariable.

1. Klicken Sie im Verwaltungsportal auf **Websites**, und wählen Sie dann Ihre Website aus.

	![Open website dashboard][go-to-dashboard]

2. Klicken Sie auf **KONFIGURIEREN**, und suchen Sie den Abschnitt **App-Einstellungen** der Seite. 

	![configure link][web-configure]

3. Im Abschnitt **App-Einstellungen** geben Sie **STORAGE_NAME** in das Feld **SCHLÜSSEL** und den Namen Ihres Speicherkontos in das Feld **WERT** ein. Klicken Sie auf das Häkchen, um zum nächsten Feld zu wechseln. Wiederholen Sie diesen Vorgang für die folgenden Schlüssel und Werte:

	* **STORAGE_KEY**: Der Zugriffsschlüssel für Ihr Speicherkonto
	
	* **PARTITION_KEY** - 'mytasks'

	* **TABLE_NAME** - 'tasks'

	![app settings][app-settings]

4. Zum Abschluss klicken Sie auf das Symbol **SPEICHERN** unten auf der Seite, um diese Änderung in die Laufzeitumgebung zu übernehmen.

	![app settings save][app-settings-save]

5. Wechseln Sie über die Befehlszeile in das Verzeichnis **tasklist**, und entfernen Sie die Datei **config.json** file:

		git rm config.json
		git commit -m "Removing config file"

6. Führen Sie den folgenden Befehl aus, um die Änderungen in Azure bereitzustellen:

		git push azure master

Nachdem die Änderungen in Azure bereitgestellt wurden, sollte Ihre Webanwendung weiterhin funktionieren, da sie nun die Verbindungszeichenfolge aus dem Eintrag in **App-Einstellungen** liest. Um dies zu überprüfen, ändern Sie den Wert für den Eintrag **STORAGE_KEY** unter **App-Einstellungen** in einen ungültigen Wert. Nachdem Sie diesen Wert gespeichert haben, sollte die Website aufgrund der ungültigen Einstellung für den Speicherzugriffsschlüssel nicht mehr funktionieren.

##Nächste Schritte

Die Schritte in diesem Artikel beschreiben zwar die Verwendung des Tabellendienstes zum Speichern von Informationen, Sie können jedoch auch MongoDB verwenden. Weitere Informationen finden Sie unter [Node.js Web Application with MongoDB].

##Zusätzliche Ressourcen

[Azure-Befehlszeilentools für Mac und Linux]
[Create and deploy a Node.js application to Azure Web Sites]: /de-de/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Publishing to Azure Web Sites with Git]: /de-de/documentation/articles/web-sites-publish-source-control/
[Azure Developer Center]: /de-de/develop/nodejs/


[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git remote]: http://git-scm.com/docs/git-remote

[Node.js Web Application with MongoDB]: /de-de/documentation/articles/web-sites-nodejs-store-data-mongodb/
[Azure command-line tool for Mac and Linux]: /de-de/documentation/articles/xplat-cli/

[Publishing to Azure Web Sites with Git]: /de-de/documentation/articles/web-sites-publish-source-control/
[azure]: https://github.com/Azure/azure-sdk-for-node


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

[Create and deploy a Node.js application to an Azure Web Site]: /de-de/documentation/articles/web-sites-nodejs-develop-deploy-mac/
<!--HONumber=42-->
