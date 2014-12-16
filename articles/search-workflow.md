<properties title="Search Service: workflow for developers" pageTitle="Search-Dienst: Arbeitsablauf für Entwickler" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Azure Search: Arbeitsablauf für Entwicklung

[WACOM.INCLUDE [This article uses the Azure Preview portal](../includes/preview-portal-note.md)]

Dieser Artikel enthält einen Überblick und einige bewährte Methoden für die Erstellung und Wartung des Search-Dienstes und der Indizes. 

Wir setzen voraus, dass Sie den Dienst bereits eingerichtet haben. Wenn Sie ihn noch nicht eingerichtet haben, finden Sie weitere Anweisungen unter [Erste Schritte mit Azure Search](../search-get-started/).

+ [Schritt 1: Erstellen des Index](#sub-1)
+ [Schritt 2: Hinzufügen von Dokumenten](#sub-2)
+ [Schritt 3: Indexabfragen](#sub-3)
+ [Schritt 4: Ändern oder Löschen von Indizes und Dokumenten](#sub-4)
+ [Betrachtungen zum Speicherentwurf](#sub-5)


<h2 id="sub-1">Schritt 1: Erstellen des Index</h2>

Abfragen zielen auf einen Suchindex, der Suchdaten und Attribute enthält. Daher sind die ersten Schritte nach der Bereitstellung des Diensts die Definition des Indexschemas im JSON-Format und das Ausführen einer HTTPS-PUT-Anforderung zum Erstellen des Indexes im Dienst. 

Indizes werden vom Anwendungscode erstellt. Es gibt keine integrierten Tools oder Editoren mit denen Sie einen Index in einer Benutzeroberfläche definieren können. Beispiele für die verschiedenen Möglichkeiten zum Erstellen des Indexes finden Sie im Artikel [Erstellen Sie Ihre erste Suchlösung mit Azure Search](../search-create-first-solution/), in dem das Schema in der Datei Program.cs angegeben ist, und [Erste Schritte mit Wertungsprofilen in Azure Search](../search-get-started-scoring-profiles), in dem der Index in einer eigenständigen JSON-Schemadatei bereitgestellt wird. Weitere Informationen zum Erstellen des Index finden Sie unter [Erstellen eines Index (Azure Search-API)](http://msdn.microsoft.com/de-de/library/dn798941.aspx) im MSDN.

<h2 id="sub-2">Schritt 2: Hinzufügen von Dokumenten</h2>

Nachdem Sie den Suchindex erstellt haben, können Sie Dokumente zum Index hinzufügen, indem Sie diese im JSON-Format POSTen. Jedes Dokument besitzt einen eindeutigen Schlüssel und eine Sammlung von Feldern mit durchsuchbaren und nicht durchsuchbaren Daten. Die Dokumentdaten werden als Satz von Schlüssel-Wert-Paaren für jedes Feld dargestellt.

Es wird empfohlen, die Dokumente stapelweise hinzuzufügen, um den Durchsatz zu verbessern. Sie können bis zu 1.000 Dokumente pro Stapel bündeln, ausgehend von einer durchschnittlichen Dokumentgröße von 1-2 KB.

Für die POST-Anforderung wird ein Gesamt-Statuscode zurückgegeben. Dieser Statuscode ist entweder HTTP 200 (Erfolg) oder HTTP 207 (Mehrfachstatus) für Kombinationen aus erfolgreichen und fehlgeschlagenen Dokumenten. Neben dem Statuscode für die POST-Anforderung pflegt Azure Search außerdem ein Statusfeld für jedes Dokument. Für einen stapelweisen Upload benötigen Sie eine Möglichkeit, für jedes einzelne Dokument abzufragen, ob das Dokument erfolgreich eingefügt wurde. Diese Information finden Sie im Statusfeld. Dieses Feld hat den Wert "false", wenn das Dokument nicht geladen werden konnte.

Unter hoher Last sind gelegentliche Fehler beim Upload nicht ungewöhnlich. In diesem Fall erhalten Sie den Gesamtstatus 207 für einen teilweisen Erfolg, und die Statuseigenschaft der fehlgeschlagenen Dokumente hat den Wert "false".

> [WACOM.NOTE] Wenn der Dienst Dokumente empfängt, werden diese in die Warteschlange für die Indexierung platziert und sind möglicherweise nicht sofort in den Suchergebnissen sichtbar. Unter normaler Last werden Dokumente normalerweise innerhalb weniger Sekunden indexiert.


<h2 id="sub-3">Schritt 3: Indexabfragen</h2>

Nach der Indexierung der Dokumente können Sie Suchabfragen ausführen. Sie können je einen Index auf einmal abfragen und dabei entweder OData oder eine einfache Abfragesyntax verwenden:

[OData-Ausdruckssyntax für Azure Search](http://msdn.microsoft.com/de-de/library/dn798921.aspx)
[Syntax für einfache Abfragen in Azure Search](http://msdn.microsoft.com/de-de/library/dn798920.aspx)

<h2 id="sub-4">Schritt 4: Ändern oder Löschen von Indizes und Dokumenten</h2>

Optional können Sie Schemaänderungen am Suchindex vornehmen, Dokumente im Index ändern oder löschen, und Indizes löschen.

Beim Ändern eines Index können Sie mehrere Aktionen (einfügen, zusammenführen, löschen) in einer Stapelausführung kombinieren und so zahlreiche Roundtrips einsparen. Azure Search unterstützt momentan keine teilweisen Updates (HTTP PATCH). Wenn Sie einen Index ändern möchten, müssen Sie daher die Indexdefinition erneut senden.

<h2 id="sub-5">Betrachtungen zum Speicherentwurf</h2>

Azure Search verwendet die internen Speicher für die Indizes und Dokumente, die bei Suchvorgängen verwendet werden. Die Textanalyse und Indexanalyse hängen davon ab, dass alle durchsuchbaren Felder und zugehörige Attribute frei verfügbar sind.

Nicht alle Felder in einem Dokument sind durchsuchbar. Wenn Ihre Anwendung z. B. einen Onlinekatalog für Musik oder Videos enthält, sollten Sie die Binärdateien nach Möglichkeit im Azure Blob-Dienst oder einer anderen Speicherform ablegen. Die Binärdateien selbst sind nicht durchsuchbar und müssen daher nicht im Azure Search-Speicher abgelegt werden. Obwohl Sie Bilder, Videos und Audiodateien in anderen Diensten oder an anderen Speicherorten speichern sollten, sollten Sie ein Feld einschließen, das auf die URL zum Speicherort der Datei verweist. Auf diese Weise können Sie die externen Daten als Teil Ihrer Suchergebnisse zurückgeben. 

Weitere Informationen zur Erstellung von Indizes und Dokumenten finden Sie unter [Azure Search REST-API](http://msdn.microsoft.com/de-de/library/dn798935.aspx).


<!--Anchors-->
[Schritt 1: Erstellen des Index]: #sub-1
[Schritt 2: Hinzufügen von Dokumenten]: #sub-2
[Schritt 3: Indexabfragen]: #sub-3
[Schritt 4: Ändern oder Löschen von Indizes und Dokumenten]: #sub-4
[Wählen eines Dokumentspeicherorts]: #sub-5


<!--Image references-->

<!--Link references-->
[Erste Schritte mit Azure Search]: ../search-get-started/
[Verwalten Ihres Suchdiensts in Microsoft Azure]: ../search-manage/
[Erstellen Sie Ihre erste Suchlösung mit Azure Search]: ../search-create-first-solution/

