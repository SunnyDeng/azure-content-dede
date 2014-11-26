<properties title="Erstellen Sie Ihre erste Suchl&ouml;sung mit Azure Search" pageTitle="Erstellen Sie Ihre erste Suchl&ouml;sung mit Azure Search" description="Erstellen Sie Ihre erste Suchl&ouml;sung mit Azure Search" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article" ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Erstellen Sie Ihre erste Suchlösung mit Azure Search

-   [Voraussetzungen][Voraussetzungen]
-   [Erstellen eines Azure Search-Index][Erstellen eines Azure Search-Index]
-   [Beschreibung von CatalogIndexer][Beschreibung von CatalogIndexer]
-   [Erstellen einer MVC4-Anwendung mithilfe von Azure Search][Erstellen einer MVC4-Anwendung mithilfe von Azure Search]
-   [Beschreibung von AdventureWorksWeb][Beschreibung von AdventureWorksWeb]
-   [Nächste Schritte][Nächste Schritte]

## Übersicht

Das folgende Beispiel zeigt eine Suchanwendung für den Fahrradhersteller Adventure Works. Nach Abschluss dieses Lernprogramms verfügen Sie über einen Online-Produktkatalog mit einer reichhaltigen Suchfunktionalität, die Facettennavigation, verschiedene Sortieroptionen für die Suchergebnisse und Abfragevorschläge mit Textvervollständigung umfasst.

![][0]

Die Demo ermöglicht Ihnen mit folgenden Übungen einen Einstieg in Azure Search:

-   Erstellen eines Azure Search-Index
-   Laden von Dokumenten (Daten) in den Azure Search-Index aus einer SQL Server-Datenbank
-   Erstellen einer ASP.NET-MVC4-Anwendung, die Azure Search für folgende Zwecke nutzt:

    -   Durchsuchen eines Azure Search-Index und Anzeigen der Ergebnisse
    -   Anzeigen von Vorschlägen in einem Suchfeld während der Eingabe eines Suchbegriffs
    -   Filtern der Suchergebnisse mithilfe der Facettierung

## Voraussetzungen

-   Visual Studio 2012 oder höher mit installiertem ASP.NET MVC 4 und SQL Server. Visual Studio 2013 Express kann über das [Download Center][Download Center] aus dem Internet heruntergeladen werden.
-   Ein Azure Search-Dienst. Sie benötigen den Search-Dienstnamen sowie den Administratorschlüssel. Einzelheiten finden Sie unter [Erste Schritte mit Azure Search][Erste Schritte mit Azure Search].
-   [Azure Search-Demoprojekt zu Adventure Works auf CodePlex][Azure Search-Demoprojekt zu Adventure Works auf CodePlex]. Klicken Sie auf der Registerkarte "Source" (Quelle) auf **Download**, um eine ZIP-Datei der Lösung herunterzuladen.

    ![][1]

Die Lösung umfasst zwei Projekte:

-   **CatalogIndex** erstellt einen Azure Search-Index und lädt Daten aus einer im Projekt enthaltenen SQL Server-Datenbank.
-   **AdventureWorksWeb** ist eine MVC4-basierte Anwendung zum Abfragen des Azure Search-Index. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie über allgemeine Kenntnisse zu ASP.NET MVC verfügen.

[WACOM.INCLUDE [Sie brauchen ein Azure-Konto, um dieses Lernprogramm abzuschließen:](../includes/free-trial-note.md)]

## Erstellen eines Azure Search-Index

In diesem Schritt verwenden Sie **CatalogIndex**, um einen neuen Azure Search-Index namens "catalog" zu erstellen, der auf den Daten einer AdventureWorks SQL Server-Datenbank beruht.

