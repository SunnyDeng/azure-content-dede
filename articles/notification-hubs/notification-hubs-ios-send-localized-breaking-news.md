<properties
	pageTitle="Lernprogramm zu lokalisierten aktuellen Nachrichten in Notification Hubs für iOS"
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus Notification Hubs zum Senden von Benachrichtigungen zu lokalisierten aktuellen Nachrichten (iOS)."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="wesmc"/>

# Verwenden von Notification Hubs zum Senden von lokalisierten Nachrichten an iOS-Geräte

> [AZURE.SELECTOR]
- [Windows Store C#](notification-hubs-windows-store-dotnet-send-localized-breaking-news)
- [iOS](notification-hubs-ios-send-localized-breaking-news)


##Übersicht

In diesem Thema wird gezeigt, wie Sie mit dem Feature [Vorlagen](notification-hubs-templates.md) von Azure Notification Hubs Benachrichtigungen senden können, die je nach Sprache und Gerät lokalisiert wurden. In diesem Tutorial beginnen Sie mit der iOS-App, die Sie in [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] erstellt haben. Sie werden anschließend in der Lage sein, sich für Kategorien zu registrieren, die Sie interessieren, eine Sprache für die Benachrichtigungen auszuwählen und nur Pushbenachrichtigungen für diese Kategorien in der jeweiligen Sprache zu empfangen.


Dieses Szenario besteht aus zwei Teilen:

- Mit der iOS-App können Client-Geräte eine Sprache auswählen und verschiedene Nachrichtenkategorien abonnieren;

- Das Back-End verteilt die Benachrichtigungen mithilfe der **tag**- und **template**-Features von Azure Notification Hubs.



##Voraussetzungen

Sie müssen zuvor das Lernprogramm [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] abschließen und den Code verfügbar haben, da dieses Lernprogramm direkt auf dem Code aufbaut.

Visual Studio 2012 oder höher ist optional.



##Konzepte von Vorlagen

Im Lernprogramm [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] haben Sie eine App erstellt, in der Benutzer mit **tags** Nachrichten aus verschiedenen Kategorien abonnieren können. Viele Apps richten sich jedoch an verschiedene Märkte und müssen lokalisiert werden. In diesen Fällen muss auch der Inhalt der Benachrichtigungen lokalisiert und an die korrekten Geräte ausgeliefert werden. In diesem Artikel erfahren Sie, wie Sie mit dem **template**-Feature von Notification Hubs lokalisierte Benachrichtigungen für Nachrichten verschicken können.

Hinweis: Sie können mehrere Versionen der einzelnen Tags erstellen, um lokalisierte Benachrichtigungen zu verschicken. Für Englisch, Französisch und Mandarin müssen Sie z. B. drei verschiedene Markierungen für Weltnachrichten erstellen: "world\_en", "world\_fr" und "world\_ch". Anschließend müssten Sie eine lokalisierte Version der Nachrichten an die einzelnen Tags schicken. Dieser Artikel verwendet Vorlagen, um die Anzahl der Tags einzugrenzen und den Versand mehrerer Nachrichten zu vermeiden.

Mit Vorlagen können Sie auf einer hohen Ebene festlegen, wie ein bestimmtes Gerät eine Benachrichtigung empfangen soll. Die Vorlage gibt das exakte Format der Nutzlast anhand von Eigenschaften an, die Teil der von Ihrem Back-End verschickten Nachricht sind. In unserem Fall senden wir eine sprachenunabhängige Nachricht, die alle unterstützten Sprachen enthält:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Anschließend stellen wir sicher, dass sich die Geräte mit einer Vorlage registrieren, die auf die korrekte Eigenschaft verweist. Eine iOS-App, die eine Registrierung für französische Nachrichten durchführen möchte, registriert beispielsweise Folgendes:

	{
		aps:{
			alert: "$(News_French)"
		}
	}

Vorlagen sind ein leistungsstarkes Feature, über das Sie weitere Informationen in unserem Artikel [Vorlagen](notification-hubs-templates.md) finden.

##Die App-Benutzeroberfläche

Wir werden nun die App zu aktuellen Nachrichten aus dem Thema [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] so modifizieren, dass aktuelle Nachrichten mit Vorlagen verschickt werden.


Fügen Sie in „MainStoryboard\_iPhone.storyboard“ ein segmentiertes Steuerelement mit den drei unterstützten Sprachen hinzu: Englisch, Französisch und Mandarin.

![][13]

Fügen Sie dann auf jeden Fall wie unten gezeigt ein IBOutlet in "ViewController.h" hinzu:

![][14]

##Erstellen der iOS-App


1. Fügen Sie in "Notification.h" die *retrieveLocale*-Methode hinzu. Ändern Sie dann die store- und subscribe-Methoden wie unten gezeigt:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	Ändern Sie in "Notification.m" die *storeCategoriesAndSubscribe*-Methode, indem Sie den locale-Parameter hinzufügen und ihn in den Benutzerstandards speichern:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	Ändern Sie dann die *subscribe*-Methode, um das Gebietsschema einzuschließen:

		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }

		    NSString* template = [NSString stringWithFormat:@"{"aps":{"alert":"$(News_%@)"},"inAppMessage":"$(News_%@)"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	Beachten Sie, dass jetzt die *registerTemplateWithDeviceToken*-Methode statt der *registerNativeWithDeviceToken*-Methode verwendet wird. Bei der Registrierung für eine Vorlage muss die json-Vorlage und ein Name für die Vorlage bereitgestellt werden (da die App möglicherweise unterschiedliche Vorlagen registrieren möchte). Registrieren Sie Ihre Kategorien auf jeden Fall als Tags, da Sie so sicherstellen, dass Sie Benachrichtigungen für diese Nachrichten erhalten.

	Fügen Sie eine Methode hinzu, um das Gebietsschema aus den Benutzerstandardeinstellungen abzurufen:

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

2. Da die Benachrichtigungsklasse jetzt geändert wurde, müssen Sie sicherstellen, dass der ViewController das neue UISegmentControl verwendet. Fügen Sie in der *viewDidLoad*-Methode die folgende Zeile hinzu, um sicherzustellen, dass das aktuell ausgewählte Gebietsschema angezeigt wird:

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	Ändern Sie dann in der *subscribe*-Methode den Aufruf von *storeCategoriesAndSubscribe* in Folgendes:

		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

3. Schließlich müssen Sie die *didRegisterForRemoteNotificationsWithDeviceToken*-Methode in "AppDelegate.m" aktualisieren, damit Ihre Registrierung beim Starten der App korrekt aktualisiert wird. Ändern Sie den Aufruf der *subscribe*-Methode für Benachrichtigungen folgendermaßen:

		NSSet* categories = [self.notifications retrieveCategories];
	    int locale = [self.notifications retrieveLocale];
	    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

##(Optional) Senden von lokalisierten Vorlagenbenachrichtigungen aus der .NET-Konsolen-App

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##(Optional) Senden von lokalisierten Vorlagenbenachrichtigungen vom Gerät

Wenn Sie keinen Zugriff auf Visual Studio haben oder einfach die lokalisierten Vorlagenbenachrichtigungen direkt aus der App auf dem Gerät senden möchten, können Sie einfach die lokalisierten Vorlagenparameter der `SendNotificationRESTAPI`-Methode hinzufügen, die Sie im vorherigen Tutorial definiert haben.

		- (void)SendNotificationRESTAPI:(NSString*)categoryTag
		{
		    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
									 defaultSessionConfiguration] delegate:nil delegateQueue:nil];

		    NSString *json;

		    // Construct the messages REST endpoint
		    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
		                                       HUBNAME, API_VERSION]];

		    // Generated the token to be used in the authorization header.
		    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

		    //Create the request to add the template notification message to the hub
		    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
		    [request setHTTPMethod:@"POST"];

		    // Add the category as a tag
		    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

			// Template notification
	        json = [NSString stringWithFormat:@"{"messageParam":"Breaking %@ News : %@","
					"News_English":"Breaking %@ News in English : %@","
					"News_French":"Breaking %@ News in French : %@","
					"News_Mandarin":"Breaking %@ News in Mandarin : %@","
	                categoryTag, self.notificationMessage.text,
	                categoryTag, self.notificationMessage.text,  // insert English localized news here
	                categoryTag, self.notificationMessage.text,  // insert French localized news here
	                categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

	        // Signify template notification format
	        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

			// JSON Content-Type
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

		    //Authenticate the notification message POST request with the SaS token
		    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

		    //Add the notification message body
		    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

		    // Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
		               completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
	           {
	           NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
	               if (error || httpResponse.statusCode != 200)
	               {
	                   NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
	               }
	               if (data != NULL)
	               {
	                   //xmlParser = [[NSXMLParser alloc] initWithData:data];
	                   //[xmlParser setDelegate:self];
	                   //[xmlParser parse];
	               }
	           }];

		    [dataTask resume];
		}




## Nächste Schritte

Weitere Informationen zum Verwenden von Vorlagen finden Sie unter:

- [Benachrichtigen von Benutzern mit Notification Hubs: ASP.NET]
- [Benachrichtigen von Benutzern mit Notification Hubs: Mobile Services]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Benachrichtigen von Benutzern mit Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Benachrichtigen von Benutzern mit Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx

<!---HONumber=AcomDC_1217_2015-->