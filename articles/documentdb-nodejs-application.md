<properties 
	pageTitle="Erstellen einer Node.js-Webanwendung mit DocumentDB | Azure" 
	description="Erfahren Sie, wie Sie mit Microsoft Azure DocumentDB Daten aus einer in Azure-Websites gehosteten Node.js Express-Webanwendung speichern und auf sie zugreifen." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="03/20/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395783175"></a>Erstellen einer Node.js-Webanwendung mithilfe von DocumentDB

In diesem Lernprogramm erfahren Sie, wie Sie den Azure DocumentDB-Dienst verwenden, um Daten aus einer in Azure-Websites gehosteten Node.js-Express-Anwendung zu speichern und abzurufen.

Für den Beginn empfiehlt sich folgendes Video, in dem Andrew Liu zeigt, wie Sie ein Azure DocumentDB-Datenbankkonto bereitstellen und JSON-Dokumente in Ihrer Node.js-Anwendung speichern. 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

Kehren Sie dann zurück zu diesem Artikel, in dem Sie die Antworten auf die folgenden Fragen erfahren:

- Wie arbeite ich mit DocumentDB mithilfe des Moduls "documentdb npm"?
- Wie kann ich die Webanwendung in Azure-Websites bereitstellen?

In diesem Lernprogramm erstellen Sie eine einfache webbasierte
Anwendung zur Aufgabenverwaltung, mit der Sie Aufgaben erstellen, abrufen
und durchführen können. Die Aufgaben werden als JSON-Dokumente in Azure
DocumentDB gespeichert.

![Screen shot of the My Todo List application created in this tutorial](./media/documentdb-nodejs-application/image1.png)

