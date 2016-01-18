<properties 
    pageTitle="Arbeiten mit Geodaten in Azure DocumentDB | Microsoft Azure" 
    description="Grundlegendes zum Erstellen, Indizieren und Abfragen räumlicher Objekte mit Azure DocumentDB." 
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
    ms.date="10/27/2015" 
    ms.author="arramac"/>
    
# Arbeiten mit Geodaten in Azure DocumentDB

Dieser Artikel bietet eine Einführung in die Funktionalität für Geodaten in [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/). Nach dem Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- Wie werden Geodaten in Azure DocumentDB gespeichert?
- Wie kann ich Geodaten in Azure-DocumentDB in SQL und LINQ abfragen?
- Wie aktiviere oder deaktiviere ich die räumliche Indizierung in DocumentDB?

In diesem [Github-Projekt](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) finden Sie Codebeispiele.

## Einführung in räumliche Daten

Räumliche Daten beschreiben die Position und Form von Objekten im Raum. In den meisten Fällen entsprechen diese Daten Objekten auf der Erde und werden deshalb Geodaten genannt. Räumliche Daten dienen zur Darstellung des Orts einer Person, einer Sehenswürdigkeit, der Umgrenzung einer Stadt oder eines Sees. Übliche Anwendungsbeispiele sind häufig Abfragen der Entfernung, z. B. "alle Cafés in der Nähe meines aktuellen Standorts suchen".

### GeoJSON
DocumentDB unterstützt eine Volltextindizierung und Abfrage von Geopunktdaten, die mithilfe der [GeoJSON-Spezifikation](http://geojson.org/geojson-spec.html) dargestellt werden. GeoJSON-Datenstrukturen sind stets gültige JSON-Objekte, weshalb sie mit DocumentDB gespeichert und ohne spezielle Tools oder Bibliotheken abgefragt werden können. Die DocumentDB SDKs bieten Hilfsklassen und Methoden, die das Arbeiten mit räumlichen Daten erleichtern.

### Punkte, LineStrings und Polygone
Ein **Punkt** kennzeichnet eine einzelne Position im Raum. In Geodaten stellt ein Punkt den exakten Ort dar, der die Adresse eines Lebensmittelgeschäfts, ein Kiosk, ein Auto oder eine Stadt sein kann. Ein Punkt wird in GeoJSON (und DocumentDB) mithilfe seines Koordinatenpaares oder Längen- und Breitengrads dargestellt. Hier ist eine Beispiel-JSON für einen Punkt.

**Punkte in DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE]Die GeoJSON-Spezifikation gibt zuerst den Längengrad und dann den Breitengrad an. Wie in anderen Kartenprogrammen sind Längen- und Breitengrade Winkel, die in Grad dargestellt werden. Längengradwerte werden ab dem Nullmeridian gemessen und betragen von -180 bis +180 Grad. Breitengradwerte werden ab dem Äquator gemessen und betragen von -90,0 bis +90,0 Grad.
>
> DocumentDB interpretiert Koordinaten gemäß der Darstellung durch das WGS 84-Referenzsystem. Nachstehend finden Sie weitere Informationen zu Koordinatenreferenzsystemen.

Ein solches System kann in ein DocumentDB-Dokument eingebettet werden, was dieses Beispiel eines Benutzerprofils mit Standortdaten veranschaulicht:

**Verwenden eines Profils mit in DocumentDB gespeichertem Standort**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Zusätzlich zu Punkten unterstützt GeoJSON auch LineStrings und Polygone. **LineStrings** stellen eine Reihe von zwei oder mehr Punkten im Raum sowie die Liniensegmente dar, die diese verbinden. In Geodaten werden LineStrings häufig zum Darstellen von Straßen oder Flüssen verwendet. Ein **Polygon** ist eine Umgrenzung miteinander verbundener Punkte, die eine geschlossene LineString bilden. Polygone dienen meist zum Darstellen natürlicher Gebilde wie Seen oder geopolitischer Gebilde wie Städte und Staaten. Hier ist ein Beispiel für ein Polygon in DocumentDB.

**Polygone in DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE]Die GeoJSON-Spezifikation erfordert, dass für gültige Polygone das letzte angegebene Koordinatenpaar mit dem ersten identisch sein muss, um eine geschlossene Form zu bilden.
>
>Punkte innerhalb eines Polygons müssen gegen den Uhrzeigersinn nacheinander angegeben werden. Ein Polygon, das im Uhrzeigersinn angegeben wird, stellt die Umkehrung der darin enthaltenen Region dar.

