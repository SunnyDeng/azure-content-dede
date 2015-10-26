<properties 
	pageTitle="Erstellen der ersten Suchlösung mit Azure Search" 
	description="Erstellen der ersten Suchlösung mit Azure Search" 
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

# Erstellen der ersten Suchlösung mit Azure Search

Das folgende Beispiel zeigt eine Suchanwendung für den Fahrradhersteller Adventure Works. Nach Abschluss dieses Lernprogramms verfügen Sie über einen Online-Produktkatalog mit einer reichhaltigen Suchfunktionalität, die Facettennavigation, verschiedene Sortieroptionen für die Suchergebnisse und Abfragevorschläge mit Textvervollständigung umfasst.

   ![][7]

Die Demo ermöglicht Ihnen mit folgenden Übungen einen Einstieg in Azure Search:

+	Erstellen eines Azure Search-Index
+	Laden von Dokumenten (Daten) in den Azure Search-Index aus einer SQL Server-Datenbank
+	Erstellen einer ASP.NET-MVC4-Anwendung, die Azure Search für folgende Zwecke nutzt:
	+	Durchsuchen eines Azure Search-Index und Anzeigen der Ergebnisse
	+	Anzeigen von Vorschlägen in einem Suchfeld während der Eingabe eines Suchbegriffs
	+	Filtern der Suchergebnisse mithilfe der Facettierung


<a id="sub-1"></a>
## Voraussetzungen

