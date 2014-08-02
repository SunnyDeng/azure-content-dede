<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Javascript Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

Prüfen und Ändern von Daten in Mobile Services mit .Net-Backend.
================================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows Store C#") [Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows Store JavaScript") [Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "JavaScript backend")

In diesem Thema erfahren Sie, wie Sie Code in den .Net Backend Windows Azure Mobile Services verwenden, um Daten zu prüfen und zu ändern. Der .Net-Backend-Dienst stellt einen HTTP-Dienst dar, der mit dem Web-API-Framework erstellt wird. Wenn Sie mit der `ApiController`-Klasse vertraut sind, die mit dem Web-API-Framework definiert wird, so ist die durch Mobile Services bereitgestellte `TableController`-Klasse überwiegend selbsterklärend. `TableController` wird von der `ApiController`-Klasse abgeleitet und bietet zusätzliche Funktionen für eine Verknüpfung über Schnittstellen mit einer Datenbanktabelle. Sie kann verwendet werden, um Vorgänge in Bezug auf einzufügende und zu aktualisierende Daten auszuführen, einschließlich Überprüfen und Ändern von Daten, was in diesem Lernprogramm demonstriert wird.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen](#string-length-validation)
2.  [Client zur Unterstützung der Überprüfung aktualisieren](#update-client-validation)
3.  [Testlängenüberprüfung](#test-length-validation)
4.  [Ein Zeitstempelfeld für CompleteDate hinzufügen](#add-timestamp)
5.  [Client zum Anzeigen von CompleteDate aktualisieren](#update-client-timestamp)

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/) oder [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/) oder [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/) abschließen.

Validierungscode zu Mobile Service hinzufügen
---------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

Den Client aktualisieren
------------------------

Der mobile Dienst ist nun eingerichtet, um bei einer ungültigen Textlänge die Daten zu überprüfen und Fehlerantworten zu senden. Sie müssen nun Ihre App aktualisieren, damit diese Fehlerantworten aus der Überprüfung verarbeiten kann. Der Fehler wird aus dem Aufruf der Client-App an `IMobileServiceTable<TodoItem].InsertAsync()` abgefangen.

1.  Navigieren Sie in Visual Studio im Fenster des Projektmappen-Explorers zum JavaScript-Clientprojekt, und erweitern Sie den Ordner **js**. Öffnen Sie dann die Datei "default.js".

2.  Ersetzen Sie in der "default.js" die vorhandene **insertTodoItem**-Funktion durch die folgende Funktionsdefinition:

         var insertTodoItem = function (todoItem) {
             // This code inserts a new TodoItem into the database. When the operation completes
             // and Mobile Services has assigned an id, the item is added to the Binding List.
             todoTable.insert(todoItem)
                 .then(function (item) {
                   todoItems.push(item);
                 },
                 function (error) {
                   var msgDialog =
                     new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                     error.request.statusText + "(" + error.request.status + ")");
                   msgDialog.showAsync();
                 });
         };

        Diese Version der Funktion umfasst eine Fehlerbehandlung, und es wird mit der Fehlermeldung ein `MessageDialog` aus der Antwort, aus dem Statustext und aus dem Statuscode angezeigt.

Testlängenüberprüfung
---------------------

1.  Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das JavaScript-Client-App-Projekt, und klicken Sie dann auf **Debug** und auf **Start new instance**.

2.  Geben Sie den Text für einen neuen todo-Eintrag mit einer Länge von mehr als 10 Zeichen ein, und klicken Sie dann auf **Save**.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png)

3.  Als Antwort auf den ungültigen Text erhalten Sie einen Meldungsdialog ähnlich dem folgenden.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png)

Ein Zeitstempelfeld für CompleteDate hinzufügen
-----------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

Client zum Anzeigen von CompleteDate aktualisieren
--------------------------------------------------

Abschließend muss der Client aktualisiert werden, um die neuen **CompleteDate**-Daten anzuzeigen.

1.  Öffnen Sie im Projektmappen-Explorer für Visual Studio im JavaScript-Client-Projekt die Datei "default.html". Ersetzen Sie das Tag-Element `div` der Verknüpfungsvorlage durch die unten angegebene Definition. Speichern Sie anschließend die Datei. Dadurch wird ein `div`-Tag mit der innerText-Eigenschaft hinzugefügt, welche mit **completeDate** verknüpft ist.

         <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
           <div style="display: -ms-grid; -ms-grid-columns: 3">
             <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
               dataContext: this" />
             <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
               data-win-bind="innerText: text">
             </div>
             <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
               data-win-bind="innerText: completeDate">
             </div>
           </div>
         </div>

2.  Entfernen Sie in der "default.js" die `.Where`-Klauselfunktion in der vorhandenen **refreshTodoItems**-Funktion, sodass abgeschlossene todoitems in die Ergebnisse einbezogen werden.

         var refreshTodoItems = function () {
                 // Dieser Code aktualisiert die Einträge in der Listenansicht, über die eine Abfrage der TodoItems-Tabelle erfolgt.
                 // Die Abfrage schließt abgeschlossene TodoItems aus.
                 todoTable.read()
                     .done(function (results) {
                         todoItems = new WinJS.Binding.List(results);
                         listItems.winControl.itemDataSource = todoItems.dataSource;
                     });
             };

3.  Aktualisieren Sie in der "default.js" die **updateCheckedTodoItem**-Funktion wie folgt, sodass die Einträge nach einem Update aktualisiert sind und abgeschlossene Einträge nicht aus der Liste entfernt werden. Speichern Sie anschließend die Datei.

         var updateCheckedTodoItem = function (todoItem) {
                 // This code takes a freshly completed TodoItem and updates the database. 
                 todoTable.update(todoItem).done(function () {
                     refreshTodoItems();
                 });
             };

4.  Klicken Sie in den Fenstern des Projektmappen-Explorers von Visual Studio mit der rechten Maustaste auf **Solution**. Klicken Sie dann auf **Rebuild Solution**, um sowohl den Client als auch den .NET-Backend-Dienst neu zu erstellen. Stellen Sie sicher, dass beide Projekte ohne Fehler erstellt werden.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png)

5.  Drücken Sie die Taste **F5**, um die Client-App und den Dienst lokal auszuführen. Fügen Sie einige neue Einträge hinzu, und klicken Sie auf diese, um einige der Einträge als abgeschlossen zu markieren, damit der **CompleteDate**-Zeitstempel als aktualisiert angezeigt werden kann.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png)

6.  Klicken Sie im Projektmappen-Explorer für Visual Studio mit der rechten Maustaste auf das todolist-Dienstprojekt, und klicken Sie auf **Veröffentlichen**. Veröffentlichen Sie Ihren .NET-Backend-Dienst unter Microsoft Azure mithilfe Ihrer Veröffentlichungseinstellungsdatei, die Sie vom Azure-Portal heruntergeladen haben.

7.  Aktualisieren Sie die Datei "default.js" für das Clientprojekt, indem Sie die Verbindung zur Adresse des mobilen Diensts auskommentieren. Testen Sie die App bezüglich des .NET-Backend, das in Ihrem Azure-Konto gehostet wird.

Nächste Schritte
----------------

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet).

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

-   [Dienstweite Autorisierung von Benutzern](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/)
    Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen](en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.