Zusätzlich zu Punkten, LineStrings und Polygonen gibt GeoJSON auch das Darstellen der Gruppierung mehrerer Geodatenstandorte sowie das Zuweisen beliebiger Eigenschaften mit Geolocation als **Feature** an. Da diese Objekte gültige JSON sind, können sie alle in DocumentDB gespeichert und verarbeitet werden. DocumentDB unterstützt jedoch nur die automatische Indizierung von Punkten.

### Koordinatenreferenzsysteme

Da die Form der Erde unregelmäßig ist, werden Koordinaten von Geodaten in zahlreichen Koordinatenreferenzsystemen (KRS) abgebildet, die jeweils eigene Bezugsrahmen und Maßeinheiten aufweisen. Das "National Grid of Britain" ist beispielsweise ein Referenzsystem, das sehr genau für Großbritannien ist, außerhalb davon hingegen nicht.

Das am häufigsten verwendete KRS ist derzeit das World Geodetic System [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). GPS-Geräte und viele Kartendienste einschließlich Google Maps- und Bing Maps-APIs verwenden WGS-84. DocumentDB unterstützt ausschließlich die Indizierung und Abfrage von Geodaten mit dem Koordinatenreferenzsystem WGS-84.

## Erstellen von Dokumenten mit räumlichen Daten
Beim Erstellen von Dokumenten, die GeoJSON-Werte enthalten, werden diese automatisch mit einem räumlichen Index in Übereinstimmung mit der Richtlinie für die Indizierung der Sammlung indiziert. Wenn Sie mit einem DocumentDB SDK in einer dynamisch typisierten Sprache wie Python oder Node.js arbeiten, müssen Sie gültige GeoJSON erstellen.

**Erstellen von Dokumenten mit Geodaten in Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(collectionLink, userProfileDocument, function (err, created) {
        // additional code within the callback
    });

Wenn Sie mit dem .NET (oder Java) SDK arbeiten, können Sie die neuen "Point"- und "Polygon"-Klassen im Namespace "Microsoft.Azure.Documents.Spatial" verwenden, um Standortinformationen in Ihre Anwendungsobjekte einzubetten. Diese Klassen vereinfachen die Serialisierung und Deserialisierung räumlicher Daten in GeoJSON.

