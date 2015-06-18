<properties 
	pageTitle="Indizierungsrichtlinien für DocumentDB | Azure" 
	description="Erfahren Sie, wie die Indizierung in DocumentDB funktioniert, und wie Sie die Indizierungsrichtlinie konfigurieren." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/13/2015" 
	ms.author="mimig"/>


Indizierungsrichtlinien für DocumentDB
============================

DocumentDB ist eine echte schemafreie Datenbank. Es setzt kein Schema für die indizierten JSON-Dokumente voraus. Dies ermöglicht Ihnen eine schnelle Definition und Iteration von Anwendungsdatenmodellen. Während Sie Dokumente zu einer Sammlung hinzufügen, werden alle Dokumenteigenschaften von DocumentDB automatisch indiziert und stehen dann für Sie für Abfragen bereit. Die automatische Indizierung ermöglicht Ihnen auch die Speicherung heterogener Dokumenttypen.

Die automatische Indizierung von Dokumenten wird durch Indexverwaltungsverfahren aktiviert, die für Schreibvorgänge optimiert, sperrfrei und protokollstrukturiert sind. DocumentDB unterstützt eine beständige Menge an schnellen Schreibvorgängen, während weiterhin konsistente Abfragen bedient werden.

Das DocumentDB-Subsystem für die Indizierung dient zur Unterstützung von:

·         Effizienten, umfassenden hierarchischen und relationalen Abfragen ohne Schema- oder Indexdefinitionen

·         Konsistenten Abfrageergebnissen auch bei der Bearbeitung einer beständigen Menge an Schreibvorgängen Bei Arbeitslasten mit hohem Schreibaufwand und konsistenten Abfragen wird der Index inkrementell, effizient und online aktualisiert, um mit dem hohen Aufkommen an Schreibvorgängen Schritt zu halten.

·         Speichereffizienz Aus Kostengründen ist der zusätzlich benötigte Speicherplatz für den Index beschränkt und vorhersagbar.

·         Mehrinstanzfähigkeit Indexaktualisierungen werden innerhalb des Budgets der pro DocumentDB-Sammlung verfügbaren Systemressourcen ausgeführt.

Für die meisten Anwendungen können Sie die automatische Standardindizerungsrichtlinie verwenden, da sie die größte Flexibilität und vernünftige Kompromisse zwischen Leistung und Speichereffizienz bietet. Auf der anderen Seite ermöglicht die Angabe einer benutzerdefinierten Indizierungsrichtlinie differenzierte Kompromisse zwischen Abfrageleistung, Schreibleistung und Indexspeicheraufwand.

Durch Ausschließen bestimmter Dokumente oder Pfade innerhalb von Dokumenten können Sie z. B. sowohl den für die Indizierung beanspruchten Speicherplatz als auch den Zeitaufwand beim Einfügen für die Indexwartung reduzieren. Sie können den Indextyp ändern, um diesen besser auf Bereichsabfragen anzupassen, oder Sie können die Indexgenauigkeit in Bytes erhöhen, um die Abfrageleistung zu verbessern. Dieser Artikel beschreibt die verschiedenen verfügbaren Optionen für die Indexkonfiguration in DocumentDB. Zudem wird beschrieben, wie die Indizierungsrichtlinie für Ihre Arbeitsauslastungen angepasst wird.

<a id="HowWorks"></a>So funktioniert die DocumentDB-Indizierung
-----------------------------

Die DocumentDB-Indizierung nutzt die Tatsache, dass Dokumente für die JSON-Grammatik als **Strukturen dargestellt** werden können. Damit ein JSON-Dokument als Struktur dargestellt werden kann, muss ein Dummy-Stammknoten erstellt werden, der für die restlichen Knoten im Dokument das übergeordnete Element darstellt. Jede Bezeichnung, einschließlich der Arrayindizes in einem JSON-Dokument, wird zu einem Knoten der Struktur. Die nachfolgende Abbildung zeigt ein JSON-Beispieldokument und die entsprechende Strukturdarstellung.

![Indexing Policies](media/documentdb-indexing-policies/image001.png)


