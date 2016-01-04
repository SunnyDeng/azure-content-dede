<properties
	pageTitle="Erstellen von Abfragen in Azure Search mithilfe von REST-Aufrufen | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Erstellen Sie eine Suchabfrage in Azure Search, und verwenden Sie Suchparameter zum Filtern, Sortieren und Facettieren von Suchergebnissen mithilfe der .NET-Bibliothek oder des SDK."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Erstellen von Abfragen in Azure Search mithilfe von REST-Aufrufen
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Dieser Artikel beschreibt, wie Sie eine Abfrage eines Indexes mithilfe der [Azure Search-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) erstellen. Ein Teil des folgenden Inhalts stammt aus [Dokumente durchsuchen (Azure Search-REST-API)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Mehr Kontextinformationen finden Sie im übergeordneten Artikel.

Zu den Voraussetzungen für den Import gehört ein bereits vorhandener Index, in den Dokumente mit durchsuchbaren Daten geladen wurden.

Um den Index mit der REST-API zu durchsuchen, können Sie eine GET-HTTP-Anforderung ausgeben. Ihre Abfrageparameter werden in der URL der HTTP-Anforderung definiert.

**Anforderung und Anforderungsheader**:

In der URL müssen Sie den Namen Ihres Diensts, den Indexnamen und die entsprechende API-Version bereitstellen. Die Abfragezeichenfolge am Ende der URL befindet sich dort, wo Sie die Abfrageparameter angeben. Einer der Parameter in der Abfragezeichenfolge muss die richtige API-Version sein (die aktuelle Version der API zum Zeitpunkt der Veröffentlichung dieses Dokuments ist „2015-02-28“ ).

Als Anforderungsheader müssen Sie den Inhaltstyp (Content-Type) definieren und den primären oder sekundären Administratorschlüssel Ihres Diensts bereitstellen.

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Azure Search bietet viele Optionen, um äußerst leistungsfähige Abfragen zu erstellen. Um weitere Informationen über die verschiedenen Parameter einer Abfragezeichenfolge zu erhalten, besuchen Sie bitte [diese Seite](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Beispiele**:

Im Folgenden finden Sie einige Beispiele für verschiedene Abfragezeichenfolgen. In diesen Beispielen wird ein Dummyindex mit dem Namen „hotels“ verwendet:

Durchsuchen des gesamten Indexes nach dem Begriff „quality“:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Durchsuchen des gesamten Indexes:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Durchsuchen des gesamten Indexes und Sortieren nach einem bestimmten Feld (lastRenovationDate):

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Bei erfolgreicher Abfrageanforderung wird der Statuscode „200 OK“ ausgegeben, und die Suchergebnisse liegen im Antworttext im JSON-Format vor. Weitere Informationen finden Sie im Abschnitt „Antwort“ [dieser Seite](https://msdn.microsoft.com/library/azure/dn798927.aspx).

<!---HONumber=Nov15_HO4-->