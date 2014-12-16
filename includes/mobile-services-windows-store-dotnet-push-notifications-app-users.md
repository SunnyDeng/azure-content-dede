
Als Nächstes müssen Sie die Methode zur Registrierung von Pushbenachrichtigungen ändern, um sicherzustellen, dass der Benutzer vor einem Registrierungsversuch authentifiziert wird. Die Client-App wird entsprechend der Methode aktualisiert, mit der Sie Pushbenachrichtigungen implementiert haben.

###Verwenden des Assistenten zum Hinzufügen von Pushbenachrichtigungen in Visual Studio 2013 Update 2 oder einer höheren Version

Bei dieser Methode generiert der Assistent eine neue push.register.cs-Datei in Ihrem Projekt.

>[WACOM.NOTE]Der Assistent zum Hinzufügen von Pushbenachrichtigungen wird derzeit nur für mobile .NET-Back-End-Dienste unterstützt.

1. Öffnen Sie in Visual Studio im Projektmappen-Explorer die Projektdatei "app.xaml.cs", und kennzeichnen Sie im **OnLaunched**-Ereignishandler den Aufruf der **UploadChannel**-Methode als Kommentar, oder löschen Sie diesen. 

2. Öffnen Sie die Projektdatei "push.register.cs", und ersetzen Sie die **UploadChannel**-Methode durch folgenden Code:

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

	Damit wird sichergestellt, dass die Registrierung mithilfe derselben Clientinstanz erfolgt, die über die authentifizierten Benutzeranmeldeinformationen verfügt. Andernfalls schlägt die Registrierung mit einem 401-Fehler vom Typ "Nicht autorisiert" fehl.

3. Öffnen Sie die Projektdatei "MainPage.xaml.cs", und ersetzen Sie die **OnNavigatedTo**-Methodenüberschreibung durch folgenden Code:

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            todolistPush.UploadChannel();
            RefreshTodoItems();
        }

	In diesem Code müssen Sie den generierten Pushklassennamen (todolistPush) durch den Namen der Klasse ersetzen, die vom Assistenten generiert wurde und in der Regel das Format <code><em>mobile_service</em>Push</code> hat.

###Manuell aktivierte Pushbenachrichtigungen		

Bei dieser Methode fügen Sie Registrierungscode aus dem Lernprogramm direkt in die Projektdatei "app.xaml.cs" ein.

1. Öffnen Sie in Visual Studio im Projektmappen-Explorer die Projektdatei "app.xaml.cs", und kennzeichnen Sie im **OnLaunched**-Ereignishandler den Aufruf der **InitNotificationsAsync**-Methode als Kommentar, oder löschen Sie diesen. 
 
2. Ändern Sie den Zugriff auf die **InitNotificationsAsync**-Methode von "private" in "public", und fügen Sie den Modifizierer "static" hinzu. 

3. Öffnen Sie die Projektdatei "MainPage.xaml.cs", und ersetzen Sie die **OnNavigatedTo**-Methodenüberschreibung durch folgenden Code:

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.InitNotificationsAsync();
            RefreshTodoItems();
        }
