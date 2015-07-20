

Die nachfolgenden Anweisungen gelten für das Aktualisieren einer Windows Store-Client-App, aber Sie können dies auch mit beliebigen anderen Plattformen testen, die von Azure Mobile Services unterstützt werden.


1. Öffnen Sie "MainPage.xaml.cs" in Visual Studio, und fügen Sie die folgende `using`-Anweisung oben in der Datei an.
 
        using System.Net.Http;

2. Fügen Sie in der Datei "MainPage.xaml.cs" die folgende Klassendefinition dem Namespace hinzu, damit die Benutzerinformationen zu serialisiert werden.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
            
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ", otherMails);
	        }
	    }


3. Aktualisieren Sie in der Datei "MainPage.xaml.cs" die `AuthenticateAsync`-Methode, sodass die benutzerdefinierte API aufgerufen und zusätzliche Informationen über den Benutzer aus dem AAD zurückgegeben werden.

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
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);

                    UserInfo userInfo = await App.MobileService
						.InvokeApiAsync<UserInfo>("getuserinfo", HttpMethod.Get, null);

                    message = string.Format("User info for the logged in user...\n\n{0}",userInfo.ToString());
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


4. Speichern Sie die Änderungen, und erstellen Sie den Dienst, um ihn auf Syntaxfehler zu überprüfen.

<!---HONumber=July15_HO2-->