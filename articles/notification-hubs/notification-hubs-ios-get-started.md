<properties
	pageTitle="Senden von Pushbenachrichtigungen an iOS mit Azure Notification Hubs | Microsoft Azure"
	description="In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an iOS-Anwendungen senden."
	services="notification-hubs"
	documentationCenter="ios"
    keywords="Pushbenachrichtigung,Pushbenachrichtigungen,iOS-Pushbenachrichtigungen"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="03/14/2016"
	ms.author="wesmc"/>

# Senden von Pushbenachrichtigungen an iOS mit Azure Notification Hubs

[AZURE.INCLUDE [Notification-Hubs-Auswahl-Erste-Schritte](../../includes/notification-hubs-selector-get-started.md)]

##Übersicht

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine iOS-App senden. Sie erstellen eine leere iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts ([Apple Push Notification Service, APNs](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)) empfängt.

Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

## Voraussetzungen

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Den vollständigen Code für dieses Tutorial finden Sie [auf GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted).

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK-Version 2.x]
+ Neueste Version von [Xcode]
+ Ein Gerät, das unter iOS 8 (oder einer neueren Version) ausgeführt werden kann
+ [Apple-Entwicklerprogramm-Mitgliedschaft](https://developer.apple.com/programs/)

   > [AZURE.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem physischen iOS-Gerät (iPhone oder iPad) anstatt im iOS-Simulator bereitgestellt und getestet werden.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hubs-Lernprogramme für iOS-Apps.

[AZURE.INCLUDE [Notification Hubs – Aktivieren von Apple-Pushbenachrichtigungen](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##Konfigurieren Ihres Notification Hub für iOS-Pushbenachrichtigungen

Dieser Abschnitt führt Sie durch das Erstellen eines neuen Notification Hubs und Konfigurieren der Authentifizierung mit APNs mithilfe des **.p12**-Pushzertifikats, das Sie erstellt haben. Wenn Sie einen bereits erstellten Notification Hub verwenden möchten, können Sie zu Schritt 5 springen.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>Klicken Sie im Blatt <b>Einstellungen</b> auf die Schaltfläche <b>Notification Services</b>, und wählen Sie <b>Apple (APNS)</b>. Klicken Sie auf <b>Zertifikat hochladen</b>, und wählen Sie die <b>P12</b>-Datei aus, die Sie zuvor exportiert haben. Achten Sie auch darauf, dass Sie das richtige Kennwort angeben.</p>
<p>Wählen Sie unbedingt den Modus <b>Sandkasten</b> aus, da Sie in einer Entwicklungsumgebung arbeiten. Wählen Sie den Modus <b>Produktion</b> nur dann, wenn Sie Pushbenachrichtigungen an Benutzer senden möchten, die Ihre App im Store erworben haben.</p>
</li>
</ol>
&emsp;&emsp;![APNS im Azure-Portal konfigurieren](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![APNS-Zertifizierung im Azure-Portal konfigurieren](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



Der Notification Hub ist jetzt für die Arbeit mit APNS konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App zu registrieren und Pushbenachrichtigungen zu versenden.

##Verbinden der iOS-App mit Notification Hubs

1. Erstellen Sie in Xcode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

   	![Xcode – Single View Application][8]

2. Stellen Sie beim Festlegen der Optionen für das neue Projekt sicher, dass Sie den gleichen **Produktnamen** und die gleiche **Organisations-ID** wie zuvor beim Festlegen der Paket-ID im Apple-Entwicklerportal verwenden.

	![Xcode – Projektoptionen][11]

3. Klicken Sie unter **Targets** auf den Projektnamen, auf die Registerkarte **Build Settings**, und erweitern Sie **Code Signing Identity**. Legen Sie anschließend unter **Debug** Ihre Identität für die Codesignierung fest. Stellen Sie **Levels** von **Basic** auf **All** um, und legen Sie **Provisioning Profile** auf das zuvor erstellte Bereitstellungsprofil fest.

	Wenn das neue in Xcode erstellte Bereitstellungsprofil nicht angezeigt wird, versuchen Sie, die Profile für Ihre Signaturidentität zu aktualisieren. Klicken Sie in der Menüleiste auf **Xcode**, dann auf **Voreinstellungen**, auf die Registerkarte **Konto**, auf die Schaltfläche **Details anzeigen**, auf die Signaturidentität und anschließend rechts unten auf die Schaltfläche „Aktualisieren“.

   	![Xcode – Bereitstellungsprofil][9]

4. Laden Sie Version 2.x des [Mobile Services iOS SDK] herunter, und entzippen Sie die Datei. Klicken Sie in Xcode mit der rechten Maustaste auf Ihr Projekt, und klicken Sie dann auf die Option **Add Files to** zum Hinzufügen des Ordners **WindowsAzureMessaging.framework** zum Xcode-Projekt. Wählen Sie **Copy items if needed**, und klicken Sie dann auf **Add**.

	>[AZURE.NOTE] Das Notification Hubs-SDK unterstützt derzeit keinen Bitcode unter Xcode 7. Sie müssen **Bitcode aktivieren** in den **Buildoptionen** für Ihr Projekt auf **Nein** setzen.

   	![Azure SDK entzippen][10]

5. Fügen Sie dem Projekt eine neue Headerdatei mit dem Namen `HubInfo.h` hinzu. Diese Datei enthält die Konstanten für Ihren Notification Hub. Fügen Sie die folgenden Definitionen hinzu, und ersetzen Sie die Platzhalter von Zeichenfolgenliteralen durch Ihren *Hubnamen* und die zuvor notierte *DefaultListenSharedAccessSignature*.

		#ifndef HubInfo_h
		#define HubInfo_h
		
			#define HUBNAME @"<Enter the name of your hub>"
			#define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
		
		#endif /* HubInfo_h */

6. Öffnen Sie die Datei `AppDelegate.h`, und fügen Sie die folgenden Importdirektiven hinzu:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
		 #import "HubInfo.h"
		
7. Fügen Sie in der Datei `AppDelegate.m file` der `didFinishLaunchingWithOptions`-Methode basierend auf Ihrer iOS-Version folgenden Code hinzu. Dieser Code registriert Ihr Gerätehandle bei APNS:

	Für iOS 8:

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	Für Versionen vor iOS 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. Fügen Sie derselben Datei die folgenden Methoden hinzu: Mit diesem Code wird eine Verbindung mit dem Notification Hub mithilfe der in „HubInfo.h“ angegebenen Verbindungsinformationen hergestellt. Dieser Code gibt das Gerätetoken an den Notification Hub weiter, sodass dieser Benachrichtigungen senden kann:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
										notificationHubPath:HUBNAME];

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


9. Fügen Sie in derselben Datei folgende Methode hinzu, um einen **UIAlert** anzuzeigen, wenn die Benachrichtigung empfangen wird, während die App aktiv ist:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

10. Erstellen Sie die App auf Ihrem Gerät und führen Sie diese dort aus, um Fehler auszuschließen.

## Senden von Test-Pushbenachrichtigungen


Sie können den Empfang von Benachrichtigungen in Ihrer App testen, indem Sie Pushbenachrichtigungen im [Azure-Portal] über den Abschnitt **Problembehandlung** im Hub-Blatt senden (Option *Testsendung*).

![Azure-Portal – Testsendung][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (Optional) Senden von Pushbenachrichtigungen aus der App

Wenn Sie Pushbenachrichtigungen aus einer App senden möchten, hilft Ihnen das Beispiel in diesem Abschnitt weiter. Es veranschaulicht, wie Sie dies mit der REST-Schnittstelle erreichen.

1. Öffnen Sie in Xcode das `Main.storyboard`, und fügen Sie die folgenden UI-Komponenten aus der Objektbibliothek hinzu, damit Benutzer in der App Pushbenachrichtigungen senden können:

	- Eine Bezeichnung ohne Beschriftungstext. Sie wird zum Melden von Fehlern durch das Senden von Benachrichtigungen verwendet. Die **Lines**-Eigenschaft muss auf **0** festgelegt werden, damit die Größe automatisch entsprechend dem rechten und linken Rand sowie dem oberen Rand der Ansicht beschränkt wird.
	- Ein Textfeld mit **Platzhalter**-Text, das auf **Enter Notification Message** festgelegt ist. Beschränken Sie das Feld wie unten dargestellt direkt unter der Bezeichnung. Festlegen des View-Controllers als Outlet-Delegat.
	- Eine Schaltfläche mit der Bezeichnung **Send Notification**, die direkt unter dem Textfeld und in der horizontalen Mitte eingeschränkt ist.

	Die Ansicht sollte wie folgt aussehen:

	![Xcode Designer][32]


2. [Fügen Sie Auslässe](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) für die Bezeichnung und das Textfeld hinzu, die mit Ihrer Ansicht verbunden sind, und aktualisieren Sie Ihre `interface`-Definition, um `UITextFieldDelegate` und `NSXMLParserDelegate` zu unterstützen. Fügen Sie die drei unten angezeigten Eigenschaftendeklarationen hinzu, um den Aufruf der REST-API zu unterstützen und die Antwort zu analysieren.

	Die Datei „ViewController.h“ sollte nun folgendermaßen aussehen:

		#import <UIKit/UIKit.h>

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end

3. Öffnen Sie `HubInfo.h`, und fügen Sie die folgenden Konstanten hinzu, die für das Senden von Benachrichtigungen an den Hub verwendet werden. Ersetzen Sie den Platzhalter für das Zeichenfolgenliteral durch die tatsächliche Verbindungszeichenfolge *DefaultFullSharedAccessSignature*.

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Fügen Sie die folgenden `#import`-Anweisungen der Datei `ViewController.h` hinzu.

		#import <CommonCrypto/CommonHMAC.h>
		#import "HubInfo.h"

5. Fügen Sie in `ViewController.m` der Schnittstellenimplementierung den folgenden Code hinzu: Dieser Code dient zum Analysieren der Verbindungszeichenfolge *DefaultFullSharedAccessSignature*. Wie in der [REST-API-Referenz](http://msdn.microsoft.com/library/azure/dn495627.aspx) erwähnt, werden diese analysierten Informationen dazu verwendet, um ein SaS-Token für den Anforderungsheader **Authorization** zu erstellen.

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

6. Aktualisieren Sie in `ViewController.m` die `viewDidLoad`-Methode, um die Verbindungszeichenfolge beim Laden der Ansicht zu analysieren. Fügen Sie die (nachstehend gezeigten) Hilfsprogrammmethoden der Schnittstellenimplementierung hinzu.


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
			[_notificationMessage setDelegate:self];
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





7. Fügen Sie in `ViewController.m` der Schnittstellenimplementierung den folgenden Code zum Generieren des SaS-Autorisierungstokens hinzu, das im Header **Authorization** gemäß der [REST-API-Referenz](http://msdn.microsoft.com/library/azure/dn495627.aspx) bereitgestellt wird.

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

				// Construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
					signature, expires, HubSasKeyName, targetUri];
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


8. Ziehen Sie bei gedrückter STRG-TASTE die Schaltfläche **Benachrichtigung senden** auf `ViewController.m`, um eine Aktion mit dem Namen **SendNotificationMessage** für das Ereignis **Touch Down** hinzuzufügen. Aktualisieren Sie die Methode mit dem folgenden Code zum Senden der Benachrichtigung mithilfe der REST-API.

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

			// Construct the message's REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generate the token to be used in the authorization header
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNs notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify Apple notification format
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
		        if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
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


9. Fügen Sie in `ViewController.m` die folgende Delegatmethode hinzu, um das Schließen der Tastatur für das Textfeld zu unterstützen. Drücken Sie STRG + Ziehen aus dem Textfeld auf das View-Controller-Symbol im Benutzeroberflächen-Designer, um den View-Controller als Outlet-Delegat festzulegen.

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


10. Fügen Sie in `ViewController.m` die folgenden Delegatmethoden hinzu, um die Analyse der Antwort mithilfe von `NSXMLParser` zu unterstützen.

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



11. Erstellen Sie das Projekt, und überprüfen Sie, dass keine Fehler vorhanden sind.


> [AZURE.NOTE] Wenn in Xcode7 ein Buildfehler zur Unterstützung von Bitcode auftritt, sollten Sie **Buildeinstellungen** > **Enable Bitcode (ENABLE\_BITCODE)** in Xcode in **NO** ändern. Das Notification Hubs-SDK unterstützt derzeit keinen Bitcode.

Sie finden alle möglichen Benachrichtigungsnutzlasten im [Local and Push Notification Programming Guide] von Apple (in englischer Sprache).


##Überprüfen, ob die App Pushbenachrichtigungen empfangen kann

Zum Testen von Pushbenachrichtigungen unter iOS müssen Sie die App auf einem physischen iOS-Gerät bereitstellen. Sie können keine Apple-Pushbenachrichtigungen mit dem iOS-Simulator senden.

1. Führen Sie die App aus, und überprüfen Sie, ob die Registrierung erfolgreich ist. Drücken Sie dann **OK**.

	![Registrierungstest für iOS-App-Pushbenachrichtigungen][33]

2. Sie können eine Test-Pushbenachrichtigung wie oben beschrieben über das [Azure-Portal] senden. Wenn Sie Code zum Senden der Pushbenachrichtigung in der App hinzugefügt haben, tippen Sie in das Textfeld, um eine Benachrichtigung einzugeben. Drücken Sie dann die **Senden**-Taste auf der Tastatur, oder klicken Sie in der Ansicht auf die Schaltfläche **Benachrichtigung senden**, um die Nachricht zu senden.

	![iOS-App-Pushbenachrichtigung – Testsendung][34]

3. Die Pushbenachrichtigung wird an alle Geräte gesendet, die dafür registriert sind, die Benachrichtigungen vom jeweiligen Notification Hub zu erhalten.

	![iOS-App-Pushbenachrichtigung – Testempfang][35]


##Nächste Schritte

In diesem einfachen Beispiel haben Sie Pushbenachrichtigungen an alle Ihre registrierten iOS-Geräte übertragen. Als nächsten Lernschritt empfehlen wir das Tutorial [Azure Notification Hubs – Benachrichtigen von iOS-Benutzern über .NET-Back-End]. Darin sind die Schritte beschrieben, die für die Erstellung eines Back-Ends zu Senden von Pushbenachrichtigungen mit Tags ausgeführt werden müssen.

Wenn Sie Ihre Benutzer in Interessengruppen einteilen möchten, finden Sie im Tutorial [Verwenden von Notification Hubs zum Übermitteln von Nachrichten] hierzu weitere Informationen.

Allgemeine Informationen zu Notification Hubs finden Sie im [Notification Hubs-Leitfaden].



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Mobile Services iOS SDK version 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: https://github.com/Azure/azure-mobile-services/blob/master/CHANGELOG.ios.md#sdk-downloads
[Mobile Services iOS SDK-Version 2.x]: https://github.com/Azure/azure-mobile-services/blob/master/CHANGELOG.ios.md#sdk-downloads
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs – Benachrichtigen von iOS-Benutzern über .NET-Back-End]: notification-hubs-aspnet-backend-ios-notify-users.md
[Verwenden von Notification Hubs zum Übermitteln von Nachrichten]: notification-hubs-ios-send-breaking-news.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure-Portal]: https://portal.azure.com

<!---HONumber=AcomDC_0316_2016-->