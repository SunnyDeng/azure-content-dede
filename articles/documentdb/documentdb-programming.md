<properties 
	pageTitle="DocumentDB-Programmierung: Gespeicherte Prozeduren, Datenbanktrigger und benutzerdefinierte Funktionen | Microsoft Azure" 
	description="Erfahren Sie, wie Sie DocumentDB dazu verwenden, gespeicherte Prozeduren, Datenbanktrigger und benutzerdefinierte Funktionen in JavaScript zu schreiben. Erhalten Sie Tipps zur Datenbankprogrammierung und mehr." 
	keywords="Datenbanktrigger, gespeicherte Prozedur, Datenbankprogramm, sproc, documentdb, Azure, Microsoft Azure"
	services="documentdb" 
	documentationCenter="" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="andrl"/>

# DocumentDB-serverseitige Programmierung : gespeicherte Prozeduren, Datenbanktrigger und benutzerdefinierte Funktionen

Erfahren Sie, wie Entwickler dank der in die DocumentDB-Sprache integrierten transaktionalen Ausführung von JavaScript **gespeicherte Prozeduren**, **Trigger** und **benutzerdefinierte Funktionen (User Defined Functions, UDFs)** in systemeigenem JavaScript erstellen können. Dadurch können Sie für ein Datenbankprogramm Anwendungslogik schreiben, die direkt auf den Partitionen des Datenbankspeichers bereitgestellt und ausgeführt werden kann.

Für den Beginn empfiehlt sich folgendes Video, in dem Andrew Liu das serverseitige Datenbankprogrammiermodell von DocumentDB kurz vorstellt.

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Kehren Sie dann zurück zu diesem Artikel, in dem Sie die Antworten auf die folgenden Fragen erfahren:

- Wie schreibe ich eine gespeicherte Prozedur, einen Trigger oder eine UDF mit JavaScript?
- Wie gewährleistet DocumentDB ACID?
- Wie funktionieren Transaktionen in DocumentDB?
- Was sind vorangestellte Trigger und nachgestellte Trigger, und wie schreibe ich sie?
- Wie kann ich eine gespeicherte Prozedur, einen Trigger oder eine UDF auf REST-konforme Weise mithilfe von HTTP registrieren und ausführen?
- Welche DocumentDB-SDKs sind für das Erstellen und Ausführen von gespeicherten Prozeduren, Triggern und UDFs verfügbar?

## Einführung in die Programmierung von gespeicherten Prozeduren und die UDF-Programmierung

Dieser Ansatz von *„JavaScript als modernes T-SQL“* befreit die Anwendungsentwickler von der Komplexität durch nicht übereinstimmende Systeme und objektrelationale Zuordnungstechnologien. Er hat auch eine Reihe spezifischer Vorteile, die zum Erstellen funktionsreicher Anwendungen genutzt werden können:

-	**Prozedurale Logik:** JavaScript bietet als Programmiersprache auf höherer Ebene eine umfangreiche und vertraute Benutzeroberfläche zur Darstellung der Geschäftslogik. Sie können komplexe Abfolgen von Vorgängen mit direkterem Zugriff auf die Daten ausführen.

-	**Atomarische Transaktionen:** DocumentDB gewährleistet, dass die innerhalb einer einzelnen gespeicherten Prozedur oder in einem Trigger ausgeführten Datenbankvorgänge atomarisch sind. Dadurch kann eine Anwendung zusammengehörige Vorgänge in einem einzelnen Batch kombinieren, damit entweder alle Vorgänge oder keiner dieser Vorgänge erfolgreich ausgeführt werden kann.

-	**Leistung:** Die Tatsache, dass JSON an sich dem JavaScript-Sprachsystem zugeordnet ist und auch die Basiseinheit für die Speicherung in DocumentDB darstellt, ermöglicht eine Reihe von Optimierungen wie die bequeme Realisierung von JSON-Dokumenten im Pufferpool und deren bedarfsgesteuerte Bereitstellung für den ausführenden Code. Es gibt weitere Leistungsvorteile, die der Übertragung der Geschäftslogik auf die Datenbank zugeordnet sind:
	-	Batchverarbeitung – Entwickler können Vorgänge wie Einlagen gruppieren und dann zusammen übermitteln. Der Aufwand für die Latenz des Netzwerkdatenverkehrs und der erhöhte Speicheraufwand beim Erstellen separater Transaktionen werden erheblich verringert. 
	-	Vorkompilierung – DocumentDB führt für gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen (UDFs) Vorkompilierungen durch, um den Aufwand der JavaScript-Kompilierung für die einzelnen Aufrufe zu vermeiden. Der Mehraufwand für die Erstellung des Bytecodes für die prozedurale Logik wird auf ein Minimum gesenkt.
	-	Sequenzierung – Viele Vorgänge benötigen einen Nebeneffekt (Auslöser oder Trigger), der möglicherweise die Ausführung eines oder vieler sekundärer Speichervorgänge einbezieht. Dies ist abgesehen von der der Atomarität effektiver, wenn der Vorgang auf den Server verlagert wird. 
-	**Kapselung:** Die Geschäftslogik kann mithilfe gespeicherter Prozeduren an einem Ort zusammengefasst werden. Das hat zwei Vorteile:
	-	Es wird eine Abstraktionsschicht über den Rohdaten hinzugefügt, die es Datenarchitekten gestattet, ihre Anwendungen unabhängig von den Daten zu entwickeln. Dies ist aufgrund der komplizierten Annahmen, die bei der direkten Behandlung der Daten zur Anwendung hinzugefügt werden müssen, insbesondere bei schemafreien Daten von Vorteil.  
	-	Durch diese Abstraktion können Unternehmen ihre Daten schützen, indem sie den Zugriff über die Scripts optimieren.  

Die Erstellung und Ausführung von Datenbanktriggern, gespeicherten Prozeduren und benutzerdefinierten Abfrageoperatoren wird auf vielen Plattformen, z. B. .NET, Node.js und JavaScript, über die [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) und [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) unterstützt. **In diesem Tutorial wird das [Node.js-SDK](http://dl.windowsazure.com/documentDB/nodedocs/)** verwendet, um die Syntax und Verwendung von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen (User Defined Functions, UDFs) zu veranschaulichen.

## Gespeicherte Prozeduren

### Beispiel: Schreiben einer einfachen gespeicherten Prozedur 
Beginnen wir mit einer einfachen gespeicherten Prozedur, die die Antwort "Hello World" zurückgibt.

	var helloWorldStoredProc = {
	    id: "helloWorld",
	    body: function () {
	        var context = getContext();
	        var response = context.getResponse();
	
	        response.setBody("Hello, World");
	    }
	}


Gespeicherte Prozeduren werden pro Sammlung registriert und können auf beliebige Dokumente und Anhänge angewendet werden, die sich in dieser Sammlung befinden. Der folgende Codeausschnitt zeigt, wie die gespeicherte Prozedur "helloWorld" mit einer Sammlung registriert wird.

	// register the stored procedure
	var createdStoredProcedure;
	client.createStoredProcedureAsync(collection._self, helloWorldStoredProc)
		.then(function (response) {
		    createdStoredProcedure = response.resource;
		    console.log("Successfully created stored procedure");
		}, function (error) {
		    console.log("Error", error);
		});


Nachdem die gespeicherte Prozedur registriert wurde, können wir sie für die Sammlung ausführen und die an den Client zurückgegebenen Ergebnisse lesen.

	// execute the stored procedure
	client.executeStoredProcedureAsync(createdStoredProcedure._self)
		.then(function (response) {
		    console.log(response.result); // "Hello, World"
		}, function (err) {
		    console.log("Error", error);
		});


Das Kontextobjekt bietet Zugriff auf alle Vorgänge, die für den DocumentDB-Speicher ausgeführt werden können, sowie Zugriff auf die Anforderungs- und Antwortobjekte. In diesem Fall haben wir das Antwortobjekt dazu verwendet, den Text der Antwort festzulegen, der an den Client zurückgesendet wurde. Weitere Informationen finden Sie in der [Dokumentation zum DocumentDB JavaScript-Server-SDK](http://dl.windowsazure.com/documentDB/jsserverdocs/).

Dieses Beispiel möchten wir jetzt erweitern und datenbankbezogenere Funktionen zur gespeicherten Prozedur hinzufügen. Gespeicherte Prozeduren können Dokumente und Anhänge innerhalb der Sammlung erstellen, aktualisieren, lesen, abfragen und löschen.

### Beispiel: Schreiben einer gespeicherten Prozedur zum Erstellen eines Dokuments 
Der nächste Codeausschnitt veranschaulicht, wie das Kontextobjekt für die Interaktion mit DocumentDB-Ressourcen verwendet wird.

	var createDocumentStoredProc = {
	    id: "createMyDocument",
	    body: function createMyDocument(documentToCreate) {
	        var context = getContext();
	        var collection = context.getCollection();
	
	        var accepted = collection.createDocument(collection.getSelfLink(),
	              documentToCreate,
				function (err, documentCreated) {
				    if (err) throw new Error('Error' + err.message);
				    context.getResponse().setBody(documentCreated.id)
				});
	        if (!accepted) return;
	    }
	}


Diese gespeicherte Prozedur übernimmt "documentToCreate", den in der aktuellen Sammlung zu erstellenden Text eines Dokuments, als Eingabe. Alle derartigen Vorgänge sind asynchron und von JavaScript-Funktionsrückrufen abhängig. Die Rückruffunktion verfügt über zwei Parameter, einer für das Fehlerobjekt, wenn der Vorgang fehlschlägt, und ein anderer für das erstellte Objekt. Innerhalb des Rückrufs können die Benutzer entweder die Ausnahme behandeln oder einen Fehler auslösen. Für den Fall, dass ein Rückruf nicht bereitgestellt wird und ein Fehler auftritt, löst die DocumentDB-Laufzeitumgebung einen Fehler aus.

Im obigen Beispiel löst der Rückruf einen Fehler aus, wenn der Vorgang fehlschlägt. Andernfalls wird die ID des erstellten Dokuments als Text für die Antwort an den Client festgelegt. Diese gespeicherte Prozedur wird mit Eingabeparametern wie folgt ausgeführt:

	// register the stored procedure
	client.createStoredProcedureAsync(collection._self, createDocumentStoredProc)
		.then(function (response) {
		    var createdStoredProcedure = response.resource;
	
		    // run stored procedure to create a document
		    var docToCreate = {
		        id: "DocFromSproc",
		        book: "The Hitchhiker’s Guide to the Galaxy",
		        author: "Douglas Adams"
		    };
	
		    return client.executeStoredProcedureAsync(createdStoredProcedure._self,
	              docToCreate);
		}, function (error) {
		    console.log("Error", error);
		})
	.then(function (response) {
	    console.log(response); // "DocFromSproc"
	}, function (error) {
	    console.log("Error", error);
	});

	
Beachten Sie, dass diese gespeicherte Prozedur modifiziert werden kann, um ein Array von Dokumenttexten als Eingabe zu übernehmen und alle während der Ausführung derselben gespeicherten Prozedur zu erstellen, anstatt mehrere Netzwerkanforderungen zu verwenden, um sie jeweils einzeln zu erstellen. Auf diese Weise kann eine effiziente Massenimportfunktion für DocumentDB implementiert werden (dies wird später in diesem Lernprogramm besprochen).

Das beschriebene Beispiel hat die Verwendung gespeicherter Prozeduren veranschaulicht. Trigger und benutzerdefinierte Funktionen (UDFs) werden später in diesem Lernprogramm behandelt.

## Datenbankprogrammtransaktionen
Eine Transaktion in einer typischen Datenbank kann als Folge von Vorgängen definiert werden, die als einzelne logische Arbeitseinheit ausgeführt wird. Jede Transaktion bietet **ACID-Garantien**. ACID (Atomicity, Consistency, Isolation, Durability) ist ein bekanntes Akronym, das für vier Eigenschaften steht: Atomarität, Konsistenz, Isolation und Dauerhaftigkeit.

Die Atomarität gewährleistet kurz gesagt, dass alle innerhalb einer Transaktion ausgeführten Vorgänge als einzelne Einheit betrachtet werden, in der entweder alle oder kein Vorgang ausgeführt wird. Die Konsistenz stellt sicher, dass die Daten zwischen den Transaktionen immer einen geeigneten internen Status aufweisen. Die Isolation sorgt dafür, dass es keine Konflikte zwischen zwei Transaktionen gibt. Im Allgemeinen stellen die meisten kommerziellen Systeme mehrere Isolationsebenen bereit, die auf Basis der Anforderungen der Anwendung genutzt werden können. Die Dauerhaftigkeit stellt sicher, dass jede an die Datenbank übergebene Änderung immer vorhanden ist.

In DocumentDB wird JavaScript im gleichen Speicherbereich wie die Datenbank gehostet. Daher werden die in gespeicherten Prozeduren und Triggern erstellten Anforderungen im gleichen Gültigkeitsbereich einer Datenbanksitzung ausgeführt. Auf diese Weise kann DocumentDB die vier Eigenschaften von ACID für alle Vorgänge garantieren, die Teil einer einzelnen gespeicherten Prozedur oder eines einzelnen Triggers sind. Betrachten Sie die folgende Definition einer gespeicherten Prozedur:

	// JavaScript source code
	var exchangeItemsSproc = {
	    name: "exchangeItems",
	    body: function (playerId1, playerId2) {
	        var context = getContext();
	        var collection = context.getCollection();
	        var response = context.getResponse();
	
	        var player1Document, player2Document;
	
	        // query for players
	        var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
	        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
	            function (err, documents, responseOptions) {
	                if (err) throw new Error("Error" + err.message);
	
	                if (documents.length != 1) throw "Unable to find both names";
	                player1Document = documents[0];
	
	                var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
	                var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
	                    function (err2, documents2, responseOptions2) {
	                        if (err2) throw new Error("Error" + err2.message);
	                        if (documents2.length != 1) throw "Unable to find both names";
	                        player2Document = documents2[0];
	                        swapItems(player1Document, player2Document);
	                        return;
	                    });
	                if (!accept2) throw "Unable to read player details, abort ";
	            });
	
	        if (!accept) throw "Unable to read player details, abort ";
	
	        // swap the two players’ items
	        function swapItems(player1, player2) {
	            var player1ItemSave = player1.item;
	            player1.item = player2.item;
	            player2.item = player1ItemSave;
	
	            var accept = collection.replaceDocument(player1._self, player1,
	                function (err, docReplaced) {
					    if (err) throw "Unable to update player 1, abort ";
	
					    var accept2 = collection.replaceDocument(player2._self, player2,
	                        function (err2, docReplaced2) {
							    if (err) throw "Unable to update player 2, abort"
							});
	
					    if (!accept2) throw "Unable to update player 2, abort";
					});
	
	            if (!accept) throw "Unable to update player 1, abort";
	        }
	    }
	}
	
	// register the stored procedure in Node.js client
	client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
		.then(function (response) {
		    var createdStoredProcedure = response.resource;
		}
	);

Diese gespeicherte Prozedur verwendet Transaktionen innerhalb einer Spiele-App, um in einem einzelnen Vorgang Objekte zwischen zwei Spielern auszutauschen. Die gespeicherte Prozedur versucht dabei zwei Dokumente zu lesen, die jeweils der Spieler-ID entsprechen, die als Argument übergeben wird. Wenn beide Spielerdokumente gefunden werden, aktualisiert die gespeicherte Prozedur die Dokumente, indem deren Objekte ausgetauscht werden. Wenn währenddessen Fehler auftreten, wird eine JavaScript-Ausnahme ausgelöst, durch die die Transaktion vorbehaltlos abgebrochen wird.

### Commit und Rollback
Transaktionen sind fest im JavaScript-Programmiermodell von DocumentDB integriert. Innerhalb einer JavaScript-Funktion werden alle Vorgänge automatisch von einer einzelnen Transaktion umschlossen. Wenn das JavaScript ohne Ausnahmen abgeschlossen wird, werden die für die Datenbank vorgenommenen Vorgänge bestätigt. Die Anweisungen "BEGIN TRANSACTION" und "COMMIT TRANSACTION" für relationale Datenbanken sind in DocumentDB praktisch eingeschlossen.
 
Wenn von dem Skript eine Ausnahme weitergegeben wird, führt die JavaScript-Laufzeitumgebung von DocumentDB ein Rollback für die gesamte Transaktion aus. Wie im vorherigen Beispiel gezeigt, ist die Auslösung einer Ausnahme in DocumentDB tatsächlich mit dem Befehl "ROLLBACK TRANSACTION" vergleichbar.
 
### Datenkonsistenz
Gespeicherte Prozeduren und Trigger werden immer für das primäre Replikat der DocumentDB-Sammlung ausgeführt. Dadurch wird sichergestellt, dass innerhalb von gespeicherten Prozeduren erfolgte Lesevorgänge eine hohe Konsistenz bieten. Abfragen, die benutzerdefinierte Funktionen verwenden, können mit dem primären oder einem beliebigen sekundären Replikat ausgeführt werden. Es wird jedoch durch die Auswahl des geeigneten Replikats sichergestellt, dass die geforderte Konsistenz erreicht wird.

