
Als Nächstes müssen Sie die Methode zur Registrierung von Push-Benachrichtigungen ändern, um sicherzustellen, dass der Benutzer vor einem Registrierungsversuch authentifiziert wird. 

1. Entfernen Sie in **QSAppDelegate.m** die Umsetzung des **DidFinishLaunchingWithOptions** vollständig:

		
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
		(NSDictionary *)launchOptions
		{
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		    return YES;
		}

2. Öffnen Sie die Projektdatei **QSTodoListViewController.m**, und fügen Sie den oben entfernten Code in der Methode **ViewDidLoad** zur ADD hinzu:

	
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
<!--HONumber=42-->
