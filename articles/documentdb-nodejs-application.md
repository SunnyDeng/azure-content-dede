<properties title="Build a Node.js web application using DocumentDB" pageTitle="Build a Node.js web application using DocumentDB | Azure" description="Learn how to use Azure DocumentDB to store and access data from a Node.js application hosted on Azure." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="ryancraw"></tags>

# <a name="_Toc395783175">Erstellen einer Node.js-Webanwendung mithilfe von DocumentDB</a>

<a name="_Toc395783175">

In diesem Lernprogramm erfahren Sie, wie Sie den Azure DocumentDB-Dienst verwenden, um Daten von einer auf Azure-Websites gehosteten
Node.js-Express-Anwendung zu speichern und abzurufen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie einige Erfahrung mit der Verwendung von Node.js haben.

Sie erhalten Informationen zu folgenden Themen:

Verwenden der Node.js-Tools für Visual Studio

Arbeiten mit dem Azure DocumentDB-Dienst mit dem documentdb npm-Modul

Bereitstellen der Webanwendung auf Azure-Websites

In diesem Lernprogramm erstellen Sie eine einfache webbasierte
Anwendung zur Aufgabenverwaltung, mit der Sie Aufgaben erstellen,
abrufen und vervollständigen können. Die Aufgaben werden als JSON-Dokumente in Azure
DocumentDB gespeichert.

![Alt text][]

## <a name="_Toc395783176">Voraussetzungen</a>

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen,
dass Folgendes installiert ist:

[Node.js][] Version v0.10.29 oder höher

[Git][]

[Visual Studio 2013][] mit Update 3

[Node.js-Tools für Visual Studio][]

**Hinweis:** Während wir Visual Studio zum Erstellen, Debuggen und Bereitstellen unseres Node.js-
Projekts verwenden, können Sie in diesem Lernprogramm einen beliebigen Editor verwenden
und Node.js direkt auf der gewählten Plattform und auf die Weise ausführen,
wie Sie ein Node.js-Projekt normalerweise ausführen würden. Sie können dann mit den [Azure CLI][]-Tools die
Anwendung auf Azure-Websites bereitstellen.

# <a name="_Toc395637761">Erstellen eines DocumentDB-Datenbankkontos</a>

Wenn Sie ein DocumentDB-Datenbankkonto in Azure bereitstellen möchten, öffnen Sie das [Azure-Verwaltungsportal][],
und klicken Sie dann entweder auf der Homepage auf die Azure Gallery-Kachel oder in der unteren linken Bildschirmecke auf das Pluszeichen ("+").

![Alt text][1]

Dadurch wird die Azure Gallery geöffnet, in der Sie aus den zahlreichen
verfügbaren Azure-Diensten auswählen können. Wählen Sie in der Azure Gallery "Daten, Speicher und
Sicherung" aus der Liste der Kategorien aus.

![Alt text][2]

Hier wählen Sie dann die Option für Azure DocumentDB aus.

![Alt text][3]

Anschließend wählen Sie am unteren Bildschirmrand die Option "Erstellen" aus.

![Alt text][4]

Dadurch wird das Fenster "Neue DocumentDB" geöffnet, in dem Sie Name, Region,
Skalierung, Ressourcengruppe und andere Einstellungen
für Ihr neues Konto eingeben können.

![Alt text][5]

Nachdem Sie die Werte für Ihr Konto bereitgestellt haben, klicken Sie auf "Erstellen",
damit der Bereitstellungsprozess mit der Erstellung Ihres Datenbankkontos beginnt.
Nachdem die Bereitstellung abgeschlossen ist, sollte
im Benachrichtigungsbereich des Portals eine Benachrichtigung angezeigt werden. Die Kachel auf Ihrer
Startseite (sofern deren Erstellung ausgewählt wurde) ändert sich daraufhin, um auf den
abgeschlossenen Vorgang hinzuweisen.

![Alt text][6]

