
1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, und klicken Sie auf **NuGet-Pakete verwalten**, und suchen Sie nach `Microsoft.Azure.NotificationHubs`. Klicken Sie anschließend auf **Installieren**. Dadurch wird die Notification Hubs-Bibliothek zum Senden von Benachrichtigungen von Ihrem Back-End installiert.

3. Öffnen Sie **Controller** > **TodoItemController.cs** im Serverprojekt, und fügen Sie die folgenden using-Anweisungen hinzu:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. Fügen Sie in der **PostTodoItem**-Methode den folgenden Code nach dem Aufruf von **InsertAsync** hinzu:

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

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Dieser Code weist den Benachrichtigungs-Hub an, eine Vorlagenbenachrichtigung an alle Vorlagenregistrierungen zu senden, die „messageParam“ enthalten. Die Zeichenfolge wird anstelle von „messageParam“ für jedes PNS eingefügt, das eine Registrierung mit „messageParam“ aufweist. Dadurch können Sie die Benachrichtigung an APNS, GCM, WNS oder eine beliebige andere PNS senden.

<!---HONumber=AcomDC_1125_2015-->