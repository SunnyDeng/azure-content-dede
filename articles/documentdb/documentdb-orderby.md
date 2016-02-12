<properties 
	pageTitle="Sortieren von DocumentDB-Daten mit ";Order By"; | Microsoft Azure" 
	description="Erfahren Sie, wie ORDER BY in DocumentDB-Abfragen in LINQ und SQL verwendet wird und wie eine Indizierungsrichtlinie für ORDER BY-Abfragen angegeben wird." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="arramac"/>

# Sortieren von DocumentDB-Daten mit "Order By"
Microsoft Azure DocumentDB unterstützt Dokumentabfragen mit SQL über JSON-Dokumente. Abfrageergebnisse können mit der ORDER BY-Klausel in SQL-Abfrageanweisungen sortiert werden.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

- Wie erstelle ich Abfragen mit "Order By"?
- Wie konfiguriere ich eine Indexrichtlinie für "Order By"?
- Neue Entwicklungen

[Beispiele](#samples) und [Häufig gestellte Fragen](#faq) werden ebenfalls bereitgestellt.

Eine vollständige Referenz zu SQL-Abfragen finden Sie unter der [DocumentDB-Abfragelernprogramm](documentdb-sql-query.md).

## Erstellen von Abfragen mit "Order By"
Wie in ANSI-SQL können Sie jetzt bei DocumentDB-Abfragen eine optionale "Order By"-Klausel in SQL-Anweisungen einschließen. Die Klausel kann ein optionales ASC/DESC-Argument enthalten, um die Reihenfolge anzugeben, in der Ergebnisse abgerufen werden sollen.

### Sortieren mit SQL
Hier sehen Sie z. B. eine Abfrage zum Abrufen von Büchern in absteigender Reihenfolge ihrer Titel.

    SELECT * 
    FROM Books 
    ORDER BY Books.Title DESC

### Sortieren mit SQL und Filtern
Verwenden eine geschachtelte Eigenschaft innerhalb von Dokumenten wie Books.ShippingDetails.Weight für das Sortieren verwenden, und Sie können zusätzliche Filter in der WHERE-Klausel in Kombination mit "Order By" wie in diesem Beispiel angeben:

    SELECT * 
    FROM Books 
	WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### Sortieren mithilfe des LINQ-Anbieters für .NET
Mit .NET SDK, Version 1.2.0 und höher, können Sie auch die "OrderBy()"- oder "OrderByDescending()"-Klausel in LINQ-Abfragen verwenden, wie in diesem Beispiel gezeigt wird:

    foreach (Book book in client.CreateDocumentQuery<Book>(booksCollection.SelfLink)
        .OrderBy(b => b.PublishTimestamp)) 
    {
        // Iterate through books
    }

### Sortieren mit Paging mithilfe des .NET SDK
Mit der systemeigenen Pagingunterstützung in den DocumentDB-SDKs können Sie jeweils eine Seite mit Ergebnissen abrufen, wie im folgenden .NET-Codeausschnitt gezeigt wird. Hier rufen Sie jedes Mal bis zu 10 Ergebnisse mit der "FeedOptions.MaxItemCount"- und der "IDocumentQuery"-Schnittstelle ab.

    var booksQuery = client.CreateDocumentQuery<Book>(
        booksCollection.SelfLink,
        "SELECT * FROM Books ORDER BY Books.PublishTimestamp DESC"
        new FeedOptions { MaxItemCount = 10 })
      .AsDocumentQuery();
            
    while (booksQuery.HasMoreResults) 
    {
        foreach(Book book in await booksQuery.ExecuteNextAsync<Book>())
        {
            // Iterate through books
        }
    }

DocumentDB unterstützt die Sortierung mit einer einzelnen numerischen, Zeichenfolgen- oder booleschen Eigenschaft pro Abfrage. Weitere Abfragetypen werden in Kürze zur Verfügung stehen. Weitere Informationen finden Sie unter [Neue Entwicklungen](#Whats_coming_next).

## Konfigurieren einer Indexrichtlinie für "Order By"

Denken Sie daran, dass DocumentDB zwei Arten von Indizes (Hash und Bereich) unterstützt, die für unterschiedliche Pfade/Eigenschaften, Datentypen (Zeichenfolgen/Zahlen) und auf verschiedene Präzisionswerte (maximale Genauigkeit oder einen festen Genauigkeitswert) festgelegt werden können. Da DocumentDB standardmäßig Hash-Indizes verwendet, müssen Sie eine neue Sammlung mit einer benutzerdefinierten Indizierungsrichtlinie mit „Bereich“ für Zahlen, Zeichenfolgen oder beide Optionen erstellen, um „Order By“ zu verwenden.

>[AZURE.NOTE] Indizes für Zeichenfolgenbereiche wurden am 7. Juli 2015 mit der REST-API-Version 2015-06-03 eingeführt. Um Richtlinien für „Order By“ für Zeichenfolgen zu erstellen, müssen Sie die SDK-Version 1.2.0 des .NET-SDK oder Version 1.1.0 des Python-, Node.js- oder Java-SDK verwenden.
>
>Vor der REST-API-Version 2015-06-03 war „Hash“ die Standardsammlung der Indizierungsrichtlinie für Zeichenfolgen und Zahlen. Dies wurde in „Hash“ für Zeichenfolgen und „Bereich“ für Zahlen geändert.

Weitere Informationen finden Sie unter [DocumentDB-Indizierungsrichtlinien](documentdb-indexing-policies.md).

### Indizierung für „Order By“ für alle Eigenschaften
Hier erfahren Sie, wie Sie eine Sammlung mit „All Range“-Indizierung für „Order By“ für alle bzw. alle numerischen Eigenschaften oder Zeichenfolgeneigenschaften erstellen, die in JSON-Dokumenten angezeigt werden. Hier steht „/ *“ für alle JSON-Eigenschaften/Pfade in der Sammlung, und „-1“ steht für die maximale Genauigkeit.
                   
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

>[AZURE.NOTE] Beachten Sie, dass „Order By“ nur Ergebnisse der Datentypen (Zeichenfolge und Anzahl) ausgibt, die mit einem RangeIndex indiziert werden. Verwenden Sie z. B. die Standardindizierungsrichtlinie, die nur RangeIndex für Zahlen verwendet, wird „Order By“ für einen Pfad mit Zeichenfolgenwerten keine Dokumente zurückgeben.

### Indizierung für "Order By" für eine einzelne Eigenschaft
Hier erfahren Sie, wie Sie eine Sammlung mit der Indizierung für „Order By“ für die „Title“-Eigenschaft erstellen können, bei der es sich um eine Zeichenfolge handelt. Hierfür gibt es zwei Pfade – einen für die „Title“-Eigenschaft (/Title/?) mit der Bereichsindizierung und den anderen für jede andere Eigenschaft mit dem Standardindizierungsschema („Hash“ für Zeichenfolgen und „Bereich“ für Zahlen).
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    // Use defaults which are:
    // (a) for strings, use Hash with precision 3 (just equality queries)
    // (b) for numbers, use Range with max precision (for equality, range and order by queries)
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*",
            Indexes = new Collection<Index> { 
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }            
        });

