<properties 
	pageTitle="Erstellen einer Prototypenanwendung für Azure Search" 
	description="Erstellen Sie Ihren ersten Anwendungsprototypen, um sich mit Azure Search vertraut zu machen." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/08/2015" 
	ms.author="heidist"/>

# Erstellen einer Prototypenanwendung für Azure Search

Entwickler, die sich mit Azure Search beschäftigen, beginnen in der Regel mit einer vorläufigen Testanwendung, die den Nutzen von Azure Search für eine benutzerdefinierte Anwendung veranschaulicht. In diesem Artikel stellen wir Ihnen einige Bausteine zur Verfügung, um Prototypenerstellung zu beschleunigen:
 
- Ein Visual Studio-Projekt in C# mit data.json- und schema.json-Dateien. Mithilfe der Beispieldaten können Sie die Projektmappe umgehend erstellen, ausführen und sich vergewissern, dass die Projektmappe auf Ihrem System funktioniert, bevor Sie auch nur eine Zeile Code schreiben. 

	Versuchen Sie nach Möglichkeit, diese eigenständigen, unzusammenhängenden Schema- und Datendateien durch Daten aus Ihrem Unternehmen zu ersetzen. Dadurch basiert der Index, der beim Ausführen der Anwendung erstellt wird, auf Ihrem Schema, und in Azure Search stehen umgehend Dokumente zur Verfügung, die auf Ihren Datendateien basieren.

- Leitfaden zur Strukturierung der Daten, die Sie an Azure Search hochladen. Pro Index wird jeweils ein Dataset benötigt. Azure Search verwendet JSON für die Datenserialisierung.

- Und schließlich gehen wir noch auf einige zusätzliche Features ein, mit denen Sie Ihren Prototyp weiter verbessern können. Hierzu binden wir suchspezifische Features wie Facettennavigation, Bewertungsprofile für die Ergebnisoptimierung, Abfragen mit automatischer Vervollständigung und suchseitenbezogene Funktionen ein, die bei einer reinen Volltextsuche nicht zur Verfügung stehen.

Am Ende verfügen Sie über eine Prototypenanwendung, die mithilfe von Azure Search zuverlässige Suchfunktionen auf der Grundlage Ihrer Daten bereitstellt.

## Vorbereiten der Daten

Suchvorgänge basieren auf einem für Azure Search erstellten Suchindex. Ein Index besteht aus mehreren Komponenten (beispielsweise Bewertungsprofile und Vorschläge), den Großteil machen jedoch die Datenfelder aus, die bei Suchvorgängen verwendet werden.

Bei dem Dataset mit den Feldern muss es sich um eine Daten-Flatfile (im JSON-Format) handeln, und jedes Feld im Dataset muss einem entsprechenden Feld in dem Index zugeordnet sein, den Sie auffüllen möchten. Andere Komponenten des Index (wie etwa ein Bewertungsprofil oder CORS-Optionen) werden dem Schema unabhängig und direkt hinzugefügt.

Datenquellen mit nativer JSON-Verwendung (wie etwa Azure-Tabellenspeicher) stellen Daten bereit, die sich einfacher verarbeiten lassen als relationale Daten. Sie benötigen eine einzelne Sicht oder Tabelle mit allen Feldern in einem Rowset.

Zum Hochladen von Daten- und Schemadateien für den Code des Prototypenbeispiels können Sie die standardmäßigen, in die Projektmappe eingebetteten Schema- und Datendateien überschreiben.

> [AZURE.NOTE]Sie können mehrere Datendateien laden, um Daten ggf. stapelweise hochzuladen, die Struktur der Daten muss jedoch bei jeder Datendatei, die Sie an den gleichen Index hochladen, identisch sein. Indizes können nicht zusammengeführt werden. Jeder Index fungiert als eigenständiger Suchkorpus.

## Testen der Prototypenprojektmappe auf Ihrem System

1. [Erstellen Sie einen Azure Search-Dienst im Azure-Portal](search-create-service-portal.md).

    Jedem bereits vorhandenen Azure-Abonnement kann eine gemeinsam genutzte (kostenlose) Version des Diensts hinzugefügt werden. Der gemeinsam genutzte Dienst wird häufig für Prototypen verwendet. Beachten Sie, dass der gemeinsam genutzte Dienst auf 50 MB Daten oder auf insgesamt 10.000 Dokumente beschränkt ist (je nachdem, was zuerst eintritt). Darüber hinaus können Daten und Dokumente auf maximal drei Indizes verteilt werden.

    Wenn Sie die Prototypenbeispielanwendung mit den integrierten Beispieldatendateien ausführen, wird für Ihren Azure Search-Dienst ein Index namens „musicstoreindex“ mit 246 Dokumenten (278 KB) erstellt. Im weiteren Verlauf dieser exemplarischen Vorgehensweise wird dieser Index durch einen neuen Index mit Adventure Works-Daten ersetzt, die bis zu ## Dokumente und ## MB beanspruchen.

