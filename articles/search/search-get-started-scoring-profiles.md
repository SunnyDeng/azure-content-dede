<properties 
	pageTitle="Verwenden von Bewertungsprofilen in Azure Search" 
	description="Erste Schritte mit Wertungsprofilen in Azure Search" 
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
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Verwenden von Bewertungsprofilen in Azure Search

Wertungsprofile sind ein Feature von Microsoft Azure Search, das die Berechnung der Ergebnisse der Suche anpasset und so beeinflusst wie Elemente in einer Liste der Suchergebnisse eingestuft werden. Sie können sich Wertungsprofile als Möglichkeit vorstellen, zum Relevanz zu modellieren, indem Elemente, die vordefinierten Kriterien entsprechen, stärker berücksichtigt werden. Nehmen wir beispielsweise an, dass die Anwendung eine Hotelreservierungswebsite ist. Wenn das Feld `location` stärker berücksichtigt wird, dann werden in Suchläufen nach einem Suchbegriff wie Seattle Elemente, die Seattle im Feld `location` enthalten, mit einer höheren Relevanz bewertet. Beachten Sie, dass Sie mehrere Wertungsprofile verwenden können, oder auch überhaupt keine, wenn die Standardwertung für Ihre Anwendung ausreichend ist.

Um mit Wertungsprofilen zu experimentieren, können Sie eine Beispielanwendung herunterladen, die Wertungsprofile verwendet, um die Rangfolge der Suchergebnisse zu ändern. Das Beispiel ist eine Konsolenanwendung – vielleicht nicht sehr realistisch bezüglich der realen Anwendungsentwicklung, aber trotzdem als Lerntool geeignet.

Die Beispielanwendung veranschaulicht Bewertungsverhalten anhand fiktiver Daten, die als `musicstoreindex` bezeichnet werden. Die Einfachheit der Beispielanwendung erleichtert die Anpassung von Wertungsprofilen und Abfragen, und Sie sehen dann die unmittelbaren Auswirkungen auf die Rangfolge, wenn das Programm ausgeführt wird.

<a id="sub-1"></a>
## Voraussetzungen

Die Beispielanwendung wird mit Visual Studio 2013 in C\# geschrieben. Wenn Sie nicht über Visual Studio verfügen, können Sie die [kostenlose Visual Studio 2013 Express-Edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) ausprobieren.

Sie benötigen ein Azure-Abonnement und einen Azure Search-Dienst zum Abschließen dieses Lernprogramms. Hilfe beim Einrichten des Diensts finden Sie unter [Erstellen eines Search-Diensts im Portal](search-create-service-portal.md).

