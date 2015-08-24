<properties 
    pageTitle="Indizierungsrichtlinien für DocumentDB | Microsoft Azure" 
    description="Erfahren Sie, wie die Indizierung in DocumentDB funktioniert, und wie Sie die Indizierungsrichtlinie konfigurieren und ändern können." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="08/03/2015" 
    ms.author="mimig"/>


# Indizierungsrichtlinien für DocumentDB

Zwar lassen zahlreiche Kunden gerne [alle Aspekte der Indizierung](documentdb-indexing.md) automatisch von DocumentDB verwalten, DocumentDB unterstützt jedoch auch die Angabe einer benutzerdefinierten **Indizierungsrichtlinie** für Sammlungen während der Erstellung. Indizierungsrichtlinien in DocumentDB sind flexibler und leistungsfähiger als sekundäre Indizes, die in anderen Datenbankplattformen angeboten werden, da sie Ihnen das Entwerfen und Anpassen der Form des Indexes ohne Einbußen bei der Schemaflexibilität ermöglichen. Durch die Verwaltung der Indizierungsrichtlinie können Sie differenzierte Kompromisse zwischen dem Indexspeicheraufwand, dem Schreib- und Abfragedurchsatz und der Abfragekonsistenz vornehmen.

In diesem Artikel befassen wir uns eingehend mit DocumentDB-Indizierungsrichtlinien, der Anpassung der Indizierungsrichtlinie und den zugehörigen Vor- und Nachteilen.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- Wie wird die automatische Indizierung standardmäßig von DocumentDB unterstützt?
- Wie kann ich angeben, welche Eigenschaften in die Indizierung einbezogen bzw. davon ausgeschlossen werden sollen?
- Wie kann ich den Index nach eventuellen Updates konfigurieren?
- Wie kann ich die Indizierung konfigurieren, um Order By- oder Bereichsabfragen durchzuführen?
- Wie nehme ich Änderungen an der Indizierungsrichtlinie einer Sammlung vor?
- Wie vergleiche ich die Speicherung und Leistung verschiedener Indizierungsrichtlinien?

##<a id="CustomizingIndexingPolicy"></a> Anpassen der Indizierungsrichtlinie einer Sammlung

Entwickler können die Kompromisse zwischen Speicher, Schreib-/Abfrageleistung und Abfragekonsistenz durch Überschreiben der Standardindizierungsrichtlinie in einer DocumentDB-Sammlung und durch Konfigurieren die folgenden Aspekte anpassen.

- **Ein-/Auschließen von Dokumenten und Pfaden in den/aus dem Index** Entwickler können bestimmte Dokumente zum Zeitpunkt des Einfügens oder Ersetzens in der Sammlung ausschließen oder einschließen. Entwickler können auch bestimmte JSON-Eigenschaften – auch als „Pfade“ bezeichnet – \(einschließlich Platzhaltermustern\) bei der Indizierung in Dokumenten ein- bzw. ausschließen, die in einem Index enthalten sind.
- **Konfigurieren verschiedener Indextypen**. Für jeden eingeschlossenen Pfad können Entwickler auch den erforderlichen Indextyp über Sammlung angeben, die auf ihren Daten basiert, sowie die erwartete Abfrage-Workload und Systemlast und die numerische/Zeichenfolge-„Genauigkeit“ für jeden Pfad.
- **Konfigurieren von Indexaktualisierungsmodi** DocumentDB unterstützt drei Indizierungsmodi, welche über die Indizierungsrichtlinie in einer DocumentDB-Sammlung konfiguriert werden können: Konsistent, Verzögert und Keine. 

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

DocumentDB unterstützt drei Indizierungsmodi, welche über die Indizierungsrichtlinie in einer DocumentDB-Sammlung konfiguriert werden können – Konsistent, Verzögert und Keine.

**Konsistent**: Wenn die Richtlinie einer DocumentDB-Sammlung als „konsistent“ festgelegt ist, folgen die Abfragen für eine bestimmte DocumentDB-Sammlung derselben Konsistenzebene wie für die Punktlesevorgänge angegeben \(d. h. „strong“, „bounded-staleness“, „session“ oder „eventual“\). Der Index wird synchron im Rahmen der Aktualisierung des Dokuments \(d. h. Einfügen, Ersetzen, Aktualisieren und Löschen eines Dokuments in einer DocumentDB-Sammlung\) aktualisiert. Die konsistente Indizierung unterstützt konsistente Abfragen auf Kosten einer möglichen Verringerung des Schreibdurchsatzes. Diese Verringerung ist eine Funktion der eindeutigen Pfade, die indiziert werden müssen, und der „Konsistenzebene“. Der konsistente Indizierungsmodus ist für Workloads mit schnellem Schreiben und sofortigem Abfragen vorgesehen.