2. [Laden Sie die Projektmappe für die Prototypenerstellung herunter](http://go.microsoft.com/fwlink/p/?LinkId=536479). Hierbei handelt es sich um eine Visual Studio 2013-Projektmappe in C#, die eine Konsolenanwendung zum Erstellen, Laden und Abfragen eines Index erstellt. Wenn Sie nicht über Visual Studio verfügen, können Sie [hier](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) die kostenlose Visual Studio 2013 Express-Edition herunterladen.

3. Fügen Sie in „app.config“ Konfigurationseinstellungen für Ihren Search-Dienst und den API-Schlüssel hinzu.

	Die URL und den API-Administratorschlüssel finden Sie auf dem [Service-Dashboard im Portal](search-create-service-portal.md). Geben Sie für die URL den vollständigen Pfad des Dienstnamens einschließlich HTTPS-Präfix und Domäne (`search.windows.net`) ein.

4. Erstellen Sie die Projektmappe, und vergewissern Sie sich, dass keine Buildfehler aufgetreten sind. Zur Behebung von Buildfehlern ist unter Umständen eine Aktualisierung der Pakete erforderlich. Klicken Sie mit der rechten Maustaste auf **NuGet-Pakete verwalten** für die Projektmappe, um die Pakete zu aktualisieren.

5. Führen Sie die Projektmappe unter Verwendung der integrierten Schema- und Datendateien aus. Mit diesem optionalen Schritt können Sie sich vor dem Hinzufügen eigener Daten vergewissern, dass die Projektmappe funktioniert. Die Konsole gibt folgende Meldungen aus:

	- Index deleted (Erscheint nur, wenn ein Index namens „musicstoreindex“ vorhanden ist.)
	- Index created (In Ihrem Dienst wurde ein neuer Index namens „musicstoreindex“ erstellt.)
	- Documents posted (Pro JSON-Datei erscheint jeweils eine Meldung.)
	- Waiting 5 seconds (Ermöglicht die Fertigstellung der Indizierung vor der ersten Abfrage.)
	- Search Results for search term 'best' with no boosting (Führt eine einfache Abfrage aus, um zu belegen, dass die Daten im Index geladen wurden.)

6. Beenden Sie die Anwendung, und löschen Sie den Index, um Platz für Ihren eigenen Index freizugeben.

    > [AZURE.TIP]Der Index kann über [das Portal](https://portal.azure.com) gelöscht werden. Klicken Sie zum Öffnen des Indexblatts auf den Namen des Index, und verwenden Sie anschließend den Befehl **Löschen**.

## Ersetzen der Schema- und Datendateien (JSON-Dateien) durch Ihre Daten

Die Prototypenprojektmappe enthält eine Schemadatei und drei Datendateien: „data1.json“, „data2.json“ und „data3.json“. Die Schemadatei (schema.json) muss durch ein Schema ersetzt werden, das Ihre Daten beschreibt.

Die entsprechenden Dateien befinden sich standardmäßig im Projektmappenordner:

![][1]

Wenn Sie Ihre Daten in JSON-Dateien speichern können, können Sie die vorhandenen Dateien mit Ihren Daten überschreiben und anschließend die Anwendung ausführen, um einen Index zu erstellen und zu laden. Durch die Aufteilung der Daten auf mehrere Dateien lässt sich ein Batch-Uploadvorgang veranschaulichen.

Daten können unter anderem auch mit einem Indexer geladen werden. Hierzu wird entweder eine Azure DocumentDB-Datenquelle oder eine Azure SQL-Datenbank-Datenquelle benötigt. Beispielcode für weitere Methoden zum Laden von Daten finden Sie bei MSDN in der [Liste mit Videos und Lernprogrammen für Azure Search](search-video-demo-tutorial-list.md).

### Bearbeiten der Abfrage

Die in die Projektmappe integrierte Standardabfrage verweist auf Felder und Strukturen der integrierten Datendateien. Nachdem Sie die Datendateien mit Ihren Daten überschrieben haben, können Sie den Abfragecode entweder auskommentieren oder für die Verwendung von Feldern aus Ihrem Schema anpassen. Weitere Informationen zur Suchabfragesyntax finden Sie unter [Dokumente durchsuchen](https://msdn.microsoft.com/library/azure/dn798927.aspx).

### Erstellen und Ausführen der Anwendung

Drücken Sie **F5**, um die App auszuführen. Auch diesmal erscheinen Meldungen, die darüber informieren, dass der Index erstellt und geladen wurde und abfragebereit ist.

Sie verfügen nun über einen einsatzbereiten Index, den Sie als Grundlage für weitere Untersuchungen verwenden können.

An diesem Punkt empfiehlt es sich unter Umständen, zur Ausführung von Abfragen zu [Fiddler](search-fiddler.md) oder [Chrome Postman](search-chrome-postman.md) zu wechseln, eine der Beispielanwendungen für die Verwendung Ihres Search-Diensts und -Index zu modifizieren oder benutzerdefinierten Code für die Datenvisualisierung hinzuzufügen.

## Hinzufügen eines Bewertungsprofils

Wir empfehlen, mit [Bewertungsprofilen](search-get-started-scoring-profiles.md) entweder über das Portal oder über den Code zu experimentieren. Bewertungsprofile steigern die Relevanz eines Suchbegriffs, wenn dieser in einem bestimmten Feld gefunden wird. Ein Beispiel: Handelt es sich bei dem Suchbegriff um einen Ortsnamen, kann davon ausgegangen werden, dass Dokumente, bei denen sich *Seattle* im Feld „Ort“ befindet, eine höhere Relevanz besitzen als Dokumente, bei denen *Seattle* in einem Beschreibungsfeld vorkommt.

Wenn Sie ein Bewertungsprofil hinzufügen, ändert sich dadurch der Index. Nach jeder Änderung des Schemas muss der Index neu erstellt und erneut geladen werden. Versehen Sie Ihren Prototypen daher ggf. mit Bewertungsprofil-Codesegmenten (oder passen Sie Bewertungsprofilbeispiele für die Verwendung Ihres Indizierungscodes an).

Die Referenzdokumentation für Bewertungsprofile finden Sie unter [Hinzufügen eines Bewertungsprofils](https://msdn.microsoft.com/library/dn798928.aspx).

## Hinzufügen von Vorschlägen

Mit Vorschlägen ist das beliebte Suchfeature gemeint, das auf der Grundlage der Benutzereingabe eine Liste mit potenziellen Suchbegriffen anzeigt. (Beispiel: Wenn der Benutzer „Wet“ eingibt, erscheint eine Liste mit automatisch vervollständigten Suchbegriffen für „Wetter“, „Wetterkarte“, „Wettervorhersage“ usw.).

Wenn Sie Vorschläge hinzufügen möchten, fügen Sie dem Indexschema einen Abschnitt mit den Feldern hinzu, die für die Erstellung automatisch vervollständigter Abfragen verwendet werden sollen. Hierfür eignen sich am besten Felder mit Namen sowie kürzere, nicht repetitive Zeichenfolgen. Weitere Informationen finden Sie unter [Index erstellen](https://msdn.microsoft.com/library/dn798928.aspx).

## Ausprobieren einer Sprachanalyse

Sprachanalysen stellen die linguistischen Regeln für die Unterscheidung zwischen wichtigen und unwichtigen Wörtern, Stammformen und sogar Synonymen bereit. Azure Search verwendet standardmäßig die Lucene-Sprachanalyse für Englisch. Verschiedene Analysen können als Indexattribut für bestimmte Felder angegeben werden. Dies ermöglicht die Erstellung von Schemas und Dokumenten, die Felder mit unterschiedlichen Analysen enthalten. So lassen sich beispielsweise in einem Dokument einer mehrsprachigen Anwendung gleichzeitig Felder für Französisch und Englisch verwenden. Weitere Informationen finden Sie unter [Sprachunterstützung](https://msdn.microsoft.com/library/dn879793.aspx).

## Nächste Schritte

In den vorherigen Abschnitten haben Sie den Index modifiziert, um den Funktionsumfang Ihres Prototypen zu erweitern. An diesem Punkt sind Ihre Indexänderungen größtenteils abgeschlossen. (Die letzte mögliche Schemaänderung ist die Aktivierung von CORS, die hier allerdings nicht behandelt wurde).

Wenn Sie sich weiter mit Prototypen beschäftigen möchten, haben Sie grundsätzlich zwei Möglichkeiten: Sie können sich der Benutzeroberfläche zuwenden und beispielsweise eine Facettennavigation oder andere Filter zur Eingrenzung der Suchergebnisse hinzufügen oder sich weiter mit der Datensynchronisierung Ihrer Projektmappe befassen. Viele Projektmappen enthalten flüchtige Daten. Nach der Überprüfung des grundlegenden Suchverhaltens wenden sich viele Entwickler daher der Synchronisierung von Datenaktualisierungen zwischen Transaktionsdatenbanksystemen und einem Azure Search-Index zu.

Weitere Informationen finden Sie unter folgenden Links:

- [Typische Workflows für Entwicklungsprojekte mit Azure Search](search-workflow.md)

- [Anpassen von Azure Search-Indexern](search-indexers-customization.md)

- [Facettennavigation in Azure Search](search-faceted-navigation.md)

- [Anordnen von Suchergebnissen auf Seiten in Azure Search](search-pagination-page-layout.md)


<!--Image references-->
[1]: ./media/search-build-prototype/azsearch-datafiles.png
 

<!---HONumber=Sept15_HO2-->