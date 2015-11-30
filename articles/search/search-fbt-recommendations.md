<properties pageTitle="Azure Search – Empfehlungen im Bereich „Häufig zusammen gekauft“ | Microsoft Azure | Apache Mahout | Azure Machine Learning" description="Beispielcode zum Hinzufügen von Empfehlungen in den Bereichen „Häufig zusammen gekauft“, „Neue Angebote“ oder „Auch gekauft“ zu einer Azure Search-Anwendung mit Apache Mahout oder Azure Machine Learning" services="search" documentationCenter="" authors="liamca" manager="pablocas" editor="" tags="machine learning"/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="11/12/2015"
   ms.author="liamca"/>

# Azure Search – Empfehlungen im Bereich „Häufig zusammen gekauft“

Wenn Sie bereits online eingekauft haben, haben Sie schon Listen wie „Häufig zusammen gekauft“ oder „Kunden, die diesen Artikel gekauft haben, haben auch gekauft“ oder sogar „Neue Angebote für Sie“ bemerkt, in denen möglicherweise passende zusätzliche Waren oder Dienstleistungen für Sie dargestellt werden. Im Allgemeinen werden diese Daten von Onlinehändlern basierend auf dem Einkaufsverlauf vieler Kunden oder nur auf Ihren ganz speziellen Einstellungen kompiliert. Die Möglichkeit, relevante, auf Verbraucher abgestimmte Empfehlungen über die Suche anzubieten, ist eine leistungsfähige Ergänzung nicht nur für kommerzielle Anwendungen, sondern auch für Websites, die Multimediainhalte oder Informationen bereitstellen.

In Azure Search-Anwendungen können Sie Empfehlungen implementieren, indem Sie zusätzlichen Code schreiben, der Daten und Dienste integriert, die zur Identifizierung von Mustern und Beziehungen in den Daten verwendet werden, und diese dann in Empfehlungen umwandelt, die für Ihre Benutzer relevant sind.

