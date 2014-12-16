<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Benachrichtigen von Benutzern" metaKeywords="Azure-Pushbenachrichtigungen, Azure Notification Hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="yuaxu" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />

#Azure Notification Hubs - Benachrichtigen von Benutzern

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/de-de/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Durch die Unterstützung von Pushbenachrichtigungen in Azure haben Sie Zugriff auf eine einfache, plattformübergreifende und skalierbare Infrastruktur, die die Verarbeitung von Pushbenachrichtigungen sowohl auf Privat- als auch auf Unternehmensanwendungen für mobile Plattformen erheblich erleichtert. In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Zum Authentifizieren von Clients und zum Generieren von Benachrichtigungen wird ein ASP.NET WebAPI-Back-End verwendet, wie im Thema [Registrieren von App-Back-End aus](http://msdn.microsoft.com/de-de/library/dn743807.aspx) gezeigt.

> [AZURE.NOTE] Bei diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (iOS)](http://azure.microsoft.com/de-de/documentation/articles/notification-hubs-ios-get-started/) beschrieben erstellt und konfiguriert haben. Dieses Lernprogramm ist außerdem die Voraussetzung für das Lernprogramm [Sichere Pushbenachrichtigungen (iOS)](http://azure.microsoft.com/de-de/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/).


## Erstellen und Konfigurieren des Notification Hubs

Halten Sie sich an die Abschnitte 1 bis 5 unter [Erste Schritte mit Notification Hubs (iOS)](http://azure.microsoft.com/de-de/documentation/articles/notification-hubs-ios-get-started/). Zusätzliche Ressourcen zur iOS-Gerätebereitstellung finden Sie in der Anleitung unter [Big Nerd Ranch](http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html) (in englischer Sprache).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Ändern der iOS-App

1. Öffnen Sie die entsprechend den Abschnitten 1 bis 5 unter [Erste Schritte mit Notification Hubs (iOS)](http://azure.microsoft.com/de-de/documentation/articles/notification-hubs-ios-get-started/) erstellte Single Page View-App.

> [AZURE.NOTE] In diesem Abschnitt wird davon ausgegangen, dass Ihr Projekt mit einem leeren Organisationsnamen konfiguriert wurde. Wenn dies nicht der Fall ist, müssen Sie Ihren Organisationsnamen allen Klassennamen voranstellen.

2. Fügen Sie die folgenden Komponenten aus der Objektbibliothek zu Ihrem MainStoryboard hinzu:
	+ Ein UITextField mit dem Platzhaltertext **Username**
	+ Ein UITextField mit dem Platzhaltertext **Password**, und aktivieren Sie die Option **Secure Text Entry** (Sicherer Texteintrag) im Attribute Inspector unter dem Textfeld "Return Key" (Eingabetaste)
	+ Eine UIButton mit der Bezeichnung **1. Log in**, und deaktivieren Sie die Option **Enabled** (Aktiviert) im rechten Bereich
	+ Eine UIButton mit der Bezeichnung **2. Send Push**, und deaktivieren Sie die Option **Enabled** (Aktiviert)

	Ihr Storyboard sollte nun folgendermaßen aussehen:

    ![][IOS1]

3. Erstellen Sie Outlets für die UITextFields und die UIButtons im interface-Abschnitt von ViewController.h.

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. Zunächst erstellen wir eine RegisterClient-Klasse als Schnittstelle mit unserem Back-End. Erstellen Sie eine Objective-C-Klasse namens RegisterClient, die von NSObject erbt. Fügen Sie dann den folgenden Code in den interface-Abschnitt von RegisterClient.h ein:

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. Fügen Sie in RegisterClient.m den folgenden interface-Abschnitt hinzu:

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. Fügen Sie dann den folgenden Code im implementation-Abschnitt "RegisterClient.m" hinzu, und ersetzen Sie den Platzhalter *{backend endpoint}* durch die Ziel-URL, die Sie zum Bereitstellen Ihres App-Back-Ends im vorherigen Abschnitt verwendet haben.

		NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
		NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

		- (instancetype)init
		{
		    self = [super init];
		    if (self) {
		        NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
		        _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
		    }
		    return self;
		}

		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
		{
		    [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
		}

		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		{
		    NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

		    NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
		    deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

		    [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
		        NSLog(@"regId: %@", registrationId);
		        if (error) {
		            completion(error);
		            return;
		        }

		        [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
		            if (error) {
		                completion(error);
		                return;
		            }

		            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
		            if (httpResponse.statusCode == 200) {
		                completion(nil);
		            } else if (httpResponse.statusCode == 410 && retry) {
		                [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
		            } else {
		                NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

		                completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
		            }

		        }];
		    }];
		}

		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
		{
		    NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
		    NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

		    NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

		    NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
		    NSURL* requestURL = [NSURL URLWithString:endpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"PUT"];
		    [request setHTTPBody:jsonData];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
		    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        if (!error)
		        {
		            completion(response, error);
		        }
		        else
		        {
		            NSLog(@"Error request: %@", error);
		            completion(nil, error);
		        }
		    }];
		    [dataTask resume];
		}

		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		{
		    NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

		    if (registrationId)
		    {
		        completion(registrationId, nil);
		        return;
		    }

		    // request new one & save
		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

		            // remove quotes
		            registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

		            [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
		            [[NSUserDefaults standardUserDefaults] synchronize];

		            completion(registrationId, nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}

	Der vorstehende Code implementiert die im Artikel [Registrieren von App-Back-End aus](http://msdn.microsoft.com/de-de/library/dn743807.aspx) erläuterte Logik unter Verwendung von NSURLSession zum Durchführen von REST-Aufrufen an das App-Back-End und von NSUserDefaults zum lokalen Speichern der vom Notification Hub zurückgegebenen registrationId.

	Beachten Sie, dass die Eigenschaft **authorizationHeader** dieser Klasse festgelegt werden muss, damit die Klasse ordnungsgemäß funktioniert. Diese Eigenschaft wird von der **ViewController**-Klasse nach der Anmeldung festgelegt.

7. Fügen Sie in ViewController.h die folgende Deklaration für das Geräte-Token und einen Verweis auf eine RegisterClient-Instanz hinzu:

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. Machen Sie die ViewController-Klasse in ViewController.m zu einem UITextFieldDelegate. Fügen Sie anschließend eine private Methodendeklaration hinzu:

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] Der folgende Ausschnitt ist kein sicheres Authentifizierungsschema, Sie sollten die Implementierung von **createAndSetAuthenticationHeaderWithUsername:AndPassword:** durch ihren spezifischen Authentifizierungsmechanismus ersetzen, der ein Authentifizierungstoken für die RegisterClient-Klasse generiert, z. B. OAuth, Active Directory.

9. Fügen Sie dann im implementation-Abschnitt von ViewController.m den folgenden Code hinzu:

		-(void) setDeviceToken: (NSData*) deviceToken
		{
		    _deviceToken = deviceToken;
		    self.LogInButton.enabled = YES;
		}

		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
		{
		    NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

		    NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

		    self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
		}

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
		    [textField resignFirstResponder];
		    return YES;
		}

	Beachten Sie, dass die Festlegung des Gerätetokens die Schaltfläche "Log in" (Anmelden) aktiviert. Dies liegt daran, dass sich der ViewController im Rahmen der Anmeldeaktion für Pushbenachrichtigungen beim App-Back-End registriert. Folglich soll erst dann auf die Anmeldeaktion zugegriffen werden können, wenn das Geräte-Token korrekt eingerichtet wurde. Sie können die Anmeldung von der Pushregistrierung entkoppeln, sofern die Anmeldung vor der Pushregistrierung erfolgt.

10. Fügen Sie in ViewController.m eine Konstante für den Back-End-Endpunkt hinzu, und verwenden Sie die folgenden Ausschnitte, um die Aktionsmethoden für die UIButtons zu implementieren. Ersetzen Sie den Platzhalter-Back-End-Endpunkt durch die Ziel-URL, die Sie für das Back-End verwendet haben.

		- (IBAction)LogInAction:(id)sender {
		    // create authentication header and set it in register client
		    NSString* username = self.UsernameField.text;
		    NSString* password = self.PasswordField.text;

		    [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

		    __weak ViewController* selfie = self;
		    [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
		        if (!error) {
		            dispatch_async(dispatch_get_main_queue(), ^{
		                selfie.SendPushButton.enabled = YES;

		                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
		                                      @"Registered successfully!" delegate:nil cancelButtonTitle:
		                                      @"OK" otherButtonTitles:nil, nil];
		                [alert show];
		            });
		        }
		    }];
		}

		NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

		- (IBAction)SendPushAction:(id)sender {
		    NSURLSession* session = [NSURLSession
		                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
		                             delegate:nil
		                             delegateQueue:nil];


		    NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
		        }
		    }];
		    [dataTask resume];
		}

11. Fügen Sie in Funktion **ViewDidLoad** Folgendes hinzu, um die RegisterClient-Instanz zu initiieren und den Delegat für die Textfelder festzulegen.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. Entfernen Sie nun in **AppDelegate.m** den gesamten Inhalt der Methode **application:didRegisterForPushNotificationWithDeviceToken:**, und ersetzen Sie ihn durch Folgendes, um sicherzustellen, dass der View Controller das neueste Geräte-Token vom APNs abgerufen hat:

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. Stellen Sie zum Schluss in **AppDelegate.m** sicher, dass die folgende Methode vorhanden ist:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## Ausführen der Anwendung

1. Unter XCode führen Sie die App auf einem physischen iOS-Gerät aus (im Simulator funktionieren Pushbenachrichtigungen nicht).

2. Geben Sie in der Benutzeroberfläche der iOS-App einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben. Klicken Sie dann auf **Log In** (Anmelden).

3. Ein Popup-Fenster, das Sie über die erfolgreiche Registrierung informiert, sollte angezeigt werden. Klicken Sie auf **OK**.

4. Klicken Sie auf **Send push** (Push senden) und dann auf die Schaltfläche "Startseite". In Kürze sollte eine Pushbenachrichtigung angezeigt werden.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
