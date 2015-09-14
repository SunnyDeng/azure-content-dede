<properties
	pageTitle="Lernprogramm zu aktuellen Nachrichten mit Notification Hubs - iOS"
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus Notification Hubs zum Senden von Benachrichtigungen zu aktuellen Nachrichten an iOS-Geräte."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="wesmc"/>

# Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Übersicht

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Benachrichtigungen zu aktuellen Nachrichten an eine iOS-Anwendung senden können. Sie werden anschließend in der Lage sein, sich für Kategorien aktueller Nachrichten zu registrieren, die Sie interessieren, und nur Pushbenachrichtigungen für diese Kategorien zu empfangen. Dieses Szenario ist ein häufiges Muster für viele Anwendungen, bei denen Benachrichtigungen an Benutzergruppen gesendet werden müssen, die zuvor Interesse daran bekundet haben, zum Beispiel RSS-Reader, Apps für Musikliebhaber, etc.

Übertragungsszenarien werden durch das Einfügen von einem oder mehreren _Tags_ möglich, wenn eine Registrierung im Notification Hub erstellt wird. Wenn Benachrichtigungen an ein Tag gesendet werden, erhalten alle Geräte, die für das Tag registriert wurden, diese Benachrichtigung. Da es sich bei Tags um Zeichenfolgen handelt, müssen diese nicht im Voraus bereitgestellt werden. Weitere Informationen zu Tags finden Sie unter [Notification Hubs-Leitfaden].


##Voraussetzungen

Dieses Thema baut auf die App auf, die Sie in [Erste Schritte mit Notification Hubs][get-started] erstellt haben. Bevor Sie dieses Lernprogramm beginnen, müssen Sie [Erste Schritte mit Notification Hubs][get-started] abgeschlossen haben.

##Hinzufügen der Kategorieauswahl zur App

Zunächst werden Sie Benutzeroberflächenelemente zum vorhandenen Storyboard hinzufügen, mit denen Benutzer Kategorien für die Registrierung auswählen können. Die durch den Benutzer ausgewählten Kategorien werden auf dem Gerät gespeichert. Wenn die App gestartet wird, wird eine Geräteregistrierung in Ihrem Notification Hub mit den ausgewählten Kategorien als Tags erstellt.

1. Fügen Sie die folgenden Komponenten aus der Objektbibliothek zu MainStoryboard\_iPhone.storyboard hinzu:
	+ Eine Beschriftung mit dem Text "Breaking News",
	+ Beschriftungen mit den Kategorien "World", "Politics", "Business", "Technology", "Science", "Sports",
	+ Sechs Switches, einen pro Kategorie, wobei für jeden Switch **State** standardmäßig auf **Off** festgelegt ist.
	+ Eine Schaltfläche mit der Beschriftung "Subscribe"

	Ihr Storyboard sollte nun folgendermaßen aussehen:

	![][3]

2. Erstellen Sie im Assistant Editor Outlets für alle Switches und nennen Sie diese "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"


3. Erstellen Sie eine Aktion für Ihre Schaltfläche mit dem Namen "subscribe". "ViewController.h" sollte Folgendes enthalten:

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

