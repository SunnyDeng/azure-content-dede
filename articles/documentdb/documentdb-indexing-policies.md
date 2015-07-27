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
    ms.date="07/06/2015" 
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

Durch Ausschließen bestimmter Dokumente oder Pfade innerhalb von Dokumenten können Sie z. B. sowohl den für die Indizierung beanspruchten Speicherplatz als auch den Zeitaufwand beim Einfügen für die Indexwartung reduzieren. Sie können den Indextyp ändern, um diesen besser auf Bereichsabfragen anzupassen, oder Sie können die Indexgenauigkeit in Bytes erhöhen, um die Abfrageleistung zu verbessern. Dieser Artikel beschreibt die verschiedenen verfügbaren Optionen für die Indexkonfiguration in DocumentDB. Zudem wird beschrieben, wie die Indizierungsrichtlinie für Ihre Arbeitsauslastungen angepasst wird.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- Wie unterstützt DocumentDB die standardmäßige Indizierung aller Eigenschaften?
- Wie kann ich angeben, welche Eigenschaften in die Indizierung einbezogen bzw. davon ausgeschlossen werden sollen?
- Wie kann ich den Index nach eventuellen Updates konfigurieren?
- Wie kann ich die Indizierung konfigurieren, um Order By- oder Bereichsabfragen durchzuführen?


## So funktioniert die DocumentDB-Indizierung

Bei der DocumentDB-Indizierung wird die Tatsache genutzt, dass Dokumente für die JSON-Grammatik **als Strukturen dargestellt** werden können. Damit ein JSON-Dokument als Struktur dargestellt werden kann, muss ein Dummy-Stammknoten erstellt werden, der für die restlichen Knoten im Dokument das übergeordnete Element darstellt. Jede Bezeichnung, einschließlich der Arrayindizes in einem JSON-Dokument, wird zu einem Knoten der Struktur. Die nachfolgende Abbildung zeigt ein JSON-Beispieldokument und die entsprechende Strukturdarstellung.

![Indizierungsrichtlinien](media/documentdb-indexing-policies/image001.png)

Die JSON-Eigenschaft {"headquarters": "Belgium"} im obigen Beispiel entspricht dem Pfad /"headquarters"/"Belgium". Das JSON-Array {"exports": [{"city": “Moscow"}, {"city": Athens"}]} entspricht den Pfaden /"exports"/0/"city"/"Moscow" und /"exports"/1/"city"/"Athens".

>[AZURE.NOTE]Die Pfaddarstellung verwischt die Grenzen zwischen Struktur/Schema und den Instanzwerten in Dokumenten, wodurch DocumentDB tatsächlich schemafrei sein kann.

In DocumentDB werden Dokumente in Sammlungen organisiert, die mithilfe von SQL abgefragt oder innerhalb des Bereichs einer einzelnen Transaktion verarbeitet werden können. Jede Sammlung kann mit einer eigenen Indizierungsrichtlinie konfiguriert werden, die in Form von Pfaden angegeben wird. Im folgenden Abschnitt betrachten wir die Konfiguration des Indizierungsverhaltens einer DocumentDB-Sammlung.

## Konfigurieren der Indizierungsrichtlinie einer Sammlung

Das folgende Beispiel veranschaulicht das Festlegen einer benutzerdefinierten Indizierungsrichtlinie bei der Erstellung einer Sammlung mithilfe der DocumentDB-REST-API. Das Beispiel stellt die Indizierungsrichtlinie in Form von Pfaden, Indextypen und Genauigkeiten dar.

    POST https://<REST URI>/colls HTTP/1.1
    Accept: application/json 

    {
       "id":"customIndexCollection",
       "indexingPolicy":{
          "automatic":true,
          "indexingMode":"Consistent",
          "includedPaths":[
             {
                "path":"/*",
                "indexes":[
    
                ]
             }
          ],
          "excludedPaths":[
             {
                "path":"/nonIndexedContent/*"
             }
          ]
       }
    }
     ...


     HTTP/1.1 201 Created

>[AZURE.NOTE]Das JSON-Schema für die Indizierungsrichtlinie wurde mit der Veröffentlichung der REST-API-Version 2015-06-03 geändert, um Bereichsindizes für Zeichenfolgen zu unterstützen. .NET SDK 1.2.0 und Java, Python und Node.js SDKs 1.1.0 unterstützen das neue Richtlinienschema. Für ältere SDKs wird die REST-API-Version 2015-04-08 genutzt und das ältere Schema der Indizierungsrichtlinie unterstützt.
>
>Die Indizierungsrichtlinie einer Sammlung muss bei der Erstellung angegeben werden. Das Ändern der Indizierungsrichtlinie nach der Erstellung der Sammlung ist nicht zulässig, wird aber in einer zukünftigen Version von DocumentDB unterstützt.
>
>Standardmäßig indiziert DocumentDB alle Pfade in Dokumenten auf einheitliche Weise mit einem Hashindex. Der interne Pfad „Timestamp (_ts)“ wird in einem Bereichsindex gespeichert.

