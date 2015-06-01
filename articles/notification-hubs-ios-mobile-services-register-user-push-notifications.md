<properties 
	pageTitle="Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mithilfe eines mobilen Diensts - Notification Hubs" 
	description="Erfahren Sie, wie Sie eine Pushbenachrichtigungsregistrierung in einer iOS-App mit Azure Notification Hubs anfordern, wenn die Registrierung von Azure Mobile Services durchgeführt wird." 
	services="mobile-services, notification-hubs" 
	documentationCenter="" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="yuaxu"/>

# Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mithilfe eines mobilen Diensts

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS" class="current">iOS</a>
</div>

In diesem Artikel erfahren Sie, wie Sie Pushbenachrichtigungs-Registrierungen mit Azure Notification Hubs anfordern können, wenn die Registrierung von Azure Mobile Services durchgeführt wird. Dieses Thema baut auf dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs] auf. Sie müssen zuvor die Schritte in diesem Lernprogramm abgeschlossen haben, in denen der authentifizierte Mobile Service erstellt wird. Weitere Informationen zum Benachrichtigen von Benutzern finden Sie unter [Benachrichtigen von Benutzern mit Notification Hubs].  

1. Öffnen Sie in Xcode die Datei "QSTodoService.h" in dem Projekt, das Sie erstellt haben, als Sie das als Voraussetzung erforderliche Lernprogramm [Erste Schritte mit der Authentifizierung] absolviert haben, und fügen Sie die folgende **deviceToken**-Eigenschaft hinzu:

		@property (nonatomic) NSData* deviceToken;

 	Diese Eigenschaft speichert das Gerätetoken.

2. Fügen Sie in der Datei "QSTodoService.m" die folgende **getDeviceTokenInHex**-Methode hinzu:

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

	Diese Methode konvertiert das Gerätetoken in einen hexadezimalen Zeichenfolgenwert.

3. Fügen Sie in der Datei "QSAppDelegate.m" die folgenden Codezeilen zur **didFinishLaunchingWithOptions**-Methode hinzu:

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	Dadurch werden in Ihrer App Pushbenachrichtigungen aktiviert.

4. 	Fügen Sie in der Datei "QSAppDelegate.m" die folgende Methode hinzu:

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
			    [QSTodoService defaultService].deviceToken = deviceToken;
			}

	Dadurch wird die **deviceToken**-Eigenschaft aktualisiert.

	> [AZURE.NOTE] Die Methode sollte nun keinen weiteren Code mehr enthalten. Falls Sie bereits einen Aufruf der Methode **registerNativeWithDeviceToken** aus dem Lernprogramm [Erste Schritte mit Notification Hubs](/de-de/manage/services/notification-hubs/get-started-notification-hubs-ios/"%20target="_blank")  haben, müssen Sie diesen Aufruf auskommentieren oder entfernen.

5.  (Optional) Fügen Sie in der Datei "QSAppDelegate.m" die folgende Handlermethode hinzu:

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
			    NSLog(@"%@", userInfo);
			    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
			                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
			                          @"OK" otherButtonTitles:nil, nil];
			    [alert show];
			}

 	Diese Methode zeigt eine Warnung in der GUI an, wenn Ihre App während der Ausführung eine Benachrichtigung empfängt.

6. Fügen Sie in der Datei "QSTodoListViewController.m" die **registerForNotificationsWithBackEnd**-Methode hinzu:

			- (void)registerForNotificationsWithBackEnd {
			    NSString* json = [NSString  stringWithFormat:@"{"platform":"ios", "deviceToken":"%@"}", [self.todoService getDeviceTokenInHex] ];

			    [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
			        if (error != nil) {
			            NSLog(@"Registration failed: %@", error);
			        } else {
			            // display UIAlert with successful login
			            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
			            [alert show];
			        }
			    }];
			}

	Diese Methode konstruiert eine json-Nutzlast, die das Gerätetoken enthält. Dann ruft sie die benutzerdefinierte API in Ihrem mobilen Dienst auf, um sich für die Benachrichtigung zu registrieren. Diese Methode erstellt ein Gerätetoken für Pushbenachrichtigungen und sendet es, zusammen mit dem Gerätetyp, an die benutzerdefinierte API-Methode, die eine Registrierung in Notification Hubs erstellt. Diese benutzerdefinierte API wurde in [Benachrichtigen von Benutzern mit Notification Hubs] definiert.

7.	Fügen Sie schließlich in der **viewDidAppear**-Methode einen Aufruf für die neue **registerForNotificationsWithBackEnd**-Methode hinzu, nachdem der Benutzer erfolgreich authentifiziert wurde, wie im folgenden Beispiel gezeigt:

			- (void)viewDidAppear:(BOOL)animated
			{
			    MSClient *client = self.todoService.client;

			    if (client.currentUser != nil) {
			        return;
			    }

			    [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
			        [self refresh];
			        [self registerForNotificationsWithBackEnd];
			    }];
			}

	> [AZURE.NOTE] Dadurch wird sichergestellt, dass die Registrierung jedes Mal beim Laden der Seite angefordert wird. In Ihrer App möchten Sie diese Registrierung ggf. nur in bestimmten Abständen vornehmen, um sicherzustellen, dass die Registrierung noch aktuell ist.
	
Nun haben Sie die Client-App aktualisiert und können zum Thema [Benachrichtigen von Benutzern mit Notification Hubs] zurückkehren und den mobilen Dienst aktualisieren, um Benachrichtigungen mit Notification Hubs zu senden.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Erste Schritte mit Notification Hubs]: /de-de/manage/services/notification-hubs/get-started-notification-hubs-ios/





<!--HONumber=42-->
