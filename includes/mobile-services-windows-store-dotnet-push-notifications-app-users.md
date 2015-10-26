
Als Nächstes müssen Sie die Methode zur Registrierung von Pushbenachrichtigungen ändern. Dadurch stellen Sie sicher, dass der Benutzer vor einem Registrierungsversuch authentifiziert wird. Die Client-App wird entsprechend der Methode aktualisiert, mit der Sie Pushbenachrichtigungen implementiert haben.

###Verwenden des Assistenten zum Hinzufügen von Pushbenachrichtigungen in Visual Studio 2013 Update 2 oder einer höheren Version

Bei dieser Methode generiert der Assistent eine neue push.register.cs-Datei in Ihrem Projekt.

1. Öffnen Sie in Visual Studio im Projektmappen-Explorer die Projektdatei "app.xaml.cs", und kommentieren Sie im Ereignishandler**OnLaunched** den Aufruf der Methode **UploadChannel** aus, oder löschen Sie diesen. 

2. Öffnen Sie die Projektdatei "push.register.cs", und ersetzen Sie die Methode **UploadChannel** durch folgenden Code:

		public async static void UploadChannel()
		{
		    var channel = 
		        await Windows.Networking.PushNotifications.PushNotificationChannelManager
		        .CreatePushNotificationChannelForApplicationAsync();
		
		    try
		    {
		        // Create a native push notification registration.
		        await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);		        
		
		    }
		    catch (Exception exception)
		    {
		        HandleRegisterException(exception);
		    }
		}

	Damit wird sichergestellt, dass die Registrierung mithilfe derselben Clientinstanz erfolgt, die über die authentifizierten Benutzeranmeldeinformationen verfügt. Ansonsten verläuft die Registrierung mit einem 401-Fehler vom Typ "Nicht autorisiert" erfolglos.

3. Öffnen Sie die freigegebene Projektdatei „MainPage.cs“, und ersetzen den Ereignishandler **ButtonLogin\_Click** durch Folgendes:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
			todolistPush.UploadChannel();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }

	Dadurch wird sichergestellt, dass vor der Push-Registrierung eine Authentifizierung erfolgt.

4. 	Ersetzen Sie den generierten Push-Klassennamen (`todolistPush`) im vorigen Code durch den Namen der Klasse, die vom Assistenten generiert wurde (üblicherweise im Format <code><em>mobile\_service</em>Push</code>).

###Manuell aktivierte Pushbenachrichtigungen		

Bei dieser Methode fügen Sie Registrierungscode aus dem Lernprogramm direkt in die Projektdatei "app.xaml.cs" ein.

1. Öffnen Sie in Visual Studio im Projektmappen-Explorer die Projektdatei "app.xaml.cs", und kommentieren Sie im Ereignishandler**OnLaunched** den Aufruf von **InitNotificationsAsync** aus, oder löschen Sie diesen. 
 
2. Ändern Sie den Zugriff auf die **InitNotificationsAsync**-Methode von `private` in `public`, und fügen Sie den `static`-Modifizierer hinzu.

3. Öffnen Sie die freigegebene Projektdatei „MainPage.cs“, und ersetzen den Ereignishandler **ButtonLogin\_Click** durch Folgendes:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
			App.InitNotificationsAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
	
	Dadurch wird sichergestellt, dass vor der Push-Registrierung eine Authentifizierung erfolgt.

<!---HONumber=Oct15_HO3-->