**Erstellen von Dokumenten mit Geodaten in .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        collection.SelfLink, 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Wenn Sie die Informationen zu Breiten- und Längengrad nicht haben, aber über die physischen Adressen und Standortnamen wie Stadt oder Land verfügen, können Sie die tatsächlichen Koordinaten mithilfe eines Geocodierungsdiensts wie Bing Maps REST Services nachschlagen. [Hier](https://msdn.microsoft.com/library/ff701713.aspx) erfahren Sie mehr zur Bing Maps-Geocodierung.

## Abfragen räumlicher Datentypen

Nachdem wir einen Blick auf das Einfügen von Geodaten geworfen haben, wollen wir uns nun das Abfragen dieser Daten mithilfe von DocumentDB sowie SQL und LINQ ansehen.

### Integrierte räumliche SQL-Funktionen
DocumentDB unterstützt die folgenden integrierten OGC-Funktionen (Open Geospatial Consortium ) für das Abfragen von Geodaten. Weitere Informationen zu sämtlichen integrierten Funktionen in der SQL-Sprache finden Sie unter [Abfragen von DocumentDB](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Verwendung</strong></td>
  <td><strong>Beschreibung</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Gibt den Abstand zwischen den beiden GeoJSON-Punktausdrücken zurück.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Gibt einen booleschen Ausdruck zurück, der angibt, ob der im ersten Argument angegebene GeoJSON-Punkt sich innerhalb des GeoJSON-Polygons im zweiten Argument befindet.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Gibt einen booleschen Wert zurück, der angibt, ob der angegebene GeoJSON-Punkt- oder -Polygon-Ausdruck gültig ist.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Gibt einen JSON-Wert mit einem booleschen Wert zurück, wenn der angegebene GeoJSON-Punkt oder -Polygon-Ausdruck gültig ist. Falls ungültig, wird außerdem der Grund als Zeichenfolge zurückgegeben.</td>
</tr>
</table>

Räumliche Funktionen können verwendet werden, um Entfernungsabfragen auf räumliche Daten anzuwenden. Hier ist z. B. eine Abfrage, die alle Familiendokumente zurückgibt, die sich innerhalb von 30 km von der angegebenen Position befinden. Dazu wird die integrierte ST\_DISTANCE-Funktion verwendet.

**Abfrage**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Ergebnisse**

    [{
      "id": "WakefieldFamily"
    }]

Wenn Sie die räumliche Indizierung in Ihre Indizierungsrichtlinie einschließen, werden "Entfernungsabfragen" effizient über den Index beantwortet. Weitere Informationen zur räumliche Indizierung finden Sie im Abschnitt weiter unten. Wenn Sie keinen räumlichen Index für die angegebenen Pfade haben, können Sie dennoch raumbezogene Abfragen ausführen, indem Sie den `x-ms-documentdb-query-enable-scan`-Anforderungsheader mit auf „True“ festgelegtem Wert angeben. In .NET erfolgt dies durch Übergeben des optionalen **FeedOptions**-Arguments an Abfragen mit auf „True“ festgelegter [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery)-Einstellung.

ST\_WITHIN kann verwendet werden, um zu prüfen, ob ein Punkt innerhalb eines Polygons liegt. Polygone werden häufig verwendet, um Umgrenzungen wie Postleitzahlen, Staatsgrenzen oder natürliche Gebilde darzustellen. Wenn Sie wiederum die räumliche Indizierung in Ihre Indizierungsrichtlinie einschließen, werden Abfragen nach enthaltenen Elementen effizient über den Index beantwortet.

Polygonargumente in ST\_WITHIN dürfen nur einen einzigen Ring enthalten, d. h. die Polygone dürfen keine Löcher aufweisen. Überprüfen Sie die [DocumentDB-Grenzen](documentdb-limits.md) auf die maximale Anzahl von Punkten in einem Polygon, die für eine ST\_WITHIN-Abfrage zulässig ist.

**Abfrage**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
    	'type':'Polygon', 
    	'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Ergebnisse**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE]Wenn (ähnlich wie bei der Funktionsweise nicht übereinstimmender Typen in DocumentDB-Abfragen) der in einem der Argumente angegebene Standortwert falsch formatiert oder ungültig ist, wird dieser mit **undefiniert** ausgewertet. Die ausgewerteten Dokumente werden aus den Abfrageergebnissen entfernt. Wenn Ihre Abfrage keine Ergebnisse zurückgibt, führen Sie ST\_ISVALIDDETAILED aus, um herauszufinden, warum der räumliche Typ ungültig ist.

ST\_ISVALID und ST\_ISVALIDDETAILED können verwendet werden, um zu prüfen, ob ein räumliches Objekt gültig ist. Die folgende Abfrage untersucht z. B. die Gültigkeit eines Punkts mit einem Längengradwert außerhalb des Gültigkeitsbereichs (-132,8). ST\_ISVALID gibt nur einen booleschen Wert zurück. ST\_ISVALIDDETAILED gibt den booleschen Wert und eine Zeichenfolge mit dem Grund zurück, warum das Argument als ungültig eingestuft wird.

** Abfrage **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Ergebnisse**

    [{
      "$1": false
    }]

Diese Funktionen können auch verwendet werden, um Polygone zu überprüfen. Beispielsweise verwenden wir hier ST\_ISVALIDDETAILED, um ein Polygon zu überprüfen, das nicht geschlossen ist.

**Abfrage**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
    	[ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
    	]]})

**Ergebnisse**

    [{
       "$1": { 
      	  "valid": false, 
      	  "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
      	}
    }]
    
### LINQ-Abfragen im .NET SDK

Das .NET SDK für DocumentDB stellt auch die Stub-Methoden `Distance()` und `Within()` für die Verwendung in LINQ-Ausdrücken bereit. Der LINQ-Anbieter für DocumentDB übersetzt diese Methodenaufrufe in entsprechende integrierte SQL-Funktionsaufrufe (ST\_DISTANCE bzw. ST\_WITHIN).

Hier ist ein Beispiel einer LINQ-Abfrage, die alle Dokumente in der DocumentDB-Sammlung findet, deren Wert "location" sich in einem Radius von 30 km um den angegebenen Punkt befindet.

**LINQ-Abfrage der Entfernung**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Und hier ist eine Abfrage für die Suche nach allen Dokumenten, deren Position sich im angegebenen Feld/Polygon befindet.

**LINQ-Abfrage nach enthaltenen Elementen**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(collection.SelfLink)
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Nachdem wir einen Blick auf das Abfragen von Dokumenten mithilfe von LINQ und SQL geworfen haben, lassen Sie uns nun untersuchen, wie DocumentDB für die räumliche Indizierung konfiguriert wird.

## Indizierung

