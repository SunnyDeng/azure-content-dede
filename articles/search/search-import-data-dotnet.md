<properties
	pageTitle="Importieren von Daten in Azure Search mithilfe von .NET | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Hochladen von Daten in einen Index in Azure Search mit dem .NET SDK oder der .NET-Bibliothek"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Importieren von Daten in Azure Search mithilfe von .NET
> [AZURE.SELECTOR]
- [Übersicht](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Dieser Artikel beschreibt, wie Sie einen Index mit dem [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) auffüllen. Der folgende Inhalt ist eine Teilmenge des Artikels [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md). Ausführliche Anweisungen finden Sie im übergeordneten Artikel.

Zu den Voraussetzungen für das Importieren von Daten in einen Index zählt ein zuvor erstellter Index.

Wenn es einen Index mit dem Namen „Hotels“ gibt, können Sie eine Methode zum Importieren von Daten folgendermaßen erstellen.

Im nächsten Schritt in `Main` wird der neu erstellte Index gefüllt. Dies erfolgt mit folgender Methode:

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Diese Methode besteht aus vier Teilen. Im ersten Teil wird ein `Hotel`-Objektarray erstellt, das die Eingabedaten für den Upload in den Index bereitstellt. Diese Daten sind der Einfachheit halber fest codiert. In Ihrer eigenen Anwendung stammen die Daten vermutlich aus einer externen Datenquelle, beispielsweise aus einer SQL-­Datenbank.

Im zweiten Teil wird für jedes `Hotel` ein `IndexAction` erstellt. Diese werden dann in einem neuen `IndexBatch` zusammengefasst. Der Batch wird dann durch die Methode `Documents.Index` in den Azure Search-Index hochgeladen.

> [AZURE.NOTE] In diesem Beispiel werden nur Dokumente hochgeladen. Wenn Sie Änderungen mit einem vorhandenen Dokument zusammenführen oder ein Dokument löschen möchten, können Sie die Methoden `Merge`, `MergeOrUpload` oder `Delete` verwenden.

Der dritte Teil dieser Methode ist ein Catch-Block, der einen wichtigen Fehlerfall bei der Indizierung abfängt. Falls der Azure Search-Dienst Dokumente des Batch nicht indizieren kann, löst `Documents.Index` eine `IndexBatchException` aus. Dies kann bei der Indizierung von Dokumenten geschehen, wenn der Dienst stark ausgelastet ist. **Es wird dringend empfohlen, einen solchen Fall in Ihrem Code explizit zu behandeln.** Zum Beispiel kann die Indizierung der zuvor nicht indizierten Dokumente nach einer Weile wieder aufgenommen werden oder der Vorgang kann, wie im Beispiel gezeigt, nach der Aufzeichnung des Fehlers fortgeführt werden. Je nach Datenkonsistenzanforderungen Ihrer Anwendung sind aber auch andere Lösungen möglich.

Der letzte Teil der Methode fügt eine Verzögerung von zwei Sekunden hinzu. Da die Indizierung in Ihrem Azure Search-Dienst asynchron erfolgt, muss die Beispielanwendung einen Augenblick warten, damit sichergestellt ist, dass die Dokumente für Suchen zur Verfügung stehen. Verzögerungen wie diese sind in der Regel nur in Demos, Tests und Beispielanwendungen erforderlich.

<!---HONumber=AcomDC_0302_2016-->