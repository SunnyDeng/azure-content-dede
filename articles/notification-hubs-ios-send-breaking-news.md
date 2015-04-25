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
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>

# Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten
<div class="dev-center-tutorial-selector sublanding">
	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
	<a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

##Übersicht

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Benachrichtigungen zu aktuellen Nachrichten an eine iOS-Anwendung senden können. Sie werden anschließend in der Lage sein, sich für Kategorien aktueller Nachrichten zu registrieren, die Sie interessieren, und nur Pushbenachrichtigungen für diese Kategorien zu empfangen. Dieses Szenario ist ein häufiges Muster für viele Anwendungen, bei denen Benachrichtigungen an Benutzergruppen gesendet werden müssen, die zuvor Interesse daran bekundet haben, zum Beispiel RSS-Reader, Apps für Musikliebhaber, etc.

Übertragungsszenarios werden durch das Einfügen von einem oder mehreren _Tags_ möglich, wenn eine Registrierung im Notification Hub erstellt wird. Wenn Benachrichtigungen an ein Tag gesendet werden, erhalten alle Geräte, die für das Tag registriert wurden, diese Benachrichtigung. Da es sich bei Tags um Zeichenfolgen handelt, müssen diese nicht im Voraus bereitgestellt werden. Weitere Informationen zu Tags finden Sie unter [Notification Hubs-Leitfaden].


##Voraussetzungen

Dieses Thema baut auf der App auf, die Sie in [Erste Schritte mit Notification Hubs][get-started] erstellt haben. Bevor Sie dieses Lernprogramm beginnen, müssen Sie [Erste Schritte mit Notification Hubs][get-started] abgeschlossen haben.

##Hinzufügen der Kategorieauswahl zur App

Zunächst werden Sie Benutzeroberflächenelemente zum vorhandenen Storyboard hinzufügen, mit denen Benutzer Kategorien für die Registrierung auswählen können. Die durch den Benutzer ausgewählten Kategorien werden auf dem Gerät gespeichert. Beim Starten der App wird eine Geräteregistrierung in Ihrem Notification Hub mit den ausgewählten Kategorien als Tags erstellt.

2. Fügen Sie die folgenden Komponenten aus der Objektbibliothek zu MainStoryboard_iPhone.storyboard hinzu:
	+ Eine Beschriftung mit dem Text "Breaking News",
	+ Beschriftungen mit den Kategorien "World", "Politics", "Business", "Technology", "Science", "Sports",
	+ Sechs Switches, einen pro Kategorie,
	+ Eine Schaltfläche mit der Beschriftung "Subscribe"

	Ihr Storyboard sollte nun folgendermaßen aussehen:

	![][3]

3. Erstellen Sie im Assistant Editor Outlets für alle Switches und nennen Sie diese "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

	![][4]

4. Erstellen Sie eine Action für Ihre Schaltfläche mit dem Namen "subscribe". Die Datei BreakingNewsViewController.h sollte den folgenden Code enthalten:

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

5. Erstellen Sie eine neue Klasse namens  `Notifications`. Fügen Sie den folgenden Code im Schnittstellenbereich der Datei Notifications.h ein:

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
		- (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

6. Fügen Sie die folgende Import-Anweisung zu Notifications.h hinzu:

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

7. Fügen Sie den folgenden Code im Implementierungsbereich der Datei Notifications.m ein:

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}

		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}

	Diese Klasse verwendet den lokalen Speicher, um Nachrichtenkategorien zu speichern, die das Gerät empfangen soll. Sie enthält außerdem Methoden zum Registrieren dieser Kategorien.

4. Ersetzen Sie im Code oben die Platzhalter `<hub name>`  und `<connection string with listen access>`  durch den Namen des Notification Hubs und die Verbindungszeichenfolge für  *DefaultListenSharedAccessSignature*, die Sie zuvor erhalten haben.

	> [AZURE.NOTE] Da Anmeldenamen, die mit einer Client-App verteilt werden, nicht sehr sicher sind, sollten Sie nur den Schlüssel für den Abhörzugriff mit Ihrer Client-App verteilen. Der Abhörzugriff ermöglicht der App, sich für Benachrichtigungen zu registrieren, aber es können keine vorhandenen Registrierungen geändert und keine Benachrichtigungen versendet werden. Der Vollzugriffsschlüssel wird in einem gesicherten Back-End-Dienst für das Versenden von Benachrichtigungen und das Ändern vorhandener Benachrichtigungen verwendet.

8. Fügen Sie in BreakingNewsAppDelegate.h die folgende Eigenschaft hinzu:

		@property (nonatomic) Notifications* notifications;

	Diese Eigenschaft erstellt eine Singleton-Instanz der Notification-Klasse in AppDelegate.

9. Fügen Sie in der **didFinishLaunchingWithOptions**-Methode in "BreakingNewsAppDelegate.m" den folgenden Code vor **registerForRemoteNotificationTypes** ein:

		self.notifications = [[Notifications alloc] init];

	The initializes the Notification singleton.

10. Entfernen Sie in der **didRegisterForRemoteNotificationsWithDeviceToken**-Methode in "BreakingNewsAppDelegate.m" den Aufruf von **registerNativeWithDeviceToken**, und fügen Sie den folgenden Code ein:

		self.notifications.deviceToken = deviceToken;

	Die **didRegisterForRemoteNotificationsWithDeviceToken**-Methode sollte nun keinen weiteren Code mehr enthalten.

