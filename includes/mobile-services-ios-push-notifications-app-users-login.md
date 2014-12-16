
Als Nächstes müssen Sie die Methode zur Registrierung von Pushbenachrichtigungen ändern, um sicherzustellen, dass der Benutzer vor einem Registrierungsversuch authentifiziert wird. 

1. Entfernen Sie die Implementierung von **didFinishLaunchingWithOptions** vollständig aus der Datei **QSAppDelegate.m**:

		
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
		(NSDictionary *)launchOptions
		{
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		    return YES;
		}

2. Öffnen Sie die Projektdatei **QSTodoListViewController.m**, und fügen Sie in der **viewDidLoad**-Methode den oben entfernten Code zu "add" hinzu:

	
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
