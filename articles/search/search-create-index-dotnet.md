<properties
    pageTitle="Erstellen eines Azure Search-Index mit dem .NET SDK | Microsoft Azure | Gehosteter Cloudsuchdienst"
    description="Erstellen Sie einen Index im Code mithilfe des Azure Search .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="brjohnst"/>

# Erstellen eines Azure Search-Index mit dem .NET SDK
> [AZURE.SELECTOR]
- [Übersicht](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


In diesem Artikel lernen Sie, wie Sie einen Azure Search-[Index](https://msdn.microsoft.com/library/azure/dn798941.aspx) mithilfe des [Azure Search-.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) erstellen.

Bevor Sie dieser Anleitung folgen und einen Index erstellen, müssen Sie einen [Azure Search-Dienst erstellen](search-create-service-portal.md).

Beachten Sie, dass der gesamte Beispielcode in diesem Artikel in C# geschrieben wurde. Den vollständigen Quellcode finden Sie [bei GitHub](http://aka.ms/search-dotnet-howto).

## I. Identifizieren des Admin-API-Schlüssels Ihres Azure Search-Diensts
Nachdem Sie einen Azure Search-Dienst bereitgestellt haben, sind Sie fast bereit, Anforderungen für Ihren Dienstendpunkt mithilfe des .NET SDK auszugeben. Zunächst müssen Sie aber einen der Admin-API-Schlüssel abrufen, die für den bereitgestellten Suchdienst generiert wurden. Das .NET SDK sendet diesen API-Schlüssel für jede Anforderung an Ihren Dienst. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, um die API-Schlüssel für Ihren Dienst zu suchen.
2. Wechseln Sie zum Blatt Ihres Azure Search-Diensts.
3. Klicken Sie auf das Schlüsselsymbol.

Der Dienst enthält *Admin-Schlüssel* und *Abfrageschlüssel*.

  - Die primären und sekundären *Admin-Schlüssel* gewähren Ihnen Vollzugriff auf alle Vorgänge. Dazu zählen die Dienstverwaltung und das Erstellen und Löschen von Indizes, Indexern und Datenquellen. Ihnen stehen zwei Schlüssel zur Verfügung, damit Sie den sekundären Schlüssel weiterhin nutzen können, wenn Sie den primären Schlüssel neu generieren möchten, und umgekehrt.
  - Die *Abfrageschlüssel* gewähren Ihnen Lesezugriff auf Indizes und Dokumente. Diese werden in der Regel auf Clientanwendungen verteilt, die Suchanfragen ausgeben.

Verwenden Sie zum Erstellen eines Indexes entweder den primären oder den sekundären Admin-Schlüssel.

<a name="CreateSearchServiceClient"></a>
## II. Erstellen einer Instanz der SearchServiceClient-Klasse
Damit Sie das Azure Search .NET SDK verwenden können, müssen Sie eine Instanz der `SearchServiceClient`-Klasse erstellen. Diese Klasse verfügt über mehrere Konstruktoren. Für den gewünschten Konstruktor werden der Name Ihres Suchdiensts und ein `SearchCredentials`-Objekt als Parameter verwendet. `SearchCredentials` umschließt Ihren API-Schlüssel.

Mit dem folgenden Code wird ein neues `SearchServiceClient`-Element mit den Werten für den Namen des Suchdiensts und den API-Schlüssel erstellt, die in der Konfigurationsdatei der Anwendung (`app.config` oder `web.config`) gespeichert sind:

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` verfügt über eine `Indexes`-Eigenschaft. Diese Eigenschaft stellt alle Methoden bereit, die Sie benötigen, um Azure Search-Indizes zu erstellen, aufzulisten, zu aktualisieren oder zu löschen.

> [AZURE.NOTE] Die `SearchServiceClient`-Klasse verwaltet die Verbindungen mit Ihrem Suchdienst. Um zu vermeiden, dass zu viele Verbindungen geöffnet werden, sollten Sie nur eine Instanz von `SearchServiceClient` in Ihrer Anwendung freigeben, sofern dies möglich ist. Die zugehörigen Methoden sind threadsicher und ermöglichen diese Freigabe.

<a name="DefineIndex"></a>
## III. Definieren des Azure Search-Index mit der `Index`-Klasse
Der Index kann mit einem einzigen Aufruf der `Indexes.Create`-Methode erstellt werden. Für diese Methode wird als Parameter ein `Index`-Objekt verwendet, das Ihren Azure Search-Index definiert. Sie müssen ein `Index`-Objekt erstellen und wie folgt initialisieren:

1. Legen Sie die `Name`-Eigenschaft des `Index`-Objekts auf den Namen des Index fest.
2. Legen Sie die `Fields`-Eigenschaft des `Index`-Objekts auf ein Array von `Field`-Objekten fest. Jedes der `Field`-Objekte definiert das Verhalten eines Felds im Index. Sie können den Namen des Felds zusammen mit dem Datentyp (oder dem Analyzer bei Zeichenfolgenfeldern) für den Konstruktor angeben. Sie können auch andere Eigenschaften festlegen, z. B. `IsSearchable`, `IsFilterable` usw.

Berücksichtigen Sie beim Gestalten des Index die Benutzerfreundlichkeit und die geschäftlichen Anforderungen, da jedem `Field` die [richtigen Eigenschaften](https://msdn.microsoft.com/library/azure/dn798941.aspx) zugewiesen sein müssen. Über diese Eigenschaften wird gesteuert, welche Suchfunktionen (Filtern, Facettierung, Sortieren der Volltextsuche usw.) für welche Felder gelten. Für jede Eigenschaft, die Sie nicht explizit festlegen, wird mit der `Field`-Klasse automatisch die entsprechende Suchfunktion deaktiviert, bis Sie sie ausdrücklich aktivieren.

In unserem Beispiel hat der Index den Namen „hotels“. Die Felder wurden wie folgt definiert:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Wir haben die Eigenschaftswerte für jedes `Field` ausgehend davon ausgewählt, wie sie unserer Meinung nach in einer Anwendung verwendet werden. Beispielsweise ist es wahrscheinlich, dass für Personen, die nach Hotels suchen, Übereinstimmungen mit Schlüsselwörtern im Feld `description` relevant sind, daher aktivieren wir für dieses Feld die Volltextsuche, indem `IsSearchable` auf `true` festgelegt wird.

In Ihrem Index des Typs `DataType.String` muss genau ein Feld als _key_-Feld bestimmt sein, indem `IsKey` auf `true` festgelegt wird (siehe `hotelId` im Beispiel oben).

Diese Indexdefinition verwendet für das Feld `description_fr` eine benutzerdefinierte Sprachanalyse, da sie französischen Text speichern soll. Im [Thema zur Sprachunterstützung auf MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) sowie im entsprechenden [Blogbeitrag](https://azure.microsoft.com/blog/language-support-in-azure-search/) finden Sie weitere Informationen zu Sprachanalysen.

> [AZURE.NOTE]  Beachten Sie, dass durch Übergeben von `AnalyzerName.FrLucene` an den Konstruktor das `Field` automatisch den Typ `DataType.String` aufweist und `IsSearchable` auf `true` festgelegt ist.

## IV. Erstellen des Index
Nachdem Sie nun über ein initialisiertes `Index`-Objekt verfügen, können Sie den Index einfach durch Aufrufen von `Indexes.Create` für das `SearchServiceClient`-Objekt aufrufen:

```csharp
serviceClient.Indexes.Create(definition);
```

Bei einer erfolgreich durchgeführten Anforderung wird die Methode normal zurückgegeben. Wenn ein Problem mit der Anforderung auftritt, z. B. ein ungültiger Parameter vorhanden ist, gibt die Methode `CloudException` zurück.

Wenn Sie einen Index nicht mehr benötigen und ihn löschen möchten, rufen Sie einfach die `Indexes.Delete`-Methode für `SearchServiceClient` auf. Der Index „hotels“ wird beispielsweise wie folgt gelöscht:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] Der Beispielcode in diesem Artikel verwendet der Einfachheit halber die synchronen Methoden des Azure Search-.NET-SDK. Für Ihre eigenen Anwendungen empfehlen wir aus Gründen der Skalierbarkeit und Reaktionsfähigkeit die asynchronen Methoden. In den Beispielen oben können Sie `Create` und `Delete` beispielsweise auch durch `CreateAsync` und `DeleteAsync` ersetzen.

## Weiter
Nach dem Erstellen eines Azure Search-Indexes können Sie [Ihre Inhalte in den Index hochladen](search-what-is-data-import.md) und mit dem Durchsuchen der Daten beginnen.

<!---HONumber=AcomDC_0316_2016-->