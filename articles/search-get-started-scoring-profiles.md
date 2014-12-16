<properties title="Get started with scoring profiles in Azure Search" pageTitle="Erste Schritte mit Wertungsprofilen in Azure Search" description="Get started with scoring profiles in Azure Search" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="" ms.date="10/16/2014" ms.author="heidist" />

# Erste Schritte mit Wertungsprofilen in Azure Search

Wertungsprofile sind ein Feature von Microsoft Azure Search, das die Berechnung der Ergebnisse der Suche anpasset und so beeinflusst wie Elemente in einer Liste der Suchergebnisse eingestuft werden. Sie können sich Wertungsprofile als Möglichkeit vorstellen, zum Relevanz zu modellieren, indem Elemente, die vordefinierten Kriterien entsprechen, stärker berücksichtigt werden. Nehmen wir beispielsweise an, dass die Anwendung eine Hotelreservierungswebsite ist. Durch stärkere Berücksichtigung des Felds "Ort", ergeben Suchen, die einen Ausdruck wie "Seattle" enthalten, höhere Werte für Elemente, die "Seattle" im Feld "Ort" enthalten. Beachten Sie, dass Sie mehrere Wertungsprofile verwenden können, oder auch überhaupt keine, wenn die Standardwertung für Ihre Anwendung ausreichend ist.

Um mit Wertungsprofilen zu experimentieren, können Sie eine Beispielanwendung herunterladen, die Wertungsprofile verwendet, um die Rangfolge der Suchergebnisse zu ändern. Das Beispiel ist eine Konsolenanwendung - vielleicht nicht sehr realistisch bezüglich der realen Anwendungsentwicklung, aber trotzdem als Lerntool geeignet. 

Die Beispielanwendung zeigt Wertungsverhaltensweisen anhand fiktiver Daten namens "musicstoreindex". Die Einfachheit der Beispielanwendung erleichtert die Anpassung von Wertungsprofilen und Abfragen, und Sie sehen dann die unmittelbaren Auswirkungen auf die Rangfolge, wenn das Programm ausgeführt wird.

<h2 id="sub-1">Voraussetzungen</h2>

Die Beispielanwendung wird in C# geschrieben, mit Visual Studio 2013. Wenn Sie noch nicht über Visual Studio verfügen, versuchen Sie es der kostenlosen [Visual Studio 2013 Express Edition](http://www.visualstudio.com/de-de/products/visual-studio-express-vs.aspx).

Sie benötigen ein Azure-Abonnement und einen Azure Search-Dienst zum Abschließen dieses Lernprogramms. Unter [Erste Schritte mit Azure Search](../search-get-started/) erhalten Sie nach Hilfe beim Einrichten des Diensts.

[WACOM.INCLUDE [Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können.:](../includes/free-trial-note.md)]

