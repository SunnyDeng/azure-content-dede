<properties
   pageTitle="Upgrade auf Version „1.0.1-preview“ des Azure Search .NET SDK | Microsoft Azure | Gehosteter Cloudsuchdienst"
   description="Upgrade auf Version „1.0.1-preview“ des Azure Search .NET SDK"
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
   ms.date="01/22/2016"
   ms.author="brjohnst"/>

# Upgrade auf Version „1.0.1-preview“ des Azure Search .NET SDK

Wenn Sie die Version „0.13.0-preview“ oder eine frühere Version des [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) verwenden, unterstützt dieser Artikel Sie beim Upgrade Ihrer Anwendung auf die neueste Version, „1.0.1-preview“.

Eine allgemeinere exemplarische Vorgehensweise für das SDK sowie Beispiele finden Sie unter [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md).

Version „1.0.1-preview“ des Azure Search .NET SDK enthält mehrere wichtige Änderungen im Vergleich zur vorherigen Version (0.13.0-preview). Dabei handelt es sich hauptsächlich um kleinere Änderungen, sodass zum Ändern Ihres Codes nur ein geringer Aufwand erforderlich sein sollte. Anweisungen zum Ändern Ihres Codes, um die neue SDK-Version zu verwenden, finden Sie unter [Schritte zum Upgrade](#UpgradeSteps).

<a name="WhatsNew"></a>
## Neuerungen in „1.0.1-preview“

Für Version „1.0.1-preview“ gilt dieselbe REST-API-Version wie für ältere Versionen des Azure Search .NET SDK (2015-02-28), daher gibt es keine neuen Dienstfeatures in dieser Version. Es gibt jedoch einige neue clientseitige Serialisierungsfeatures.

Das SDK verwendet JSON.NET zum Serialisieren und Deserialisieren von Dokumenten. Die neue Version des SDK unterstützt die benutzerdefinierte Serialisierung über `JsonConverter` und `IContractResolver`. (Weitere Einzelheiten finden Sie in der [JSON.NET-Dokumentation](http://www.newtonsoft.com/json/help/html/Introduction.htm).) Dies kann nützlich sein, wenn Sie eine vorhandene Modellklasse aus der Anwendung für die Verwendung mit Azure Search und andere fortgeschrittenere Szenarien anpassen möchten. Bei einer benutzerdefinierten Serialisierung bieten sich zum Beispiel folgende Möglichkeiten:
 
 - Ein- oder Ausschließen bestimmter Eigenschaften der Modellklasse bei der Speicherung als Dokumentfelder
 - Zuordnen zwischen Eigenschaftennamen im Code und Feldnamen im Index
 - Erstellen benutzerdefinierter Attribute, die sowohl für die Zuordnung von Eigenschaften zu Dokumentfeldern als auch für die Erstellung der entsprechenden Indexdefinition verwendet werden können

Beispiele für die Implementierung der benutzerdefinierten Serialisierung in den Unittests für das Azure Search .NET SDK finden Sie auf GitHub. Ein guter Ausgangspunkt ist [dieser Ordner](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Er enthält Klassen, die in den Tests für die benutzerdefinierte Serialisierung verwendet werden.

Zusätzlich zu benutzerdefinierten Serialisierung unterstützt das neue SDK auch die Serialisierung von `SearchContinuationToken`-Objekten. Dies kann nützlich sein, wenn Sie Azure Search aus einer Webanwendung aufrufen und beim seitenweisen Durchblättern von Suchergebnissen Fortsetzungstoken mit einem Browser oder einem mobilen Client austauschen müssen.

<a name="UpgradeSteps"></a>
## Schritte zum Upgrade

Aktualisieren Sie zunächst die NuGet-Referenz für `Microsoft.Azure.Search`, indem Sie entweder die NuGet-Paket-Manager-Konsole verwenden oder mit der rechten Maustaste auf die Projektverweise klicken und in Visual Studio „NuGet-Pakete verwalten...“ auswählen.

> [AZURE.NOTE] Stellen Sie sicher, dass Ihnen Vorabversionspakete angezeigt werden, indem Sie „Vorabversion einbeziehen“ auswählen, falls Sie Visual Studio verwenden, oder indem Sie den Schalter `-IncludePrerelease` festlegen, falls Sie die Paket-Manager-Konsole verwenden.

Nachdem NuGet die neuen Pakete und deren Abhängigkeiten heruntergeladen hat, erstellen Sie Ihr Projekt neu. Ihnen werden in etwa folgende Buildfehler angezeigt:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Der nächste Schritt besteht darin, die Buildfehler einzeln zu beheben. Für die meisten müssen einige Klassen- und Methodennamen geändert werden, die im SDK umbenannt wurden. [Liste mit wichtigen Änderungen in „1.0.1-preview“](#ListOfChanges) enthält eine Liste mit diesen Namensänderungen.

Wenn Sie benutzerdefinierte Klassen zum Modellieren von Dokumenten verwenden und diese Klassen Eigenschaften mit primitiven Datentypen umfassen, die keine NULL-Werte zulassen (z. B. `int` oder `bool` in C#), gibt es in der Version „1.0.1-preview“ des SDK eine Programmfehlerbehebung, die Sie kennen sollten. Weitere Details finden Sie unter [Programmfehlerbehebungen in „1.0.1-preview“](#BugFixes) .

Sobald Sie alle Buildfehler behoben haben, können Sie Änderungen an Ihrer Anwendung vornehmen, um nach Bedarf die neue Funktionalität zu nutzen. Das benutzerdefinierte Serialisierungsfeature im neuen SDK wird im Detail unter [Neuerungen in „1.0.1-preview“](#WhatsNew) beschrieben.

<a name="ListOfChanges"></a>
## Liste mit wichtigen Änderungen in „1.0.1-preview“

Die folgende Liste ist nach der Wahrscheinlichkeit sortiert, dass die Änderung sich auf den Anwendungscode auswirkt.

### Änderungen an IndexBatch und IndexAction

`IndexBatch.Create` wurde in `IndexBatch.New` umbenannt und umfasst kein `params`-Argument mehr. Sie können `IndexBatch.New` für Batches verwenden, in denen verschiedene Typen von Aktionen (Zusammenführen, Löschen usw.) kombiniert werden. Darüber hinaus stehen neue statische Methoden zum Erstellen von Batches zur Verfügung, in denen alle Aktionen gleich sind: `Delete`, `Merge`, `MergeOrUpload` und `Upload`.

`IndexAction` verfügt nicht mehr über öffentliche Konstruktoren, und die zugehörigen Eigenschaften sind unveränderlich. Sie sollten die neuen statischen Methoden zum Erstellen von Aktionen für verschiedene Zwecke verwenden: `Delete`, `Merge`, `MergeOrUpload` und `Upload`. `IndexAction.Create` wurde entfernt. Wenn Sie die Überladung verwendet haben, die nur ein Dokument akzeptiert, müssen Sie stattdessen `Upload` verwenden.

#### Beispiel

Angenommen, Sie verwenden folgenden Code:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Zum Beheben von Buildfehlern können Sie diesen Code folgendermaßen ändern:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Wenn Sie möchten, können Sie ihn weiter vereinfachen:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### Änderungen an IndexBatchException

Die `IndexBatchException.IndexResponse`-Eigenschaft wurde umbenannt in `IndexingResults`, und der Typ ist jetzt `IList<IndexingResult>`.

#### Beispiel

Angenommen, Sie verwenden folgenden Code:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Zum Beheben von Buildfehlern können Sie diesen Code folgendermaßen ändern:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
### Änderungen an der Vorgangsmethode

Jeder Vorgang im Azure Search .NET SDK wird als eine Reihe von Methodenüberladungen für synchrone und asynchrone Aufrufer verfügbar gemacht. Die Signaturen und die Faktorisierung dieser Methodenüberladungen wurden in der Version „1.0.1-preview“ geändert.

Durch den Vorgang „Indexstatistiken abrufen“ in älteren Versionen des SDK wurden beispielsweise folgende Signaturen verfügbar gemacht:

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Die Methodensignaturen für denselben Vorgang sehen in Version „1.0.1-preview“ folgendermaßen aus:

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations, 
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Ab Version „1.0.1-preview“ werden Vorgangsmethoden in Azure Search .NET SDK anders organisiert:
 
 - Optionale Parameter werden jetzt als Standardparameter statt als zusätzliche Methodenüberladungen modelliert. Dadurch verringert sich die Anzahl an Methodenüberladungen zum Teil erheblich.
 - Bei den Erweiterungsmethoden werden jetzt viele irrelevante HTTP-Details für den Aufrufer ausgeblendet. Ältere Versionen des SDK gaben zum Beispiel ein Antwortobjekt mit einem HTTP-Statuscode zurück, den Sie häufig nicht überprüfen mussten, weil Vorgangsmethoden für jeden Statuscode, der einen Fehler angibt, `CloudException` auslösen. Die neuen Erweiterungsmethoden geben nur Modellobjekte zurück, sodass Sie sie nicht extra in Ihrem Code entpacken müssen.
 - Umgekehrt machen die Kernschnittstellen jetzt Methoden verfügbar, die Ihnen mehr Steuerungsmöglichkeiten auf der HTTP-Ebene ermöglichen, wenn Sie sie benötigen. Sie können nun benutzerdefinierte HTTP-Header in Anforderungen übergeben, und der neue `AzureOperationResponse<T>`-Rückgabetyp bietet Ihnen direkten Zugriff auf die Elemente `HttpRequestMessage` und `HttpResponseMessage` für den Vorgang. `AzureOperationResponse` wird im `Microsoft.Rest.Azure`-Namespace definiert und ersetzt `Hyak.Common.OperationResponse`.

### Änderungen an Modellklassen

Aufgrund der Signaturänderungen, die unter [Änderungen an der Vorgangsmethode](#OperationMethodChanges) beschrieben werden, wurden viele Klassen im `Microsoft.Azure.Search.Models`-Namespace umbenannt oder entfernt. Beispiel:

 - `IndexDefinitionResponse` wurde durch `AzureOperationResponse<Index>` ersetzt.
 - `DocumentSearchResponse` wurde in `DocumentSearchResult` umbenannt.
 - `IndexResult` wurde in `IndexingResult` umbenannt.
 - `Documents.Count()` gibt jetzt `long` mit der Dokumentanzahl anstelle von `DocumentCountResponse` zurück.
 - `IndexGetStatisticsResponse` wurde in `IndexGetStatisticsResult` umbenannt.
 - `IndexListResponse` wurde in `IndexListResult` umbenannt.

Zusammenfassend wurden von `OperationResponse` abgeleitete Klassen entfernt, die nur zum Umschließen eines Modellobjekts dienten. Bei den übrigen Klassen wurde das Suffix von `Response` in `Result` geändert.

#### Beispiel

Angenommen, Sie verwenden folgenden Code:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Zum Beheben von Buildfehlern können Sie diesen Code folgendermaßen ändern:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### Antwortklassen und IEnumerable

Eine weitere Änderung, die sich auf Ihren Code auswirken kann, besteht darin, dass `IEnumerable<T>` von Antwortklassen mit darin enthaltenen Auflistungen nicht länger implementiert wird. Stattdessen können Sie direkt auf die Auflistungseigenschaft zugreifen. Angenommen, Sie verwenden folgenden Code:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Zum Beheben von Buildfehlern können Sie diesen Code folgendermaßen ändern:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### Wichtiger Hinweis für Webanwendungen

Wenn Sie eine Webanwendung verwenden, die `DocumentSearchResponse` direkt serialisiert, um Suchergebnisse an den Browser zu senden, müssen Sie zum ordnungsgemäßen Serialisieren der Ergebnisse den Code ändern. Angenommen, Sie verwenden folgenden Code:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Sie können ihn durch Abrufen der `.Results`-Eigenschaft der Suchantwort ändern, um das Rendering der Suchergebnisse zu korrigieren:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Nach solchen Fällen müssen Sie selbst in Ihrem Code suchen. **Sie erhalten keine Warnung vom Compiler**, weil `JsonResult.Data` vom Typ `object` ist.

### Änderungen an CloudException

Die `CloudException`-Klasse wurde vom `Hyak.Common`-Namespace in den `Microsoft.Rest.Azure`-Namespace verschoben. Darüber hinaus wurde die `Error`-Eigenschaft in `Body` umbenannt.

### Änderungen an SearchServiceClient und SearchIndexClient

Der Typ der `Credentials`-Eigenschaft wurde von `SearchCredentials` in die Basisklasse `ServiceClientCredentials` geändert. Wenn Sie auf die `SearchCredentials` eines `SearchIndexClient` oder `SearchServiceClient` zugreifen müssen, verwenden Sie die neue `SearchCredentials`-Eigenschaft.

In älteren Versionen des SDK besaßen `SearchServiceClient` und `SearchIndexClient` Konstruktoren, die einen `HttpClient`-Parameter akzeptierten. Diese wurden durch Konstruktoren ersetzt, die einen `HttpClientHandler` und ein Array von `DelegatingHandler`-Objekten akzeptieren. Dies erleichtert die Installation benutzerdefinierter Ereignishandler für die Vorverarbeitung von HTTP-Anforderungen, falls erforderlich.

Schließlich wurden die Konstruktoren, die `Uri` und `SearchCredentials` akzeptierten, geändert. Angenommen, Sie verwenden folgenden Code:

    var client = 
        new SearchServiceClient(
            new SearchCredentials("abc123"), 
            new Uri("http://myservice.search.windows.net"));

Zum Beheben von Buildfehlern können Sie diesen Code folgendermaßen ändern:

    var client = 
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123")); 

Beachten Sie auch, dass der Typ des Parameters mit den Anmeldeinformationen in `ServiceClientCredentials` geändert wurde. Dies wird sich wahrscheinlich nicht auf Ihren Code auswirken, weil `SearchCredentials` von `ServiceClientCredentials` abgeleitet wird.

### Übergeben eine Anforderungs-ID

In früheren Versionen des SDK konnten Sie eine Anforderungs-ID für `SearchServiceClient` oder `SearchIndexClient` festlegen, die dann in jede Anforderung an die REST-API integriert wurde. Dies ist für die Behandlung von Problemen mit Ihrem Suchdienst nützlich, wenn Sie sich an den Support wenden müssen. Es ist jedoch noch nützlicher, eine eindeutige Anforderungs-ID für jeden Vorgang festzulegen, statt dieselbe ID für alle Vorgänge zu verwenden. Aus diesem Grund wurden die `SetClientRequestId`-Methoden `SearchServiceClient` und `SearchIndexClient` entfernt. Über den optionalen `SearchRequestOptions`-Parameter können Sie stattdessen für jede Vorgangsmethode eine Anforderungs-ID übergeben.

> [AZURE.NOTE] In einer zukünftigen Version des SDK fügen wir einen neuen Mechanismus für die globale Festlegung einer Anforderungs-ID für die Clientobjekte hinzu. Dieser entspricht dem Ansatz, der von anderen Azure SDKs verwendet wird.

#### Beispiel

Angenommen, Sie verwenden folgenden Code:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Zum Beheben von Buildfehlern können Sie diesen Code folgendermaßen ändern:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### Änderungen an Schnittstellennamen

Die Schnittstellennamen der Vorgangsgruppen wurden alle geändert und stimmen jetzt mit ihren entsprechenden Eigenschaftsnamen überein:
 
 - Der Typ von `ISearchServiceClient.Indexes` wurde von `IIndexOperations` in `IIndexesOperations` umbenannt.
 - Der Typ von `ISearchServiceClient.Indexers` wurde von `IIndexerOperations` in `IIndexersOperations` umbenannt.
 - Der Typ von `ISearchServiceClient.DataSources` wurde von `IDataSourceOperations` in `IDataSourcesOperations` umbenannt.
 - Der Typ von `ISearchIndexClient.Documents` wurde von `IDocumentOperations` in `IDocumentsOperations` umbenannt.

Diese Änderung wird sich wahrscheinlich nicht auf Ihren Code auswirken, es sei denn, Sie haben Modelle dieser Schnittstellen zu Testzwecken erstellt.

<a name="BugFixes"></a>
## Programmfehlerbehebungen in „1.0.1-preview“

In älteren Versionen des Azure Search .NET SDK lag ein Fehler in Bezug auf die Serialisierung benutzerdefinierter Modellklassen vor. Der Fehler konnte auftreten, wenn Sie eine benutzerdefinierte Modellklasse mit einer Eigenschaft erstellt haben, deren Typ keine NULL-Werte zuließ.

### Schritte zum Reproduzieren

Erstellen Sie eine benutzerdefinierte Modellklasse mit einer Eigenschaft, deren Typ keine NULL-Werte zulässt. Fügen Sie z. B. eine öffentliche `UnitCount`-Eigenschaft vom Typ `int` anstelle von `int?` hinzu.

Wenn Sie ein Dokument mit dem Standardwert dieses Typs indizieren (z. B. 0 für `int`), lautet das Feld in Azure Search NULL. Wenn Sie anschließend nach diesem Dokument suchen, gibt der `Search`-Aufruf `JsonSerializationException` aus mit der Begründung, dass `null` nicht in `int` konvertiert werden kann.

Filter funktionieren möglicherweise auch nicht wie erwartet, da anstelle des gewünschten Werts NULL in den Index geschrieben wurde.

### Details zur Korrektur

Wir haben dieses Problem in Version „1.0.1-preview“ des SDK behoben. Wenn Sie jetzt eine Modellklasse wie die folgende verwenden:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

und `IntValue` auf 0 setzen, wird dieser Wert jetzt bei der Übertragung ordnungsgemäß als 0 serialisiert und als 0 im Index gespeichert. Auch der Roundtrip funktioniert erwartungsgemäß.

Bei diesem Ansatz gibt es ein mögliches Problem, das Sie beachten sollten: Wenn Sie einen Modelltyp mit einer Eigenschaft verwenden, die keine NULL-Werte zulässt, müssen Sie **garantieren**, dass keine Dokumente im Index einen NULL-Wert für das entsprechende Feld enthalten. Weder das SDK noch die Azure Search-REST-API hilft Ihnen, dies durchzusetzen.

Dieser Aspekt ist nicht nur hypothetisch relevant: Stellen Sie sich ein Szenario vor, in dem Sie ein neues Feld einem vorhandenen Index vom Typ `Edm.Int32` hinzufügen. Nach dem Aktualisieren der Indexdefinition besitzen alle Dokumente einen NULL-Wert für das neue Feld (da in Azure Search alle Typen NULL-Werte zulassen). Wenn Sie für dieses Feld anschließend eine Modellklasse mit einer `int`-Eigenschaft verwenden, die keine NULL-Werte zulässt, erhalten Sie beim Abrufen von Dokumenten folgende `JsonSerializationException`:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Aus diesem Grund empfehlen wir als bewährte Methode in den Modellklassen die Verwendung von Typen, die NULL-Werte zulassen.

Weitere Informationen zu diesem Fehler und der entsprechenden Korrektur finden Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

## Zusammenfassung
Weitere Informationen zur Verwendung des Azure Search .NET SDK finden Sie in unseren kürzlich aktualisierten Artikeln [Gewusst wie](search-howto-dotnet-sdk.md) und [Erste Schritte](search-get-started-dotnet.md) .

Wir freuen uns auf Ihr Feedback zum SDK! Wenn Probleme auftreten, können Sie sich gerne über das [Azure Search-MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/de-DE/home?forum=azuresearch) an uns wenden. Wenn Sie einen Fehler finden, können Sie das Problem im [Azure .NET SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/issues) melden. Stellen Sie sicher, dass Sie dem Titel des Problems „Search SDK: “ voranstellen.

Vielen Dank für die Nutzung von Azure Search!

<!---HONumber=AcomDC_0128_2016-->