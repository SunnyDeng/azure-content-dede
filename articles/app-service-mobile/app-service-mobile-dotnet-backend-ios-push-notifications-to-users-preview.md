<properties 
	pageTitle="Senden von plattformübergreifenden Benachrichtigungen an einen bestimmten Benutzer in iOS" 
	description="Erfahren Sie, wie Sie Pushbenachrichtigungen an alle Geräte eines bestimmten Benutzers senden."
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-dotnet" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="09/25/2015"
	ms.author="yuaxu"/>

# Senden von plattformübergreifenden Benachrichtigungen an einen bestimmten Benutzer

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../../includes/app-service-mobile-selector-push-users-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

In diesem Thema wird das Senden von Benachrichtigungen an alle registrierten Geräte eines bestimmten Benutzers aus Ihrem mobilen Back-End veranschaulicht. Sie erhalten eine Einführung in das Konzept der [Vorlagen], mit denen Clientanwendungen die Möglichkeit erhalten, bei der Registrierung Nutzlastformate und variable Platzhalter anzugeben. Die Versendung erfolgt anschließend an alle Plattformen mit diesen Platzhaltern, sodass plattformübergreifende Benachrichtigungen ermöglicht werden.

> [AZURE.NOTE]Um Pushbenachrichtigungen mit plattformübergreifenden Clients zu unterstützen, müssen Sie dieses Lernprogramm für jede Plattform absolvieren, die Sie aktivieren möchten. Die [Aktualisierung des mobilen Back-Ends](#backend) muss für Clients, die dasselbe mobile Back-End verwenden, nur einmal durchgeführt werden.
 
##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die entsprechenden App Service-Lernprogramme für jede Clientplattform abschließen, mit der Sie arbeiten möchten:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App "TodoList" hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen]<br/>Konfiguriert die Beispiel-App "TodoList" für Pushbenachrichtigungen.

##<a name="client"></a>Aktualisieren des Clients zum Registrieren von Vorlagen zur Verarbeitung plattformübergreifender Pushvorgänge

1. Verschieben Sie die Codeausschnitte zur APNS-Registrierung in **QSAppDelegate.m** unter **application:didFinishLaunchingWithOptions** in den Aufruf von **loginWithProvider** in **QSTodoListViewController.m**, damit die Registrierung nach Abschluss der Authentifizierung erfolgt:

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. Ersetzen Sie den Aufruf von **registerDeviceToken** in **application:didRegisterForRemoteNotificationsWithDeviceToken** durch den folgenden Code, um die Verwendung von Vorlagen zu ermöglichen:

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="backend"></a>Aktualisieren Ihres Dienst-Back-Ends zum Senden von Benachrichtigungen an einen bestimmten Benutzer

1. Aktualisieren Sie in Visual Studio die `PostTodoItem`-Methodendefinition mit dem folgenden Code:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create tag to identify user
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // build dictionary for template
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
[Erste Schritte mit der Authentifizierung]: app-service-mobile-dotnet-backend-ios-get-started-push-preview.md
[Erste Schritte mit Pushbenachrichtigungen]: app-service-mobile-dotnet-backend-ios-get-started-push-preview.md
[Vorlagen]: https://msdn.microsoft.com/de-DE/library/dn530748.aspx
 

<!---HONumber=Oct15_HO1-->