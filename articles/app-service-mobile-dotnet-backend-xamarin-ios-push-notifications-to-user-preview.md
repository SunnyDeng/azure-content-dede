<properties 
	pageTitle="Senden von Pushbenachrichtigungen an einen bestimmten Benutzer mit dem Xamarin iOS-Client" 
	description="Erfahren Sie, wie Sie Pushbenachrichtigungen an alle Geräte eines bestimmten Benutzers senden" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/17/2015"
	ms.author="yuaxu"/>

# Senden von plattformübergreifenden Benachrichtigungen an einen bestimmten Benutzer

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../includes/app-service-mobile-selector-push-users-preview.md)]

In diesem Thema wird das Senden von Benachrichtigungen an alle registrierten Geräte eines bestimmten Benutzers aus Ihrem mobilen Back-End veranschaulicht. Sie erhalten eine Einführung in das Konzept der [Vorlagen], mit denen Clientanwendungen die Möglichkeit erhalten, bei der Registrierung Nutzlastformate und variable Platzhalter anzugeben. Die Versendung erfolgt anschließend an alle Plattformen mit diesen Platzhaltern, sodass plattformübergreifende Benachrichtigungen ermöglicht werden.

> [AZURE.NOTE] Um Pushbenachrichtigungen mit plattformübergreifenden Clients zu unterstützen, müssen Sie dieses Lernprogramm für jede Plattform absolvieren, die Sie aktivieren möchten. Die [Aktualisierung des mobilen Back-Ends](#backend) muss für Clients, die dasselbe mobile Back-End verwenden, nur einmal durchgeführt werden.
 
##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die entsprechenden App Service-Lernprogramme für jede Clientplattform abschließen, mit der Sie arbeiten möchten:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung zur TodoList-Beispiel-App hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen]<br/>Konfiguriert die TodoList-Beispiel-App für Pushbenachrichtigungen.

##<a name="client"></a>Aktualisieren des Clients zum Registrieren von Vorlagen zur Verarbeitung plattformübergreifender Pushvorgänge

1. Verschieben Sie die Codeausschnitte zur APNS-Registrierung von **FinishedLaunching** in **AppDelegate.cs** in die **RefreshAsync**-Aufgabendefinition in **QSTodoListViewController.cs**. Die Registrierung sollte erfolgen, nachdem die Authentifizierung abgeschlossen wurde.

        ...
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound, 
                new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        ...

2. Ersetzen Sie in **AppDelegate.cs** den **RegisterAsync**-Aufruf in **RegisteredForRemoteNotifications** durch den folgenden Code, um mit Vorlagen zu arbeiten:

        // delete await push.RegisterAsync (deviceToken);
        
        var notificationTemplate = "{"aps": {"alert":"$(message)"}}";

        JObject templateBody = new JObject();
        templateBody["body"] = notificationTemplate;

        JObject templates = new JObject();
        templates["testApnsTemplate"] = templateBody;

        // register with templates
        await push.RegisterAsync (deviceToken, templates);

##<a name="backend"></a>Aktualisieren Ihres Dienst-Back-Ends zum Senden von Benachrichtigungen an einen bestimmten Benutzer

1. Aktualisieren Sie in Visual Studio die  `PostTodoItem`-Methodendefinition mit dem folgenden Code:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create given user tag
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
                await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>Testen der App

Veröffentlichen Sie erneut Ihr mobiles Back-End-Projekt, und führen Sie eine beliebige der Client-Apps aus, die Sie eingerichtet haben. Beim Einfügen eines Elements sendet das Back-End Benachrichtigungen an alle Client-Apps, an denen der Benutzer angemeldet ist.

<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Erste Schritte mit Pushbenachrichtigungen]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[Vorlagen]: https://msdn.microsoft.com/de-de/library/dn530748.aspx

<!--HONumber=49-->