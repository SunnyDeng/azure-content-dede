<properties 
	pageTitle="Sortieren von DocumentDB-Daten mit &quot;Order By&quot; | Azure" 
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
	ms.date="06/04/2015" 
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
Hier ist z. B. eine Abfrage zum Abrufen von Büchern in absteigender Reihenfolge von PublishTimestamp.

    SELECT * 
    FROM Books 
    ORDER BY Books.PublishTimestamp DESC

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

DocumentDB unterstützt die Sortierung für numerische Datentypen (keine Zeichenfolgen) und nur für eine einzige Order By-Eigenschaft pro Abfrage in dieser Vorschau des Features. Weitere Informationen finden Sie unter [Neue Entwicklungen](#Whats_coming_next).

## Konfigurieren einer Indexrichtlinie für "Order By"
Um "Order By"-Abfragen auszuführen, haben Sie folgende Möglichkeiten:

- Indizieren Sie bestimmte Pfade in Ihren Dokumenten mit maximaler Genauigkeit oder 
- Indizieren Sie *alle* Pfade rekursiv für die gesamte Auflistung mit maximaler Genauigkeit 

Die maximale Genauigkeit (dargestellt als die Genauigkeit von -1 in der JSON-Konfigurationsdatei) verwendet eine variable Anzahl von Bytes, je nach dem Wert, der indiziert wird. Deshalb gilt Folgendes:

- Bei Eigenschaften mit größeren Zahlenwerten, z. B. Epochenzeitstempel, hat die maximale Genauigkeit einen hohen Indizierungsaufwand. 
- Eigenschaften mit kleineren Zahlenwerten (Aufzählungen, Nullen, Postleitzahlen, Alter usw.) haben einen geringen Indizierungsaufwand.

Weitere Informationen finden Sie unter [DocumentDB-Indizierungsrichtlinien](documentdb-indexing-policies.md).

### Indizierung für "Order By" für alle numerischen Eigenschaften
Hier erfahren Sie, wie Sie eine Auflistung mit der Indizierung für "Order By" für jede (numerische) Eigenschaft erstellen können.
                   

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range, 
            Path = "/",
            NumericPrecision = -1 });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

### Indizierung für "Order By" für eine einzelne Eigenschaft
Hier erfahren Sie, wie Sie eine Auflistung mit der Indizierung für "Order By" für die "PublishTimestamp"-Eigenschaft erstellen können.

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range,
            Path = "/"PublishTimestamp"/?",
            NumericPrecision = -1
        });

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            Path = "/"
        });


## Beispiele
Sehen Sie sich dieses [Github-Beispielprojekt](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) an, das veranschaulicht, wie "Order By" verwendet wird, einschließlich Erstellen von Richtlinien für die Indizierung und Paging mit "Order By". Diese Beispiele sind Open-Source-basiert, und wir freuen uns, wenn Sie Pullanforderungen mit Beiträgen senden, von denen andere DocumentDB-Entwickler profitieren können. In den [Anleitungen für Beiträge](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) finden Sie Informationen dazu, wie Sie beitragen können.

## Neue Entwicklungen

Zukünftige Dienstaktualisierungen erweitern die hier eingeführte "Order By"-Unterstützung. Wir arbeiten an den folgenden Hinzufügungen und priorisieren die Veröffentlichung dieser Verbesserungen anhand Ihres Feedbacks:

- Dynamische Indizierungsrichtlinien: Unterstützung des Änderns der Indizierungsrichtlinie nach der Erstellung der Auflistung.
- Zeichenfolgenbereichsindizes: Index für die Unterstützung von Bereichsabfragen (>, <, >=, <=) für Zeichenfolgenwerte. Um dies zu unterstützen, wird ein neues umfangreicheres Schema für Indizierungsrichtlinien eingeführt.
- Unterstützung für "String Order By" in DocumentDB-Abfragen.
- Möglichkeit, Indizierungsrichtlinien über das Azure-Vorschauportal zu aktualisieren.
- Unterstützung für zusammengesetzte Indizes für ein effizienteres "Order By" und für "Order By" für mehrere Eigenschaften.

## Häufig gestellte Fragen

**Welche Plattformen/Versionen des SDK unterstützen Sortieren?**

