<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-ios" urlDisplayName="Notify iOS app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification iOS app" description="Learn how to request push notification registration in an iOS app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="" solutions="" manager="" editor="" />

Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mithilfe eines mobilen Dienstes
===============================================================================================

[Windows Store C\#](/en-us/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "Windows Store C#")[iOS](/en-us/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS")

In diesem Artikel erfahren Sie, wie Sie Pushbenachrichtigungs-Registrierungen mit Azure Notification Hubs anfordern können, wenn die Registrierung von Azure Mobile Services durchgeführt wird. Dieses Lernprogramm baut auf dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs](/en-us/manage/services/notification-hubs/notify-users) auf. Sie müssen zuvor die Schritte in diesem Lernprogramm abgeschlossen haben, in denen der authentifizierte Mobile Service erstellt wird. Weitere Informationen zum Benachrichtigen von Benutzern finden Sie unter [Benachrichtigen von Benutzern mit Notification Hubs](/en-us/manage/services/notification-hubs/notify-users).

1.  Öffnen Sie in Xcode die Datei "QSTodoService.h" in dem Projekt, das Sie erstellt haben, als Sie das als Voraussetzung erforderliche Lernprogramm [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-ios/) absolviert haben, und fügen Sie die folgende **deviceToken**-Eigenschaft hinzu:

         @property (nonatomic) NSData* deviceToken;

	Diese Eigenschaft speichert das Gerätetoken.

1.  Fügen Sie in der Datei "QSTodoService.m" die folgende **getDeviceTokenInHex**-Methode hinzu:

             - (NSString*)getDeviceTokenInHex {
                 const unsigned *tokenBytes = [[self deviceToken] bytes];
                 NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                       ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                       ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                       ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                 return hexToken;
             }

    Diese Methode konvertiert das Gerätetoken in einen hexadezimalen Zeichenfolgenwert.

2.  Fügen Sie in der Datei "QSAppDelegate.m" die folgenden Codezeilen zur **didFinishLaunchingWithOptions**-Methode hinzu:

          [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    Dadurch werden in Ihrer App Pushbenachrichtigungen aktiviert.

3.  Fügen Sie in der Datei "QSAppDelegate.m" die folgende Methode hinzu:

         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
                [QSTodoService defaultService].deviceToken = deviceToken;
            }

    Dadurch wird die **deviceToken**-Eigenschaft aktualisiert.

    **Hinweis**

    Die Methode sollte nun keinen weiteren Code mehr enthalten. Falls Sie bereits einen Aufruf der Methode **registerNativeWithDeviceToken** aus dem Lernprogramm [Erste Schritte mit Notification Hubs](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios/) haben, müssen Sie diesen Aufruf auskommentieren oder entfernen.

4.  (Optional) Fügen Sie in der Datei "QSAppDelegate.m" die folgende Handlermethode hinzu:

            - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
                NSLog(@"%@", userInfo);
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            }

	Diese Methode zeigt eine Warnung in der GUI an, wenn Ihre App während der Ausführung eine Benachrichtigung empfängt.

1.  Fügen Sie in der Datei "QSTodoListViewController.m" die **registerForNotificationsWithBackEnd**-Methode hinzu:

             - (void)registerForNotificationsWithBackEnd {    
                 NSString* json = [NSString  stringWithFormat:@"{     - (void)registerForNotificationsWithBackEnd {    
                 NSString* json = [NSString  stringWithFormat:@"{\"platform\":\"ios\", \"deviceToken\":\"%@\"}", [self.todoService getDeviceTokenInHex] ];
                    
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


    Diese Methode konstruiert eine json-Nutzlast, die das Gerätetoken enthält. Dann ruft sie die benutzerdefinierte API in Ihrem mobilen Dienst auf, um sich für die Benachrichtigung zu registrieren. Diese Methode erstellt ein Gerätetoken für Pushbenachrichtigungen und sendet es, zusammen mit dem Gerätetyp, an die benutzerdefinierte API-Methode, die eine Registrierung in Notification Hubs erstellt. Diese benutzerdefinierte API wurde in [Benachrichtigen von Benutzern mit Notification Hubs](/en-us/manage/services/notification-hubs/notify-users) definiert.

2.  Fügen Sie schließlich in der **viewDidAppear**-Methode einen Aufruf für die neue **registerForNotificationsWithBackEnd**-Methode hinzu, nachdem der Benutzer erfolgreich authentifiziert wurde, wie im folgenden Beispiel gezeigt:

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

    **Hinweis**

    Auf diese Weise wird sichergestellt, dass die Registrierung bei jedem Laden der Seite angefordert wird. In Ihrer App können Sie diese Registrierung auch nur von Zeit zu Zeit durchführen, um sicherzustellen, dass die Registrierung aktuell ist.

Nun haben Sie die Client-App aktualisiert und können zum Thema [Benachrichtigen von Benutzern mit Notification Hubs](/en-us/manage/services/notification-hubs/notify-users) zurückkehren und den mobilen Dienst aktualisieren, um Benachrichtigungen mit Notification Hubs zu senden.