## Gebundene Ausführung
Alle DocumentDB-Vorgänge müssen innerhalb der vom Server angegebenen Anforderungstimeoutdauer abgeschlossen werden. Diese Einschränkung gilt auch für JavaScript-Funktionen (gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen). Wenn ein Vorgang nicht innerhalb dieser Frist abgeschlossen werden kann, wird für die Transaktion ein Rollback ausgeführt. JavaScript-Funktionen müssen innerhalb der Frist abgeschlossen sein oder ein auf der Fortdauer basierendes Modell implementieren, um die Ausführung zusammenzufassen oder fortzuführen.

Um die Entwicklung gespeicherter Prozeduren und Trigger zur Behandlung von Zeitlimits zu vereinfachen, geben alle Funktionen unter dem Sammlungsobjekt (zum Erstellen, Lesen, Ersetzen und Löschen von Dokumenten und Anhängen) einen booleschen Wert zurück, der angibt, ob dieser Vorgang abgeschlossen wird. Wenn dieser Wert "false" ist, weist dies darauf hin, dass das Zeitlimit in Kürze abläuft und die Prozedur die Ausführung beenden muss. Vorgänge, die vor dem ersten nicht angenommenen Speichervorgang in die Warteschlange gestellt wurden, werden garantiert abgeschlossen, wenn die gespeicherte Prozedur rechtzeitig abgeschlossen wird und keine weiteren Anforderungen in die Warteschlange stellt.

JavaScript-Funktionen sind auch an den Ressourcenverbrauch gebunden. DocumentDB reserviert den Durchsatz auf Basis der bereitgestellten Größe eines Datenbankkontos pro Sammlung. Der Durchsatz wird gemäß einer normierten Einheit des CPU-, Arbeitsspeicher- und E/A-Verbrauchs angegeben, der als Anforderungseinheit (Request Unit, RU) bezeichnet wird. JavaScript-Funktionen können eventuell große Mengen von Anforderungseinheiten innerhalb eines kurzen Zeitraums verbrauchen und werden möglicherweise eingeschränkt, wenn das Limit der Sammlung erreicht ist. Ressourcenintensive gespeicherte Prozeduren werden möglicherweise auch in Quarantäne gestellt, um die Verfügbarkeit grundlegender Datenbankvorgänge sicherzustellen.

### Beispiel: Massenimport von Daten in ein Datenbankprogramm
Nachfolgend finden Sie ein Beispiel einer gespeicherten Prozedur, die für den massenhaften Import von Dokumenten in eine Sammlung erstellt wurde. Beachten Sie, wie die gespeicherte Prozedur die gebundene Ausführung handhabt, indem der boolesche Rückgabewert von "createDocument" geprüft und dann die Anzahl der Dokumente verwendet wird, die bei jedem Aufruf der gespeicherten Prozedur eingefügt werden, um den mengenübergreifenden Fortschritt nachzuverfolgen und zu übernehmen.

	function bulkImport(docs) {
	    var collection = getContext().getCollection();
	    var collectionLink = collection.getSelfLink();
	
	    // The count of imported docs, also used as current doc index.
	    var count = 0;
	
	    // Validate input.
	    if (!docs) throw new Error("The array is undefined or null.");
	
	    var docsLength = docs.length;
	    if (docsLength == 0) {
	        getContext().getResponse().setBody(0);
	    }
	
	    // Call the create API to create a document.
	    tryCreate(docs[count], callback);
	
	    // Note that there are 2 exit conditions:
	    // 1) The createDocument request was not accepted. 
	    //    In this case the callback will not be called, we just call setBody and we are done.
	    // 2) The callback was called docs.length times.
	    //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
	    function tryCreate(doc, callback) {
	        var isAccepted = collection.createDocument(collectionLink, doc, callback);
	
	        // If the request was accepted, callback will be called.
	        // Otherwise report current count back to the client, 
	        // which will call the script again with remaining set of docs.
	        if (!isAccepted) getContext().getResponse().setBody(count);
	    }
	
	    // This is called when collection.createDocument is done in order to process the result.
	    function callback(err, doc, options) {
	        if (err) throw err;
	
	        // One more document has been inserted, increment the count.
	        count++;
	
	        if (count >= docsLength) {
	            // If we created all documents, we are done. Just set the response.
	            getContext().getResponse().setBody(count);
	        } else {
	            // Create next document.
	            tryCreate(docs[count], callback);
	        }
	    }
	}