Nachdem die Bereitstellung abgeschlossen ist, können Sie auf der
Startseite auf die DocumentDB-Kachel klicken, um das Hauptfenster für
dieses neu erstellte DocumentDB-Konto zu eröffnen.

![Alt text][7]
![Alt text][8]

Greifen Sie über die Schaltfläche "Schlüssel" auf Ihre Endpunkt-URL und den primären Schlüssel zu,
kopieren Sie diese dann in die Zwischenablage, um
sie dort griffbereit zu halten, da wir diese Werte in der als nächstes zu erstellenden Webanwendung verwenden werden.

## <a name="_Toc395783178">Erstellen einer neuen Node.js-Anwendung</a>

Wählen Sie in Visual Studio die Option "Datei – Neues Projekt", und wählen Sie dann die Erstellung einer Microsoft Azure Express-Standardanwendung.

![Alt text][9]

Dadurch wird eine einfache Express-Anwendung für Sie erstellt. Wenn Sie zur
Installation von Abhängigkeiten aufgefordert werden, wählen Sie "Ja". Dadurch werden alle
npm-Pakete installiert, die für eine neue Express-Anwendung erforderlich sind.

Nachdem dieser Schritt abgeschlossen ist, sollte der Projektmappen-Explorer
wie folgt aussehen:

![Alt text][10]

Dadurch wird angezeigt, dass Express, Jade und Stylus installiert sind.

Wenn Sie in Visual Studio auf F5 drücken, sollte das Projekt erstellt,
Node.js gestartet und ein Browser mit einer Express-Version
von "Hallo Welt" angezeigt werden.

![Alt text][11]

## <a name="_Toc395783179">Installieren zusätzlicher Module</a>

Die Datei **package.json** ist eine der im Stammverzeichnis des Projekts
erstellten Dateien. Diese Datei enthält eine Liste zusätzlicher Module, die
für eine Express-Anwendung erforderlich sind. Wenn Sie später diese Anwendung
auf einer Azure-Website bereitstellen, wird anhand dieser Datei
bestimmt, welche Module auf Azure installiert werden müssen, um Ihre Anwendung zu unterstützen.

![Alt text][12]

Für dieses Lernprogramm müssen zwei weitere Pakete installiert werden.

Klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf "npm", und wählen Sie "npm
Pakete installieren" aus.

![Alt text][13]

Geben Sie im Dialogfeld "Neue npm-Pakete installieren" **nconf** ein, um
nach dem Modul zu suchen. Die Anwendung benötigt dieses Modul zum Lesen der
Datenbank-Konfigurationswerte aus einer Konfigurationsdatei.

![Alt text][14]

Abschließend installieren Sie das Azure DocumentDB-Modul auf dieselbe Weise, indem
Sie nach **documentdb** suchen. Dieses Modul ist für die DocumentDB-Funktionen
zuständig.

Nachdem Sie diese beiden zusätzlichen Module und Abhängigkeiten installiert haben,
sollten diese im Projektmappen-Explorer unter den **npm**-
Modulen angezeigt werden.

![Alt text][15]

Eine kurze Überprüfung der Datei **package.json** der Anwendung sollte die
zusätzlichen Module anzeigen. Der Inhalt dieser Datei teilt Azure später mit,
welche Pakete beim Ausführen der Anwendung heruntergeladen und
installiert werden müssen.

Bearbeiten Sie die Datei "package.json" bei Bedarf, damit sie dem nachfolgenden Beispiel entspricht.

![Alt text][16]

Dadurch wird Node (und später Azure) mitgeteilt, dass Ihre Anwendung von diesen
zusätzlichen Modulen abhängig ist.

# <a name="_Toc395783180">Verwenden des DocumentDB-Diensts in einer Node-Anwendung</a>

