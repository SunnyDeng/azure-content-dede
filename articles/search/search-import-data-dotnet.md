<properties
    pageTitle="Hochladen von Daten in Azure Search mit dem .NET SDK | Microsoft Azure | Gehosteter Cloudsuchdienst"
    description="Hier erfahren Sie, wie Sie Daten mit dem .NET SDK in einen Index in Azure Search hochladen."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# Hochladen von Daten in Azure Search mit dem .NET SDK
> [AZURE.SELECTOR]
- [Übersicht](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)

In diesem Artikel erfahren Sie, wie Sie das [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) zum Importieren von Daten in einen Azure Search-Index verwenden.

[Erstellen Sie einen Azure Search-Index](search-what-is-an-index.md), bevor Sie mit dieser exemplarischen Vorgehensweise beginnen. Dieser Artikel setzt auch voraus, dass Sie bereits wie unter [Erstellen eines Azure Search-Index mit dem .NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient) beschrieben ein `SearchServiceClient`-Objekt erstellt haben.

Beachten Sie, dass der gesamte Beispielcode in diesem Artikel in C# geschrieben wurde. Den vollständigen Quellcode finden Sie [auf GitHub](http://aka.ms/search-dotnet-howto).

Gehen Sie wie folgt vor, um Dokumente mit dem .NET SDK in Ihren Index zu übertragen:

  1. Erstellen Sie ein `SearchIndexClient`-Objekt, um eine Verbindung mit dem Suchindex herzustellen.
  2. Erstellen Sie ein `IndexBatch`-Element mit den Dokumenten, die hinzugefügt, geändert oder gelöscht werden sollen.
  3. Rufen Sie die `Documents.Index`-Methode des `SearchIndexClient`-Elements auf, um das `IndexBatch`-Element an den Suchindex zu senden.

