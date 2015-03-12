<properties 
	pageTitle="Webanwendung mit Tabellenspeicher (Node.js) | Microsoft Azure" 
	description="Ein Lernprogramm, das auf der Web-App mit dem Express-Lernprogramm basiert, und Azure-Speicherdienste sowie das Azure-Modul hinzufügt." 
	services="cloud-services, storage" 
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






# Node.js-Webanwendung mit Speicher

In diesem Lernprogramm erweitern Sie die Anwendung aus dem Lernprogramm [Node.js Web Application using Express], indem Sie die Microsoft Azure-Clientbibliotheken für Node.js für die Arbeit mit Datenverwaltungsdiensten verwenden. Sie werden Ihre Anwendung erweitern und eine webbasierte Anwendung mit Aufgabenlisten erstellen, die Sie in Azure bereitstellen können. Mit Aufgabenlisten können Benutzer Aufgaben abrufen, neue Aufgaben erstellen und Aufgaben als abgeschlossen markieren.

Die Aufgaben werden im Azure-Speicher gespeichert. Der Azure-Speicher bietet einen unstrukturierten Datenspeicher, der gleichzeitig fehlertolerant und hochverfügbar ist. Der Azure-Speicher enthält verschiedene Datenstrukturen für die Speicherung und den Zugriff von Daten, und Sie können die Speicherdienste aus den APIs im Azure SDK für Node.js oder aus den REST-APIs nutzen. Weitere Informationen finden Sie unter [Speichern und Zugreifen auf Daten in Azure].

In diesem Lernprogramm wird davon ausgegangen, dass Sie die [Node.js Web Application] und [Node.js with Express][Node.js Web Application using Express]-Lernprogramme bereits durchlaufen haben.

Sie erhalten Informationen zu folgenden Themen:

-   Arbeiten mit der Jade Template Engine
-   Arbeiten mit den Azure-Datenverwaltungsdiensten

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![The completed web page in internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Einstellen der Speicher-Anmeldeinformationen in Web.Config

Sie müssen die Speicher-Anmeldeinformationen übergeben, um auf den Azure-Speicher zugreifen zu können. Verwenden Sie dazu die Anwendungseinstellungen in der Datei web.config.
Diese Einstellungen werden als Umgebungsvariablen an Node übergeben und anschließend vom Azure SDK gelesen.

> [AZURE.NOTE] Die Speicher-Anmeldeinformationen werden nur verwendet, wenn die Anwendung in Azure bereitgestellt wird. Bei der Ausführung im Emulator verwendet die Anwendung den Speicheremulator.

Führen Sie die folgenden Schritte aus, um die Anmeldeinformationen des Speicherkontos abzurufen und zu den web.config-Einstellungen hinzuzufügen:

1.  Wenn es nicht bereits geöffnet ist, starten Sie Azure PowerShell aus dem **Start**-Menü, indem Sie **Alle Programme, Azure** erweitern, **Azure PowerShell** per Rechtsklick öffnen, und dann **Als Administrator ausführen** auswählen.

2.  Wechseln Sie in den Ordner, der Ihre Anwendung enthält. Zum Beispiel C:\\node\\tasklist\\WebRole1.

3.  Geben Sie im Azure PowerShell-Fenster das folgende Cmdlet ein, um die Anmeldeinformationen für das Speicherkonto abzurufen:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

	Dieser Befehl ruft eine Liste der Speicherkonten und Kontoschlüssel Ihres gehosteten Dienstes ab.

	> [AZURE.NOTE] Da das Azure SDK beim Bereitstellen eines Dienstes ein Speicherkonto erstellt, sollten Sie bereits von den Bereitstellungen Ihrer Anwendung aus den vorherigen Lernprogrammen ein Speicherkonto besitzen.

4.  Öffnen Sie die Datei **ServiceDefinition.csdef** mit den Umgebungseinstellungen, die bei der Bereitstellung der Anwendung in Azure verwendet werden:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Fügen Sie den folgenden Block unter dem Element **Umgebung** ein, und ersetzen Sie {STORAGE ACCOUNT} und {STORAGE ACCESS KEY} durch den Kontonamen und den Primärschlüssel für das Speicherkonto, das für die Bereitstellung verwendet werden soll:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

	![The web.cloud.config file contents](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Speichern Sie die Datei und schließen Sie Notepad.

###Installieren zusätzlicher Module

2. Verwenden Sie den folgenden Befehl zum lokalen Installieren der [azure], [node-uuid], [nconf] and [async]-Module, und um für diese einen Eintrag zur **package.json**-Datei zu speichern:

	    PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

	Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.9 node_modules\nconf
		├── ini@1.1.0
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##Verwenden des Tabellendiensts in einer Node-Anwendung

In diesem Abschnitt erweitern Sie die durch den Befehl **Express** erstellte Grundanwendung, indem Sie die Datei **task.js** hinzufügen, die das Modell für Ihre Aufgaben enthält. Außerdem ändern Sie die vorhandene **app.js**, und erstellen Sie eine neue **tasklist.js**-Datei, die das Modell verwendet.

### Erstellen des Modells

1. Erstellen Sie ein neues Verzeichnis namens **Modelle** im Verzeichnis **WebRole1**.

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
		    self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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

1. Im Verzeichnis **WebRole1/routes** erstellen Sie eine neue Datei namens **tasklist.js**, und öffnen Sie diese in einem Text-Editor.

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
		    var query = azure.TableQuery()
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

1. Öffnen Sie im Verzeichnis **WebRole1** die Datei **app.js** in einem Text-Editor. 

2. Fügen Sie am Anfang der Datei Folgendes zum Laden des Azure-Moduls hinzu, und legen Sie den Tabellennamen und Partitionsschlüssel fest:

		var azure = require('azure-storage');
		var tableName = 'tasks';
		var partitionKey = 'hometasks';

3. Blättern Sie in der Datei "app.js" nach unten, bis Sie die folgende Zeile sehen:

		app.use('/', routes);
		app.use('/users', users);

	Ersetzen Sie die vorstehenden Zeilen durch den nachfolgenden Code. Hierdurch wird eine Instanz von <strong>Task</strong> mit einer Verbindung zu Ihrem Speicherkonto initialisiert. Diese wird an die <strong>TaskList</strong> übergeben, die darüber mit dem Tabellendienst kommuniziert:

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(), tableName, partitionKey);
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
		      if tasks != []
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

### Ausführen der Anwendung im Emulator

Verwenden Sie den folgenden Befehl, um die Anwendung im Emulator zu starten.

	PS C:\node\tasklist\WebRole1> start-azureemulator -launch

Der Browser wird geöffnet und zeigt die folgende Seite an:

![A web paged titled My Task List with a table containing tasks and fields to add a new task.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Verwenden Sie das Formular zum Hinzufügen von Elementen, oder entfernen Sie vorhandene Elemente, indem Sie sie als abgeschlossen markieren.

## Veröffentlichen der Anwendung in Azure


Rufen Sie im Windows PowerShell-Fenster das folgende Cmdlet auf, um Ihren gehosteten Dienst erneut auf Azure zu veröffentlichen.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Ersetzen Sie **myuniquename** durch einen eindeutigen Namen für diese Anwendung. Ersetzen Sie **datacentername** durch den Namen eines Azure-Rechenzentrums wie **West US**.

Nach Abschluss der Bereitstellung sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

	PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
	WARNING: Publishing tasklist to Windows Azure. This may take several minutes...
	WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
	WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
	WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
	WARNING: 2:19:01 PM - Connecting...
	WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
	WARNING: 2:19:40 PM - Upgrading...
	WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
	WARNING: 2:22:48 PM - Initializing...
	WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
	WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Da Sie wie auch zuvor die Option **-launch** angegeben haben, öffnet sich der Browser mit Ihrer Anwendung in Azure nach Abschluss der Veröffentlichung.

![A browser window displaying the My Task List page. The URL indicates the page is now being hosted on Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Anhalten und Löschen Ihrer Anwendung

Nach der Bereitstellung der Anwendung empfiehlt es sich, diese zu deaktivieren, damit Sie andere Anwendungen im Rahmen der kostenlosen Serverzeit erstellen und bereitstellen können.

Azure berechnet Webrolleninstanzen pro Stunde verbrauchter Serverzeit.
Die Serverzeit wird verbraucht, sobald Ihre Anwendung bereitgestellt wurde, selbst wenn die Instanzen nicht ausgeführt werden und gestoppt wurden.

In den folgenden Schritten erfahren Sie, wie Sie die Anwendung beenden und löschen.

1.  Stoppen Sie im Windows PowerShell-Fenster die im vorherigen Abschnitt erstellte Dienstbereitstellung mit folgendem Cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

	Das Stoppen des Diensts kann einige Minuten dauern. Wenn der Dienst gestoppt wurde, erhalten Sie eine entsprechende Nachricht.

2.  Um den Dienst zu löschen, rufen Sie folgendes Cmdlet auf:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

	Geben Sie nach entsprechender Aufforderung **Y** ein, um den Dienst zu löschen.

	Das Löschen des Diensts kann einige Minuten dauern. Nachdem der Dienst gelöscht wurde, erhalten Sie eine entsprechende Nachricht.

  [Node.js Web Application using Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Node.js Web Application]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
<!--HONumber=42-->
