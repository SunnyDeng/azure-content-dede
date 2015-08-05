<properties 
    pageTitle="Indizierungsrichtlinien für DocumentDB | Azure" 
    description="Erfahren Sie, wie die Indizierung in DocumentDB funktioniert, und wie Sie die Indizierungsrichtlinie konfigurieren und ändern können." 
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
    ms.date="07/19/2015" 
    ms.author="mimig"/>


# Indizierungsrichtlinien für DocumentDB

DocumentDB ist eine echte schemafreie Datenbank. Es setzt kein Schema für die indizierten JSON-Dokumente voraus. Dies ermöglicht Ihnen eine schnelle Definition und Iteration von Anwendungsdatenmodellen. Während Sie Dokumente zu einer Sammlung hinzufügen, werden alle Dokumenteigenschaften von DocumentDB automatisch indiziert und stehen dann für Sie für Abfragen bereit. Die automatische Indizierung ermöglicht Ihnen auch die Speicherung heterogener Dokumenttypen.

Die automatische Indizierung von Dokumenten wird durch Indexverwaltungsverfahren aktiviert, die für Schreibvorgänge optimiert, sperrfrei und protokollstrukturiert sind. DocumentDB unterstützt eine beständige Menge an schnellen Schreibvorgängen, während weiterhin konsistente Abfragen bedient werden.

Das DocumentDB-Subsystem für die Indizierung dient zur Unterstützung von:

-  Effizienten, umfassenden hierarchischen und relationalen Abfragen ohne Schema- oder Indexdefinitionen
-  Konsistenten Abfrageergebnissen auch bei der Bearbeitung einer beständigen Menge an Schreibvorgängen Bei Arbeitslasten mit hohem Schreibaufwand und konsistenten Abfragen wird der Index inkrementell, effizient und online aktualisiert, um mit dem hohen Aufkommen an Schreibvorgängen Schritt zu halten.
- Speichereffizienz Aus Kostengründen ist der zusätzlich benötigte Speicherplatz für den Index beschränkt und vorhersagbar.
- Mehrinstanzfähigkeit Indexaktualisierungen werden innerhalb des Budgets der pro DocumentDB-Sammlung verfügbaren Systemressourcen ausgeführt. 

Für die meisten Anwendungen können Sie die automatische Standardindizerungsrichtlinie verwenden, da sie die größte Flexibilität und vernünftige Kompromisse zwischen Leistung und Speichereffizienz bietet. Auf der anderen Seite ermöglicht die Angabe einer benutzerdefinierten Indizierungsrichtlinie differenzierte Kompromisse zwischen Abfrageleistung, Schreibleistung und Indexspeicheraufwand.

Durch Ausschließen bestimmter Dokumente oder Pfade innerhalb von Dokumenten können Sie z. B. sowohl den für die Indizierung beanspruchten Speicherplatz als auch den Zeitaufwand beim Einfügen für die Indexwartung reduzieren. Sie können den Indextyp ändern, um diesen besser auf Bereichsabfragen anzupassen, oder Sie können die Indexgenauigkeit in Bytes erhöhen, um die Abfrageleistung zu verbessern. Dieser Artikel beschreibt die verschiedenen verfügbaren Optionen für die Indexkonfiguration in DocumentDB. Zudem wird beschrieben, wie die Indizierungsrichtlinie für Ihre Workloads angepasst wird.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- Wie unterstützt DocumentDB die standardmäßige Indizierung aller Eigenschaften?
- Wie kann ich angeben, welche Eigenschaften in die Indizierung einbezogen bzw. davon ausgeschlossen werden sollen?
- Wie kann ich den Index nach eventuellen Updates konfigurieren?
- Wie kann ich die Indizierung konfigurieren, um Order By- oder Bereichsabfragen durchzuführen?

## So funktioniert die DocumentDB-Indizierung

Bei der DocumentDB-Indizierung wird die Tatsache genutzt, dass Dokumente für die JSON-Grammatik **als Strukturen dargestellt** werden können. Damit ein JSON-Dokument als Struktur dargestellt werden kann, muss ein Dummy-Stammknoten erstellt werden, der für die restlichen Knoten im Dokument das übergeordnete Element darstellt. Jede Bezeichnung, einschließlich der Arrayindizes in einem JSON-Dokument, wird zu einem Knoten der Struktur. Die nachfolgende Abbildung zeigt ein JSON-Beispieldokument und die entsprechende Strukturdarstellung.