## <a id="trigger"></a> Datenbanktrigger
### Vorangestellte Datenbanktrigger
DocumentDB stellt Trigger bereit, die durch einen für ein Dokument erfolgten Vorgang ausgeführt oder ausgelöst werden. Sie können z. B. einen vorangestellten Trigger angeben, wenn Sie ein Dokument erstellen. Dieser vorangestellte Trigger wird ausgeführt, bevor das Dokument erstellt wird. Nachfolgend finden Sie ein Beispiel dafür, wie vorangestellte Trigger zum Überprüfen der Eigenschaften eines zu erstellenden Dokuments verwendet werden können:

	var validateDocumentContentsTrigger = {
	    name: "validateDocumentContents",
	    body: function validate() {
	        var context = getContext();
	        var request = context.getRequest();
	
	        // document to be created in the current operation
	        var documentToCreate = request.getBody();
	
	        // validate properties
	        if (!("timestamp" in documentToCreate)) {
	            var ts = new Date();
	            documentToCreate["my timestamp"] = ts.getTime();
	        }
	
	        // update the document that will be created
	        request.setBody(documentToCreate);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.Create
	}


Der entsprechende clientseitige Node.js-Registrierungscode für den Trigger sieht wie folgt aus:

	// register pre-trigger
	client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
		.then(function (response) {
		    console.log("Created", response.resource);
		    var docToCreate = {
		        id: "DocWithTrigger",
		        event: "Error",
		        source: "Network outage"
		    };
	
		    // run trigger while creating above document 
		    var options = { preTriggerInclude: "validateDocumentContents" };
	
		    return client.createDocumentAsync(collection.self,
	              docToCreate, options);
		}, function (error) {
		    console.log("Error", error);
		})
	.then(function (response) {
	    console.log(response.resource); // document with timestamp property added
	}, function (error) {
	    console.log("Error", error);
	});


Vorangestellte Trigger können keine Eingabeparameter übernehmen. Das Anforderungsobjekt kann dazu verwendet werden, um die Anforderungsnachricht zu verändern, die dem Vorgang zugeordnet ist. Hier wird der vorangestellte Trigger mit der Erstellung eines Dokuments ausgeführt, und der Text der Anforderungsnachricht enthält das im JSON-Format zu erstellende Dokument.

Wenn Trigger registriert werden, können die Benutzer die Vorgänge angeben, mit denen sie ausgeführt werden können. Dieser Trigger wurde mit "TriggerOperation.Create" erstellt, d. h. Folgendes ist nicht zulässig:

	var options = { preTriggerInclude: "validateDocumentContents" };
	
	client.replaceDocumentAsync(docToReplace.self,
	              newDocBody, options)
	.then(function (response) {
	    console.log(response.resource);
	}, function (error) {
	    console.log("Error", error);
	});
	
	// Fails, can’t use a create trigger in a replace operation

### Nachgestellte Datenbanktrigger
Nachgestellte Trigger werden wie vorangestellte Trigger einem Vorgang zugeordnet, der für ein Dokument ausgeführt wird. Auch dieser Trigger übernimmt keine Eingabeparameter. Sie werden ausgeführt **nachdem** der Vorgang abgeschlossen ist und haben Zugriff auf die Antwortnachricht, die an den Client gesendet wird.

Das folgende Beispiel veranschaulicht nachgestellte Trigger:

	var updateMetadataTrigger = {
	    name: "updateMetadata",
	    body: function updateMetadata() {
	        var context = getContext();
	        var collection = context.getCollection();
	        var response = context.getResponse();
	
	        // document that was created
	        var createdDocument = response.getBody();
	
	        // query for metadata document
	        var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
	        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
	            updateMetadataCallback);
	        if(!accept) throw "Unable to update metadata, abort";
	 
	        function updateMetadataCallback(err, documents, responseOptions) {
	            if(err) throw new Error("Error" + err.message);
	                     if(documents.length != 1) throw 'Unable to find metadata document';
	                     
	                     var metadataDocument = documents[0];
	                     
	                     // update metadata
	                     metadataDocument.createdDocuments += 1;
	                     metadataDocument.createdNames += " " + createdDocument.id;
	                     var accept = collection.replaceDocument(metadataDocument._self,
	                           metadataDocument, function(err, docReplaced) {
	                                  if(err) throw "Unable to update metadata, abort";
	                           });
	                     if(!accept) throw "Unable to update metadata, abort";
	                     return;                    
	        }																							
	    },
	    triggerType: TriggerType.Post,
	    triggerOperation: TriggerOperation.All
	}


Der Trigger kann, wie im folgenden Beispiel gezeigt, registriert werden.

	// register post-trigger
	client.createTriggerAsync(collection.self, updateMetadataTrigger)
		.then(function(createdTrigger) { 
		    var docToCreate = { 
		        name: "artist_profile_1023",
		        artist: "The Band",
		        albums: ["Hellujah", "Rotators", "Spinning Top"]
		    };
	
		    // run trigger while creating above document 
		    var options = { postTriggerInclude: "updateMetadata" };
		
		    return client.createDocumentAsync(collection.self,
	              docToCreate, options);
		}, function(error) {
		    console.log("Error" , error);
		})
	.then(function(response) {
	    console.log(response.resource); 
	}, function(error) {
	    console.log("Error" , error);
	});


Dieser Trigger fragt das Metadatendokument ab und aktualisiert es mit den Details zum neu erstellten Dokument.

Ein wichtiges Element ist die **transaktionale** Ausführung von Triggern in DocumentDB. Dieser nachgestellte Trigger wird als Teil derselben Transaktion wie bei der Erstellung des ursprünglichen Dokuments ausgeführt. Daher schlägt die gesamte Transaktion fehl und es wird ein Rollback ausgeführt, wenn vom nachgestellten Trigger eine Ausnahme ausgelöst wird (wenn das Metadatendokument z. B. nicht aktualisiert werden konnte). Es wird kein Dokument erstellt und stattdessen eine Ausnahme zurückgegeben.

##<a id="udf"></a>Benutzerdefinierte Funktionen
Mithilfe der benutzerdefinierten Funktionen (UDFs) kann die Grammatik der SQL-Abfragesprache von DocumentDB erweitert und eine benutzerdefinierte Geschäftslogik implementiert werden. Sie können ausschließlich innerhalb von Abfragen aufgerufen werden. Sie haben keinen Zugriff auf das Kontextobjekt und sind als JavaScript-Komponente vorgesehen, die ausschließlich der Berechnung dient. Daher können benutzerdefinierte Funktionen auf sekundären Replikaten des DocumentDB-Diensts ausgeführt werden.
 
Das folgende Beispiel erstellt eine UDF, um die Einkommenssteuer auf Basis der Sätze verschiedener Einkommensgruppen zu berechnen. Dann werden sie innerhalb einer Abfrage ausgeführt, um alle Personen zu finden, die mehr als 20.000 $ Steuern gezahlt haben.

	var taxUdf = {
	    name: "tax",
	    body: function tax(income) {
	
	        if(income == undefined) 
	            throw 'no input';
	
	        if (income < 1000) 
	            return income * 0.1;
	        else if (income < 10000) 
	            return income * 0.2;
	        else
	            return income * 0.4;
	    }
	}


