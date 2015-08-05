
1. Erweitern Sie in Visual Studio im Projektmappen-Explorer den Ordner "App_Start", und öffnen Sie die Datei "WebApiConfig.cs".

2. Fügen Sie der Methode "Register" die folgende Codezeile nach der Definition von **ConfigOptions** hinzu:

        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;
 
	Damit wird die Benutzerauthentifizierung erzwungen, bevor die Registrierung für Pushbenachrichtigungen erfolgt.

2. Klicken Sie mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Hinzufügen** und **Klasse...**.

3. Geben Sie der neuen leeren Klasse den Namen `PushRegistrationHandler`, und klicken Sie anschließend auf **Hinzufügen**.

4. Fügen Sie am Anfang der Codepage die folgenden **using**-Anweisungen hinzu:

		using System.Threading.Tasks; 
		using System.Web.Http; 
		using System.Web.Http.Controllers; 
		using Microsoft.WindowsAzure.Mobile.Service; 
		using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
		using Microsoft.WindowsAzure.Mobile.Service.Security; 

5. Ersetzen Sie die vorhandene Klasse **PushRegistrationHandler** durch den folgenden Code:
 
	    public class PushRegistrationHandler : INotificationHandler
	    {
	        public Task Register(ApiServices services, HttpRequestContext context,
            NotificationRegistration registration)
	        {
	            try
	            {
	                // Perform a check here for user ID tags, which are not allowed.
	                if(!ValidateTags(registration))
	                {
	                    throw new InvalidOperationException(
	                        "You cannot supply a tag that is a user ID.");                    
	                }
	
	                // Get the logged-in user.
	                var currentUser = context.Principal as ServiceUser;
	
	                // Add a new tag that is the user ID.
	                registration.Tags.Add(currentUser.Id);
	
	                services.Log.Info("Registered tag for userId: " + currentUser.Id);
	            }
	            catch(Exception ex)
	            {
	                services.Log.Error(ex.ToString());
	            }
	                return Task.FromResult(true);
	        }
	
	        private bool ValidateTags(NotificationRegistration registration)
	        {
	            // Create a regex to search for disallowed tags.
	            System.Text.RegularExpressions.Regex searchTerm =
	            new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
	                System.Text.RegularExpressions.RegexOptions.IgnoreCase);
	
	            foreach (string tag in registration.Tags)
	            {
	                if (searchTerm.IsMatch(tag))
	                {
	                    return false;
	                }
	            }
	            return true;
	        }
		
	        public Task Unregister(ApiServices services, HttpRequestContext context, 
	            string deviceId)
	        {
	            // This is where you can hook into registration deletion.
	            return Task.FromResult(true);
	        }
	    }

	Die Methode **Register** wird während der Registrierung aufgerufen. Damit können Sie der Registrierung eine Markierung hinzufügen, die die ID des angemeldeten Benutzers ist. Die eingegebenen Tags werden überprüft, um zu verhindern, dass ein Benutzer sich mit der ID eines anderen Benutzers anmeldet. Wenn eine Benachrichtigung an diesen Benutzer gesendet wird, wird diese auf diesem und jedem anderen Gerät, das vom Benutzer registriert wurde, empfangen.

6. Erweitern Sie den Ordner "Controllers", öffnen Sie die Projektdatei "TodoItemController.cs", suchen Sie die Methode **PostTodoItem**, und ersetzen Sie die Codezeile, die **SendAsync** aufruft, durch folgenden Code:

        // Get the logged-in user.
		var currentUser = this.User as ServiceUser;
		
		// Use a tag to only send the notification to the logged-in user.
        var result = await Services.Push.SendAsync(message, currentUser.Id);

7. Veröffentlichen Sie das Projekt mit dem mobilen Dienst erneut.

Nun verwendet der Dienst die Benutzer-ID-Markierung, um eine Pushbenachrichtigung (mit dem Text des eingefügten Elements) an alle Registrierungen zu senden, die vom angemeldeten Benutzer erstellt wurden.
 

<!---HONumber=July15_HO4-->