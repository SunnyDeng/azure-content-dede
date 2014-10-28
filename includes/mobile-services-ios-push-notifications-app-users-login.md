Als Nächstes müssen Sie die Methode zur Registrierung von Pushbenachrichtigungen ändern. Dadurch stellen Sie sicher, dass der Benutzer vor einem Registrierungsversuch authentifiziert wird.

1.  Entfernen Sie aus **QSAppDelegate.m** die Implementierung von **didFinishLaunchingWithOptions** vollständig:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  Öffnen Sie die Projektdatei **QSTodoListViewController.m**, und fügen Sie in der Methode **viewDidLoad** den oben entfernten Code am Ende hinzu:

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;

            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];

            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }


