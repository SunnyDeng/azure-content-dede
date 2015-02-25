<properties pageTitle="Verwenden von Serverskripts zum Überprüfen und Ändern von Daten (JavaScript) | Mobile Dev Center" description="Erfahren Sie, wie Sie Daten überprüfen und ändern, die mithilfe von Serverskripts von Ihrer Windows Store-JavaScript-App gesendet wurden." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga"/>

# Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts 

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre Windows Store-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Hinzufügen einer Überprüfung der Zeichenfolgenlänge]
2. [Aktualisieren des Clients zur Unterstützung der Überprüfung]
3. [Hinzufügen eines Zeitstempels beim Einfügen]
4. [Aktualisieren des Clients zum Anzeigen des Zeitstempels]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm, [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten] abschließen.  

## <a name="string-length-validation"></a>Hinzufügen einer Überprüfung

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App. 

   	![][0]

2. Klicken Sie auf die Registerkarte **Daten**, dann auf die Tabelle **TodoItem**.

   	![][1]

3. Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen** aus.

   	![][2]

4. Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Dieses Skript überprüft die Länge der **TodoItem.text**-Eigenschaft und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die **execute**-Methode aufgerufen, um den Einfügevorgang abzuschließen.

    > [AZURE.TIP] Sie können ein registriertes Skript auf der Registerkarte **Skript** entfernen, indem Sie auf **Löschen** und dann auf **Speichern** klicken.	

## <a name="update-client-validation"></a>Aktualisieren des Clients

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Nun müssen Sie Ihre App aktualisieren, damit sie Fehlerantworten der Überprüfung verarbeiten kann.

1. Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] geändert haben.

2. Drücken Sie die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit mehr als zehn Zeichen in das Feld **TodoItem einfügen** ein, und klicken Sie auf **Speichern**.

   	Beachten Sie, dass die App einen unbehandelten Fehler als Ergebnis der vom mobilen Dienst zurückgegebenen 400-Antwort (Bad Request) ausgibt.

6. 	Öffnen Sie die Datei "default.js", und ersetzen Sie die vorhandene **InsertTodoItem**-Methode durch den folgenden Code:

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

> [AZURE.NOTE] Die hier demonstrierte **createdAt**-Zeitstempeleigenschaft ist nun redundant. Mobile Services erstellt automatisch eine **__createdAt**-Systemeigenschaft für jede Tabelle.

1. Ersetzen Sie im [Verwaltungsportal] auf der Registerkarte **Skripts** das aktuelle **Insert**-Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Diese Funktion erweitert das vorherige Einfügeskript, indem eine neue **createdAt**-Zeitstempeleigenschaft zum Objekt hinzugefügt wird, bevor es durch den **request**.**execute**-Aufruf eingefügt wird. 

    > [AZURE.NOTE] Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte **createdAt** in die Tabelle **TodoItem** ein. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App im Windows Store veröffentlicht wird.

2. Drücken Sie in Visual Studio die Taste **F5**, um die App auszuführen, geben Sie dann einen Text mit weniger als zehn Zeichen in das Feld **TodoItem einfügen** ein, und klicken Sie auf **Speichern**.

   	Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3. Klicken Sie im Verwaltungsportal auf die Registerkarte **Durchsuchen** in der Tabelle **todoitem**.
   
   	Beachten Sie, dass es nun eine Spalte **createdAt** gibt, und dass das neu eingefügte Element über einen Zeitstempelwert verfügt.
  
Als Nächstes müssen Sie die Windows Store-App aktualisieren, um diese neue Spalte anzuzeigen.

## <a name="update-client-timestamp"></a>Erneutes Aktualisieren des Clients

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1. Öffnen Sie die Datei default.html in Visual Studio und fügen Sie das folgende HTML-Element im TemplateItem-Grid ein:
	      
        <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
            data-win-bind="innerText: createdAt"></div>  

   	Dadurch wird die neue **createdAt**-Eigenschaft angezeigt. 
	
6. Drücken Sie die Taste **F5**, um die App auszuführen. 

   	Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

7. Ersetzen Sie in der Datei "default.js" die vorhandene **RefreshTodoItems**-Methode durch den folgenden Code:

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
	
8. Drücken Sie die Taste **F5**, um die App auszuführen.

   	Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

* [Autorisieren von Benutzern mit Skripts]
  <br/>Erfahren Sie, wie Daten anhand der ID eines authentifizierten Benutzers gefiltert werden können.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. -->
[Hinzufügen einer Überprüfung der Zeichenfolgenlänge]: #string-length-validation
[Aktualisieren des Clients zur Unterstützung der Überprüfung]: #update-client-validation
[Hinzufügen eines Zeitstempels beim Einfügen]: #add-timestamp
[Aktualisieren des Clients zum Anzeigen des Zeitstempels]: #update-client-timestamp
[Nächste Schritte]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-js
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-js
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-js
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-js
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-js
[C# und XAML]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet

[Verwaltungsportal]: https://manage.windowsazure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=42-->
