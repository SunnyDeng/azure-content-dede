<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-js" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#] [Windows Store JavaScript][Windows Store JavaScript] [Windows Phone][Windows Phone][iOS][iOS] [Android][Android] [HTML][HTML] [Xamarin.iOS][Xamarin.iOS] [Xamarin.Android][Xamarin.Android]

</div>

<div class="dev-center-tutorial-subselector">

[.NET-Back-End][.NET-Back-End] | [JavaScript-Back-End][JavaScript-Back-End]

</div>

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre Windows Store-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen][Überprüfung der Zeichenfolgenlänge hinzufügen]
2.  [Client zur Unterstützung der Überprüfung aktualisieren][Client zur Unterstützung der Überprüfung aktualisieren]
3.  [Zeitstempel beim Einfügen hinzufügen][Zeitstempel beim Einfügen hinzufügen]
4.  [Client zum Anzeigen des Zeitstempels aktualisieren][Client zum Anzeigen des Zeitstempels aktualisieren]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen.

## <a name="string-length-validation"></a>Überprüfung hinzufügen

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][]

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

    ![][1]

3.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen**.

    ![][2]

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Das Skript überprüft die Länge der Eigenschaft **TodoItem.text** und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die Methode **execute** aufgerufen, um das Einfügen abzuschließen.

    <div class="dev-callout">

    **Hinweis**
    Sie können ein registriertes Skript auf der Registerkarte **Script** entfernen, indem Sie auf **Clear** und dann auf **Save** klicken.

    </div>

## <a name="update-client-validation"></a>Den Client aktualisieren

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Nun müssen Sie Ihre App aktualisieren, damit sie Fehlerantworten der Überprüfung verarbeiten kann.

1.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten] abgeschlossen haben.

2.  Drücken Sie die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit mehr als zehn Zeichen in **Insert a TodoItem** ein, und klicken Sie auf **Save**.

    Beachten Sie, dass die App einen unbehandelten Fehler als Ergebnis der vom mobilen Dienst zurückgegebenen 400-Antwort (Bad Request) ausgibt.

3.  Öffnen Sie die Datei default.js, und ersetzen Sie die vorhandene **InsertTodoItem**-Methode durch den folgenden Code:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            }, function (error) {
                // Create the error message dialog and set its content to the error
                // message contained in the response.
                var msg = new Windows.UI.Popups.MessageDialog(
                    error.request.responseText);
                msg.showAsync();
            });
        };

    Diese Version der Methode enthält Code zur Fehlerbehandlung, und die Fehlermeldung wird in einem Dialog angezeigt.

## <a name="add-timestamp"></a>Hinzufügen eines Zeitstempels

In der vorherigen Aufgabe wurde eine Einfügung überprüft und entweder akzeptiert oder abgelehnt. Nun aktualisieren Sie die eingefügten Daten mithilfe eines Serverskripts, das eine Zeitstempeleigenschaft zu dem Objekt hinzufügt, bevor es eingefügt wird.

<div class="dev-callout">

**Hinweis**
Die hier demonstrierte Zeitstempeleigenschaft **createdAt** ist nun redundant. Mobile Services erstellt automatisch eine Systemeigenschaft **\_\_createdAt** für jede Tabelle.

</div>

1.  Ersetzen Sie im [Verwaltungsportal][Azure-Verwaltungsportal] auf der Registerkarte **Scripts** das aktuelle Skript **Insert** durch die folgende Funktion und klicken Sie dann auf **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Diese Funktion erweitert das vorherige insert-Skript, indem es eine neue Zeitstempeleigenschaft **createdAt** zum Objekt hinzufügt, bevor es durch den **request**.**execute**-Aufruf eingefügt wird.

    <div class="dev-callout">

    **Hinweis**
    Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services automatisch die Spalte **createdAt** in die Tabelle **TodoItem** bei der ersten Ausführung ein. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App im Windows Store veröffentlicht wird.

    </div>

2.  Drücken Sie in Visual Studio die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit weniger als zehn Zeichen in **Insert a TodoItem** ein, und klicken Sie auf **Save**.

    Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Browse** in der Tabelle **todoitem**.

    Beachten Sie, dass es nun ein Spalte **createdAt** gibt und dass das neu eingefügte Element über einen Zeitstempelwert verfügt.

Als Nächstes müssen Sie die Windows Store-App aktualisieren, um diese neue Spalte anzuzeigen.

## <a name="update-client-timestamp"></a>Den Client erneut aktualisieren

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1.  Öffnen Sie die Datei default.html in Visual Studio und fügen Sie das folgende HTML-Element im TemplateItem-Grid ein:

        <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
            data-win-bind="innerText: createdAt"></div>  

    Dadurch wird die neue **createdAt**-Eigenschaft angezeigt.

2.  Drücken Sie **F5**, um die App auszuführen.

    Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

3.  Ersetzen Sie in default.js die vorhandene Methode **RefreshTodoItems** durch den folgenden Code:

        var refreshTodoItems = function () {
            // More advanced query that filters out completed items. 
            todoTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            })
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

    Diese Methode aktualisiert die Abfrage, damit auch Elemente herausgefiltert werden, die nicht über einen Zeitstempelwert verfügen.

4.  Drücken Sie **F5**, um die App auszuführen.

    Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging][Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

-   [Autorisieren von Benutzern mit Skripts][Autorisieren von Benutzern mit Skripts]
    Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript"
  [Windows Phone]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [.NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ ".NET-Back-End"
  [JavaScript-Back-End]: /de-de/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "JavaScript-Back-End"
  [Überprüfung der Zeichenfolgenlänge hinzufügen]: #string-length-validation
  [Client zur Unterstützung der Überprüfung aktualisieren]: #update-client-validation
  [Zeitstempel beim Einfügen hinzufügen]: #add-timestamp
  [Client zum Anzeigen des Zeitstempels aktualisieren]: #update-client-timestamp
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-js
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-js
  [Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-js
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-js
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