### Automatische Indizierung

Sie können wählen, ob die Sammlung automatisch alle Dokumente indizieren soll. Standardmäßig werden automatisch alle Dokumente indiziert, aber Sie können diese Option bei Bedarf deaktivieren. Wenn die Indizierung deaktiviert ist, kann auf die Dokumente nur über ihre eigenen Links oder über Abfragen mithilfe der ID zugegriffen werden.

Mit deaktivierter automatischer Indizierung können Sie lediglich bestimmte Dokumente weiterhin zum Index hinzufügen. Umgekehrt können Sie die automatische Indizierung aktiviert lassen und nur bestimmte Dokumente einzeln ausschließen. Konfigurationen mit aktivierter/deaktivierter Indizierung sind hilfreich, wenn nur eine Teilmenge der Dokumente abgefragt werden muss.

Sie können die Standardrichtlinie konfigurieren, indem Sie für den Wert der automatischen Eigenschaft "true" oder "false" angeben. Um dies für ein einzelnes Dokument außer Kraft zu setzen, können Sie den "x-ms-indexingdirective"-Anforderungsheader beim Einfügen oder Ersetzen eines Dokuments festlegen.

Das folgende Beispiel veranschaulicht z. B. das explizite Einbeziehen eines Dokuments mithilfe des [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) und der [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx)-Eigenschaft.

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });
        


### Indizierungsmodi

Sie können zwischen synchronen (konsistent) und asynchronen (verzögert) Indexaktualisierungen wählen. Der Index wird bei jedem Einfügen, Ersetzen oder Löschen eines Dokuments der Sammlung standardmäßig synchron aktualisiert. Auf diese Weise können die Abfragen dieselbe Konsistenzebene wie die von Dokumentlesevorgängen berücksichtigen, ohne dass sich Verzögerung dadurch ergeben, dass der Index aufholen muss.

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

### Indextypen und Genauigkeit

Der Typ oder das Schema, der bzw. das für die Indexeinträge verwendet wird, hat einen direkten Einfluss auf die Indexspeicherung und Leistung. Bei einem Schema mit höherer Genauigkeit werden Abfragen in der Regel schneller ausgeführt. Es ergibt sich aber auch ein höherer Speicherbedarf für den Index. Die Auswahl einer geringeren Genauigkeit bedeutet, dass während der Abfrageausführung mehr Dokumente verarbeitet werden müssen, wobei aber der Speicheraufwand geringer ist.

Die Indexgenauigkeit für Werte unter jedem Pfad kann zwischen 1 und 8 liegen oder auf -1 festgelegt sein. Hiermit wird angegeben, dass für den Pfad die maximal erforderliche Genauigkeit verwendet werden muss. Jeder Pfad kann mit einem Array von Indizes konfiguriert werden: einer für jeden Datentyp (Zeichenfolge und Zahl) und jeweils mit Angabe der Genauigkeit.

Es gibt auch zwei unterstützte Arten von Indextypen: Hash und Bereich. Durch die Auswahl des Indextyps **Hash** werden effiziente Gleichheitsabfragen ermöglicht. In den meisten Fällen benötigen Hashindizes keine höhere Genauigkeit als den Standardwert von 3 Bytes.

Wenn Sie den Indextyp **Bereich** auswählen, sind sowohl Bereichsabfragen (mit >, <, >=, <=, !=) als auch **Order By**-Abfragen möglich. Order By-Abfragen erfordern standardmäßig auch, dass der Bereichsindex mit der maximalen Indexgenauigkeit (-1) erstellt wird, um die gesamte Sortierung der Ergebnisse sicherzustellen.

Für Pfade mit großen Wertebereichen wird empfohlen, eine höhere Genauigkeit zu verwenden, z. B. 6 Bytes. Ein häufiges Anwendungsbeispiel, das einen höheren Genauigkeitsbereichsindex erfordert, sind als Epochenzeit gespeicherte Zeitstempel.

Wenn Ihr Anwendungsfall keine Order By-Abfragen bzw. effizienten Bereichsabfragen erfordert, bieten die standardmäßigen Hashindizes den besten Kompromiss zwischen Speicherung und Leistung. Beachten Sie, dass Sie zum Unterstützen von Order By- oder Bereichsabfragen eine benutzerdefinierte, nicht standardmäßige Indexrichtlinie angeben müssen.

Das folgende Beispiel zeigt, wie die Genauigkeit für Bereichsindizes in einer Sammlung mithilfe des .NET SDKs erhöht wird. Beachten Sie, dass hierbei ein spezieller Pfad („/“) verwendet wird. Dies wird im nächsten Abschnitt erläutert.

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


### Indexpfade

Innerhalb von Dokumenten können Sie auswählen, welche Pfade bei der Indizierung ein- oder ausgeschlossen werden müssen. Dies kann bessere Schreibleistungen und geringeren Indexspeicherbedarf für Szenarien bieten, in denen die Abfragemuster im Voraus bekannt sind.

