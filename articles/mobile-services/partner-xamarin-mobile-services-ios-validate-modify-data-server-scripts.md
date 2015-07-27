<properties
	pageTitle="Verwenden von Serverskripts zum Prüfen und Ändern von Daten (Xamarin iOS) | Mobile Dev Center"
	description="Erfahren Sie, wie Sie Daten überprüfen und ändern, die mithilfe von Serverskripts von Ihrer Xamarin iOS-App gesendet wurden."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="ggailey777"/>

# Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../../includes/mobile-services-selector-validate-modify-data.md)]

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre iOS-App aktualisieren, um von diesen neuen Verhalten zu profitieren. Der komplette Code ist verfügbar in der [ValidateModifyData -Beispielapp][GitHub].

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Hinzufügen einer Überprüfung der Zeichenfolgenlänge]
2. [Aktualisieren des Clients zur Unterstützung der Überprüfung]
3. [Hinzufügen eines Zeitstempels beim Einfügen]
4. [Aktualisieren des Clients zum Anzeigen des Zeitstempels]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, sollten Sie zunächst [Erste Schritte mit Daten] abschließen.

## <a name="string-length-validation"></a>Hinzufügen einer Überprüfung

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

	![][0]

2. Klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

   	![][1]

3. Klicken Sie auf **Skript** und wählen Sie die **Einfügen**-Operation aus.

   	![][2]

4. Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

				function insert(item, user, request) {
        	if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
          } else {
            request.execute();
          }
        }

    Das Skript überprüft die Länge der Eigenschaft **text** und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die **execute**-Methode aufgerufen, um das Einfügen abzuschließen.

    > [AZURE.NOTE]Sie können ein registriertes Skript auf der Registerkarte **Skript** entfernen, indem Sie auf **Löschen** und dann auf **Speichern** klicken.

## <a name="update-client-validation"></a>Aktualisieren des Clients

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Nun müssen Sie Ihre App aktualisieren, damit sie Fehlerantworten der Überprüfung verarbeiten kann.

1. Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] geändert haben.

2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text mit mehr als zehn Zeichen in das Textfeld ein, und klicken Sie auf das Plus-Symbol (**+**).

	Beachten Sie, dass die App einen unbehandelten Fehler als Ergebnis der vom mobilen Dienst zurückgegebenen 400-Antwort (Bad Request) ausgibt.

3. Suchen Sie in der Datei "TodoService.cs" nach der aktuellen <code>try/catch</code>-Ausnahmebehandlung in der **InsertTodoItemAsync**-Methode, und ersetzen Sie <code>catch</code> durch Folgendes:

    catch (Exception ex) { var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException); Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() {
            	Title = "Error",
            	Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
		}

	Nun öffnet sich ein Popup-Fenster, in dem dem Benutzer der Fehler angezeigt wird.

4. Suchen Sie **OnAdd**-Methode in **TodoListViewController.cs**. Aktualisieren Sie die Methode, damit der zurückgegebene <code>index</code> nicht <code>-1</code> ist (wie in der zurückgegebenen Ausnahmebehandlung in **InsertTodoItemAsync**). In diesem Fall fügen wir keine neue Zeile in <code>TableView</code> ein.

    if (index != -1) { TableView.InsertRows(new { NSIndexPath.FromItemSection(index, 0) }, UITableViewRowAnimation.Top); itemText.Text = ""; }


5. Erstellen und starten Sie die App.

	![][4]

	Beachten Sie, dass der Fehler behandelt und die Fehlermeldung für den Benutzer angezeigt wird.


## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem abschließenden Lernprogramm der Reihe zu Daten fortfahren: [Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

* [Autorisieren von Benutzern mit Skripts] <br/>Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

* [Erste Schritte mit Pushbenachrichtigungen] <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

* [Mobile Services: Serverskriptreferenz] <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. -->
[Hinzufügen einer Überprüfung der Zeichenfolgenlänge]: #string-length-validation
[Aktualisieren des Clients zur Unterstützung der Überprüfung]: #update-client-validation
[Hinzufügen eines Zeitstempels beim Einfügen]: #add-timestamp
[Aktualisieren des Clients zum Anzeigen des Zeitstempels]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Autorisieren von Benutzern mit Skripts]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Optimieren von Abfragen mit Paging]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Erste Schritte mit Daten]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit Pushbenachrichtigungen]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Management Portal]: https://manage.windowsazure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330
 

<!---HONumber=July15_HO3-->