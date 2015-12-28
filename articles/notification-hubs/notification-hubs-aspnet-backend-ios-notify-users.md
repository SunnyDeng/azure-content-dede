<properties
	pageTitle="Azure Notification Hubs – Benachrichtigen von iOS-Benutzern über .NET-Back-End"
	description="Erfahren Sie, wie Pushbenachrichtigungen in Azure an Benutzer gesendet werden. Die Codebeispiele wurden in Objective-C und der .NET-API für das Back-End geschrieben."
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="wesmc"/>

#Azure Notification Hubs – Benachrichtigen von iOS-Benutzern über .NET-Back-End

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##Übersicht

Durch die Unterstützung von Pushbenachrichtigungen in Azure haben Sie Zugriff auf eine einfache, plattformübergreifende und skalierbare Infrastruktur, die die Verarbeitung von Pushbenachrichtigungen sowohl auf Privat- als auch auf Unternehmensanwendungen für mobile Plattformen erheblich erleichtert, In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Zum Authentifizieren von Clients und zum Generieren von Benachrichtigungen wird ein ASP.NET WebAPI-Back-End verwendet, wie im Thema [Registrieren von App-Back-End aus](notification-hubs-registration-management.md#registration-management-from-a-backend) gezeigt.

> [AZURE.NOTE]In diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (iOS)](notification-hubs-ios-get-started.md) beschrieben erstellt und konfiguriert haben. Dieses Lernprogramm ist außerdem die Voraussetzung für das Lernprogramm [Sichere Pushbenachrichtigungen (iOS)](notification-hubs-aspnet-backend-ios-secure-push.md). Wenn Sie Mobile Services als Back-End-Dienst verwenden, finden Sie weitere Informationen in der [Mobile Services-Version](../mobile-services-javascript-backend-ios-push-notifications-app-users.md) dieses Lernprogramms.



