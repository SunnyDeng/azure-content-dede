<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-data-dotnet/" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Daten in Mobile Services
===========================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/de-de/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

[.NET-Backend](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ ".NET-Backend") | [JavaScript-Backend](/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "JavaScript-Backend")

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

**Hinweis**

Für dieses Lernprogramm benötigen Sie Visual Studio 2013, mit dem eine Verbindung Ihrer Windows Store-App mit Mobile Services einfacher hergestellt werden kann. Um dasselbe grundlegende Verfahren mit Visual Studio 2012 auszuführen, befolgen Sie die Schritte im Thema [Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012](/de-de/develop/mobile/tutorials/get-started-with-data-js-vs2012/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

1.  [Herunterladen des Windows Store-App-Projekts](#download-app)
2.  [Erstellen des mobilen Diensts](#create-service)
3.  [Erstellen einer Datentabelle als Datenspeicher](#add-table)
4.  [Aktualisieren der App zur Verwendung von mobilen Diensten](#update-app)
5.  [Testen der App mit Mobile Services](#test-app)

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F).

Herunterladen des ProjektsHerunterladen des GetStartedWithData-Projekts
-----------------------------------------------------------------------

Dieses Lernprogramm baut auf der [GetStartedWithMobileServices-App](http://go.microsoft.com/fwlink/p/?LinkId=328660) auf. Dabei handelt es sich um ein Windows Store-App-Projekt in Visual Studio 2013. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die JavaScript-Version der GetStartedWithData-Beispiel-App von der Website mit den [Codebeispielen für Entwickler](http://go.microsoft.com/fwlink/p/?LinkId=328660) herunter.

   	![][10]

2.  Öffnen Sie das heruntergeladene Projekt in Visual Studio 2012 Express für Windows 8, erweitern Sie den Ordner **js** und sehen Sie sich die Datei "default.js" an.

   	Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **List**-Objekt gespeichert werden.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

   	![][0]  

   	Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

Erstellen des mobilen DienstsErstellen eines mobilen Diensts in Visual Studio
-----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="1">
<li><p>Erweitern Sie die Ordner **services**, **mobile services**, **&lt;your_service>** im Projektmappen-Explorer, öffnen Sie die Skriptdatei service.js und beachten Sie die neue globale Variable, die wie folgt aussieht:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
              "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über globale Variable bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Da Sie einen Verweis auf das Skript zur Datei default.html hinzugefügt haben, ist diese Variable in allen Skriptdateien verfügbar, die von dieser Seite aus referenziert werden.</p>
</li>
</ol>

Hinzufügen einer neuen TabelleHinzufügen einer neuen Tabelle zu dem mobilen Dienst und Aktualisieren der App
------------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  Kommentieren Sie in der Skriptdatei default.js die Zeile mit der Definition der vorhandenen Eintragssammlung aus, heben Sie dann die Auskommentierung der folgenden Codezeile auf und ersetzen Sie `<yourClient>;` durch die Variable, die beim Verbinden Ihres Projekts mit dem mobilen Dienst zur Datei service.js hinzugefügt wurde:

         var todoTable = <yourClient>.getTable('TodoItem');

   	Dieser Code erstellt ein Proxyobjekt (**todoTable**) für die neue Datenbanktabelle. 

2.  Ersetzen Sie die Funktion **InsertTodoItem** durch den folgenden Code:

         var insertTodoItem = function (todoItem) {
             // Fügt eine neue Zeile in die Datenbank ein. Wenn die Operation endet
            // und der mobile Dienst eine Id generiert hat, wird das Element zur bindenden Liste hinzugefügt.
             todoTable.insert(todoItem).done(function (item) {
                 todoItems.push(item);
             });
         };

    Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

    **Hinweis**

    Neue Tabellen enthalten bei der Erstellung lediglich eine Id-Spalte. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://msdn.microsoft.com/de-de/library/windowsazure/jj193175.aspx).

3.  Ersetzen Sie die Funktion **RefreshTodoItems** durch den folgenden Code:

         var refreshTodoItems = function () {
             // Dieser Code aktualisiert die Einträge in der Liste, indem die Tabelle abgefragt wird. 
             todoTable.read().done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };

   	Damit wird die Bindung an die Sammlung der Einträge in der todoTable festgelegt, die alle vom mobilen Dienst zurückgegebenen **TodoItem**-Objekte enthält. 

4.  Ersetzen Sie die Funktion **UpdateCheckedTodoItem** durch den folgenden Code:

         var updateCheckedTodoItem = function (todoItem) {
             // Dieser Code verwendet ein gerade abgeschlossenes TodoItem und aktualisiert die Datenbank. 
             todoTable.update(todoItem);
         };

   	Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

Testen der AppTesten der App mit Ihrem neuen mobilen Dienst
-----------------------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie wie zuvor Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

   	Dieser Code schickt ein neues einzufügendes Element an den mobilen Dienst.

3.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
          
   	Die **TodoItem**-Tabelle enthält nun Daten, deren ID-Werte vom mobilen Dienst generiert wurden, und die entsprechenden Spalten für die TodoItem-Klasse in der App wurden automatisch in der Tabelle erstellt.

5.  Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**.

   	Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

1.  Ersetzen Sie in der Projektdatei "default.js" die vorhandene **RefreshTodoItems**-Funktion durch den folgenden Code, mit dem abgeschlossene Einträge herausgefiltert werden:

         var refreshTodoItems = function () {                     
             // Erweiterte Abfragefilter zum Herausfiltern abgeschlossener Elemente. 
             todoTable.where({ complete: false })
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });            
         };

2.  Markieren Sie in der App einen anderen Eintrag in der Liste, und klicken Sie dann auf die Schaltfläche **Refresh**.

   	Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Aktualisierung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js)
  <br/>Informationen zur Verwendung von Serverskripts in mobilen Diensten, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging](/de-de/develop/mobile/tutorials/add-paging-to-data-js)
  <br/>Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-js)
  <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-js)
  <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz](/de-de/develop/mobile/how-to-guides/work-with-html-js-client/)
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.



<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png