## I. Erstellen einer Instanz der SearchIndexClient-Klasse
Um Daten mit dem Azure Search .NET SDK in den Index zu importieren, müssen Sie eine Instanz der `SearchIndexClient`-Klasse erstellen. Sie können diese Instanz selbst erstellen. Es ist aber einfacher, wenn Sie bereits über eine `SearchServiceClient`-Instanz zum Aufrufen der `Indexes.GetClient`-Methode verfügen. Hier ist beispielsweise angegeben, wie Sie ein `SearchIndexClient`-Element für den Index mit dem Namen „hotels“ über ein `SearchServiceClient`-Element mit dem Namen `serviceClient` erhalten:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] In einer typischen Suchanwendung erfolgen Verwaltung und Füllung des Index getrennt von Suchabfragen. `Indexes.GetClient` ist zum Füllen eines Index recht praktisch, da Sie kein weiteres `SearchCredentials` bereitstellen müssen. Vielmehr wird der Admin-Schlüssel, den Sie zum Erstellen des Objekts `SearchServiceClient` verwendet haben, auf das neue `SearchIndexClient`-Objekt übertragen. In dem Teil der Anwendung, in dem Abfragen ausgeführt werden, sollte das Objekt `SearchIndexClient` hingegen direkt erstellt werden, damit anstelle des Admin-Schlüssels ein Abfrageschlüssel übergeben werden kann. Dies entspricht dem [Prinzip der geringsten Rechte](https://en.wikipedia.org/wiki/Principle_of_least_privilege) und trägt dazu bei, die Anwendung sicherer zu machen. Weitere Informationen zu Administrator- und Abfrageschlüsseln finden Sie in der Referenz zur [Azure Search REST-API auf der MSDN-Website](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient` verfügt über eine `Documents`-Eigenschaft. Mit dieser Eigenschaft werden alle Methoden bereitgestellt, die Sie benötigen, um Dokumente im Index hinzuzufügen, zu ändern, zu löschen oder abzufragen.

## II. Entscheiden Sie, welche Indizierungsaktion verwendet werden soll.
Zum Importieren von Daten mit dem .NET SDK müssen Sie Ihre Daten in einem `IndexBatch`-Objekt verpacken. Ein `IndexBatch`-Element kapselt eine Sammlung mit `IndexAction`-Objekten. Jedes Objekt enthält ein Dokument und eine Eigenschaft, die Azure Search mitteilt, welche Aktion für das jeweilige Dokument durchgeführt werden soll (Hochladen, Zusammenführen, Löschen usw.). Je nachdem, welche der folgenden Aktionen Sie wählen, müssen für jedes Dokument nur bestimmte Felder eingefügt werden:

Aktion | Beschreibung | Erforderliche Felder für jedes Dokument | Hinweise
--- | --- | --- | ---
`Upload` | Eine `Upload`-Aktion entspricht „upsert“, wobei neue Dokumente eingefügt und bestehende Dokumente aktualisiert/ersetzt werden. | Schlüssel und alle anderen zu definierenden Felder | Wenn ein bestehendes Dokument aktualisiert/ersetzt wird, werden alle in der Anforderung nicht festgelegten Felder auf `null` festgelegt. Dies tritt auch auf, wenn das Feld zuvor auf einen Wert festgelegt wurde, der nicht Null ist.
`Merge` | Aktualisiert ein bestehendes Dokument mit den angegebenen Feldern. Wenn das Dokument im Index nicht vorhanden ist, schlägt die Zusammenführung fehl. | Schlüssel und alle anderen zu definierenden Felder | Jedes Feld, das Sie in einer Zusammenführung angeben, ersetzt das vorhandene Feld im Dokument. Dies beinhaltet auch Felder vom Typ `DataType.Collection(DataType.String)`. Wenn das Dokument beispielsweise das Feld `tags` mit dem Wert `["budget"]` enthält und Sie eine Zusammenführung mit dem Wert `["economy", "pool"]` für `tags` durchführen, hat das Feld `tags` am Ende den Wert `["economy", "pool"]`. Der Wert lautet nicht `["budget", "economy", "pool"]`.
`MergeOrUpload` | Verhält sich wie `Merge`, wenn im Index bereits ein Dokument mit dem entsprechenden Schlüssel vorhanden ist. Wenn das Dokument nicht vorhanden ist, verhält es sich wie `Upload` mit einem neuen Dokument. | Schlüssel und alle anderen zu definierenden Felder | - `Delete` | Entfernt das festgelegte Dokument aus dem Index. | Nur Schlüssel | Alle festgelegten Felder mit Ausnahme des Schlüsselfelds werden ignoriert. Wenn Sie ein einzelnes Feld aus einem Dokument entfernen möchten, verwenden Sie stattdessen `Merge`, und setzen Sie das Feld ausdrücklich auf Null.

Sie können angeben, welche Aktion Sie verwenden möchten, indem Sie verschiedene statische Methoden der Klassen `IndexBatch` und `IndexAction` verwenden. Dies wird im nächsten Abschnitt gezeigt.

## III. Erstellen des IndexBatch-Elements
Nachdem Sie nun wissen, welche Aktionen für die Dokumente durchgeführt werden, können Sie das `IndexBatch`-Element erstellen. Im folgenden Beispiel wird veranschaulicht, wie Sie einen Batch mit einigen anderen Aktionen erstellen. Beachten Sie, dass im Beispiel eine benutzerdefinierte Klasse mit dem Namen `Hotel` verwendet wird, die einem Dokument im Index „hotels“ zugeordnet ist.

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

In diesem Fall verwenden wir `Upload`, `MergeOrUpload` und `Delete` als Suchaktionen. Diese werden von den Methoden angegeben, die für die `IndexAction`-Klasse aufgerufen werden.

Wir gehen davon aus, dass der Index in diesem Beispiel („hotels“) bereits mit einigen Dokumenten gefüllt ist. Beachten Sie, dass bei `MergeOrUpload` nicht alle möglichen Dokumentfelder festgelegt werden mussten und bei `Delete` nur der Dokumentschlüssel (`HotelId`) definiert werden musste.

Beachten Sie außerdem, dass in einer Indizierungsanforderung nur bis zu 1.000 Dokumente enthalten sein können.

> [AZURE.NOTE] In diesem Beispiel wenden wir unterschiedliche Aktionen auf unterschiedliche Dokumente an. Falls Sie für alle Dokumente im Batch die gleichen Aktionen durchführen möchten, können Sie die anderen statischen Methoden von `IndexBatch` verwenden, anstatt `IndexBatch.New` aufzurufen. Beispielsweise können Sie Batches erstellen, indem Sie `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` oder `IndexBatch.Delete` aufrufen. Für diese Methoden wird anstelle von `IndexAction`-Objekten eine Sammlung mit Dokumenten verwendet (in diesem Beispiel Objekte vom Typ `Hotel`).

## IV. Importieren von Daten in den Index
Nachdem Sie nun über ein initialisiertes `IndexBatch`-Objekt verfügen, können Sie es an den Index senden, indem Sie `Documents.Index` für das `SearchIndexClient`-Objekt aufrufen. Das folgende Beispiel zeigt, wie Sie `Index` aufrufen, sowie einige zusätzliche Schritte, die Sie ausführen müssen:

```csharp
try
{
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

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Beachten Sie das `try`/`catch`-Element, mit dem der Aufruf der `Index`-Methode umgeben ist. Mit dem Catch-Block wird ein wichtiger Fehlerfall im Zusammenhang mit der Indizierung behandelt. Falls der Azure Search-Dienst Dokumente des Batch nicht indizieren kann, löst `Documents.Index` eine `IndexBatchException` aus. Dies kann bei der Indizierung von Dokumenten geschehen, wenn der Dienst stark ausgelastet ist. **Es wird dringend empfohlen, einen solchen Fall in Ihrem Code explizit zu behandeln.** Zum Beispiel kann die Indizierung der zuvor nicht indizierten Dokumente nach einer Weile wieder aufgenommen werden oder der Vorgang kann, wie im Beispiel gezeigt, nach der Aufzeichnung des Fehlers fortgeführt werden. Je nach Datenkonsistenzanforderungen Ihrer Anwendung sind aber auch andere Lösungen möglich.

Schließlich wird der Code im obigen Beispiel um zwei Sekunden verzögert. Da die Indizierung in Ihrem Azure Search-Dienst asynchron erfolgt, muss die Beispielanwendung einen Augenblick warten, damit sichergestellt ist, dass die Dokumente für Suchen zur Verfügung stehen. Verzögerungen wie diese sind in der Regel nur in Demos, Tests und Beispielanwendungen erforderlich.

<a name="HotelClass"></a>
### Behandeln von Dokumenten durch das .NET-SDK

Vielleicht fragen Sie sich, wie das Azure Search-.NET-SDK Instanzen einer benutzerdefinierten Klasse wie `Hotel` in einen Index hochladen kann. Zur Beantwortung dieser Frage sehen wir uns die `Hotel`-Klasse an, die dem unter [Erstellen eines Azure Search-Index mit dem .NET SDK](search-create-index-dotnet.md#DefineIndex) definierten Indexschema entspricht:

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Das Erste, was Sie sehen, ist, dass jede öffentliche Eigenschaft von `Hotel` einem Feld in der Indexdefinition entspricht, allerdings mit einem bedeutsamen Unterschied: die Namen der Felder beginnen mit einem Kleinbuchstaben („camel-Schreibweise“), während die Namen der öffentlichen Eigenschaften von `Hotel` mit einem Großbuchstaben („Pascal-Schreibweise“) beginnen. Dies ist ein typisches Szenario für .NET-Anwendungen, die Datenbindungen durchführen, wenn das Zielschema außerhalb der Kontrolle des Anwendungsentwicklers liegt. Anstatt nun die Namenskonventionen von .NET mit Eigenschaftsnamen in camel-Schreibweise zu verletzen, können Sie das SDK mit dem Attribut `[SerializePropertyNamesAsCamelCase]` anweisen, Eigenschaftsnamen automatisch der camel-Schreibweise zuzuordnen.

> [AZURE.NOTE] Das Azure Search .NET SDK verwendet die [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm)-Bibliothek, um die Objekte Ihres benutzerdefinierten Modells nach JSON zu serialisieren und aus JSON zu deserialisieren. Sie können diese Serialisierung bei Bedarf anpassen. Weitere Informationen finden Sie unter [Upgrade auf Version 1.1 des Azure Search .NET SDK](search-dotnet-sdk-migration.md#WhatsNew). Ein Beispiel hierfür ist die Verwendung des Attributs `[JsonProperty]` für die `DescriptionFr`-Eigenschaft im obigen Beispielcode.

Ebenso bemerkenswert an der Klasse `Hotel` sind die Datentypen der öffentlichen Eigenschaften. Die .NET-Typen dieser Eigenschaften stimmen mit den entsprechenden Feldtypen in der Indexdefinition überein. Die Zeichenfolgeeigenschaft `Category` passt zum Beispiel zum Feld `category`, das den Typ `DataType.String` hat. Ähnliche Zuordnungen bestehen auch zwischen `bool?` und `DataType.Boolean`, `DateTimeOffset?`, `DataType.DateTimeOffset` usw. Die jeweiligen Regeln für die Zuordnung eines Typs sind mit der `Documents.Get`-Methode auf [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx) dokumentiert.

Diese Möglichkeit, eigene Klassen als Dokumente zu verwenden, funktioniert in beide Richtungen. Denn ebenso können Sie das SDK, wie im [nächsten Artikel](search-query-dotnet.md) gezeigt, beim Abrufen von Suchergebnissen anweisen, diese automatisch in einen Typ Ihrer Wahl zu deserialisieren.

> [AZURE.NOTE] Das Azure Search-.NET-SDK unterstützt durch die Klasse `Document`, einer Schlüssel-/Wertzuordnung von Feldnamen zu Feldwerten, auch dynamisch typisierte Dokumente. Diese Art der Typisierung eignet sich für Szenarien, in denen Sie bei der Entwicklung noch nicht wissen, wie das Indexschema aussehen soll, oder in denen eine Bindung an bestimmte Modellklassen unpassend ist. Alle Methoden des SDK, die Dokumente verarbeiten, verfügen über Überladungen, welche die Klasse `Document` verwenden, wie auch stark typisierte Überladungen, die einen generischen Typparameter verwenden. Nur letztere Art von Überladung wird im Beispielcode dieses Artikels verwendet. Weitere Informationen zur `Document`-Klasse finden Sie auf der [MSDN-Website](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Ein wichtiger Hinweis zu Datentypen**

Beim Entwerfen eigener Modellklassen für die Zuordnung zum Azure Search-Index wird empfohlen, Eigenschaften von Werttypen, z. B. `bool` und `int`, als Eigenschaften zu deklarieren, die NULL-Werte zulassen (z. B. `bool?` anstelle von `bool`). Wenn Sie eine Eigenschaft verwenden, die keine NULL-Werte zulässt, müssen Sie **garantieren**, dass keine Dokumente im Index einen NULL-Wert für das entsprechende Feld enthalten. Weder das SDK noch der Azure Search-Dienst helfen Ihnen, dies durchzusetzen.

Dieser Aspekt ist nicht nur hypothetischer Art: Stellen Sie sich ein Szenario vor, bei dem Sie ein neues Feld einem vorhandenen Index vom Typ `DataType.Int32` hinzufügen. Nach dem Aktualisieren der Indexdefinition besitzen alle Dokumente einen NULL-Wert für das neue Feld (da in Azure Search alle Typen NULL-Werte zulassen). Wenn Sie für dieses Feld anschließend eine Modellklasse mit einer `int`-Eigenschaft verwenden, die keine NULL-Werte zulässt, erhalten Sie beim Abrufen von Dokumenten folgendes `JsonSerializationException`-Element:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Aus diesem Grund empfehlen wir als bewährte Methode, in Ihren Modellklassen Typen zu verwenden, die NULL-Werte zulassen.

## Weiter
Nach dem Auffüllen des Azure Search-Indexes können Sie mit Abfragen für die Suche nach Dokumenten beginnen. Ausführliche Informationen finden Sie unter [Abfragen des Azure Search-Index](search-query-overview.md).

<!---HONumber=AcomDC_0316_2016-->