![Indizierungsrichtlinien](media/documentdb-indexing-policies/image001.png)

Die JSON-Eigenschaft `{"headquarters": "Belgium"}` im Beispiel oben entspricht dem Pfad `/headquarters/Belgium`. Das JSON-Array `{"exports": [{"city": “Moscow"}, {"city": Athens"}]}` entspricht den Pfaden `/exports/[]/city/Moscow` und `/exports/[]/city/Athens`.

>[AZURE.NOTE]Die Pfaddarstellung verwischt die Grenzen zwischen Struktur/Schema und den Instanzwerten in Dokumenten, wodurch DocumentDB tatsächlich schemafrei sein kann.

In DocumentDB werden Dokumente in Sammlungen organisiert, die mithilfe von SQL abgefragt oder innerhalb des Bereichs einer einzelnen Transaktion verarbeitet werden können. Jede Sammlung kann mit einer eigenen Indizierungsrichtlinie konfiguriert werden, die in Form von Pfaden angegeben wird. Im folgenden Abschnitt betrachten wir die Konfiguration des Indizierungsverhaltens einer DocumentDB-Sammlung.

## Konfigurieren der Indizierungsrichtlinie einer Sammlung

Für jede DocumentDB-Sammlung können Sie die folgenden Optionen konfigurieren:

- Indizierungsmodus: **Consistent**, **Lazy** (für asynchrone Aktualisierungen) oder **None** (nur "ID"-basierter Zugriff)
- Eingeschlossene und ausgeschlossene Pfade: Legen Sie fest, welche Pfade in JSON eingeschlossen und ausgeschlossen sind.
- Indexart: **Hash** (für Gleichheitsabfragen), **Range** (für Gleichheits-, Bereichs- und Order By-Abfragen mit größerem Speicher)
- Indexgenauigkeit: 1–8 oder Maximum (–1) für Kompromiss zwischen Speicher und Leistung
- Automatic: **true** oder **false** zur Aktivierung oder **manual** (opt-in mit jedem Einfügen)

Mit dem folgenden .NET-Codeausschnitt wird gezeigt, wie eine benutzerdefinierte Indizierungsrichtlinie während der Erstellung einer Sammlung festgelegt wird. Hier wird die Richtlinie mit dem Bereichsindex für Zeichenfolgen und Zahlen mit maximaler Genauigkeit festgelegt. Mit dieser Richtlinie können Order By-Abfragen für Zeichenfolgen ausgeführt werden.

    var collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, collection);   


>[AZURE.NOTE]Das JSON-Schema für die Indizierungsrichtlinie wurde mit der Veröffentlichung der REST-API-Version 2015-06-03 geändert, um Bereichsindizes für Zeichenfolgen zu unterstützen. .NET SDK 1.2.0 und Java, Python und Node.js SDKs 1.1.0 unterstützen das neue Richtlinienschema. Für ältere SDKs wird die REST-API-Version 2015-04-08 genutzt und das ältere Schema der Indizierungsrichtlinie unterstützt.
>
>Standardmäßig indiziert DocumentDB alle Zeichenfolgeneigenschaften in Dokumenten konsistent mit einem Hashindex und numerische Eigenschaften mit einem Bereichsindex.

### Indizierungsmodi

Sie können zwischen synchronen (**Consistent**), asynchronen (**Lazy**) und keinen (**None**) Indexaktualisierungen wählen. Der Index wird bei jedem Einfügen, Ersetzen oder Löschen eines Dokuments der Sammlung standardmäßig synchron aktualisiert. Auf diese Weise können die Abfragen dieselbe Konsistenzebene wie die von Dokumentlesevorgängen berücksichtigen, ohne dass sich Verzögerung dadurch ergeben, dass der Index aufholen muss.

