

Schließlich müssen Sie das Skript, das für den Einfügevorgang in der Tabelle "TodoItem" registriert wird, aktualisieren, um Benachrichtigungen zu senden.

1.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

      ![](./media/mobile-services-javascript-update-script-notification-hubs/mobile-insert-script-push2.png)

2.  Ersetzen Sie die Einfügefunktion durch den folgenden Code, und klicken Sie auf **Save**:

         function insert(item, user, request) {
         // Definieren Sie eine Nutzlast für die Windows Store-Toastbenachrichtigung.
         var payload = '<
         xml version="1.0" encoding="utf-8"
         ><toast><visual>' +    
             '<binding template="ToastText01">  <text id="1">' +
             item.text + '</text></binding></visual></toast>';
            
         request.execute({
             success: function() {
                 // Wenn der Einfügevorgang erfolgreich war, senden Sie eine Benachrichtigung.
                push.wns.send(null,payload, 'wns/toast', {
                     success: function(pushResponse) {
                         console.log("Sent push:", pushResponse);
                         request.respond();
                         },              
                         error: function(pushResponse) {
                             console.log("Error Sending push:", pushResponse);
                             request.respond(500, { error: pushResponse });
                             }
                         });
                     }
                 });
         }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Windows Store-App-Registrierungen, wenn der Einfügevorgang erfolgreich war.


