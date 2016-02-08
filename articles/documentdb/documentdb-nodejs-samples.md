<properties 
	pageTitle="NoSQL Node.js-Beispiele für DocumentDB | Microsoft Azure" 
	description="Auf GitHub finden Sie NoSQL Node.js-Beispiele für gängige Aufgaben in DocumentDB, darunter CRUD-Vorgänge für JSON-Dokumente in NoSQL-Datenbanken." 
	keywords="Node.js-Beispiele"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="nodejs"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="mimig"/>


# Node.js-Beispiele für DocumentDB

> [AZURE.SELECTOR]
- [.NET Examples](documentdb-dotnet-samples.md)
- [Node.js Examples](documentdb-nodejs-samples.md)
- [Azure Code Sample Gallery](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Beispiellösungen, in denen CRUD- und andere gängige Vorgänge für DocumentDB-Ressourcen ausgeführt werden, sind im GitHub-Repository [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) enthalten. Dieser Artikel enthält Folgendes:

- Links zu den Aufgaben in den einzelnen Node.js-Beispielprojektdateien. 
- Links zum zugehörigen API-Referenzinhalt.

**Voraussetzungen**

1. Sie benötigen ein Azure-Konto, um diese Node.js-Beispiele zu verwenden:
    - Sie können [ein Azure-Konto kostenlos erstellen](https://azure.microsoft.com/pricing/free-trial/): Sie erhalten ein Guthaben, das Sie zum Ausprobieren zahlungspflichtiger Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste wie Websites nutzen, wenn das Guthaben aufgebraucht ist. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit einer Zahlung einverstanden sind.
   - Sie können Ihre [Vorteile für Visual Studio-Abonnenten aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): Ihr Visual Studio-Abonnement schließt ein monatliches Guthaben ein, das Sie für zahlungspflichtige Azure-Dienste nutzen können.
2. Sie benötigen zudem das [Node.js SDK](documentdb-sdk-node.md). 

    > [AZURE.NOTE] Jedes Beispiel ist eigenständig, richtet sich selbst ein und bereinigt sich selbst. Dementsprechend geben die Beispiele mehrere Aufrufe an [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html) aus. Bei jeder Ausführung dieses Vorgangs wird Ihrem Abonnement 1 Stunde Nutzung gemäß Leistungsebene der erstellten Sammlung in Rechnung gestellt.

## Datenbankbeispiele

Die Datei [app.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.CollectionManagement/app.js) im Projekt [DocumentDB.Samples.CollectionManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.CollectionManagement) zeigt, wie die folgenden Aufgaben ausgeführt werden.

Aufgabe | API-Referenz
--- | ---
[Erstellen einer Datenbank](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[Abfragen eines Kontos nach einer Datenbank](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabase)
[Lesen einer Datenbank nach ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Auflisten von Datenbanken für ein Konto](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Löschen einer Datenbank](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## Sammlungsbeispiele 

Die Datei [app.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.CollectionManagement/app.js) im Projekt [DocumentDB.Samples.CollectionManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.CollectionManagement) zeigt, wie die folgenden Aufgaben ausgeführt werden.

Aufgabe | API-Referenz
--- | ---
[Erstellen einer Sammlung](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[Lesen einer Liste aller Sammlungen in einer Datenbank](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.listCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#listCollections)
[Abrufen einer Sammlung nach „\_self“] (https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) [Abrufen einer Auflistung nach ID](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) [Abrufen der Leistungsebene einer Sammlung](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers) [Ändern der Leistungsebene einer Sammlung](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer) [Löschen einer Sammlung](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## Dokumentbeispiele

Die Datei [app.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.DocumentManagement/app.js) im Projekt [DocumentDB.Samples.DocumentManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.DocumentManagement) zeigt, wie die folgenden Aufgaben ausgeführt werden.

Aufgabe | API-Referenz
--- | ---
[Erstellen eines Dokuments](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[Lesen des Dokumentfeeds für eine Sammlung](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Lesen eines Dokuments nach ID](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Abfragen von Dokumenten](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) 
[Aktualisieren eines Dokuments](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[Löschen eines Dokuments](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## Indizierungsbeispiele

Die Datei [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.IndexManagement/app.js) im Projekt [DocumentDB.Samples.IndexManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.IndexManagement) zeigt, wie die folgenden Aufgaben ausgeführt werden.

Aufgabe | API-Referenz
--- | ---
Erstellen einer Sammlung mit Standardindizierung | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[Manuelles Indizieren eines bestimmten Dokuments](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[Manuelles Ausschließen eines bestimmten Dokuments aus dem Index](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Verwenden der verzögerten Indizierung für den Massenimport oder das Lesen umfangreicher Sammlungen](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#indexingmode)
[Einschließen bestimmter Pfade eines Dokuments in die Indizierung](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy) 
[Ausschließen bestimmter Pfade von der Indizierung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy)
[Zulassen einer Überprüfung eines Zeichenfolgenpfads während eines Bereichsvorgangs](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[Erstellen eines Bereichsindexes in einem Zeichenfolgenpfad](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[Erstellen einer Sammlung mit indexPolicy-Standardwert und anschließende Onlineaktualisierung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

Weitere Informationen zur Indizierung finden Sie unter [Indizierungsrichtlinien für DocumentDB](documentdb-indexing-policies.md).

## Serverseitige Programmierbeispiele

Die Datei [app.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.ServerSideScripts/app.js) im Projekt [DocumentDB.Samples.IndexManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.ServerSideScripts) zeigt, wie die folgenden Aufgaben ausgeführt werden.

Aufgabe | API-Referenz
--- | ---
[Erstellen einer gespeicherten Prozedur](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[Ausführen einer gespeicherten Prozedur](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

Weitere Informationen über die serverseitige Programmierung finden Sie unter [DocumentDB-serverseitige Programmierung: gespeicherte Prozeduren, Datenbanktrigger und UDFs](documentdb-programming.md).

<!---HONumber=AcomDC_0128_2016-->