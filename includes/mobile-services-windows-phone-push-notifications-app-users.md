
Als Nächstes müssen Sie die Art ändern, wie Pushbenachrichtigungen registriert werden, um sicherzustellen, dass der Benutzer vor dem Registrierungsversuch authentifiziert wird. 

1. Öffnen sie in Visual Studio im Projektmappen-Explorer die Projektdatei "app.xaml.cs", und kommentieren Sie im **Application_Launching**-Ereignishandler den Aufruf der **AcquirePushChannel**-Methode aus, oder löschen Sie ihn. 
 
2. Ändern Sie den Zugriffsmodus der **AcquirePushChannel**-Methode von  `private` in  `public`, und fügen Sie den  `static`-Modifizierer hinzu. 

3. Öffnen Sie die Projektdatei "MainPage.Xaml.cs", und ersetzen Sie die **OnNavigatedTo**-Methodenüberschreibung durch Folgendes:

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }
<!--HONumber=42-->
