<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Azure Notification Hubs – Benachrichtigen von Benutzern

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/de-de/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
        <a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Durch die Unterstützung von Pushbenachrichtigungen in Azure haben Sie Zugriff auf eine benutzerfreundliche, plattformübergreifende und horizontal skalierte Pushinfrastruktur, die die Implementierung von Pushbenachrichtigungen sowohl für Endbenutzer- als auch für Unternehmensanwendungen für mobile Plattformen erheblich vereinfacht. In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Zum Authentifizieren von Clients und zum Generieren von Benachrichtigungen wird ein ASP.NET WebAPI-Back-End verwendet, wie im Thema [Registrieren von App-Back-End aus][Registrieren von App-Back-End aus] gezeigt. Dieses Lernprogramm basiert auf dem Notification Hub, den Sie im Lernprogramm **Erste Schritte mit Notification Hubs** erstellt haben.

Dieses Lernprogramm ist außerdem die Voraussetzung für das Lernprogramm **Sichere Pushbenachrichtigungen**. Nachdem Sie die Schritte im Lernprogramm **Benachrichtigen von Benutzern** abgeschlossen haben, können Sie mit dem Lernprogramm **Sichere Pushbenachrichtigungen** fortfahren, in dem Sie erfahren, wie Sie den Code aus dem Lernprogramm **Benachrichtigen von Benutzern** ändern, um eine Pushbenachrichtigung auf sichere Weise zu senden.

> [AZURE.NOTE] Bei diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (iOS)][Erste Schritte mit Notification Hubs (iOS)] beschrieben erstellt und konfiguriert haben.

## Erstellen und Konfigurieren des Notification Hubs

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie ein iOS-Bereitstellungsprofil und ein Entwicklungs-Pushzertifikat erstellen; anschließend müssen Sie einen Azure Notification Hub erstellen und diesen mit der Anwendung verbinden. Führen Sie die Schritte im Thema [Erste Schritte mit Notification Hubs (iOS)][Erste Schritte mit Notification Hubs (iOS)] durch, insbesondere die Abschnitte 1 bis 5.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Ändern der iOS-App

1.  Führen Sie die Schritte in den Abschnitten 1 bis 5 des Themas [Erste Schritte mit Notification Hubs (iOS)][Erste Schritte mit Notification Hubs (iOS)] aus, um eine Single View Application zu erstellen, die Pushbenachrichtigungen von einem Notification Hub empfangen kann.

> [AZURE.NOTE] In diesem Abschnitt wird davon ausgegangen, dass Sie Ihr Projekt mit einem leeren Organisationsnamen konfiguriert haben. Wenn dies nicht der Fall ist, müssen Sie den Organisationsnamen allen Klassennamen im unten abgebildeten Code voranstellen.

1.  Fügen Sie die folgenden Komponenten aus der Objektbibliothek zu Ihrem MainStoryboard hinzu:

    -   Ein UITextField mit dem Platzhaltertext **Username**
    -   Ein UITextField mit dem Platzhaltertext **Password**; aktivieren Sie außerdem die Option **Secure Text Entry** in der TextField-Eigenschaftengruppe im rechten Bereich.
    -   Eine UIButton mit der Beschriftung **1. Log in**; deaktivieren Sie außerdem die Option **Enabled** in der Accessibility-Eigenschaftengruppe im rechten Bereich.
    -   Eine UIButton mit der Beschriftung **2. Send Push**; deaktivieren Sie außerdem die Option **Aktiviert** in der Accessibility-Eigenschaftengruppe im rechten Bereich.

    Ihr Storyboard sollte nun folgendermaßen aussehen:

    ![][0]

2.  Erstellen Sie Outlets für die UITextFields und die UIButtons im interface-Abschnitt von ViewController.m.

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  Erstellen Sie Aktionen für beide Schaltflächen in der ViewController.m-Implementierung:
     - (IBAction)LogInAction:(id)sender { }
     - (IBAction)SendPushAction:(id)sender { }