[Dieses Beispiel zeigt](https://github.com/liamca/azure-search-recommendations), wie [Apache Mahout]() für das Empfehlungsmodul verwendet wird.

Im Rest dieses Artikels werde ich den Beispielcode erläutern, mit dem Empfehlungen für „Häufig zusammen ausgeliehen“ hinzugefügt werden. Dazu werden Beispieldaten über den Filmverleih verwendet.

![1](./media/search-fbt-recommendations/product_recommendations.png)

## Was ist eine Empfehlung?

*Empfehlungen* sind eine Technik, mehr Artikel aus einem Katalog anzuzeigen. Dazu wird die vorhandene Aktivität des suchenden Benutzers (z. B. Webprotokolle) verwendet, um Artikel zu empfehlen und den Abschluss zu verbessern.

Empfehlungsmodule werden häufig mit bisherigen Kundenaktivitäten oder durch das Sammeln von Daten direkt von einem digitalen Store trainiert. In diesem Beispiel verwende ich Apache Mahout, um Empfehlungsdaten zu kompilieren.

Beispiele für Empfehlungen: – Häufig zusammen gekauft: Ein Kunde, der dies gekauft hat, hat auch das gekauft. – Empfehlungen anhand von Kunden und Artikeln: Ein Kunde wie Sie, hat auch dies gekauft.

## Erstellen eines Azure Search-Index

- Öffnen Sie die Projektmappe „AzureSearchMovieRecommendations.sln“, und legen Sie „ImportAzureSearchIndexData“ als Standardprojekt fest.  
- Öffnen Sie „Program.cs“ in „ImportAzureSearchIndexData“, und ändern Sie „SearchServiceName“ und „SearchApiKey“ so, dass sie auf Ihren Azure Search-Dienst verweisen.
- Laden Sie „hetrec2011-movielens-2k.zip“ von http://grouplens.org/datasets/hetrec-2011/ herunter, und kopieren Sie die Dateien „Movies.dat“ und „user\_ratedmovies.dat“ nach „\\ImportAzureSearchIndexData\\data“.
- Führen Sie das Projekt aus, um einen Index zu erstellen und die Filmdaten zu laden. 
- Zum Abschluss führt die Anwendung eine Testsuche durch.

## Erstellen einer einfachen HTML-Anwendung zum Suchen von Filmen

Sie finden eine fertige JavaScript-Webanwendung, die es Ihnen ermöglicht, den Azure Search-Index abzufragen, unter: \\WebSite\\starter-template-complete

Wenn Sie die Demo komplett durchgehen möchten, finden Sie die ursprüngliche CSS hier: \\WebSite\\starter-template

Öffnen Sie die Datei „search.js“ in „\\WebSite\\starter-template-complete“, und aktualisieren Sie „apiKey“ und „azureSearchService“ mit Ihren Azure Search-Dienstdetails.

Sie sollten diese Datei in einem Browser wie beispielsweise Chrome öffnen können, um jetzt Filme anzuzeigen, indem Sie Text in das Suchfeld eingeben.

## Befehl zum Ausführen der Erstellung von Empfehlungen mit Mahout

- Laden Sie die Datei „data\\movie\_usage.txt“ in Azure Blob Storage hoch. 
- Erstellen Sie eine HDInsight-Instanz (aktivieren Sie Remotedesktop), und stellen Sie eine Verbindung mit dem Computer über Remotedesktop her (im Azure-Portal verfügbar).
- Öffnen Sie auf dem HDInsight-Computer die „Hadoop-Befehlszeile“.
- Ändern Sie das bin-Verzeichnis von Mahout unter „C:\\apps\\dist“. Bei mir sieht es wie folgt aus, aber Sie können eine neuere Version von Mahout verwenden: C:\\apps\\dist\\mahout-1.0.0.2.3.3.0-2992\\bin
- Führen Sie die folgende Befehlszeile aus, und ersetzen Sie dabei [CONTAINER] und [STORAGEACT] durch Ihre Azure Storage-Details (wo Sie die Datei „movie\_usage.txt“ platziert haben):

    mahout itemsimilarity -s SIMILARITY\_COSINE --input "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/movie\_usage.txt" --output "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/output/" --tempDir "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/temp" -m 5

Die Verarbeitung sollte ein paar Minuten dauern. Aber wenn sie abgeschlossen ist, sollte der Speichercontainer die folgende Datei mit Ihren Filmempfehlungen enthalten: /movies/output/part-r-00000

Diese Datei enthält drei Spalten: [Item ID of Movie], [Item ID of Recommendations related to this Movie], [Similarity Percentage]

## Importieren von Daten aus Mahout in Azure Search

Das Programm, das den Azure Search-Index erstellt hat, hat auch ein Feld namens `Recommendations` mit dem Typ „Sammlung“ erstellt (entspricht einer Reihe durch Trennzeichen getrennter Zeichenfolgen). Wir führen die Daten, die im vorherigen Schritt erstellt wurden, mit diesem Azure Search-Index zusammen.

- Öffnen Sie über die Visual Studio-Projektmappe „AzureSearchMovieRecommendations.sln“ die Datei „Program.cs“ in „MahoutOutputLoader“.
- Aktualisieren Sie „SearchServiceName“ und „SearchApiKey“ mit Ihren Azure Search-Dienstdetails.
- Aktualisieren Sie „StorageApiKey“ und „StorageAccountName“ mit den Details des Azure Storage-Kontos, für das Sie Ihre Mahout-Produktempfehlungsdatei gespeichert haben.
- Führen Sie die Anwendung aus, um die Daten zusammenzuführen.
 
## Visualisieren der Empfehlungen
An diesem Punkt sollten Sie zur Webanwendung zurückkehren und auf einen der Filme klicken können, um Empfehlungen zu sehen.

Wenn Sie sehen möchten, wie Empfehlungen zurückgegeben wurden, als Sie auf dieses Bild geklickt haben, öffnen Sie „Search.js“, und sehen Sie sich die openMovieDetails()-Funktion an.

## Quelle

Daten wurden von GroupLens bereitgestellt (http://grouplens.org/datasets/hetrec-2011/).

Auf dieser Seite finden Sie Informationen über die Lizenzierung dieser Daten: http://files.grouplens.org/datasets/hetrec2011/hetrec2011-movielens-readme.txt

<!---HONumber=Nov15_HO4-->