Dies ist für die anfängliche Einrichtung und Konfiguration erforderlich. Jetzt wollen wir zur eigentlichen Aufgabe übergehen, d. h. Code mithilfe von Azure DocumentDB erstellen. Bearbeiten Sie zunächst die Datei **app.js**, die sich im Stammverzeichnis der soeben erstellten Express-Anwendung befindet. Suchen Sie in der Datei die folgenden Zeilen:

    app.get('/', routes.index);
    app.get('/users', user.list);

Ersetzen Sie diese durch die folgenden beiden Zeilen:

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

Dadurch wird der Anwendung mitgeteilt, wie mit den Standardmethoden GET und POST auf einem Formular zu verfahren ist, das wir im nächsten Schritt erstellen.

Suchen Sie jetzt die Datei **index.js**, die sich unter dem Ordner **routes** befindet. Öffnen Sie diese Datei im Editor, und löschen Sie sämtlichen Code aus dieser Datei.

Fügen Sie Folgendes am Anfang der Datei hinzu:

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

Dadurch werden die Module definiert, die wir verwenden werden: **documentdb** und **nconf**.

**nconf** ist ein Modul, über das Sie Konfigurationswerte, z. B. Datenbankverbindungszeichenfolgen, aus externen Dateien laden können, anstatt diese Werte direkt in Ihrem Code einzufügen. "nconf" sucht für seine Konfiguration standardmäßig nach der Datei **config.json**.

Jetzt erstellen wir eine leere Textdatei namens **config.json** im Stammverzeichnis des Projekts (gleiche Position wie "app.js").

Öffnen Sie diese neue **config.json**-Datei, und geben Sie die für Ihren DocumentDB-Endpunkt geeigneten folgenden Werte ein. Achten Sie darauf, dass die HOST- und MASTER\_KEY-Werte ordnungsgemäß festgelegt werden.

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

Wechseln Sie jetzt wieder zur Datei **index.js**, fügen Sie die folgenden Zeilen hinter den letzten Zeilen hinzu, damit die Konfigurationsdatei gelesen und die Werte in Variablen auf Seitenebene gespeichert werden.

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

Fügen Sie anschließend den folgenden Code zur Datei **index.js** hinzu.

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

Dies sind die beiden Funktionen, über deren Verwendung wir die Anwendung zuvor in der Datei **app.js** während der Definition der Routen informiert haben. Wenn eine GET-Methode zur Indexansicht gelangt, wird die Funktion **exports.index** ausgeführt. Gleichsam wird die Funktion **exports.createOfUpdateItem** ausgeführt, wenn die Indexansicht die POST-Methode empfängt.

Diese beiden Funktionen führen sämtliche Aufgaben der von uns erstellten Anwendung aus. Sie rufen jedoch weitere Funktionen auf, damit der Code besser zu lesen und einfacher nachzuverfolgen ist. Fügen Sie jetzt der Datei **index.js** den folgenden Code hinzu. Darin sind alle Methoden enthalten, die von den beiden obigen Funktionen verwendet werden und alle Aufrufe von DocumentDB enthalten. Die einzelnen Funktionen werden später ausführlicher behandelt.

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem** – Aktualisiert ein Dokument in der Datenbank auf Basis der Element-ID, die über das Formular übergeben wird. Mithilfe dieser ID wird eine ***readDocument***-Methode für DocumentDB ausgeführt, um das bestimmte Dokument zu lesen, das wir gespeichert haben. Dann wird das "completed"-Attribut des Dokuments zu "true" geändert und damit angezeigt, dass es abgeschlossen ist. Anschließend wird mit der Ersetzung des Dokuments in der Datenbank fortgefahren.

**getItem** – Verwendet ***queryDocuments***, um mithilfe der "id"-Eigenschaft eines Elements ein einzelnes Element aus der Datenbank abzurufen.

**createItem** – Verwendet die ***createDocument***-Methode, um auf Basis von Elementname und -kategorie, die über das Formular eingegeben wurden, ein neues Dokument in der Datenbank zu erstellen. Außerdem wird für das "completed"-Kennzeichen der Wert "false" festgelegt, um anzuzeigen, dass dieses Element noch nicht abgeschlossen ist.

