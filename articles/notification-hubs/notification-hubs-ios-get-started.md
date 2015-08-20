<properties
	pageTitle="Erste Schritte mit Azure Notification Hubs"
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article" 
	ms.date="05/27/2015"
	ms.author="wesmc"/>

# Erste Schritte mit Notification Hubs

[AZURE.INCLUDE [Notification-Hubs-Auswahl-Erste-Schritte](../../includes/notification-hubs-selector-get-started.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine iOS-App senden. In diesem Lernprogramm erstellen Sie eine leere iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNs) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs.

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK]
+ [XCode 6][Install Xcode]
+ Ein Gerät, das auf iOS 8 (oder einer neueren Version) laufen kann
+ iOS-Entwicklerprogramm-Mitgliedschaft

   >[AZURE.NOTE]Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Simulator bereitgestellt und getestet werden.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für iOS-Apps.

> [AZURE.NOTE]Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Notification Hubs – Aktivieren von Apple-Pushbenachrichtigungen](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##Konfigurieren Ihres Notification Hub

Dieser Abschnitt führt Sie durch das Erstellen und Konfigurieren eines neuen Notification Hub mit dem Pushzertifikat, das Sie erstellt haben. Wenn Sie einen bereits erstellten Notification Hub verwenden möchten, können Sie die Schritte 2 bis 5 überspringen.


1. Klicken Sie in der Kategorie **Zertifikate** in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neu erstellte Pushzertifikat. Klicken Sie auf **Exportieren**, benennen Sie die Datei, wählen Sie das Format **.p12** aus, und klicken Sie dann auf **Sichern**.

    ![][1]

	Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

	>[AZURE.NOTE]In diesem Lernprogramm wird eine QuickStart.p12-Datei erstellt. Name und Ort Ihrer Datei können verschieden sein.

2. Melden Sie sich am [Azure-Verwaltungsportal] an, und klicken Sie unten auf dem Bildschirm auf **+NEU**.

3. Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

   	![][2]

4. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

   	![][3]

5. Klicken Sie auf den soeben erstellten Namespace (in der Regel ***Name des Notification Hubs*-ns**), um das Dashboard zu öffnen.

   	![][4]

6. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

   	![][5]

7. Klicken Sie oben auf die Registerkarte **Konfigurieren** und dann auf die Schaltfläche **Hochladen** in den Benachrichtigungseinstellungen von Apple, um den Zertifikatfingerabdruck hochzuladen. Wählen Sie anschließend das zuvor exportierte **.p12**-Zertifikat und das Kennwort für das Zertifikat aus. Wählen Sie aus, ob Sie den Pushdienst **Production** (wenn Sie Pushbenachrichtigungen an Benutzer versenden möchten, die Ihre App aus dem Store erworben haben) oder **Sandbox** (während der Entwicklung) verwenden möchten.

   	![][6]

8. Klicken Sie oben auf die Registerkarte **Dashboard** und anschließend auf **View Connection String**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][7]

Der Notification Hub ist nun konfiguriert, um mit APNS arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

##Verbinden Ihrer App mit dem Notification Hub

1. Erstellen Sie in XCode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

   	![][8]

2. Stellen Sie beim Festlegen der Optionen für das neue Projekt sicher, dass Sie den gleichen **Produktnamen** und die gleiche **Organisations-ID** verwenden wie zuvor beim Festlegen der Paket-ID im Apple-Entwicklerportal.

	![][11]

3. Klicken Sie unter **Targets** auf den Projektnamen, dann auf die Registerkarte **Build Settings** und erweitern Sie **Code Signing Identity**. Legen Sie anschließend unter **Debug** Ihre **Identität für die Codesignierung** fest. Stellen Sie **Levels** von **Basic** auf **All** um und legen Sie **Provisioning Profile** auf das zuvor erstellte Bereitstellungsprofil fest.

	Wenn Sie das neue, in XCode erstellte Bereitstellungsprofil nicht sehen, aktualisieren Sie die Profile für Ihre Signaturidentität durch Klicken auf **XCode** in der Menüleiste, dann auf **Preferences**, die Registerkarte **Account**, die Schaltfläche **View Details**, und anschließend auf Ihre Signaturidentität. Klicken Sie zum Schluss in der unteren rechten Ecke auf die Schaltfläche "Aktualisieren".

   	![][9]

