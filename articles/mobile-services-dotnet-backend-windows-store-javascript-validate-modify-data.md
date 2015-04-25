<properties 
	pageTitle="Verwenden des .Net-Back-End zum Prüfen und Ändern von Daten (Windows Store) | Mobile Dev Center" description="Erfahren Sie, wie Sie Daten für Ihre Windows Store-JavaScript-App mit dem .NET-Back-End von Microsoft Azure Mobile Services überprüfen, ändern und vergrößern." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="wesmc"/>

# Prüfen und Ändern von Daten in Mobile Services mit .NET-Back-End

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

In diesem Thema erfahren Sie, wie Sie Code im Azure Mobile Services .NET-Back-End verwenden, um Daten zu prüfen und zu ändern. Der .NET-Back-End-Dienst ist ein HTTP-Dienst, der mit dem Web-API-Framework erstellt wird. Wenn Sie mit der durch das Web-API-Framework definierten  `ApiController`-Klasse vertraut sind, werden Sie die von Mobile Services bereitgestellte  `TableController`-Klasse intuitiv verstehen.  `TableController` wird von der  `ApiController`-Klasse abgeleitet und bietet zusätzliche Funktionen für eine Verknüpfung mit einer Datenbanktabelle. Sie kann verwendet werden, um Vorgänge in Bezug auf einzufügende und zu aktualisierende Daten auszuführen, einschließlich Überprüfen und Ändern von Daten, was in diesem Lernprogramm demonstriert wird. 

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Überprüfung der Zeichenfolgenlänge hinzufügen]
2. [Client zur Unterstützung der Überprüfung aktualisieren]
3. [Testlängenüberprüfung]
4. [Ein Zeitstempelfeld für CompleteDate hinzufügen]
5. [Client zum Anzeigen von CompleteDate aktualisieren]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte] oder [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte] oder [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/) abschließen.  

## <a name="string-length-validation"></a>Validierungscode zu Mobile Service hinzufügen

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Aktualisieren des Clients

Der mobile Dienst ist nun eingerichtet, um bei einer ungültigen Textlänge die Daten zu überprüfen und Fehlerantworten zu senden. Sie müssen nun Ihre App aktualisieren, damit diese Fehlerantworten aus der Überprüfung verarbeiten kann. Der Fehler wird vom  `IMobileServiceTable<TodoItem].InsertAsync()`-Aufruf der Client-App aufgefangen.

1. Navigieren Sie in Visual Studio im Fenster des Projektmappen-Explorers zum JavaScript-Clientprojekt, und erweitern Sie den Ordner **js**. Öffnen Sie dann die Datei "default.js".

2. Ersetzen Sie in der Datei "default.js" die vorhandene **insertTodoItem**-Funktion durch die folgende Funktionsdefinition:


        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoIte
	ms.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

   	Diese Version der Funktion umfasst eine Fehlerbehandlung. Außerdem werden  `MessageDialog` mit der Fehlermeldung aus der Antwort sowie der Statustext und Statuscode angezeigt.

## <a name="test-length-validation"></a>Testlängenüberprüfung

1. Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das JavaScript-Client-App-Projekt, und klicken Sie dann auf **Debug** und auf **Neue Instanz starten**.

2. Geben Sie den Text für einen neuen todo-Eintrag mit einer Länge von mehr als 10 Zeichen ein, und klicken Sie dann auf **Speichern**.

    ![][1]

3. Als Antwort auf den ungültigen Text erhalten Sie einen Meldungsdialog ähnlich dem folgenden.

    ![][2]

## <a name="add-timestamp"></a>Ein Zeitstempelfeld für CompleteDate hinzufügen


[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Client zum Anzeigen von CompleteDate aktualisieren

Abschließend muss der Client aktualisiert werden, um die neuen **completeDate**-Daten anzuzeigen. 


1. Öffnen Sie im Projektmappen-Explorer für Visual Studio im JavaScript-Client-Projekt die Datei "default.html". Ersetzen Sie das  `div`-Tagelement der Bindungsvorlage durch die unten angegebene Definition. Speichern Sie anschließend die Datei. Dadurch wird ein  `div`-Tag mit der innerText-Eigenschaft hinzugefügt, die an **completeDate** gebunden ist.
	      
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



2. Entfernen Sie in der Datei "default.js" die  `.Where`-Klauselfunktion in der vorhandenen **refreshTodoItems**-Funktion, sodass abgeschlossene "todoitems" in die Ergebnisse einbezogen werden.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listIte
	ms.winControl.itemDataSource = todoIte
	ms.dataSource;
                    });
            };


3. Aktualisieren Sie in der Datei "default.js" die **updateCheckedTodoItem**-Funktion wie folgt, sodass die Einträge nach einem Update aktualisiert sind und abgeschlossene Einträge nicht aus der Liste entfernt werden. Speichern Sie anschließend die Datei.	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. Klicken Sie in den Fenstern des Projektmappen-Explorers von Visual Studio mit der rechten Maustaste auf **Projektmappe**. Klicken Sie dann auf **Projektmappe neu erstellen**, um sowohl den Client als auch den .NET-Back-End-Dienst neu zu erstellen. Stellen Sie sicher, dass beide Projekte ohne Fehler erstellt werden.

    ![][3]
	
5. Drücken Sie die Taste **F5**, um die Client-App und den Dienst lokal auszuführen. Fügen Sie einige neue Einträge hinzu, und klicken Sie auf diese, um einige der Einträge als abgeschlossen zu markieren, damit der **CompleteDate**-Zeitstempel als aktualisiert angezeigt werden kann.

    ![][4]

6. Klicken Sie im Projektmappen-Explorer für Visual Studio mit der rechten Maustaste auf das todolist-Dienstprojekt, und klicken Sie auf **Veröffentlichen**. Veröffentlichen Sie Ihren .NET-Backend-Dienst unter Microsoft Azure mithilfe Ihrer Veröffentlichungseinstellungsdatei, die Sie vom Azure-Portal heruntergeladen haben.

7. Aktualisieren Sie die Datei "default.js" für das Clientprojekt, indem Sie die Verbindung zur Adresse des mobilen Diensts auskommentieren. Testen Sie die App bezüglich des .NET-Backend, das in Ihrem Azure-Konto gehostet wird.




## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

* [Dienstseitige Autorisierung von Benutzern]
  <br/>Erfahren Sie, wie Daten anhand der ID eines authentifizierten Benutzers gefiltert werden können.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. -->
[Überprüfung der Zeichenfolgenlänge hinzufügen]: #string-length-validation
[Client zur Unterstützung der Überprüfung aktualisieren]: #update-client-validation
[Testlängenüberprüfung]: #test-length-validation
[Ein Zeitstempelfeld für CompleteDate hinzufügen]: #add-timestamp
[Client zum Anzeigen von CompleteDate aktualisieren]: #update-client-timestamp
[Nächste Schritte]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Dienstseitige Autorisierung von Benutzern]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
[Erste Schritte]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Verwaltungsportal]: https://manage.windowsazure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library



<!--HONumber=42-->