1.  Öffnen Sie in Visual Studio die Azure Search-Demolösung namens **AdventureWorksCatalog.sln**.
2.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **CatalogIndexer**, und wählen Sie **Als Startprojekt festlegen** aus, damit diese Anwendung anstelle des Projekts **AdventureWorksWeb** ausgeführt wird, wenn Sie **F5** drücken.
3.  Öffnen Sie in diesem Projekt die Datei **App.config**, und aktualisieren Sie die Werte in den Feldern "SearchServiceName" und "SearchServiceApiKey" entsprechend Ihrem Azure Search-Dienst. Wenn Ihr Dienst beispielsweise "mysearch.search.windows.net" lautet, geben Sie unter "SearchServiceName" den Wert "mysearch" ein.
4.  Optional enthält die Datei App.config einen Eintrag für "SourceSqlConnectionString", der SQL Server 2014 Express LocalDB (Server=(LocalDB)\\v11.0) voraussetzt. Wenn Sie eine andere SQL Server-Edition verwenden, aktualisieren Sie den Servernamen entsprechend. Wenn Sie z. B. eine lokale Standardinstanz haben, können Sie "(local)" oder "localhost" verwenden.
5.  Speichern Sie die Datei **App.config**.
6.  Drücken Sie **F5**, um das Projekt zu starten.

Eine Eingabeaufforderung mit dem folgenden Text wird geöffnet: "Index wird erstellt..."

Nach einigen Augenblicken wird der Vorgang mit folgender Meldung abgeschlossen: "Abgeschlossen. Drücken Sie zum Fortfahren die <enter>:"

![][2]

Drücken Sie die **Eingabetaste**, um die Anwendung zu schließen. Damit ist die Erstellung des Azure Search-Index abgeschlossen.

> [WACOM.NOTE] Wenn Sie Fehlermeldungen erhalten, die den Text "Ungültiger Wert für den Schlüssel 'attachdbfilename'“ oder einen anderen Datenbankanhang-Fehler enthalten, sind eventuell UAC-Konflikte aufgetreten. Beheben Sie diese Fehler für diese Demonstration auf folgende Weise:
> Kopieren Sie die Lösung in einen neuen oder vorhandenen Ordner (z. B. "Temp"), der authentifizierten Benutzern Zugriff gewährt.
> Verwenden Sie **Als Administrator ausführen**, um Visual Studio zu starten.
> Öffnen Sie die Lösung, erstellen Sie sie, und drücken Sie **F5**, um den Index zu erstellen.

Um das Erstellen des Index und das Hochladen der Dokumente zu überprüfen, gehen Sie im [Azure Preview-Portal][Azure Preview-Portal] zum Suchdienst-Dashboard. Unter "Verwendung" muss der Indexzähler um eins erhöht worden sein, und es sollten 294 Dokumente vorhanden sein, eines für jedes Produkt in der Datenbank.

Klicken Sie auf die Kachel **Indizes**, um die Indexliste anzuzeigen. Die Indexliste fährt heraus und zeigt die neue Index- und Dokumentanzahl an.

![][3]

## Beschreibung von CatalogIndexer

Sehen wir uns das Projekt **CatalogIndexer** genauer an, um zu verstehen, wie es funktioniert.

1.  Öffnen Sie die Datei **Program.cs** im Projektmappen-Explorer, und gehen Sie zur Funktion `Main(string[] args)`.

    Diese Funktion erstellt eine Uri namens `_serviceURI`, die auf dem spezifischen Azure Search-Dienst beruht, und erstellt anschließend einen HttpClient namens `_httpClient`, der sich über Ihren API-Schlüssel bei diesem Suchdienst authentifiziert.

2.  `ChangeEnumeratorSql` und `ChangeSet` dienen dazu, die Daten aus der Tabelle "Produkte" in der SQL Server-Datenbank von AdventureWorks aufzulisten.

3.  Auf Grundlage der aus dieser Tabelle erfassten Daten wird anschließend `ApplyChanges` aufgerufen, um diese Daten auf den Azure Search-Index anzuwenden.

4.  Gehen Sie zu `ApplyChanges` in derselben Datei. Sie sehen, dass diese Funktion den Index löscht, wenn er bereits existiert (`DeleteCatalogIndex`), und danach einen neuen Index namens "catalog" (`CreateCatalogIndex`) erstellt.