11.	Fügen Sie die folgende Methode in BreakingNewsAppDelegate.m ein:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		    [alert show];
	    }

	Diese Methode verarbeitet Benachrichtigungen, die während der Ausführung der App empfangen werden, durch Anzeige eines einfachen **UIAlert**.

9. Fügen Sie in "BreakingNewsViewController.m" den folgenden Code in die von XCode generierte **subscribe**-Methode ein:

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Diese Methode erstellt ein **NSMutableArray** von Kategorien und verwendet die **Notifications**-Klasse zum Speichern der Liste im lokalen Speicher sowie zum Registrieren der entsprechenden Tags bei Ihrem Notification Hub. Wenn Kategorien geändert werden, wird die Registrierung mit neuen Kategorien neu erstellt.

Die App kann jetzt verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert.

##Registrieren für Benachrichtigungen

Durch diese Schritte findet beim Starten eine Registrierung beim Notification Hub statt, wobei die im lokalen Speicher gespeicherten Kategorien verwendet werden.

> [AZURE.NOTE] Da sich der durch den Apple Push Notification Service (APNS) zugeteilte Geräte-Token jederzeit ändern kann, sollten Sie sich regelmäßig für Benachrichtigungen registrieren, um Benachrichtigungsfehler zu vermeiden. Dieses Beispiel registriert sich jedes Mal für Benachrichtigungen, wenn die App gestartet wird. Für häufig ausgeführte Anwendungen (öfters als einmal täglich) können Sie die Registrierung wahrscheinlich überspringen, wenn weniger als ein Tag seit der letzten Registrierung vergangen ist, um Bandbreite einzusparen.

1. Fügen Sie die folgende Methode im Schnittstellenbereich der Datei Notifications.h ein:

		- (NSSet*)retrieveCategories;

	Dieser Code ruft die Kategorien in der Notifications-Klasse ab.

2. Fügen Sie die entsprechende Implementierung in der Datei Notifications.m ein:

		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}

2. Fügen Sie in der **didRegisterForRemoteNotificationsWithDeviceToken**-Methode folgenden Code hinzu:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];
	    [notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

	Dadurch wird sichergestellt, dass bei jedem Start der App die Kategorien vom lokalen Speicher abgerufen werden und eine Registrierung für diese Kategorien abgefragt wird.

3. Fügen Sie in "BreakingNewsViewController.h" den folgenden Code zur **viewDidLoad**-Methode hinzu:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

	Dadurch wird die Benutzeroberfläche beim Start basierend auf dem Status der zuvor gespeicherten Kategorien aktualisiert.

Die App ist jetzt vollständig und kann verschiedene Kategorien in einem lokalen Speicher auf dem Gerät speichern und beim Notification Hub registrieren, wenn der Benutzer die Auswahl der Kategorien ändert. Als nächstes definieren Sie ein Back-End, das Kategoriebenachrichtigungen an diese App senden kann.

##Senden von Benachrichtigungen von Ihrem Back-End aus

[AZURE.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##Ausführen der Anwendung und Erzeugen von Benachrichtigungen

1. Klicken Sie auf die Schaltfläche "Ausführen", um das Projekt zu erstellen und die App zu starten.

	![][2]

	Die Benutzeroberfläche der App bietet verschiedene Umschaltmöglichkeiten, mit denen Sie die Kategorien auswählen können, die Sie abonnieren möchten.

2. Aktivieren Sie eine oder mehrere Kategorien, und klicken Sie dann auf **Abonnieren**.

	Wenn Sie **Abonnieren** auswählen, konvertiert die App die ausgewählten Kategorien in Tags und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an.

4. Verwenden Sie eine dieser Möglichkeiten, um eine neue Benachrichtigung vom Back-End zu versenden:

	+ **Konsolenanwendung:** Starten Sie die Konsolenanwendung.

	+ **Java/PHP:** Führen Sie die App bzw. das Skript aus.

5. Die Benachrichtigungen für die ausgewählten Kategorien werden als Popupbenachrichtigungen angezeigt.

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie aktuelle Nachrichten nach Kategorie übermittelt werden. Sie können nun eines der folgenden Lernprogramme durchführen, die andere komplexe Notification Hub-Szenarios zeigen:

+ **[Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]**

	Hier erfahren Sie, wie Sie die App zu aktuellen Nachrichten für das Versenden von lokalisierten Benachrichtigungen erweitern.

+ **[Benachrichtigen von Benutzern mit Notification Hubs]**

	Hier erfahren Sie, wie Sie Pushbenachrichtigungen an bestimmte authentifizierte Benutzer versenden. Dies ist eine gute Möglichkeit, um Benachrichtigungen nur an bestimmte Benutzer zu versenden.



<!-- Images. -->
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png







<!-- URLs. -->
[Gewusst wie: Service Bus Notification Hubs (iOS-Apps)].: http://msdn.microsoft.com/library/jj927168.aspx
[Verwenden von Notification Hubs zum Übermitteln von lokalisierten aktuellen Nachrichten]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Benachrichtigen von Benutzern mit Notification Hubs]: /manage/services/notification-hubs/notify-users/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs - Anleitungen für iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/

<!--HONumber=49-->