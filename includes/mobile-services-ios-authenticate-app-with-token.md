
Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, die erfordert, dass der Client bei jedem Start der App sowohl den Identitätsanbieter als auch den mobilen Dienst kontaktiert. Diese Methode ist nicht nur ineffizient, sie kann auch zu nutzungsbezogenen Problemen führen, wenn eine große Anzahl von Kunden die App gleichzeitig starten sollte. Ein besserer Ansatz ist es daher, den von Mobile Services zurückgegebenen Authentifizierungstoken zwischenzuspeichern und zu verwenden, bevor eine anbieterbasierte Anmeldung durchgeführt wird. 


>[WACOM.NOTE] Unabhängig davon, ob Sie clientverwaltete oder dienstverwaltete Authentifizierung verwenden, können Sie den von Mobile Services ausgestellten Authentifizierungstoken zwischenspeichern. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.

1. Die empfohlene Methode zum Verschlüsseln und Speichern von Authentifizierungstokens auf einem iOS-Client ist die Verwendung des Schlüsselbunds. Erstellen Sie hierfür eine KeychainWrapper-Klasse, indem Sie [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) und [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) aus dem [LensRocket-Beispiel](https://github.com/WindowsAzure-Samples/iOS-LensRocket) kopieren. Diese KeychainWrapper-Klasse wird verwendet, weil die in der Apple-Dokumentation definierte KeychainWrapper-Klasse die automatische Referenzzählung (Automatic Reference Counting, ARC) nicht berücksichtigt.


2. Öffnen Sie die Projektdatei **QSTodoListViewController.m**, und fügen Sie den folgenden Code hinzu:

		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		


3. Fügen Sie am Ende der **viewDidAppear**-Methode in der Datei **QSTodoListViewController.m** einen Aufruf von "saveAuthInfo" hinzu. Mit diesem Aufruf werden schlicht die Eigenschaften "userId" und "token" gespeichert.  



		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}

  
4. Nachdem Sie jetzt wissen, wie der Benutzertoken und die ID gespeichert werden, wird im Folgenden demonstriert, wie diese beim Starten der App geladen werden können. Fügen Sie in der Methode **viewDidLoad** in **QSTodoListViewController.m** einen Aufruf für "loadAuthInfo" nach der Initialisierung von **self.todoService** hinzu. 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. Wenn die App eine Anforderung an den mobilen Dienst sendet, die ankommt, weil der Benutzer authentifiziert worden ist und Sie eine 401-Antwort (Fehler "Nicht autorisiert") erhalten, dann bedeutet dies, dass das von Ihnen übergebene Benutzertoken abgelaufen ist. Nun könnte im Completion Handler für jede Methode, die mit dem mobilen Dienst interagiert, geprüft werden, ob eine 401-Antwort vorliegt. Alternativ lässt sich alles an einem einzigen Ort behandeln: in der MSFilter-Methode "handleRequest".  Wie Sie in diesem Fall vorgehen, erfahren Sie in [diesem Blogbeitrag](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx).

