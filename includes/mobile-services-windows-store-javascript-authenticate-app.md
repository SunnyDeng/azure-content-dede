

1.  Öffnen Sie die Projektdatei "default.js", und ersetzen Sie in der Methodenüberladung **app.OnActivated** den letzten Aufruf der **refreshTodoItems**-Methode durch den folgenden Code:

         var userId = null;

         // Fordern Sie die Authentifizierung über Mobile Services mithilfe eines Facebook-Logins an.
         var login = function () {
             return new WinJS.Promise(function (complete) {
                 client.login("facebook").done(function (results) {
                     userId = results.userId;
                     refreshTodoItems();
                     var message = "You are now logged in as: " + userId;
                     var dialog = new Windows.UI.Popups.MessageDialog(message);
                     dialog.showAsync().done(complete);
                 }, function (error) {
                     userId = null;
                     var dialog = new Windows.UI.Popups
                         .MessageDialog("An error occurred during login", "Login Required");
                     dialog.showAsync().done(complete);
                 });
             });
         }            

         var authenticate = function () {
             login().then(function () {
                 if (userId === null) {

                     // Authentifizierung fehlgeschlagen, erneut versuchen.
                     authenticate();
                 }
             });
         }

         authenticate();

    Auf diese Weise wird eine Membervariable zum Speichern des aktuellen Benutzers erstellt und eine Methode zur Verarbeitung des Authentifizierungsprozesses. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert. Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die **login**-Methode übergebenen Wert auf einen der folgenden Werte: *microsoftaccount*, *twitter* oder *google*.

    > [WACOM.NOTE]Wenn Sie Ihre Windows Store-App-Paketinformationen mit Mobile Services registriert haben, sollten Sie die [Anmelde](http://go.microsoft.com/fwlink/p/?LinkId=322050)methode aufrufen, indem Sie für *useSingleSignOn* den Wert **Wahr** angeben. Wenn Sie dies nicht tun, werden Ihre Benutzer jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird.

2.  Drücken Sie F5, um die App auszuführen und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden.

      Wenn Sie erfolgreich angemeldet sind, sollte die App ohne Fehler ausgeführt werden, und Sie sollten in der Lage sein, Mobile Services abzufragen und Daten zu aktualisieren.


