
1. Loggen Sie sich in das [Azure-Verwaltungsportal] ein, klicken Sie auf **Mobile Services** und klicken Sie anschließend auf Ihren mobilen Dienst.

   	![](./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-selection.png)

2. Klicken Sie auf die Registerkarte **Push**, wählen Sie **Nur authentifizierte Benutzer** unter **Berechtigungen** aus, und klicken Sie anschließend auf **Skript bearbeiten**.

   	![](./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-push-registration-endpoint.png)
	
	Dadurch können Sie die Rückruffunktion für die Registrierung von Pushbenachrichtigungen anpassen. Wenn Sie Git verwenden, um den Quellcode zu bearbeiten, wird die gleiche Registrierungsfunktion in `.\service\extensions\push.js` gefunden.

3. Ersetzen Sie die vorhandene **Registrierungs**-Funktion mit dem folgenden Code:

		exports.register = function (registration, registrationContext, done) {   
		    // Get the ID of the logged-in user.
			var userId = registrationContext.user.userId;    
		    
			// Perform a check here for any disallowed tags.
			if (!validateTags(registration))
			{
				// Return a service error when the client tries 
		        // to set a user ID tag, which is not allowed.		
				done("You cannot supply a tag that is a user ID");		
			}
			else{
				// Add a new tag that is the user ID.
				registration.tags.push(userId);
				
				// Complete the callback as normal.
				done();
			}
		};
		
		function validateTags(registration){
		    for(var i = 0; i < registration.tags.length; i++) { 
		        console.log(registration.tags[i]);           
				if (registration.tags[i]
				.search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
					return false;
				}
				return true;
			}
		}

	Dadurch wird der Registrierung ein Tag hinzugefügt, das die ID des angemeldeten Benutzers darstellt. Die eingegebenen Tags werden überprüft, um zu verhindern, dass ein Benutzer sich mit der ID eines anderen Benutzers anmeldet. Wenn eine Benachrichtigung an diesen Benutzer gesendet wird, wird diese auf diesem und jedem anderen Gerät, das vom Benutzer registriert wurde, empfangen.

4. Klicken Sie auf "Zurück", klicken Sie auf die Registerkarte **Daten**, klicken Sie auf **TodoItem**, klicken Sie auf **Skript**, und wählen Sie **Einfügen** aus. <!--HONumber=42-->