Sie haben keine Zeit, um das Lernprogramm abzuschließen, und möchten nur die vollständige Lösung von GitHub abrufen? Kein Problem. Die Lösung erhalten Sie [hier](https://github.com/Azure/azure-documentdb-node/tree/master/tutorial/todo).

## <a name="_Toc395783176"></a>Voraussetzungen

> [AZURE.TIP] In diesem Lernprogramm wird davon ausgegangen, dass Sie bereits Erfahrung mit Node.js und Azure-Websites haben.

Bevor Sie die Anweisungen in diesem Artikel befolgen, sollten Sie sicherstellen,
dass Sie über Folgendes verfügen:

- Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](../../pricing/free-trial/).
- [Node.js][]-Version 0.10.29 oder höher.
- [Express Generator](http://www.expressjs.com/starter/generator.html) (Installation über `npm install express-generator -g`)
- [Git][].

## <a name="_Toc395637761"></a>Schritt 1: Erstellen eines DocumentDB-Datenbankkontos

Erstellen Sie zunächst ein DocumentDB-Konto. Wenn Sie bereits ein Konto erstellt haben, können Sie zu [Schritt 2: Erstellen einer neuen Node.js-Anwendung](#_Toc395783178).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Schritt 2: Erstellen einer neuen Node.js-Anwendung

Jetzt erstellen Sie ein einfaches "Hello World"-Node.js-Projekt mithilfe des [Express](http://expressjs.com/)-Frameworks.

1. Öffnen Sie den bevorzugten Terminaldienst.

2. Verwenden Sie den Express Generator, um eine neue Anwendung namens **todo** zu erstellen.

		express todo

3. Öffnen Sie das neue **todo**-Verzeichnis, und installieren Sie alle Abhängigkeiten.

		cd todo
		npm install

4. Führen Sie die neue Anwendung aus.

		npm start

5. Sie können die neue Anwendung anzeigen, indem Sie in Ihrem Browser zu [http://localhost:3000](http://localhost:3000) navigieren.

	![Screenshot of the Hello World application in a browser window](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>Schritt 3: Installieren zusätzlicher Module

Die Datei **package.json** ist eine der im Stammverzeichnis des Projekts erstellten Dateien
. Diese Datei enthält eine Liste zusätzlicher Module, die für
Ihre Node.js-Anwendung erforderlich sind. Wenn Sie später diese
Anwendung auf einer Azure-Website bereitstellen, wird anhand dieser Datei bestimmt,
welche Module auf Azure installiert werden müssen, um Ihre Anwendung zu unterstützen. Für dieses Lernprogramm müssen zwei weitere Pakete installiert werden.

1. Wechseln Sie zum Terminal, und installieren Sie das **async**-Modul über npm.

		npm install async --save

1. Installieren Sie das **documentdb**-Modul über npm. Dieses Modul ist für die DocumentDB-Funktionen zuständig.

		npm install documentdb --save

3. Bei einer schnellen Überprüfung der Datei **package.json** der Anwendung können Sie die zusätzlichen Module anzeigen. Diese Datei weist Azure beim Ausführen der Anwendung an, welche Pakete heruntergeladen und installiert werden sollen. Sie sollte dem unten stehenden Beispiel ähneln.

	![Screenshot of the package.json tab](./media/documentdb-nodejs-application/image17.png)

       Dadurch wird Node (und später Azure) darüber informiert, dass Ihre Anwendung von diesen zusätzlichen Modulen abhängig ist.

## <a name="_Toc395783180"></a>Schritt 4: Verwenden des DocumentDB-Diensts in einer Node-Anwendung

Dies ist für die anfängliche Einrichtung und Konfiguration erforderlich. Jetzt wollen wir zur eigentlichen Aufgabe übergehen, d. h. Code mithilfe von Azure DocumentDB erstellen.

### Erstellen des Modells

1. Erstellen Sie im Projektverzeichnis ein neues Verzeichnis namens **models**.
2. Erstellen Sie im Verzeichnis **models** eine neue Datei namens **taskDao.js**. Diese Datei enthält das Modell für die von Ihrer Anwendung erstellten Aufgaben.
3. Erstellen Sie im gleichen Verzeichnis **models** eine weitere neue Datei namens **docdbUtils.js**. Diese Datei enthält einigen nützlichen, wiederverwendbaren Code, den wir in unserer Anwendung verwenden werden. 
4. Kopieren Sie den folgenden Code in **docdbUtils.js**

		var DocumentDBClient = require('documentdb').DocumentClient;
			
		var DocDBUtils = {
		    getOrCreateDatabase: function (client, databaseId, callback) {
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: databaseId
		            }]
		        };
		
		        client.queryDatabases(querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                if (results.length === 0) {
		                    var databaseSpec = {
		                        id: databaseId
		                    };
		
		                    client.createDatabase(databaseSpec, function (err, created) {
		                        callback(null, created);
		                    });
		
		                } else {
		                    callback(null, results[0]);
		                }
		            }
		        });
		    },
		
		    getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: collectionId
		            }]
		        };
		        
		        client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {		
		                if (results.length === 0) {
		                    var collectionSpec = {
		                        id: collectionId
		                    };
		
		                    client.createCollection(databaseLink, collectionSpec, function (err, created) {
		                        callback(null, created);
		                    });
		
		                } else {
		                    callback(null, results[0]);
		                }
		            }
		        });
		    }
		};
				
		module.exports = DocDBUtils;
		
3. Speichern und schließen Sie die Datei **docdbUtils.js**.

4. Fügen Sie am Anfang der Datei **taskDao.js** folgenden Code ein, um auf die oben erstellten **DocumentDBClient** und **docdbUtils.js** zu verweisen:

        var DocumentDBClient = require('documentdb').DocumentClient;
		var docdbUtils = require('./docdbUtils');

4. Anschließend fügen Sie Code hinzu, um das Task-Objekt zu definieren und zu exportieren. Dieser ist verantwortlich für das Initialisieren des Task-Objekts und für das Einrichten der Datenbank und der Dokumentenauflistung, die wir verwendet werden.

		function TaskDao(documentDBClient, databaseId, collectionId) {
		  this.client = documentDBClient;
		  this.databaseId = databaseId;
		  this.collectionId = collectionId;
		
		  this.database = null;
		  this.collection = null;
		}
		
		module.exports = TaskDao;

