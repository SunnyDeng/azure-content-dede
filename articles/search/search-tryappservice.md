<properties 
   pageTitle="Kostenloses Testen von Azure Search mit Azure App Service | Microsoft Azure"
   description="Testen Sie Azure Search gratis bis zu einer Stunde lang, indem Sie die Azure App Service-Vorlage nutzen."
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="01/13/2016"
   ms.author="heidist"/>

# Kostenloses Testen von Azure Search mit Azure App Service

Sie können jetzt **Azure Search** in einer Sitzung mit unserem [Azure App Service testen](https://tryappservice.azure.com/) – eine Stunde lang, in einem beliebigen Webbrowser, vollkommen kostenlos und ohne Registrierung für ein Abonnement. Diese Website bietet Ihnen mehrere Vorlagen zur Auswahl. Wenn Sie die ASP.NET-Vorlage auswählen, die auch Azure Search beinhaltet, erhalten Sie Zugriff auf eine voll funktionsfähige Website sowie auf alle ausgewählten unterstützenden Dienste.

Bei unserem Angebot [Azure App Service testen](https://tryappservice.azure.com/) wurde der Azure Search-Dienst bereits für Sie erstellt – er kann sofort für Suchabfragen verwendet werden. Sie können keinen eigenen Index und keine eigenen Daten hochladen oder verwenden, aber Sie können Abfragen ausführen und eine Reihe von Codeänderungen vornehmen, um das Benutzererlebnis anzupassen.

Die Suchdaten stammen aus der [United States Geological Survey (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), bestehend aus ungefähr 3 Millionen Zeilen mit Sehenswürdigkeiten, historischen Stätten, Gebäuden und anderen Landmarken in den USA.

## Erste Schritte

Wenn die einstündige Sitzung noch nicht begonnen hat, gehen Sie wie folgt vor, um loszulegen.

1. Wechseln Sie zu [https://tryappservice.azure.com](https://tryappservice.azure.com/) und scrollen Sie nach unten auf **Web App**. 
2. Klicken Sie auf **Weiter**.
3. Wählen Sie die Vorlage **ASP.NET + Azure Search Site** aus.

    ![][1]

4. Klicken Sie auf **Erstellen**.
5. Wählen Sie eine Login-Methode, und geben Sie den Benutzernamen und das Kennwort ein.

    ![][2]

6. Warten Sie, während die Website bereitgestellt wird. Wenn sie bereit ist, sehen Sie eine Website ähnlich der folgenden. Auf jeder Seite erscheint eine laufende Uhr, damit Sie immer wissen, wie viel Zeit Ihnen noch verbleibt.

    ![][3]

7. Wählen Sie **Mit Visual Studio Online bearbeiten**, um die Lösung anzuzeigen und die Website zu durchsuchen.
9. Erweitern Sie in Visual Studio Team Services die Sitzungsoptionen am oberen Rand der Seite, und klicken Sie dann auf **Website durchsuchen**.

    ![][4]

10. Die Seite mit den ersten Schritten für Ihre Azure Search-Website sollte angezeigt werden. Klicken Sie auf die Schaltfläche **Erste Schritte**, um die Website zu öffnen.

    ![][5]

11. Eine ASP.NET-Website öffnet im Browser und stellt ein Suchfeld bereit. Geben Sie einen vertrauten Begriff ein, wie z. B. *Yellowstone*, oder einen bekannten Bergnamen wie *Mount Rainier*. Mit einem vertrauten Ort zu beginnen, erleichtert die Auswertung der Ergebnisse.

    ![][6]


## Anfängliche Vorgehensweise

Da der Suchindex voll funktionsfähig ist, ist es ein guter erster Schritt, einige Abfragen auszuprobieren. Azure Search unterstützt alle Standardsuchoperatoren (+, -, |), Anführungszeichen für Übereinstimmungen bei Zeichenfolgenliteralen, Platzhalter (*) und die Rangfolge von Operatoren. Sie können den Syntaxverweis der Abfrage für die vollständige Liste der Operatoren überprüfen.

- Beginnen Sie mit einer Platzhaltersuche durch Hinzufügen eines Sternchens (`*`). Damit sehen Sie, wie viele Dokumente im Index gefunden werden: 2.262.578.
- Als Nächstes geben Sie "Yellowstone" ein, fügen dann "+center", "+building" und "-ND" hinzu, um die Suchergebnisse progressiv auf Yellowstone-Besucher in Zentren einzugrenzen, mit Ausnahme derjenigen in North Dakota: `Yellowstone +center +building -ND`.  
- Probieren Sie es mit einem Suchbegriff, der die Rangfolge von Operatoren und den Zeichenfolgenabgleich kombiniert: `statue+(park+MT)`. Sie sollten ähnliche Ergebnisse wie im Screenshot unten erhalten. Beachten Sie, dass Facettenkategorien unter der Funktionsklasse erscheinen, und ds selbstgesteuerte Filtern durch die Facettennavigation bieten. Dies ist eine Funktion, die im Allgemeinen in den meisten Suchanwendungen vorhanden ist.

    ![][7]

Möchten Sie fortfahren? Ändern Sie ein paar Codezeilen, um die Auswirkungen auf die Volltext-Suchvorgänge zu sehen.

## Ändern Sie „SearchMode.All“.

Azure Search bietet eine konfigurierbare **SearchMode**-Eigenschaft, die Sie verwenden können, um das Suchverhalten des Operators zu steuern. Gültige Werte für diese Eigenschaft sind `Any`(Standard) oder `All`. Weitere Anleitungen zum Festlegen dieser Optionen finden Sie unter [Einfache Abfragesyntax](https://msdn.microsoft.com/library/dn798920.aspx).

- **searchMode.Any** schreibt vor, dass eine Übereinstimmung für einen Suchbegriff ausreichet, damit ein Element in den Suchergebnissen enthalten ist. Wenn Ihre Suchphrase `Yellowstone visitor center` ist, dann ist jedes Dokument mit einem dieser Begriffe in den Suchergebnissen enthalten. Dieser Modus ist unvoreingenommen gegenüber der *Sensitivität*.
- Das in diesem Beispiel verwendete **searchModel.All** erfordert, dass alle angegebenen Bedingungen im Dokument vorhanden sind. Dieser Modus ist strenger als **searchMode.Any**, aber wenn Sie *Genauigkeit* gegenüber Sensitivität bevorzugen, ist er wahrscheinlich die beste Wahl für Ihre Anwendung. 

> [AZURE.NOTE] **searchMode.Any** funktioniert am besten, wenn die Abfragekonstruktion hauptsächlich aus Phrasen besteht, bei minimaler Verwendung von Operatoren. Als allgemeine Faustregel gilt, dass Personen, die Konsumanwendungen wie z. B. e-Commerce-Websites durchsuchen, nur Begriffe verwenden, während Personen, die Inhalte oder Daten suchen, eher Operatoren in den Suchausdrücken nutzen. Wenn Sie glauben, dass bei Suchvorgängen wahrscheinlich Operatoren eingesetzt werden, insbesondere der `NOT (-)`-Operator, beginnen Sie mit **searchModel.All**. Im Gegensatz dazu `OR` Ihre andere Wahl **searchMode.Any** den `NOT`-Operator mit anderen Suchbegriffen befüllen, was die Ergebnisse erheblich erweitern statt einschränken kann. Das folgende Beispiel hilft Ihnen dabei, den Unterschied zu verstehen.

In dieser Aufgabe ändern Sie **SearchMode** und vergleichen auf dem Modus basierend die Ergebnisse der Suche.

1. Öffnen Sie das Browserfenster mit der Beispielanwendung, und wählen Sie **Mit Visual Studio Team Services verbinden**.

    ![][8]

2. Öffnen Sie **Search.cshtml**, suchen Sie `searchMode.All` in Zeile 39 und ändern Sie es in `searchMode.Any`.

    ![][9]

3. Klicken Sie in der Navigationsleiste rechts auf **Ausführen**.

    ![][10]
 
Geben Sie im neu erstellten Anwendungsfenster einen Suchbegriff ein, den Sie zuvor verwendet haben, wie z. B. `Yellowstone +center +building -ND`, und vergleichen Sie die Vorher- und Nachher-Ergebnisse der Änderungen in **SearchMode**.

Der Unterschied ist ziemlich groß. Anstelle von sieben Suchergebnissen erhalten Sie mehr als zwei Millionen.

   ![][11]
 
Das beobachtete Verhalten liegt am enthaltenen `NOT`-Operator (in diesem Fall "-ND"), der *ODER* statt *UND* verwendet, wenn **SearchMode** auf `Any` festgelegt ist.

Bei dieser Konfiguration beinhalten die Suchergebnisse Treffer für die Suchbegriffe `Yellowstone`, `center`und `building`, aber auch jedes Dokument, das `NOT North Dakota` ist. Da nur 13.081 Dokumente mit dem Ausdruck `North Dakota` vorhanden sind, wird fast das komplette Dataset zurückgegeben.

Dieses Szenario ist zwar zugegebenermaßen unwahrscheinlich, veranschaulicht aber die Auswirkungen von **SearchMode** auf Suchausdrücke, die den `NOT`-Operator enthalten. Daher ist das Verständnis dafür hilfreich, warum das Verhalten auftritt, und wie es geändert werden kann, wenn es unerwünscht ist.

Um mit diesem Lernprogramm fortzufahren, setzen Sie **SearchMode** wieder auf den ursprünglichen Wert zurück (auf `All` in Zeile 39 festgelegt), führen Sie das Programm aus, und verwenden Sie die neu erstellte App für die verbleibenden Vorgänge.
 
## Hinzufügen eines globalen Filters für den Bundesstaat Washington

Wenn Sie normalerweise eine Teilmenge verfügbarer Daten durchsuchen wollen, würden Sie den Filter in der Datenquelle beim Importieren von Daten festlegen. Zu Lernzwecken und da mit schreibgeschützten Daten gearbeitet wird, müssen wir den Filter in unserer Anwendung so festlegen, dass nur die Dokumente zurückgegeben werden, die den US-Bundesstaat Washington enthalten.

1. Öffnen Sie Search.cshtml, suchen Sie den **SearchParameters**-Codeblock (ab Zeile 36) und fügen Sie eine Kommentarzeile plus Filter hinzu.

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


Filter werden mit OData-Syntax angegeben und häufig mit der Facettennavigation verwendet oder sind in der Abfragezeichenfolge zum Einschränken der Abfrage enthalten. Weitere Informationen finden Sie unter [OData Filtersyntax](https://msdn.microsoft.com/library/azure/dn798921.aspx).

2. Klicken Sie auf **Run** (Ausführen).

3. Öffnen Sie die Anwendung.

4. Geben Sie den Platzhalter (*) ein, um eine Anzahl zurückgeben. Beachten Sie, dass die Ergebnisse nun auf 42.411 Elemente beschränkt sind, die von allen Dokumenten für alle geografischen Eigenschaften im Bundesstaat Washington stammen.

   ![][12]

## Hinzufügen von Trefferhervorhebungen

Nun, da Sie eine Reihe von einzeiligen Codeänderungen vorgenommen haben, empfiehlt es sich, detailliertere Änderungen zu versuchen, die Codeänderungen an mehreren Orten benötigen. Die folgende Version von **Search.cshtml** kann direkt über die Search.cshtml-Datei in der aktuellen Sitzung eingefügt werden.

Der folgende Code fügt Trefferhervorhebungen ein. Beachten Sie die neuen Eigenschaften, die in [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx) deklariert werden. Es gibt auch eine neue Funktion, die über die Ergebnissammlung läuft, um die Dokumente zu erhalten, die hervorgehoben werden sollen, sowie HTML, das das Ergebnis rendert.

Wenn Sie dieses Codebeispiel ausführen, werden Eingaben für Suchbegriffe, die eine Übereinstimmung in den angegebenen Feldern haben, in Fettschrift hervorgehoben.

   ![][14]

Möglicherweise möchten Sie eine Kopie der Originaldatei **Search.cshtml** speichern, um beide Versionen vergleichen zu können.

> [AZURE.NOTE] Kommentare wurde abgeschnitten, um die Größe der Datei zu reduzieren.
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
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
    }
    }

## Was spricht für Azure Search?

Obwohl andere Azure-Dienste wie z. B. SQL-Datenbank die Volltextsuche bieten, kann ein Dienst wie Azure Search die Suche optimieren und bietet das Paginieren und Zählen, Trefferhervorhebungen, Abfragevorschläge automatisch vervollständigen, natürliche Sprachunterstützung, Facettennavigation, Filter und vieles mehr. Wie viele unserer [Beispiele](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=search) veranschaulichen, ist es möglich, eine voll funktionsfähige suchbasierte Anwendung nur mit Azure Search und ASP.NET zu entwickeln.

## Nächste Schritte

Mithilfe des schreibgeschützten Dienstes gemäß der [Try Azur App Service](https://tryappservice.azure.com/)-Website, haben Sie die Abfragesyntax und die Volltextsuche in Aktion gesehen, von SearchMode und Filtern erfahren, und für die Suchanwendung Trefferhervorhebungen hinzugefügt. Im nächsten Schritt sollten Sie Indizes erstellen und aktualisieren. Dadurch entsteht die Möglichkeit:

- [Bewertungsprofile zu definieren](https://msdn.microsoft.com/library/dn798928.aspx), die für die Optimierung der Suchergebnisse verwendet werden, damit hochwertige Elemente zuerst angezeigt werden.
- [Vorschläge zu definieren](https://msdn.microsoft.com/library/mt131377.aspx), die die automatische Vervollständigung oder Type-Ahead-Vorschläge als Reaktion auf eine Benutzereingabe hinzufügen.
- [Indexer zu definieren](https://msdn.microsoft.com/library/dn946891.aspx), die den Index automatisch aktualisieren, wenn die Datenquelle eine Azure SQL-Datenbank oder Azure DocumentDB ist.

Um alle Aufgaben durchführen zu können, benötigen Sie ein Azure-Abonnement, damit Sie Indizes in einem Dienst erstellen und befüllen können. Weitere Informationen dazu, wie Sie sich für eine kostenlose Testversion registrieren, finden Sie auf [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/).

Weitere Informationen zu Azure Search finden Sie in der [Azure Search-Dokumentation](https://azure.microsoft.com/documentation/services/search/) auf [http://azure.microsoft.com](https://azure.microsoft.com/), oder sehen Sie sich eine beliebige Anzahl von [Beispielen und Videos](search-video-demo-tutorial-list.md) an, die die ganze Bandbreite von Azure Search-Funktionen abdecken.

## Fakten zum Code und Azure Search

Azure Search ist ein vollständig verwalteter Platform as a Service ([PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service))-Dienst, mit dem Entwickler großartige Suchfunktionen in Web- und Mobilanwendungen integrieren können. Daten, die bei Suchvorgängen verwendet werden, werden mit dem Search-Dienst in Azure gespeichert, wobei die Nähe von den Daten zu den Vorgängen eine niedrige Latenz und konsistentes Suchverhalten sicherstellen. Wir betrachten dies noch näher:

- Durchsuchbare Daten werden in einem von Azure Search verwalteten Index gespeichert.
- Das Schema, das Ihren Index definiert, ist benutzerdefiniert und gibt durchsuchbare Felder und nicht durchsuchbare Felder an, die in einem Filterausdruck und Konstrukten wie bei der Bewertung von Profile für die Optimierung der Ergebnisse nützlich sein können.
- Ein Suchindex enthält mehrere Dokumente (ähnlich einer Zeile in einer Tabelle), die durchsucht und abgerufen werden können.
- Die meisten Indizes werden aus einem einzelnen Dataset geladen. Dieses Dataset wird von Ihnen so vorbereitet, dass es nur die im Kontext von Suchvorgängen nützlichen Felder enthält. 
- Daten können automatisch von einem Indexer (nur für Azure SQL-Datenbank oder Azure DocumentDB unterstützt) geladen oder in einem Suchindex über eine der Azure Search-APIs abgelegt werden. Mit der API können Sie Daten aus einer beliebigen Datenquelle übertragen, solange sie im JSON-Format vorliegen.

Mit der Option [Azure App Service](https://tryappservice.azure.com/) enthält die Vorlage „ASP.NET + Azure Search Site“ den Quellcode für die Webanwendung, die in Visual Studio Team Services bearbeitet werden kann (verfügbar als Teil der einstündigen Sitzung). Zum Anzeigen oder Ändern des Codes sind keine separaten Entwicklungstools erforderlich.

Code wird mithilfe der [Azure Search-.NET-Clientbibliothek](https://msdn.microsoft.com/library/dn951165.aspx) in C# geschrieben, zum Ausführen von Abfragen gegen den Index, zum Bereitstellen der Facettennavigation, und zum Anzeigen von Zahlen und Suchergebnissen auf einer Webseite.

Anderer Code, der nicht in die Vorlage eingefügt ist, wurde dazu verwendet, den USGS-Suchindex zu erstellen und zu laden. Da der Dienst schreibgeschützt ist, mussten alle Vorgänge, die einen Schreibzugriff erfordern, im Voraus abgeschlossen werden. Sie sehen eine [Kopie des Schemas](#schema), die am Ende dieses Artikels dazu verwendet wird, das Schema zu erstellen.

<a name="Schema"></a>
## Zum Schema

Der folgende Screenshot zeigt das verwendete Schema, um den in dieser Vorlage verwendeten Index zu erstellen.
 
   ![][13]

### Schema.JSON-Datei

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }

<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=AcomDC_0211_2016-->