5.  Gehen Sie zur Funktion `CreateCatalogIndex`. Sie sehen, dass der Index mit einem Schema erstellt wird, das den Spalten in der Tabelle "Produkte" in SQL Server entspricht. Jedes Feld hat einen Typ (d. h. `Edm.String` oder `Edm.Double`) sowie Attribute, die bestimmen, wofür diese Felder verwendet werden. Weitere Einzelheiten zu diesen Attributen finden Sie in der [Dokumentation zur REST API von Azure Search][Dokumentation zur REST API von Azure Search].

6.  Gehen Sie zurück zur Funktion `ApplyChanges`. Sie sehen, dass diese Funktion alle Daten in den aufgelisteten Änderungen `ChangeSet` in einer Schleife durchläuft. Anstatt die Änderungen nacheinander anzuwenden, werden sie in Gruppen von 1000 zusammengefasst und dann auf den Suchdienst angewendet. Dies ist wesentlich effizienter als die Anwendung der Dokumente nacheinander.

Nachdem Sie erfahren haben, wie Sie einen Index mit relationalen Daten aus SQL Server erstellen und füllen können, betrachten wir nun, wie Sie einen Produktkatalog erstellen, in dem die Suchdaten verwendet werden.

## Erstellen einer MVC4-Anwendung mithilfe von Azure Search

In diesem Schritt erstellen und führen Sie die Suchanwendung in einem Webbrowser aus.

1.  Öffnen Sie in Visual Studio die Azure Search-Demolösung namens **AdventureWorksCatalog.sln**.

2.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **AdventureWorksWeb**, und wählen Sie **Als Startprojekt festlegen** aus.

3.  Öffnen Sie in diesem Projekt die Datei **Web.config**, und aktualisieren Sie die Werte in den Feldern "SearchServiceName" und "SearchServiceApiKey" entsprechend Ihrem Azure Search-Dienst. Wenn Ihr Dienst beispielsweise "mysearch.search.windows.net" lautet, geben Sie unter "SearchServiceName" den Wert "mysearch" ein.

4.  Speichern Sie die Datei **Web.config**.

5.  Drücken Sie **F5**, um das Projekt zu starten. Wenn Sie einen Buildfehler erhalten, folgen Sie [diesen Schritten zur Problembehandlung][diesen Schritten zur Problembehandlung].

    ![][4]

6.  Lassen Sie das Suchfeld leer, und klicken Sie auf **Suchen**, um alle 294 Produkte zurückzugeben.

7.  Achten Sie auf die Liste der Facetten auf der linken Seite. Klicken Sie auf eine der Facetten. Die Suche wird erneut ausgeführt, diesmal jedoch mit der gewählten Facette zum Filtern der Ergebnisse. Es empfiehlt sich, in die erste Zeile der Funktion `Search` in der Datei **HomeController.cs** einen Haltepunkt einzufügen, um die einzelnen Zeilen schrittweise auszuführen (F11).

8.  Geben Sie einen Suchbegriff wie z. B. "mountain bikes" ein. Während der Eingabe wird eine Dropdownliste mit vorgeschlagenen Abfragen angezeigt.

    ![][5]

Der Screenshot vom Anfang dieser Demo wird als Erinnerung an das behandelte Thema unten erneut angezeigt. In den vorherigen Abschnitten haben wir die Facettennavigation (auf der linken Seite), die Dokumentanzahl oben auf der Seite, Vorschläge und die Optionen zum Sortieren nach Relevanz, Liste oder Preis erläutert.

![][0]

## Beschreibung von AdventureWorksWeb

Das Projekt AdventureWorksWeb zeigt, wie ASP.NET MVC 4 in einer Webanwendung für die Interaktion mit Azure Search verwendet werden kann. In diesem Abschnitt sehen wir uns einzelne Stellen des Programmcodes noch einmal genauer an.

1.  Erweitern Sie im Projektmappen-Explorer **AdventureWorksWeb** | **Controller**, und öffnen Sie die Datei **HomeController.cs**.

    Mit diesem MVC-Controller wird die Interaktion über die Indexansicht verwaltet. Oben im Controller sehen Sie einen Verweis auf `CatalogSearch _catalogSearch`, der ein HttpClient-Verbindungsobjekt zu Ihrem Azure Search-Dienst erstellt. Der Code für `CatalogSearch` befindet sich in der Datei **CatalogSeach.cs**.

