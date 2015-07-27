
Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, die erfordert, dass der Client bei jedem Start der App sowohl den Identitätsanbieter als auch den App Service kontaktiert. Diese Methode ist nicht nur ineffizient, sie kann auch zu nutzungsbezogenen Problemen führen, wenn eine große Anzahl von Kunden die App gleichzeitig starten sollte. Ein besserer Ansatz ist es daher, das vom App Service zurückgegebene Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden.

>[AZURE.NOTE]Unabhängig davon, ob Sie clientverwaltete oder dienstverwaltete Authentifizierung verwenden, können Sie den von App Service ausgestellten Authentifizierungstoken zwischenspeichern. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.

1. Fügen Sie in der Projektdatei "MainPage.xaml.cs" die folgenden **using**-Anweisungen hinzu:

		using System.Linq;		
		using Windows.Security.Credentials;

2. Ersetzen Sie die Methode **AuthenticateAsync** durch den folgenden Code:

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";
              
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            while (credential == null)
            {
                try
                {
                    // Try to get an existing credential from the vault.
                    credential = vault.FindAllByResource(provider).FirstOrDefault();
                }
                catch (Exception)
                {
                    // When there is no matching resource an error occurs, which we ignore.
                }

                if (credential != null)
                {
                    // Create a user from the stored credentials.
                    user = new MobileServiceUser(credential.UserName);
                    credential.RetrievePassword();
                    user.MobileServiceAuthenticationToken = credential.Password;
                    
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {                        
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            vault.Remove(credential);
                            credential = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);                        

                        // Create and store the user credentials.
                        credential = new PasswordCredential(provider,
                            user.UserId, user.MobileServiceAuthenticationToken);
                        vault.Add(credential);
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

	In dieser Version von **AuthenticateAsync** versucht die App, auf die mobile App über Anmeldeinformationen zuzugreifen, die in **PasswordVault** gespeichert sind. Über eine einfache Abfrage wird sichergestellt, dass das gespeicherte Token nicht abgelaufen ist. Bei Rückgabe eines 401-Fehlers wird ein normaler anbieterbasierter Anmeldungsversuch unternommen. Dies erfolgt auch dann, wenn keine gespeicherten Anmeldeinformationen vorhanden sind.

	>[AZURE.NOTE]Diese App überprüft während der Anmeldung auf abgelaufene Token, allerdings können Token auch nach der Authentifizierung ablaufen, wenn die App verwendet wird. Eine Lösung zur Behandlung von Autorisierungsfehlern in Zusammenhang mit abgelaufenen Token finden Sie im Beitrag [Caching and handling expired tokens in Azure Mobile Services managed SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx) (Zwischenspeichern und Behandeln von abgelaufenen Token mit dem Azure Mobile Services-SDK für verwalteten Code, in englischer Sprache).

3. Starten Sie die App zweimal neu.

	Beachten Sie, dass beim ersten Start die Anmeldung beim Anbieter wieder erforderlich ist. Beim zweiten Neustart jedoch werden die zwischengespeicherten Anmeldeinformationen verwendet, und die Anmeldung wird umgangen.

<!---HONumber=July15_HO3-->