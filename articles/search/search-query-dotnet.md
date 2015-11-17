<properties
	pageTitle="Erstellen von Abfragen in Azure Search mithilfe von .NET | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Erstellen Sie eine Suchabfrage in Azure Search, und verwenden Sie Suchparameter zum Filtern, Sortieren und Facettieren von Suchergebnissen mithilfe der .NET-Bibliothek oder des SDK."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Erstellen von Abfragen in Azure Search in .NET
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Dieser Artikel beschreibt, wie Sie eine Abfrage mit dem [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) erstellen. Der folgende Inhalt ist eine Teilmenge des Artikels [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md). Ausführliche Anweisungen finden Sie im übergeordneten Artikel.

Zu den Voraussetzungen zum Erstellen einer Abfrage gehört das vorherige Einrichten einer Verbindung mit Ihrem Suchdienst, was üblicherweise über einen `SearchServiceClient` erfolgt.

Der folgende Codeausschnitt erstellt eine Methode, die eine Suchzeichenfolgeneingabe an eine „SearchDocuments“-Methode übergibt.

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
	
Diese Methode erstellt zunächst ein neues „SearchParameters“-Objekt. Damit werden zusätzliche Abfrageoptionen wie Sortierung, Filter, Paging und Facettierung festgelegt. In diesem Beispiel legen wir nur die „Filter“-Eigenschaft fest.

Im nächsten Schritt wird die Suchabfrage bereits ausgeführt. Dies erfolgt mithilfe der „Documents.Search“-Methode. In diesem Fall übergeben wir den zu verwendenden Suchtext als Zeichenfolge sowie die zuvor erstellten Suchparameter. Außerdem geben wir „Hotel“ als Typparameter für „Documents.Search“ an, wodurch das SDK angewiesen wird, die Dokumente im Suchergebnis in Objekte des Typs „Hotel“ zu deserialisieren.

Schließlich geht diese Methode durch alle Übereinstimmungen im Suchergebnis, wobei sie jedes Dokument auf der Konsole ausgibt.

Wir wollen uns nun näher ansehen, wie diese Methode aufgerufen wird:

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Im ersten Aufruf werden alle Dokumente mit den Abfrageausdrücken „fancy“ und „wifi“ gesucht. Im zweiten Aufruf ist der Suchtext auf „*“ gesetzt, d. h., es wird nach allem gesucht. Weitere Informationen zur Syntax von Such- bzw. Abfrageausdrücken finden Sie unter [Syntax für einfache Abfragen in Azure Search](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Der zweite Aufruf verwendet den OData-Ausdruck „$filter“ mit „Luxury“ als Kategorie. Diese Suche gibt dadurch nur Dokumente zurück, deren Kategoriefeld genau der Zeichenfolge „Luxury“ entspricht. Weitere Informationen zur OData-Syntax finden Sie auf der Seite [OData-Ausdruckssyntax für Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx).

<!---HONumber=Nov15_HO3-->