4. Erstellen Sie eine neue **Cocoa Touch-Klasse** namens `Notifications`. Fügen Sie den folgenden Code im Schnittstellenbereich der Datei Notifications.h ein:

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
					completion:(void (^)(NSError* error))completion;

		- (NSSet*)retrieveCategories;

		- (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Fügen Sie die folgende Import-Anweisung zu Notifications.h hinzu:

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Kopieren Sie den folgenden Code in den Implementierungsabschnitt der Datei "Notifications.m", und ersetzen Sie im obigen Code die Platzhalter `<hub name>` und `<connection string with listen access>` durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für *DefaultListenSharedAccessSignature*, die Sie zuvor erhalten haben.

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}


		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}


		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
		{
		    SBNotificationHub* hub = [[SBNotificationHub alloc]
										initWithConnectionString:@"<connection string with listen access>"
										notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}



	Diese Klasse verwendet den lokalen Speicher, um Nachrichtenkategorien zu speichern und abzurufen, die das Gerät empfangen wird. Sie enthält außerdem eine Methode zum Registrieren dieser Kategorien.

	> [AZURE.NOTE]Da Anmeldenamen, die mit einer Client-App verteilt werden, nicht sehr sicher sind, sollten Sie nur den Schlüssel für den Abhörzugriff mit Ihrer Client-App verteilen. Der Abhörzugriff ermöglicht der App, sich für Benachrichtigungen zu registrieren, aber es können keine vorhandenen Registrierungen geändert und keine Benachrichtigungen versendet werden. Der Vollzugriffsschlüssel wird in einem gesicherten Back-End-Dienst für das Versenden von Benachrichtigungen und das Ändern vorhandener Benachrichtigungen verwendet.

7. Fügen Sie in der Datei "AppDelegate.h" eine Importanweisung für "Notifications.h" hinzu, und fügen Sie eine Eigenschaft für eine Instanz der Notifications-Klasse hinzu:

		#import "Notifications.h"

		@property (nonatomic) Notifications* notifications;

	Diese Eigenschaft erstellt eine Singleton-Instanz der Notification-Klasse in AppDelegate.

8. Fügen Sie in der **didFinishLaunchingWithOptions**-Methode in "AppDelegate.m" den Code zum Initialisieren der Notifications-Instanz am Anfang der Methode hinzu:

		self.notifications = [[Notifications alloc] init];

	Dieser Code initialisiert den Notification-Singleton.


9. Ersetzen Sie in der **didRegisterForRemoteNotificationsWithDeviceToken**-Methode in "AppDelegate.m" den Code in der Methode durch den folgenden Code, um das Gerätetoken an die Notifications-Klasse zu übergeben. Die Notifications-Klasse führt die Registrierung für Benachrichtigungen mit den Kategorien durch. Wenn der Benutzer die Auswahl der Kategorien ändert, wird die `subscribeWithCategories`-Methode als Antwort auf die Schaltfläche **subscribe** aufgerufen, um sie zu aktualisieren.

	> [AZURE.NOTE]Da sich der durch den Apple Push Notification Service (APNS) zugeteilte Geräte-Token jederzeit ändern kann, sollten Sie sich regelmäßig für Benachrichtigungen registrieren, um Benachrichtigungsfehler zu vermeiden. Dieses Beispiel registriert sich jedes Mal für Benachrichtigungen, wenn die App gestartet wird. Für häufig ausgeführte Anwendungen (öfters als einmal täglich) können Sie die Registrierung wahrscheinlich überspringen, wenn weniger als ein Tag seit der letzten Registrierung vergangen ist, um Bandbreite einzusparen.

		self.notifications.deviceToken = deviceToken;

		// Retrieves the categories from local storage and requests a registration for these categories
		// each time the app starts and performs a registration.

	    NSSet* categories = [self.notifications retrieveCategories];
	    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];


	Die **didRegisterForRemoteNotificationsWithDeviceToken**-Methode sollte nun keinen weiteren Code mehr enthalten.

10.	Die folgenden Methoden sollten bereits durch Abschließen des Lernprogramms [Erste Schritte mit Notification Hubs][get-started] in "AppDelegate.m" vorliegen. Wenn dies nicht der Fall ist, fügen Sie sie hinzu.

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
	    }

	Diese Methode verarbeitet Benachrichtigungen, die während der Ausführung der App empfangen werden, durch Anzeige eines einfachen **UIAlert**.

11. Fügen Sie in "ViewController.m" eine Importanweisung für "AppDelegate.h" hinzu, und kopieren Sie den folgenden Code in die von XCode generierte **subscribe**-Methode. Dieser Code aktualisiert die Benachrichtigungsregistrierung so, dass die neuen Kategorietags verwendet werden, die der Benutzer in der Benutzeroberfläche ausgewählt hat.

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            [self MessageBox:@"Notification" message:@"Subscribed!"];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Diese Methode erstellt ein **NSMutableArray** von Kategorien und verwendet die **Notifications**-Klasse zum Speichern der Liste im lokalen Speicher sowie zum Registrieren der entsprechenden Tags bei Ihrem Notification Hub. Wenn Kategorien geändert werden, wird die Registrierung mit neuen Kategorien neu erstellt.

12. Fügen Sie in "ViewController.m" den folgenden Code in der **viewDidLoad**-Methode hinzu, um die Benutzeroberfläche auf Grundlage der zuvor gespeicherten Kategorien festzulegen.


		// This updates the UI on startup based on the status of previously saved categories.

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



Die App kann jetzt verschiedene Kategorien im lokalen Speicher des Geräts speichern, die für die Registrierung beim Notification Hub verwendet werden, wenn die App gestartet wird. Der Benutzer kann zur Laufzeit die Auswahl der Kategorien ändern und auf die **subscribe**-Methode klicken, um die Registrierung für das Gerät zu aktualisieren. Als Nächstes aktualisieren Sie die App zum Senden von Benachrichtigungen zu aktuellen Nachrichten direkt in der App selbst.


##Senden von Benachrichtigungen

Normalerweise würden die Benachrichtigungen von einem Back-End-Dienst gesendet werden, aber für dieses Lernprogramm wird der Code zum Senden von Benachrichtigungen so aktualisiert, dass Benachrichtigungen zu aktuellen Nachrichten direkt über die App gesendet werden können. Dazu wird die `SendNotificationRESTAPI`-Methode aktualisiert, die im Lernprogramm [Erste Schritte mit Notification Hubs][get-started] definiert wurde.


