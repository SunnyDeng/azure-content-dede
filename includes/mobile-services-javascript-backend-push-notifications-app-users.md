
1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und anschließend auf Ihren mobilen Dienst.

   	![](./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-selection.png)

2. Klicken Sie auf die Registerkarte **Push**, wählen Sie für **Berechtigungen** die Option **Nur authentifizierte Benutzer **, und klicken Sie dann auf **Skript bearbeiten**.

   	![](./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-push-registration-endpoint.png)
	
	Damit können Sie die Rückruffunktion für die Registrierungs von Pushbenachrichtigungen anpassen. Wenn Sie den Quellcode mit Git bearbeiten, finden Sie dieselbe Registrierungsfunktion in `.\service\extensions\push.js`.

3. Ersetzen Sie die vorhandene **register**-Funktion durch den folgenden Code:

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

	Damit wird der Registrierung ein Tag hinzugefügt, das die ID des angemeldeten Benutzers ist. Die bereitgestellten Tags werden überprüft, damit sich kein Benutzer mit der ID eines anderen Benutzers registrieren kann. Eine an diesen Benutzer gesendete Benachrichtigung wird auf diesem und jedem anderen Gerät empfangen, das vom Benutzer registriert wurde.

4. Klicken Sie nacheinander auf den Pfeil für "Zurück", auf die Registerkarte **Daten**, auf **TodoItem** und auf **Skript**, und wählen Sie dann **Einfügen** aus. 
