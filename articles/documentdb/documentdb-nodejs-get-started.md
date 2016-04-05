<properties
	pageTitle="NoSQL Node.js-Tutorial für DocumentDB | Microsoft Azure"
	description="Ein NoSQL Node.js-Tutorial, in dem Sie eine Node-Datenbank und eine Konsolenanwendung mit dem DocumentDB Node.js SDK erstellen. DocumentDB ist eine NoSQL-Datenbank für JSON."
    keywords="node.js tutorial, node database"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# NoSQL Node.js-Tutorial: DocumentDB Node.js-Konsolenanwendung  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Willkommen beim Node.js-Tutorial für das DocumentDB Node.js SDK! Im Rahmen dieses Tutorials erstellen Sie eine Konsolenanwendung, mit der DocumentDB-Ressourcen, einschließlich einer Node-Datenbank, erstellt und abgefragt werden können.

Folgende Themen werden behandelt:

- Erstellen eines DocumentDB-Kontos und Verbindungsaufbau
- Einrichten der Anwendung
- Erstellen einer Node-Datenbank
- Erstellen einer Sammlung
- Erstellen von JSON-Dokumenten
- Abfragen der Sammlung
- Löschen der Node-Datenbank

Sie haben nicht genügend Zeit? Keine Sorge! Die vollständige Lösung ist auf [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started) verfügbar. In [Abrufen der vollständigen Lösung](#GetSolution) finden Sie eine Kurzanleitung.

Bitte verwenden Sie nach Abschluss des Node.js-Tutorials die Abstimmungsschaltflächen am Anfang und am Ende dieser Seite, um uns Ihre Meinung mitzuteilen. Wenn wir mit Ihnen Kontakt aufnehmen sollen, können Sie Ihre E-Mail-Adresse im Kommentar hinterlassen.

Lassen Sie uns anfangen.

## Voraussetzungen für das Node.js-Tutorial

Stellen Sie sicher, dass Sie über Folgendes verfügen:

- Ein aktives Azure-Konto. Wenn Sie kein Konto haben, können Sie sich für eine [kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.
- [Node.js](https://nodejs.org/) Version v0.10.29 oder höher.

## Schritt 1: Erstellen eines DocumentDB-Kontos

In diesem Schritt erstellen Sie ein DocumentDB-Konto. Wenn Sie bereits über ein Konto verfügen, das Sie verwenden möchten, können Sie diesen Schritt überspringen und mit [Einrichten der Node.js-Anwendung](#SetupNode) fortfahren.

[AZURE.INCLUDE [DocumentDB-Erstellen-DB-Account](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Schritt 2: Einrichten der Node.js-Anwendung

1. Öffnen Sie den bevorzugten Terminaldienst.
2. Suchen Sie nach dem Ordner oder Verzeichnis, in dem Sie die Node.js-Anwendung speichern möchten.
3. Erstellen Sie mit den folgenden Befehlen zwei leere JavaScript-Dateien:
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X: 
	    * ```touch app.js```
        * ```touch config.js```
4. Installieren Sie das documentdb-Modul über npm. Verwenden Sie den folgenden Befehl:
    * ```npm install documentdb --save```

Prima. Damit ist die Einrichtung abgeschlossen, und wir können mit dem Schreiben von Code beginnen.

##<a id="Config"></a> Schritt 3: Festlegen der Konfigurationen der App

Öffnen Sie ```config.js``` in einem Text-Editor Ihrer Wahl.

Erstellen Sie dann ein leeres Objekt mit dem Titel ```config```, und legen Sie die Eigenschaften ```config.endpoint``` und ```config.authKey``` auf Ihren DocumentDB-Endpunkt und den Autorisierungsschlüssel fest. Beide Konfigurationen finden Sie im [Azure-Portal](https://portal.azure.com).

![Node.js-Tutorial – Screenshot des Azure-Portals mit einem DocumentDB-Konto, bei dem der AKTIVE Hub, die Schaltfläche SCHLÜSSEL auf dem Blatt „DocumentDB-Konto“ und auf dem Blatt „Schlüssel“ die Werte URI, PRIMÄRSCHLÜSSEL und SEKUNDÄRSCHLÜSSEL hervorgehoben sind – Node-Datenbank][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Jetzt fügen wir ```database id```, ```collection id``` und ```JSON documents``` zu Ihrem ```config```-Objekt hinzu. Fügen Sie weiter unten an der Stelle, an der Sie die ```config.endpoint```- und die ```config.authKey```-Eigenschaft festgelegt haben, folgenden Code hinzu. Wenn Sie bereits über Daten verfügen, die Sie in der Datenbank speichern möchten, können Sie das [Datenmigrationstool](documentdb-import-data.md) von DocumentDB verwenden, statt Dokumentdefinitionen hinzuzufügen.

    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl",
        "partitionKey": { "paths": ["/district"], "kind": "Hash" }
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "district": "WA5",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "district": "NY23",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


Die Datenbank-, Sammlungs- und Dokumentdefinitionen dienen als ```database id```, ```collection id``` und Dokumentdaten Ihrer DocumentDB.

Exportieren Sie abschließend das ```config```-Objekt, sodass Sie in der Datei ```app.js``` darauf verweisen können.

    module.exports = config;

##<a id="Connect"></a> Schritt 4: Herstellen einer Verbindung mit einem DocumentDB-Konto

Öffnen Sie die leere Datei ```app.js``` im Text-Editor. Importieren Sie das ```documentdb```-Modul und das neu erstellte ```config```-Modul.

    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Als Nächstes verwenden wir die zuvor gespeicherten Eigenschaften ```config.endpoint``` und ```config.authKey```, um ein neues DocumentClient-Element zu erstellen.

    var client = new documentClient(config.endpoint, { "masterKey": config.authKey });

Nachdem Sie eine Verbindung mit einem DocumentDB-Konto hergestellt haben, betrachten wir jetzt die Verwendung von DocumentDB-Ressourcen.

## Schritt 5: Erstellen einer Node-Datenbank
Eine [Datenbank](documentdb-resources.md#databases) kann mithilfe der [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html)-Funktion der **DocumentClient**-Klasse erstellt werden. Eine Datenbank ist ein logischer Container für Dokumentspeicher, der auf Sammlungen aufgeteilt ist. Fügen Sie eine Funktion zum Erstellen der neuen Datenbank in der Datei „app.js“ mit der ```id``` aus dem ```config```-Objekt hinzu. Wir stellen zunächst sicher, dass eine Datenbank mit der gleichen ```FamilyRegistry```-ID noch nicht vorhanden ist. Wenn eine vorhanden ist, verwenden wir diese Datenbank, statt eine neue zu erstellen.

    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    /**
     * Get the database by ID, or create if it doesn't exist.
     * @param {string} database - The database to get or create
     */
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }
##<a id="CreateColl"></a>Schritt 6: Erstellen einer Sammlung  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** erstellt eine neue Sammlung, die Auswirkungen auf die Preise hat. Weitere Informationen finden Sie auf unserer [Preisseite](https://azure.microsoft.com/pricing/details/documentdb/).

Eine [Sammlung](documentdb-resources.md#collections) kann mithilfe der [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html)-Funktion der **DocumentClient**-Klasse erstellt werden. Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Fügen Sie eine Funktion zum Erstellen der neuen Sammlung in der Datei „app.js“ mit der ```id``` aus dem ```config```-Objekt hinzu. Auch hier stellen wir sicher, dass noch keine Sammlung mit der gleichen ```FamilyCollection```-ID vorhanden ist. Wenn eine vorhanden ist, verwenden wir diese Sammlung, statt eine neue zu erstellen.

    /**
     * Get the collection by ID, or create if it doesn't exist.
     */
    function getCollection() {
        console.log(`Getting collection:\n${collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

##<a id="CreateDoc"></a>Schritt 7: Erstellen eines Dokuments
Ein [Dokument](documentdb-resources.md#documents) kann mithilfe der [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html)-Funktion der **DocumentClient**-Klasse erstellt werden. Dokumente sind benutzerdefinierter (beliebiger) JSON-Inhalt. Sie können nun ein Dokument in DocumentDB einfügen.

Fügen Sie anschließend in „app.js“ eine Funktion für die Erstellung der Dokumente hinzu, die die im ```config```-Objekt gespeicherten JSON-Daten enthalten. Auch hier stellen wir sicher, dass ein Dokument mit der gleichen ID noch nicht vorhanden ist.

    /**
     * Get the document by ID, or create if it doesn't exist.
     * @param {function} callback - The callback function on completion
     */
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Glückwunsch! Sie verfügen jetzt über die Funktionen zum Erstellen einer Datenbank, einer Sammlung und eines Dokuments in DocumentDB!

![Node.js-Tutorial – Diagramm zur Veranschaulichung der hierarchischen Beziehung zwischen dem Konto, der Datenbank, der Sammlung und den Dokumenten – Node-Datenbank](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Schritt 8: Abfragen von DocumentDB-Ressourcen

DocumentDB unterstützt [umfassende Abfragen](documentdb-sql-query.md) der in jeder Sammlung gespeicherten JSON-Dokumente. Der folgende Beispielcode zeigt eine Abfrage, die Sie für die Dokumente in Ihrer Sammlung ausführen können. Fügen Sie der Datei ```app.js``` die folgende Funktion hinzu: DocumentDB unterstützt SQL-ähnliche Abfragen, wie unten dargestellt. Weitere Informationen zum Erstellen von komplexen Abfragen finden Sie unter [Query Playground](https://www.documentdb.com/sql/demo) (in englischer Sprache) und in der [Abfragedokumentation](documentdb-sql-query.md).

    /**
     * Query the collection using SQL
     */
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.address.city FROM root r WHERE r.lastName = "Andersen"',
                { enableCrossPartitionQuery: true }
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        console.log(`Query returned ${queryResult}`);
                    }
                    resolve(results);
                }
            });
        });
    };


Das folgende Diagramm veranschaulicht, wie die DocumentDB-SQL-Abfragesyntax für die erstellte Sammlung aufgerufen wird.

![Node.js-Tutorial – Diagramm zur Veranschaulichung des Bereichs und der Bedeutung der Abfrage – Node-Datenbank](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Das [FROM](documentdb-sql-query.md/#from-clause) -Schlüsselwort ist in der Abfrage optional, da DocumentDB Abfragen auf eine Sammlung begrenzt. Aus diesem Grund ist "FROM Familien f" austauschbar mit "FROM Stamm r" oder einem anderen variablen Namen, den Sie auswählen. DocumentDB wird ableiten, dass Familien, Stamm oder der variable Name, den Sie ausgewählt haben, standardmäßig auf die aktuelle Sammlung verweisen.

##<a id="DeleteDatabase"></a>Schritt 9: Löschen der Node-Datenbank

Das Löschen der erstellten Datenbank entfernt die Datenbank und alle untergeordneten Ressourcen (Sammlungen, Dokumente usw.). Sie können die Datenbank löschen, indem Sie den folgenden Codeausschnitt hinzufügen.

    /**
     * Cleanup the database and collection on completion
     */
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

##<a id="Build"></a>Schritt 10: Zusammenstellen

Da Sie jetzt alle erforderlichen Funktionen für die Anwendung festgelegt haben, können Sie sie aufrufen.

Fügen Sie den folgenden Codeausschnitt unten im Code in ```app.js``` hinzu.

    /**
     * Exit the app with a prompt
     * @param {message} message - The message to display
     */
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
        .then(() => getCollection())
        .then(() => getFamilyDocument(config.documents.Andersen))
        .then(() => getFamilyDocument(config.documents.Wakefield))
        .then(() => queryCollection())
        .then(() => cleanup())
        .then(() => { exit(`Completed successfully`); })
        .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a> Schritt 11: Ausführen Ihrer Node.js-Anwendung

Sie können die Node.js-Anwendung jetzt ausführen.

Suchen Sie auf Ihrem Terminal nach der Datei ```app.js```, und führen Sie den Befehl ```node app.js``` aus.

Die Ausgabe der GetStarted-Anwendung sollte angezeigt werden. Die Ausgabe sollte dem folgenden Beispieltext entsprechen.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
      Query returned Seattle

    Cleaning up by deleting database FamilyDB

    Completed successfully
    Press any key to exit

Glückwunsch! Sie haben das Node.js-Tutorial abgeschlossen und Ihre erste DocumentDB-Konsolenanwendung erstellt.

##<a id="GetSolution"></a> Abrufen der vollständigen Projektmappe für das Node.js-Tutorial
Um die "GetStarted"-Lösung zu erstellen, die alle Beispiele dieses Artikels enthält, ist Folgendes erforderlich:

-   [DocumentDB-Konto][documentdb-create-account].
-   Die [GetStarted-Lösung](https://github.com/Azure-Samples/documentdb-node-getting-started) auf GitHub.

Installieren Sie das **documentdb**-Modul über npm. Verwenden Sie den folgenden Befehl:
* ```npm install documentdb --save```

Aktualisieren Sie dann in der Datei ```config.js``` die Werte für „config.endpoint“ und „config.authKey“, wie unter [Schritt 3: Festlegen der Konfigurationen der App](#Config) beschrieben.

## Nächste Schritte

-   Benötigen Sie ein komplexeres Node.js-Beispiel? Sie finden eines unter [Erstellen einer Node.js-Webanwendung mithilfe von DocumentDB](documentdb-nodejs-application.md).
-	Sie erfahren, wie Sie ein [DocumentDB-Konto überwachen](documentdb-monitor-accounts.md).
-	Fragen Sie unser Beispieldataset im [Query Playground](https://www.documentdb.com/sql/demo) ab.
-	Weitere Informationen zum Programmiermodell finden Sie auf der [DocumentDB-Dokumentationsseite](../../services/documentdb/) im Abschnitt "Entwickeln".

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->