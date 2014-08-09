<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-js" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" solutions="" manager="" editor="" />

Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts
=============================================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#") [Windows Store JavaScript](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript") [Windows Phone](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML") [Xamarin.iOS](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")
[.NET-Backend](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ ".NET-Backend") | [JavaScript-Backend](de-de/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "JavaScript-Backend")

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre Windows Store-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen](#string-length-validation)
2.  [Client zur Unterstützung der Überprüfung aktualisieren](#update-client-validation)
3.  [Zeitstempel beim Einfügen hinzufügen](#add-timestamp)
4.  [Client zum Anzeigen des Zeitstempels aktualisieren](#update-client-timestamp)

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-js). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-js) abschließen.

Überprüfung hinzufügen
----------------------

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

      ![](./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

      ![](./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  Klicken Sie auf **Skript** und wählen Sie die **Einfügen**-Operation aus.

      ![](./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 request.execute();
             }
         }

    Das Skript überprüft die Länge der Eigenschaft **TodoItem.text** und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die Methode **execute** aufgerufen, um das Einfügen abzuschließen.

    **Hinweis**

    Sie können ein registriertes Skript auf der Registerkarte **Script** entfernen, indem Sie auf **Clear** und dann auf **Save** klicken.

Aktualisieren des Clients
-------------------------

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Nun müssen Sie Ihre App aktualisieren, damit sie Fehlerantworten der Überprüfung verarbeiten kann.

1.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-js) abgeschlossen haben.

2.  Drücken Sie die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit mehr als zehn Zeichen in **Insert a TodoItem** ein, und klicken Sie auf **Save**.

     Beachten Sie, dass die App einen unbehandelten Fehler als Ergebnis der vom mobilen Dienst zurückgegebenen 400-Antwort (Bad Request) ausgibt.

3.  Öffnen Sie die Datei default.js, und ersetzen Sie die vorhandene **InsertTodoItem**-Methode durch den folgenden Code:

        var insertTodoItem = function (todoItem) {
             // Fügt eine neue Zeile in die Datenbank ein. Wenn die Operation endet
            // und der mobile Dienst eine Id generiert hat, wird das Element zur bindenden Liste hinzugefügt.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            }, function (error) {
                // Fehlermeldungs-Dialog erstellen und die Fehlermeldung
                // aus der Antwort als Inhalt setzen.
                var msg = new Windows.UI.Popups.MessageDialog(
                    error.request.responseText);
                msg.showAsync();
            });
        };

    Diese Version der Methode enthält Code zur Fehlerbehandlung, und die Fehlermeldung wird in einem Dialog angezeigt.

Hinzufügen eines Zeitstempels
-----------------------------

In der vorherigen Aufgabe wurde eine Einfügung überprüft und entweder akzeptiert oder abgelehnt. Nun aktualisieren Sie die eingefügten Daten mithilfe eines Serverskripts, das eine Zeitstempeleigenschaft zu dem Objekt hinzufügt, bevor es eingefügt wird.

**Hinweis**

Die hier demonstrierte Zeitstempeleigenschaft **createdAt** ist nun redundant. Mobile Services erstellt automatisch eine Systemeigenschaft **\_\_createdAt** für jede Tabelle.

1.  Ersetzen Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf der Registerkarte **Scripts** das aktuelle Skript **Insert** durch die folgende Funktion und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Diese Funktion erweitert das vorherige insert-Skript, indem es eine neue Zeitstempeleigenschaft **createdAt** zum Objekt hinzufügt, bevor es durch den **request**.**execute**-Aufruf eingefügt wird.

    **Hinweis**

    Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services automatisch die Spalte **createdAt** in die Tabelle **TodoItem** bei der ersten Ausführung ein. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App im Windows Store veröffentlicht wird.

2.  Drücken Sie in Visual Studio die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit weniger als zehn Zeichen in **Insert a TodoItem** ein, und klicken Sie auf **Save**.

     Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Browse** in der Tabelle **todoitem**.

     Beachten Sie, dass es hier nun ein Spalte **createdAt** gibt und das neu eingefügte Element über einen Zeitstempelwert verfügt.

Als Nächstes müssen Sie die Windows Store-App aktualisieren, um diese neue Spalte anzuzeigen.

Den Client erneut aktualisieren
-------------------------------

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1.  Öffnen Sie die Datei default.html in Visual Studio und fügen Sie das folgende HTML-Element im TemplateItem-Grid ein:

         <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
             data-win-bind="innerText: createdAt"></div>  

      Zeigt die neue **createdAt**-Eigenschaft an. 

2.  Drücken Sie **F5**, um die App auszuführen.

      Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

3.  Ersetzen Sie in default.js die vorhandene Methode **RefreshTodoItems** durch den folgenden Code:

         var refreshTodoItems = function () {                     
             // Erweiterte Abfragefilter zum Herausfiltern abgeschlossener Elemente. 
             todoTable.where(function () {
                 return (this.complete === false && this.createdAt !== null);
             })
             .read()
             .done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };

      Diese Funktion aktualisiert die Abfrage, damit auch Elemente herausgefiltert werden, die nicht über einen Zeitstempelwert verfügen.

4.  Drücken Sie **F5**, um die App auszuführen.

      Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

Nächste Schritte
----------------

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging](/de-de/develop/mobile/tutorials/add-paging-to-data-js).

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

-   [Autorisieren von Benutzern mit Skripts](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-js)
    <br/>Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-js)
    <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.