2.  Die Datei **HomeController.cs** enthält zwei Hauptfunktionen:

    **Search**: Wenn der Benutzer auf die Suchschaltfläche klickt oder eine Facette wählt, wird diese Funktion aufgerufen, um die Ergebnisse abzurufen und an den Index zurückzusenden.

    **Suggest**: Während der Eingabe in das Suchfeld wird diese Funktion aufgerufen und gibt eine Liste von Vorschlägen zurück, die auf dem Inhalt des Azure Search-Index beruhen.

Betrachten wir diese beiden Funktionen genauer.

1.  In der Datei **HomeController.cs** sehen Sie in der Funktion `Search` einen Aufruf von `_catalogSearch.Search`, welches das Verbindungsobjekt zu dem Azure Search-Dienst enthält. Wenn Sie die in der Datei **CatalogSearch.cs** enthaltene Funktion "Search" aufrufen, können Sie feststellen, dass sie diese Parameter zum Erstellen einer Azure Search-Abfrage verwendet. Die Ergebnisse der Abfrage werden in einem JSON-Objekt namens `result` gespeichert und anschließend wieder an die Ansicht `Index` übergeben, wo die Ergebnisse analysiert und auf der Webseite angezeigt werden.

2.  Öffnen Sie die Datei **Index.cshtml** unter "Ansichten | Startseite". Dort sehen Sie den Code, der zum Analysieren dieser Ergebnisse verwendet wird.

3.  Halten Sie die Anwendung an, wenn sie noch ausgeführt wird, und öffnen Sie die Datei **Index.cshtml** unter "Ansichten | Startseite". Am Ende dieser Datei sehen Sie eine JavaScript-Funktion, die `JQuery $(function ())` verwendet. Diese Funktion wird beim Laden der Seite aufgerufen. Sie verwendet die Funktion "JQuery" zum automatischen Vervollständigen und verknüpft diese Funktion als Rückruf über das Suchtextfeld, das als "q" bezeichnet wird. Bei einer Eingabe in dieses Textfeld wird diese Funktion für automatische Empfehlungen aufgerufen, die wiederum "/home/suggest" mit dem eingegebenen Text aufruft. `/home/suggest` ist ein Verweis auf die in der Datei **HomeController.cs** enthaltene Funktion namens `Suggest`.

4.  Öffnen Sie die Datei **HomeController.cs**, und gehen Sie zur Funktion "Suggest". Dieser Code hat große Ähnlichkeit mit der Funktion "Search", die das Objekt `_catalogSearch` verwendet, um in der Datei **CatalogSearch.cs** eine Funktion namens `Suggest` aufzurufen. Anstatt eine Suchabfrage zu erstellen, ruft die Funktion `Suggest` die [Empfehlungen-API][Empfehlungen-API] auf. Diese verwendet die in das Textfeld eingegebenen Ausdrücke und erzeugt eine Liste möglicher Empfehlungen. Die Werte werden an die Datei **Index.cshtml** zurückgegeben und werden im Suchfeld automatisch als Eingabevorschläge aufgelistet.

An diesem Punkt fragen Sie sich vielleicht, wie Azure Search weiß, für welche Felder Empfehlungen erstellt werden sollen. Die Antwort darauf finden Sie beim Erstellen des Index. Die Funktion `CreateCatalogIndex` in der Datei "Program.cs" des Projekts **CatalogIndexer** enthält ein Attribut namens `Suggestions`. Wenn dieses Attribut auf `True` festgelegt ist, kann Azure Search es als Feld zum Abrufen von Vorschlägen verwenden.

Probieren wir dies einmal aus.

1.  Erstellen Sie die Anwendung erneut, und drücken Sie anschließend **F5**, um sie auszuführen.

2.  Geben Sie in das Suchfeld das Wort "Road" ein. Nach einer Sekunde sollte unter dem Textfeld eine Liste von Elementen mit Vorschlägen angezeigt werden, unter denen ein Benutzer wählen kann.

