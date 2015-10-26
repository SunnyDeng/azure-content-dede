
## <a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

* Importieren Sie das iOS-SDK und "QSTodoService.h" in "QSAppDelegate.m":

```
        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>
        #import "QSTodoService.h"
```

* Fügen Sie in "QSAppDelegate.m" in `didFinishLaunchingWithOptions` die folgenden Zeilen direkt vor `return YES;` ein:

```
        UIUserNotificationSettings* notificationSettings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:notificationSettings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
```

* Fügen Sie in "QSAppDelegate.m" die folgenden Handlermethoden hinzu. Ihre App kann Pushbenachrichtigungen nun unterstützen.

```
        // Registration with APNs is successful
        - (void)application:(UIApplication *)application
        didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

        // Handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

        // Use userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);

            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];

            UIAlertView *alert = [[UIAlertView alloc]
              initWithTitle:@"Notification"
              message:alertString
              delegate:nil
              cancelButtonTitle:@"OK"
              otherButtonTitles:nil];
            [alert show];
        }
```

<!---HONumber=Oct15_HO3-->