Die UDF kann anschließend wie im folgenden Beispiel in Abfragen verwendet werden:

	// register UDF
	client.createUserDefinedFunctionAsync(collection.self, taxUdf)
		.then(function(response) { 
		    console.log("Created", response.resource);
	
		    var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
		    return client.queryDocuments(collection.self,
	               query).toArrayAsync();
		}, function(error) {
		    console.log("Error" , error);
		})
	.then(function(response) {
	    var documents = response.feed;
	    console.log(response.resource); 
	}, function(error) {
	    console.log("Error" , error);
	});

## JavaScript-Language Integrated Query (LINQ)-API
Zusätzlich zu Abfragen mithilfe der SQL-Grammatik von DocumentDB ermöglicht das serverseitige SDK die Durchführung optimierter Abfragen mithilfe einer flüssigen JavaScript-Schnittstelle, die keinerlei SQL-Kenntnisse voraussetzt. Mit der JavaScript-Abfrage-API können Sie programmgesteuert Abfragen erstellen, indem Sie unter Verwendung einer Syntax, die mit den integrierten und weit verbreiteten JavaScript-Bibliotheken von ECMAScript5 wie lodash vergleichbar ist, Prädikatfunktionen in verkettbare Funktionsaufrufe übergeben. Abfragen werden von der JavaScript-Laufzeit zur effizienten Ausführung mithilfe von DocumentDB-Indizes analysiert.

> [AZURE.NOTE]`__` (doppelter Unterstrich) ist ein Alias für `getContext().getCollection()`.
> <br/>
> Sie können also `__` oder `getContext().getCollection()` verwenden, um auf die JavaScript-Abfrage-API zuzugreifen.

Folgende Funktionen werden unterstützt: <ul> <li> <b>chain() ... .value([callback] [, options])</b> <ul> <li> Startet einen verketteten Aufruf, der mit „value()“ abgeschlossen werden muss. </li> </ul> </li> <li> <b>filter(predicateFunction [, options] [, callback])</b> <ul> <li> Filtert die Eingabe anhand einer Prädikatfunktion, die „true/false“ zurückgibt, um Eingabedokumente in den resultierenden Satz einzuschließen oder auszuschließen. Diese Funktion verhält sich ähnlich wie eine WHERE-Klausel in SQL. </li> </ul> </li> <li> <b>map(transformationFunction [, options] [, callback])</b> <ul> <li> Wendet anhand einer angegebenen Transformationsfunktion eine Projektion an, die jedes Eingabeelement einem JavaScript-Objekt oder -Wert zuordnet. Diese Funktion verhält sich ähnlich wie eine SELECT-Klausel in SQL. </li> </ul> </li> <li> <b>pluck([propertyName] [, options] [, callback])</b> <ul> <li> Dies ist eine Kurzform für eine Zuordnung, die den Wert einer einzelnen Eigenschaft aus jedem Eingabeelement extrahiert. </li> </ul> </li> <li> <b>flatten([isShallow] [, options] [, callback])</b> <ul> <li> Kombiniert und vereinfacht Arrays aus jedem Eingabeelement zu einem einzigen Array. Diese Funktion verhält sich ähnlich wie „SelectMany“ in LINQ. </li> </ul> </li> <li> <b>sortBy([predicate] [, options] [, callback])</b> <ul> <li> Erzeugt einen neuen Satz von Dokumenten, indem die Dokumente im Eingabedokument-Datenstrom anhand des angegebenen Prädikats in aufsteigender Reihenfolge sortiert werden. Diese Funktion verhält sich ähnlich wie eine ORDER BY-Klausel in LINQ. </li> </ul> </li> <li> <b>sortByDescending([predicate] [, options] [, callback])</b> <ul> <li> Erzeugt einen neuen Satz von Dokumenten, indem die Dokumente im Eingabedokument-Datenstrom anhand des angegebenen Prädikats in absteigender Reihenfolge sortiert werden. Diese Funktion verhält sich ähnlich wie eine ORDER BY X DESC-Klausel in SQL. </li> </ul> </li> </ul>


Bei der Verwendung innerhalb von Prädikat- und/oder Selektorfunktionen werden die folgenden JavaScript-Konstrukte automatisch optimiert, damit sie direkt für DocumentDB-Indizes ausgeführt werden:

* Einfache Operatoren: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literale, einschließlich des Objektliterals: {}
* var, return

Die folgenden JavaScript-Konstrukte werden nicht für DocumentDB-Indizes optimiert:

* Ablaufsteuerung (z. B. if, for, while)
* Funktionsaufrufe

