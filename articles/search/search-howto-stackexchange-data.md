<properties
	pageTitle="Durchsuchen von StackExchange-Daten mithilfe von Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Informationen zum Ausführen von REST-Suchen mithilfe von Azure Search, einem in Microsoft Azure gehosteten Cloudsuchdienst."
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="liamca"/>

# Durchsuchen von Stack Exchange-Daten mithilfe von Azure Search

Dieser Artikel bietet eine exemplarische Vorgehensweise, in der einige der wichtigsten Volltextsuchfunktionen vorgestellt werden, die [Azure Search](https://azure.microsoft.com/services/search/) zu bieten hat. Verwendet werden Daten, die von Stack Exchange für die Creative Commons-Nutzung mit der folgenden [Attribuierung](http://blog.stackoverflow.com/2009/06/attribution-required/) zur [Verfügung gestellt](https://archive.org/details/stackexchange) wurden.

## Erste Schritte

Um einige dieser Funktionen zu verdeutlichen, habe ich einen Azure Search-Index für Sie erstellt, mit dem experimentieren können und der Daten aus Programmer Stack Exchange ab dem 14. Okt. 2015 enthält. HINWEIS: Ich zeigen Ihnen später auch, wie Sie mit diesen Daten einen eigenen Azure Search-Index erstellen können.

Lassen Sie uns mit einer wirklich einfachen Volltextsuchabfrage beginnen, bei der die Benutzer das Wort „Azure“ eingeben, um alle Stack Exchange-Beiträge mit Bezug auf Azure zu finden. Probieren Sie dies durch Klicken auf diesen Link aus, um sich eine Demo anzusehen:

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

Bei diesem Beispiel übergeben wir einfach das Wort "Azure" als Suchparameter und zeigen die JSON-formatierten Ergebnisse an, die zurückgegeben werden. Hier sind einige weitere Beispiele für Abfragen, die Sie ausprobieren können.

-	`Faceting`: Wenn der Benutzer das Dataset durchsucht, ist die Möglichkeit der Filterung der Daten für das Navigieren durch die Ergebnisse hilfreich. Um dies zu implementieren, beginnen Sie in der Regel mit einer Gruppe von Kategorien (Facets), die dem Benutzer angezeigt werden. Hier sind einige Beispiele von Facets, die wir nutzen können:
  -	**Tags**: Vielen Fragen sind Tags zugeordnet, um Benutzern Detailsuchen in bestimmten Kategorien zu ermöglichen.
  -	**Datumsangaben**: Ein Benutzer möchte ggf. nur Fragen anzeigen, die in einem bestimmten Zeitrahmen gestellt oder beantwortet wurden.
  -	**Benutzer**: Sie möchten ggf. nur Ergebnisse von bestimmten Benutzern anzeigen. In diesem Beispiel suchen wir „Azure“, geben aber die Anzahl der Facets für die Benutzernamen „tagsCollection“ und „acceptedAnswerDisplayName“ zurück.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering`: Nachdem ein Benutzer ein Facet gewählt hat, möchten Sie anschließend eine weitere Suche ausführen, aber einen Filter zum Begrenzen der Ergebnisse auf diesen Facetwert nutzen. Beispiel: Nach „Azure“ suchen, aber die Ergebnisse auf das Tag „Architektur“ begrenzen, und zwar sortiert nach „viewCount“ in absteigender Reihenfolge:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Spelling Mistakes`: Unsere neue Unterstützung für [Lucene-Abfrageausdrücke](https://msdn.microsoft.com/library/mt589323.aspx) (in der Vorschauphase) ermöglicht Ihnen nützliche Abfragen, wie z. B. nach Fuzzyübereinstimmungen von Ergebnissen und Begrenzen der Suche auf bestimmte Felder. Bei diesem Beispiel wird das Titelfeld auf das Wort „visualize“ durchsucht, doch das Tildezeichen (~) bedeutet Fuzzyübereinstimmungen, was heißt, dass auch Ergebnisse wie „visualise“ und „visualizing“ zurückgegeben werden.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning`: [Bewertungsprofile](https://msdn.microsoft.com/library/azure/dn798928.aspx) sind äußerst hilfreich bei der Optimierung der Ergebnisse, die vom Suchdienst zurückgegeben werden. Wir können nun sogar auch [Lucene-Abfrageausdrücke](https://msdn.microsoft.com/library/mt589323.aspx) verwenden, um einzelne Felder und Begriffe dynamisch mit Bewertungen zu versehen. Wenn wir z. B. im Titelfeld nach den Worten „visualize“ oder „chart“ suchen möchten, Elementen mit dem enthaltenen Wort „chart“ jedoch mehr Gewicht beimessen möchten, können wir wie folgt vorgehen:

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  Dieses Dataset enthält viele andere Felder, die zum Optimieren relevanter Ergebnisse für Benutzer dienen können. Beispielsweise kann ich Folgendes verwenden:

  -	eine Bewertung nach **Menge** anstelle numerischer Felder wie „answerCount“, „commentCount“, „favoriteCount“ und „viewCount“ zur Optimierung der Suchergebnisse, sofern diese hohe Anzahlwerte aufweisen.
  -	eine Bewertung nach **Aktualität** anstelle von Datum-/Uhrzeitfeldern wie „creationDate“ und „lastActivityDate“ zum Hervorheben von Elementen, die vor Kurzem erstellt wurden oder aktiv waren.
  -	**Feldgewichtungen** zum Angeben, dass wenn der Suchtext im Text der Frage gefunden wird, dies relevanter ist, als wenn er in der Antwort gefunden wird.

Zu anderen Dinge, mit denen Sie ggf. experimentieren möchten, gehören u. a.:

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx): Während die Benutzer Eingaben in das Suchfeld vornehmen, ist es hilfreich, Felder wie „Title“, „Tags“ und „UserName“ mit automatischer Vervollständigung zu verwenden.  

-	`Recommendations`: Oft benötigen Sie Tools wie Apache Mahout oder Azure Machine Learning zum einfacheren Erstellen von Vorschlägen, die Ihnen das Anzeigen ähnlicher Fragen erlauben, die für Benutzer ggf. von Interesse sind. Doch glücklicherweise bietet dieses Dataset bereits einige Vorschläge.

Probieren Sie diese JSFiddle-Seite nach Wunsch aus, um verschiedene Arten von Abfragen zu testen. Wenn Sie mehr darüber erfahren möchten, wie dieser Index erstellt wurde, lesen Sie bitte weiter. Nach Wunsch können sich auch über mein Twitter-Konto [@liamca](https://twitter.com/liamca) direkt an mich wenden.

## Wie wurde dieser Azure Search-Index erstellt?

Brent hat bereits viel harte Arbeit geleistet, indem gezeigt wird, wie das Staging der Daten in einer SQL-Datenbank erfolgt. Wenn Sie diese Schritte zum Staging der Daten selbst durchlaufen möchten, schauen Sie sich sein [Tutorial hier](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/) an. Andernfalls können Sie entweder einfach die Azure SQL-Datenbank nutzen, die ich mit Daten ab dem 15. Oktober 2015 vorab aufgefüllt habe, oder den von mir erstellten Azure Search-Index ausprobieren. Die Details sind unten im Abschnitt „Importieren von Daten aus einer Azure SQL-Datenbank in einer Stagingumgebung“ aufgeführt. Das einzige, was ich getan habe, das über Brents Bemühungen hinaus geht, war das Erstellen einer Sicht in meiner Azure SQL-Datenbank, die von meinem [Azure Search-Indexer](https://msdn.microsoft.com/library/azure/dn946891.aspx) verwendet wird, um eine Gruppe von Tabellen zu durchforsten und zu erfassen, die ich gerne verwenden möchten. So habe ich diese Sicht definiert.

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

Sobald dies erfolgt ist, können Sie im [klassischen Azure-Portal](https://portal.azure.com) Daten aus der obigen Azure SQL-Sicht importieren, woraufhin ein Azure Search-Index basierend auf dem Schema der Felder in der Sicht erstellt wird. Wenn Sie meine Azure SQL-Datenbank in der Stagingumgebung nutzen möchten, folgt hier die schreibgeschützte Verbindungszeichenfolge, die Sie dazu benötigen:

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=AcomDC_0128_2016-->