**listItems** – Verwendet ***queryDocuments***, um alle Dokumente in einer Sammlung zu suchen, die noch nicht abgeschlossen sind oder für die "completed=false" gilt. Dabei wird die auf ANSI-SQL basierende Abfragegrammatik von DocumentDB verwendet, um diese geläufige, aber dennoch leistungsstarke Abfragefunktion zu veranschaulichen.

**readOrCreateDatabase** – Verwendet ***queryDatabases***, um zu prüfen, ob bereits eine Datenbank mit diesem Namen vorhanden ist. Wenn keine entsprechende Datenbank gefunden wurde, wird mit ***createDatabase*** unter Verwendung des bereitgestellten Bezeichners (aus der Konfigurationsdatei) auf dem angegebenen Endpunkt (ebenfalls aus der Konfigurationsdatei) eine neue Datenbank erstellt.

**readOrCreateCollection** – Wie bei "readOrCreateDatabase" versucht diese Methode zunächst, mit dem bereitgestellten Bezeichner eine Sammlung zu finden. Wenn die Methode eine Sammlung findet, wird diese zurückgegeben. Andernfalls wird diese Sammlung für Sie erstellt.

Dies ist der gesamte Code, der für die Interaktion mit DocumentDB für diese Beispielanwendung erforderlich ist.

Jetzt konzentrieren wir uns auf die Erstellung der Benutzeroberfläche, um den Benutzern die eigentliche Interaktion mit unserer Anwendung zu ermöglichen. Die von uns erstellte Express-Anwendung verwendet **Jade** als Ansichtsmodul. Weitere Informationen zu Jade finden Sie unter <http://jade-lang.com/>.

Öffnen Sie die Datei **layout.jade**, die sich im Ordner **views** befindet, und ersetzen Sie die Inhalte durch folgenden Code:

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

Dadurch wird dem **Jade**-Modul mitgeteilt, dass für unsere Anwendung einige HTML-Elemente dargestellt werden müssen. Das Modul erstellt dann einen "**Block**" mit der Bezeichnung "**content**", in dem wir das Layout für unsere Inhaltsseiten bereitstellen können.

Öffnen Sie jetzt die Datei **index.jade**, die "index.js" zugeordnete Ansicht, und ersetzen Sie den Inhalt dann durch den folgenden Code:

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

Dadurch wird das Layout erweitert und Inhalt für den Platzhalter "block content"
bereitgestellt, den wir zuvor in der Datei "layout.jade" gesehen haben.

In diesem Layout haben wir zwei HTML-Formulare erstellt. Das erste Formular enthält eine Tabelle
für unsere Daten und eine Schaltfläche, über die wir Elemente aktualisieren können. Das zweite Formular enthält
einige Eingabefelder und eine Schaltfläche, über die wir ein neues Element erstellen können.

Wenn auf eine dieser Schaltflächen geklickt wird, erfolgt ein Aufruf der Funktion **createOrUpdateItem**
(d. h. die POST-Methode des Formulars ist abgeschlossen), die wir in der Datei **index.js**
erstellt haben. Wenn diese Seite einfach direkt geladen wird (d. h. es wird eine
GET-Methode ausgeführt), wird die Funktion **index** ausgeführt.

Dies sollte jetzt alles sein, damit unsere Anwendung funktioniert.


# <a name="_Toc395783181">Lokales Ausführen der Anwendung</a>

Drücken Sie F5 in Visual Studio, um die Anwendung zu testen.
Daraufhin sollte die Anwendung erstellt, Node.js gestartet und ein Browser mit einer
Seite geöffnet werden, die der folgenden Abbildung entspricht:

![Alt text][17]

1. Füllen Sie die Felder für "Element", "Elementname" und "Kategorie" aus, um
Informationen einzugeben, und klicken Sie dann auf **Element hinzufügen**.

