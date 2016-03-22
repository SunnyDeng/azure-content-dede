<properties
    pageTitle="Abfragen des Azure Search-Indexes mit dem .NET SDK | Microsoft Azure | Gehosteter Cloudsuchdienst"
    description="Erstellen Sie eine Suchabfrage in Azure Search, und verwenden Sie Suchparameter zum Filtern und Sortieren von Suchergebnissen."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# Abfragen des Azure Search-Index mit dem .NET SDK
> [AZURE.SELECTOR]
- [Übersicht](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Dieser Artikel beschreibt, wie Sie einen Index mithilfe des [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) abfragen.

[Erstellen Sie einen Azure Search-Index](search-what-is-an-index.md) und [füllen Sie ihn mit Daten](search-what-is-data-import.md), bevor Sie mit dieser exemplarischen Vorgehensweise beginnen.

Beachten Sie, dass der gesamte Beispielcode in diesem Artikel in C# geschrieben wurde. Den vollständigen Quellcode finden Sie [bei GitHub](http://aka.ms/search-dotnet-howto).

## I. Identifizieren des Abfrage-API-Schlüssel Ihres Azure Search-Diensts
Nach der Erstellung eines Azure Search-Index sind Sie nun fast bereit, Abfragen mit dem .NET SDK auszugeben. Zunächst müssen Sie aber einen der Abfrage-API-Schlüssel abrufen, die für den bereitgestellten Suchdienst generiert wurden. Das .NET SDK sendet diesen API-Schlüssel für jede Anforderung an Ihren Dienst. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, um die API-Schlüssel für Ihren Dienst zu suchen.
2. Wechseln Sie zum Blatt Ihres Azure Search-Diensts.
3. Klicken Sie auf das Schlüsselsymbol.

Der Dienst enthält *Admin-Schlüssel* und *Abfrageschlüssel*.

  - Die primären und sekundären *Admin-Schlüssel* gewähren Ihnen Vollzugriff auf alle Vorgänge. Dazu zählen die Dienstverwaltung und das Erstellen und Löschen von Indizes, Indexern und Datenquellen. Ihnen stehen zwei Schlüssel zur Verfügung, damit Sie den sekundären Schlüssel weiterhin nutzen können, wenn Sie den primären Schlüssel neu generieren möchten, und umgekehrt.
  - Die *Abfrageschlüssel* gewähren Ihnen Lesezugriff auf Indizes und Dokumente. Diese werden in der Regel auf Clientanwendungen verteilt, die Suchanfragen ausgeben.

Zum Abfragen eines Indexes können Sie einen der Abfrageschlüssel verwenden. Admin-Schlüssel können auch für Abfragen verwendet werden, Sie sollten jedoch einen Abfrageschlüssel in Ihrem Anwendungscode verwenden, da dies dem [Prinzip der geringsten Rechte](https://en.wikipedia.org/wiki/Principle_of_least_privilege) besser entspricht.

## II. Erstellen einer Instanz der SearchIndexClient-Klasse
Damit Sie Abfragen mit dem Azure Search .NET SDK ausgeben können, müssen Sie eine Instanz der Klasse `SearchIndexClient` erstellen. Diese Klasse verfügt über mehrere Konstruktoren. Für den gewünschten Konstruktor werden der Name Ihres Suchdiensts, der Indexname und ein `SearchCredentials`-Objekt als Parameter verwendet. `SearchCredentials` umfasst Ihren API-Schlüssel.

Der folgende Code erstellt ein neues `SearchIndexClient`-Element für den Index „hotels“ (der in [Erstellen eines Azure Search-Indexes mit dem .NET SDK](search-create-index-dotnet.md) erstellt wurde). Dazu werden Werte für den Suchdienstnamen und den API-Schlüssel verwendet, die in der Konfigurationsdatei der Anwendung gespeichert sind (`app.config` oder `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient` verfügt über eine `Documents`-Eigenschaft. Diese Eigenschaft stellt alle Methoden bereit, die Sie benötigen, um Azure Search-Indizes abzufragen.

## III. Abfragen Ihres Index
Die Suche mit dem .NET SDK erfolgt ganz einfach durch Aufrufen der `Documents.Search`-Methode für `SearchIndexClient`. Diese Methode verwendet einige Parameter, einschließlich des Suchtexts, zusammen mit einem `SearchParameters`-Objekt, mit dem die Abfrage weiter verfeinert werden kann.

#### Abfragetypen
Die zwei wichtigsten [Abfragetypen](search-query-overview.md#types-of-queries), die Sie verwenden werden, sind `search` und `filter`. Eine `search`-Abfrage sucht nach einem oder mehreren Begriffen in allen _durchsuchbaren_ Feldern in Ihrem Index. Eine `filter`-Abfrage wertet einen booleschen Ausdruck für alle _filterbaren_ Felder in einem Index aus.

Suchen und Filtern erfolgt mithilfe der `Documents.Search`-Methode. Eine Suchabfrage kann im Parameter `searchText` und ein Filterausdruck kann in der Eigenschaft `Filter` der Klasse `SearchParameters` übergeben werden. Um ohne Suche zu filtern, übergeben Sie einfach `"*"` für den Parameter `searchText`. Wenn Sie ohne Filter suchen möchten, legen Sie die Eigenschaft `Filter` nicht fest, oder übergeben Sie einfach keine `SearchParameters`-Instanz.

#### Beispielabfragen

Der folgende Beispielcode zeigt verschiedene Methoden zum Abfragen des Index „hotel“, der unter [Erstellen eines Azure Search-Indexes mit dem .NET SDK](search-create-index-dotnet.md#DefineIndex) definiert wurde. Beachten Sie, dass die in den Suchergebnissen zurückgegebenen Dokumente Instanzen der Klasse `Hotel` sind, die unter [Importieren von Daten in Azure Search mit dem .NET SDK](search-import-data-dotnet.md#HotelClass) definiert wurde. Der Beispielcode nutzt eine `WriteDocuments`-Methode, um die Suchergebnisse auf der Konsole auszugeben. Diese Methode wird im nächsten Abschnitt beschrieben.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## IV. Verarbeiten von Suchergebnissen
Die Methode `Documents.Search` gibt ein `DocumentSearchResult`-Objekt zurück, das die Ergebnisse der Abfrage enthält. Das Beispiel im vorherigen Abschnitt verwendet eine Methode namens `WriteDocuments`, um die Suchergebnisse auf der Konsole auszugeben:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Hier ist dargestellt, wie die Ergebnisse für die Abfragen im vorherigen Abschnitt aussehen. Dabei wird davon ausgegangen, dass der Index „hotels“ mit den Beispieldaten unter [Importieren von Daten in Azure Search mit dem .NET SDK](search-import-data-dotnet.md) gefüllt wird:

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

Der obige Beispielcode verwendet die Konsole zum Ausgeben von Suchergebnissen. Sie müssen ebenso Suchergebnisse in Ihrer eigenen Anwendung anzeigen. Ein Beispiel zum Rendern von Suchergebnissen in einer ASP.NET MVC-basierten Webanwendung finden Sie [hier auf GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample).

<!---HONumber=AcomDC_0316_2016-->