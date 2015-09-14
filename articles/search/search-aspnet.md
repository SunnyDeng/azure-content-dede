<properties
	pageTitle="Verbinden von Azure Search mit ASP.NET-Web-Apps | Microsoft Azure"
	description="Binden Sie Azure Search in eine ASP.NET-Web-App ein. Erfahren Sie, wie Sie mit der .NET-Bibliothek oder der REST-API eine Verbindung herstellen, Abfragen senden und Ergebnisse empfangen."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="08/26/2015"
	ms.author="heidist"/>

#Integrieren von Azure Search in ASP.NET-Web-Apps

ASP.NET ist das vorherrschende Webanwendungsframework bei benutzerdefinierten Lösungen für Azure Search. In diesem Artikel erfahren Sie, wie Sie Ihre ASP.NET-Web-App mit Azure Search verbinden und Entwurfsmuster für allgemeine Vorgänge herstellen. Außerdem lernen Sie einige Codierungsmethoden kennen, mit denen die Entwicklung vereinfacht wird.

##Organisieren des Codes

Wenn Sie die Workloads in eigenständige Projekte innerhalb derselben Visual Studio-Projektmappe aufteilen, gewinnen Sie mehr Flexibilität beim Planen, Verwalten und Ausführen der einzelnen Programme. Wir empfehlen drei:

- Indexerstellungscode
- Datenerfassungscode
- Benutzerinteraktionscode

In Azure Search sind Indizierungs- und Dokumentvorgänge – z. B. das Hinzufügen oder Aktualisieren von Dokumenten oder das Ausführen von Abfragen – vollständig unabhängig voneinander. Dies bedeutet, dass Sie den Code zur Indexverwaltung vom ASP.NET-Code zur Benutzerinteraktion, mit dem Sie Suchanforderungen formulieren und die Ergebnisse rendern, entkoppeln können.

In den meisten unserer Codebeispiele wird der Index (in den verschiedenen Beispielen bezeichnet als DataIndexer, CatalogIndexer oder DataCatalog) in einem Projekt erstellt und geladen, während sich der Code für die Suchanforderungen und -antworten in einem ASP.NET-MVC-Anwendungsprojekt befindet. In den Codebeispielen ist es praktisch, die Indexerstellung und das Hochladen von Dokumenten in einem Projekt zusammenzufassen – im Produktionscode würden diese Vorgänge jedoch vermutlich isoliert werden. Nachdem ein Index erstellt wurde, wird er nur selten geändert (und bei einer Änderung muss er neu erstellt werden), während Dokumente regelmäßig aktualisiert werden können.

Das Trennen der Workloads bietet noch andere Vorteile, z. B. durch unterschiedliche Berechtigungsstufen für Azure Search (vollständige Administratorrechte im Gegensatz zu reinen Abfrageberechtigungen), die Verwendung unterschiedlicher Programmiersprachen, spezifischere Abhängigkeiten pro Programm sowie die Möglichkeit, Programme einzeln zu überprüfen oder mehrere Front-End-Anwendung zu erstellen, die alle den erstellten Index verwenden und von einer zentralen Indizierungsanwendung verwaltet werden.

##Beispiele und Demos mit ASP.NET und Azure Search

Es gibt bereits verschiedene Codebeispiele, die zeigen, wie Search in ASP.NET integriert wird. Unter den folgenden Links gelangen Sie direkt zum Code oder zu Demonstrations-Apps:

- [Demo-Website zu Jobs in New York City (NYC)](http://aka.ms/azjobsdemo)
- [Testen von App Service mit Azure Search](search-tryappservice.md)
- [Vollständige Liste der Videos, Lernprogramme, Demos und Codebeispiele](earch-video-demo-tutorial-list.md)

##Herstellen einer Verbindung mit dem Dienst

Zum Herstellen einer Verbindung mit dem Dienst und zum Übermitteln von Anforderungen benötigt Ihre Webanwendung lediglich drei Dinge:

- Eine URL zum dem von Ihnen bereitgestellten Azure Search-Dienst im Format "https://<service-name>.search.windows.net"
- Einen API-Schlüssel (GUID) zur Authentifizierung der Verbindung mit Azure Search
- Einen HTTPClient oder SearchServiceClient zum Formulieren der Verbindungsanfrage

####URLs und API-Schlüssel

Sie finden die URL und den API-Schlüssel im [Portal](search-create-service-portal.md) oder durch einen programmgesteuerten Aufruf mit der [Management-REST-API](https://msdn.microsoft.com/library/dn832684.aspx).

Normalerweise werden die URL und der Schlüssel in der Datei "web.config" des Programms für die Benutzerinteraktion gespeichert:

      <appSettings>
    	<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
    	<add key="SearchServiceApiKey" value="[API KEY]" />
    	. . .
      </appSettings>

Der Search-Dienstname kann der Kurzname sein, den Sie während der Bereitstellung angegeben haben, sofern Sie die Domäne ("search.windows.net") an die Verbindung anfügen. Sie können aber auch den vollqualifizierten Namen ("<service-name>.search.windows.net") in der Datei "web.config" ohne das HTTPS-Präfix angeben.

Der API-Schlüssel ist ein Authentifizierungstoken, das während der Dienstbereitstellung (nur Administratorschlüssel) oder manuell generiert wird, wenn Sie die Abfrageschlüssel im Portal erstellen. Der Schlüsseltyp legt fest, welche Suchvorgänge Ihrer Anwendung zur Verfügung stehen:

- Administratorschlüssel (Lese-/Schreibberechtigungen, 2 pro Dienst)
- Abfrageschlüssel (schreibgeschützt, bis zu 50 pro Dienst)

Alle API-Schlüssel sind GUIDs. Visuell gibt es keinen Unterschied zwischen Administratoren- und Abfrageschlüsseln. Sie müssen den Schlüsseltyp im Portal oder mithilfe der Management-REST-API bestimmen.

> [AZURE.TIP]Ein Abfrageschlüssel ermöglicht lediglich schreibgeschützte Vorgänge auf dem Client. Informationen zu den bei einem schreibgeschützten Dienst verfügbaren Azure Search-Vorgängen finden Sie unter [Testen von App Service mit Azure Search](search-tryappservice.md). Beachten Sie, dass beim Testen von App Service der Web-App-Code vollständig geändert werden kann – Sie können beliebigen C#-Code im ASP.NET-Projekt und damit das Webseitenlayout, die Struktur der Suchabfrage oder die Suchergebnisse ändern – lediglich die Vorgänge für den Azure Search-Index und das Laden der Dokumente sind schreibgeschützt, da ein Abfrage-API-Schlüssel auf die Dienstverbindung angewendet wird.

####Clientverbindung

Die folgenden beiden Codeausschnitte richten unter Verwendung von URL und API-Schlüssel eine Verbindung mit dem Search-Dienst ein. Denken Sie daran, dass der Dienstname und die API-Schlüssel in der Datei "Web.config" angegeben werden. Für REST-Aufrufe müssen im Anforderungsheader Administratorschlüssel übergeben werden, während die Abfrageschlüssel im Header oder direkt in der URL übergeben werden können.

**[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx) mit REST-API-Aufrufen**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

**[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx) mit .NET**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##Entwurfsmuster

Eine Web-App mit Integration in Azure Search muss Abfragen formulieren und die Ergebnisse rendern. Dieser Abschnitt enthält Hinweise zum Strukturieren von Code für Aufgaben, die in einem Programm mit Benutzerinteraktionscode ausgeführt werden. Schemadefinition, Indexgenerierung und Datenerfassung werden absichtlich ausgeschlossen. Anleitungen zum Codieren dieser Vorgänge finden Sie unter den exemplarischen Vorgehensweisen und Beispielen in [Videos, Beispiele und Lernprogramme in Azure Search](search-video-demo-tutorial-list.md).

###Formulieren von Abfragen

Eine Volltextsuche über den Index wird in den als **isSearchable** markierten Feldern im Schema ausgeführt, das der Index definiert. Wird ein Suchbegriff (unten durch die Zeichenfolge "q" dargestellt) eingegeben, sucht das Suchmodul nach einer Übereinstimmung in allen durchsuchbaren Feldern und gibt die Ergebnisse aus den als **isRetrievable** markierten Feldern zurück.

> [AZURE.NOTE]Obwohl i. d. R. die meisten Felder durchsuchbar sind, kann ein Index Felder enthalten, die nur in Filterausdrücken verwendet werden. Solche Felder können Sie als nicht durchsuchbar markieren, um sie von der Volltextsuche auszuschließen, und als nicht abrufbar, um sie aus den Suchergebnissen auszuschließen.

Eine Suchabfrage umschließt den vom Benutzer angegebenen Eingabeausdruck in einer Suchabfrage, die den Zielindex angibt, sowie Parameter für das Filtern oder Verfeinern der Anforderung. In die Suchzeichenfolge eingebettete Operatoren wie +, - oder | werden automatisch verarbeitet, sodass es keine Codierungsanforderungen für die Analyse eines Suchbegriffs gibt. Die Analyse wird vom Suchmodul als interner Vorgang durchgeführt. Sie können davon ausgehen, dass die von Ihnen übergebene Zeichenfolge vom Modul analysiert und ausgewertet wird.

Es gibt zwei Arten von Suchabfragen: **Suche** oder **Vorschlag**. Sie definieren für beide Abfragearten verschiedene Methoden. **Suche** ist eine Volltextsuche über Felder im Index. **Vorschlag** ist eine AutoVervollständigen-Abfrage in Azure Search, die eine Liste der möglichen Suchbegriffe basierend auf den ersten drei Zeichen der Benutzereingabe erstellt. In den meisten Fällen beschränken Sie **Vorschläge** auf die Felder, die relativ eindeutige oder unterschiedliche Werte (z. B. Produkt- oder Veröffentlichungsbezeichnungen) enthalten, im Gegensatz zu Feldern, die sehr ähnliche Daten enthalten.

Der folgende Codeausschnitt erfasst eine Suchbegriffeingabe in einem Programm, das die REST-API verwendet. Der eingegebene Begriff wird durch die Zeichenfolge "q" dargestellt. Die übrigen Parameter werden verwendet, um Filterwerte aus einer Facettennavigationsstruktur auf der gleichen Suchseite zu übergeben. Sowohl der eingegebene Begriff als auch die Filterparameter werden in die Search-Methode verwendet.

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
Die **Search**-Methode, die diese Abfrage akzeptiert, wird wie folgt definiert. Beachten Sie, dass die Parameter in der Abfragezeichenfolge definiert werden, sowie die Facettennavigationsstruktur (unterstützt durch Filter, die die Hauptarbeit bei der Eingrenzung der Suchergebnisse erledigen) und die Sortierreihenfolge.

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

Eine .NET-Methode, die eine Suchzeichenfolge erstellt, könnte in einer MVC-Ansicht oder in einem Controller platziert werden. Diese Funktion übergibt die Zeichenfolge an den Home-Controller. Sie definiert auch eine Datenstruktur für die Ergebnisse.

    function Search() {

        var q = $("#q").val();
        
        $.post('/home/search',
        {
            q: q
        },
        function (data) {
            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
            searchResultsHTML += "<td>DATE EDITED</td></tr>";
            for (var i = 0; i < data.length; i++) {
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
            }

            $("#searchResults").html(searchResultsHTML);

        });

Eine .NET-Methode zum Aufrufen von **Search** könnte wie folgt aussehen (das Beispiel ist in einem C#-Hauptprogramm enthalten, das die Verbindung und den Suchvorgang bereitstellt):

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###Verarbeiten der Suchergebnisse

Suchergebnisse werden als Rowset zurückgegeben, das aus Feldern besteht, die im Indexschema als "isRetrievable" markiert sind. Eine einfachere Methode zum Rendern eines Resultsets bietet die Verwendung des ViewBag-Systemobjekts in MVC. Das folgende Codeausschnitt stammt aus der Datei "Index.cshtml" im [Azure Search Adventure Works-Demo auf CodePlex](https://azuresearchadventureworksdemo.codeplex.com/) (in englischer Sprache).

	@model dynamic
	
	@{
	    ViewBag.Title = "Search";
	}
	
	<h2>Product search</h2>
	
	@if (@ViewBag.errorMessage != null) {
	    @ViewBag.errorMessage
	} else {
	    <div class="container">
	        <form action="/Home/Search" method="get">
	            <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
	            <input type="hidden" name="color" id="color" value="@ViewBag.color" />
	            <input type="hidden" name="category" id="category" value="@ViewBag.category" />
	            <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
	            <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
	            <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
	        </form>
	    </div>

###Facettennavigation

In der gleichen Datei "Index.cshmtl" finden Sie den HTML-Code zum Erstellen einer Facettennavigationsstruktur, die die Klassifikationen für das selbstgesteuerte Filtern und das progressive Eingrenzen der Suchergebnisse nach Farbe, Preis oder Kategorie bietet.

	    if (@Model != null)
	    {
	        <div class="container">
	            <div class="row">
	                <div class="col-md-4">
	                    Colors:
	                    <ul>
	                        @foreach (var colorFacet in Model["@search.facets"].color)
	                        {
	                            <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
	document.forms[0].submit(); 
	return false;">@colorFacet.value</a> (@colorFacet.count)</li>
	                        }
	                    </ul>
	                    Categories:
	                    <ul>
	                        @foreach (var categoryFacet in Model["@search.facets"].categoryName)
	                        {
	                            <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
	                        }
	                    </ul>
	                    Prices:
	                    <ul>
	                        @foreach (var priceFacet in Model["@search.facets"].listPrice)
	                        {
	                            if (priceFacet.count > 0)
	                            {
	                       <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
	document.forms[0].submit(); return false;
	">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
	                            }
	                        }
	                    </ul>
	                </div>
	                <div class="col-md-8">
	                    <p>
	                        Sort -
	                        <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
	                        <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
	                        <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
	                    </p>
	                    <p>Found @Model["@odata.count"] products in the catalog</p>
	
	                    <ul>
	                        @foreach (var product in Model.value)
	                        {
	                            <li>
	                                <h3><b>@product.name</b></h3>
	                                price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
	                            </li>
	                        }
	                    </ul>
	                </div>
	            </div>
	        </div>
	    }
	}


###Treffermarkierung

Das Anwenden eines Formats auf die Instanz des Suchbegriffs in einem Suchergebnis wird als Treffermarkierung bezeichnet. In Azure Search werden Treffermarkierungen über entsprechende Parameter in der Abfrage angegeben, an die Sie eine durch Trennzeichen getrennte Liste von Feldern übergeben, die nach übereinstimmenden Ausdrücken durchsucht werden sollen. Das tatsächlich angewendete Format legen Sie fest. Die folgenden drei Codeausschnitte stammen aus dem [Lernprogramm zum Testen von App Service mit Azure Search](search-tryappservice.md).

Definieren Sie als Erstes die Treffermarkierung als Suchparameter, und geben Sie die Felder an, die nach übereinstimmenden Ausdrücken durchsucht werden sollen. Geben Sie das HTML-Format für die Treffermarkierung an.

	// Set the Search parameters used when executing the search request
	     var sp = new SearchParameters
	{
	// Include a count of results in the query result
	     IncludeTotalResultCount = true,
	// Limit the results to 20 documents
	     Top = 20,
	// Enable hit-highlighting
	     HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
	     HighlightPreTag = "<b>",
	     HighlightPostTag = "</b>",
	};

Durchlaufen Sie anschließend die Suchergebnisse, um die Zeichenfolge zu suchen, die hervorgehoben werden soll. "private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)".

	  {
	     if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
	      {
	      string highlightedResult = string.Join("...", item.Highlights[fieldName]);
	      return new HtmlString(highlightedResult);
	      }
	      return new HtmlString(item.Document[fieldName].ToString());
	   }

Zum Abschluss stellen Sie das Layout für die Suchergebnisse bereit, indem Sie das Resultset angeben, das im vorherigen Codeausschnitt ausgewertet wurde.

	<div class="col-xs-12 col-sm-6 col-md-10">
	  <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
	
	  <ul class="list-unstyled">
	    <!-- Cycle through the search results -->
	   @foreach (var item in response.Results)
	    {
	     <li>
	       <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
	       <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
	       <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
	       <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
	       <br />
	     </li>
	    }
	  </ul>
	</div>


##Allgemeine Codierungsverfahren

Sie sind noch nicht mit MVC, der .NET-Programmierung oder REST-APIs vertraut? Diese Abschnitte bieten einige Programmierpraktiken, um Ihnen den Einstieg zu erleichtern.

###MVC-Vorlage

Die folgende Tabelle fasst zusammen, wie MVC-Vorlagenkomponenten in Anwendungen mit Azure Search verwendet werden. Wenn Sie MVC 4 oder MVC 5 verwenden, wird Code, der Azure Search integriert, i. Allg. diesen Modulen hinzugefügt.

Datei|Beschreibung
----|-----------
Web.config|Geben Sie die Dienst-URL und den API-Schlüssel an. Fügen Sie einen Verweis auf "System.Configuration" im Hauptprogrammmodul hinzu, um die Werte zu lesen.
Program.cs|Richten Sie im Hauptprogramm einen HttpClient oder SearchServiceClient ein, um eine Verbindung mit dem Dienst herzustellen. Fügen Sie die Search-Methode in diesem Programm hinzu.
DataModel|Wird nicht verwendet. Vorausgesetzt, dass die Indexerstellung und das Laden der Daten in verschiedenen Programmen durchgeführt werden, ist kein Datenmodell für Azure Search in der Webanwendung erforderlich.
Ansichten|Eine Ansicht enthält den HTML-Code für die Anwendungswebseite – vom Eingabefeld für die Suche bis zum dynamischen HTML-Code für die Behandlung von Suchergebnissen.
Controllers|Das Erstellen der Abfrage und die Fehlerbehandlung erfolgen i. d. R. in der Datei "HomeContoller.cs". Der Controller sollte zumindest eine Suchmethode enthalten, die Ergebnisse von Azure Search abruft und das Resultset an die Ansicht weiterleitet. 

Wenn Sie Vorschläge für die AutoVervollständigung von Abfragen verwenden, können Sie optional eine Methode einfügen, die die vorgeschlagenen Abfragen zurückgibt. Dies hängt davon ab, ob der Index einen mit dem vom Benutzer eingegebenen Suchbegriff übereinstimmenden Wert enthält.

###Vergleich zwischen .NET-Clientbibliothek und REST-API

Für ASP.NET-Anwendungen gilt die .NET-Clientbibliothek als besser geeignet, da sie die HTTP-Verbindung einrichtet und die JSON-Serialisierung und -Deserialisierung für Sie übernimmt, wodurch sich Ihr Code vereinfacht.

In einigen Fällen kann die Wahl aufgrund der Featureübereinstimmungen der beiden Ansätze aber auch auf die API fallen. Im Allgemeinen sind die [.NET-Clientbibliothek](https://msdn.microsoft.com/library/azure/dn951165.aspx) und [der REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) austauschbar, sofern die benötigten Vorgänge in beiden implementiert sind. Manchmal kommen jedoch neue Features zuerst in der REST-API im Rahmen einer Vorschauversion hinzu und werden der .NET-Bibliothek erst Monate später hinzugefügt. Indexer zum Automatisieren der Datenladevorgänge aus bestimmten Datenquellentypen waren z. B. zuerst in der Vorschau-REST-API verfügbar, bevor sie Monate später auch in der Clientbibliothek verfügbar wurden. Einschränkungen hinsichtlich der Implementierung von Features sind in der Dokumentation aufgeführt.

###Einschließen von "AzureSearchHelper.cs" für die JSON-Serialisierung und -Deserialisierung in die REST-API

Im Gegensatz zur .NET-Bibliothek, die diesen Schritt für Sie übernimmt, müssen die Dienst-REST-APIs JSON-Dokumente beim Austausch von Anforderung und Antwort mit dem Dienst serialisieren und deserialisieren. JSON ist das Nutzlastformat für die Datenübertragung beim Laden oder Aktualisieren von Dokumenten im Index.

Den Code für die JSON-Serialisierung finden Sie in einigen der Beispiele in der Datei **AzureSearchHelper.cs**:

	using System;
	using System.Net.Http;
	using System.Text;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Converters;
	using Newtonsoft.Json.Serialization;
	
	namespace CatalogCommon
	{
	    public class AzureSearchHelper
	    {
	        public const string ApiVersionString = "api-version=2014-07-31-Preview";
	
	        private static readonly JsonSerializerSettings _jsonSettings;
	
	        static AzureSearchHelper()
	        {
	            _jsonSettings = new JsonSerializerSettings
	            {
	                Formatting = Formatting.Indented, // for readability, change to None for compactness
	                ContractResolver = new CamelCasePropertyNamesContractResolver(),
	                DateTimeZoneHandling = DateTimeZoneHandling.Utc
	            };
	
	            _jsonSettings.Converters.Add(new StringEnumConverter());
	        }
	
	        public static string SerializeJson(object value)
	        {
	            return JsonConvert.SerializeObject(value, _jsonSettings);
	        }
	
	        public static T DeserializeJson<T>(string json)
	        {
	            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
	        }
	
	        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
	        {
	            UriBuilder builder = new UriBuilder(uri);
	            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
	            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;
	
	            var request = new HttpRequestMessage(method, builder.Uri);
	
	            if (json != null)
	            {
	                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
	            }
	
	            return client.SendAsync(request).Result;
	        }
	
	        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
	        {
	            if (!response.IsSuccessStatusCode)
	            {
	                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
	                throw new Exception("Search request failed: " + error);
	            }
	        }
	    }
	}


##Nächste Schritte

Zur Vertiefung Ihres Verständnisses der Integration von Azure Search und ASP.NET besuchen Sie die folgenden Links:

- [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md) 
- [Azure Search-Fallstudie für Entwickler](search-dev-case-study-whattopedia.md)
- [Typischer Workflow für die Azure Search-Entwicklung](search-workflow.md) 

<!---HONumber=September15_HO1-->