4. Laden Sie **Version 1.2.4** des [Mobile Services iOS-SDK] herunter und entzippen Sie die Datei. Klicken Sie in XCode mit der rechten Maustaste auf Ihr Projekt, und klicken Sie dann auf die Option **Add Files to** zum Hinzufügen des Ordners **WindowsAzureMessaging.framework** zum XCode-Projekt. Wählen Sie **Copy items if needed**, und klicken Sie dann auf **Add**.

   	![][10]

5. Öffnen Sie die Datei "AppDelegate.h" und fügen Sie die folgende Importdirektive hinzu:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Fügen Sie in der Datei "AppDelegate.m" der `didFinishLaunchingWithOptions`-Methode folgenden Code basierend auf Ihrer iOS-Version hinzu. Dieser Code registriert Ihr Gerätehandle bei APNS:

	Für iOS 8:

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	Für Versionen vor iOS 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


7. Fügen Sie in der gleichen Datei die folgenden Methoden hinzu und ersetzen Sie die literalen Zeichenfolgenplatzhalter durch Ihren *Hubnamen* und die zuvor notierte *DefaultListenSharedAccessSignature*. Dieser Code gibt das Geräte-Token an den Notification Hub weiter, sodass dieser Benachrichtigungen senden kann:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<Enter your listen connection string>"
										notificationHubPath:@"<Enter your hub name>"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
				else {
				    [self MessageBox:@"Registration Status" message:@"Registered"];
				}
	    	}];
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}


8. Fügen Sie in derselben Datei folgende Methode hinzu, um einen **UIAlert** anzuzeigen, wenn die Benachrichtigung erhalten wird, während die App aktiv ist:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

8. Erstellen Sie die Anwendung auf Ihrem Gerät und führen Sie diese dort aus, um Fehler auszuschließen.

## Senden von Benachrichtigungen


Sie können den Empfang von Benachrichtigungen in Ihrer App testen, indem Sie wie im Bildschirm unten abgebildet Benachrichtigungen im Azure-Portal über die Registerkarte zum Debuggen im Notification Hub senden.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. Öffnen Sie in XCode das Main.storyboard, und fügen Sie die folgenden UI-Komponenten aus der Objektbibliothek hinzu, damit Benutzer in der App Pushbenachrichtigungen senden können.

	- Eine Bezeichnung ohne Beschriftungstext. Sie wird zum Melden von Fehlern durch das Senden von Benachrichtigungen verwendet. Die **Lines**-Eigenschaft sollte auf **0** festgelegt werden, sodass die Größe automatisch auf dem rechten und linken Rand und dem oberen Rand der Ansicht beschränkt ist.
	- Ein Textfeld mit **Platzhalter**-Text, das auf **Enter Notification Message** festgelegt ist. Beschränken Sie das Feld wie unten dargestellt direkt unter der Bezeichnung. Festlegen des View-Controllers als Outlet-Delegat.
	- Eine Schaltfläche mit der Bezeichnung **Send Notification**, die direkt unter dem Textfeld und in der horizontalen Mitte eingeschränkt ist.

	Die Ansicht sollte wie folgt aussehen:

	![][32]


2. Öffnen Sie die Datei „ViewController.h“, und fügen Sie die folgenden `#import`- und `#define`-Anweisungen hinzu. Ersetzen Sie die literale Platzhalterzeichenfolge mit der tatsächlichen Verbindungszeichenfolge *DefaultFullSharedAccessSignature* und dem *Hub-Namen*.


		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"