4.  Zunächst erstellen wir eine RegisterClient-Klasse als Schnittstelle mit unserem Back-End. Erstellen Sie eine Objective-C-Klasse namens RegisterClient, die von NSObject erbt. Fügen Sie dann den folgenden Code in den interface-Abschnitt von RegisterClient.h ein:

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  Fügen Sie in RegisterClient.m den folgenden interface-Abschnitt hinzu:

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  Fügen Sie dann den folgendem Code im implementation-Abschnitt hinzu, und ersetzen Sie den Platzhalter *{backend endpoint}* durch den Endpunkt, den Sie zum Bereitstellen Ihres App-Back-Ends im vorherigen Abschnitt verwendet haben.

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

    Der vorstehende Code implementiert die im Artikel [Registrieren von App-Back-End aus][Registrieren von App-Back-End aus] erläuterte Logik unter Verwendung von NSURLSession zum Durchführen von REST-Aufrufen an das App-Back-End und von NSUserDefaults zum lokalen Speichern der vom Notification Hub zurückgegebenen registrationId.

    Beachten Sie, dass die Eigenschaft **authorizationHeader** dieser Klasse festgelegt werden muss, damit die Klasse ordnungsgemäß funktioniert. Diese Eigenschaft wird von der **ViewController**-Klasse nach der Anmeldung festgelegt.

7.  Fügen Sie in ViewController.h die folgende Eigenschaft hinzu, die das Gerätetoken enthalten wird:

        @property (strong, nonatomic) NSData* deviceToken;

8.  Definieren Sie in ViewController.m die ViewController-Klasse als UITextFieldDelegate, fügen Sie eine Eigenschaft hinzu, um auf eine RegisterClient-Instanz zu verweisen, und fügen Sie eine private Methodendeklaration hinzu. Ihr interface-Abschnitt sollte wie folgt aussehen:

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    Mithilfe der privaten Methode wird der Autorisierungsheader zum Durchführen der Standardauthentifizierung beim App-Back-End erstellt.

> [AZURE.NOTE] Dies ist kein sicheres Authentifizierungsschema, Sie sollten die Implementierung von **createAndSetAuthenticationHeaderWithUsername:AndPassword:** durch ihren spezifischen Authentifizierungsmechanismus ersetzen, der ein Authentifizierungstoken für die RegisterClient-Klasse generiert, z. B. OAuth, Active Directory.

1.  Fügen Sie dann im implementation-Abschnitt von ViewController.m den folgenden Code hinzu:

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

    Beachten Sie, dass die Festlegung des Gerätetokens die Schaltfläche „Log in“ aktiviert. Dies liegt daran, dass sich der ViewController im Rahmen der Anmeldeaktion für Pushbenachrichtigungen beim App-Back-End registriert. Wir möchten daher vermeiden, dass der Benutzer die Schaltfläche „Log in“ wählt, bevor das Gerätetoken ordnungsgemäß eingerichtet wurde. In Ihrer App können Sie die Anmeldung von der Pushregistrierung entkoppeln, solange die Anmeldung vor der Registrierung erfolgt.

2.  Fügen Sie in ViewController.m eine Konstante für den Back-End-Endpunkt hinzu, und füllen Sie die Implementierungen der Aktionsmethoden für die UIButtons aus. Denken Sie daran, den Platzhalter durch den Back-End-Endpunkt zu ersetzen, den Sie im vorherigen Abschnitt verwendet haben.

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

3.  Fügen Sie zum Schluss in **ViewDidLoad** den folgenden Code hinzu, um die RegisterClient-Instanz zu instanziieren und den Delegat für die Textfelder festzulegen.

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  Entfernen Sie nun in **AppDelegate.m** den gesamten Inhalt der Methode **application:didRegisterForPushNotificationWithDeviceToken:**, und ersetzen Sie ihn durch:

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    Hierdurch wird sichergestellt, dass der ViewController das neueste von APNs abgerufene Gerätetoken enthält.

5.  Stellen Sie sicher, dass **AppDelegate.m** die folgende Methode enthält:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## Ausführen der Anwendung

Gehen Sie zum Ausführen der Anwendung folgendermaßen vor:

1.  Stellen Sie sicher, dass **AppBackend** in Azure bereitgestellt ist. Falls Sie Visual Studio verwenden, führen Sie die Web-API-Anwendung **AppBackend** aus. Daraufhin wird eine ASP.NET-Webseite angezeigt.

2.  Unter XCode führen Sie die App auf einem physischen iOS-Gerät aus (im Simulator funktionieren Pushbenachrichtigungen nicht).

3.  Geben Sie in der iOS-App-UI einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben.

4.  Klicken Sie in der iOS-App-UI auf **Log in**. Klicken Sie anschließend auf **Send push**.

  [Registrieren von App-Back-End aus]: http://msdn.microsoft.com/de-de/library/dn743807.aspx
  [0]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