Obwohl DocumentDB für Schreibvorgänge optimiert ist und beständige Mengen an Dokumentschreibvorgängen zusammen mit der synchronen Indexwartung unterstützt, können Sie bestimmte Sammlungen so konfigurieren, dass ihr Index flexibel aktualisiert wird. Die verzögerte Indizierung eignet sich hervorragend für Szenarios, in denen Daten in großen Mengen geschrieben werden und sich der Arbeitsaufwand zum Indizieren von Inhalten über einen längeren Zeitraum bezahlt machen soll. Dadurch können Sie den bereitgestellten Durchsatz effektiv verwenden und Schreibanforderungen zu Spitzenzeiten mit minimalen Wartezeiten behandeln. Bei aktivierter verzögerter Indizierung werden Abfrageergebnisse unabhängig von der für das Datenbankkonto konfigurierten Konsistenzstufe letztendlich konsistent.

Das folgende Beispiel zeigt die Erstellung einer DocumentDB-Sammlung mithilfe des .NET SDK mit konsistenter automatischer Indizierung bei jeder Dokumenteinfügung.


     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     // Optional. Override Automatic to false for opt-in indexing of documents.
     collection.IndexingPolicy.Automatic = true;
     
     // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
     // collections. Queries might return stale results with Lazy indexing.
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);

### Indexpfade

Innerhalb von Dokumenten können Sie auswählen, welche Pfade bei der Indizierung ein- oder ausgeschlossen werden müssen. Dies kann bessere Schreibleistungen und geringeren Indexspeicherbedarf für Szenarien bieten, in denen die Abfragemuster im Voraus bekannt sind.

Indexpfade beginnen mit dem Stamm (/) und enden in der Regel mit dem Platzhalterzeichen "?", das angibt, dass mehrere mögliche Werte für das Präfix verfügbar sind. Für SELECT * FROM Families F WHERE F.familyName = "Andersen" müssen Sie z. B. einen Indexpfad für /familyName/? in die Indexrichtlinie der Sammlung einbinden.

Indexpfade können auch das Platzhalterzeichen "*" verwenden, um das Verhalten für Pfade rekursiv unter dem Präfix anzugeben. Beispielsweise kann "/payload/*" dazu verwendet werden, um sämtliche Elemente unter der "payload"-Eigenschaft von der Indizierung auszuschließen.