**Verzögert**: Um den maximalen Dokumenterfassungsdurchsatz zu ermöglichen, kann eine DocumentDB-Sammlung mit verzögerter Konsistenz konfiguriert werden; d. h. Abfragen sind letztendlich konsistent. Der Index wird asynchron aktualisiert, wenn eine DocumentDB-Sammlung untätig ist, z. B. wenn die Durchsatzkapazität der Sammlung nicht vollständig mit dem Verarbeiten von Benutzeranforderungen ausgelastet ist. Für Workloads, die „jetzt erfassen, später abfragen“ und eine ungehinderte Dokumenterfassung erfordern, kann der „verzögerte“ Indizierungsmodus geeignet sein.

**Keine**: Einer Sammlung mit dem Indexmodus „Keine“ ist kein Index zugeordnet. Das Konfigurieren der Indizierungsrichtlinie mit „Keine“ hat den Nebeneffekt, dass alle vorhandenen Indizes gelöscht werden.

>[AZURE.NOTE]Das Konfigurieren der Indizierungsrichtlinie mit „Keine“ hat den Nebeneffekt, dass alle vorhandenen Indizes gelöscht werden. Verwenden Sie diese Option, wenn Ihre Zugriffsmuster nur „ID“ und/oder „Self-Link“ erfordern.

Das folgende Beispiel zeigt die Erstellung einer DocumentDB-Sammlung mithilfe des .NET SDK mit konsistenter automatischer Indizierung bei jeder Dokumenteinfügung.

Die folgende Tabelle zeigt die Konsistenz für Abfragen auf Grundlage des konfigurierten Indizierungsmodus \(Konsistent und Verzögert\) für die Sammlung und die für die Abfrageanforderung angegebene Konsistenzebene. Dies gilt für Abfragen mit beliebigen Schnittstellen – REST-API, SDKs aus gespeicherten Prozeduren und Triggern.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Consistent</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Verzögert</strong>
                </p>
            </td>            
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>Strong</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Strong
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>            
        </tr>       
        <tr>
            <td valign="top">
                <p>
                    <strong>Bounded Staleness</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Bounded Staleness
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>            
        </tr>          
        <tr>
            <td valign="top">
                <p>
                    <strong>Session</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Session
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>            
        </tr>      
        <tr>
            <td valign="top">
                <p>
                    <strong>Eventual</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>            
        </tr>         
    </tbody>
</table>

Standardmäßig wird für alle Abfragen ein Fehler zurückgegeben, wenn die Sammlung mit dem Indizierungsmodus „Keine“ eingerichtet ist, um zu signalisieren, dass möglicherweise eine Überprüfung erforderlich ist, um die Abfrage zu verarbeiten. Diese Abfragen können ohne Bereichsindex mit dem Header `x-ms-documentdb-enable-scans` in der REST-API oder mithilfe der `EnableScanInQuery`-Option des .NET SDK ausgeführt werden. Für einige Abfragen, z. B. wenn sie „ORDER BY“ verwenden, ist der Modus „Keine“ auch mit `EnableScanInQuery` nicht zulässig.

Die folgende Tabelle zeigt die Konsistenz für Abfragen auf Grundlage des Indizierungsmodus \(Konsistent, Verzögert und Keine\) wenn „EnableScanInQuery“ angegeben wird.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Consistent</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Verzögert</strong>
                </p>
            </td>       
            <td valign="top">
                <p>
                    <strong>Keine</strong>
                </p>
            </td>             
        </tr>
        <tr>
            <td valign="top">
                <p>
                    <strong>Strong</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Strong
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>    
            <td valign="top">
                <p>
                    Strong
                </p>
            </td>                
        </tr>       
        <tr>
            <td valign="top">
                <p>
                    <strong>Bounded Staleness</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Bounded Staleness
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>      
            <td valign="top">
                <p>
                    Bounded Staleness
                </p>
            </td> 
        </tr>          
        <tr>
            <td valign="top">
                <p>
                    <strong>Session</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Session
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>   
            <td valign="top">
                <p>
                    Session
                </p>
            </td>             
        </tr>      
        <tr>
            <td valign="top">
                <p>
                    <strong>Eventual</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>      
            <td valign="top">
                <p>
                    Eventual
                </p>
            </td>              
        </tr>         
    </tbody>
