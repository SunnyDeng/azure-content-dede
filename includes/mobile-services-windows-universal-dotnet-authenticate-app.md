
1. Öffnen Sie die freigegebene Projektdatei "MainPage.cs", und fügen Sie der MainPage-Klasse den folgenden Codeausschnitt hinzu:
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

		// Define a method that performs the authentication process
		// using a Facebook sign-in. 
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
					// Change 'MobileService' to the name of your MobileServiceClient instance.
					// Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Dieser Benutzer wird mithilfe eines Facebook-Logins authentifiziert. Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für **MobileServiceAuthenticationProvider** oben entsprechend Ihrem Anbieter.

3. Löschen Sie in der vorhandenen Methodenüberschreibung von **OnNavigatedTo** den Aufruf der Methode **RefreshTodoItems**, oder kommentieren Sie diesen Aufruf aus.

	Dadurch wird sichergestellt, dass die Daten erst geladen werden, nachdem der Benutzer authentifiziert wurde.

	>[AZURE.NOTE]Um sich aus einer App für Windows Phone Store 8.1 erfolgreich zu authentifizieren, müssen Sie LoginAsync aufrufen, nachdem die **OnNavigated**-Methode aufgerufen wurde und nachdem das **Loaded**-Ereignis der Seite ausgelöst wurde. In diesem Lernprogramm wird der App dazu die Schaltfläche **Sign in** hinzugefügt.

4. Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
5. Öffnen Sie im Windows Store-App-Projekt die Projektdatei "MainPage.xaml", und fügen Sie unmittelbar vor dem Element, das die Schaltfläche **Save** definiert, folgendes **Button**-Element hinzu:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Öffnen Sie im Windows Phone-App-Projekt die folgenden **Button**-Elemente unmittelbar vor dem Element hinzu, das die Schaltfläche **Speichern** definiert:

		<Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible" Margin="10, 0, 0, 0">Sign in</Button> 

5. Öffnen Sie die gemeinsam genutzte Projektdatei "App.xaml.cs", und fügen Sie die folgende using-Anweisung hinzu, sofern sie nicht bereits vorhanden ist:

        using Microsoft.WindowsAzure.MobileServices;  
 
6. Fügen Sie in der Projektdatei "App.xaml.cs" folgenden Code hinzu:

        protected override void OnActivated(IActivatedEventArgs args)
        {
			// Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
				// Completes the sign-in process started by LoginAsync.
				// Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

	Wenn die **OnActivated**-Methode bereits vorhanden ist, fügen Sie lediglich den Codeabschnitt `#if...#endif` hinzu.

8. Drücken Sie F5, um die Windows Store-App auszuführen, und klicken Sie auf die Schaltfläche **Sign in**, und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden.

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

9. Klicken Sie mit der rechten Maustaste auf das Windows Phone Store-App-Projekt, klicken Sie auf **Als Startprojekt festlegen**, und führen Sie dann den obigen Schritt erneut aus, um sicherzustellen, dass die Windows Phone Store-App ebenfalls ordnungsgemäß ausgeführt wird.

<!---HONumber=Oct15_HO3-->