Die JSON-Eigenschaft {"headquarters": "Belgium"} im obigen Beispiel entspricht dem Pfad /"headquarters"/"Belgium". Das JSON-Array {"exports": [{"city": "Moscow"}, {"city": Athens"}]} entspricht den Pfaden /"exports"/0/"city"/"Moscow" und /"exports"/1/"city"/"Athens".

**Hinweis** Die Pfaddarstellung verwischt die Grenzen zwischen Struktur/Schema und den Instanzwerten in Dokumenten, wodurch DocumentDB tatsächlich schemafrei sein kann.

In DocumentDB werden Dokumente in Sammlungen organisiert, die mithilfe von SQL abgefragt oder innerhalb des Bereichs einer einzelnen Transaktion verarbeitet werden können. Jede Sammlung kann mit einer eigenen Indizierungsrichtlinie konfiguriert werden, die in Form von Pfaden angegeben wird. Im folgenden Abschnitt betrachten wir die Konfiguration des Indizierungsverhaltens einer DocumentDB-Sammlung.

<a id="ConfigPolicy"></a>Konfigurieren der Indizierungsrichtlinie einer Sammlung
-------------------------------------------

Das folgende Beispiel zeigt, wie Sie mithilfe der DocumentDB-REST-API während der Erstellung einer Sammlung eine benutzerdefinierte Indizierungsrichtlinie einrichten . Das Beispiel zeigt die Indizierungsrichtlinie in Form von Pfaden, Indextypen und Genauigkeiten.


	POST https://<REST URI>/colls HTTP/1.1                                                  
 	...                                                             
 	Accept: application/json 
                                                                                                                         
 	{                                                                     
	 "id":"customIndexCollection",                                     
	 "indexingPolicy":{                                                 
     "automatic":true,                                            
	 "indexingMode":"Consistent",                                     
	 "IncludedPaths":[                                       
	           {                                                             
	              "IndexType":"Hash",                                        
	              "Path":"/"                                                 
	           }                                                  
	        ],                                                               
	        "ExcludedPaths":[                                                
	           "/"nonIndexedContent"/*"                                 
	        ]                                                               
	     }                                                                 
	 }                                                                                                                                                
 	...                                                                      
                  
                                                        
	 HTTP/1.1 201 Created                                                     


**Hinweis:** Die Indizierungsrichtlinie einer Sammlung muss zur Erstellungszeit angegeben werden. Die Änderung der Indizierungsrichtlinie nach der Erstellung der Sammlung ist nicht zulässig, wird jedoch in einer zukünftigen Version von DocumentDB unterstützt.

**Hinweis:** Standardmäßig indiziert DocumentDB alle Pfade in Dokumenten konsistent mit einem Hashindex. Der interne Pfad für Zeitstempel (_ts) wird mit einem Bereichsindex gespeichert.

### Automatische Indizierung

Sie können wählen, ob die Sammlung automatisch alle Dokumente indizieren soll. Standardmäßig werden automatisch alle Dokumente indiziert, aber Sie können diese Option bei Bedarf deaktivieren. Wenn die Indizierung deaktiviert ist, kann auf die Dokumente nur über ihre eigenen Links oder über Abfragen mithilfe der ID zugegriffen werden.

Mit deaktivierter automatischer Indizierung können Sie lediglich bestimmte Dokumente weiterhin zum Index hinzufügen. Umgekehrt können Sie die automatische Indizierung aktiviert lassen und nur bestimmte Dokumente einzeln ausschließen. Konfigurationen mit aktivierter/deaktivierter Indizierung sind hilfreich, wenn nur eine Teilmenge der Dokumente abgefragt werden muss.

Sie können die Standardrichtlinie konfigurieren, indem Sie für den Wert der automatischen Eigenschaft "true" oder "false" angeben. Um dies für ein einzelnes Dokument außer Kraft zu setzen, können Sie den "x-ms-indexingdirective"-Anforderungsheader beim Einfügen oder Ersetzen eines Dokuments festlegen.

Das folgende Beispiel veranschaulicht z. B. das explizite Einbeziehen eines Dokuments mithilfe des [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) und der [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx)-Eigenschaft.

	// If you want to override the default collection behavior to either     
	// exclude (or include) a Document from indexing,                                                                                           
	// use the RequestOptions.IndexingDirective property.                                  
	                                                                         
	client.CreateDocumentAsync(defaultCollection.SelfLink,  
	    new { Id = "AndersenFamily", isRegistered = true },                            
		new RequestOptions                               
		    {                                                                    
			    IndexingDirective = IndexingDirective.Include                                                                                      
			}
		);                                                                  


### Indizierungsmodi

Sie können zwischen synchronen (**konsistent**) und asynchronen (**verzögert**) Indexaktualisierungen wählen. Der Index wird bei jedem Einfügen, Ersetzen oder Löschen eines Dokuments der Sammlung standardmäßig synchron aktualisiert. Auf diese Weise können die Abfragen dieselbe Konsistenzebene wie die von Dokumentlesevorgängen berücksichtigen, ohne dass sich Verzögerung dadurch ergeben, dass der Index aufholen muss.

Obwohl DocumentDB für Schreibvorgänge optimiert ist und beständige Mengen an Dokumentschreibvorgängen zusammen mit der synchronen Indexwartung unterstützt, können Sie bestimmte Sammlungen so konfigurieren, dass ihr Index flexibel aktualisiert wird. Die verzögerte Indizierung eignet sich hervorragend für Szenarios, in denen Daten in großen Mengen geschrieben werden und sich der Arbeitsaufwand zum Indizieren von Inhalten über einen längeren Zeitraum bezahlt machen soll. Dadurch können Sie den bereitgestellten Durchsatz effektiv verwenden und Schreibanforderungen zu Spitzenzeiten mit minimalen Wartezeiten behandeln. Bei aktivierter verzögerter Indizierung werden Abfrageergebnisse unabhängig von der für das Datenbankkonto konfigurierten Konsistenzstufe letztendlich konsistent.

Das folgende Beispiel zeigt die Erstellung einer DocumentDB-Sammlung mithilfe des .NET SDK mit konsistenter automatischer Indizierung bei jeder Dokumenteinfügung.


	 // Default collection creates a hash index for all string and numeric    
	 // fields. Hash indexes are compact and offer efficient                                                                                           
	 // performance for equality queries.                                     
	                                                                          
	 var defaultCollection = new DocumentCollection { Id ="defaultCollection" };                                                   
	                                                                          
	 // Optional. Override Automatic to false for opt-in indexing of documents.                                                                
	                                                                          
	 defaultCollection.IndexingPolicy.Automatic = true;                       
	                                                                          
	 // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
	 // collections. Queries might return stale results with Lazy indexing.       
	                                                                          
	 defaultCollection.IndexingPolicy.IndexingMode = IndexingMode.Consistent; 
	                                                                          
	 defaultCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,defaultCollection);                                                      


### Indextypen und Genauigkeit

Der Typ oder das Schema, der bzw. das für die Indexeinträge verwendet wird, hat einen direkten Einfluss auf die Indexspeicherung und Leistung. Bei einem Schema mit höherer Genauigkeit werden Abfragen in der Regel schneller ausgeführt. Es ergibt sich aber auch ein höherer Speicherbedarf für den Index. Die Auswahl einer geringeren Genauigkeit bedeutet, dass während der Abfrageausführung mehr Dokumente verarbeitet werden müssen, wobei aber der Speicheraufwand geringer ist.

Für die Indexgenauigkeit für Werte in einem beliebigen Pfad können zwischen 3 und 7 Byte festgelegt werden.
Da derselbe Pfad möglicherweise numerische und Zeichenfolgenwerte in verschiedenen Dokumenten aufweisen kann, können diese separat gesteuert werden. Im .NET SDK entsprechen diese Werte den Eigenschaften [NumericPrecision](http://msdn.microsoft.com/library/microsoft.azure.documents.indexingpath.numericprecision.aspx) und [StringPrecision](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpath.stringprecision.aspx).

Es gibt zwei unterstützte Arten von Indextypen: Hash und Bereich. Durch die Auswahl des Indextyps **Hash** werden effiziente Gleichheitsabfragen ermöglicht. In den meisten Fällen benötigen Hashindizes keine höhere Genauigkeit als den Standardwert von 3 Bytes.

Durch die Auswahl des Indextyps **Bereich** werden Bereichsabfragen (mit >, <, >=, <=, !=) ermöglicht. Für Pfade mit großen Wertebereichen wird empfohlen, eine höhere Genauigkeit zu verwenden, z. B. 6 Bytes. Ein häufiges Anwendungsbeispiel, das einen höheren Genauigkeitsbereichsindex erfordert, sind als Epochenzeit gespeicherte Zeitstempel.

Wenn Ihr Anwendungsfall keine effizienten Bereichsabfragen erfordert, bieten die standardmäßigen Hashindizes den besten Kompromiss zwischen Speicherung und Leistung. Beachten Sie, dass Sie zur Unterstützung von Bereichsabfragen eine benutzerdefinierte Indexrichtlinie angeben müssen.

> [AZURE.NOTE] Bereichsindizes werden nur für numerische Werte unterstützt.
  
Das folgende Beispiel zeigt, wie die Genauigkeit für Bereichsindizes in einer Sammlung mithilfe des .NET SDKs erhöht wird. Beachten Sie, dass hierbei ein spezieller Pfad ("/") verwendet wird. Dies wird im nächsten Abschnitt erläutert.


	 // If your collection has numeric fields that need to be queried    
	 // against ranges (>,>=,<=,<), then you can configure the collection to 
	 // use range queries for all numeric values.                                                                                                      
 
	var rangeDefault = new DocumentCollection { Id = "rangeCollection" };                                                              
	rangeDefault.IndexingPolicy.IncludedPaths.Add(                                                             
												 new IndexingPath {   
													IndexType = IndexType.Range, Path = "/", 
													NumericPrecision = 7 }
												 ); 
	 rangeDefault = await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### Indexpfade

Innerhalb von Dokumenten können Sie auswählen, welche Pfade bei der Indizierung ein- oder ausgeschlossen werden müssen. Dies kann bessere Schreibleistungen und geringeren Indexspeicherbedarf für Szenarien bieten, in denen die Abfragemuster im Voraus bekannt sind.

Indexpfade beginnen mit dem Stamm (/) und enden in der Regel mit dem Platzhalterzeichen "?", das angibt, dass mehrere mögliche Werte für das Präfix verfügbar sind. Für "SELECT * FROM Families F WHERE F.familyName = "Andersen"" müssen Sie z. B. einen Indexpfad für /"familyName"/? in die Indexrichtlinie der Sammlung einbeziehen.

Indexpfade können auch das Platzhalterzeichen "*" verwenden, um das Verhalten für Pfade rekursiv unter dem Präfix anzugeben. Beispielsweise kann /"payload"/* dazu verwendet werden, um sämtliche Elemente unter der "payload"-Eigenschaft von der Indizierung auszuschließen.

Im Folgenden sind die allgemeinen Muster zum Angeben von Indexpfaden aufgeführt:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Pfad</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Beschreibung/Anwendungsfall</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /
                </p>
            </td>
            <td valign="top">
                <p>
                    Der Standardpfad für die Sammlung. Rekursiv und gilt für die gesamte Dokumentstruktur.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Zum Verarbeiten der nachfolgenden Abfragen erforderlicher Indexpfad (entsprechend mit Hash- und Bereichstyp):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop &gt; 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Indexpfad für alle Pfade unter der angegebenen Bezeichnung. Wurde nur für den Ausschluss von der Indexerstellung angegeben.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/
                </p>
            </td>
            <td valign="top">
                <p>
                    Ein Indexpfad, der während der Abfrageausführung zum Löschen von Dokumenten verwendet wird, die nicht über den angegebenen Pfad verfügen.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Zum Verarbeiten der nachfolgenden Abfragen erforderlicher Indexpfad (entsprechend mit Hash- oder Bereichstyp):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] Beim Festlegen von benutzerdefinierten Indexpfaden ist es erforderlich, dass Sie die Standardindizierungsregel für die gesamte Dokumentstruktur angeben, die durch den speziellen Pfad "/" angegeben ist.

Im folgenden Beispiel wird ein bestimmter Pfad mit Bereichsindizierung und benutzerdefiniertem Genauigkeitswert von 7 Byte konfiguriert:


 	// If you only want to specify range queries against a specific field,   
 	// then use a range index against that field. Doing so reduces the   
	// amount of storage required for indexes for the collection. In this    
 	// case, the query creates an index against the JSON path                   
 	// /"CreatedTimestamp"/?    
 	// allowing queries of the form WHERE CreatedTimestamp [>] X            
	
	var pathRange = new DocumentCollection { Id = "rangeSinglePathCollection" };    
	
	pathRange.IndexingPolicy.IncludedPaths.Add(
								new IndexingPath { 
										IndexType = IndexType.Range, 
										Path = "/"CreatedTimestamp"/?",   
										NumericPrecision = 7   
							 			}
									);   
	
	pathRange.IndexingPolicy.IncludedPaths.Add(   
											 new IndexingPath {  
											  	 Path = "/"  
											 });                                                                      
                                                   
	 pathRange = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);      


DocumentDB gibt einen Fehler zurück, wenn eine Abfrage einen Bereichsoperator verwendet, jedoch nicht über einen Bereichsindex für den abgefragten Pfad und nicht über andere Filter verfügt, die vom Index verarbeitet werden können. Diese Abfragen können jedoch ohne Bereichsindex mit dem Header "x-ms-documentdb-allow-scans" in der REST-API oder mithilfe der "AllowScanInQueryrequest"-Option des .NET SDKs ausgeführt werden.

Im nächsten Beispiel wird eine Pfadunterstruktur mithilfe des
Platzhalters "*" von der Indizierung ausgeschlossen.

	var excluded = new DocumentCollection { Id = "excludedPathCollection" };                                                                       
  	excluded.IndexingPolicy.IncludedPaths.Add(
	newIndexingPath {  Path = "/" });  

	excluded.IndexingPolicy.ExcludedPaths.Add("/" nonIndexedContent"/*");    
	excluded = await client.CreateDocumentCollectionAsync(database.SelfLink,excluded);                                                               


Leistungsoptimierung
------------------

Da Sie unterschiedliche Konfigurationen für die Indizierungsrichtlinie auswerten, sollten Sie die Auswirkungen der Richtlinie auf den Speicher und den Durchsatz über die DocumentDB-APIs messen.

Um das Speicherkontingent und die Verwendung einer Sammlung zu überprüfen, führen Sie ein "HEAD" oder "GET" für die Sammlungsressource aus, und überprüfen Sie die Header "x-ms-request-quota" und "x-ms-request-usage". Im .NET SDK enthalten die Eigenschaften [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) und [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) in [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) diese entsprechenden Werte.


 	// Measure the document size usage (which includes the index size) against   
 	// different policies.        
	 ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
	 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentSizeQuota, collectionInfo.DocumentSizeUsage);                                       


Um den Indizierungsaufwand für jeden Schreibvorgang (Erstellen, Aktualisieren oder Löschen) zu messen, überprüfen Sie den "x-ms-request-charge"-Header (oder die entsprechende [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx)-Eigenschaft in [ResourceResponse < T\ >](http://msdn.microsoft.com/library/dn799209.aspx) im .NET SDK). Hier können Sie die Anzahl der Anforderungseinheiten messen, die von diesen Vorgängen genutzt werden.


 	// Measure the performance (request units) of writes.     
 	ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
                                                                    
 	Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);                                                 
                                                                          
 	// Measure the performance (request units) of queries.    
	 IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                          
                                                                          
 	while (queryable.HasMoreResults)                                                                            
 	{                                                                         
 	   FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
	   Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
	}                                                                        




<!--HONumber=49--> 