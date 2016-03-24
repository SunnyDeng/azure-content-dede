<properties
    pageTitle="Abfragen des Azure Search-Index | Microsoft Azure | Gehosteter Cloudsuchdienst"
    description="Erstellen Sie eine Suchabfrage in Azure Search, und verwenden Sie Suchparameter zum Filtern und Sortieren von Suchergebnissen."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="ashmaka"/>

# Abfragen des Azure Search-Index
> [AZURE.SELECTOR]
- [Übersicht](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Beim Senden von Suchanforderungen an Azure Search können Sie einige Parameter zusätzlich zu den eigentlichen Wörtern angeben, die Sie in das Suchfeld der Anwendung eingeben. Mit diesen Abfrageparametern haben Sie eine genauere Kontrolle über die Volltext-Suchoberfläche.

Unten ist eine Liste angegeben, in der kurz häufige Verwendungsmöglichkeiten der Abfrageparameter in Azure Search erläutert werden. Umfassende Informationen zu Abfrageparametern und ihrem Verhalten finden Sie auf den ausführlichen Seiten für die [REST-API](https://msdn.microsoft.com/library/azure/dn798927.aspx) und das [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## Abfragetypen

Azure Search bietet viele Optionen, um äußerst leistungsfähige Abfragen zu erstellen. Die zwei wichtigsten Abfragetypen, die Sie verwenden werden, sind `search` und `filter`. Eine `search`-Abfrage sucht nach einem oder mehreren Begriffen in allen _durchsuchbaren_ Feldern im Index. Sie funktioniert so, wie Sie es von einer Suchmaschine wie Google oder Bing erwarten. Eine `filter`-Abfrage wertet einen booleschen Ausdruck für alle _filterbaren_ Felder in einem Index aus. Im Gegensatz zu `search`-Abfragen gleichen `filter`-Abfragen den genauen Inhalt eines Felds ab, d. h., bei Zeichenfolgenfeldern muss die Groß-/Kleinschreibung berücksichtigt werden.

Suchvorgänge und Filter können separat oder zusammen verwendet werden. Wenn sie zusammen verwendet werden, wird der Filter zuerst auf den gesamten Index angewendet, und anschließend wird die Suche für die Ergebnisse des Filters ausgeführt. Filter können daher eine nützliche Methode zum Verbessern der Abfrageleistung darstellen, da sie die Menge der Dokumente reduzieren, die bei der Suchabfrage verarbeitet werden müssen.

Die Syntax für Filterausdrücke ist eine Teilmenge der [OData-Filtersprache](https://msdn.microsoft.com/library/azure/dn798921.aspx). Für Suchabfragen können Sie die [vereinfachte Syntax](https://msdn.microsoft.com/library/azure/dn798920.aspx) oder die [Lucene-Abfragesyntax](https://msdn.microsoft.com/library/azure/mt589323.aspx) verwenden. Beide sind unten beschrieben.

### Einfache Abfragesyntax
Die [einfache Abfragesyntax](https://msdn.microsoft.com/library/azure/dn798920.aspx) ist die Abfragesprache, die in Azure Search standardmäßig verwendet wird. Die einfache Abfragesyntax unterstützt eine Reihe von allgemeinen Suchoperatoren, z. B. AND, OR, NOT, Begriff, Suffix und Rangfolgeoperatoren.

### Lucene-Abfragesyntax
Mit der [Lucene-Abfragesyntax](https://msdn.microsoft.com/library/azure/mt589323.aspx) können Sie die viel genutzte und ausdrucksbasierte Abfragesprache verwenden, die als Teil von [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) entwickelt wurde.

Diese Syntax ermöglicht auf einfache Weise Folgendes: [Feldbezogene Abfragen](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [Fuzzy-Suche](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [NEAR-Suche](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [„Term Boosting“](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [Suche mit regulärem Ausdruck](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [Platzhaltersuche](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [Grundlagen der Syntax](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax) und [Abfragen mit booleschen Operatoren](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## Sortieren von Ergebnissen
Wenn Sie Ergebnisse für eine Suchabfrage erhalten, können Sie anfordern, dass Azure Search die Ergebnisse sortiert nach den Werten in einem bestimmten Feld bereitstellt. Azure Search sortiert die Suchergebnisse standardmäßig basierend auf der Rangfolge der Suchbewertung eines Dokuments, die von [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) abgeleitet wird.

Falls Azure Search die Ergebnisse sortiert nach einem anderen Wert als der Suchbewertung zurückgeben soll, können Sie den Suchparameter `orderby` verwenden. Sie können den Wert des Parameters `orderby` angeben, um Feldnamen und Aufrufe der [`geo.distance()`-Funktion](https://msdn.microsoft.com/library/azure/dn798921.aspx) für Geowerte einzubinden. Auf jeden Ausdruck kann `asc` folgen, um anzugeben, dass die Ergebnisse in aufsteigender Reihenfolge angefordert werden. Verwenden Sie `desc`, um anzugeben, dass die Ergebnisse in absteigender Reihenfolge zurückgegeben werden sollen. Standardmäßig wird die aufsteigende Sortierung verwendet.

## Paging
Mit Azure Search ist es einfach, das Paging (Anordnen auf Seiten) von Suchergebnissen zu implementieren. Mit den Parametern `top` und `skip` können Sie reibungslos Suchanforderungen ausgeben, die Ihnen das Empfangen aller Suchergebnisse in Form von verwaltbaren, sortierten Teilmengen und somit eine benutzerfreundliche Vorgehensweise auf der Suchoberfläche ermöglichen. Wenn Sie diese kleineren Teilmengen mit Ergebnissen empfangen, können Sie auch die Zahl der Dokumente in der Gesamtmenge der Suchergebnisse erhalten.

Weitere Informationen zum Paging von Suchergebnissen finden Sie im Artikel [Anordnen von Suchergebnissen auf Seiten in Azure Search](search-pagination-page-layout.md).


## Treffermarkierung
In Azure Search ist das Hervorheben der exakten Menge von Suchergebnissen, die mit der Suchabfrage übereinstimmen, dank der Parameter `highlight`, `highlightPreTag` und `highlightPostTag` einfach. Sie können angeben, für welche _durchsuchbaren_ Felder der gefundene Text hervorgehoben werden soll. Außerdem können Sie die genauen Zeichenfolgentags angeben, die am Anfang und Ende des von Azure Search zurückgegebenen Übereinstimmungstexts angefügt werden sollen.

<!---HONumber=AcomDC_0316_2016-->