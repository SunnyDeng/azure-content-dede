

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

Auf diese Weise wird eine Membervariable zum Speichern des aktuellen Benutzers sowie eine Methode zur Verarbeitung des Authentifizierungsprozesses erstellt. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert. Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die <strong>login</strong>-Methode übergebenen Wert auf einen der folgenden Werte: _microsoftaccount_, _twitter_, _google_, or _windowsazureactivedirectory_.

    >[WACOM.NOTE]Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registriert haben, sollten Sie die <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a>-Methode aufrufen, indem Sie für den <strong>useSingleSignOn</strong>-Parameter den Wert <em>true</em> übergeben. Wenn Sie dies nicht tun, werden Ihre Benutzer jedes Mal zur Anmeldung aufgefordert, wenn diese login-Methode aufgerufen wird.

2. Drücken Sie F5, um die App auszuführen und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden. 

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.