Weitere Informationen finden Sie in unseren [serverseitigen JSDocs](http://dl.windowsazure.com/documentDB/jsserverdocs/).

### Beispiel: Schreiben einer gespeicherten Prozedur mit der JavaScript-Abfrage-API

Das folgende Codebeispiel zeigt, wie die JavaScript-Abfrage-API im Kontext einer gespeicherten Prozedur verwendet werden kann. Die gespeicherte Prozedur fügt ein Dokument ein (angegeben durch einen Eingabeparameter) und aktualisiert mit der `__.filter()`-Methode ein Metadatendokument basierend auf der Size-Eigenschaft des Eingabedokuments mit „minSize“, „maxSize“ und „totalSize“.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## Cheat Sheet für SQL und Javascript
In der folgenden Tabelle sind verschiedene SQL-Abfragen und die entsprechenden JavaScript-Abfragen aufgeführt.

Wie bei SQL-Abfragen muss bei Key-Eigenschaften von Dokumenten (z. B. `doc.id`) die Groß-/Kleinschreibung beachtet werden.

<br/> <table border="1" width="100%"> <colgroup> <col span="1" style="width: 40%;"> <col span="1" style="width: 40%;"> <col span="1" style="width: 20%;"> </colgroup> <tbody> <tr> <th>SQL</th> <th>JavaScript-Abfrage-API</th> <th>Details</th> </tr> <tr> <td> <pre> SELECT * FROM docs </pre> </td> <td> <pre> \_\_.map(function(doc) { return doc; }); </pre> </td> <td>Alle Dokumente (umgebrochen mit Fortsetzungstoken) bleiben unverändert.</td> </tr> <tr> <td> <pre> SELECT docs.id, docs.message AS msg, docs.actions FROM docs </pre> </td> <td> <pre> \_\_.map(function(doc) { return { id: doc.id, msg: doc.message, actions: doc.actions }; }); </pre> </td> <td>Projiziert die ID, Meldung (Alias für „msg“) und Aktion aus allen Dokumenten.</td> </tr> <tr> <td> <pre> SELECT * FROM docs WHERE docs.id="X998\_Y998" </pre> </td> <td> <pre> \_\_.filter(function(doc) { return doc.id === "X998\_Y998"; }); </pre> </td> <td>Fragt Dokumente mit dem folgenden Prädikat ab: id = "X998\_Y998".</td> </tr> <tr> <td> <pre> SELECT * FROM docs WHERE ARRAY\_CONTAINS(docs.Tags, 123) </pre> </td> <td> <pre> \_\_.filter(function(x) { return x.Tags && x.Tags.indexOf(123) > -1; }); </pre> </td> <td>Fragt Dokumente mit einer Tags-Eigenschaft ab, wobei „Tags“ ein Array mit dem Wert 123 ist.</td> </tr> <tr> <td> <pre> SELECT docs.id, docs.message AS msg FROM docs WHERE docs.id="X998\_Y998" </pre> </td> <td> <pre> \_\_.chain() .filter(function(doc) { return doc.id === "X998\_Y998"; }) .map(function(doc) { return { id: doc.id, msg: doc.message }; }) .value(); </pre> </td> <td>Fragt Dokumente mit einem Prädikat, id = "X998\_Y998", ab und projiziert anschließend die ID und die Meldung (Alias für „msg“).</td> </tr> <tr> <td> <pre> SELECT VALUE tag FROM docs JOIN tag IN docs.Tags ORDER BY docs.\_ts </pre> </td> <td> <pre> \_\_.chain() .filter(function(doc) { return doc.Tags && Array.isArray(doc.Tags); }) .sortBy(function(doc) { return doc.\_ts; }) .pluck("Tags") .flatten() .value() </pre> </td> <td>Filtert nach Dokumenten mit einer Array-Eigenschaft „Tags“, sortiert die resultierenden Dokumente anhand der Zeitstempel-Systemeigenschaft „\_ts“ und projiziert und vereinfacht anschließend das Tags-Array.</td> </tr> </tbody> </table>

## Laufzeitunterstützung
Das [serverseitige DocumentDB JavaScript-SDK](http://dl.windowsazure.com/documentDB/jsserverdocs/) unterstützt die meisten gängigen Funktionen der JavaScript-Sprache gemäß dem [ECMA-262](documentdb-interactions-with-resources.md)-Standard.

### Sicherheit
Gespeicherte Prozeduren und Trigger werden bei JavaScript in einer Sandkastenlösung verwaltet, damit die Auswirkungen eines Skripts nicht zu den anderen Skripts gelangen, ohne die Momentaufnahmetransaktionsisolation der Datenbankschicht zu durchlaufen. Die Laufzeitumgebungen werden in einem Pool zusammengefasst, aber nach jeder Ausführung vom Kontext bereinigt. Daher sind sie untereinander garantiert vor unbeabsichtigten Nebeneffekten geschützt.

### Vorkompilierung
Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen (UDFs) werden implizit in das Bytecodeformat vorkompiliert, um den Kompilierungsaufwand zum Zeitpunkt des jeweiligen Skriptaufrufs zu vermeiden. Dadurch wird sichergestellt, dass gespeicherte Prozeduren schnell aufgerufen werden können und kompakt sind.

## Client-SDK-Unterstützung
Zusätzlich zum [Node.js](http://dl.windowsazure.com/documentDB/nodedocs/)-Client unterstützt DocumentDB [.NET](https://msdn.microsoft.com/library/azure/dn783362.aspx)-, [Java](http://dl.windowsazure.com/documentdb/javadoc/)-, [ JavaScript](http://dl.windowsazure.com/documentDB/jsclientdocs/)- und [Python](http://dl.windowsazure.com/documentDB/pythondocs/)-SDKs. Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen können jedem dieser SDKs erstellt und ausgeführt werden. Das folgende Beispiel zeigt, wie eine gespeicherte Prozedur mithilfe des .NET-Clients erstellt und ausgeführt wird. Beachten Sie, wie die .NET-Typen als JSON an die gespeicherte Prozedur übergeben und eingelesen werden.

	var markAntiquesSproc = new StoredProcedure
	{
	    Id = "ValidateDocumentAge",
	    Body = @"
	            function(docToCreate, antiqueYear) {
	                var collection = getContext().getCollection();    
	                var response = getContext().getResponse();    
	
			        if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
				        docToCreate.antique = true;
			        }
	
	                collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
	                    function(err, docCreated, options) { 
	                        if(err) throw new Error('Error while creating document: ' + err.message);                              
	                        if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
	                        response.setBody(docCreated);
	                });
	 	    }"
	};
	
	// register stored procedure
	StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(collection.SelfLink, markAntiquesSproc);
	dynamic document = new Document() { Id = "Borges_112" };
	document.Title = "Aleph";
	document.Year = 1949;
	
	// execute stored procedure
	Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(createdStoredProcedure.SelfLink, document, 1920);


Dieses Beispiel zeigt, wie mit dem [.NET-SDK](https://msdn.microsoft.com/library/azure/dn783362.aspx) ein vorangestellter Trigger und ein Dokument mit aktiviertem Trigger erstellt werden.

	Trigger preTrigger = new Trigger()
	{
	    Id = "CapitalizeName",
	    Body = @"function() {
	        var item = getContext().getRequest().getBody();
	        item.id = item.id.toUpperCase();
	        getContext().getRequest().setBody(item);
	    }",
	    TriggerOperation = TriggerOperation.Create,
	    TriggerType = TriggerType.Pre
	};
	
	Document createdItem = await client.CreateDocumentAsync(collection.SelfLink, new Document { Id = "documentdb" },
	    new RequestOptions
	    {
	        PreTriggerInclude = new List<string> { "CapitalizeName" },
	    });


Das nachfolgende Beispiel veranschaulicht die Erstellung einer benutzerdefinierten Funktion (UDF) und deren Verwendung in einer [SQL-Abfrage von DocumentDB](documentdb-sql-query.md).

	UserDefinedFunction function = new UserDefinedFunction()
	{
	    Id = "LOWER",
	    Body = @"function(input) 
		{
	        return input.toLowerCase();
	    }"
	};
	
	foreach (Book book in client.CreateDocumentQuery(collection.SelfLink,
	    "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
	{
	    Console.WriteLine("Read {0} from query", book);
	}

## REST-API
Alle DocumentDB-Vorgänge können RESTful-basiert ausgeführt werden. Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen können mithilfe von HTTP POST unter einer Sammlung registriert werden. Nachfolgend sehen Sie ein Beispiel für die Registrierung einer gespeicherten Prozedur:

	POST https://<url>/sprocs/ HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
	
	
	var x = {
	  "name": "createAndAddProperty",
	  "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
	            var collectionManager = getContext().getCollection();
	            collectionManager.createDocument(
	                collectionManager.getSelfLink(),
	                docToCreate,
	                function(err, docCreated) {
	                  if(err) throw new Error('Error:  ' + err.message);
	                  docCreated[addedPropertyName] = addedPropertyValue;
	                  getContext().getResponse().setBody(docCreated);
	                });
	        }
	}


Die gespeicherte Prozedur wird durch Ausführung einer POST-Anforderung für den URI "dbs/sehcAA==/colls/sehcAIE2Qy4=/sprocs" registriert, wobei der Textteil die zu erstellende gespeicherte Prozedur enthält. Trigger und UDFs können auf ähnliche Weise registriert werden: durch einen POST bezüglich /triggers bzw. /udfs.
Diese gespeicherte Prozedur kann dann durch Anlegen einer POST-Anforderung für ihren Ressourcenlink ausgeführt werden:

	POST https://<url>/sprocs/<sproc> HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
	
	
	[ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Hier wird die Eingabe für die gespeicherte Prozedur im Anforderungstext übergeben. Beachten Sie, dass die Eingabe als JSON-Array von Eingabeparametern übergeben wird. Die gespeicherte Prozedur übernimmt die erste Eingabe als Dokument, bei dem es sich um einen Antworttext handelt. Dabei erhalten wir folgende Antwort:

	HTTP/1.1 200 OK
	 
	{ 
	  name: 'TestDocument',
	  book: ‘Autumn of the Patriarch’,
	  id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
	  ts: 1407830727,
	  self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
	  etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
	  attachments: ‘attachments/’,
	  Price: 200
	}


Trigger können im Gegensatz zu gespeicherten Prozeduren nicht direkt ausgeführt werden. Stattdessen werden sie als Teil eines Vorgangs für ein Dokument ausgeführt. Wir können die auszuführenden Trigger unter Verwendung von HTTP-Headern über eine Anforderung angeben. Das folgende Beispiel ist eine Anforderung zum Erstellen eines Dokuments.

	POST https://<url>/docs/ HTTP/1.1
	authorization: <<auth>>
	x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
	x-ms-documentdb-pre-trigger-include: validateDocumentContents 
	x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
	
	
	{
	   "name": "newDocument",
	   “title”: “The Wizard of Oz”,
	   “author”: “Frank Baum”,
	   “pages”: 92
	}


Hier wird der mit der Anforderung auszuführende vorangestellte Trigger im Header "x-ms-documentdb-pre-trigger-include" angegeben. Entsprechend werden alle nachgestellten Trigger im Header "x-ms-documentdb-post-trigger-include" angegeben. Beachten Sie, dass sowohl vorangestellte als auch nachgestellte Trigger für eine bestimmte Anforderung angegeben werden können.

## Beispielcode

Weitere Beispiele für serverseitigen Code (einschließlich [upsert](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/upsert.js), [Massenlöschung](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/bulkDelete.js) und [Aktualisieren](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/update.js)) finden Sie in unserem [Github-Repository](https://github.com/Azure/azure-documentdb-js/tree/master/server-side/samples).

Möchten Sie Ihre fantastische gespeicherte Prozedur freigeben? Bitte senden Sie uns eine Pull-Anforderung!

## Nächste Schritte

Sobald Sie eine oder mehrere gespeicherte Prozeduren, Trigger und benutzerdefinierten Funktionen erstellt haben, können Sie sie laden und in Azure-Vorschauportal mit dem Skript-Explorer anzeigen. Weitere Informationen finden Sie unter [Anzeigen von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen mit dem DocumentDB-Skript-Explorer](documentdb-view-scripts.md).

Weitere Informationen zur serverseitigen DocumentDB-Programmierung finden Sie auch in den folgenden Referenzen und Ressourcen:

- [Azure DocumentDB SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [JSON](http://www.json.org/) 
- [JavaScript-ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
-	[JavaScript – JSON-Typsystem](http://www.json.org/js.html) 
-	[Sichere und portable Erweiterbarkeit von Datenbanken](http://dl.acm.org/citation.cfm?id=276339) 
-	[Dienstorientierte Datenbankarchitektur](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
-	[Hosten der .NET-Lautzeitumgebung in Microsoft SQL Server](http://dl.acm.org/citation.cfm?id=1007669)  

<!---HONumber=Oct15_HO4-->