Sie sollten der Funktion `Suggest` in **HomeController.cs** eventuell einen Haltepunkt hinzufügen und die einzelnen Aufrufe zum Erstellen der Liste mit Vorschlägen schrittweise ausführen (F11).

Die Demonstration ist hiermit abgeschlossen. Sie haben sich nun mit den wichtigsten Operationen vertraut gemacht, die Sie zum Erstellen einer ASP.NET MVC4-Anwendung mit Azure Search benötigen.

## Beheben der Fehlermeldung "Datei oder Assemblydatei 'System.Web.Mvc' konnte nicht geladen werden"

Wenn beim Buildvorgang von AdventureWorksWeb die Fehlermeldung "Datei oder Assemblydatei 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' oder eine ihrer Abhängigkeiten konnte nicht geladen werden" angezeigt wird, gehen Sie wie folgt vor, um den Fehler zu beheben.

1.  Öffnen Sie die Paket-Manager-Konsole: **Extras** | **NuGet-Paket-Manager** | **Paket-Manager-Konsole**.
2.  Geben Sie an der Paket-Manager-Eingabeaufforderung folgenden Befehl ein: "Update-package -reinstall Microsoft.AspNet.Mvc".
3.  Wählen Sie bei Aufforderung zum Neuladen der Datei die Option **Ja, alle** aus.
4.  Erstellen Sie die Lösung neu, und drücken Sie **F5**.

## Nächste Schritte

Um Ihr Selbststudium zu vertiefen, können Sie eine Seite "Details" hinzufügen, die geöffnet wird, wenn ein Benutzer auf eines der Suchergebnisse klickt. Zur Vorbereitung darauf können Sie die folgenden Aufgaben ausführen:

-   Informieren Sie sich über die [Lookup-API][Lookup-API], mit der Sie eine Abfrage von Azure Search erstellen können, die ein bestimmtes Dokument zurückgibt (Sie können z. B. die Produkt-ID übergeben).
-   Versuchen Sie, der Datei **HomeController.cs** eine neue Funktion namens "Details" hinzuzufügen. Fügen Sie eine entsprechende Ansicht **Details.cshtml** hinzu, welche die Ergebnisse dieser Suche empfängt und anzeigt.
-   Prüfen Sie das folgende zusätzliche Codebeispiel und Video zur geografischen Suche: [Channel 9 - Azure Search und Geodaten][Channel 9 - Azure Search und Geodaten] und [CodePlex: Azure Search GeoSearch-Beispiel][CodePlex: Azure Search GeoSearch-Beispiel].

Informationen finden Sie auch unter [Azure Search REST-API][Dokumentation zur REST API von Azure Search] auf MSDN.

<!--Anchors-->
<!--Image references-->

  [Voraussetzungen]: #sub-1
  [Erstellen eines Azure Search-Index]: #sub-2
  [Beschreibung von CatalogIndexer]: #sub-3
  [Erstellen einer MVC4-Anwendung mithilfe von Azure Search]: #sub-4
  [Beschreibung von AdventureWorksWeb]: #sub-5
  [Nächste Schritte]: #next-steps
  [0]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
  [Download Center]: http://www.microsoft.com/de-de/download/details.aspx?id=40747
  [Erste Schritte mit Azure Search]: ../search-get-started/
  [Azure Search-Demoprojekt zu Adventure Works auf CodePlex]: http://go.microsoft.com/fwlink/p/?LinkID=510972
  [1]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG
  [2]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
  [Azure Preview-Portal]: https://portal.azure.com
  [3]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
  [Dokumentation zur REST API von Azure Search]: http://msdn.microsoft.com/de-de/library/azure/dn798935.aspx
  [diesen Schritten zur Problembehandlung]: #err-mvc
  [4]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
  [5]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
  [Empfehlungen-API]: http://msdn.microsoft.com/de-de/library/azure/dn798936.aspx
  [Lookup-API]: http://msdn.microsoft.com/de-de/library/azure/dn798929.aspx
  [Channel 9 - Azure Search und Geodaten]: http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data
  [CodePlex: Azure Search GeoSearch-Beispiel]: http://azuresearchgeospatial.codeplex.com