[AZURE.INCLUDE [Sie benötigen ein Azure-Konto, um dieses Lernprogramm durchführen zu können:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Herunterladen der Beispielanwendung

Wechseln Sie zu [Azure Search Scoring Profiles Demo](https://azuresearchscoringprofiles.codeplex.com/) auf Codeplex, um die in diesem Lernprogramm beschriebene Beispielanwendung herunterzuladen.

Klicken Sie auf der Registerkarte "Source Code" \(Quellcode\) auf **Download**, um eine ZIP-Datei der Lösung herunterzuladen.

 ![][12]

<a id="sub-3"></a>
## Bearbeiten von "app.config"

1. Nachdem Sie die Dateien extrahiert haben, öffnen Sie die Projektmappe in Visual Studio, um die Konfigurationsdatei zu bearbeiten.
1. Doppelklicken Sie im Projektmappen-Explorer auf **app.config**. Diese Datei gibt den Dienstendpunkt und einen `api-key` an, die zur Authentifizierung der Anforderung verwendet werden. Diese Werte erhalten Sie im Verwaltungsportal.
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Rufen Sie das Dienst-Dashboard für Azure Search auf.
1. Klicken Sie auf die Kachel **Eigenschaften**, um die Dienst-URL zu kopieren.
1. Klicken Sie auf die Kachel **Schlüssel**, um den `api-key` zu kopieren.

Wenn Sie URL und `api-key` zu "app.config" hinzugefügt haben, sollten die Anwendungseinstellungen wie folgt aussehen:

   ![][11]


<a id="sub-4"></a>
## Erkunden der Anwendung

Sie sind beinahe bereit, die Anwendung zu erstellen und auszuführen, aber bevor Sie dies tun, sehen Sie sich die JSON-Dateien an, die zum Erstellen und Auffüllen des Indexes verwendet werden.

**Schema.json** definiert den Index, einschließlich der Bewertungsprofile, die in dieser Demo hervorgehoben werden. Beachten Sie, dass das Schema alle im Index verwendeten Felder definiert, einschließlich nicht durchsuchbarer Felder, wie z. B. `margin`, die Sie in einem Bewertungsprofil verwenden können. Die Bewertungsprofilsyntax ist in [Hinzufügen eines Bewertungsprofils zu einem Azure Search-Index](http://msdn.microsoft.com/library/azure/dn798928.aspx) dokumentiert.

**Data1-3.json** enthält die Daten: 246 Alben aus einigen Genres. Die Daten sind eine Kombination aus tatsächliche Alben und Interpreteninformationen mit fiktiven Feldern wie `price` und `margin`, die zur Veranschaulichung von Suchvorgängen dienen. Die Datendateien entsprechen dem Index und werden in Ihren Azure Search-Dienst hochgeladen. Nachdem die Daten hochgeladen und indiziert sind, können Sie Abfragen für diese ausgeben.

**Program.cs** führt die folgenden Vorgänge aus:

- Ein Konsolenfenster wird geöffnet.

- Es wird mithilfe der Dienst-URL und des `api-key` eine Verbindung mit Azure Search hergestellt.

- `musicstoreindex` wird gelöscht, sofern vorhanden.

- Mithilfe der Datei "schema.json" wird ein neuer `musicstoreindex` erstellt.

- Der Index wird mit den Datendateien gefüllt.

- Der Index wird mit vier Abfragen abgefragt. Beachten Sie, dass die Wertungsprofile als Abfrageparameter angegeben sind. Alle Abfragen suchen nach dem gleichen Begriff: "best". Die erste Abfrage zeigt die Standardwertung an. Die verbleibenden drei Abfragen verwenden ein Wertungsprofil.

<a id="sub-5"></a>
## Erstellen und Ausführen der Anwendung

Um Probleme mit Konnektivität oder Assemblyverweisen auszuschließen, erstellen und führen Sie die Anwendung aus, um sicherzustellen, dass keine Probleme bestehen, die zuerst behandelt werden müssen. Eine Konsolenanwendung sollte im Hintergrund geöffnet sein. Alle vier Abfragen werden ohne Unterbrechung nacheinander ausgeführt. Auf vielen Systemen wird das gesamte Programm in weniger als 15 Sekunden ausgeführt. Enthält die Konsolenanwendung eine Meldung wie die folgende: "Abgeschlossen. EINGABETASTE zum Fortfahren drücken", wurde das Programm erfolgreich abgeschlossen.

Zum Vergleichen der Abfragen können Sie die Ergebnisse der Abfrage in der Konsole markieren, kopieren und in eine Excel-Datei einfügen.

Die folgende Abbildung zeigt die Ergebnisse der ersten drei Abfragen nebeneinander. Alle Abfragen verwenden den gleichen Suchbegriff "best", der in zahlreichen Albumtiteln enthalten ist.

   ![][10]

Die erste Abfrage verwendet die Standardwertung. Da der Suchbegriff nur in Albumtiteln enthalten ist und keine anderen Kriterien angegeben sind, werden Artikel mit "best" im Albumtitel in der Reihenfolge zurückgegeben, in der sie vom Search-Dienst gefunden werden.

Die zweite Abfrage verwendet ein Wertungsprofil, aber beachten Sie, dass das Profil keine Auswirkungen hat. Die Ergebnisse sind identisch mit denen der ersten Abfrage. Dies liegt daran, dass ein Wertungsprofil ein Feld \("genre" "\) stärker berücksichtigt, das nicht von Belang für die Abfrage ist. Der Suchbegriff "best" existiert nicht in einem beliebigen Feld "genre" "für jedes Dokument. Wenn ein Wertungsprofil keine Auswirkungen hat, sind die Ergebnisse identisch mit der Standardwertung.

Die dritte Abfrage ist der erste Beweis für die Auswirkungen des Wertungsprofils. Der Suchbegriff ist weiterhin "best", daher arbeiten wir mit dem gleichen Satz von Alben, aber da das Wertungsprofil zusätzliche Kriterien bietet, welche die Felder "rating" und "last-updated" stärker berücksichtigt, steigen einige Elemente höher in der Liste.

Die nächste Abbildung zeigt die vierte und letzte Abfrage, mit Verstärkung von "margin". Das Feld "margin" kann nicht durchsucht werden und nicht in den Suchergebnissen zurückgegeben werden. Der Werte "margin" wurde in der Kalkulationstabelle manuell hinzugefügt, um zu verdeutlichen, dass Elemente mit höheren Margen weiter oben in der Liste der Suchergebnisse angezeigt werden.

   ![][9]

Nun, da Sie sich mit den Wertungsprofilen vertraut gemacht haben, ändern Sie das Programm so, dass es andere Abfragesyntaxarten, Wertungsprofile oder umfangreichere Daten verwendet. Über die Links im nächsten Abschnitt erhalten Sie weitere Informationen.

<a id="next-steps"></a>
## Nächste Schritte

Erhalten Sie weitere Informationen zu Wertungsprofilen. Details finden Sie unter [Hinzufügen eines Bewertungsprofils zu einem Azure Search-Index](http://msdn.microsoft.com/library/azure/dn798928.aspx).

Erhalten Sie weitere Informationen zu Syntax und Abfrageparametern. Details finden Sie unter [Suchen von Dokumenten \(REST-API für den Azure Search-Dienst\)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Sie möchten einen Schritt zurückgehen und mehr über die Erstellung eines Indexes erfahren? [Sehen Sie sich dieses Video an](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh), um die Grundlagen zu erlernen.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG

<!---HONumber=July15_HO5-->