[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Ändern der iOS-App

1. Öffnen Sie die Einzelseitenansicht-App, die Sie im Lernprogramm [Erste Schritte mit Notification Hubs (iOS)](notification-hubs-ios-get-started.md) erstellt haben.

	> [AZURE.NOTE]In diesem Abschnitt wird davon ausgegangen, dass Sie Ihr Projekt mit einem leeren Organisationsnamen konfiguriert haben. Falls nicht, müssen Sie allen Klassennamen Ihren Organisationsnamen voranstellen.

2. Fügen Sie die im folgenden Screenshot abgebildeten Komponenten aus der Objektbibliothek dem Ordner "Main.storyboard" hinzu.

    ![][1]

	+ **Username**: Ein UITextField mit dem Platzhaltertext *Enter Username*, direkt unter "sendResult Label" und durch den linken und rechten Rand und die Anordnung unter "sendResult Label" beschränkt.
	+ **Password**: Ein UITextField mit dem Platzhaltertext *Enter Password*, direkt unter dem Textfeld "Username" und durch den linken und rechten Rand und die Anordnung unter dem Textfeld "Username" beschränkt. Aktivieren Sie die Option **Secure Text Entry** im Attribute Inspector unter *Return Key*.
	+ **Log in**: Ein bezeichneter UIButton direkt unter dem Textfeld "Password". Deaktivieren Sie die Option **Enabled** im Attributes Inspector unter *Control-Content*.
	+ **WNS**: Bezeichnung und Switch, um das Senden der Benachrichtigung an den Windows-Benachrichtigungsdienst zu aktivieren, wenn er auf dem Hub eingerichtet wurde. Siehe dazu das Lernprogramm [Erste Schritte für Windows](notification-hubs-windows-store-dotnet-get-started.md).
	+ **GCM**: Bezeichnung und Switch, um das Senden der Benachrichtigung an Google Cloud Messaging zu aktivieren, wenn es auf dem Hub eingerichtet wurde. Siehe dazu das Lernprogramm [Erste Schritte für Android](notification-hubs-android-get-started.md).
	+ **APNS**: Bezeichnung und Switch, um das Senden der Benachrichtigung an den Apple Platform Notification Service zu aktivieren.
	+ **Recipient Username**: Ein UITextField mit dem Platzhaltertext *Recipient username tag*, direkt unter der GCM-Bezeichnung und durch den linken und rechten Rand und die Anordnung unter der GCM-Bezeichnung beschränkt.


	Im Lernprogramm [Erste Schritte mit Notification Hubs (iOS)](notification-hubs-ios-get-started.md) wurden einige Komponenten hinzugefügt.

3. Ziehen Sie die Komponenten mit gedrückter **STRG**-Taste aus der Ansicht in "ViewController.h", und fügen Sie diese neuen Outlets hinzu.

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UITextField *RecipientField;
		@property (weak, nonatomic) IBOutlet UITextField *NotificationField;

		// Used to enable the buttons on the UI
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

		// Used to enabled sending notifications across platforms
		@property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

		- (IBAction)LogInAction:(id)sender;

4. Fügen Sie in "ViewController.h" folgende `#define` direkt unter den import-Anweisungen hinzu. Ersetzen Sie den Platzhalter *<Enter Your Backend Endpoint>* durch die Ziel-URL, die Sie zum Bereitstellen Ihres App-Back-Ends im vorherigen Abschnitt verwendet haben. Zum Beispiel **http://you_backend.azurewebsites.net*.

		#define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"

4. Erstellen Sie in Ihrem Projekt eine neue **Cocoa Touch-Klasse** mit dem Namen **RegisterClient** für die Schnittstelle mit dem erstellten ASP.NET-Back-End. Erstellen Sie die Klasse, die von `NSObject` erbt. Fügen Sie dann in "RegisterClient.h" folgenden Code hinzu.

		@interface RegisterClient : NSObject

		@property (strong, nonatomic) NSString* authenticationHeader;

		-(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
			andCompletion:(void(^)(NSError*))completion;

		-(instancetype) initWithEndpoint:(NSString*)Endpoint;

		@end

5. Aktualisieren Sie in "RegisterClient.m" den `@interface`-Abschnitt:

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		@property (strong, nonatomic) NSURLSession* endpoint;

		-(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
					andCompletion:(void(^)(NSError*))completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
					completion:(void(^)(NSString*, NSError*))completion;
		-(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
					tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

		@end

6. Ersetzen Sie den `@implementation`-Abschnitt in "RegisterClient.m" durch den folgenden Code.


		@implementation RegisterClient

		// Globals used by RegisterClient
		NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

		-(instancetype) initWithEndpoint:(NSString*)Endpoint
		{
		    self = [super init];
		    if (self) {
		        NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
		        _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
				_endpoint = Endpoint;
		    }
		    return self;
		}

		-(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
					andCompletion:(void(^)(NSError*))completion
		{
		    [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
		}

		-(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
					andCompletion:(void(^)(NSError*))completion
		{
		    NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

		    NSString *deviceTokenString = [[token description]
				stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
		    deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
									uppercaseString];

		    [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
				completion:^(NSString* registrationId, NSError *error) {
		        NSLog(@"regId: %@", registrationId);
		        if (error) {
		            completion(error);
		            return;
		        }

		        [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
					tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
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
		                NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

		                completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
									userInfo:nil]);
		            }

		        }];
		    }];
		}

		-(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
					tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
		{
		    NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
													@"Tags": [tags allObjects]};
		    NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
								options:NSJSONWritingPrettyPrinted error:nil];

		    NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
												encoding:NSUTF8StringEncoding]);

		    NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
									registrationId];
		    NSURL* requestURL = [NSURL URLWithString:endpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"PUT"];
		    [request setHTTPBody:jsonData];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
													self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
		    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
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

		-(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
					completion:(void(^)(NSString*, NSError*))completion
		{
		    NSString* registrationId = [[NSUserDefaults standardUserDefaults]
										objectForKey:RegistrationIdLocalStorageKey];

		    if (registrationId)
		    {
		        completion(registrationId, nil);
		        return;
		    }

		    // request new one & save
		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
									_endpoint, token]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
													self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSString* registrationId = [[NSString alloc] initWithData:data
						encoding:NSUTF8StringEncoding];

		            // remove quotes
		            registrationId = [registrationId substringWithRange:NSMakeRange(1,
										[registrationId length]-2)];

		            [[NSUserDefaults standardUserDefaults] setObject:registrationId
						forKey:RegistrationIdLocalStorageKey];
		            [[NSUserDefaults standardUserDefaults] synchronize];

		            completion(registrationId, nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
									userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}

		@end

	Der vorstehende Code implementiert die im Artikel [Registrieren von App-Back-End aus](notification-hubs-registration-management.md#registration-management-from-a-backend) erläuterte Logik unter Verwendung von NSURLSession zum Durchführen von REST-Aufrufen an das App-Back-End und von NSUserDefaults zum lokalen Speichern der vom Notification Hub zurückgegebenen registrationId.

	Beachten Sie, dass die Eigenschaft **authorizationHeader** dieser Klasse festgelegt werden muss, damit die Klasse ordnungsgemäß funktioniert. Diese Eigenschaft wird von der **ViewController**-Klasse nach der Anmeldung festgelegt.

7. Fügen Sie in "ViewController.h" eine `#import`-Anweisung für "RegisterClient.h" ein. Fügen Sie dann eine Deklaration für das Gerätetoken und einen Verweis auf eine `RegisterClient`-Instanz im `@interface`-Abschnitt hinzu:

		#import "RegisterClient.h"

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. Fügen Sie in "ViewController.m" eine private Methodendeklaration im `@interface`-Abschnitt hinzu:

		@interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
						AndPassword:(NSString*)password;

		@end

> [AZURE.NOTE]Der folgende Codeausschnitt ist kein sicheres Authentifizierungsschema. Sie sollten die Implementierung von **createAndSetAuthenticationHeaderWithUsername:AndPassword:** durch Ihren speziellen Authentifizierungsmechanismus ersetzen, der ein von der RegisterClient-Klasse verwendetes Authentifizierungstoken generiert, z. B. OAuth, Active Directory.

9. Fügen Sie dann im `@implementation`-Abschnitt von "ViewController.m" den folgenden Code ein, der die Implementierung zum Festlegen des Gerätetokens und Authentifizierungsheaders hinzugefügt.

		-(void) setDeviceToken: (NSData*) deviceToken
		{
		    _deviceToken = deviceToken;
		    self.LogInButton.enabled = YES;
		}

		-(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
						AndPassword:(NSString*)password;
		{
		    NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

		    NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

		    self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
														encoding:NSUTF8StringEncoding];
		}

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
		    [textField resignFirstResponder];
		    return YES;
		}

	Beachten Sie, dass die Festlegung des Gerätetokens die Schaltfläche "Log in" (Anmelden) aktiviert. Dies liegt daran, dass sich der ViewController im Rahmen der Anmeldeaktion für Pushbenachrichtigungen beim App-Back-End registriert. Daher soll erst auf die Anmeldeaktion zugegriffen werden können, wenn das Gerätetoken ordnungsgemäß eingerichtet wurde. Sie können die Anmeldung von der Pushregistrierung entkoppeln, solange die Anmeldung vor der Registrierung erfolgt.

10. Verwenden Sie in "ViewController.m" die folgenden Codeausschnitte zum Implementieren der Aktionsmethode für die Schaltfläche **Log In** und einer Methode zum Senden der Benachrichtigungsmeldung mithilfe des ASP.NET-Back-Ends.

		- (IBAction)LogInAction:(id)sender {
		    // create authentication header and set it in register client
		    NSString* username = self.UsernameField.text;
		    NSString* password = self.PasswordField.text;

		    [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

		    __weak ViewController* selfie = self;
		    [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
				andCompletion:^(NSError* error) {
		        if (!error) {
		            dispatch_async(dispatch_get_main_queue(),
					^{
		                selfie.SendNotificationButton.enabled = YES;
		                [self MessageBox:@"Success" message:@"Registered successfully!"];
		            });
		        }
		    }];
		}


		- (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
					Message:(NSString*)message
		{
		    NSURLSession* session = [NSURLSession
		    	sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
		        delegateQueue:nil];

			// Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
		    NSURL* requestURL = [NSURL URLWithString:[NSString
				stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
				usernameTag]];

		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];

			// Get the mock authenticationheader from the register client
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
				self.registerClient.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    //Add the notification message body
		    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

			// Execute the send notification REST API on the ASP.NET Backend
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
										pns, httpResponse.statusCode, error];
		            dispatch_async(dispatch_get_main_queue(),
		            ^{
						// Append text because all 3 PNS calls may also have information to view
		                [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
		            });
		            NSLog(status);
		        }

		        if (data != NULL)
		        {
		            xmlParser = [[NSXMLParser alloc] initWithData:data];
		            [xmlParser setDelegate:self];
		            [xmlParser parse];
		        }
			}];
		    [dataTask resume];
		}


11. Aktualisieren Sie die Aktion für die Schaltfläche **Send Notification**, sodass das ASP.NET-Back-End verwendet wird und der Sendevorgang an ein beliebiges durch einen Switch aktiviertes PNS erfolgt.


		- (IBAction)SendNotificationMessage:(id)sender
		{
		    //[self SendNotificationRESTAPI];
		    [self SendToEnabledPlatforms];
		}


		-(void)SendToEnabledPlatforms
		{
		    NSString* json = [NSString stringWithFormat:@""%@"",self.notificationMessage.text];

			[self.sendResults setText:@""];

		    if ([self.WNSSwitch isOn])
		        [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

		    if ([self.GCMSwitch isOn])
		        [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

		    if ([self.APNSSwitch isOn])
		        [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
		}



11. Fügen Sie in der Funktion **ViewDidLoad** Folgendes hinzu, um die RegisterClient-Instanz zu instanziieren und den Delegat für Ihre Textfelder festzulegen.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.RecipientField.delegate = self;
		self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];

12. Entfernen Sie jetzt in **AppDelegate.m** den gesamten Inhalt der Methode **application:didRegisterForPushNotificationWithDeviceToken:**, und ersetzen Sie ihn durch Folgendes, um sicherzustellen, dass der View Controller das neueste Gerätetoken vom APNs abgerufen hat:

		// Add import to the top of the file
		#import "ViewController.h"

	    - (void)application:(UIApplication *)application
	    			didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
	    {
		    ViewController* rvc = (ViewController*) self.window.rootViewController;
		    rvc.deviceToken = deviceToken;
		}

13. Stellen Sie schließlich sicher, dass **AppDelegate.m** die folgende Methode enthält:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

## Testen der Anwendung

1. Unter XCode führen Sie die App auf einem physischen iOS-Gerät aus (im Simulator funktionieren Pushbenachrichtigungen nicht).

2. Geben Sie in der iOS-App-UI einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Zeichenfolgenwert haben. Tippen Sie dann auf **Log In**.

	![][2]


3. Ein Popupfenster sollte angezeigt werden, in dem Sie über eine erfolgreiche Registrierung informiert werden. Klicken Sie auf **OK**.

	![][3]

4. Geben Sie im Textfeld **Recipient username tag* das Benutzernamenstag ein, das bei der Anmeldung von einem anderen Gerät verwendet wird.
5. Geben Sie eine Benachrichtigungsmeldung ein, und klicken Sie auf **Send Notification**. Die Benachrichtigungsmeldung wird nur auf den Geräten empfangen, die für das Empfänger-Benutzernamenstag registriert sind. Sie wird nur an diese Benutzer gesendet.

	![][4]


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png

<!---HONumber=AcomDC_1217_2015-->