## Beispiele
Sehen Sie sich dieses [Github-Beispielprojekt](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) an, das veranschaulicht, wie "Order By" verwendet wird, einschließlich Erstellen von Richtlinien für die Indizierung und Paging mit "Order By". Diese Beispiele sind Open-Source-basiert, und wir freuen uns, wenn Sie Pullanforderungen mit Beiträgen senden, von denen andere DocumentDB-Entwickler profitieren können. In den [Anleitungen für Beiträge](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) finden Sie Informationen dazu, wie Sie beitragen können.

## Häufig gestellte Fragen

**Welche Plattformen/Versionen des SDK unterstützen Sortieren?**

Um Sammlungen mit den erforderlichen Indizierungsrichtlinien für „Order By“ zu erstellen, müssen Sie die neueste Version des SDK (1.2.0 für .NET und 1.1.0 für Node.js, JavaScript, Python und Java) herunterladen. .NET SDK 1.2.0 ist auch erforderlich, um „OrderBy()“ und „OrderByDescending()“ in LINQ-Ausdrücken verwenden.


**Was ist der erwartete Anforderungseinheitsverbrauch (Request Unit, RU) der "Order By"-Abfragen?**

Da "Order By" den DocumentDB-Index für Suchvorgänge verwendet, wird die Anzahl der Anforderungseinheiten, die von "Order By"-Abfragen verwendet werden, ungefähr den Abfragen ohne "Order By" entsprechen. Die Anzahl der Anforderungseinheiten hängt wie bei jedem anderen Vorgang für DocumentDB von den Größen/Formen Dokumente sowie von der Komplexität der Abfrage ab.


**Was ist der erwartete Indizierungsaufwand für "Order By"?**

Die Indizierungsspeicheraufwand verhält sich proportional zur Anzahl der Eigenschaften. Im schlimmsten Fall beträgt der Indizierungsaufwand 100 % der Daten. Es gibt keinen Unterschied beim Durchsatz (Anforderungseinheiten) zwischen Bereichs-/"Order By"-Indizierung und der Standardhashindizierung.

**Wie frage ich vorhandene Daten in DocumentDB mit "Order By" ab?**

Um die Ergebnisse der Abfrage mit „Order By“ zu sortieren, muss die Indizierungsrichtlinie der Sammlung so geändert werden, dass ein Bereichsindextyp auf die Eigenschaft angewendet wird, nach der sortiert wird. Weitere Informationen finden Sie unter [Ändern der Indizierungsrichtlinie](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection).

**Was sind die aktuellen Einschränkungen von "Order By"?**

„Order By“ kann nur für eine Eigenschaft angegeben werden (numerisch oder Zeichenfolgen), wenn der Bereich mit der maximalen. Genauigkeit (-1) indiziert wird.

Sie können Folgendes nicht durchführen:
 
- "Order By" mit internen Zeichenfolgeneigenschaften wie id, \_rid und \_self (demnächst verfügbar).
- "Order By" mit Eigenschaften, die vom Ergebnis einer dokumentinternen Verknüpfung abgeleitet werden (demnächst verfügbar).
- "Order By" mit mehreren Eigenschaften (demnächst verfügbar).
- „Order By“ mit Abfragen für Datenbanken, Sammlungen, Benutzer, Berechtigungen oder Anlagen (in Kürze verfügbar).
- "Order By" mit berechneten Eigenschaften, z. B. das Ergebnis eines Ausdrucks oder eine UDF-Funktion/integrierte Funktion.

## Nächste Schritte

Verwenden Sie das [Github-Beispielprojekt](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) und starten Sie die Sortierung Ihrer Daten!

## Referenzen
* [DocumentDB-Abfragereferenz](documentdb-sql-query.md)
* [DocumentDB-Indizierungsrichtlinienreferenz](documentdb-indexing-policies.md)
* [DocumentDB-SQL-Referenz](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB-"Order By"-Beispiele](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

<!---HONumber=AcomDC_0204_2016-->