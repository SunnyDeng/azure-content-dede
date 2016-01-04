1. Öffnen Sie die Projektdatei mainpage.xaml.cs, und fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:
	
        private MobileServiceUser user;
        private async Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileServiceDotNetClient.LoginAsync(MobileServiceAuthenticationProvider.Twitter);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                var dialog = new MessageDialog(message);
                await dialog.ShowAsync();
            }
        }

    Auf diese Weise wird eine Membervariable zum Speichern des aktuellen Benutzers erstellt und eine Methode zur Verarbeitung des Authentifizierungsprozesses. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert.

    >[AZURE.NOTE]Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für <strong>MobileServiceAuthenticationProvider</strong> oben entsprechend Ihrem Anbieter.</p> </div>

2. Löschen Sie die vorhandene Methodenüberschreibung **OnNavigatedTo** oder kommentieren diese aus, und ersetzen Sie diese durch folgende Methode, die das Ereignis **Loaded** für die Seite behandelt.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	Diese Methode ruft die neue **Authenticate**-Methode auf.

3. Ersetzen Sie den MainPage-Konstruktor durch den folgenden Code:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	Dieser Konstruktor registriert zudem den Handler für das Ereignis Loaded.
		
4. Drücken Sie F5, um die App auszuführen und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden.

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

<!---HONumber=Nov15_HO4-->