5. Fügen Sie als Nächstes den folgenden Code hinzu, um zusätzliche Methoden für das Task-Objekt zu definieren, die Interaktionen mit in DocumentDB gespeicherten Daten ermöglichen.

		TaskDao.prototype = {
		    init: function (callback) {
		        var self = this;
		
		        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
		            if (err) {
		                callback(err);

		            } else {
		                self.database = db;
		                docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
		                    if (err) {
		                        callback(err);
		
		                    } else {
		                        self.collection = coll;
		                    }
		                });
		            }
		        });
		    },
		
		    find: function (querySpec, callback) {
		        var self = this;
		
		        self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, results);
		            }
		        });
		    },
		
		    addItem: function (item, callback) {
		        var self = this;
		
		        item.date = Date.now();
		        item.completed = false;
		
		        self.client.createDocument(self.collection._self, item, function (err, doc) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, doc);
		            }
		        });
		    },
		
		    updateItem: function (itemId, callback) {
		        var self = this;
		
		        self.getItem(itemId, function (err, doc) {
		            if (err) {
		                callback(err);
		
		            } else {
		                doc.completed = true;
		
		                self.client.replaceDocument(doc._self, doc, function (err, replaced) {
		                    if (err) {
		                        callback(err);
		
		                    } else {
		                        callback(null, replaced);
		                    }
		                });
		            }
		        });
		    },
		
		    getItem: function (itemId, callback) {
		        var self = this;
		
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: itemId
		            }]
		        };
		
		        self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, results[0]);
		            }
		        });
		    }
		};

6. Speichern und schließen Sie die Datei **taskDao.js**. 

### Erstellen des Controllers

1. Erstellen Sie im Verzeichnis **routes** des Projekts eine neue Datei namens **tasklist.js**. 
2. Fügen Sie **tasklist.js** den folgenden Code hinzu. Hierdurch werden die Module "DocumentDBClient" und "async" geladen, die von **tasklist.js** verwendet werden. Darüber hinaus wird hierdurch die Funktion **TaskList** definiert, an die eine Instanz des zuvor definierten **Task**-Objekts übergeben wird:

		var DocumentDBClient = require('documentdb').DocumentClient;
		var async = require('async');
		
		function TaskList(taskDao) {
		  this.taskDao = taskDao;
		}
		
		module.exports = TaskList;

3. Fügen Sie außerdem zur Datei **tasklist.js** die Methoden **showTasks, addTask** und **completeTasks** hinzu:
		
		TaskList.prototype = {
		    showTasks: function (req, res) {
		        var self = this;
		
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.completed=@completed',
		            parameters: [{
		                name: '@completed',
		                value: false
		            }]
		        };
		
		        self.taskDao.find(querySpec, function (err, items) {
		            if (err) {
		                throw (err);
		            }
		
		            res.render('index', {
		                title: 'My ToDo List ',
		                tasks: items
		            });
		        });
		    },
		
		    addTask: function (req, res) {
		        var self = this;
		        var item = req.body;
		
		        self.taskDao.addItem(item, function (err) {
		            if (err) {
		                throw (err);
		            }
		
		            res.redirect('/');
		        });
		    },
		
		    completeTask: function (req, res) {
		        var self = this;
		        var completedTasks = Object.keys(req.body);
		
		        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		            self.taskDao.updateItem(completedTask, function (err) {
		                if (err) {
		                    callback(err);
		                } else {
		                    callback(null);
		                }
		            });
		        }, function goHome(err) {
		            if (err) {
		                throw err;
		            } else {
		                res.redirect('/');
		            }
		        });
		    }
		};


4. Speichern und schließen Sie die Datei **tasklist.js**.
 
### Hinzufügen von config.json