2. Die Seite sollte nun das neu erstellte Element in der
Aufgabenliste anzeigen.

	![Alt text][18]

3. Zum Abschließen einer Aufgabe aktivieren Sie einfach das Kontrollkästchen
in der entsprechenden Spalte, und klicken Sie dann auf **Aufgaben aktualisieren**.


# <a name="_Toc395783182">Bereitstellen der Anwendung auf Azure-Websites</a>

Mit den installierten Node.js-Tools für Visual Studio kann die Bereitstellung auf Azure-
Websites mit wenigen kurzen Schritten problemlos erreicht werden.

Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "Veröffentlichen" aus.

![Alt text][19]

Befolgen Sie dann die Schritte des Assistenten, um die erforderliche Konfiguration
für Ihre Azure-Website bereitzustellen. Im Assistenten können Sie auswählen, ob Sie entweder eine vorhandene
Website aktualisieren oder eine neue Website erstellen möchten.

Nachdem Sie die erforderliche Konfiguration bereitgestellt haben, klicken Sie einfach auf "Veröffentlichen".

![Alt text][20]

Befolgen Sie dann die Schritte des Assistenten, um die erforderliche Konfiguration
für Ihre Azure-Website bereitzustellen. Im Assistenten können Sie auswählen, ob Sie entweder eine vorhandene
Website aktualisieren oder eine neue Website erstellen möchten.

Nachdem Sie die erforderliche Konfiguration bereitgestellt haben, klicken Sie einfach auf "Veröffentlichen".

Visual Studio stellt dann eine Verbindung zu Ihrem Azure-Abonnement her und
veröffentlicht diese Node.js-Anwendung.

Dann schließt Visual Studio die Veröffentlichung Ihrer Webanwendung
in wenigen Sekunden ab und startet einen Browser, in dem das Ergebnis Ihrer Arbeit
in Azure ausgeführt wird!

# <a name="_Toc395783183"></a> <a name="_Toc395637775">Zusammenfassung</a>

Glückwunsch! Sie haben Ihre erste Node.js-Express-Webanwendung
mit Azure DocumentDB erstellt und auf Azure-Websites veröffentlicht.

Den Quellcode für die vollständige Referenzanwendung können Sie [hier][] herunterladen.

</h1>

  [Alt text]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/en-us/vstudio/cc136611.aspx
  [Node.js-Tools für Visual Studio]: https://nodejstools.codeplex.com/
  [Azure CLI]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/
  [Azure-Verwaltungsportal]: http://portal.azure.com
  [1]: ./media/documentdb-nodejs-application/image2.png
  [2]: ./media/documentdb-nodejs-application/image3.png
  [3]: ./media/documentdb-nodejs-application/image4.png
  [4]: ./media/documentdb-nodejs-application/image5.png
  [5]: ./media/documentdb-nodejs-application/image6.png
  [6]: ./media/documentdb-nodejs-application/image7.png
  [7]: ./media/documentdb-nodejs-application/image8.png
  [8]: ./media/documentdb-nodejs-application/image9.png
  [9]: ./media/documentdb-nodejs-application/image10.png
  [10]: ./media/documentdb-nodejs-application/image11.png
  [11]: ./media/documentdb-nodejs-application/image12.png
  [12]: ./media/documentdb-nodejs-application/image13.png
  [13]: ./media/documentdb-nodejs-application/image14.png
  [14]: ./media/documentdb-nodejs-application/image15.png
  [15]: ./media/documentdb-nodejs-application/image16.png
  [16]: ./media/documentdb-nodejs-application/image17.png
  [17]: ./media/documentdb-nodejs-application/image18.png
  [18]: ./media/documentdb-nodejs-application/image19.png
  [19]: ./media/documentdb-nodejs-application/image20.png
  [20]: ./media/documentdb-nodejs-application/image21.png
  [hier]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409