3. Fügen Sie Outlets für die Bezeichnung und das Textfeld hinzu, die mit Ihrer Ansicht verbunden sind, und aktualisieren Sie Ihre `interface`-Definition, um `UITextFieldDelegate` und `NSXMLParserDelegate` zu unterstützen. Fügen Sie die drei unten angezeigten Eigenschaftendeklarationen hinzu, um den Aufruf der REST-API zu unterstützen und die Antwort zu analysieren.

	Die Datei „ViewController.h“ sollte nun folgendermaßen aussehen:

		#import <UIKit/UIKit.h>
		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging.
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end


4. Öffnen Sie „ViewController.m“, und fügen Sie den folgenden Code zum Analysieren der Verbindungszeichenfolge *DefaultFullSharedAccessSignature* hinzu. Wie in der [REST-API-Referenz](http://msdn.microsoft.com/library/azure/dn495627.aspx) erwähnt, werden diese analysierten Informationen dazu verwendet, um ein SAS-Token für den Anforderungsheader *Autorisierung* zu erstellen.

		NSString *HubEndpoint;
		NSString *HubSasKeyName;
		NSString *HubSasKeyValue;

		-(void)ParseConnectionString
		{
			NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
			NSString *part;

			if ([parts count] != 3)
			{
				NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
					reason:@"Invalid full shared access connection string" userInfo:nil];

				@throw parseException;
			}

			for (part in parts)
			{
				if ([part hasPrefix:@"Endpoint"])
				{
					HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
				}
				else if ([part hasPrefix:@"SharedAccessKeyName"])
				{
					HubSasKeyName = [part substringFromIndex:20];
				}
				else if ([part hasPrefix:@"SharedAccessKey"])
				{
					HubSasKeyValue = [part substringFromIndex:16];
				}
			}
		}

5. Aktualisieren Sie in „ViewController.m“ die `viewDidLoad` -Methode, um die Verbindungszeichenfolge beim Laden der Ansicht zu analysieren. Fügen Sie auch die nachfolgend aufgeführten Hilfsmethoden hinzu.


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
		}

		-(NSString *)CF_URLEncodedString:(NSString *)inputString
		{
		   return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
				NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}





