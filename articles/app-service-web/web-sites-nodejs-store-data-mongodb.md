<properties
	pageTitle="Erstellen einer Node.js-Web-App in Azure mit MongoDB auf einem virtuellen Computer"
	description="Verwenden von MongoDB zum Speichern von Daten in einer Node.js-Anwendung, die in Azure gehostet wird."
	tags="azure-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="mwasson"/>


# Erstellen einer Node.js-Web-App in Azure mit MongoDB auf einem virtuellen Computer

In diesem Lernprogramm erfahren Sie, wie Sie [MongoDB], das auf einem virtuellen Azure-Computer gehostet wird, zum Speichern von Daten verwenden, und wie Sie von einer [Node]-Anwendung aus, die in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps gehostet wird, auf die Daten zugreifen. [MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank.

Sie erhalten Informationen zu folgenden Themen:

* Einrichten eines virtuellen Computers mit Ubuntu und MongoDB aus dem VM Depot
* Zugreifen auf MongoDB über eine Node-Anwendung
* Verwenden der Azure-Befehlszeilenschnittstelle zum Erstellen einer Web-App in Azure App Service

In diesem Lernprogramm erstellen Sie eine einfache webbasierte Anwendung zur Aufgabenverwaltung, mit der Sie Aufgaben erstellen, abrufen und vervollständigen können. Die Aufgaben werden in MongoDB gespeichert.

> [AZURE.NOTE]In diesem Programm wird eine Instanz von MongoDB verwendet, die auf einem virtuellen Computer installiert ist. Wenn Sie lieber eine gehostete MongoDB-Instanz verwenden möchten, die von MongoLabs bereitgestellt wird, finden Sie weitere Informationen unter [Erstellen einer Node.js-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-Ons](store-mongolab-web-sites-nodejs-store-data-mongodb).

Die Projektdateien für dieses Lernprogramm werden in einem Verzeichnis namens **tasklist** gespeichert, und die fertige Anwendung sieht etwa wie folgt aus:

![Webseite mit einer leeren Aufgabenliste][node-mongo-finished]

> [AZURE.NOTE]Im Folgenden wird öfters die Verwendung der Befehlszeile verlangt. Verwenden Sie in diesen Fällen die Befehlszeile für Ihr Betriebssystem, zum Beispiel **Windows PowerShell** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die Terminal-Anwendung zugreifen.

##Voraussetzungen

Für die Schritte in diesem Lernprogramm, die Node.js verwenden, benötigen Sie eine aktuelle Version von [Node.js][node] in Ihrer Entwicklungsumgebung.

Zusätzlich muss [Git] über die Befehlszeile in Ihrer Entwicklungsumgebung verfügbar sein, da dies zur Bereitstellung der Anwendung auf einer Azure-Website verwendet wird.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Erstellen eines virtuellen Computers

<!--
After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

Sie können zwar einen neuen virtuellen Computer erstellen und dann anhand der [MongoDB-Installationsanleitungen][installguides] MongoDB darauf installieren, jedoch ist ein virtueller Computer, auf dem MongoDB vorinstalliert ist, im Azure Marketplace verfügbar. Die folgenden Schritte veranschaulichen, wie eine solche VM-Vorlage verwendet wird.

> [AZURE.NOTE]In dem in diesem Lernprogramm verwendeten Communityimage sind MongoDB-Daten auf dem Betriebssystemdatenträger gespeichert. Obwohl dies für die Zwecke des Lernprogramms ausreicht, erzielen Sie eine bessere Leistung, wenn MongoDB-Daten auf einem Datenträger gespeichert werden. Eine Anleitung zum Erstellen eines neuen virtuellen Computers (mit Datenträger) und Speichern von MongoDB-Daten auf dem Datenträger finden Sie unter [Install MongoDB on Linux on Azure][mongodbonazure] (Installieren von MongoDB unter Linux auf Azure, in englischer Sprache).

1. Melden Sie sich beim [Azure-Portal][azureportal] an.

3. Klicken Sie auf **Neu** > **Data + Storage** > **Marketplace**.

	![Screenshot zur Auswahl von VM Depot][selectdepo]

2. Geben Sie in das Suchfeld oben "mongodb" ein, und wählen Sie dann **MongoDB v2.2.3 on Hardened Ubuntu 12.04 LTS** aus. Klicken Sie auf **Erstellen**, um fortzufahren.

	![Screenshot des ausgewählten Image MongoDB v2.2.3 auf Hardened Ubuntu][selectedimage]

	Klicken Sie unten auf den Pfeil, um zum nächsten Bildschirm zu gelangen.

7. Konfigurieren Sie **Hostname**, Administrator **Benutzername** und **Kennwort** sowie **Ressourcengruppe** der VM. Klicken Sie dann auf **Optionale Konfiguration**.

	![Screenshot der VM-Konfiguration][vmconfig]

8. Geben Sie zusätzliche Endpunkte für den virtuellen Computer an. Da wir auf MongoDB zugreifen wollen, fügen Sie einen neuen Endpunkt mit den folgenden Informationen hinzu:

	* Name - MongoDB
	* Protokoll - TCP
	* Öffentlicher Port - 27017
	* Privater Port - 27017

	Um das MongoDB-Webportal zur Verfügung zu stellen, geben Sie einen weiteren Endpunkt mit den folgenden Informationen an:

	* Name - MongoDBWeb
	* Protokoll - TCP
	* Öffentlicher Port - 28017
	* Privater Port - 28017

	![Screenshot der Endpunktkonfiguration][vmendpoint]

9. Klicken Sie zweimal auf **OK**, und klicken Sie dann auf **Erstellen**, um den virtuellen Computer zu erstellen.

	Sobald der virtuelle Computer erstellt ist, wird er in Ihrem Startmenü angezeigt, und Sie können darauf klicken, um das Blatt des virtuellen Computers zu öffnen. Sie sollten in der Lage sein, einen Webbrowser für **http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/** zu öffnen, um zu bestätigen, dass MongoDB ausgeführt wird. Unten auf der Seite sollte ein Protokoll mit Informationen über den Dienst angezeigt werden, das in etwa wie folgt aussieht:

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	Falls das Protokoll Fehler anzeigt, lesen Sie die [MongoDB-Dokumentation][mongodocs] für Schritte zur Fehlerbehebung.


##Installieren der Module und Generieren des Gerüsts

In diesem Abschnitt erstellen Sie eine neue Node-Anwendung in Ihrer Entwicklungsumgebung und verwenden "npm" zum Hinzufügen von Modulpaketen. Für die tasklist-Anwendung verwenden Sie die Module [Express] und [Mongoose]. Das Express-Modul biete ein Modellansichts-Controller-Framework für Node, während Mongoose die Kommunikation mit MongoDB betreibt.

###Installieren von Express und Generieren des Gerüsts

1. Wechseln Sie an der Befehlszeile zum Verzeichnis **tasklist**. Falls das Verzeichnis **tasklist** nicht vorhanden ist, erstellen Sie es.

	> [AZURE.NOTE]In diesem Lernprogramm wird auf den Ordner **tasklist** Bezug genommen. Der vollständige Ordnerpfad wird nicht angegeben, da die Pfadsemantik je nach Betriebssystem unterschiedlich ist. Sie sollten diesen Ordner in einem Verzeichnis erstellen, auf das Sie im lokalen Dateisystem einfach zugreifen können, z. B. **~/node/tasklist** oder **c:\\node\\tasklist**.

2. Geben Sie folgenden Befehl ein, um den Befehl "express" zu installieren.

	npm install express-generator -g

	> [AZURE.NOTE]Bei Verwendung des Parameters '-g' bei einigen Betriebssystemen kann ein Fehler ___Error: EPERM, chmod '/usr/local/bin/express'___ zurückgegeben werden und die Aufforderung erfolgen, zu versuchen, das Konto als Administrator auszuführen. Verwenden Sie in diesem Fall den Befehl `sudo`, um npm mit einer höheren Berechtigungsstufe auszuführen.

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE]Wird der Parameter "-g" bei der Installation des Express-Moduls verwendet, so erfolgt die Installation global. Auf diese Weise können wir den Befehl ___express___ aufrufen, um das Web-App-Gerüst zu generieren, ohne zusätzliche Pfadinformationen angeben zu müssen.

4. Verwenden Sie den Befehl **express**, um das Gerüst zu erstellen, das für diese Anwendung verwendet wird:

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

3. Kopieren Sie die Datei **tasklist/bin/www** in eine Datei namens **server.js** im Ordner **tasklist**. App Service-Web-Apps erwarten, dass der Einstiegspunkt für eine Node.js-Anwendung entweder **server.js** oder **app.js** ist. Da **app.js** zwar bereits vorliegt, jedoch nicht der Einstiegspunkt ist, muss **server.js** verwendet werden.

4. Entfernen Sie in der Datei **server.js** eines der '.'-Zeichen aus der folgenden Zeile.

		var app = require('../app');

	Die geänderte Zeile sollte wie folgt aussehen.

		var app = require('./app');

	Dies ist erforderlich, da sich **server.js** (früher **bin/www**) nun im selben Ordner wie die erforderliche Datei **app.js** befindet.

###Installieren zusätzlicher Module

Die Datei **package.json** ist eine der durch den Befehl **express** erstellten Dateien. Diese Datei enthält eine Liste zusätzlicher Module, die für eine Express-Anwendung erforderlich sind. Wenn Sie später diese Anwendung in App Service-Web-Apps bereitstellen, wird anhand dieser Datei bestimmt, welche Module auf Azure installiert werden müssen, um Ihre Anwendung zu unterstützen.

1. Verwenden Sie im Ordner **tasklist** Folgendes, um die in der Datei **package.json** beschriebenen Module zu installieren:

        npm install

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		debug@0.7.4 node_modules\debug

		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0

		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1

		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)

		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)

		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)

	Damit werden alle Standardmodule installiert, die von einer Express-Anwendung verwendet werden.

2. Geben Sie anschließend den folgenden Befehl ein, um das Mongoose-Modul lokal zu installieren und einen Eintrag dafür in der Datei **package.json** zu speichern:

		npm install mongoose --save

	Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)

    > [AZURE.NOTE]Sie können alle Meldungen zur Installation des C++-bson-Parsers ignorieren.

##Verwenden von MongoDB in einer Node-Anwendung

In diesem Abschnitt erweitern Sie die durch den Befehl **express** erstellte Grundanwendung, indem Sie die Datei **task.js** hinzufügen, die das Modell für Ihre Aufgaben enthält. Außerdem ändern Sie die vorhandene Datei **app.js** und erstellen eine neue Controllerdatei **tasklist.js**, um das Modell zu verwenden.

### Erstellen des Modells

1. Erstellen Sie im Verzeichnis **tasklist** ein neues Verzeichnis namens **models**.

2. Erstellen Sie im Verzeichnis **models** eine neue Datei namens **task.js**. Diese Datei enthält das Modell für die von Ihrer Anwendung erstellten Aufgaben.

3. Fügen Sie am Anfang der Datei **task.js** den folgenden Code hinzu, um auf die erforderlichen Bibliotheken zu verweisen:

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. Anschließend fügen Sie Code ein, um das Modell zu definieren und zu exportieren. Dieses Modell wird für die Interaktionen mit der MongoDB-Datenbank verwendet.

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Speichern und schließen Sie die Datei **task.js**.

###Erstellen des Controllers

1. Erstellen Sie im Verzeichnis **tasklist/routes** eine neue Datei namens **tasklist.js**, und öffnen Sie sie in einem Texteditor.

2. Fügen Sie den folgenden Code zu **tasklist.js** hinzu. Dadurch werden das Mongoose-Modul und das in **task.js** definierte Aufgabenmodell geladen. Die TaskList-Funktion wird verwendet, um die Verbindung zum MongoDB-Server basierend auf dem Wert für **connection** zu erstellen:

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. Fügen Sie außerdem zur Datei **tasklist.js** die Methoden **showTasks**, **addTask** und **completeTasks** hinzu:

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

3. Speichern Sie die Datei **tasklist.js**.

### Ändern von app.js

1. Öffnen Sie im Verzeichnis **tasklist** die Datei **app.js** in einem Texteditor. Diese Datei wurde zuvor mit dem Befehl **express** erstellt.

2. Fügen Sie den folgenden Code am Anfang der Datei **app.js** hinzu. Damit wird **TaskList** mit der Verbindungszeichenfolge für den MongoDB-Server initialisiert:

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	Beachten Sie die zweite Zeile: Sie rufen eine Umgebungsvariable auf, die Sie später konfigurieren werden und die die Verbindungsinformationen für die Mongo-Instanz enthält. Falls Sie eine lokale Mongo-Instanz zu Entwicklungszwecken haben, können Sie diesen Wert temporär auf "localhost" statt auf "process.env.MONGODB_URI" setzen.

3. Suchen Sie folgende Zeilen:

		app.use('/', routes);
		app.use('/users', users);

	Ersetzen Sie die zwei obigen Zeilen durch Folgendes:

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	Dadurch werden die in **tasklist.js** definierten Funktionen als Routen hinzugefügt.

4. Speichern Sie die Datei **app.js**.

###Ändern der Indexansicht

1. Wechseln Sie zum Verzeichnis **views**, und öffnen Sie die Datei **index.jade** in einem Texteditor.

2. Ersetzen Sie den Inhalt der Datei **index.jade** durch den folgenden Code. Damit werden die Ansicht zur Anzeige vorhandener Aufgaben sowie ein Formular für das Hinzufügen neuer Aufgaben und das Markieren von vorhandenen Aufgaben als abgeschlossen definiert.

		h1= title
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

3. Speichern und schließen Sie die Datei **index.jade**.

<!-- ##Run your application locally

To test the application on your local machine, perform the following steps:

1. From the command-line, change directories to the **tasklist** directory.

2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.

	On a Windows system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	On an OS X or Linux-based system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.

2. Use the following command to launch the application locally:

        node app.js

3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. The page should update to display the item in the ToDo List table.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.

7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

##Bereitstellen der Anwendung in Azure

Die Schritte in diesem Abschnitt verwenden die Azure-Befehlszeilentools zum Erstellen einer neuen Web-App in Azure App Service und verwenden dann Git zum Bereitstellen der Anwendung. Zur Durchführung dieser Schritte benötigen Sie ein Azure-Abonnement.

> [AZURE.NOTE]Diese Schritte können auch über das Azure-Portal ausgeführt werden. Anleitungen zur Verwendung des Azure-Portals zum Bereitstellen einer Node.js-Anwendung finden Sie unter [Erstellen und Bereitstellen einer Node.js-Web-App in Azure App Service](web-sites-nodejs-develop-deploy-mac.md).

> [AZURE.NOTE]Falls dies die erste von Ihnen erstellte App Service-Web-App ist, müssen Sie das Azure-Portal verwenden, um die Anwendung bereitzustellen.

###Installieren der Azure-Befehlszeilenschnittstelle (Azure-CLI)

Mit der Azure-Befehlszeilenschnittstelle können Sie Verwaltungsvorgänge für Azure-Dienste ausführen. Falls Sie die Azure-Befehlszeilenschnittstelle nicht bereits in Ihrer Entwicklungsumgebung installiert und konfiguriert haben, finden Sie weitere Informationen unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).

###Erstellen einer App-Service-Web-App

1. Wechseln Sie an der Befehlszeile zum Verzeichnis **tasklist**.

2. Verwenden Sie den folgenden Befehl, um eine neue App Service-Web-App zu erstellen. Ersetzen Sie 'meineindeutigerappname' durch den eindeutigen Namen für Ihre Web-App. Dieser Wert wird als Teil der URL für die resultierende Web-App verwendet.

		azure site create myuniqueappname --git

	Sie werden aufgefordert, das Datencenter anzugeben, in dem die Web-App gespeichert werden soll. Wählen Sie ein Datencenter in geografischer Nähe zu Ihrem Standort aus.

	Der Parameter `--git` erstellt lokal im Ordner **tasklist** ein Git-Repository, falls keines vorhanden ist. Außerdem erstellen Sie ein [Git remote] namens "azure", das zum Veröffentlichen der Anwendung in Azure verwendet wird. Daraus wird ein [iisnode.yml] erstellt, das Einstellungen enthält, die von Azure zum Hosting von Node-Anwendungen verwendet werden. Abschließend wird die Datei ".gitignore" erstellt, um zu verhindern, dass der Ordner "node-modules" auf .git veröffentlicht wird.

	> [AZURE.NOTE]Wenn dieser Befehl aus einem Verzeichnis ausgeführt wird, das bereits ein Git-Repository enthält, wird das Verzeichnis nicht erneut initialisiert.

	> [AZURE.NOTE]Wenn der Parameter "--git" weggelassen wird, das Verzeichnis jedoch ein Git-Repository enthält, wird "azure" remote trotzdem erstellt.

	Nach Abschluss dieses Befehls wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass die Zeile, die mit **Created website at** beginnt, die URL für die App Service-Web-App enthält.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [AZURE.NOTE> Falls dies die erste App Service-Web-App für Ihr Abonnement ist, werden Sie angewiesen, das Portal zum Erstellen der Web-App zu verwenden. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Web-App in Azure App Service](web-sites-nodejs-develop-deploy-mac.md).

###Festlegen der Umgebungsvariable MONGODB_URL

Die Anwendung erwartet, dass die Verbindungszeichenfolge für die MongoDB-Instanz in der MONGODB_URI-Umgebungsvariable verfügbar ist. Verwenden Sie den folgenden Befehl, um diesen Wert für die Web-App festzulegen:

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Damit wird eine neue Anwendungseinstellung für die Web-App erstellt, die verwendet wird, um die von der Web-App gelesene MONGODB_URI-Umgebungsvariable auszufüllen. Ersetzen Sie den Wert von "mymongodb.cloudapp.net" durch den Namen des virtuellen Computers, auf dem MongoDB installiert wurde.

###Veröffentlichen der Anwendung

1. Wechseln Sie im Terminalfenster zum Verzeichnis **tasklist**, falls Sie sich nicht bereits dort befinden.

2. Verwenden Sie die folgenden Befehle, um Dateien hinzuzufügen und anschließend in das lokale Git-Repository zu übernehmen:

		git add .
		git commit -m "adding files"

3. Wenn Sie die letzten Änderungen am Git-Repository per Push an die App Service-Web-App übertragen, müssen Sie angeben, dass die Zielstruktur **master** lautet, da diese für den Web-App-Inhalt verwendet wird.

		git push azure master

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

4. Sobald der Push-Vorgang abgeschlossen ist, navigieren Sie mit dem Befehl `azure site browse` zu der Web-App, um Ihre Anwendung anzuzeigen.

##Nächste Schritte

Die Schritte in diesem Artikel beschreiben zwar die Verwendung von MongoDB zum Speichern von Informationen, Sie können jedoch auch den Azure-Tabellendienst verwenden. Weitere Informationen dazu finden Sie unter [Node.js-Web-App mit dem Azure-Tabellendienst].

Wenn Sie erfahren möchten, wie Sie eine von MongoLabs bereitgestellte gehostete MongoDB-Instanz verwenden, finden Sie weitere Informationen unter [Erstellen einer Node.js-Web-App auf Azure mit MongoDB mithilfe des MongoLab-Add-Ons](store-mongolab-web-sites-nodejs-store-data-mongodb.md).

Informationen zur Sicherheit von MongoDB finden Sie unter [MongoDB-Sicherheit][mongosecurity].

##Zusätzliche Ressourcen

[Azure-Befehlszeilentool für Mac und Linux][Build and deploy a Node.js web app in Azure App Service] [Kontinuierliche Bereitstellung mit GIT in Azure App Service]

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Build and deploy a Node.js web app in Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[Kontinuierliche Bereitstellung mit GIT in Azure App Service]: web-sites-publish-source-control.md
[Node.js-Web-App mit dem Azure-Tabellendienst]: storage-nodejs-use-table-storage-web-site.md
[node-mongo-finished]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/web-sites-nodejs-store-data-mongodb/todo_add_item.png
[node-mongo-list-items]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://portal.azure.com
[mongodocs]: http://docs.mongodb.org/manual/
[Azure CLI]: ../xplat-cli.md

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->