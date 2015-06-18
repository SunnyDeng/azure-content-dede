<properties 
	pageTitle="DocumentDB-Programmierung: Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen | Azure" 
	description="Erfahren Sie, wie Sie mit Microsoft Azure DocumentDB gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen systemeigen in JavaScript schreiben." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="mimig"/>

# DocumentDB-Programmierung: Gespeicherte Prozeduren, Trigger und UDFs

Erfahren Sie, wie Entwickler dank der in die DocumentDB-Sprache integrierten transaktionalen Ausführung von JavaScript **gespeicherte Prozeduren**, **Trigger** und **benutzerdefinierte Funktionen (User Defined Functions, UDFs) ** in systemeigenem JavaScript erstellen können. Dadurch können Sie eine Anwendungslogik schreiben, die direkt auf den Partitionen des Datenbankspeichers bereitgestellt und ausgeführt werden kann. 

Für den Beginn empfiehlt sich folgendes Video, in dem Andrew Liu das serverseitige Programmiermodell von DocumentDB kurz vorstellt. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Kehren Sie dann zurück zu diesem Artikel, in dem Sie die Antworten auf die folgenden Fragen erfahren:  

- Wie schreibe ich eine gespeicherte Prozedur, einen Trigger oder eine UDF mit JavaScript?
- Wie gewährleistet DocumentDB ACID?
- Wie funktionieren Transaktionen in DocumentDB?
- Was sind vorangestellte Trigger und nachgestellte Trigger, und wie schreibe ich sie?
- Wie kann ich eine gespeicherte Prozedur, einen Trigger oder eine UDF auf REST-konforme Weise mithilfe von HTTP registrieren und ausführen?
- Welche DocumentDB-SDKs sind für das Erstellen und Ausführen von gespeicherten Prozeduren, Triggern und UDFs verfügbar?

##Einführung

Dieser Ansatz von *"JavaScript als modernes T-SQL"* befreit die Anwendungsentwickler von der Komplexität durch nicht übereinstimmende Systeme und objektrelationale Zuordnungstechnologien. Er hat auch eine Reihe spezifischer Vorteile, die zum Erstellen funktionsreicher Anwendungen genutzt werden können:  

-	**Prozedurlogik:** JavaScript bietet als Programmiersprache auf höherer Ebene eine umfangreiche und vertraute Benutzeroberfläche zur Darstellung der Geschäftslogik. Sie können komplexe Abfolgen von Vorgängen mit direkterem Zugriff auf die Daten ausführen.

-	**Atomarische Transaktionen:** DocumentDB gewährleistet, dass die innerhalb einer einzelnen gespeicherten Prozedur oder in einem Trigger ausgeführten Datenbankvorgänge atomarisch sind. Dadurch kann eine Anwendung zusammengehörige Vorgänge in einem einzelnen Batch kombinieren, damit entweder alle Vorgänge oder keiner dieser Vorgänge erfolgreich ausgeführt werden kann. 

-	**Leistung:** Die Tatsache, dass JSON an sich dem JavaScript-Sprachsystem zugeordnet ist und auch die Basiseinheit für die Speicherung in DocumentDB darstellt, ermöglicht eine Reihe von Optimierungen wie die bequeme Realisierung von JSON-Dokumenten im Pufferpool und deren bedarfsgesteuerte Bereitstellung für den ausführenden Code. Es gibt weitere Leistungsvorteile im Zusammenhang mit der Übertragung der Geschäftslogik auf die Datenbank:
	-	Batchverarbeitung - Entwickler können Vorgänge wie Einlagen gruppieren und dann zusammen übermitteln. Der Aufwand für die Latenz des Netzwerkdatenverkehrs und der erhöhte Speicheraufwand beim Erstellen separater Transaktionen werden erheblich verringert. 
	-	Vorkompilierung - DocumentDB führt für gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen (UDFs) Vorkompilierungen durch, um den Aufwand der JavaScript-Kompilierung für die einzelnen Aufrufe zu vermeiden. Der Mehraufwand für die Erstellung des Bytecodes für die prozedurale Logik wird auf ein Minimum gesenkt.
	-	Sequenzierung - Viele Vorgänge benötigen einen Nebeneffekt (Auslöser oder Trigger), der möglicherweise die Ausführung eines oder vieler sekundärer Speichervorgänge einbezieht. Dies ist abgesehen von der der Atomarität effektiver, wenn der Vorgang auf den Server verlagert wird. 