</table>

Das folgende Codebeispiel zeigt die Erstellung einer DocumentDB-Sammlung mithilfe des .NET SDK mit konsistenter Indizierung bei jeder Dokumenteinfügung.

     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);


### Indexpfade

DocumentDB modelliert JSON-Dokumente und den Index als Strukturen und ermöglicht es Ihnen, die Richtlinien für Pfade innerhalb der Struktur zu optimieren. Weitere Informationen finden Sie in dieser [Einführung in die DocumentDB Indizierung](documentdb-indexing.md). Innerhalb von Dokumenten können Sie auswählen, welche Pfade bei der Indizierung ein- oder ausgeschlossen werden müssen. Dies kann bessere Schreibleistungen und geringeren Indexspeicherbedarf für Szenarien bieten, in denen die Abfragemuster im Voraus bekannt sind.

Indexpfade beginnen mit dem Stamm \(/\) und enden in der Regel mit dem Platzhalterzeichen "?", das angibt, dass mehrere mögliche Werte für das Präfix verfügbar sind. Für SELECT * FROM Families F WHERE F.familyName = "Andersen" müssen Sie z. B. einen Indexpfad für /familyName/? in die Indexrichtlinie der Sammlung einbinden.

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

- Datentyp: **String**, **Number** oder **Point** \(kann nur einen Eintrag pro Datentyp pro Pfad enthalten\)
- Indexart: **Hash** \(Gleichheitsabfragen\), **Range** \(Gleichheits-, Bereichs- und Order By-Abfragen\) oder **Spatial** \(räumliche Abfragen\) 
- Genauigkeit: 1–8 oder –1 \(maximale Genauigkeit\) für Zahlen, 1–100 \(maximale Genauigkeit\) für Zeichenfolgen

#### Indexart

DocumentDB unterstützt die Indextypen "Hash" und "Range" für alle Pfade \(die für Zeichenfolgen, Zahlen oder beides konfiguriert sein können\).

- **Hash** unterstützt effiziente Gleichheits- und JOIN-Abfragen. In den meisten Fällen benötigen Hashindizes keine höhere Genauigkeit als den Standardwert von 3 Bytes.
- **Range** unterstützt effizienten Gleichheitsabfragen, Bereichsabfragen \(mit \>, \<, \>=, \<=, !=\) und Order By-Abfragen. Bei Order By-Abfragen muss standardmäßig auch die maximale Indexgenauigkeit \(–1\) angegeben werden.

DocumentDB unterstützt auch den Indextyp "Spatial" für alle Pfade, die für den Datentyp "Point" angegeben werden können. Der Wert im angegebenen Pfad muss ein gültiger GeoJSON-Punkt wie `{"type": "Point", "coordinates": [0.0, 10.0]}` sein.

- **Spatial** unterstützt effiziente räumliche Abfragen zur Entfernung und zu enthaltenen Elementen.

