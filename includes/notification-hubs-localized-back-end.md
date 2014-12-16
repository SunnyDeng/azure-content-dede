

In der Back-End-Anwendung müssen Sie jetzt Vorlagenbenachrichtigungen statt systemeigener Nutzlasten senden. Auf diese Weise wird der Back-End-Code vereinfacht, da Sie nicht mehrere Nutzlasten für die verschiedenen Plattformen senden müssen.

Wenn Sie Vorlagenbenachrichtigungen senden, müssen Sie nur eine Reihe von Eigenschaften angeben. In unserem Fall senden wir die Eigenschaften, die beispielsweise die lokalisierte Version der aktuellen Nachrichten enthalten:

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


Dieser Abschnitt zeigt, wie Benachrichtigungen auf zwei unterschiedliche Weisen gesendet werden:

- mit einer Konsolenanwendung
- mit einem Mobile Services-Skript

Der enthaltene Code wird an Windows Store- und iOS-Geräte übertragen, da das Back-End an ein beliebiges unterstütztes Gerät übertragen kann.



## Senden von Benachrichtigungen mit einer C#-Konsolenanwendung ##

Wir ändern einfach die *SendNotificationAsync*-Methode durch Senden einer einzelnen Vorlagenbenachrichtigung.

	var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
							{"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

Beachten Sie, dass dieser einfache Aufruf die korrekten lokalisierten Nachrichten unabhängig von der Plattform an **alle** Ihre Geräte sendet, da Ihr Benachrichtigungshub die korrekte systemeigene Nutzlast erstellt und an alle Geräte sendet, die ein bestimmtes Tag abonniert haben.

### Mobile Services

Überschreiben Sie im Mobile Service-Scheduler Ihr Skript mit Folgendem:

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
	
Beachten Sie, dass es in diesem Fall nicht erforderlich ist, mehrere Benachrichtigungen für verschiedene Gebietsschemas und Plattformen zu senden.
