

1. Öffnen Sie die Projektdatei "default.js", und ersetzen Sie in der **app.OnActivated**-Methodenüberladung den letzten Aufruf der **refreshTodoItems**-Methode durch den folgenden Code: 
	
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
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

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    Auf diese Weise wird eine Membervariable zum Speichern des aktuellen Benutzers sowie eine Methode zur Verarbeitung des Authentifizierungsprozesses erstellt. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert. Wenn Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den oben für <strong>Anmeldung</strong> eingegebenen Wert auf einen der folgenden: _microsoftaccount_, _twitter_, _google_ oder _windowsazureactivedirectory_.

    >[AZURE.NOTE]Wenn Sie Ihre Windows Store-App-Paketinformationen mit Mobile Services registriert haben, sollten Sie die Methode <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> aufrufen, indem Sie für <em>useSingleSignOn</em> den Wert <strong>Wahr</strong> angeben. Wenn Sie dies nicht tun, werden Ihre Benutzer jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird.

2. Drücken Sie F5, um die App auszuführen und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden. 

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.
\<!--HONumber=42-->
