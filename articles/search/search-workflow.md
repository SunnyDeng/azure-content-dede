<properties
	pageTitle="Typischer Workflow für die Azure Search-Entwicklung | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Ein Workflow oder Wegweiser zum Erstellen von Prototypen und Produktionsanwendungen mit Integration von Azure Search, einem gehosteten Cloudsuchdienst."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Typischer Workflow für die Azure Search-Entwicklung

Dieser Artikel enthält einen Wegweiser für die Einbindung von Azure Search, einem gehosteten Cloudsuchdienst, in Ihre benutzerdefinierte Anwendung, um deren Suchfunktionalität zu verbessern. Unabhängig davon, ob Sie Azure Search zunächst nur ausprobieren wollen oder gleich voll einsteigen möchten, benötigen Sie vorab einige Informationen dazu, wie Sie Azure Search in Ihr benutzerdefiniertes Entwicklungsprojekt integrieren können.

In den folgenden Abschnitten wird ein typischer Workflow für einen anfänglichen Prototyp beschrieben, mit dem Sie auswerten können, wie gut Azure Search die Suchanforderungen für Ihre Anwendung erfüllt. Im zweiten Teil dieses Artikels werden wichtige Entwurfsentscheidungen behandelt, die mit einem Mehraufwand bei der Anwendungsentwicklung einhergehen.

