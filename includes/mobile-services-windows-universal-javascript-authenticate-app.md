1.  Öffnen Sie die Projektdatei "default.js", und ersetzen Sie in der Methodenüberladung **app.OnActivated** den letzten Aufruf der **refreshTodoItems**-Methode durch den folgenden Code:

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

    Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die **login**-Methode übergebenen Wert auf einen der folgenden Werte: *microsoftaccount*, *twitter*, *google* oder *windowsazureactivedirectory*.

    > [WACOM.NOTE]Wenn Sie Ihre Windows Store-App-Paketinformationen mit Mobile Services registriert haben, sollten Sie die [Anmelde][Anmelde]methode aufrufen, indem Sie für *useSingleSignOn* den Wert **Wahr** angeben. Wenn Sie dies nicht tun, werden Ihre Benutzer jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird.

2.  Öffnen Sie im Windows Store-App-Projekt die Projektdatei "default.html", und fügen Sie unmittelbar vor dem Element, das die Schaltfläche **Save** definiert, folgendes **Button**-Element hinzu:

        <button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3.  Drücken Sie F5, um die App auszuführen und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden.

    Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

  [Anmelde]: http://go.microsoft.com/fwlink/p/?LinkId=322050