1. Erstellen Sie im Projektverzeichnis eine neue Datei namens **config.js**.
2. Fügen Sie **config.json** Folgendes hinzu. Dadurch werden die für Ihre Anwendung erforderlichen Konfigurationseinstellungen und Werte definiert.

		var config = {}
		
		config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.databaseId = "ToDoList";
		config.collectionId = "Items";
		
		module.exports = config;

3. Aktualisieren Sie in der Datei **config.js** die Werte für HOST und AUTH_KEY unter Verwendung der Werte im Blatt "Schlüssel" Ihres DocumentDB-Kontos im [Azure-Vorschauportal](http://portal.azure.com):

4. Speichern und schließen Sie die Datei **config.js**.
 
### Ändern von app.js

1. Öffnen Sie im Projektverzeichnis die Datei **app.js**. Diese Datei wurde bereits während der Erstellung der Express-Webanwendung erstellt.
2. Fügen Sie am Anfang von **app.js** den folgenden Code ein:
	
		var DocumentDBClient = require('documentdb').DocumentClient;
		var config = require('./config');
		var TaskList = require('./routes/tasklist');
		var TaskDao = require('./models/taskDao');

3. Dieser Code definiert, welche Config-Datei verwendet wird, und liest dann Werten aus dieser Datei in einige Variablen ein, die wir später verwenden werden.
4. Ersetzen Sie die folgenden beiden Zeilen in der Datei **app.js**:

		app.use('/', routes);
		app.use('/users', users); 

      durch den folgenden Codeausschnitt:

		var docDbClient = new DocumentDBClient(config.host, {
		    masterKey: config.authKey
		});
		var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
		var taskList = new TaskList(taskDao);
		taskDao.init();
		
		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));


6. Diese Zeilen definieren eine neue Instanz unseres **TaskDao**-Objekts mit einer neuen Verbindung zu DocumentDB (unter Verwendung der aus **config.js** gelesenen Werte). Sie initialisieren das Task-Objekt und binden dann Formularaktionen an Methoden auf unserem **TaskList**-Controller. 

7. Speichern und schließen Sie abschließend die Datei **app.js**. Wir sind fast fertig.
 
## <a name="_Toc395783181"></a>Schritt 5: Erstellen einer Benutzeroberfläche