6. Fügen Sie in „ViewController.m“ den folgenden Code zum Generieren des SAS-Autorisierung-Tokens hinzu, das im *Autorisierungs*-Header gemäß der [REST-API-Referenz](http://msdn.microsoft.com/library/azure/dn495627.aspx) bereitgestellt wird.

		-(NSString*) generateSasToken:(NSString*)uri
		{
			NSString *targetUri;
			NSString* utf8LowercasedUri = NULL;
			NSString *signature = NULL;
			NSString *token = NULL;

			@try
			{
				// Add expiration
				uri = [uri lowercaseString];
				utf8LowercasedUri = [self CF_URLEncodedString:uri];
				targetUri = [utf8LowercasedUri lowercaseString];
				NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
				int expiresInMins = 60; // 1 hour
				expiresOnDate += expiresInMins * 60;
				UInt64 expires = trunc(expiresOnDate);
				NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

				// Get an hmac_sha1 Mac instance and initialize with the signing key
				const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
				const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
				unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
				CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
				NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
				signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

				// construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sr=%@&sig=%@&se=%qu&skn=%@",
					targetUri, signature, expires, HubSasKeyName];
			}
			@catch (NSException *exception)
			{
				[self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
			}
			@finally
			{
				if (utf8LowercasedUri != NULL)
					CFRelease((CFStringRef)utf8LowercasedUri);
				if (signature != NULL)
				CFRelease((CFStringRef)signature);
			}

			return token;
		}


7. Drücken Sie **STRG + Ziehen** aus der Schaltfläche **Send Notification** auf „ViewController.m“, um eine Aktion für das Ereignis **Touch Down** hinzuzufügen, das die REST-API mit dem folgenden Code ausführt.

		- (IBAction)SendNotificationMessage:(id)sender
		{
			self.sendResults.text = @"";
			[self SendNotificationRESTAPI];
		}

		- (void)SendNotificationRESTAPI
		{
		    NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

			// Apple Notification format of the notification message
		    NSString *json = [NSString stringWithFormat:@"{"aps":{"alert":"%@"}}",
								self.notificationMessage.text];

			// Construct the messages REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generated the token to be used in the authorization header.
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNS notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify apple notification format
			[request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

			//Authenticate the notification message POST request with the SaS token
			[request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

			//Add the notification message body
			[request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

			// Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
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


8. Fügen Sie in „ViewController.m“ die folgende Delegatmethode hinzu, um das Schließen der Tastatur für das Textfeld zu unterstützen. Drücken Sie **STRG + Ziehen** aus dem Textfeld auf das View-Controller-Symbol im Benutzeroberflächen-Designer, um den View-Controller als Outlet-Delegat festzulegen.

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


9. Fügen Sie in „ViewController.m“ die folgenden Delegatmethoden hinzu, um die Analyse der Antwort mithilfe von `NSXMLParser` zu unterstützen.

		//===[ Implement NSXMLParserDelegate methods ]===

		-(void)parserDidStartDocument:(NSXMLParser *)parser
		{
		    self.statusResult = @"";
		}

		-(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
			namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
			attributes:(NSDictionary *)attributeDict
		{
		    NSString * element = [elementName lowercaseString];
		    NSLog(@"*** New element parsed : %@ ***",element);

		    if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
		    {
		        self.currentElement = element;
		    }
		}

		-(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
		{
		    self.statusResult = [self.statusResult stringByAppendingString:
		        [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
		}

		-(void)parserDidEndDocument:(NSXMLParser *)parser
		{
			// Set the status label text on the UI thread
			dispatch_async(dispatch_get_main_queue(),
			^{
				[self.sendResults setText:self.statusResult];
			});
		}



10. Erstellen Sie das Projekt, und überprüfen Sie, dass keine Fehler vorhanden sind.



Sie finden alle möglichen Benachrichtigungs-Nutzlasten im [Apple Local and Push Notification Programming Guide] (in englischer Sprache).



##Testen der App

Zum Testen von Pushbenachrichtigungen auf iOS müssen Sie die Anwendung auf einem Gerät bereitstellen. Sie können keine Apple-Pushbenachrichtigungen mit dem iOS-Simulator senden.

1. Führen Sie die Anwendung aus und überprüfen Sie, ob die Registrierung erfolgreich ist. Drücken Sie dann **OK**.

	![][33]

2. Tippen Sie in das Textfeld eine Benachrichtigungsmeldung ein. Drücken Sie dann die **Senden**-Taste auf der Tastatur oder klicken Sie in der Ansicht auf die Schaltfläche **Send Notification**, um die Nachricht zu senden.

	![][34]

3. Die Benachrichtigung wird an alle Geräte gesendet, die für den Erhalt von Nachrichten registriert sind.

	![][35]

Wenn Sie Probleme haben oder Vorschläge zur Verbesserung dieses Lernprogramms für alle Leser, so hinterlassen Sie bitte einen Kommentar im Abschnitt „Disqus“ unten.


##Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Ihre iOS-Geräte übertragen. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten] lesen. Weitere Informationen zur Verwendung von Benachrichtigungshubs finden Sie in der [Benachrichtigungshubs-Anleitung].



<!-- Images. -->

[1]: ./media/notification-hubs-ios-get-started/notification-hubs-export-cert-p12.png
[2]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal.png
[3]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal2.png
[4]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal.png
[5]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal2.png
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-connection-strings.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Mobile Services iOS-SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Benachrichtigungshubs-Anleitung]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Erste Schritte mit Pushbenachrichtigungen in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: notification-hubs-aspnet-backend-ios-notify-users.md
[Verwenden von Notification Hubs zum Übermitteln von Nachrichten]: notification-hubs-ios-send-breaking-news.md

[Apple Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
 

<!----HONumber=August15_HO6-->