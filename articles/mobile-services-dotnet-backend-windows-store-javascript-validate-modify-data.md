<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Javascript Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Prüfen und Ändern von Daten in Mobile Services mit .Net-Backend.

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#] [Windows Store JavaScript][Windows Store JavaScript] [Windows Phone][Windows Phone][iOS][iOS] [Android][Android] [HTML][HTML][Xamarin.iOS][Xamarin.iOS] [Xamarin.Android][Xamarin.Android]

</div>

<div class="dev-center-tutorial-subselector">

[.NET-Back-End][.NET-Back-End] | [JavaScript-Back-End][JavaScript-Back-End]

</div>

In diesem Thema erfahren Sie, wie Sie Code im Azure Mobile Services .NET-Back-End verwenden, um Daten zu prüfen und zu ändern. Der .Net-Backend-Dienst stellt einen HTTP-Dienst dar, der mit dem Web-API-Framework erstellt wird. Wenn Sie mit der durch das Web-API-Framework definierten `ApiController`-Klasse vertraut sind, werden Sie die von Mobile Services bereitgestellte `TableController`-Klasse intuitiv verstehen. `TableController` wird von der `ApiController`-Klasse abgeleitet und bietet zusätzliche Funktionen für eine Verknüpfung mit einer Datenbanktabelle. Sie kann verwendet werden, um Vorgänge in Bezug auf einzufügende und zu aktualisierende Daten auszuführen, einschließlich Überprüfen und Ändern von Daten, was in diesem Lernprogramm demonstriert wird.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen][Überprüfung der Zeichenfolgenlänge hinzufügen]
2.  [Client zur Unterstützung der Überprüfung aktualisieren][Client zur Unterstützung der Überprüfung aktualisieren]
3.  [Testlängenüberprüfung][Testlängenüberprüfung]
4.  [Ein Zeitstempelfeld für CompleteDate hinzufügen][Ein Zeitstempelfeld für CompleteDate hinzufügen]
5.  [Client zum Anzeigen von CompleteDate aktualisieren][Client zum Anzeigen von CompleteDate aktualisieren]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte][Erste Schritte] oder [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte][Erste Schritte] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen.

## <a name="string-length-validation"></a>Validierungscode zu Mobile Service hinzufügen

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation][mobile-services-dotnet-backend-add-validation]]

## <a name="update-client-validation"></a>Den Client aktualisieren

Der mobile Dienst ist nun eingerichtet, um bei einer ungültigen Textlänge die Daten zu überprüfen und Fehlerantworten zu senden. Sie müssen nun Ihre App aktualisieren, damit diese Fehlerantworten aus der Überprüfung verarbeiten kann. Der Fehler wird vom `IMobileServiceTable<TodoItem].InsertAsync()`-Aufruf der Client-App aufgefangen.

1.  Navigieren Sie in Visual Studio im Fenster des Projektmappen-Explorers zum JavaScript-Clientprojekt, und erweitern Sie den Ordner **js**. Öffnen Sie dann die Datei "default.js".

2.  Ersetzen Sie in der "default.js" die vorhandene **insertTodoItem**-Funktion durch die folgende Funktionsdefinition:

        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
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

    Diese Version der Funktion umfasst eine Fehlerbehandlung. Außerdem werden `MessageDialog` mit der Fehlermeldung aus der Antwort sowie der Statustext und Statuscode angezeigt.

## <a name="test-length-validation"></a>Testlängenüberprüfung

1.  Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das JavaScript-Client-App-Projekt, und klicken Sie dann auf **Debug** und auf **Start new instance**.

2.  Geben Sie den Text für einen neuen todo-Eintrag mit einer Länge von mehr als 10 Zeichen ein, und klicken Sie dann auf **Save**.

    ![][]

3.  Als Antwort auf den ungültigen Text erhalten Sie einen Meldungsdialog ähnlich dem folgenden.

    ![][1]

## <a name="add-timestamp"></a>Ein Zeitstempelfeld für CompleteDate hinzufügen

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate][mobile-services-dotnet-backend-add-completedate]]

## <a name="update-client-timestamp"></a>Client zum Anzeigen von CompleteDate aktualisieren

Abschließend muss der Client aktualisiert werden, um die neuen **CompleteDate**-Daten anzuzeigen.

1.  Öffnen Sie im Projektmappen-Explorer für Visual Studio im JavaScript-Client-Projekt die Datei "default.html". Ersetzen Sie das `div`-Tagelement der Bindungsvorlage durch die unten angegebene Definition. Speichern Sie anschließend die Datei. Dadurch wird ein `div`-Tag mit der innerText-Eigenschaft hinzugefügt, die an **completeDate** gebunden ist.

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

2.  Entfernen Sie in "default.js" die `.Where`-Klauselfunktion in der vorhandenen **refreshTodoItems**-Funktion, sodass abgeschlossene "todoitems" in die Ergebnisse einbezogen werden.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
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

    ![][2]

5.  Drücken Sie die Taste **F5**, um die Client-App und den Dienst lokal auszuführen. Fügen Sie einige neue Einträge hinzu, und klicken Sie auf diese, um einige der Einträge als abgeschlossen zu markieren, damit der **CompleteDate**-Zeitstempel als aktualisiert angezeigt werden kann.

    ![][3]

6.  Klicken Sie im Projektmappen-Explorer für Visual Studio mit der rechten Maustaste auf das todolist-Dienstprojekt, und klicken Sie auf **Veröffentlichen**. Veröffentlichen Sie Ihren .NET-Backend-Dienst unter Microsoft Azure mithilfe Ihrer Veröffentlichungseinstellungsdatei, die Sie vom Azure-Portal heruntergeladen haben.

7.  Aktualisieren Sie die Datei "default.js" für das Clientprojekt, indem Sie die Verbindung zur Adresse des mobilen Diensts auskommentieren. Testen Sie die App bezüglich des .NET-Backend, das in Ihrem Azure-Konto gehostet wird.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging][Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

-   [Dienstweite Autorisierung von Benutzern][Dienstweite Autorisierung von Benutzern]
    Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone"
  [iOS]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [.NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ ".NET-Back-End"
  [JavaScript-Back-End]: /de-de/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "JavaScript-Back-End"
  [Überprüfung der Zeichenfolgenlänge hinzufügen]: #string-length-validation
  [Client zur Unterstützung der Überprüfung aktualisieren]: #update-client-validation
  [Testlängenüberprüfung]: #test-length-validation
  [Ein Zeitstempelfeld für CompleteDate hinzufügen]: #add-timestamp
  [Client zum Anzeigen von CompleteDate aktualisieren]: #update-client-timestamp
  [Erste Schritte]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png
  [Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Dienstweite Autorisierung von Benutzern]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
