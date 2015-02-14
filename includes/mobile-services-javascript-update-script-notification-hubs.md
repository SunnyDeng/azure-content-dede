

Schließlich müssen Sie das Skript, das für den Einfügevorgang in der Tabelle "TodoItem" registriert wird, aktualisieren, um Benachrichtigungen zu senden.

1. Klicken Sie auf **TodoItem**, klicken Sie auf **Skript**, und wählen Sie **Einfügen** aus. 

   	![](./media/mobile-services-javascript-update-script-notification-hubs/mobile-insert-script-push2.png)

2. Ersetzen Sie die Einfügefunktion durch den folgenden Code, und klicken Sie anschließend auf **Speichern**:

		function insert(item, user, request) {
		// Define a payload for the Windows Store toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
		    '<binding template="ToastText01">  <text id="1">' +
		    item.text + '</text></binding></visual></toast>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.wns.send(null, payload, 'wns/toast', {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });
		}

	Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Windows Store-App-Registrierungen, wenn der Einfügevorgang erfolgreich war.


<!--HONumber=42-->