Da "Order By" ein serverseitiges Update ist, müssen Sie keine neue Version des SDK herunterladen, um dieses Feature zu verwenden. Alle Plattformen und Versionen des SDK, einschließlich des serverseitigen JavaScript-SDK, können "Order By" mithilfe von SQL-Abfragezeichenfolgen verwenden. Wenn Sie LINQ verwenden, sollten Sie Version 1.2.0 oder höher von Nuget herunterladen.

**Was ist der erwartete Anforderungseinheitsverbrauch (Request Unit, RU) der "Order By"-Abfragen?**

Da "Order By" den DocumentDB-Index für Suchvorgänge verwendet, wird die Anzahl der Anforderungseinheiten, die von "Order By"-Abfragen verwendet werden, ungefähr den Abfragen ohne "Order By" entsprechen. Die Anzahl der Anforderungseinheiten hängt wie bei jedem anderen Vorgang für DocumentDB von den Größen/Formen Dokumente sowie von der Komplexität der Abfrage ab.


**Was ist der erwartete Indizierungsaufwand für "Order By"?**

Die Indizierungsspeicheraufwand ist proportional zur Anzahl der numerischen Eigenschaften. Im schlimmsten Fall beträgt der Indizierungsaufwand 100 % der Daten. Es gibt keinen Unterschied beim Durchsatz (Anforderungseinheiten) zwischen Bereichs-/"Order By"-Indizierung und der Standardhashindizierung.

**Hat diese Änderung Auswirkungen auf Abfragen ohne "Order By"?**

Aktuell gibt es keine Änderungen bezüglich der Funktionsweise von Abfragen ohne "Order By". Vor der Veröffentlichung dieser Funktion haben alle DocumentDB-Abfragen Ergebnisse in der Reihenfolge der Ressourcen-ID (_rid) zurückgegeben. Mit "Order By" werden Abfragen normalerweise in der angegebenen Reihenfolge der Werte zurückgegeben. In "Order By"-Abfragen wird _rid als sekundäre Sortierreihenfolge verwendet, wenn mehrere Dokumente mit demselben Wert zurückgegeben werden.

**Wie frage ich vorhandene Daten in DocumentDB mit "Order By" ab?**

Dies wird durch die Verfügbarkeit der Verbesserung der dynamischen Indizierungsrichtlinien unterstützt, die im Abschnitt [Neue Entwicklungen](what's-coming-next) erwähnt werden. Heute müssen Sie dazu Ihre Daten exportieren und erneut in eine neue DocumentDB-Auflistung importieren, die mit einem Bereichs-/"Order By"-Index erstellt wurde. Das DocumentDB-Importtool kann zum Migrieren der Daten zwischen Auflistungen verwendet werden.

**Was sind die aktuellen Einschränkungen von "Order By"?**

"Order By" kann nur für eine numerische Eigenschaft angegebene werden und nur, wenn der Bereich mit maximaler Genauigkeit (-1) indiziert wurde. "Order By" wird nur für Dokumentauflistungen unterstützt.

Sie können Folgendes nicht durchführen:
 
- "Order By" mit Zeichenfolgeneigenschaften (demnächst verfügbar).
- "Order By" mit internen Zeichenfolgeneigenschaften wie id, _rid und _self (demnächst verfügbar).- "Order By" mit Eigenschaften, die vom Ergebnis einer dokumentinternen Verknüpfung abgeleitet werden (demnächst verfügbar).
- "Order By" mit mehreren Eigenschaften (demnächst verfügbar).
- "Order By" mit berechneten Eigenschaften, z. B. das Ergebnis eines Ausdrucks oder eine UDF-Funktion/integrierte Funktion.
- "Order By" mit Abfragen für Datenbanken, Auflistungen, Benutzer, Berechtigungen oder Anlagen.

## Nächste Schritte

Verwenden Sie das [Github-Beispielprojekt](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) und starten Sie die Sortierung Ihrer Daten!

## Referenzen
* [DocumentDB-Abfragereferenz](documentdb-sql-query.md)
* [DocumentDB-Indizierungsrichtlinienreferenz](documentdb-indexing-policies.md)
* [DocumentDB-SQL-Referenz](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB-"Order By"-Beispiele](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=62-->