Bevor Sie mit dem Erstellen eines Prototyps beginnen, wird empfohlen, eines der Lernprogramme für den Einstieg durchzuarbeiten, oder sich dieses [einstündige Video für einen umfassenden Einblick](http://azure.microsoft.com/documentation/videos/tech-ed-europe-2014-azure-search-deep-dive/) (in englischer Sprache) anzusehen. Lernprogramme für den Einstieg werden in diesen Sprachen bereitgestellt: [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md), [Node.JS](search-get-started-nodejs.md).

## Entwickeln eines Prototyps

Der schnellste Weg zu einem erfolgreichen Prototyp umfasst typischerweise die in diesem Abschnitt beschriebenen Schritte. Hierbei werden die folgenden Schritte ausgeführt: Bereitstellen eines Diensts, Definieren eines Schemas für Ihren Index, Laden des Index mit Dokumenten und Abfragen des Index.

Für Anwendungen mit flüchtigen Daten (wenn beispielsweise häufig Änderungen an Bestand oder Inhalten vorgenommen werden) sollte Ihr Prototyp auch über eine Komponente zum Aktualisieren von Dokumenten verfügen.

   ![][1]

### Schritt 1: Bereitstellen des Diensts

Azure Search ist ein vollständig verwalteter Onlinedienst, der über ein Azure-Abonnement bereitgestellt wird. [Nachdem Sie sich für Azure registriert haben](http://azure.microsoft.com/pricing/free-trial/), kann der Suchdienst schnell hinzugefügt werden. Anweisungen zum Hinzufügen eines Suchdiensts zu Ihrem Abonnement finden Sie unter [Erstellen eines Suchdiensts im Portal](search-create-service-portal.md).

Es stehen zwei Tarife zur Auswahl. Es wird empfohlen, den (kostenlosen) Shared-Tarif für die Prototyperstellung zu verwenden, mit der Einschränkung, dass Sie nur mit einer kleinen Teilmenge Ihrer Daten arbeiten können. Der Shared-Tarif ist für bestehende Abonnenten (reguläre Mitgliedschaft oder Testmitgliedschaft) kostenlos und schnell einzurichten, allerdings ist die Anzahl von Indizes und Dokumenten beschränkt. Sie können bis zu 3 Indizes und bis zu 10.000 Dokumente pro Index bzw. 50 MB Speicherplatz insgesamt nutzen – je nachdem, welcher Wert zuerst erreicht wird.

### Schritt 2: Erstellen des Index

Nachdem Sie den Dienst erstellt haben, können Sie einen Index erstellen, beginnend mit der zugehörigen Schemadefinition.

Am schnellsten und einfachsten kann ein Index über das Azure-Portal erstellt werden. Jedes Dokument muss mindestens über einen eindeutigen Schlüssel und mindestens ein Feld mit durchsuchbaren Daten verfügen. Weitere Informationen für den Einstieg finden Sie unter [Erstellen eines Index im Portal](search-create-index-portal.md).

> [AZURE.NOTE]Details zum Azure Search-Index
>
> Als *Index* werden strukturierte, dauerhafte Daten bezeichnet, die als *Suchkorpus* für alle nachfolgenden Suchvorgänge verwendet werden. Ihr Suchkorpus wird in der Cloud als Bestandteil Ihres Suchdienstabonnements gespeichert, wodurch Suchvorgänge schnell und konsistent ausgeführt werden können. In der Suchterminologie wird ein Element in Ihrem Suchkorpus als *Dokument* bezeichnet, und die Menge aller Dokumente ist die *Dokumentsammlung*.
>
>Ein *Indexschema* definiert alle Felder innerhalb eines Dokuments nach Name, Datentyp und Attributen. Letztere geben an, ob das Feld durchsucht, gefiltert, eine Facettensuche durchgeführt werden kann usw.
>
> Neben der Dokumentstruktur gibt ein Indexschema auch Bewertungsprofile an, die Kriterien zum Höherstufen eines Suchergebnisses sowie Konfigurationseinstellungen bereitstellen, die Abfragen mit einer automatischen Vervollständigung (Vorschläge) ermöglichen und CORS-Optionen für domänenübergreifende Abfrageanforderungen bieten. *Für Prototypen wird empfohlen, lediglich mit der Festlegung der Felder in einem Dokument zu beginnen* und anschließend weitere Features hinzuzufügen (siehe Schritt 5 für eine Liste der zusätzlichen Funktionen, die später hinzugefügt werden können).
>
> Angewendet auf ein praktisches Beispiel betrachten wir eine E-Commerce-Anwendung. Der Suchindex würde alle Produkte oder Dienste umfassen, die in Ihrer Anwendung durchsucht werden können (alle Elemente, die als Ergebnis einer Suche zurückgegeben werden). Es läge ein Dokument für jede SKU. Jedes Dokument umfasst Produktname, Marke, Größen, Preis, Farben und sogar Verweise auf Bilder oder andere Ressourcendateien, die innerhalb der Suchergebnisse zurückgegeben werden sollen.

### Schritt 3: Laden von Dokumenten

Nachdem Sie den Index in Azure Search gespeichert haben, wird der Index im nächsten Schritt mit Dokumenten gefüllt. In diesem Schritt werden Daten hochgeladen, analysiert, mit Token versehen und in Datenstrukturen gespeichert (z. B. in invertierten Indizes), die für Suchworkloads konzipiert sind.

Daten, die Sie in einen Index hochladen, müssen dem im vorherigen Schritt definierten Schema entsprechen. Die Dokumentdaten werden für jedes Feld als Satz aus Schlüssel-Wert-Paaren im JSON-Format dargestellt. Wenn das Schema ein ID-Feld (Schlüssel), ein Namensfeld, ein Nummernfeld und ein URL-Feld angibt (dieses kann verwendet werden, wenn Sie externe Bilder in die Suchergebnisse einbeziehen möchten), dann müssen Sie für alle in den Index geladenen Dokumente für jedes Feld Werte (oder NULL) angeben.

Es gibt verschiedene Möglichkeiten zum Laden von Dokumenten, zurzeit ist jedoch für alle Methoden eine API erforderlich. Für die meisten Prototypen ist dieser Schritt in Bezug auf den Codierungsaufwand möglicherweise der zeitraubendste. Optionen werden weiter unten in diesem Artikel beschrieben.

> [AZURE.NOTE]Denken Sie daran, dass im Shared-Tarif eine Begrenzung von 10.000 Dokumente pro Index gilt. Verringern Sie Ihr Dataset so, dass die Grenzwerte nicht erreicht werden. Weitere Details finden Sie im Abschnitt [Limits und Einschränkungen](search-limits-quotas-capacity.md).

#### Laden von Daten in einen Index

Ein Ansatz besteht darin, einen Indexer zu verwenden. Für Azure DocumentDB- oder relationale SQL Server-Datenquellen in Azure (insbesondere für Azure SQL-Datenbank oder SQL Server in einer Azure-VM), können Sie [Indexer](https://msdn.microsoft.com/library/dn946891.aspx) zum Abrufen von Dokumenten aus einer unterstützten Datenquelle verwenden. Codebeispiele, die Indexer zum Laden von Dokumenten verwenden, finden Sie in diesen Lernprogrammen für die ersten Schritte: [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md), [Node.JS](search-get-started-nodejs.md).

Eine zweite Option besteht darin, mit der REST-API oder der .NET-Bibliothek ein einfaches Programm zum Laden der Dokumente zu schreiben:

- [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (REST-API)](https://msdn.microsoft.com/library/dn798930.aspx)
- [DocumentOperationsExtensions Class](https://msdn.microsoft.com/library/microsoft.azure.search.documentoperationsextensions.aspx)

Eine dritte Option besteht darin, für sehr kleine Datasets [Fiddler](search-fiddler.md) oder [Chrome Postman](search-chrome-postman.md) zum Hochladen von Dokumenten zu verwenden.

Eine vierte Option, vielleicht die einfachste, besteht darin, Code entweder aus [Adventure Works – C#-REST-API-Beispiel](https://azuresearchadventureworksdemo.codeplex.com/), mit dem Dokumente aus einer eingebetteten Datenbank (.mdf) in das Projekt geladen werden, oder aus [Bewertungsprofile – C#-REST-API-Beispiel](https://azuresearchscoringprofiles.codeplex.com/) zu entlehnen, mit dem Daten aus im Projekt enthaltenen JSON-Datendateien geladen werden.

> [AZURE.TIP]Sie können das [Beispiel zu Bewertungsprofilen](https://azuresearchscoringprofiles.codeplex.com/) ändern und ausführen, indem Sie die JSON-Datendateien und die Datei "schema.json" durch die für Ihre Anwendung gültigen Daten ersetzen.

### Schritt 4: Abfragen von Dokumenten

Sobald Dokumente in den Index geladen wurden, können Sie Ihre erste Abfrage schreiben.

Die schnellste Methode, anfängliche Suchergebnisse vom Suchdienst zu erhalten, ist die Verwendung von [Fiddler](search-fiddler.md) oder [Chrome Postman](search-chrome-postman.md) zum Anzeigen einer Antwort. Tatsächlich aber werden Sie Code für eine einfache Benutzeroberfläche schreiben wollen, um die Ergebnisse in einem lesbaren Format anzuzeigen.

Zu den APIs für Suchvorgänge gehören:

- [Search Documents-Vorgang](https://msdn.microsoft.com/library/dn798927.aspx)
- [SearchIndexClient-Klasse](https://msdn.microsoft.com/library/microsoft.azure.search.searchindexclient.aspx)

In Azure Search können Abfragen sehr einfach sein. Durch das Einschließen von `search=*` in der URI werden die ersten 50 Elemente im Suchkorpus zurückgegeben. Durch das Festlegen von `search=<some phrase>` wird eine Volltextsuche nach einem Ausdruck durchgeführt, und es werden bis zu 50 Dokumente zurückgegeben – sofern mindestens 50 Dokumente eine Übereinstimmung mit der Sucheingabe aufweisen.

50 Dokumente sind als Standardwert festgelegt. Sie können die Anzahl von zurückgegebenen Elementen über den `$Count`-Abfrageparameter ändern. Dieser Parameter wird unter [Dokumente durchsuchen](https://msdn.microsoft.com/library/dn798927.aspx) dokumentiert.

> [AZURE.TIP]Die wohl umfangreichste Liste mit Abfragebeispielen finden Sie unter [Dokumente durchsuchen](https://msdn.microsoft.com/library/dn798927.aspx), aber Sie können sich auch die Liste der unterstützten Operatoren in der [Syntaxreferenz](https://msdn.microsoft.com/library/dn798920.aspx) ansehen.

### Schritt 5: Untersuchen weiterer Features

Nun, da Sie über einen Dienst und einen Index verfügen, können Sie mit Features experimentieren, um das Sucherlebnis weiter zu verbessern. Nachfolgend finden Sie eine Liste der Features, die untersucht werden können.

**Suchseiten** enthalten häufig Dokumentzahlen in einem Resultset oder verwenden eine Paginierung zum Unterteilen von Ergebnissen in besser verwaltbare Mengen. Ausführliche Informationen finden Sie unter [Paginierung](search-pagination-page-layout.md).

**searchMode=all** ist ein Abfrageparameter, der die Auswertung des NOT-Operators durch Azure Search ändert. Standardmäßig führen Abfragen mit NOT (-) eher zu einer größeren Ergebnismenge als zu einer Eingrenzung derselben. Sie können diesen Parameter festlegen, um die Auswertung des Operators zu ändern. Eine entsprechende Dokumentation finden Sie unter [Dokumente durchsuchen](https://msdn.microsoft.com/library/dn798927.aspx) oder [SearchMode-Enumeration](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchmode.aspx).

**Bewertungsprofile** werden zum Höherstufen von Suchbewertungen verwendet, sodass Elemente, die vordefinierten Kriterien entsprechen, weiter oben in den Suchergebnissen angezeigt werden. Eine Schrittanleitung für dieses Feature finden Sie unter [Erste Schritte mit Bewertungsprofilen](search-get-started-scoring-profiles.md).

**Filter** werden verwendet, um Suchergebnisse anhand von zusätzlichen Kriterien für die Auswahl weiter einzugrenzen. Filterausdrücke werden innerhalb der Abfrage platziert. Ausführliche Informationen finden Sie unter [Dokumente durchsuchen](https://msdn.microsoft.com/library/dn798927.aspx).

Die **Facettennavigation** wird für eine selbsttätige Filterung eingesetzt. Azure Search erstellt und liefert die Struktur, und Ihr Code rendert die Struktur der Facettennavigation in einer Seite mit Suchergebnissen. Ausführliche Informationen finden Sie unter [Facettennavigation](search-faceted-navigation.md).

**Vorschläge** beziehen sich auf eine automatische Vervollständigung von Abfragen, die dem Benutzer Suchbegriffe vorschlagen, wenn dieser die ersten Buchstaben eines Suchausdrucks eingibt. Weitere Informationen finden Sie unter [Suggestions-Vorgang](https://msdn.microsoft.com/library/dn798936.aspx) oder [Suggesters-Klasse](https://msdn.microsoft.com/library/microsoft.azure.search.models.suggester.aspx).

**Sprachanalyseprogramme** stellen die zur Textanalyse verwendeten Sprachregeln bereit. Das Standardprogramm zur Sprachanalyse für Azure Search ist Lucene (Englisch), Sie können jedoch auch andere oder sogar mehrere Analyseprogramme verwenden, indem Sie diese in Ihrem Index angeben. Lucene-Analyseprogramme stehen in allen APIs zur Verfügung. Microsoft NLPs (Natural Language Processors) stehen nur in der [2015-02-28-Preview-REST-API](search-api-2015-02-28-preview.md) zur Verfügung. Weitere Informationen finden Sie unter [Sprachunterstützung](https://msdn.microsoft.com/library/dn879793.aspx).

### Schritt 6: Aktualisieren von Indizes und Dokumenten

Für einige der Features, die Sie auswerten möchten, ist möglicherweise eine Aktualisierung des Index erforderlich, was wiederum eine Aktualisierung der Dokumente erforderlich macht.

Wenn Sie einen Index oder Dokumente aktualisieren müssen, um beispielsweise Vorschläge hinzuzufügen oder Sprachanalyseprogramme für Felder anzugeben, die Sie zu diesem Zweck hinzugefügt haben, finden Sie nachfolgend entsprechende Anleitungen:

- [Index aktualisieren (REST-API)](https://msdn.microsoft.com/library/dn800964.aspx)
- [Indexer aktualisieren (REST-API)](https://msdn.microsoft.com/library/dn946892.aspx)
- [Dokumente hinzufügen, aktualisieren oder löschen (REST-API)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Index-Klasse (.NET-Bibliothek)](https://msdn.microsoft.com/library/microsoft.azure.search.models.index.aspx)
- [Documents-Klasse (.NET-Bibliothek)](https://msdn.microsoft.com/library/microsoft.azure.search.models.document.aspx)

Nachdem Sie einen Prototyp erstellt haben, der einen Machbarkeitsnachweis erbringt, können Sie das erworbene Wissen dazu nutzen, ein Entwicklungsprojekt zur Unterstützung von Produktionsworkloads zu entwerfen.

## Anwendungsentwicklung

Für die nächste Phase müssen Sie Entscheidungen dazu treffen, welche APIs verwendet werden sollen, wie Dokumente verwaltet und wie häufig sie hochgeladen werden. Außerdem müssen Sie entscheiden, ob externe Ressourcen in Ihre Suchergebnisse einbezogen werden sollen.

Ihr Projektentwurf umfasst weiterhin sämtliche der für Prototypen beschriebenen Schritte. Anstatt jedoch die schnellste Methode zu wählen, berücksichtigen Sie jetzt, welche Ansätze am besten für Ihre Gesamtlösung geeignet sind.

### Auswählen einer API

Azure Search bietet zwei Programmiermodelle: die .NET-Bibliothek für verwalteten Code und eine REST-API für Programmiersprachen wie Java, JavaScript oder andere Sprachen, die nicht auf das Microsoft .NET Framework abzielen.

Aktuell ist ein kleiner Teil der Features noch nicht in der .NET-Bibliothek enthalten, d. h. auch wenn Sie verwalteten Code bevorzugen, müssen Sie auf die REST-API zurückgreifen, um die gewünschten Funktionen zu erhalten. Folgende Features sind nur in der REST-API enthalten:

- [Microsoft NLPs (Natural Language Processors) – nur Vorschau](../search-api-2015-02-28-preview/)
- [moreLikeThis-Funktion – nur Vorschau](../search-api-2015-02-28-preview/)
- [Verwaltungs-API](https://msdn.microsoft.com/library/dn832684.aspx)

Überprüfen Sie regelmäßig den Artikel [Neuerungen](search-latest-updates.md), um Änderungen am Funktionsstatus zu überwachen.

### Legen Sie die Methoden für die Datensynchronisierung fest: Push oder Pull

Push- und Pullmodelle beziehen sich darauf, wie Dokumente im Index aktualisiert werden. Häufig gibt das Szenario das richtige Modell vor.

Beispielsweise benötigen Sie als Onlinehändler sehr wahrscheinlich ein Pushmodell, damit Sie Änderungen am Bestand sowohl in Ihre OLTP-Datenbank als auch in den Azure Search-Index pushen oder doppelt schreiben können. Wenn eine bestimmte SKU ausverkauft oder eine Größe oder Farbe nicht mehr erhältlich ist, muss der Index so schnell wie möglich aktualisiert werden, um Enttäuschungen auf Kundenseite zu vermeiden. Nur Pushmodelle bieten eine Aktualisierung Ihres Suchindex nahezu in Echtzeit.

Es gibt in Azure Search keinen spezifischen Mechanismus zum Implementieren eines Pushmodells. Die Dokumentaktualisierung muss auf der Datenebene in Ihrem Anwendungscode entweder über die [REST-API](https://msdn.microsoft.com/library/dn798935.aspx) oder die [.NET-Bibliothek](https://msdn.microsoft.com/library/dn951165.aspx) durchgeführt werden. Als Hinweis für die Implementierung: Die Verwendung einer Produkt-SKU für den Dokumentschlüssel kann bei dieser Aufgabe nützlich sein.

Pull-Modelle sind üblicherweise geplante Vorgänge, bei denen Daten aus externen Datenquellen abgerufen werden. In Azure Search steht ein Pullmodell über [Indexer](https://msdn.microsoft.com/library/azure/dn946891.aspx) zur Verfügung, die wiederum für spezifische Datenquellen zur Verfügung stehen: Azure DocumentDB oder Azure SQL-Datenbank (sowie SQL Server in Azure-VMs)

### Laden von Dokumenten in Batches

Es wird empfohlen, die Dokumente in Batches hinzuzufügen, um den Durchsatz zu verbessern. Sie können bis zu 1.000 Dokumente pro Batch zusammenfassen, ausgehend von einer durchschnittlichen Dokumentgröße von 1 bis 2 KB.

Für die POST-Anforderung wird ein Gesamt-Statuscode zurückgegeben. Dieser Statuscode ist entweder HTTP 200 (Erfolg) oder HTTP 207 (Mehrfachstatus) für Kombinationen aus erfolgreichen und fehlerhaften Dokumenten. Neben dem Statuscode für die POST-Anforderung pflegt Azure Search außerdem ein Statusfeld für jedes Dokument. Für einen Batchupload benötigen Sie eine Möglichkeit, für jedes einzelne Dokument abzufragen, ob das Dokument erfolgreich eingefügt wurde. Diese Information finden Sie im Statusfeld. Dieses Feld hat den Wert "false", wenn das Dokument nicht geladen werden konnte.

Unter hoher Last sind gelegentliche Fehler beim Upload nicht ungewöhnlich. In diesem Fall erhalten Sie den Gesamtstatus 207 für einen teilweisen Erfolg, und die Statuseigenschaft der fehlerhaften Dokumente hat den Wert "false".

> [AZURE.NOTE]Wenn der Dienst Dokumente empfängt, werden diese in die Warteschlange für die Indexierung platziert und sind möglicherweise nicht sofort in den Suchergebnissen sichtbar. Unter normaler Last werden Dokumente normalerweise innerhalb weniger Sekunden indexiert.

Beim Ändern eines Index können Sie mehrere Aktionen (einfügen, zusammenführen, löschen) in einer Batchausführung kombinieren und so zahlreiche Roundtrips einsparen. Azure Search unterstützt momentan keine teilweisen Updates (HTTP PATCH). Wenn Sie einen Index ändern möchten, müssen Sie daher die Indexdefinition erneut senden.

### Integrieren externer Daten in Suchergebnisse

Azure Search verwendet die internen Speicher für die Indizes und Dokumente, die bei Suchvorgängen verwendet werden. Die Textanalyse und Indexanalyse hängen davon ab, dass alle durchsuchbaren Felder und zugehörige Attribute frei verfügbar sind.

Es sind jedoch nicht alle Felder in einem Dokument durchsuchbar. Wenn Ihre Anwendung z. B. einen Onlinekatalog für Musik oder Videos enthält, sollten Sie die Binärdateien nach Möglichkeit im Azure Blob-Dienst oder einer anderen Speicherform ablegen. Die Binärdateien selbst sind nicht durchsuchbar und müssen daher nicht im Azure Search-Speicher abgelegt werden. Obwohl Sie Bilder, Videos und Audiodateien in anderen Diensten oder an anderen Speicherorten speichern sollten, sollten Sie ein Feld einschließen, das auf die URL zum Speicherort der Datei verweist. Auf diese Weise können Sie die externen Daten als Teil Ihrer Suchergebnisse zurückgeben.

Um externe Daten zu verwenden, sollten Sie ein Feld in Ihrem Index definieren, das einen URL-Zeiger auf die externe Datendatei speichert. Wenn Sie eine [Lookup Documents](https://msdn.microsoft.com/library/dn798929.aspx)-Anforderung ausgeben oder das Feld in Suchergebnisse einschließen, erscheint die Binärdatei im Kontext eines Dokuments.

### Kapazitätsplanung

Besonders positiv hervorzuheben ist, wie einfach Ressourcen in Azure Search abhängig vom Bedarf zentral hoch- oder herunterskaliert werden können. Auch wenn durch diese Funktion die Anforderung für eine Kapazitätsplanung nicht entfällt, werden die Risiken jedoch minimiert. Sie laufen nicht Gefahr, überflüssige oder falsche Hardware zum Ausführen Ihrer Suchworkloads zu erwerben.

Im letzten Schritt überprüfen Sie die vorhandenen Ressourcenebenen für Replikate und Partitionen und ermitteln, ob Anpassungen erforderlich sind. Die einfachste Möglichkeit zum Anpassen der Kapazität ist das [Azure-Portal](https://ms.portal.azure.com/).

Denken Sie daran, dass nur im Standard-Tarif eine Skalierung durchgeführt werden kann. Darüber hinaus kann es abhängig vom Grad der Anpassung zwischen einigen Minuten bis hin zu mehreren Stunden dauern, zusätzliche Cluster für Ihren Dienst bereitzustellen.

> [AZURE.NOTE]Mithilfe der Verwaltungs-REST-API können Sie eine programmgesteuerte Anpassung der Kapazität durchführen. Weitere Informationen finden Sie unter [Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn832684.aspx).


<!--Image references-->
[1]: ./media/search-workflow/AzSearch-Workflow.png

<!---HONumber=Nov15_HO3-->