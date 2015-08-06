<properties 
	pageTitle="Erstellen einer geografischen Suchanwendung mit Azure Search" 
	description="Erstellen einer geografischen Suchanwendung mit Bing und Azure Search" 
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

# Erstellen einer geografischen Suchanwendung mit Azure Search

In diesem Lernprogramm erfahren Sie, wie Sie Webanwendungen mithilfe von Azure Search und Bing Maps um eine geografische Suche erweitern können. Mithilfe der geografischen Suche können Sie nach Zielen suchen, die sich innerhalb eines bestimmten Umkreises eines gegebenen Punktes befinden (z. B. alle Restaurants im Umkreis von 5 km meines Standortes). Die Geofunktion in Azure Search unterstützt häufig verwendete Kartierungstechniken. Wenn Sie beispielsweise in einer Immobilienanwendung polygone Formen verwenden möchten, um zum Verkauf stehende Häuser innerhalb eines bestimmten Wabenbereichs anzuzeigen, können Sie dazu das OData-Protokoll oder unsere einfache Suchsyntax verwenden.

In diesem Channel 9-Video zu [Azure Search und Geodaten](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) erhalten Sie einen weiteren Überblick über das Thema.

![][7]

Zum Erstellen der Anwendung nutzen wir den Bing-Kartendienst, um aus einer CSV-Datei geladene Adressen zu geokodieren und die Ergebnisse in einem Suchindex zu speichern.

