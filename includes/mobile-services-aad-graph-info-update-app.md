

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
        }


3. Aktualisieren Sie in der Datei "MainPage.xaml.cs" die `AuthenticateAsync`-Methode, sodass die benutzerdefinierte API aufgerufen und zusätzliche Informationen über den Benutzer aus dem AAD zurückgegeben werden. 

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
                    UserInfo userInfo = await App.MobileService.InvokeApiAsync<UserInfo>("getuserinfo", 
                        HttpMethod.Get, null);
                    message = string.Format("{0}, you are now logged in.\n\nYour address is...\n\n{1}\n{2}, {3} {4}", 
                        userInfo.displayName, userInfo.streetAddress, userInfo.city, userInfo.state, 
                        userInfo.postalCode);
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
<!--HONumber=42-->
