
Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, die erfordert, dass der Client bei jedem Start der App sowohl den Identitätsanbieter als auch Mobile Services kontaktiert. Diese Methode ist nicht nur ineffizient, sie kann auch zu nutzungsbezogenen Problemen führen, wenn eine große Anzahl von Kunden die App gleichzeitig starten sollte. Ein besserer Ansatz ist es daher, den von Mobile Services zurückgegebenen Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden. 


>[AZURE.NOTE] Den von Mobile Services zurückgegebenen Authentifizierungstoken können Sie unabhängig davon, ob Sie die clientverwaltete oder die dienstverwaltete Authentifizierung verwenden, zwischenspeichern. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.

1. Als Methode zum Verschlüsseln und Speichern von Authentifizierungstokens auf einem iOS-Client wird der Schlüsselbund empfohlen. Erstellen Sie dazu eine Klasse "KeychainWrapper", indem Sie [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) und [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) aus dem [LensRocket Beispiel](https://github.com/WindowsAzure-Samples/iOS-LensRocket) kopieren. Dieser KeychainWrapper wird verwendet, da der in der Apple-Dokumentation definierte KeychainWrapper ARC (Automatic Reference Counting) nicht berücksichtigt.


2. Öffnen Sie die Projektdatei **QSTodoListViewController.m** und fügen Sie den folgenden Code hinzu:

		
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
		


3. Fügen Sie am Ende der Methode **ViewDidAppear** in **QSTodoListViewController.m** einen Aufruf von "SaveAuthInfo" hinzu. Mit diesem Aufruf werden schlicht die Eigenschaften "userId" und "token" gespeichert.  



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

  
4. Nachdem Sie jetzt wissen, wie sich das Benutzertoken und die ID speichern lassen, soll im Folgenden demonstriert werden, wie diese beim Starten der App geladen werden können. Fügen Sie in der Methode **ViewDidLoad** in **QSTodoListViewController.m** einem Aufruf von "LoadAuthInfo" hinzu, nachdem **Self.todoService** initialisiert wurde. 
		
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

5. Wenn die App eine Anforderung an Ihren mobilen Dienst sendet, sollte diese ankommen, da der Benutzer authentifiziert ist und Sie eine 401-Antwort (Fehler "Nicht autorisiert") erhalten. Dies bedeutet, dass das von Ihnen übergebene Benutzertoken abgelaufen ist. Im Vervollständigungshandler können wir jede Methode, die mit unserem mobilen Dienst interagiert, auf eine 401-Antwort überprüfen oder können die Dinge an einem Ort erledigen: Die Methode "HandleRequest" des MSFilters.  Weitere Informationen dazu, wie dieses Szenario gehandhabt wird, finden Sie in [diesem Blogbeitrag](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx)


<!--HONumber=42-->
