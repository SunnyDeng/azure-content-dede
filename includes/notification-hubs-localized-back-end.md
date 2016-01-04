



Wenn Sie Vorlagenbenachrichtigungen senden, müssen Sie nur eine Reihe von Eigenschaften angeben. In unserem Fall senden wir die Eigenschaften, die beispielsweise die lokalisierte Version der aktuellen Nachrichten enthalten:

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


Dieser Abschnitt zeigt, wie Benachrichtigungen mit einer Konsolen-App gesendet werden.

Der enthaltene Code wird an Windows Store- und iOS-Geräte übertragen, da das Back-End an ein beliebiges unterstütztes Gerät übertragen kann.


### Senden von Benachrichtigungen mit einer C#-Konsolenanwendung 

Ändern Sie die `SendTemplateNotificationAsync`-Methode in der zuvor erstellten Konsolen-App mit dem folgenden Code. Wie Ihnen möglicherweise auffällt, ist es in diesem Fall nicht erforderlich, mehrere Benachrichtigungen für verschiedene Gebietsschemas und Plattformen zu senden.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
			// "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
			// This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

					// Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Beachten Sie, dass dieser einfache Aufruf die lokalisierten Nachrichten unabhängig von der Plattform an **alle** Ihre Geräte sendet, da Ihr Notification Hub die korrekte systemeigene Nutzlast erstellt und an alle Geräte sendet, die ein bestimmtes Tag abonniert haben.

### Senden der Benachrichtigung mit Mobile Services

Im Mobile Service-Scheduler können Sie das folgende Skript verwenden:

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	

<!---HONumber=AcomDC_1217_2015-->