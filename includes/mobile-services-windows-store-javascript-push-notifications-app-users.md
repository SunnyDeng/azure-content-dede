
Als Nächstes müssen Sie die Methode zur Registrierung von Pushbenachrichtigungen ändern, um sicherzustellen, dass der Benutzer vor einem Registrierungsversuch authentifiziert wird. Die Client-App wird entsprechend der Methode aktualisiert, mit der Sie Pushbenachrichtigungen implementiert haben.

###Verwenden des Assistenten für Pushbenachrichtigungen in Visual Studio 2013 Update 2 oder einer höheren Version

Bei dieser Methode generiert der Assistent neue push.register.js- und service.js-Dateien in Ihrem Projekt.

>[WACOM.NOTE]Der Assistent zum Hinzufügen von Pushbenachrichtigungen wird derzeit nur für mobile .NET-Back-End-Dienste unterstützt.

1. Öffnen Sie in Visual Studio im Projektmappen-Explorer, die Projektdatei "push.register.js", und kennzeichnen Sie den Aufruf von **addEventListener** als Kommentar, oder löschen Sie diesen. 

2. Ersetzen Sie in der Projektdatei "default.js" die vorhandene **login**-Funktion durch den folgenden Code:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

###Manuell aktivierte Pushbenachrichtigungen		

Bei dieser Methode fügen Sie Registrierungscode aus dem Lernprogramm direkt in die Projektdatei "default.js" ein.

1. Öffnen Sie in Visual Studio im Projektmappen-Explorer die Projektdatei "default.js", und suchen Sie im **onActivated**-Ereignishandler die Codezeile, die die **createPushNotificationChannelForApplicationAsync**-Funktion aufruft und wie folgt aussieht:

		// Request a push notification channel.
		Windows.Networking.PushNotifications
		    .PushNotificationChannelManager
		    .createPushNotificationChannelForApplicationAsync()
		    .then(function (channel) {
		        // Register for notifications using the new channel
		        client.push.registerNative(channel.uri);
		    }); 
 
2. Fügen Sie diese Codezeile in der **login**-Funktion unmittelbar vor dem Aufruf von**refreshTodoItems** ein, sodass die **login**-Funktion wie folgt aussieht:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  