-	**Kapselung:** Die Geschäftslogik kann mithilfe gespeicherter Prozeduren an einem Ort zusammengefasst werden. Das hat zwei Vorteile:
	-	Es wird eine Abstraktionsschicht über den Rohdaten hinzugefügt, die es Datenarchitekten gestattet, ihre Anwendungen unabhängig von den Daten zu entwickeln. Dies ist aufgrund der komplizierten Annahmen, die bei der direkten Behandlung der Daten zur Anwendung hinzugefügt werden müssen, insbesondere bei schemafreien Daten von Vorteil.  
	-	Durch diese Abstraktion können Unternehmen ihre Daten schützen, indem sie den Zugriff über die Scripts optimieren.  

Die Erstellung und Ausführung von Triggern, gespeicherten Prozeduren und benutzerdefinierten Abfrageoperatoren wird auf vielen Plattformen, einschließlich .NET, Node.js und JavaScript, über die [REST-API](https://msdn.microsoft.com/library/azure/dn781481.aspx) und [Client-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) unterstützt. **In diesem Lernprogramm wird das [Node.js-SDK](http://dl.windowsazure.com/documentDB/nodedocs/)** verwendet, um die Syntax und Verwendung von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen (User Defined Functions, UDFs) zu veranschaulichen.   

##Beispiel: Schreiben einer einfachen gespeicherten Prozedur 
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

##Beispiel: Schreiben einer gespeicherten Prozedur zum Erstellen eines Dokuments 
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
		        book: "The Hitchhiker's Guide to the Galaxy",
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

Das beschriebene Beispiel hat die Verwendung gespeicherter Prozeduren veranschaulicht. Trigger und benutzerdefinierte Funktionen (UDFs) werden später in diesem Lernprogramm behandelt. Zunächst wollen wir die allgemeinen Merkmale der Unterstützung der Skripterstellung in DocumentDB betrachten.  

##Laufzeitunterstützung
[Das serverseitige DocumentDB JavaScript-SDK ](http://dl.windowsazure.com/documentDB/jsserverdocs/) unterstützt die meisten gängigen Funktionen der JavaScript-Sprache gemäß des [ECMA-262](documentdb-interactions-with-resources.md)-Standards.
 
##Transaktionen
Eine Transaktion in einer typischen Datenbank kann als Folge von Vorgängen definiert werden, die als einzelne logische Arbeitseinheit ausgeführt wird. Jede Transaktion bietet die **ACID-Garantien**. ACID (Atomicity, Consistency, Isolation, Durability) ist ein bekanntes Akronym, das für vier Eigenschaften steht - Atomarität, Konsistenz, Isolation und Dauerhaftigkeit.  

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
	
	        // swap the two players' items
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
	

##Commit und Rollback
Transaktionen sind fest im JavaScript-Programmiermodell von DocumentDB integriert. Innerhalb einer JavaScript-Funktion werden alle Vorgänge automatisch von einer einzelnen Transaktion umschlossen. Wenn das JavaScript ohne Ausnahmen abgeschlossen wird, werden die für die Datenbank vorgenommenen Vorgänge bestätigt. Die Anweisungen "BEGIN TRANSACTION" und "COMMIT TRANSACTION" für relationale Datenbanken sind in DocumentDB praktisch eingeschlossen.  
 
Wenn von dem Skript eine Ausnahme weitergegeben wird, führt die JavaScript-Laufzeitumgebung von DocumentDB ein Rollback für die gesamte Transaktion aus. Wie im vorherigen Beispiel gezeigt, ist die Auslösung einer Ausnahme in DocumentDB tatsächlich mit dem Befehl "ROLLBACK TRANSACTION" vergleichbar. 

###Datenkonsistenz
Gespeicherte Prozeduren und Trigger werden immer für das primäre Replikat der DocumentDB-Sammlung ausgeführt. Dadurch wird sichergestellt, dass innerhalb von gespeicherten Prozeduren erfolgte Lesevorgänge eine hohe Konsistenz bieten. Abfragen, die benutzerdefinierte Funktionen verwenden, können mit dem primären oder einem beliebigen sekundären Replikat ausgeführt werden. Es wird jedoch durch die Auswahl des geeigneten Replikats sichergestellt, dass die geforderte Konsistenz erreicht wird.

##Sicherheit
Gespeicherte Prozeduren und Trigger werden bei JavaScript in einer Sandkastenlösung verwaltet, damit die Auswirkungen eines Skripts nicht zu den anderen Skripts gelangen, ohne die Momentaufnahmetransaktionsisolation der Datenbankschicht zu durchlaufen. Die Laufzeitumgebungen werden in einem Pool zusammengefasst, aber nach jeder Ausführung vom Kontext bereinigt. Daher sind sie untereinander garantiert vor unbeabsichtigten Nebeneffekten geschützt.

##Vorkompilierung
Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen (UDFs) werden implizit in das Bytecodeformat vorkompiliert, um den Kompilierungsaufwand zum Zeitpunkt des jeweiligen Skriptaufrufs zu vermeiden. Dadurch wird sichergestellt, dass gespeicherte Prozeduren schnell aufgerufen werden können und kompakt sind.

##<a id="trigger"></a> Trigger
###Vorangestellte Trigger
DocumentDB stellt Trigger bereit, die durch einen für ein Dokument erfolgten Vorgang ausgeführt oder ausgelöst werden. Sie können z. B. einen vorangestellten Trigger angeben, wenn Sie ein Dokument erstellen. Dieser vorangestellte Trigger wird ausgeführt, bevor das Dokument erstellt wird. Nachfolgend finden Sie ein Beispiel dafür, wie vorangestellte Trigger zum Überprüfen der Eigenschaften eines zu erstellenden Dokuments verwendet werden können:

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

Wenn Trigger registriert werden, können die Benutzer die Vorgänge angeben, mit denen sie ausgeführt werden können. Dieser Trigger wurde mit "TriggerOperation.Create" erstellt, d. h. Folgendes ist nicht zulässig:

	var options = { preTriggerInclude: "validateDocumentContents" };
	
	client.replaceDocumentAsync(docToReplace.self,
	              newDocBody, options)
	.then(function (response) {
	    console.log(response.resource);
	}, function (error) {
	    console.log("Error", error);
	});
	
	// Fails, can't use a create trigger in a replace operation

###Nachgestellte Trigger
Nachgestellte Trigger werden wie vorangestellte Trigger einem Vorgang zugeordnet, der für ein Dokument ausgeführt wird. Auch dieser Trigger übernimmt keine Eingabeparameter. Sie werden ausgeführt, **nachdem** der Vorgang abgeschlossen ist, und haben Zugriff auf die Antwortnachricht, die an den Client gesendet wird.   

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

Ein wichtiges Element ist die **transaktionale** Ausführung von Triggern in DocumentDB. Dieser nachgestellte Trigger wird als Teil derselben Transaktion wie bei der Erstellung des ursprünglichen Dokuments ausgeführt. Daher schlägt die gesamte Transaktion fehl und es wird ein Rollback ausgeführt, wenn vom nachgestellten Trigger eine Ausnahme ausgelöst wird (wenn das Metadatendokument z. B. nicht aktualisiert werden konnte). Es wird kein Dokument erstellt und stattdessen eine Ausnahme zurückgegeben.  

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

##Gebundene Ausführung
Alle DocumentDB-Vorgänge müssen innerhalb der vom Server angegebenen Anforderungstimeoutdauer abgeschlossen werden. Diese Einschränkung gilt auch für JavaScript-Funktionen (gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen). Wenn ein Vorgang nicht innerhalb dieser Frist abgeschlossen werden kann, wird für die Transaktion ein Rollback ausgeführt. JavaScript-Funktionen müssen innerhalb der Frist abgeschlossen sein oder ein auf der Fortdauer basierendes Modell implementieren, um die Ausführung zusammenzufassen oder fortzuführen.  

Um die Entwicklung gespeicherter Prozeduren und Trigger zur Behandlung von Zeitlimits zu vereinfachen, geben alle Funktionen unter dem Sammlungsobjekt (zum Erstellen, Lesen, Ersetzen und Löschen von Dokumenten und Anhängen) einen booleschen Wert zurück, der angibt, ob dieser Vorgang abgeschlossen wird. Wenn dieser Wert "false" ist, weist dies darauf hin, dass das Zeitlimit in Kürze abläuft und die Prozedur die Ausführung beenden muss.  Vorgänge, die vor dem ersten nicht angenommenen Speichervorgang in die Warteschlange gestellt wurden, werden garantiert abgeschlossen, wenn die gespeicherte Prozedur rechtzeitig abgeschlossen wird und keine weiteren Anforderungen in die Warteschlange stellt.  

JavaScript-Funktionen sind auch an den Ressourcenverbrauch gebunden. DocumentDB reserviert den Durchsatz auf Basis der bereitgestellten Größe eines Datenbankkontos pro Sammlung. Der Durchsatz wird gemäß einer normierten Einheit des CPU-, Arbeitsspeicher- und E/A-Verbrauchs angegeben, der als Anforderungseinheit (Request Unit, RU) bezeichnet wird. JavaScript-Funktionen können eventuell große Mengen von Anforderungseinheiten innerhalb eines kurzen Zeitraums verbrauchen und werden möglicherweise eingeschränkt, wenn das Limit der Sammlung erreicht ist. Ressourcenintensive gespeicherte Prozeduren werden möglicherweise auch in Quarantäne gestellt, um die Verfügbarkeit grundlegender Datenbankvorgänge sicherzustellen.  

##Massenimport von Daten
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
	    //    In this case all documents were created and we don't need to call tryCreate anymore. Just call setBody and we are done.
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


##REST-konformes Ausführen von Vorgängen
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
	  book: 'Autumn of the Patriarch',
	  id: 'V7tQANV3rAkDAAAAAAAAAA==',
	  ts: 1407830727,
	  self: 'dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/',
	  etag: '6c006596-0000-0000-0000-53e9cac70000',
	  attachments: 'attachments/',
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
	   "title": "The Wizard of Oz",
	   "author": "Frank Baum",
	   "pages": 92
	}


Hier wird der mit der Anforderung auszuführende vorangestellte Trigger im Header "x-ms-documentdb-pre-trigger-include" angegeben. Entsprechend werden alle nachgestellten Trigger im Header "x-ms-documentdb-post-trigger-include" angegeben. Beachten Sie, dass sowohl vorangestellte als auch nachgestellte Trigger für eine bestimmte Anforderung angegeben werden können.

##SDK-Unterstützung
Zusätzlich zum [Node.js](http://dl.windowsazure.com/documentDB/nodedocs/)-Client unterstützt DocumentDB [.NET](https://msdn.microsoft.com/library/azure/dn783362.aspx)-, [Java](http://dl.windowsazure.com/documentdb/javadoc/)-, [JavaScript](http://dl.windowsazure.com/documentDB/jsclientdocs/)- und [Python-SDKs](http://dl.windowsazure.com/documentDB/pythondocs/). Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen können jedem dieser SDKs erstellt und ausgeführt werden. Das folgende Beispiel zeigt, wie eine gespeicherte Prozedur mithilfe des .NET-Clients erstellt und ausgeführt wird. Beachten Sie, wie die .NET-Typen als JSON an die gespeicherte Prozedur übergeben und eingelesen werden.

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


Dieses Beispiel zeigt, wie mit dem [.NET SDK](https://msdn.microsoft.com/library/azure/dn783362.aspx) ein vorangestellter Trigger und ein Dokument mit aktiviertem Trigger erstellt wird. 

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
##Nächste Schritte

Über die [Azure DocumentDB-SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) können Sie zusätzliche gespeicherte Prozeduren, Trigger und UDFs erstellen.


##Referenzen

- JSON [http://www.json.org/](http://www.json.org/) 
- JavaScript ECMA-262 [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm )
-	JavaScript - JSON-Typsystem [http://www.json.org/js.html](http://www.json.org/js.html) 
-	Sichere und portable Erweiterbarkeit von Datenbanken - [http://dl.acm.org/citation.cfm?id=276339](http://dl.acm.org/citation.cfm?id=276339) 
-	Dienstorientierte Datenbankarchitektur  - [http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
-	Hosten der .NET-Laufzeitumgebung in Microsoft SQL Server - [http://dl.acm.org/citation.cfm?id=1007669](http://dl.acm.org/citation.cfm?id=1007669) 

<!--HONumber=49--> 