1. Aktualisieren Sie in "ViewController.m" die `SendNotificationRESTAPI`-Methode wie folgt, sodass ein `pns`-Parameter für einen Platform Notification Service und ein Parameter für das Kategorietag verwendet werden.

		- (void)SendNotificationRESTAPI:(NSString*)pns Category:(NSString*)categoryTag
		{
		    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
									 defaultSessionConfiguration] delegate:nil delegateQueue:nil];

		    NSString *json;

		    // Construct the messages REST endpoint
		    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
		                                       HUBNAME, API_VERSION]];

		    // Generated the token to be used in the authorization header.
		    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

		    //Create the request to add the APNS notification message to the hub
		    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
		    [request setHTTPMethod:@"POST"];

		    // Add the category as a tag
		    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

		    // Windows Notification format of the notification message
		    if ([pns isEqualToString:@"wns"])
		    {
		        json = [NSString stringWithFormat:@"<?xml version="1.0" encoding="utf-8"?>"
		                                           "<toast>"
		                                           "<visual><binding template="ToastText01">"
		                                           "<text id="1">Breaking %@ News : %@</text>"
		                                           "</binding>"
		                                           "</visual>"
		                                           "</toast>",
		                categoryTag, self.notificationMessage.text];

		        // Signify windows notification format
		        [request setValue:@"windows" forHTTPHeaderField:@"ServiceBusNotification-Format"];

		        // XML Content-Type
		        [request setValue:@"application/xml" forHTTPHeaderField:@"Content-Type"];

		        // Set X-WNS-TYPE header
		        [request setValue:@"wns/toast" forHTTPHeaderField:@"X-WNS-Type"];
		    }

		    // Google Cloud Messaging Notification format of the notification message
		    if ([pns isEqualToString:@"gcm"])
		    {
		        json = [NSString stringWithFormat:@"{"data":{"message":"Breaking %@ News : %@"}}",
		                categoryTag, self.notificationMessage.text];
		        // Signify gcm notification format
		        [request setValue:@"gcm" forHTTPHeaderField:@"ServiceBusNotification-Format"];

				// JSON Content-Type
				[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    }

		    // Apple Notification format of the notification message
		    if ([pns isEqualToString:@"apns"])
		    {
		        json = [NSString stringWithFormat:@"{"aps":{"alert":"Breaking %@ News : %@"}}",
		                categoryTag, self.notificationMessage.text];
		        // Signify apple notification format
		        [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

				// JSON Content-Type
				[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    }

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



2. Aktualisieren Sie in "ViewController.m" die Aktion **Send Notification**, wie im folgenden Code gezeigt. Dadurch werden die Benachrichtigungen gesendet, indem jedes Tag einzeln verwendet wird, und sie werden an mehrere Plattformen gesendet.



		- (IBAction)SendNotificationMessage:(id)sender
		{
		    self.sendResults.text = @"";

		    NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
									@"Technology", @"Science", @"Sports", nil];

		    // Lets send the message as breaking news for each category to WNS, GCM, and APNS
		    for(NSString* category in categories)
		    {
		        [self SendNotificationRESTAPI:@"wns" Category:category];
		        [self SendNotificationRESTAPI:@"gcm" Category:category];
		        [self SendNotificationRESTAPI:@"apns" Category:category];
		    }
		}



3. Erstellen Sie das Projekt neu, und stellen Sie sicher, dass keine Buildfehler vorliegen.


##Ausführen der App und Erzeugen von Benachrichtigungen

1. Klicken Sie auf die Schaltfläche Ausführen, um das Projekt zu erstellen und die App zu starten. Wählen Sie einige Optionen zum Abonnieren aktueller Nachrichten aus, und klicken Sie dann auf die Schaltfläche **Subscribe**. Es sollte in einem Dialogfeld angegeben werden, dass die Benachrichtigungen abonniert wurden.

	![][1]

	Wenn Sie **Subscribe** auswählen, konvertiert die App die ausgewählten Kategorien in Tags und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an.

2. Geben Sie eine Nachricht ein, die als aktuelle Nachricht gesendet werden soll, klicken Sie dann auf die Schaltfläche **Send Notification**.

	![][2]


3. Jedes Gerät, das aktuelle Nachrichten abonniert hat, erhält die Benachrichtigungen, die Sie gerade gesendet haben.



## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie aktuelle Nachrichten nach Kategorie übermittelt werden. Sie können nun eines der folgenden Lernprogramme durchführen, die andere komplexe Notification Hub-Szenarios zeigen:

+ **[Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]**

	Hier erfahren Sie, wie Sie die App zu aktuellen Nachrichten für das Versenden von lokalisierten Benachrichtigungen erweitern.

+ **[Benachrichtigen von Benutzern mit Notification Hubs]**

	Hier erfahren Sie, wie Sie Pushbenachrichtigungen an bestimmte authentifizierte Benutzer versenden. Dies ist eine gute Möglichkeit, um Benachrichtigungen nur an bestimmte Benutzer zu versenden.



<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Benachrichtigen von Benutzern mit Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/

<!---HONumber=September15_HO1-->