Wie im Dokument zur [vom Schema unabhängigen Indizierung mit Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) beschrieben, haben wir das DocumentDB-Datenbankmodul so entworfen, dass es wirklich vom Schema unabhängig ist und erstklassige Unterstützung für JSON bietet. Das für Schreibvorgänge optimierte Datenbankmodul von DocumentDB versteht nun systemeigen räumliche Daten, die gemäß dem Standard GeoJSON dargestellt sind.

Kurz gesagt, die Geometrie wird von geodätischen Koordinaten auf eine 2D-Ebene projiziert und dann schrittweise mithilfe eines **Quadtrees** in Zellen unterteilt. Diese Zellen werden zu 1D basierend auf der Position der Zelle auf einer **raumfüllenden Hilbert-Kurve** zugeordnet, die die Lage von Punkten beibehält. Wenn Positionsdaten darüber hinaus indiziert werden, durchlaufen sie einen als **Mosaikarbeit** bezeichneten Prozess, bei dem alle Zellen, die eine Position schneiden, im DocumentDB-Index als Schlüssel indiziert und gespeichert werden. Zur Abfragezeit werden Argumente wie Punkte und Polygone auch in den Mosaikprozess einbezogen, um die entsprechenden Zellen-ID-Bereiche zu extrahieren, und dann zum Abrufen von Daten aus dem Index verwendet.

Wenn Sie eine Indizierungsrichtlinie angeben, die einen räumlichen Index für "/*" (alle Pfade) enthält, werden alle in der Sammlung gefundenen Punkte für effiziente räumliche Abfragen (ST\_WITHIN und ST\_DISTANCE) indiziert. Räumliche Indizes haben keinen Genauigkeitswert und verwenden stets einen Standardwert für die Genauigkeit.

Der folgende JSON-Ausschnitt zeigt eine Indizierungsrichtlinie mit aktivierter räumlicher Indizierung, d. h. dass alle in Dokumenten für räumliche Abfragen gefundenen GeoJSON-Punkte indiziert werden. Wenn Sie die Indizierungsrichtlinie im Azure-Portal ändern, können Sie die folgende JSON für die Indizierungsrichtlinie angeben, um die räumliche Indizierung für Ihre Sammlung zu aktivieren.

**JSON-Sammlungsindizierungsrichtlinie mit aktivierter räumlicher Indizierung**

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
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                }
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Hier ist ein Codeausschnitt in .NET, der veranschaulicht, wie eine Sammlung mit aktivierter räumlicher Indizierung für alle Pfade mit enthaltenen Punkten erstellt wird.

**Erstellen einer Sammlung mit räumlicher Indizierung**

    IndexingPolicy spatialIndexingPolicy = new IndexingPolicy();
    spatialIndexingPolicy.IncludedPaths.Add(new IncludedPath
    {
        Path = "/*",
        Indexes = new System.Collections.ObjectModel.Collection<Index>()
            {
                new RangeIndex(DataType.Number) { Precision = -1 },
                new RangeIndex(DataType.String) { Precision = -1 },
                new SpatialIndex(DataType.Point)
            }
    });

    Console.WriteLine("Creating new collection...");
    collection = await client.CreateDocumentCollectionAsync(dbLink, collectionDefinition);

Hier wird gezeigt, wie Sie eine vorhandene Sammlung so ändern können, dass die räumliche Indizierung für Punkte verwendet wird, die in Dokumenten gespeichert sind.

**Ändern einer vorhandenen Sammlung mit räumlicher Indizierung**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = spatialIndexingPolicy; 
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(collection.SelfLink);
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE]Wenn der GeoJSON-Wert "location" innerhalb des Dokuments fehlerhaft oder ungültig ist, wird er nicht für räumliche Abfragen indiziert. Sie können Werte von "location" mit ST\_ISVALID und ST\_ISVALIDDETAILED überprüfen.

## Nächste Schritte
Nachdem Sie die ersten Schritte mit räumlichen Daten in DocumentDB ausgeführt haben, haben Sie folgende Möglichkeiten:

- Starten der Programmierung mit den [.NET-Codebeispielen auf Github für räumliche Daten](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs)
- Praktisches Arbeiten mit Abfragen von Geodaten im [DocumentDB Query Playground](http://www.documentdb.com/sql/demo#geospatial)
- Weitere Informationen zu [DocumentDB-Abfragen](documentdb-sql-query.md)
- Weitere Informationen zu [DocumentDB-Indizierungsrichtlinien](documentdb-indexing-policies.md)

<!---HONumber=AcomDC_0107_2016-->