<h2 id="sub-2">Herunterladen der Beispielanwendung</h2>
Wechseln Sie zu [Azure Search Scoring Profiles Demo](https://azuresearchscoringprofiles.codeplex.com/) auf Codeplex, um die in diesem Lernprogramm beschriebene Beispielanwendung herunterzuladen.

Klicken Sie auf der Registerkarte "Source Code" (Quellcode) auf **Download**, um eine ZIP-Datei der Lösung herunterzuladen. 

 ![][12]

<h2 id="sub-3">Bearbeiten von "app.config"</h2>

1. Nachdem Sie die Dateien extrahiert haben, öffnen Sie die Projektmappe in Visual Studio, um die Konfigurationsdatei zu bearbeiten.
1. Doppelklicken Sie im Projektmappen-Explorer auf **app.config**. Diese Datei gibt den Dienstendpunkt und einen API-Schlüssel an, die zur Authentifizierung der Anforderung verwendet werden. Diese Werte erhalten Sie im Verwaltungsportal.
1. Melden Sie sich auf dem [Azure-Vorschauportal](https://portal.azure.com) an.
1. Rufen Sie das Dienst-Dashboard für Azure Search auf.
1. Klicken Sie auf die Kachel **Eigenschaften**, um die Dienst-URL zu kopieren.
1. Klicken Sie auf die Kachel **Schlüssel**, um den API-Schlüssel zu kopieren.

Wenn Sie die URL und den API-Schlüssel zu "app.config" hinzugefügt haben, sollten die Anwendungseinstellungen wie folgt aussehen:

   ![][11]


<h2 id="sub-4">Erkunden der Anwendung</h2>

Sie sind beinahe bereit, die Anwendung zu erstellen und auszuführen, aber bevor Sie dies tun, sehen Sie sich die JSON-Dateien an, die zum Erstellen und Auffüllen des Indexes verwendet werden.

**Schema.json** definiert den Index, einschließlich Wertungsprofile, die in dieser Demo hervorgehoben werden. Beachten Sie, dass das Schema alle Felder im Index definiert, einschließlich nicht durchsuchbarer Felder, wie z. B. "margin", die Sie in einem Wertungsprofil verwenden können. Die Wertungsprofilsyntax ist in [Hinzufügen eines Wertungsprofils zu einem Azure Search-Index](http://msdn.microsoft.com/de-de/library/azure/dn798928.aspx) dokumentiert.

**Data1-3.json** enthält die Daten: 246 Alben aus einigen Genres. Die Daten sind eine Kombination aus tatsächliche Alben und Interpreteninformationen mit fiktiven Feldern wie "price" und "margin", die verwendet werden, um Suchvorgänge zu veranschaulichen. Die Datendateien entsprechen dem Index und werden in Ihren Azure Search-Dienst hochgeladen. Nachdem die Daten hochgeladen und indiziert sind, können Sie Abfragen für diese ausgeben.

**Program.cs** führt die folgenden Vorgänge aus:

- Ein Konsolenfenster wird geöffnet.

- Eine Verbindung mit Azure Search wird mithilfe der Dienst-URL und des API-Schlüssels hergestellt.

- Falls vorhanden, wird "musicstoreindex" gelöscht.

- Eine neue Datei "musicstoreindex" mithilfe der Datei "schema.json" erstellt.

- Der Index wird mit den Datendateien gefüllt.

- Der Index wird mit vier Abfragen abgefragt. Beachten Sie, dass die Wertungsprofile als Abfrageparameter angegeben sind. Alle Abfragen suchen nach dem gleichen Begriff: "best". Die erste Abfrage zeigt die Standardwertung an. Die verbleibenden drei Abfragen verwenden ein Wertungsprofil.

<h2 id="sub-5">Erstellen und Ausführen der Anwendung</h2>

Um Probleme mit Konnektivität oder Assemblyverweisen auszuschließen, erstellen und führen Sie die Anwendung aus, um sicherzustellen, dass keine Probleme bestehen, die zuerst behandelt werden müssen. Eine Konsolenanwendung sollte im Hintergrund geöffnet sein. Alle vier Abfragen werden ohne Unterbrechung nacheinander ausgeführt. Auf vielen Systemen wird das gesamte Programm in weniger als 15 Sekunden ausgeführt. Enthält die Konsolenanwendung eine Meldung wie die folgende: "Abgeschlossen. EINGABETASTE zum Fortfahren drücken", wurde das Programm erfolgreich abgeschlossen. 

Zum Vergleichen der Abfragen können Sie die Ergebnisse der Abfrage in der Konsole markieren, kopieren und in eine Excel-Datei einfügen. 

Die folgende Abbildung zeigt die Ergebnisse der ersten drei Abfragen nebeneinander. Alle Abfragen verwenden den gleichen Suchbegriff "best", der in zahlreichen Albumtiteln enthalten ist.

   ![][10]

Die erste Abfrage verwendet die Standardwertung. Da der Suchbegriff nur in Albumtiteln enthalten ist und keine anderen Kriterien angegeben sind, werden Artikel mit "best" im Albumtitel in der Reihenfolge zurückgegeben, in der sie vom Search-Dienst gefunden werden. 

Die zweite Abfrage verwendet ein Wertungsprofil, aber beachten Sie, dass das Profil keine Auswirkungen hat. Die Ergebnisse sind identisch mit denen der ersten Abfrage. Dies liegt daran, dass ein Wertungsprofil ein Feld ("genre" ") stärker berücksichtigt, das nicht von Belang für die Abfrage ist. Der Suchbegriff "best" existiert nicht in einem beliebigen Feld "genre" "für jedes Dokument. Wenn ein Wertungsprofil keine Auswirkungen hat, sind die Ergebnisse identisch mit der Standardwertung.  

Die dritte Abfrage ist der erste Beweis für die Auswirkungen des Wertungsprofils. Der Suchbegriff ist weiterhin "best", daher arbeiten wir mit dem gleichen Satz von Alben, aber da das Wertungsprofil zusätzliche Kriterien bietet, welche die Felder "rating" und "last-updated" stärker berücksichtigt, steigen einige Elemente höher in der Liste.

Die nächste Abbildung zeigt die vierte und letzte Abfrage, mit Verstärkung von "margin". Das Feld "margin" kann nicht durchsucht werden und nicht in den Suchergebnissen zurückgegeben werden. Der Werte "margin" wurde in der Kalkulationstabelle manuell hinzugefügt, um zu verdeutlichen, dass Elemente mit höheren Margen weiter oben in der Liste der Suchergebnisse angezeigt werden. 

   ![][9]

Nun, da Sie sich mit den Wertungsprofilen vertraut gemacht haben, ändern Sie das Programm so, dass es andere Abfragesyntaxarten, Wertungsprofile oder umfangreichere Daten verwendet. Über die Links im nächsten Abschnitt erhalten Sie weitere Informationen.

<h2 id="next-steps">Nächste Schritte</h2>

Erhalten Sie weitere Informationen zu Wertungsprofilen. Details finden Sie unter [Hinzufügen eines Wertungsprofils zu einem Azure Search-Index](http://msdn.microsoft.com/de-de/library/azure/dn798928.aspx).

Erhalten Sie weitere Informationen zu Syntax und Abfrageparametern. Details finden Sie unter [Suchen von Dokumenten (Azure Search-REST-API)](http://msdn.microsoft.com/de-de/library/azure/dn798927.aspx).

 Sie möchten einen Schritt zurückgehen und mehr über die Erstellung eines Indexes erfahren? [Sehen Sie sich dieses Video an](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh), um die Grundlagen zu erlernen.

<!--Anchors-->
[Voraussetzungen]: #sub-1
[Herunterladen der Beispielanwendung]: #sub-2
[Bearbeiten von "app.config"]: #sub-3
[Erkunden der Anwendung]: #sub-4
[Erstellen und Ausführen der Anwendung]: #sub-5
[Nächste Schritte]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG
