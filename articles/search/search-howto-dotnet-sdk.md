<properties
   pageTitle="Verwenden von Azure Search aus einer .NET Application | Microsoft Azure"
   description="Verwenden von Azure Search aus einer .NET-Anwendung"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/07/2015"
   ms.author="brjohnst"/>

# Verwenden von Azure Search aus einer .NET-Anwendung #

In diesem Artikel erfahren Sie, wie Sie Ihr [Azure Search-.NET-SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) schnell betriebsbereit machen. Dank .NET-SDK erhalten Sie in Ihrer Anwendung die vielfältigen Suchfunktionen von Azure Search.

## Inhalt des Azure Search-SDK ##

Das SDK enthält die Clientbibliothek `Microsoft.Azure.Search`. Mit dem SDK können Sie Ihre Indizes, Datenquellen und Indexer verwalten, Dokumente hochladen und verwalten und Abfragen ausführen, ohne sich mit den Details von HTTP und JSON befassen zu müssen.

Die Clientbibliothek definiert Klassen wie `Index`, `Field` und `Document` sowie Operationen wie `Indexes.Create` und `Documents.Search` für die Klassen `SearchServiceClient` und `SearchIndexClient`. Diese Klassen sind in die folgenden Namespaces aufgeteilt:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Die aktuelle Version des Azure Search-.NET-SDK ist eine Vorabversion. Wir freuen uns sehr über Ihr Feedback, das wir versuchen, in der ersten stabilen Version des Programms zu berücksichtigen. Sie können uns dieses über die [Seite „Feedback“](http://feedback.azure.com/forums/263029-azure-search) bereitstellen.

Das .NET-SDK unterstützt Version `2015-02-28` der Azure Search-REST-API, die auf [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx) dokumentiert ist. Diese Version unterstützt jetzt Microsoft-Sprachanalyseprogramme. Neue Funktionen, die *nicht* Teil dieser Version sind, wie die Unterstützung für den Suchparameter `moreLikeThis`, befinden sich noch im [Preview](search-api-2015-02-28-preview.md)-Stadium und stehen im SDK noch nicht zur Verfügung. Statusaktualisierungen zu diesen Funktionen finden Sie in den Abschnitten [Search service versioning](https://msdn.microsoft.com/library/azure/dn864560.aspx) (+++Versionsverwaltung für Suchdienst) oder [Latest updates to Azure Search](search-latest-updates.md) (+++Neueste Aktualisierungen in Azure Search).

Folgende Funktionen werden in diesem SDK nicht unterstützt:

  - [Verwaltungsvorgänge](https://msdn.microsoft.com/library/azure/dn832684.aspx): Die Verwaltungsvorgänge umfassen die Bereitstellung von Azure Search-Diensten sowie die Verwaltung der API-Schlüssel. Diese werden in einem künftigen Azure Search-.NET-Management-SDK unterstützt.

## Anforderungen für das SDK ##

1. Visual Studio 2013 oder eine neuere Version.

2. Ihr eigener Azure Search-Dienst. Zur Verwendung des SDK benötigen Sie den Namen Ihres Dienstes und mindestens einen API-Schlüssel. Informationen zu diesen Schritten finden Sie im Abschnitt [Create a service in the portal](search-create-service-portal.md) (+++Erstellen eines Dienstes im Portal).

3. Laden Sie das [NuGet-Paket](http://www.nuget.org/packages/Microsoft.Azure.Search) mit dem Azure Search-.NET-SDK in Visual Studio mithilfe von „NuGet-Pakete verwalten“ herunter. Suchen Sie unter NuGet.org nach dem Paketnamen `Microsoft.Azure.Search`. Vergewissern Sie sich, dass „Vorabversion einschließen“ aktiviert ist, um sicherzustellen, dass das SDK der Vorabversion in den Suchergebnissen angezeigt wird.

Das Azure-.NET-SDK unterstützt Anwendungen für .NET Framework 4.0 oder höher wie auch Windows Store-Apps für Windows 8.1 und Windows Phone 8.1. Silverlight wird nicht unterstützt.

## Schlüsselszenarien ##

In Ihrer Suchanwendung müssen Sie verschiedene Aktionen ausführen. In diesem Zusammenhang werden in diesem Lernprogramm die folgenden Schlüsselszenarien behandelt:

- Erstellen eines Index
- Füllen des Index mit Dokumenten
- Suche nach Dokumenten mit Volltextsuche und Filtern

Im nachfolgenden Beispielcode werden all diese Aktionen vorgeführt. Die Codeausschnitte können Sie gerne in Ihrer eigenen Anwendung übernehmen.

### Übersicht ###

Die nachfolgend untersuchte Beispielanwendung erstellt einen neuen Index mit dem Namen „hotels“, füllt diesen mit Dokumenten und führt danach einige Suchabfragen aus. Hier das Hauptprogramm mit dem allgemeinen Ablauf:

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

Dieses gehen wir nun Schritt für Schritt durch. Zunächst muss das Objekt `SearchServiceClient` erstellt werden. Mit diesem Objekt können Indizes verwaltet werden. Zur Erstellung dieses Objekts müssen Sie Ihren Azure Search-Dienstnamen sowie einen Admin-API-Schlüssel angeben.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE]Wenn Sie einen falschen Schlüssel angeben (z. B. einen Abfrageschlüssel, statt eines Admin-Schlüssels), gibt `SearchServiceClient` beim ersten Aufruf einer Operationsmethode (z. B. eines `Indexes.Create`) eine `CloudException` mit der Fehlermeldung „Forbidden“ (Unzulässig) aus. Überprüfen Sie in diesem Fall unsere API-Schlüssel.

Durch die nächsten Zeilen werden Methoden zum Erstellen eines Index mit dem Namen „hotels“ erstellt, wobei dieser, sofern er bereits vorhanden ist, zuerst gelöscht wird. Auf diese Methoden gehen wir später ein.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

Danach muss der Index gefüllt werden. Dazu benötigen wir einen `SearchIndexClient`. Dieses Objekt erhalten Sie entweder, indem Sie es erstellen oder indem Sie für `SearchServiceClient` `Indexes.GetClient` aufrufen. Der Einfachheit halber verwenden wir die zweite Methode.

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE]In einer typischen Suchanwendung erfolgen Verwaltung und Füllung des Index getrennt von Suchabfragen. `Indexes.GetClient` ist zum Füllen eines Index recht praktisch, da Sie kein weiteres `SearchCredentials` bereitstellen müssen. Vielmehr wird der Admin-Schlüssel, den Sie zum Erstellen des Objekts `SearchServiceClient` verwendet haben, auf das neue `SearchIndexClient`-Objekt übertragen. In dem Teil der Anwendung, in dem Abfragen ausgeführt werden, sollte das Objekt `SearchIndexClient` hingegen direkt erstellt werden, damit anstelle des Admin-Schlüssels ein Abfrageschlüssel übergeben werden kann. Dies entspricht dem Prinzip der geringsten Rechte und trägt dazu bei, die Anwendung sicherer zu machen. Weitere Informationen zu Admin- und Abfrageschlüsseln erhalten Sie [hier](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Nachdem das Objekt `SearchIndexClient` erstellt ist, kann der Index gefüllt werden. Dies erfolgt durch eine weitere Methode, die wir später besprechen.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Zum Schluss führen wir einige Suchabfragen aus und zeigen die Ergebnisse an. Dazu verwenden wir wiederum das Objekt `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Wenn Sie diese Anwendung mit einem gültigen Dienstnamen und API-Schlüssel ausführen, sollte die Ausgabe wie folgt aussehen:

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []
    Complete.  Press any key to end application...

Der vollständige Quellcode der Anwendung wird am Ende dieses Artikels bereitgestellt.

Nun sehen wir uns die von `Main` aufgerufenen Methoden näher an.

### Erstellen eines Index ###

Nachdem das Objekt `SearchServiceClient` erstellt ist, löscht `Main` den Index „hotels“, sofern dieser bereits vorhanden ist. Dies erfolgt mit folgender Methode:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Diese Methode verwendet das bereitgestellte Objekt `SearchServiceClient`, um zu überprüfen, ob der Index vorhanden ist, und löscht ihn gegebenenfalls.

> [AZURE.NOTE]Der Beispielcode in diesem Artikel verwendet der Einfachheit halber die synchronen Methoden des Azure Search-.NET-SDK. Für Ihre eigenen Anwendungen empfehlen wir aus Gründen der Skalierbarkeit und Reaktionsfähigkeit die asynchronen Methoden. In der oben gezeigten Methode können Sie `Exists` und `Delete` zum Beispiel auch durch `ExistsAsync` und `DeleteAsync` ersetzen.

Als Nächstes ruft `Main` die folgende Methode auf, um einen neuen Index mit dem Namen „hotels“ zu erstellen:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Diese Methode erstellt ein neues `Index`-Objekt mit einer Liste von `Field`-Objekten, die das Schema des neuen Index definiert. Jedes Feld weist einen Namen, einen Datentyp und mehrere Attribute auf, die das Suchverhalten des Felds definieren. Neben Feldern können Sie dem Index auch Wertungsprofile, Vorschläge oder CORS-Optionen hinzufügen, die im Beispiel fehlen, um es einfach zu halten. Weitere Informationen zum Indexobjekt und seinen Komponenten finden Sie in der SDK-Referenz auf [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx) sowie in der [Azure Search-REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn798935.aspx).

### Füllen des Index ###

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
            indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Diese Methode besteht aus vier Teilen. Im ersten Teil wird ein `Hotel`-Objektarray erstellt, das die Eingabedaten für den Upload in den Index bereitstellt. Diese Daten sind der Einfachheit halber fest codiert. In Ihrer eigenen Anwendung stammen die Daten vermutlich aus einer externen Datenquelle, beispielsweise aus einer SQL-­Datenbank.

Im zweiten Teil wird für jedes `Hotel` ein `IndexAction` erstellt. Diese werden dann in einem neuen `IndexBatch` zusammengefasst. Der Batch wird dann durch die Methode `Documents.Index` in den Azure Search-Index hochgeladen.

> [AZURE.NOTE]In diesem Beispiel werden nur Dokumente hochgeladen. Wenn Sie Änderungen mit einem vorhandenen Dokument zusammenführen oder ein Dokument löschen wollten, können Sie eine `IndexAction` mit dem entsprechenden `IndexActionType` erstellen. `IndexActionType` muss in diesem Beispiel nicht angegeben werden, da der Standardwert `Upload` ist.

Der dritte Teil dieser Methode ist ein Catch-Block, der einen wichtigen Fehlerfall bei der Indizierung abfängt. Falls der Azure Search-Dienst Dokumente des Batch nicht indizieren kann, löst `Documents.Index` eine `IndexBatchException` aus. Dies kann bei der Indizierung von Dokumenten geschehen, wenn der Dienst stark ausgelastet ist. **Es wird dringend empfohlen, einen solchen Fall in Ihrem Code explizit zu behandeln.** Zum Beispiel kann die Indizierung der zuvor nicht indizierten Dokumente nach einer Weile wieder aufgenommen werden oder der Vorgang kann, wie im Beispiel gezeigt, nach der Aufzeichnung des Fehlers fortgeführt werden. Je nach Datenkonsistenzanforderungen Ihrer Anwendung sind aber auch andere Lösungen möglich.

Der letzte Teil der Methode fügt eine Verzögerung von zwei Sekunden hinzu. Da die Indizierung in Ihrem Azure Search-Dienst asynchron erfolgt, muss die Beispielanwendung einen Augenblick warten, damit sichergestellt ist, dass die Dokumente für Suchen zur Verfügung stehen. Verzögerungen wie diese sind in der Regel nur in Demos, Tests und Beispielanwendungen erforderlich.

#### Verarbeitung von Dokumenten durch das .NET-SDK ####

Vielleicht fragen Sie sich, wie das Azure Search-.NET-SDK Instanzen einer benutzerdefinierten Klasse wie `Hotel` in einen Index hochladen kann. Um diese Frage zu beantworten, sehen wir uns die Klasse `Hotel` an:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

Das Erste, was Sie sehen, ist, dass jede öffentliche Eigenschaft von `Hotel` einem Feld in der Indexdefinition entspricht, allerdings mit einem bedeutsamen Unterschied: die Namen der Felder beginnen mit einem Kleinbuchstaben („camel-Schreibweise“), während die Namen der öffentlichen Eigenschaften von `Hotel` mit einem Großbuchstaben („Pascal-Schreibweise“) beginnen. Dies ist ein typisches Szenario für .NET-Anwendungen, die Datenbindungen durchführen, wenn das Zielschema außerhalb der Kontrolle des Anwendungsentwicklers liegt. Anstatt nun die Namenskonventionen von .NET mit Eigenschaftsnamen in camel-Schreibweise zu verletzen, können Sie das SDK mit dem Attribut `[SerializePropertyNamesAsCamelCase]` anweisen, Eigenschaftsnamen automatisch der camel-Schreibweise zuzuordnen.

Ebenso bemerkenswert an der Klasse `Hotel` sind die Datentypen der öffentlichen Eigenschaften. Die .NET-Typen dieser Eigenschaften stimmen mit den entsprechenden Feldtypen in der Indexdefinition überein. Die Zeichenfolgeeigenschaft `Category` passt zum Beispiel zum Feld `category`, das den Typ `Edm.String` hat. Ähnliche Zuordnungen bestehen auch zwischen `bool?` und `Edm.Boolean`, `DateTimeOffset?`, `Edm.DateTimeOffset` usw. Die jeweiligen Regeln für die Zuordnung eines Typs sind mit der `Documents.Get`-Methode auf [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx) dokumentiert.
 
> [AZURE.NOTE]Beim Entwerfen eigener Modellklassen für die Zuordnung zum Azure Search-Index müssen Sie Eigenschaften von Werttypen, z. B. `bool` und `int`, als Eigenschaften deklarieren, die NULL-Werte zulassen (z. B. `bool?` anstelle von `bool`). Dies ist erforderlich, da alle primitiven Feldtypen in Azure Search NULL-Werte zulassen. Wenn Sie Typen verwenden, die keine NULL-Werte zulassen, treten beim Indizieren von Standardwerten, z. B. `0` und `false`, unerwartete Ergebnisse auf. Insbesondere werden die Standardwerte während der Indizierung in NULL konvertiert. In einer zukünftigen Version des SDK verursacht stattdessen die Verwendung von Typen, die keine NULL-Werte zulassen, das Auslösen einer Ausnahme.

Diese Möglichkeit, eigene Klassen als Dokumente zu verwenden, funktioniert in beide Richtungen. Denn ebenso können Sie das SDK, wie im nächsten Abschnitt gezeigt, beim Abrufen von Suchergebnissen anweisen, diese automatisch in einen Typ Ihrer Wahl zu deserialisieren.

> [AZURE.NOTE]Das Azure Search-.NET-SDK unterstützt durch die Klasse `Document`, einer Schlüssel-/Wertzuordnung von Feldnamen zu Feldwerten, auch dynamisch typisierte Dokumente. Diese Art der Typisierung eignet sich für Szenarien, in denen Sie bei der Entwicklung noch nicht wissen, wie das Indexschema aussehen soll, oder in denen eine Bindung an bestimmte Modellklassen unpassend ist. Alle Methoden des SDK, die Dokumente verarbeiten, verfügen über Überladungen, welche die Klasse `Document` verwenden, wie auch stark typisierte Überladungen, die einen generischen Typparameter verwenden. Nur letztere Art von Überladung wird im Beispielcode dieses Lernprogramms verwendet. Weitere Informationen zur Klasse `Document` erhalten Sie [hier](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

### Suche nach Dokumenten im Index ###

Im letzten Schritt der Beispielanwendung wird nach Dokumenten im Index gesucht. Dazu wird folgende Methode verwendet:

    private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response)
        {
            Console.WriteLine(result.Document);
        }
    }

Diese Methode erstellt zunächst ein neues `SearchParameters`-Objekt. Damit werden zusätzliche Abfrageoptionen wie Sortierung, Filter, Paging und Facettierung festgelegt. In diesem Beispiel legen wir nur die Eigenschaft `Filter` fest.

Im nächsten Schritt wird die Suchabfrage bereits ausgeführt. Dazu wird die Methode `Documents.Search` verwendet: In diesem Fall übergeben wir den zu verwendenden Suchtext als Zeichenfolge sowie die zuvor erstellten Suchparameter. Außerdem geben wir `Hotel` als Typparameter für `Documents.Search` an, wodurch das SDK angewiesen wird, die Dokumente im Suchergebnis in Objekte des Typs `Hotel` zu deserialisieren.

Schließlich geht diese Methode durch alle Übereinstimmungen im Suchergebnis, wobei sie jedes Dokument auf der Konsole ausgibt.

Wir wollen uns nun näher ansehen, wie diese Methode aufgerufen wird:

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Im ersten Aufruf werden alle Dokumente mit den Abfrageausdrücken „fancy“ und „wifi“ gesucht. Im zweiten Aufruf ist der Suchtext auf „*“ gesetzt, d. h., es wird nach allem gesucht. Weitere Informationen zur Syntax von Such- bzw. Abfrageausdrücken finden Sie [hier](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Der zweite Aufruf verwendet den OData-`$filter`-Ausdruck `category eq 'Luxury'`. Diese Suche gibt damit nur Dokumente zurück, deren `category`-Feld genau der Zeichenfolge „Luxury“ entspricht. Weitere Informationen zur von Azure Search unterstützten OData-Syntax finden Sie [hier](https://msdn.microsoft.com/library/azure/dn798921.aspx).

Nachdem Sie nun wissen, was diese beiden Aufrufe bewirken, wird deren Ausgabe verständlicher:

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []

Die erste Suche gibt zwei Dokumente zurück. Das erste enthält im Namen die Zeichenfolge „fancy“, während das zweite im Feld `tags` die Zeichenfolge „wifi“ enthält. Die zweite Suche gibt ebenfalls zwei Dokumente zurück. Dies sind die einzigen Dokumente im Index, deren `category`-Feld auf „Luxury“ gesetzt ist.

Mit diesem Schritt ist das Lernprogramm abgeschlossen. Gerne können Sie sich aber noch weiter mit dem Programm vertraut machen. Im Abschnitt **Nächste Schritte** finden Sie eine Zusammenstellung weiterer Ressourcen zur Einarbeitung in Azure Search.

## Nächste Schritte ##

- Vertiefen Sie Ihre Kenntnisse mithilfe von [Videos und anderen Beispielen und Lernprogrammen](search-video-demo-tutorial-list.md).
- Erfahren Sie mehr über die Features und Funktionen in dieser Version des Azure Search-SDK: [Übersicht über Azure Search](https://msdn.microsoft.com/library/azure/dn798933.aspx).
- Lesen Sie den Abschnitt [Namenskonventionen](https://msdn.microsoft.com/library/azure/dn857353.aspx), um sich mit den Namensregeln für Objekte vertraut zu machen.
- Lesen Sie den Abschnitt zu den in Azure Search [unterstützten Datentypen](https://msdn.microsoft.com/library/azure/dn798938.aspx).


## Quellcode der Beispielanwendung ##

Nachfolgend sehen Sie den vollständigen Quellcode der in diesem Artikel besprochenen Beispielanwendung. Wenn Sie das Beispiel erstellen und ausführen möchten, müssen Sie die Platzhalter für den Dienstnamen und den API-Schlüssel in der Datei Program.cs durch Ihre eigenen Werte ersetzen.

Program.cs:

    using System;
    using System.Configuration;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.NETSDKSample
    {
        class Program
        {
            // This sample shows how to delete, create, upload documents and query an index
            static void Main(string[] args)
            {
                // Put your search service name here. This is the hostname portion of your service URL.
                // For example, if your service URL is https://myservice.search.windows.net, then your
                // service name is myservice.
                string searchServiceName = "myservice";

                string apiKey = "Put your API admin key here."

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteHotelsIndexIfExists(serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateHotelsIndex(serviceClient);

                SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

                Console.WriteLine("{0}", "Uploading documents...\n");
                UploadDocuments(indexClient);

                Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
                SearchDocuments(indexClient, searchText: "fancy wifi");

                Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
                SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists("hotels"))
                {
                    serviceClient.Indexes.Delete("hotels");
                }
            }

            private static void CreateHotelsIndex(SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = "hotels",
                    Fields = new[]
                    {
                        new Field("hotelId", DataType.String)                       { IsKey = true },
                        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                    }
                };

                serviceClient.Indexes.Create(definition);
            }

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
                    indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
                }
                catch (IndexBatchException e)
                {
                    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                    // the batch. Depending on your application, you can take compensating actions like delaying and
                    // retrying. For this simple demo, we just log the failed document keys and continue.
                    Console.WriteLine(
                        "Failed to index some of the documents: {0}",
                        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
                }

                // Wait a while for indexing to complete.
                Thread.Sleep(2000);
            }

            private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
            {
                // Execute search based on search text and optional filter
                var sp = new SearchParameters();

                if (!String.IsNullOrEmpty(filter))
                {
                    sp.Filter = filter;
                }

                DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
                foreach (SearchResult<Hotel> result in response)
                {
                    Console.WriteLine(result.Document);
                }
            }
        }
    }

Hotel.cs:

    using System;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.NETSDKSample
    {
        [SerializePropertyNamesAsCamelCase]
        public class Hotel
        {
            public string HotelId { get; set; }

            public string HotelName { get; set; }

            public double? BaseRate { get; set; }

            public string Category { get; set; }

            public string[] Tags { get; set; }

            public bool? ParkingIncluded { get; set; }

            public DateTimeOffset? LastRenovationDate { get; set; }

            public int? Rating { get; set; }

            public GeographyPoint Location { get; set; }

            public override string ToString()
            {
                return String.Format(
                    "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                    HotelId,
                    HotelName,
                    Category,
                    (Tags != null) ? String.Join(", ", Tags) : String.Empty);
            }
        }
    }
 

<!---HONumber=Oct15_HO3-->