Dieses Lernprogramm baut auf der [Azure Search – Adventure Works Demo](http://azuresearchadventureworksdemo.codeplex.com) auf. Gehen Sie die Demonstration Schritt für Schritt durch, falls Sie dies noch nicht getan haben, um sich mit dem Erstellen eines Index sowie dem Aufrufen der Azure Search-API aus einer Webanwendung vertraut zu machen.

<a id="sub-1"></a>
## Voraussetzungen

+	Visual Studio 2012 oder höher mit installiertem ASP.NET MVC 4 und SQL Server. Falls diese Software noch nicht installiert ist, können Sie jeweils die kostenlose Express-Edition herunterladen: [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) und [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Ein Azure Search-Dienst. Sie benötigen den Search-Dienstnamen sowie den Administratorschlüssel. Ausführliche Informationen finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md).
+	Einen Bing-Kartendienst sowie einen zugehörigen Zugriffsschlüssel. Anweisungen dazu finden Sie im nächsten Abschnitt.
+	[Azure Search GeoSearch-Beispiel auf CodePlex](https://azuresearchgeospatial.codeplex.com/). Klicken Sie auf der Registerkarte "Source" (Quelle) auf **Download**, um eine ZIP-Datei der Lösung herunterzuladen. 

    ![][12]

Die Lösung umfasst zwei Projekte:

+	**StoreIndexer** erstellt einen Azure Search-Index und lädt Daten.
+	**AdventureWorksWebGeo** ist eine MVC4-basierte Anwendung, die den Azure Search-Index abfragt und Geschäftsstandorte auf einer Bing-Karte anzeigt.

[AZURE.INCLUDE [Für dieses Lernprogramm benötigen Sie ein Azure-Konto:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Bing Maps

Wir werden die Bing Maps-API für zwei Dinge verwenden:

+ **Geokodieren von Adressen:** Die Daten umfassen zwar Adressen (Stadt, Bundesstaat, Postleitzahl), wir benötigen aber auch die Längen- und Breitenkoordinaten einer Adresse, um eine geografische Suche durchführen zu können. Zum Abrufen der Koordinaten wird die Bing Maps DataFlow-API verwendet, um ein Adress-Batch zum Geokodieren zu verarbeiten. Mit dem Testkonto von Bing können wir gleichzeitig bis zu 50 Adressen verarbeiten. Diese Beschränkung sollte für dieses Lernprogramm ausreichen.

+ **Bing Maps:** Beim Ausführen der Anwendung werden in Bing Maps Geschäftsstandorte als Überlagerung auf einer Bing-Karte angezeigt.

### Erstellen eines Kontos für Bing Maps

1. Rufen Sie das [Bing Maps Portal](https://www.bingmapsportal.com/) auf, und erstellen Sie ein neues Konto. Geben Sie die erforderlichen Informationen zum Erstellen eines Kontos ein.

2. Wählen Sie nach Erstellen des Kontos die Option **Create or view keys** (Schlüssel erstellen oder anzeigen) aus, und geben Sie die Informationen zum Erstellen eines Schlüssels ein. Für diese Demonstration können Sie die Option **Trial Key** (Schlüssel für die Testversion) auswählen.

3. Klicken Sie auf **Submit** (Senden). Daraufhin sollten die Schlüsselinformationen für Ihre Bing Maps-Anmeldung angezeigt werden. Notieren Sie sich den Schlüssel. Wir werden später darauf zurückgreifen.

<a id="sub-3"></a>
## Geokodieren von Adressen in C# mit Bing Maps DataFlow-API

In diesem Schritt verwenden wir die Bing Maps DataFlow-API, um einige Adressen von verschiedenen weltweiten Fahrradgeschäften zu geokodieren.

Die Daten stammen aus der Datei "store_locations.csv", die sich in den heruntergeladenen Materialien befindet. Wenn Sie diese Datei mit einem Texteditor oder Microsoft Excel öffnen, sehen Sie, dass jedes Geschäft mit einer ID, dem Namen des Geschäfts und der Adresse aufgelistet ist.

Lassen Sie uns gemeinsam den Code durchgehen, um seine Funktionsweise zu verstehen.

1. Öffnen Sie das Projekt "AdventureWorksGeo" in Visual Studio, erweitern Sie im Projektmappen-Explorer das Projekt **StoreIndexer**, und öffnen Sie die Datei "Program.cs". Da wir die Indexerstellung bereits im Artikel [Azure Search – Adventure Works Demo](http://azuresearchadventureworksdemo.codeplex.com/) behandelt haben, gehen wir an dieser Stelle nicht darauf ein, wie dies in "Program.cs" funktioniert.

2. Suchen Sie die Funktion **Main**. Wie Sie sehen, ruft sie die Funktion **ApplyStoreData** auf. Suchen Sie diese Funktion, und gehen Sie den Code Zeile für Zeile durch.

3. Die Funktion **ApplyStoreData** lädt Daten aus einer CSV-Datei mit dem Namen "store_locations.csv" in eine Tabelle (System.Data.DataTable).

    Diese Datei enthält alle Geschäfte (einschließlich der Adressen), die wir in Azure Search laden möchten. Durch die Wiederholung jeder Zeile in dieser Datei können wir einen Satz an **indexOperations** erstellen, die dann in einen Azure Search-Index eingefügt werden (zuvor in der Funktion **CreateStoresIndex()** erstellt).

    Wenn Sie sich den Index anschließend genau ansehen, fällt auf, dass das Feld **GeoPt** mit den Längen- und Breitenangaben für jedes Geschäft leer ist. Dies führt uns zum nächsten Schritt der Funktion **Main**.

5. Suchen Sie nach der Funktion **ExtractAddressInfoToXML()**. Mit dieser Funktion werden die Adressinformationen aus der Datei "store_locations.csv" extrahiert und in eine XML-Datei geladen, die für die Geokodierung in Bing Maps formatiert ist. Nachdem die Datei erstellt ist, wird sie zur Verarbeitung an Bing Maps DataFlow gesendet. Dieser Vorgang erfolgt durch Aufrufen der Funktion **GeoCoding.CreateJob**.

6. Da die Geokodierung einige Zeit in Anspruch nehmen kann, wird mithilfe einer Programmschleife alle 10 Sekunden die Funktion **GeoCoding.CheckStatus** aufgerufen, um zu prüfen, ob der Vorgang abgeschlossen ist. Nachdem der Vorgang abgeschlossen ist, werden die Ergebnisse durch Aufrufen der Funktion **GeoCoding.DownloadResults** in eine Adressenklasse heruntergeladen.

7. Im letzten Schritt werden die geokodierten Adressen an Azure Search gesendet. Sehen wir uns diesen Vorgang, der von der Funktion **UpdateStoreData** durchgeführt wird, genauer an.

  Die Funktion **UpdateStoreData** verwendet die Aktion **@search.action: merge**, um das Standortfeld vom Typ „Edm.GeographyPoint“ mit den geokodierten Längen- und Breitenkoordinaten zu aktualisieren, die gerade von Bing Maps heruntergeladen wurden. Dazu wird die "storeId" nachgeschlagen, bei der es sich um den eindeutigen Schlüssel für ein Dokument im Index "stores" handelt. Anschließend werden die neuen Daten in das bestehende Dokument übertragen.

8. Bevor Sie die Anwendung ausführen, fügen Sie Ihre Azure Search- und Bing Maps-API-Informationen hinzu. Öffnen Sie dazu die Datei "App.config“, und aktualisieren Sie die Werte in den Feldern "SearchServiceName", "SearchServiceApiKey" und "BingMapsAPI" entsprechend Ihrem Azure Search-Dienst und Bing Maps-API. Wenn Ihr Dienst beispielsweise "mysearch.search.windows.net" lautet, geben Sie unter "SearchServiceName" den Wert "mysearch" ein.

9. Klicken Sie mit der rechten Maustaste auf das Projekt **StoreIndexer**, und wählen Sie **Debuggen** | **Neue Instanz starten** aus, um das Projekt auszuführen.

<a id="sub-4"></a>
## Hinzufügen von Karten zu einer MVC4-Anwendung mithilfe von Azure Search und Bing Maps

In diesem Schritt wird die Suchanwendung erstellt und in einem Webbrowser ausgeführt, der die Suche nach Geschäften lädt und in Bing Maps darstellt.

1.	Öffnen Sie in Visual Studio die Azure Search-Demolösung mit dem Namen "AdventureWorksGeo.sln". 
	
2.	Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **AdventureWorksWebGeo**, und wählen Sie **Als Startprojekt festlegen** aus.
	
3.	Öffnen Sie in diesem Projekt die Datei "Web.config“, und aktualisieren Sie die Werte in den Feldern "SearchServiceName", "SearchServiceApiKey" und "BingMapsAPI" entsprechend Ihrem Azure Search-Dienst und Bing Maps-API. Wenn Ihr Dienst beispielsweise "mysearch.search.windows.net" lautet, geben Sie unter "SearchServiceName" den Wert "mysearch" ein.

4.	Speichern Sie die Datei "Web.config".
	
5.	Drücken Sie **F5**, um das Projekt zu starten. Wenn Sie einen Buildfehler erhalten, folgen Sie [diesen Schritten zur Problembehandlung](#err-mvc).

Die Geschäfte werden als Punkte dargestellt und sind der Karte überlagert. Klicken Sie auf ein Geschäft, um Informationen dazu in einem Popup-Fenster anzuzeigen. Die Informationen stammen aus dem Azure Search-Index "stores", der in den vorherigen Schritten erstellt wurde.

<a id="sub-5"></a>
## Beschreibung von AdventureWorksWebGeo

Das Projekt **AdventureWorksWebGeo** beschreibt, wie ASP.NET MVC 4 zusammen mit Azure Search zum Erstellen einer Kartenanwendung mithilfe der Geosuche verwendet wird. In diesem Abschnitt sehen wir uns noch einmal einzelne Stellen des Programmcodes genauer an.

1.	Erweitern Sie im Projektmappen-Explorer **AdventureWorksWebGeo** | **Controller**, und öffnen Sie die Datei "HomeController.cs". Beim Starten der Anwendung wird die Funktion **Index()** aufgerufen und die Indexseite geladen. In dieser Funktion wird die Bing Maps-API aus der Datei "Web.config" geladen und als "ViewBag.BingAPI" an die Indexansicht übergeben.

2.	Öffnen Sie über **Ansichten** | **Startseite** die Datei "Index.cshtml".

3.	Diese Datei ermöglicht, Bing Maps auf typische Weise zu einer Webanwendung hinzuzufügen. Beachten Sie dennoch die folgenden Hinweise:

+	Mit der Eigenschaft "ViewBag" im Controller werden die Anmeldedaten für die Karte geladen: Anmeldedaten: '@ViewBag.BingAPI'. 

+	Nach dem Laden der Karte wird mit der JQuery-Funktion "$.post" die HomeController-Funktion **Search** aufgerufen, mit Referenz zu: /home/search.

+	Die Funktion **Search** ruft die Geschäftsstandorte ab, die nach Empfang als PushPins zur Bing-Karte hinzugefügt werden.

4.	Öffnen Sie unter **Controllers** die Datei "HomeController.cs“, und sehen Sie sich die Funktion **Search** an. Diese Funktion ruft die Funktion „_storeSearch.Search(lat, lon, 10000)“ auf. Dadurch wird eine Abfrage ausgeführt, um nach allen Geschäften in einem Umkreis von 10.000 km des mit dem Längengrad (lon) und Breitengrad (lat) angegebenen Standorts zu suchen. Die Abfrageergebnisse werden verarbeitet und anschließend an die Indexansicht zurückgesendet, um als PushPins verarbeitet und als Überlagerung auf der Bing-Karte angezeigt zu werden.

Die Demonstration ist hiermit abgeschlossen. Sie haben sich nun mit den wichtigsten Operationen vertraut gemacht, die Sie zum Erstellen einer kartenbasierten ASP.NET MVC4-Anwendung mit Azure Search benötigen.


<a id="err-mvc"></a>
## Beheben der Fehlermeldung "Datei oder Assemblydatei 'System.Web.Mvc' konnte nicht geladen werden"

Wenn beim Buildvorgang von AdventureWorksWeb die Fehlermeldung "Datei oder Assemblydatei 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' oder eine ihrer Abhängigkeiten konnte nicht geladen werden" angezeigt wird, gehen Sie wie folgt vor, um den Fehler zu beheben.

1. Wählen Sie **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole **, um die Paket-Manager-Konsole zu öffnen.
2. Geben Sie an der Paket-Manager-Eingabeaufforderung folgenden Befehl ein: "Update-package -reinstall Microsoft.AspNet.Mvc".
3. Wählen Sie bei Aufforderung zum Neuladen der Datei die Option **Ja, alle** aus.
4. Erstellen Sie die Lösung neu, und drücken Sie **F5**.

<a id="next-steps"></a>
## Nächste Schritte

Um Ihr Selbststudium zu vertiefen, fügen Sie beispielsweise weitere Funktionen zur Kartenanwendung hinzu. Zum Beispiel:

+ Ein Suchfeld, um Benutzern die Suche nach bestimmten Geschäften zu ermöglichen

+ Facetten, damit Benutzer ihre Suche nach Land oder Bundesstaat filtern können

+ Benutzerbezogene Auswahlbereiche, über die Benutzer zu durchsuchende Regionen angeben können, indem sie den gewünschten Bereich auf der Karte markieren. Der markierte Bereich wird mithilfe der geo-intersect-API von Azure Search gefiltert und auf der Karte dargestellt.


<!--Anchors-->
[Prerequisites]: #sub-1
[Bing Maps]: #sub-2
[Geocode Addresses in C# using Bing Maps DataFlow API]: #sub-3
[Add Mapping to an MVC4 Application using Azure Search and Bing Maps]: #sub-4
[Explore AdventureWorksWebGeo]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG

<!----HONumber=July15_HO4-->