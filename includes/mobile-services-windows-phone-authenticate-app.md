<ol>

1.  Öffnen Sie die Projektdatei mainpage.xaml.cs, und fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

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

    Auf diese Weise wird eine Membervariable zum Speichern des aktuellen Benutzers und eine Methode zur Verarbeitung des Authentifizierungsprozesses erstellt. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert.

    > [WACOM.NOTE]Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für **MobileServiceAuthenticationProvider** oben entsprechend Ihrem Anbieter.&lt;/p\>



2.  Löschen Sie die vorhandene Methodenüberschreibung **OnNavigatedTo** oder kommentieren diese aus, und ersetzen Sie diese durch folgende Methode, die das Ereignis **Loaded** für die Seite behandelt.

         async void MainPage_Loaded(object sender, RoutedEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

        Diese Methode ruft die neue Methode **Authenticate** auf. 

3.  Ersetzen Sie den MainPage-Konstruktor durch den folgenden Code:

         // Constructor
         public MainPage()
         {
             InitializeComponent();
             this.Loaded += MainPage_Loaded;
         }

        Dieser Konstruktor registriert zudem den Handler für das Ereignis Loaded.

4.  Drücken Sie F5, um die App auszuführen und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden.

	Wenn Sie erfolgreich angemeldet sind, sollte die App ohne Fehler ausgeführt werden, und Sie sollten in der Lage sein, Mobile Services abzufragen und Daten zu aktualisieren.