Jetzt konzentrieren wir uns auf die Erstellung der Benutzeroberfläche, um den Benutzern die eigentliche Interaktion mit unserer Anwendung zu ermöglichen. Die von uns erstellte Express-Anwendung verwendet **Jade** als Ansichtsmodul. Weitere Informationen zu Jade finden Sie unter [http://jade-lang.com/](http://jade-lang.com/).

1. Die Datei **layout.jade** im Verzeichnis **Ansichten** dient als globale Vorlage für andere **.jade**-Dateien. In diesem Schritt werden Sie sie modifizieren, um [Twitter Bootstrap](https://github.com/twbs/bootstrap) zu verwenden, ein Toolkit zum einfachen Gestalten ansprechender Websites. 
2. Öffnen Sie die Datei **layout.jade**, die sich im Ordner **views** befindet, und ersetzen Sie die Inhalte durch folgenden Code:
	
		doctype html
		html
		  head
		    title= title
		    link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		  body
		    nav.navbar.navbar-inverse.navbar-fixed-top
		      div.navbar-header
		        a.navbar-brand(href='#') My Tasks
		    block content
		    script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
		    script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')



	Dadurch wird dem **Jade**-Modul mitgeteilt, dass für unsere Anwendung einige HTML-Elemente dargestellt werden müssen. Das Modul erstellt dann einen **Block** mit der Bezeichnung **content**, in dem wir das Layout für unsere Inhaltsseiten bereitstellen können.
	Speichern und schließen Sie die Datei **layout.jade**.

4. Öffnen Sie nun die Datei **index.jade**, d. h. die Ansicht, die von der Anwendung verwendet wird, und ersetzen Sie den Inhalt der Datei durch Folgendes:

		extends layout
		
		block content
		  h1 #{title}
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
		            td #{task.name}
		            td #{task.category}
		            - var date  = new Date(task.date);
		            - var day   = date.getDate();
		            - var month = date.getMonth() + 1;
		            - var year  = date.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="name", type="textbox")
		    label Item Category:
		    input(name="category", type="textbox")
		    br
		    button.btn(type="submit") Add item

	Dadurch wird das Layout erweitert und Inhalt für den Platzhalter **content** bereitgestellt, den wir zuvor in der Datei **layout.jade** gesehen haben.
	
	In diesem Layout haben wir zwei HTML-Formulare erstellt. 
	Das erste Formular enthält eine Tabelle für unsere Daten sowie eine Schaltfläche, mit der die Elemente durch Übermittlung an die **/completetask**-Methode des Controllers aktualisiert werden können.
	Das zweite Formular enthält zwei Eingabefelder und eine Schaltfläche, mit der Sie ein neues Element durch Übermittlung an die **/addtask**-Methode des Controllers erstellen können.
	
	Dies sollte jetzt alles sein, damit unsere Anwendung funktioniert.

5. Öffnen Sie die Datei **style.css** im Verzeichnis **Public\stylesheets**, und ersetzen Sie den Code durch Folgendes:

		body {
		  padding: 50px;
		  font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
		}
		a {
		  color: #00B7FF;
		}
		.well label {
		  display: block;
		}
		.well input {
		  margin-bottom: 5px;
		}
		.btn {
		  margin-top: 5px;
		  border: outset 1px #C8C8C8;
		}

	Speichern und schließen Sie die Datei **style.css**.

## <a name="_Toc395783181"></a>Schritt 6: Lokales Ausführen der Anwendung

1. Führen Sie zum Testen der Anwendung auf dem lokalen Computer `npm start` in einem Terminal aus, um die Anwendung zu starten, und öffnen Sie einen Browser mit einer Seite, die wie die folgende Abbildung aussieht:

	![Screenshot of the MyTodo List application in a browser window](./media/documentdb-nodejs-application/image18.png)


2. Füllen Sie die Felder für "Element", "Elementname" und "Kategorie" aus, um
Informationen einzugeben, und klicken Sie dann auf **Element hinzufügen**.

3. Die Seite sollte nun das neu erstellte Element in der Todo-Liste
hinzufügen.

	![Screenshot of the application with a new item in the ToDo list](./media/documentdb-nodejs-application/image19.png)

4. Aktivieren Sie zum Abschließen einer Aufgabe das Kontrollkästchen in der entsprechenden Spalte,
und klicken Sie dann auf **Aufgaben aktualisieren**.

## <a name="_Toc395783182"></a>Schritt 7: Bereitstellen der Anwendung auf Azure-Websites

1. Falls noch nicht geschehen, aktivieren Sie ein Git-Repository für Ihre Azure-Website. Anweisungen dazu finden Sie [hier](web-sites-publish-source-control-git.md#step4).

2. Fügen Sie Ihre Azure-Website als "Git Remote" hinzu.

		git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. Stellen Sie sie mittels Push auf Remote bereit.

		git push azure master

4. Git schließt die Veröffentlichung Ihrer Webanwendung in wenigen Sekunden ab
und startet einen Browser, in dem das Ergebnis Ihrer Arbeit
in Azure ausgeführt wird!

## <a name="_Toc395637775"></a>Nächste Schritte

Glückwunsch! Sie haben soeben Ihre erste Node.js Express-Webanwendung
mit Azure DocumentDB erstellt und auf Azure-Websites veröffentlicht.

Den Quellcode für die vollständige Referenzanwendung können Sie [hier](https://github.com/Azure/azure-documentdb-node/tree/master/tutorial/todo) herunterladen.

  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Azure-Verwaltungsportal]: http://portal.azure.com

<!--HONumber=49-->