Im Folgenden sind die allgemeinen Muster zum Angeben von Indexpfaden aufgeführt:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Path</strong>
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
                    /*
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
                    /prop/?
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
                    SELECT * FROM collection c WHERE c.prop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>                
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Indexpfad für alle Pfade unter der angegebenen Bezeichnung. Funktioniert mit den folgenden Abfragen.
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Indexpfad ist erforderlich für Iteration und JOIN-Abfragen für Arrays mit Skalaren wie ["a", "b", "c"]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Indexpfad ist erforderlich für Iteration und JOIN-Abfragen für Arrays mit Objekten wie [{subprop: "a"}, {subprop: "b"}]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
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
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop.subprop
                </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]Beim Festlegen von benutzerdefinierten Indexpfaden ist es erforderlich, dass Sie die Standardindizierungsregel für die gesamte Dokumentstruktur angeben, die durch den speziellen Pfad "/" angegeben ist.

Im folgenden Beispiel wird ein bestimmter Pfad mit Bereichsindizierung und benutzerdefiniertem Genauigkeitswert von 20 Byte konfiguriert:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);

### Indexdatentypen, Indexarten und Indexgenauigkeiten

Nachdem wir uns mit der Angabe von Pfaden beschäftigt haben, wenden wir uns jetzt den Optionen zu, mit denen die Indizierungsrichtlinie für einen Pfad konfiguriert werden kann. Für jeden Pfad können Sie eine oder mehrere Indizierungsdefinitionen angeben:

- Datentyp: **Zeichenfolge** oder **Zahl** (kann nur einen Eintrag pro Datentyp pro Pfad enthalten)
- Indexart: **Hash** (Gleichheitsabfragen) oder **Range** (Gleichheits-, Bereichs- und Order By-Abfragen)
- Genauigkeit: 1–8 oder –1 (maximale Genauigkeit) für Zahlen, 1–100 (maximale Genauigkeit) für Zeichenfolgen

#### Indexart

DocumentDB unterstützt zwei Indexarten für jedes Paar aus Pfad und Datentyp.

- **Hash** unterstützt wirksame Gleichheitsabfragen. In den meisten Fällen benötigen Hashindizes keine höhere Genauigkeit als den Standardwert von 3 Bytes.
- **Range** unterstützt wirksame Gleichheitsabfragen, Bereichsabfragen (mit >, <, >=, <=, !=) und Order By-Abfragen. Bei Order By-Abfragen muss standardmäßig auch die maximale Indexgenauigkeit (–1) angegeben werden.

#### Indexgenauigkeit

Die Indexgenauigkeit ermöglicht eine Abstimmung zwischen dem Indexspeicheraufwand und der Abfrageleistung. Bei Zahlen wird die Verwendung der Standardkonfiguration für die Genauigkeit von –1 empfohlen. Da Zahlen in JSON eine Größe von 8 Byte aufweisen, entspricht dies einer Konfiguration von 8 Byte. Die Auswahl eines niedrigeren Werts für die Genauigkeit, z. B. 1 bis 7, bedeutet, dass Werte in einigen Bereichen dem gleichen Indexeintrag zugeordnet werden. Der Indexspeicherplatz reduziert sich, bei der Abfrageausführung muss jedoch möglicherweise eine größere Anzahl von Dokumenten verarbeitet werden, sodass sich somit der Durchsatz erhöht, d. h., eine größere Anzahl von Anforderungseinheiten genutzt wird.

Die Konfiguration der Indexgenauigkeit lässt sich bei Zeichenfolgenbereichen sinnvoller anwenden. Da Zeichenfolgen eine beliebige Länge aufweisen können, kann sich die Wahl der Indexgenauigkeit auf die Leistung von Abfragen von Zeichenfolgenbereichen und auf den benötigten Indexspeicherplatz auswirken. Zeichenfolgenbereichsindizes können mit einem Wert von 1 bis 100 oder mit dem Wert für die maximale Genauigkeit (–1) konfiguriert werden. Für Order By-Abfragen von Zeichenfolgen müssen Sie einen Wert für den angegebenen Pfad festlegen (–1).

Das folgende Beispiel zeigt, wie die Genauigkeit für Bereichsindizes in einer Sammlung mithilfe des .NET SDKs erhöht wird. Beachten Sie, dass dabei der Standardpfad "/*" verwendet wird.

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


> [AZURE.NOTE]DocumentDB gibt einen Fehler zurück, wenn in einer Abfrage "Order By" verwendet wird, aber kein Bereichsindex für den abgefragten Pfad mit maximaler Genauigkeit vorhanden ist.
>
> Ein Fehler wird für Abfragen mit Bereichsoperatoren wie z. B. ">=" zurückgegeben, wenn kein Bereichsindex vorhanden ist (mit beliebiger Genauigkeit). Sie können aber verarbeitet werden, sofern andere Filter vorhanden sind, für die die Verarbeitung über den Index möglich ist.
> 
> Bereichsabfragen können ohne Bereichsindex mit dem Header "x-ms-documentdb-enable-scans" in der REST-API oder mithilfe der "EnableScanInQuery"-Option des .NET SDK ausgeführt werden.

Auf ähnliche Weise können Pfade vollständig von der Indizierung ausgeschlossen werden. Im nächsten Beispiel wird gezeigt, wie Sie einen gesamten Abschnitt der Dokumente (eine Unterstruktur) mit dem Platzhalterzeichen "*" von der Indizierung ausschließen.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


### Automatische Indizierung

Sie können wählen, ob die Sammlung automatisch alle Dokumente indizieren soll. Standardmäßig werden automatisch alle Dokumente indiziert, aber Sie können diese Option bei Bedarf deaktivieren. Wenn die Indizierung deaktiviert ist, kann auf die Dokumente nur über ihre eigenen Links oder über Abfragen mithilfe der ID zugegriffen werden.

Mit deaktivierter automatischer Indizierung können Sie lediglich bestimmte Dokumente weiterhin zum Index hinzufügen. Umgekehrt können Sie die automatische Indizierung aktiviert lassen und nur bestimmte Dokumente einzeln ausschließen. Konfigurationen mit aktivierter/deaktivierter Indizierung sind hilfreich, wenn nur eine Teilmenge der Dokumente abgefragt werden muss.

Das folgende Beispiel veranschaulicht z. B. das explizite Einbeziehen eines Dokuments mithilfe des [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) und der [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx)-Eigenschaft.

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## Leistungsoptimierung

Die DocumentDB-APIs enthalten Informationen zu Leistungsindikatoren, z. B. dem genutzten Indexspeicher, und zu den Durchsatzkosten (Anforderungseinheiten) für jeden Vorgang. Anhand dieser Informationen können unterschiedliche Indizierungsrichtlinien im Hinblick auf die Leistungsoptimierung verglichen werden.

Um das Speicherkontingent und die Verwendung einer Sammlung zu überprüfen, führen Sie ein "HEAD" oder "GET" für die Sammlungsressource aus, und überprüfen Sie die Header "x-ms-request-quota" und "x-ms-request-usage". Im .NET SDK enthalten die Eigenschaften [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) und [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) in [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) diese entsprechenden Werte.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Um den Indizierungsaufwand für jeden Schreibvorgang (Erstellen, Aktualisieren oder Löschen) zu messen, überprüfen Sie den "x-ms-request-charge"-Header (oder die entsprechende [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx)-Eigenschaft in [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) im .NET SDK). Hier können Sie die Anzahl der Anforderungseinheiten messen, die von diesen Vorgängen genutzt werden.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## Änderungen an der Spezifikation der Indizierungsrichtlinie
Am 7. Juli 2015 wurde mit der REST-API-Version 2015-06-03 eine Änderung im Schema für die Indizierungsrichtlinie eingeführt. Die entsprechenden Klassen in den SDK-Versionen verfügen über neue Implementierungen für das Schema.

Die folgenden Änderungen wurden in die JSON-Spezifikation implementiert:

- Indizierungsrichtlinie unterstützt Bereichsindizes für Zeichenfolgen
- Jeder Pfad kann mehrere Indexdefinitionen aufweisen, eine für jeden Datentyp
- Indizierungsgenauigkeit unterstützt 1 bis 8 bei den Zahlen und 1 bis 100 bei Zeichenfolgen sowie –1 (maximale Genauigkeit)
- Pfadsegmente erfordern für die einzelnen Pfade keine doppelten Anführungszeichen als Escapezeichen (beispielsweise können Sie einen Pfad für /title/? hinzufügen, anstatt /"title"/?)
- Stammpfad für "alle Pfade" kann als "/*" dargestellt werden (zusätzlich zu "/")

Wenn in Ihrem Code Sammlungen mit einer benutzerdefinierten Indizierungsrichtlinie bereitgestellt werden, die mit Version 1.1.0 des .NET SDK oder früher geschrieben wurden, müssen Sie Ihren Anwendungscode zum Behandeln dieser Änderungen ändern, um die Umstellung auf SDK-Version 1.2.0 durchzuführen. Falls Sie keinen Code mit Konfiguration der Indizierungsrichtlinie haben oder weiterhin eine ältere SDK-Version verwenden möchten, sind keine Änderungen erforderlich.

Für einen praktischen Vergleich folgen ein Beispiel für eine benutzerdefinierte Indizierungsrichtlinie unter Verwendung der REST-API-Version 2015-06-03 sowie ein Beispiel unter Verwendung der vorherigen Version 2015-04-08.

**Vorherige Indizierungsrichtlinie (JSON)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/"nonIndexedContent"/*"
       ]
    }

**Aktuelle Indizierungsrichtlinie (JSON)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## Nächste Schritte

Verwenden Sie die unten angegebenen Links, um auf Beispiele für die Verwaltung der Indizierungsrichtlinie und weitere Informationen zur Abfragesprache von DocumentDB zuzugreifen.

1.	[Codebeispiele für die DocumentDB-.NET-Indexverwaltung](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.	[Vorgänge für DocumentDB-Dokumentauflistungen](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.	[Abfragen mit DocumentDB-SQL](documentdb-sql-query.md)

 

<!---HONumber=July15_HO4-->