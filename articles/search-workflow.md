<properties title="Search Service: workflow for developers" pageTitle="Search Service: workflow for developers" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Azure Search: Entwicklungs-Workflow

[WACOM.INCLUDE [Dieser Artikel verwendet das Azure-Vorschauportal][]]

Dieser Artikel enthält einen Überblick und einige bewährte Methoden für die Erstellung und Wartung des Suchdienstes und der Indizes.

Wir setzen voraus, dass Sie den Dienst bereits eingerichtet haben. Falls Sie dies noch nicht getan haben, lesen Sie den Artikel [Konfigurieren der Suche im Azure-Vorschauportal][].

-   [Schritt 1: Erstellen des Index][]
-   [Schritt 2: Hinzufügen von Dokumenten][]
-   [Schritt 3: Indexabfragen][]
-   [Schritt 4: Ändern oder Löschen von Indizes und Dokumenten][]
-   [Betrachtungen zum Speicherentwurf][]

## Schritt 1: Erstellen des Index

Abfragen (zumindest nicht-Systemabfragen) werden auf einem Suchindex durchgeführt, der Suchdaten und Attribute enthält. In diesem Schritt definieren Sie das Indexschema im JSON-Format und führen eine HTTPS PUT-Anforderung aus, um diesen Index im Dienst zu erstellen.

Indizes werden üblicherweise in Ihrer lokalen Entwicklungsumgebung entworfen. Es existieren keine integrierten Tools oder Editoren für die Indexdefinition. Weitere Informationen zur Erstellung von Indizes finden Sie unter [Index erstellen (Azure Search-API)][] auf MSDN.

## Schritt 2: Hinzufügen von Dokumenten

Nachdem Sie den Suchindex erstellt haben, können Sie Dokumente zum Index hinzufügen, indem Sie diese im JSON-Format POSTen. Jedes Dokument besitzt einen eindeutigen Schlüssel und eine Sammlung von Feldern mit durchsuchbaren und nicht durchsuchbaren Daten. Die Dokumentdaten werden als Satz von Schlüssel-Wert-Paaren dargestellt.

Wir empfehlen, die Dokumente stapelweise hinzuzufügen, um den Durchsatz zu verbessern. Sie können bis zu 10.000 Dokumente pro Stapel bündeln, ausgehend von einer durchschnittlichen Dokumentgröße von 1-2 KB.

Für die POST-Anforderung wird ein Gesamt-Statuscode zurückgegeben. Dieser Statuscode ist entweder HTTP 200 (Erfolg) oder HTTP 207 (Mehrfachstatus) für Kombinationen aus erfolgreichen und fehlgeschlagenen Dokumenten. Neben dem Statuscode für die POST-Anforderung pflegt Azure Search außerdem ein Statusfeld für jedes Dokument. Für einen stapelweisen Upload benötigen Sie eine Möglichkeit, für jedes einzelne Dokument abzufragen, ob das Dokument erfolgreich eingefügt wurde. Diese Information finden Sie im Statusfeld. Dieses Feld hat den Wert "false", wenn das Dokument nicht geladen werden konnte.

Unter hoher Last sind gelegentliche Fehler beim Upload nicht ungewöhnlich. In diesem Fall erhalten Sie den Gesamtstatus 207 für einen teilweisen Erfolg, und die Statuseigenschaft der fehlgeschlagenen Dokumente hat den Wert "false".

> [WACOM.NOTE] Wenn der Dienst Dokumente empfängt, werden diese in die Warteschlange für die Indexierung platziert und sind möglicherweise nicht sofort in den Suchergebnissen sichtbar. Unter normaler Last werden Dokumente normalerweise innerhalb weniger Sekunden indexiert.

## Schritt 3: Indexabfragen

Nach der Indexierung der Dokumente können Sie Suchabfragen ausführen. Sie können je einen Index auf einmal abfragen und dabei entweder OData oder eine einfache Abfragesyntax verwenden:

-   [OData-Ausdruckssyntax für Azure Search][]
-   [Syntax für einfache Abfragen in Azure Search][]

## Schritt 4: Ändern oder Löschen von Indizes und Dokumenten

Optional können Sie Schemaänderungen am Suchindex vornehmen, Dokumente im Index ändern oder löschen, und Indizes löschen.

Beim Ändern eines Index können Sie mehrere Aktionen (einfügen, zusammenführen, löschen) in einer Stapelausführung kombinieren und so zahlreiche Roundtrips einsparen. Azure Search unterstützt momentan keine teilweisen Updates (HTTP PATCH). Wenn Sie einen Index ändern möchten, müssen Sie daher die Indexdefinition erneut senden.

## Betrachtungen zum Speicherentwurf

Viele Suchanwendungen verwenden unterschiedliche Speicherformate für unterschiedliche Anwendungsanforderungen. Der interne Speicher von Azure muss für die Speicherung von Indizes und Dokumenten verwendet werden. Die Textanalyse hängt davon ab, dass alle durchsuchbaren Felder und zugehörige Attribute frei verfügbar sind.

Nicht alle Felder in einem Dokument sind durchsuchbar. Wenn Ihre Anwendung z. B. einen Onlinekatalog für Musik oder Videos enthält, sollten Sie die Binärdateien nach Möglichkeit in Blobs oder einer anderen Speicherform ablegen. Die Binärdateien selbst sind nicht durchsuchbar und müssen daher nicht im Azure Search-Speicher abgelegt werden. Sie sollten Bilder, Videos und Audiodateien in anderen Diensten oder an anderen Speicherorten ablegen und die URL zum Speicherort in ein Feld im Dokument schreiben.

Weitere Informationen zur Erstellung von Indizes und Dokumenten finden Sie unter [Azure Search REST-API][].

<!--Anchors--> 
<!--Image references--> 
<!--Link references-->

  [Dieser Artikel verwendet das Azure-Vorschauportal]: ../includes/preview-portal-note.md
  [Konfigurieren der Suche im Azure-Vorschauportal]: ../search-configure/
  [Schritt 1: Erstellen des Index]: #sub-1
  [Schritt 2: Hinzufügen von Dokumenten]: #sub-2
  [Schritt 3: Indexabfragen]: #sub-3
  [Schritt 4: Ändern oder Löschen von Indizes und Dokumenten]: #sub-4
  [Betrachtungen zum Speicherentwurf]: #sub-5
  [Index erstellen (Azure Search-API)]: http://msdn.microsoft.com/en-us/library/dn798941.aspx
  [OData-Ausdruckssyntax für Azure Search]: http://msdn.microsoft.com/en-us/library/dn798921.aspx
  [Syntax für einfache Abfragen in Azure Search]: http://msdn.microsoft.com/en-us/library/dn798920.aspx
  [Azure Search REST-API]: http://msdn.microsoft.com/en-us/library/dn798935.aspx