Indexpfade beginnen mit dem Stamm (/) und enden in der Regel mit dem Platzhalterzeichen "?", das angibt, dass mehrere mögliche Werte für das Präfix verfügbar sind. Für SELECT * FROM Families F WHERE F.familyName = "Andersen" müssen Sie z. B. einen Indexpfad für /familyName/? in die Indexrichtlinie der Sammlung einbinden.

Indexpfade können auch das Platzhalterzeichen "*" verwenden, um das Verhalten für Pfade rekursiv unter dem Präfix anzugeben. Beispielsweise kann /payload/* dazu verwendet werden, um sämtliche Elemente unter der „payload“-Eigenschaft von der Indizierung auszuschließen.

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
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]Beim Festlegen von benutzerdefinierten Indexpfaden ist es erforderlich, dass Sie die Standardindizierungsregel für die gesamte Dokumentstruktur angeben, die durch den speziellen Pfad „/*“ gekennzeichnet ist.

Im folgenden Beispiel wird ein bestimmter Pfad mit Bereichsindizierung und einem benutzerdefiniertem Genauigkeitswert von 20 Byte konfiguriert:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" 
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);


DocumentDB gibt einen Fehler zurück, wenn in einer Abfrage „Order By“ verwendet wird, aber für den abgefragten Pfad kein Bereichsindex mit maximaler Genauigkeit vorhanden ist. Ein Fehler wird für Abfragen mit Bereichsoperatoren wie >= zurückgegeben, wenn kein Bereichsindex vorhanden ist (mit beliebiger Genauigkeit). Sie können aber verarbeitet werden, sofern andere Filter vorhanden sind, für die die Verarbeitung über den Index möglich ist. Bereichsabfragen können ohne Bereichsindex mit dem Header „x-ms-documentdb-enable-scans“ in der REST-API oder mithilfe der „EnableScanInQuery“-Option des .NET SDK ausgeführt werden.

Auf ähnliche Weise können Pfade vollständig von der Indizierung ausgeschlossen werden. Im nächsten Beispiel wird gezeigt, wie Sie einen gesamten Abschnitt der Dokumente (eine Unterstruktur) mit dem Platzhalterzeichen „*“ von der Indizierung ausschließen.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


## Leistungsoptimierung

Da Sie unterschiedliche Konfigurationen für die Indizierungsrichtlinie auswerten, sollten Sie die Auswirkungen der Richtlinie auf den Speicher und den Durchsatz über die DocumentDB-APIs messen.

Um das Speicherkontingent und die Verwendung einer Sammlung zu überprüfen, führen Sie ein "HEAD" oder "GET" für die Sammlungsressource aus, und überprüfen Sie die Header "x-ms-request-quota" und "x-ms-request-usage". Im .NET SDK enthalten die Eigenschaften [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) und [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) in [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) diese entsprechenden Werte.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Um den Indizierungsaufwand für jeden Schreibvorgang (Erstellen, Aktualisieren oder Löschen) zu messen, überprüfen Sie den „x-ms-request-charge“-Header (oder die entsprechende [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx)-Eigenschaft in [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) im .NET SDK). Hier können Sie die Anzahl der Anforderungseinheiten messen, die von diesen Vorgängen genutzt werden.

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
Am 7. Juli 2015 wurde mit der REST-API-Version 2015-06-03 eine Änderung im Schema für die Indizierungsrichtlinie eingeführt. Die entsprechenden Klassen in den SDK-Versionen verfügen über neue Implementierungen für das Schema.

Die folgenden Änderungen wurden in die JSON-Spezifikation implementiert:

- Indizierungsrichtlinie unterstützt Bereichsindizes für Zeichenfolgen
- Jeder Pfad kann mehrere Indexdefinitionen aufweisen, eine für jeden Datentyp
- Indizierungsgenauigkeit unterstützt 1 bis 8 bei den Zahlen und 1 bis 100 bei Zeichenfolgen sowie -1 (maximale Genauigkeit)
- Pfadsegmente erfordern für die einzelnen Pfade keine doppelten Anführungszeichen als Escapezeichen (beispielsweise können Sie einen Pfad für /title/? hinzufügen, anstatt /"title"/?)
- Stammpfad für „alle Pfade“ kann als „/*“ dargestellt werden (zusätzlich zu „/“)

Wenn in Ihrem Code Sammlungen mit einer benutzerdefinierten Indizierungsrichtlinie bereitgestellt werden, die mit Version 1.1.0 des .NET SDK oder früher geschrieben wurden, müssen Sie Ihren Anwendungscode zum Behandeln dieser Änderungen ändern, um die Umstellung auf SDK-Version 1.2.0 durchzuführen. Falls Sie keinen Code mit Konfiguration der Indizierungsrichtlinie haben oder weiterhin eine ältere SDK-Version verwenden möchten, sind keine Änderungen erforderlich.

Hier sind einige Beispiele aufgeführt, um die Unterschiede zwischen der API-Version 2015-06-03 und der Vorgängerversion 2015-04-08 zu veranschaulichen.

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

 

<!---HONumber=July15_HO3-->