+	Ein [Azure-Abonnement](../includes/free-trial-note.md). Wenn Sie sich noch nicht für ein Testabonnement registrieren möchten, können Sie dieses Lernprogramm überspringen und stattdessen Azure App Service testen[](https://tryappservice.azure.com/). Bei dieser Alternativoption erhalten Sie Azure Search mit einer ASP.NET-Web-App kostenlos – eine Stunde pro Sitzung, ganz ohne Abonnement.
+	Visual Studio 2012 oder höher mit installiertem ASP.NET MVC 4 und SQL Server. Falls diese Software noch nicht installiert ist, können Sie jeweils die kostenlose Express-Edition herunterladen: [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) und [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Ein Azure Search-Dienst. Sie benötigen den Search-Dienstnamen sowie den Administratorschlüssel. Ausführliche Informationen finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md).
+	[Azure Search-Demoprojekt zu Adventure Works auf CodePlex](http://go.microsoft.com/fwlink/p/?LinkID=510972). Klicken Sie auf der Registerkarte "Source" (Quelle) auf **Download**, um eine ZIP-Datei der Lösung herunterzuladen. 

    ![][12]


Die Lösung umfasst zwei Projekte:

+	**CatalogIndex** erstellt einen Azure Search-Index und lädt Daten aus einer im Projekt enthaltenen SQL Server-Datenbank.
+	**AdventureWorksWeb** ist eine MVC4-basierte Anwendung zum Abfragen des Azure Search-Index. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie über allgemeine Kenntnisse zu ASP.NET MVC verfügen.

<a id="sub-2"></a>
## Erstellen eines Azure Search-Index

In diesem Schritt verwenden Sie **CatalogIndex**, um einen neuen Azure Search-Index namens "catalog" zu erstellen, der auf den Daten einer AdventureWorks SQL Server-Datenbank beruht.

1.	Öffnen Sie in Visual Studio die Azure Search-Demoprojektmappe namens **AdventureWorksCatalog.sln**.  
2.	Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **CatalogIndexer**, und wählen Sie **Als Startprojekt festlegen** aus, damit diese Anwendung anstelle des Projekts **AdventureWorksWeb** ausgeführt wird, wenn Sie **F5** drücken.
3.	Öffnen Sie in diesem Projekt die Datei **App.config**, und aktualisieren Sie die Werte in den Feldern "SearchServiceName" und "SearchServiceApiKey" entsprechend Ihrem Azure Search-Dienst. Wenn Ihr Dienst beispielsweise "mysearch.search.windows.net" lautet, geben Sie unter "SearchServiceName" den Wert "mysearch" ein.
4.	Optional enthält die Datei App.config einen Eintrag für "SourceSqlConnectionString", der SQL Server 2014 Express LocalDB (Server=(LocalDB)\\v11.0) voraussetzt. Wenn Sie eine andere SQL Server-Edition verwenden, aktualisieren Sie den Servernamen entsprechend. Wenn Sie z. B. eine lokale Standardinstanz haben, können Sie "(local)" oder "localhost" verwenden.
5.	Speichern Sie die Datei **App.config**.
6.	Drücken Sie **F5**, um das Projekt zu starten.

Eine Eingabeaufforderung mit folgendem Text wird geöffnet: „Creating index...“

Nach einigen Augenblicken wird der Vorgang mit folgender Meldung abgeschlossen: „Complete. Press <enter> to continue:“

   ![][8]

Drücken Sie die **Eingabetaste**, um die Anwendung zu schließen. Damit ist die Erstellung des Azure Search-Index abgeschlossen.

> [AZURE.NOTE]Wenn Sie Fehlermeldungen erhalten, die den Text "Ungültiger Wert für den Schlüssel 'attachdbfilename'" oder einen anderen Datenbankzuordnungsfehler enthalten, sind eventuell UAC-Konflikte aufgetreten. Beheben Sie diese Fehler für diese Demonstration auf folgende Weise: Kopieren Sie die Projektmappe in einen neuen oder vorhandenen Ordner (z. B. „Temp“), auf den authentifizierte Benutzer zugreifen können. Starten Sie Visual Studio mit der Option **Als Administrator ausführen**. Öffnen Sie die Projektmappe, erstellen Sie sie, und drücken Sie dann F5, um den Index zu erstellen.

Navigieren Sie im [Azure-Verwaltungsportal](https://portal.azure.com) zum Dashboard des Search-Diensts, um die Indexerstellung und das Hochladen der Dokumente zu überprüfen. Unter "Verwendung" muss der Indexzähler um eins erhöht worden sein, und es sollten 294 Dokumente vorhanden sein, eines für jedes Produkt in der Datenbank.

Klicken Sie auf die Kachel **Indizes**, um die Indexliste anzuzeigen. Die Indexliste fährt heraus und zeigt die neue Index- und Dokumentanzahl an. Beachten Sie, dass im Tarif „Free“ bis zu drei Indizes zur Verfügung stehen. Falls bereits drei Indizes vorhanden sind, müssen Sie einen der Indizes löschen, um Platz für neue Indizes zu schaffen.

   ![][9]


<a id="sub-3"></a>
## Beschreibung von CatalogIndexer

Sehen wir uns das Projekt **CatalogIndexer** genauer an, um zu verstehen, wie es funktioniert.

1.	Öffnen Sie im Projektmappen-Explorer die Datei **Program.cs**, und navigieren Sie zur Funktion `Main(string[] args)`.

    Diese Funktion erstellt einen URI namens `_serviceURI`, der auf dem spezifischen Azure Search-Dienst beruht, und erstellt anschließend einen HTTP-Client namens `_httpClient`, der sich mithilfe Ihres API-Schlüssels bei dem Search-Dienst authentifiziert.

2.	`ChangeEnumeratorSql` und `ChangeSet` dienen dazu, die Daten aus der Produkttabelle in der SQL Server-Datenbank „AdventureWorks“ aufzulisten.

3.	Auf der Grundlage der Daten aus dieser Tabelle wird anschließend `ApplyChanges` aufgerufen, um die Daten auf den Azure Search-Index anzuwenden.

4.	Navigieren Sie in der gleichen Datei zu `ApplyChanges`. Beachten Sie, dass diese Funktion den Index löscht, wenn er bereits vorhanden ist (`DeleteCatalogIndex`), und danach einen neuen Index namens „catalog“ erstellt (`CreateCatalogIndex`).

5.	Navigieren Sie zur Funktion `CreateCatalogIndex`. Beachten Sie, dass der Index mit einem Schema erstellt wird, das den Spalten der Produkttabelle in SQL Server entspricht. Jedes Feld besitzt einen Typ (`Edm.String` oder `Edm.Double`) sowie Attribute, die bestimmen, wofür diese Felder verwendet werden. Weitere Einzelheiten zu diesen Attributen finden Sie in der [Dokumentation zur REST API von Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx).

6.	Navigieren Sie wieder zur Funktion `ApplyChanges`. Beachten Sie, dass diese Funktion alle Daten in den aufgelisteten Änderungen (`ChangeSet`) in einer Schleife durchläuft. Anstatt die Änderungen nacheinander anzuwenden, werden sie in Gruppen von 1000 zusammengefasst und dann auf den Suchdienst angewendet. Dies ist wesentlich effizienter als die Anwendung der Dokumente nacheinander.

Nachdem Sie erfahren haben, wie Sie einen Index mit relationalen Daten aus SQL Server erstellen und füllen können, betrachten wir nun, wie Sie einen Produktkatalog erstellen, in dem die Suchdaten verwendet werden.

<a id="sub-4"></a>
## Erstellen einer MVC4-Anwendung mit Azure Search

In diesem Schritt erstellen und führen Sie die Suchanwendung in einem Webbrowser aus.

1.	Öffnen Sie in Visual Studio die Azure Search-Demolösung namens **AdventureWorksCatalog.sln**.  

2.	Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **AdventureWorksWeb**, und wählen Sie **Als Startprojekt festlegen** aus.

3.	Öffnen Sie in diesem Projekt die Datei **Web.config**, und aktualisieren Sie die Werte in den Feldern "SearchServiceName" und "SearchServiceApiKey" entsprechend Ihrem Azure Search-Dienst. Wenn Ihr Dienst beispielsweise "mysearch.search.windows.net" lautet, geben Sie unter "SearchServiceName" den Wert "mysearch" ein.

4.	Speichern Sie die Datei **Web.config**.

5.	Drücken Sie **F5**, um das Projekt zu starten. Wenn Sie einen Buildfehler erhalten, folgen Sie [diesen Schritten zur Problembehandlung](#err-mvc).

    ![][10]

6.	Lassen Sie das Suchfeld leer, und klicken Sie auf **Suchen**, um alle 294 Produkte zurückzugeben.

7.	Achten Sie auf die Liste der Facetten auf der linken Seite. Klicken Sie auf eine der Facetten. Die Suche wird erneut ausgeführt, diesmal jedoch mit der gewählten Facette zum Filtern der Ergebnisse. Es empfiehlt sich, in die erste Zeile der Funktion `Search` in der Datei **HomeController.cs** einen Haltepunkt einzufügen, um die einzelnen Zeilen schrittweise ausführen zu können (F11).

7.	Geben Sie einen Suchbegriff wie z. B. "mountain bikes" ein. Während der Eingabe wird eine Dropdownliste mit vorgeschlagenen Abfragen angezeigt.

    ![][11]

Der Screenshot vom Anfang dieser Demo wird als Erinnerung an das behandelte Thema unten erneut angezeigt. In den vorherigen Abschnitten haben wir die Facettennavigation (auf der linken Seite), die Dokumentanzahl oben auf der Seite, Vorschläge und die Optionen zum Sortieren nach Relevanz, Liste oder Preis erläutert.

   ![][7]


<a id="sub-5"></a>
## Beschreibung von AdventureWorksWeb

Das Projekt AdventureWorksWeb zeigt, wie ASP.NET MVC 4 in einer Webanwendung für die Interaktion mit Azure Search verwendet werden kann. In diesem Abschnitt sehen wir uns einzelne Stellen des Programmcodes noch einmal genauer an.

1.	Erweitern Sie im Projektmappen-Explorer **AdventureWorksWeb** | **Controller**, und öffnen Sie die Datei **HomeController.cs**.

    Mit diesem MVC-Controller wird die Interaktion über die Indexansicht verwaltet. Oben im Controller sehen Sie einen Verweis auf `CatalogSearch _catalogSearch`. Damit wird ein HttpClient-Verbindungsobjekt für Ihren Azure Search-Dienst erstellt. Der Code für `CatalogSearch` befindet sich in der Datei **CatalogSeach.cs**.

2. Die Datei **HomeController.cs** enthält zwei Hauptfunktionen:

	**Search**: Wenn der Benutzer auf die Suchschaltfläche klickt oder eine Facette wählt, wird diese Funktion aufgerufen, um die Ergebnisse abzurufen und an den Index zurückzusenden.

	**Suggest**: Diese Funktion wird während der Eingabe in das Suchfeld aufgerufen und gibt eine Liste mit Vorschlägen zurück, die auf dem Inhalt des Azure Search-Index beruhen.

Betrachten wir diese beiden Funktionen genauer.

3.	In der Datei **HomeController.cs** sehen Sie in der Funktion `Search` einen Aufruf von `_catalogSearch.Search`. Darin ist das Verbindungsobjekt für den Azure Search-Dienst enthalten. Wenn Sie die in der Datei **CatalogSearch.cs** enthaltene Funktion "Search" aufrufen, können Sie feststellen, dass sie diese Parameter zum Erstellen einer Azure Search-Abfrage verwendet. Die Ergebnisse der Abfrage werden in einem JSON-Objekt namens `result` gespeichert und anschließend wieder an die Ansicht `Index` übergeben. Hier werden die Ergebnisse dann analysiert und auf der Webseite angezeigt.

4.	Öffnen Sie die Datei **Index.cshtml** unter "Ansichten | Startseite". Dort sehen Sie den Code, der zum Analysieren dieser Ergebnisse verwendet wird.

5.	Halten Sie die Anwendung an, wenn sie noch ausgeführt wird, und öffnen Sie die Datei **Index.cshtml** unter "Ansichten | Startseite". Am Ende dieser Datei sehen Sie eine JavaScript-Funktion, die `JQuery $(function ())` verwendet. Diese Funktion wird beim Laden der Seite aufgerufen. Sie verwendet die Funktion "JQuery" zum automatischen Vervollständigen und verknüpft diese Funktion als Rückruf über das Suchtextfeld, das als "q" bezeichnet wird. Bei einer Eingabe in dieses Textfeld wird diese Funktion für automatische Empfehlungen aufgerufen, die wiederum „/home/suggest“ mit dem eingegebenen Text aufruft. `/home/suggest` ist ein Verweis auf die in der Datei **HomeController.cs** enthaltene Funktion namens `Suggest`.

6.	Öffnen Sie die Datei **HomeController.cs**, und gehen Sie zur Funktion "Suggest". Dieser Code hat große Ähnlichkeit mit der Funktion „Search“, die das `_catalogSearch` Objekt verwendet, um in der Datei **CatalogSearch.cs** eine Funktion namens `Suggest` aufzurufen. Die Funktion `Suggest` initiiert jedoch keine Suchabfrage, sondern ruft die [Vorschlags-API](http://msdn.microsoft.com/library/azure/dn798936.aspx) auf. Diese verwendet die in das Textfeld eingegebenen Ausdrücke und erzeugt eine Liste möglicher Empfehlungen. Die Werte werden an die Datei **Index.cshtml** zurückgegeben und werden im Suchfeld automatisch als Eingabevorschläge aufgelistet.

An diesem Punkt fragen Sie sich vielleicht, wie Azure Search weiß, für welche Felder Empfehlungen erstellt werden sollen. Die Antwort darauf finden Sie beim Erstellen des Index. Die Funktion `CreateCatalogIndex` in der Datei „Program.cs“ des Projekts **CatalogIndexer** enthält ein Attribut namens `Suggestions`. Wenn dieses Attribut auf `True` festgelegt ist, kann es von Azure Search als Feld zum Abrufen von Vorschlägen verwendet werden.

Probieren wir dies einmal aus.

7.	Erstellen Sie die Anwendung erneut, und drücken Sie anschließend **F5**, um sie auszuführen.

8.	Geben Sie in das Suchfeld das Wort "Road" ein. Nach einer Sekunde sollte unter dem Textfeld eine Liste von Elementen mit Vorschlägen angezeigt werden, unter denen ein Benutzer wählen kann.

Sie sollten der Funktion `Suggest` in **HomeController.cs** eventuell einen Haltepunkt hinzufügen und die einzelnen Aufrufe zum Erstellen der Liste mit Vorschlägen schrittweise ausführen (F11).

Die Demonstration ist hiermit abgeschlossen. Sie haben sich nun mit den wichtigsten Operationen vertraut gemacht, die Sie zum Erstellen einer ASP.NET MVC4-Anwendung mit Azure Search benötigen.


<a id="err-mvc"></a>
## Beheben der Fehlermeldung "Datei oder Assemblydatei 'System.Web.Mvc' konnte nicht geladen werden"

Wenn beim Buildvorgang von AdventureWorksWeb die Fehlermeldung "Datei oder Assemblydatei 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' oder eine ihrer Abhängigkeiten konnte nicht geladen werden" angezeigt wird, gehen Sie wie folgt vor, um den Fehler zu beheben.

1. Wählen Sie **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole **, um die Paket-Manager-Konsole zu öffnen.
2. Geben Sie an der Paket-Manager-Eingabeaufforderung folgenden Befehl ein: "Update-package -reinstall Microsoft.AspNet.Mvc".
3. Wählen Sie bei Aufforderung zum Neuladen der Datei die Option **Ja, alle** aus.
4. Erstellen Sie die Lösung neu, und drücken Sie **F5**.


<a id="next-steps"></a>
## Nächste Schritte

Um Ihr Selbststudium zu vertiefen, können Sie eine Seite "Details" hinzufügen, die geöffnet wird, wenn ein Benutzer auf eines der Suchergebnisse klickt. Zur Vorbereitung darauf können Sie die folgenden Aufgaben ausführen:

+	Informieren Sie sich über die [Lookup-API](http://msdn.microsoft.com/library/azure/dn798929.aspx), mit der Sie eine Abfrage von Azure Search erstellen können, die ein bestimmtes Dokument zurückgibt (Sie können z. B. die Produkt-ID übergeben).
+	Versuchen Sie, der Datei **HomeController.cs** eine neue Funktion namens "Details" hinzuzufügen. Fügen Sie eine entsprechende Ansicht **Details.cshtml** hinzu, welche die Ergebnisse dieser Suche empfängt und anzeigt.
+	Sehen Sie sich das folgende zusätzliche Codebeispiel und Video zur geografischen Suche an: [Channel 9 – Azure Search und Geodaten](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) und [CodePlex: Azure Search GeoSearch-Beispiel](http://azuresearchgeospatial.codeplex.com)

Informationen finden Sie auch unter [Azure Search REST-API](http://msdn.microsoft.com/library/azure/dn798935.aspx) auf MSDN.


<!--Anchors-->
[Prerequisites]: #sub-1
[Create an Azure Search index]: #sub-2
[Explore CatalogIndexer]: #sub-3
[Build an MVC4 Application using Azure Search]: #sub-4
[Explore AdventureWorksWeb]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
[8]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
[9]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
[10]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
[11]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
[12]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG

<!---HONumber=Oct15_HO3-->