<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012
==================================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-data-js-vs2012 "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/de-de/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Store-App nutzen können. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Speicher ablegt, einen neuen mobilen Dienst erstellt, den mobilen Dienst mit der App integriert und melden sich anschließend im Azure-Verwaltungsportal an, um die Änderungen an den Daten beim Ausführen der App anzuzeigen.

**Hinweis**

Durch dieses Lernprogramm werden Mobile Services-Funktionen einer in Visual Studio 2012 erstellten Windows Store-App hinzugefügt. Visual Studio 2013 umfasst neue Funktionen, die das Verknüpfen Ihrer Windows Store-App mit Mobile Services vereinfachen. Weitere Informationen finden Sie unter [Erste Schritte mit Daten in Mobile Services](/de-de/develop/mobile/tutorials/get-started-with-data-js/).

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Store-App-Projekts](#download-app)
2.  [Erstellen des mobilen Diensts](#create-service)
3.  [Erstellen einer Datentabelle als Datenspeicher](#add-table)
4.  [Aktualisieren der App zur Verwendung von mobilen Diensten](#update-app)
5.  [Testen der App mit Mobile Services](#test-app)

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie hier [Kostenloses Azure-Testkonto](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F).

Herunterladen des ProjektsHerunterladen des GetStartedWithData-Projekts
-----------------------------------------------------------------------

Dieses Lernprogramm baut auf der [GetStartedWithData](http://go.microsoft.com/fwlink/?LinkId=262308)-App auf, bei der es sich um eine Windows Store-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die JavaScript-Version der GetStartedWithData-Beispiel-App von der Website mit den [Codebeispielen für Entwickler](http://go.microsoft.com/fwlink/?LinkId=262308) herunter.

	![][10]

2.  Öffnen Sie das heruntergeladene Projekt in Visual Studio 2012 Express für Windows 8, erweitern Sie den Ordner **js** und sehen Sie sich die Datei "default.js" an.

	Notice that added **TodoItem** objects are stored in an in-memory **List** object.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

	![][0]  

 	Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

Erstellen des mobilen DienstesErstellen eines neuen mobilen Dienstes im Verwaltungsportal
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Erstellen einer neuen TabelleFügen Sie eine neue Tabelle zum mobilen Dienst hinzu
---------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Aktualisieren der AppAktualisieren der App für den Datenzugriff über mobile Dienste
-----------------------------------------------------------------------------------

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass diese Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online** aus, suchen Sie nach `WindowsAzure.MobileServices.WinJS`, klicken Sie auf **Installieren** im Paket **Azure Mobile Services for WinJS**, und stimmen Sie dem Lizenzvertrag zu.

	![](./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png)

	Dadurch wird die Mobile Services-Clientbibliothek zum Projekt hinzugefügt.

1.  Fügen Sie in der Projektdatei "default.html" folgenden Skriptverweis im Seitenkopf hinzu:

         <script type="text/javascript" src="/js/MobileServices.js"></script>

2.  Klicken Sie im Verwaltungsportal auf **Mobile Dienste** und klicken Sie auf den zuvor erstellten mobilen Dienst.

3.  Klicken Sie die Registerkarte **Dashboard** und notieren Sie die **Site-URL**. Klicken Sie anschließend auf **Schlüssel verwalten** und notieren Sie den **Anwendungsschlüssel**.

	![][8]

	Sie brauchen diese Werte für den Zugriff auf den mobilen Dienst in Ihrem App-Code.

1.  Öffnen Sie die Datei "default.js" in Visual Studio und fügen Sie den folgenden Code hinzu, um die **client**-Variable zu definieren und übergeben Sie Anwendungs-URL und -Schlüssel in dieser Reihenfolge an den **MobileServiceClient**-Konstruktor.

             var client = new WindowsAzure.MobileServiceClient(
                 "AppUrl",
                 "appKey"
             );

	Dieser Code erstellt eine neue MobileServiceClient-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird.

1.  Entfernen Sie die Kommentare in den folgenden Codezeilen:

         var todoTable = client.getTable('TodoItem');

        This code creates a proxy object (**todoTable**) for the new database table. 

2.  Ersetzen Sie die Funktion **InsertTodoItem** durch den folgenden Code:

         var insertTodoItem = function (todoItem) {
             // Inserts a new row into the database. When the operation completes
             // and Mobile Services has assigned an id, the item is added to the binding list.
             todoTable.insert(todoItem).done(function (item) {
                 todoItems.push(item);
             });
         };

	Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

1.  Ersetzen Sie die Funktion **RefreshTodoItems** durch den folgenden Code:

         var refreshTodoItems = function () {
             // This code refreshes the entries in the list by querying the TodoItems table. 
             todoTable.read().done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };      

        Damit wird die Bindung auf die Sammlung von Objekten in der todoTable festgelegt, die alle abgeschlossenen Objekte enthält, die vom mobilen Dienst zurückgegeben werden. 

2.  Ersetzen Sie die Funktion **UpdateCheckedTodoItem** durch den folgenden Code:

         var updateCheckedTodoItem = function (todoItem) {
             // This code takes a freshly completed TodoItem and updates the database. 
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
          
	Die **TodoItem**-Tabelle enthält nun Daten, mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

5.  Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**.

	Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

1.  Ersetzen Sie in der Projektdatei "default.js" die vorhandene **RefreshTodoItems**-Funktion durch den folgenden Code, mit dem abgeschlossene Einträge herausgefiltert werden:

         var refreshTodoItems = function () {                     
             // More advanced query that filters out completed items. 
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
    Informationen zur Verwendung von Serverskripts in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging](/de-de/develop/mobile/tutorials/add-paging-to-data-js)
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-js)
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-js-vs2012)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.


<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png






[7]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
[8]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png


<!-- URLs. -->
[Validate and modify data with scripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Refine queries with paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-js
[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /de-de/develop/mobile/tutorials/get-started-with-push-js-vs2012
[C# and XAML]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/?LinkId=262308