Hier sehen Sie die unterstützten Indexarten und Beispiele für Abfragen, für die sie verwendet werden können:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Indexart</strong>
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
                    Hash
                </p>
            </td>
            <td valign="top">
                <p>
                    Hash über /prop/? (oder /*) kann verwendet werden, um die folgenden Abfragen effektiv zu verarbeiten: SELECT * FROM collection c WHERE c.prop = "value" Hash über /props/[]/? (oder /* oder /props/*) kann verwendet werden, um die folgenden Abfragen effektiv zu verarbeiten: SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    Bereich
                </p>
            </td>
            <td valign="top">
                <p>
                    Bereich über /prop/? (oder /*) kann verwendet werden, um die folgenden Abfragen effektiv zu verarbeiten: SELECT * FROM collection c WHERE c.prop = "value" SELECT * FROM collection c WHERE c.prop > 5 SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    Spatial
                </p>
            </td>
            <td valign="top">
                <p>
                    "Range" über "/prop/?" (oder /*) kann verwendet werden, um die folgenden Abfragen effizient zu verarbeiten:  SELECT * FROM collection c WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) &lt; 40 SELECT * FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... })
                </p>
            </td>
        </tr>        
    </tbody>
</table>

Standardmäßig wird ein Fehler für Abfragen mit Bereichsoperatoren wie „\> =“ zurückgegeben, wenn kein Bereichsindex \(mit Genauigkeit\) vorhanden ist, um zu signalisieren, dass möglicherweise eine Überprüfung erforderlich ist, um die Abfrage zu verarbeiten. Bereichsabfragen können ohne Bereichsindex mit dem Header "x-ms-documentdb-enable-scans" in der REST-API oder mithilfe der "EnableScanInQuery"-Option des .NET SDK ausgeführt werden. Wenn die Abfrage weitere Filter enthält, die DocumentDB für den Index verwenden kann, wird kein Fehler gemeldet.

Die gleichen Regeln gelten für räumliche Abfragen. Standardmäßig wird ein Fehler für räumliche Abfragen zurückgegeben, wenn keine räumlicher Index vorhanden ist. Sie können als Suche mit "x-ms-documentdb-enable-scan/EnableScanInQuery" erfolgen.

#### Indexgenauigkeit

Die Indexgenauigkeit ermöglicht eine Abstimmung zwischen dem Indexspeicheraufwand und der Abfrageleistung. Bei Zahlen wird die Verwendung der Standardkonfiguration für Genauigkeit -1 \("Maximum"\) empfohlen. Da Zahlen in JSON eine Größe von 8 Byte aufweisen, entspricht dies einer Konfiguration von 8 Byte. Die Auswahl eines niedrigeren Werts für die Genauigkeit, z. B. 1 bis 7, bedeutet, dass Werte in einigen Bereichen dem gleichen Indexeintrag zugeordnet werden. Der Indexspeicherplatz reduziert sich, bei der Abfrageausführung muss jedoch möglicherweise eine größere Anzahl von Dokumenten verarbeitet werden, sodass sich somit der Durchsatz erhöht, d. h., eine größere Anzahl von Anforderungseinheiten genutzt wird.

Die Konfiguration der Indexgenauigkeit lässt sich bei Zeichenfolgenbereichen sinnvoller anwenden. Da Zeichenfolgen eine beliebige Länge aufweisen können, kann sich die Wahl der Indexgenauigkeit auf die Leistung von Abfragen von Zeichenfolgenbereichen und auf den benötigten Indexspeicherplatz auswirken. Zeichenfolgenbereich-Indizes können mit einem Wert von 1 bis 100 oder -1 \("Maximum"\) konfiguriert werden. Wenn Sie ORDER BY-Abfragen für Zeichenfolgeneigenschaften ausführen möchten, müssen Sie eine Genauigkeit von-1 für die entsprechenden Pfade angeben.

Räumliche Indizes verwenden stets die standardmäßige Indexgenauigkeit für Punkte und können nicht überschrieben werden.

Das folgende Beispiel zeigt, wie die Genauigkeit für Bereichsindizes in einer Sammlung mithilfe des .NET SDKs erhöht wird. Beachten Sie, dass dabei der Standardpfad "/*" verwendet wird.

**Erstellen einer Sammlung mit benutzerdefinierter Indexgenauigkeit**

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

Auf ähnliche Weise können Pfade vollständig von der Indizierung ausgeschlossen werden. Im nächsten Beispiel wird gezeigt, wie Sie einen gesamten Abschnitt der Dokumente \(eine Unterstruktur\) mit dem Platzhalterzeichen "*" von der Indizierung ausschließen.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


## Aktivierung und Deaktivierung der Indizierung

Sie können wählen, ob die Sammlung automatisch alle Dokumente indizieren soll. Standardmäßig werden automatisch alle Dokumente indiziert, aber Sie können diese Option bei Bedarf deaktivieren. Wenn die Indizierung deaktiviert ist, kann auf die Dokumente nur über ihre eigenen Links oder über Abfragen mithilfe der ID zugegriffen werden.

Mit deaktivierter automatischer Indizierung können Sie lediglich bestimmte Dokumente weiterhin zum Index hinzufügen. Umgekehrt können Sie die automatische Indizierung aktiviert lassen und nur bestimmte Dokumente einzeln ausschließen. Konfigurationen mit aktivierter/deaktivierter Indizierung sind hilfreich, wenn nur eine Teilmenge der Dokumente abgefragt werden muss.

Das folgende Beispiel veranschaulicht z. B. das explizite Einbeziehen eines Dokuments mithilfe des [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) und der [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx)-Eigenschaft.

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## Ändern der Indizierungsrichtlinie einer Sammlung

DocumentDB ermöglicht Ihnen das dynamische Ändern der Indizierungsrichtlinie einer Sammlung. Eine Änderung der Indizierungsrichtlinie in einer DocumentDB-Sammlung kann zu einer Änderung in der Form des Index einschließlich der indizierbaren Pfade, deren Genauigkeit sowie des Konsistenzmodells des Index selbst führen. Eine Änderung der Indizierungsrichtlinie erfordert daher effektiv eine Transformation des alten Index in einen neuen.

**Onlineindextransformationen**

![Onlineindextransformationen](media/documentdb-indexing-policies/index-transformations.png)

Indextransformationen erfolgen online; das bedeutet, dass die gemäß der alten Richtlinien indizierten Dokumente effizient gemäß der neuen Richtlinie transformiert werden, **ohne dass Auswirkungen auf die Schreibverfügbarkeit oder den bereitgestellten Durchsatz** der Sammlung entstehen. Die Transformation hat keine Auswirkungen auf die Konsistenz der Lese- und Schreibvorgänge, die mithilfe der REST-API, SDKs oder innerhalb von gespeicherten Prozeduren und Triggern erfolgen. Dies bedeutet, dass es keine Leistungseinbußen oder Ausfallzeiten für Ihre Apps gibt, wenn Sie eine Indizierungsrichtlinie ändern.

Abfragen sind jedoch unabhängig von der Indizierungsmoduskonfiguration \(„Konsistent“ oder „Verzögert“\) während der Ausführung der Indextransformation letztendlich konsistent. Dies gilt auch für Abfragen über beliebige Schnittstellen – REST-API, SDKs oder innerhalb von gespeicherten Prozeduren und Triggern. Wie die verzögerte Indizierung wird auch die Indextransformation asynchron im Hintergrund auf den Replikaten mit den verfügbaren freien Ressourcen für ein bestimmtes Replikat ausgeführt.

Indextransformationen werden auch **direkt** vorgenommen, d. h. DocumentDB behält nicht zwei Kopien des Indexes bei, sondern tauscht den alten Index durch den neuen aus. Dies bedeutet, dass beim Durchführen von Indextransformationen in Ihren Sammlungen kein zusätzlicher Speicherplatz erforderlich ist oder belegt wird.

Wenn Sie eine Indizierungsrichtlinie ändern, hängt die Anwendung der Änderungen Wechsel vom alten zum neuen Index stärker von den Indizierungmoduskonfigurationen ab als von den anderen Werten wie ein-/ausgeschlossene Pfade, Indexarten und Genauigkeiten. Wenn sowohl Ihre alten als auch die neuen Richtlinien konsistente Indizierung verwenden, führt DocumentDB eine Onlineindextransformation durch. Während der Ausführung der Transformation können Sie keine weitere Änderung der Indizierungsrichtlinie mit konsistentem Indizierungsmodus vornehmen.

Sie können jedoch in den Indizierungsmodus „Verzögert“ oder „Keine“ wechseln, während eine Transformation ausgeführt wird.

- Beim Wechsel zu „Verzögert“ erfolgt die Änderung der Indexrichtlinie effektiv sofort und DocumentDB beginnt mit dem erneuten asynchronen Erstellen des Index. 
- Wenn Sie zu „Keine“ wechseln, wird der Index effektiv sofort verworfen. Der Wechseln zu „Keine“ ist nützlich, wenn Sie eine laufende Transformation abbrechen und mit einer anderen Indizierungsrichtlinie neu beginnen möchten. 

Wenn Sie das .NET SDK verwenden, können Sie die Änderung einer Indizierungsrichtlinie mit der neuen **ReplaceDocumentCollectionAsync**-Methode starten und den prozentualen Fortschritt der Indextransformation mithilfe der Response-Eigenschaft **IndexTransformationProgress** aus einem **ReadDocumentCollectionAsync**-Aufruf nachverfolgen. Andere SDKs und die REST-API unterstützen entsprechende Eigenschaften und Methoden Änderungen an Indizierungsrichtlinien.

Der folgende Codeausschnitt zeigt die Änderung der Indizierungsrichtlinie einer Sammlung vom konsistenten zum verzögerten Indizierungsmodus.

**Änderung der Indizierungsrichtlinie von konsistent zu verzögert**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


Sie können den Fortschritt einer Indextransformation beispielsweise durch Aufrufen von „ReadDocumentCollectionAsync“ überprüfen \(siehe unten\).

**Nachverfolgen des Fortschritts des Indextransformation**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await     client.ReadDocumentCollectionAsync(collection.SelfLink);
        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

Sie können den Index für eine Sammlung verwerfen, indem Sie in den Indizierungsmodus „Keine“ wechseln. Dies kann ein hilfreiches Betriebstool sein, wenn Sie eine laufende Transformation abbrechen und sofort eine neue starten möchten.

**Verwerfen des Index für eine Sammlung**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Warum sollten Sie Änderungen an der Indizierungsrichtlinie Ihrer DocumentDB-Sammlungen vornehmen? Folgende Anwendungsfälle sind die gängigsten:

- Bereitstellung konsistenter Ergebnisse während des normalen Betriebs, aber Reduzierung auf verzögerte Indizierung während Massendatenimporten
- Einführung neuer Indizierungsfunktionen in Ihren aktuellen DocumentDB-Sammlungen, z. B. räumliche Abfragen, die den Indextyp "Spatial" erfordern, oder ORDER BY- und Zeichenfolgenbereich-Abfragen, die den neu eingeführten Zeichenfolgenindex-Typ "Range" erfordern.
- Manuelles Auswählen der zu indizierenden Eigenschaften und Änderungen dieser mit der Zeit
- Optimieren der Indizierungsgenauigkeit zur Verbesserung der Abfrageleistung oder zum Reduzieren der Speicherbelegung

>[AZURE.NOTE]Zum Ändern der Indizierungsrichtlinie mit „ReplaceDocumentCollectionAsync“ benötigen Sie Version \> = 1.3.0 von .NET SDK.

## Leistungsoptimierung

Die DocumentDB-APIs enthalten Informationen zu Leistungsindikatoren, z. B. dem genutzten Indexspeicher, und zu den Durchsatzkosten \(Anforderungseinheiten\) für jeden Vorgang. Anhand dieser Informationen können unterschiedliche Indizierungsrichtlinien im Hinblick auf die Leistungsoptimierung verglichen werden.

Um das Speicherkontingent und die Verwendung einer Sammlung zu überprüfen, führen Sie ein "HEAD" oder "GET" für die Sammlungsressource aus, und überprüfen Sie die Header "x-ms-request-quota" und "x-ms-request-usage". Im .NET SDK enthalten die Eigenschaften [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) und [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) in [ResourceResponse\<T\>](http://msdn.microsoft.com/library/dn799209.aspx) diese entsprechenden Werte.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Um den Indizierungsaufwand für jeden Schreibvorgang \(Erstellen, Aktualisieren oder Löschen\) zu messen, überprüfen Sie den Header „x-ms-request-charge“ \(oder die entsprechende [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx)-Eigenschaft in [ResourceResponse\<T\>](http://msdn.microsoft.com/library/dn799209.aspx) im .NET SDK\). Hier können Sie die Anzahl der Anforderungseinheiten messen, die von diesen Vorgängen genutzt werden.

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
- Indizierungsgenauigkeit unterstützt 1 bis 8 bei den Zahlen und 1 bis 100 bei Zeichenfolgen sowie –1 \(maximale Genauigkeit\)
- Pfadsegmente erfordern für die einzelnen Pfade keine doppelten Anführungszeichen als Escapezeichen \(beispielsweise können Sie einen Pfad für /title/? hinzufügen, anstatt /"title"/?\)
- Stammpfad für "alle Pfade" kann als "/*" dargestellt werden \(zusätzlich zu "/"\)

Wenn in Ihrem Code Sammlungen mit einer benutzerdefinierten Indizierungsrichtlinie bereitgestellt werden, die mit Version 1.1.0 des .NET SDK oder früher geschrieben wurden, müssen Sie Ihren Anwendungscode zum Behandeln dieser Änderungen ändern, um die Umstellung auf SDK-Version 1.2.0 durchzuführen. Falls Sie keinen Code mit Konfiguration der Indizierungsrichtlinie haben oder weiterhin eine ältere SDK-Version verwenden möchten, sind keine Änderungen erforderlich.

Für einen praktischen Vergleich folgen ein Beispiel für eine benutzerdefinierte Indizierungsrichtlinie unter Verwendung der REST-API-Version 2015-06-03 sowie ein Beispiel unter Verwendung der vorherigen Version 2015-04-08.

**Vorherige Indizierungsrichtlinie \(JSON\)**

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
          "/\"nonIndexedContent\"/*"
       ]
    }

**Aktuelle Indizierungsrichtlinie \(JSON\)**

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

 

<!---HONumber=August15_HO7-->