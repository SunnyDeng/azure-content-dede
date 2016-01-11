
+ **.NET-Back-End (C#):**  	
	1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, und klicken Sie auf **NuGet-Pakete verwalten**, und suchen Sie nach `Microsoft.Azure.NotificationHubs`. Klicken Sie anschließend auf **Installieren**. Dadurch wird die Notification Hubs-Bibliothek zum Senden von Benachrichtigungen von Ihrem Back-End installiert.

	2. Öffnen Sie im Visual Studio-Projekt des Back-Ends **Controller** > **TodoItemController.cs**. Fügen Sie am Anfang der Datei die folgenden `using`-Anweisung hinzu:

	        using Microsoft.Azure.Mobile.Server.Config;
	        using Microsoft.Azure.NotificationHubs;


	3. Ersetzen Sie die `PostTodoItem`-Methode durch den folgenden Code:
      
	        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
	        {
	            TodoItem current = await InsertAsync(item);
	            // Get the settings for the server project.
	            HttpConfiguration config = this.Configuration;
	
	            MobileAppSettingsDictionary settings = 
	                this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
	
	            // Get the Notification Hubs credentials for the Mobile App.
	            string notificationHubName = settings.NotificationHubName;
	            string notificationHubConnection = settings
	                .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;
	
	            // Create a new Notification Hub client.
	            NotificationHubClient hub = NotificationHubClient
	            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);
	
	            // iOS payload
	            var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";
	
	            try
	            {
	                // Send the push notification and log the results.
	                var result = await hub.SendAppleNativeNotificationAsync(appleNotificationPayload);
	
	                // Write the success result to the logs.
	                config.Services.GetTraceWriter().Info(result.State.ToString());
	            }
	            catch (System.Exception ex)
	            {
	                // Write the failure result to the logs.
	                config.Services.GetTraceWriter()
	                    .Error(ex.Message, null, "Push.SendAsync Error");
	            }
	            return CreatedAtRoute("Tables", new { id = current.Id }, current);
	        }

	4. Veröffentlichen Sie das Serverprojekt erneut.

+ **Node.js-Back-End:**
   
	1. Falls nicht bereits geschehen, [laden Sie das Schnellstartprojekt herunter](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), oder verwenden Sie den [Online-Editor im Azure-Portal](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).	
	
	2. Ersetzen Sie das Tabellenskript in „todoitem.js“ durch den folgenden Code:


	        var azureMobileApps = require('azure-mobile-apps'),
	            promises = require('azure-mobile-apps/src/utilities/promises'),
	            logger = require('azure-mobile-apps/src/logger');
	        
	        var table = azureMobileApps.table();
	        
	        // When adding record, send a push notification via APNS
	        // For this to work, you must have an APNS Hub already configured
	        table.insert(function (context) {
	            // For details of the Notification Hubs JavaScript SDK, 
	            // see http://aka.ms/nodejshubs
	            logger.info('Running TodoItem.insert');
	            
				// Create a payload that contains the new item Text.
	            var payload = "{"aps":{"alert":"" + context.item.text + ""}}";
	            
	            // Execute the insert.  The insert returns the results as a Promise,
	            // Do the push as a post-execute action within the promise flow.
	            return context.execute()
	                .then(function (results) {
	                    // Only do the push if configured
	                    if (context.push) {
	                        context.push.apns.send(null, payload, function (error) {
	                            if (error) {
	                                logger.error('Error while sending push notification: ', error);
	                            } else {
	                                logger.info('Push notification sent successfully!');
	                            }
	                        });
	                    }
	                    // Don't forget to return the results from the context.execute()
	                    return results;
	                })
	                .catch(function (error) {
	                    logger.error('Error while running context.execute: ', error);
	                });
	        });
	        
	        module.exports = table;

	2. Beim Bearbeiten der Datei auf Ihrem lokalen Computer veröffentlichen Sie das Serverprojekt erneut.

<!---HONumber=AcomDC_1223_2015-->