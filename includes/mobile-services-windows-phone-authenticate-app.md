1. Öffnen Sie die Projektdatei "mainpage.xaml.cs", und fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message =
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                MessageBox.Show(message);
            }
        }

Auf diese Weise wird eine Membervariable zum Speichern des aktuellen Benutzers sowie eine Methode zur Verarbeitung des Authentifizierungsprozesses erstellt. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert.

    >[WACOM.NOTE]Wenn Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für <strong>MobileServiceAuthenticationProvider</strong> oben entsprechend Ihrem Anbieter.</p>
    </div>

2. Löschen Sie die vorhandene **OnNavigatedTo**-Methodenüberschreibung, oder stellen Sie ihr Kommentarzeichen voran, und ersetzen Sie sie durch die folgende Methode, die das **Loaded**-Ereignis für die Seite verarbeitet. 

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
