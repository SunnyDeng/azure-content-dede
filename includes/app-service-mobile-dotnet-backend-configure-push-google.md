1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, und klicken Sie auf **NuGet-Pakete verwalten**, und suchen Sie nach `Microsoft.Azure.NotificationHubs`. Klicken Sie anschließend auf **Installieren**. Dadurch wird die Notification Hubs-Clientbibliothek installiert.

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

            // Android payload
            var androidNotificationPayload = "{ "data" : {"message":"" + item.Text + ""}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

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

<!---HONumber=AcomDC_1203_2015-->