
1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

2. Klicken Sie auf die Registerkarte **Push**, wählen Sie **Nur authentifizierte Benutzer** für **Berechtigungen** aus, klicken Sie auf **Speichern** und anschließend auf **Skript bearbeiten**.
	
	Damit können Sie die Registrierungs-Rückruffunktion für Pushbenachrichtigungen anpassen. Wenn Sie den Quellcode mit Git bearbeiten, finden Sie dieselbe Registrierungsfunktion in `.\service\extensions\push.js`.

3. Ersetzen Sie die vorhandene **Registrierungsfunktion** durch den folgenden Code. Klicken Sie dann auf **Speichern**.

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

	Damit wird der Registrierung eine Markierung hinzugefügt, die die ID des angemeldeten Benutzers ist. Die eingegebenen Tags werden überprüft, um zu verhindern, dass ein Benutzer sich mit der ID eines anderen Benutzers anmeldet. Wenn eine Benachrichtigung an diesen Benutzer gesendet wird, wird diese auf diesem und jedem anderen Gerät, das vom Benutzer registriert wurde, empfangen.

4. Klicken Sie nacheinander auf den Pfeil für "Zurück", auf die Registerkarte **Daten**, auf **TodoItem** und auf **Skript**. Wählen Sie anschließend **Einfügen** aus.

<!---HONumber=AcomDC_1203_2015-->