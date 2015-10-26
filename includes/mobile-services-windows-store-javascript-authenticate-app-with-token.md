
Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, bei der der Client bei jedem Starten der App sowohl den Identitätsanbieter als auch den mobilen Dienst kontaktieren muss. Diese Methode ist nicht nur ineffizient, sie kann auch zu nutzungsbezogenen Problemen führen, wenn eine große Anzahl von Kunden die App gleichzeitig starten sollte. Ein besserer Ansatz ist es daher, den von Mobile Services zurückgegebenen Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden.

>[AZURE.NOTE]Den von Mobile Services zurückgegebenen Authentifizierungstoken können Sie unabhängig davon, ob Sie die clientverwaltete oder die dienstverwaltete Authentifizierung verwenden, zwischenspeichern. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.

1. Ersetzen Sie in der Projektdatei "default.js" die vorhandene Funktion **Login** durch den folgenden Code:

        var credential = null;
        var vault = new Windows.Security.Credentials.PasswordVault();

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    // Create a credential for the returned user.
                    credential = new Windows.Security.Credentials
                        .PasswordCredential("Facebook", results.userId,
                        results.mobileServiceAuthenticationToken);
                    vault.add(credential);
                    userId = results.userId;

                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }

2. Ersetzen Sie die vorhandene Funktion **authenticate** durch den folgenden Code:

        var authenticate = function () {
            // Try to get a stored credential from the PasswordVault.                
            try{
                credential = vault.findAllByResource("Facebook").getAt(0);
            }
            catch (error) {
                // This is expected when there's no stored credential.
            }
            
            if (credential) {
                // Set the user from the returned credential.   
                credential.retrievePassword();
                client.currentUser = {
                    "userId": credential.userName,
                    "mobileServiceAuthenticationToken": credential.password
                };

                // Try to return an item now to determine if the cached credential has expired.
                todoTable.take(1).read()
                            .done(function () {
                                refreshTodoItems();
                            }, function (error) {
                                if (error.request.status === 401) {
                                    login(credential, vault).then(function () {
                                        if (!credential) {

                                            // Authentication failed, try again.
                                            authenticate();
                                        }
                                    });
                                }                                   
                            });
            } else {

                login().then(function () {
                    if (!credential) {
                        // Authentication failed, try again.
                        authenticate();
                    }
                });
            }
        }

	In dieser Version von **authenticate** versucht die App, auf den mobilen Dienst über Anmeldeinformationen zuzugreifen, die in **PasswordVault** gespeichert sind. Über eine einfache Abfrage wird sichergestellt, dass das gespeicherte Token nicht abgelaufen ist. Bei Rückgabe eines 401-Fehlers wird ein normaler anbieterbasierter Anmeldungsversuch unternommen. Dies erfolgt auch dann, wenn keine gespeicherten Anmeldeinformationen vorhanden sind.

3. Starten Sie die App zweimal neu.

	Beachten Sie, dass beim ersten Start die Anmeldung beim Anbieter wieder erforderlich ist. Beim zweiten Neustart jedoch werden die zwischengespeicherten Anmeldeinformationen verwendet, und die Anmeldung wird umgangen.

<!---HONumber=Oct15_HO3-->