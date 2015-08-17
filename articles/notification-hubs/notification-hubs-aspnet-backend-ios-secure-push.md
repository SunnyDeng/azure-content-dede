<properties 
	pageTitle="Azure Notification Hubs – Sichere Pushbenachrichtigungen" 
	description="Erfahren Sie mehr über das Senden sicherer Pushbenachrichtigungen an eine iOS-App von Azure. Die Codebeispiele wurden in Objekctive-C und C# geschrieben." 
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
	ms.date="06/02/2015" 
	ms.author="wesmc"/>

#Azure Notification Hubs – Sichere Pushbenachrichtigungen

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-secure-push.md)
- [iOS](notification-hubs-aspnet-backend-ios-secure-push.md)
- [Android](notification-hubs-aspnet-backend-android-secure-push.md)


##Übersicht

Durch die Unterstützung von Pushbenachrichtigungen in Microsoft Azure haben Sie Zugriff auf eine benutzerfreundliche, plattformübergreifende und horizontal skalierte Pushinfrastruktur, die die Implementierung von Pushbenachrichtigungen sowohl für Endbenutzer- als auch für Unternehmensanwendungen für mobile Plattformen erheblich vereinfacht.

Es kann vorkommen, dass eine Anwendung etwas in die Benachrichtigung einschließen möchte, dass aufgrund gesetzlicher oder sicherheitsbedingter Einschränkungen nicht über die Standardinfrastruktur von Pushbenachrichtigungen übertragen werden kann. In diesem Lernprogramm wird beschrieben, wie Sie die gleiche Funktionalität erzielen, indem Sie vertrauliche Informationen über eine sichere authentifizierte Verbindung zwischen dem Clientgerät und dem App-Back-End senden..

Der generelle Ablauf sieht folgendermaßen aus:

1. Das App-Back-End:
	- Speichert die sichere Nutzlast in einer Back-End-Datenbank.
	- Sendet die ID dieser Benachrichtigung an das Gerät (es werden keine sicheren Informationen gesendet).
2. Bei Erhalt der Benachrichtigung reagiert die App auf dem Gerät folgendermaßen:
	- Das Gerät kontaktiert das Back-End und fordert die sichere Nutzlast an.
	- Die App kann die Nutzlast als Benachrichtigung auf dem Gerät anzeigen.

Beachten Sie, dass im obigen Ablauf (und in diesem Lernprogramm) angenommen wird, dass das Gerät ein Authentifizierungstoken im lokalen Speicher speichert, nachdem sich der Benutzer angemeldet hat. Dies gewährleistet einen vollständig nahtlosen Ablauf, da das Gerät mit diesem Token die sichere Nutzlast der Benachrichtigung abrufen kann. Wenn Ihre Anwendung keine Authentifizierungstoken auf dem Gerät speichert oder diese Token ablaufen können, sollte die Geräte-App nach Erhalt der Benachrichtigung eine generische Benachrichtigung anzeigen, in der der Benutzer zum Starten der App aufgefordert wird. Anschließend authentifiziert die App den Benutzer und zeigt die Nutzlast der Benachrichtigung an.

Dieses Lernprogramm zu sicheren Pushbenachrichtigungen veranschaulicht das sichere Senden einer Pushbenachrichtigung. Es baut auf dem Lernprogramm **Benachrichtigen von Benutzern** auf, daher sollten Sie die Schritte in diesem Lernprogramm zuerst durchführen.

> [AZURE.NOTE]In diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (iOS)](notification-hubs-ios-get-started.md) beschrieben erstellt und konfiguriert haben.

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## Ändern des iOS-Projekts

Nachdem Sie Ihr App-Back-End so geändert haben, dass lediglich die *ID* einer Benachrichtigung gesendet wird, müssen Sie Ihre iOS-App so ändern, dass sie diese Benachrichtigung verarbeitet und einen Rückruf an das Back-End sendet, um die anzuzeigende sichere Nachricht abzurufen.

Hierzu müssen wir die Logik zum Abrufen des sicheren Inhalts aus dem App-Back-End schreiben.

1. Stellen Sie in **AppDelegate.m** sicher, dass die App für automatische Benachrichtigungen registriert ist, damit sie die vom Back-End gesendete Benachrichtigungs-ID verarbeiten kann. Fügen Sie die unter "didFinishLaunchingWithOptions" die Option **UIRemoteNotificationTypeNewsstandContentAvailability** hinzu:

		[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. Fügen Sie am Anfang von **AppDelegate.m** einen implementation-Abschnitt mit der folgenden Deklaration hinzu:

		@interface AppDelegate ()
		- (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
		@end

3. Fügen Sie dann im implementation-Abschnitt den folgenden Code hinzu, und ersetzen Sie dabei den Platzhalter `{back-end endpoint}` durch den Back-End-Endpunkt, den Sie zuvor erhalten haben:

```
		NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

		- (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
		{
		    // check if authenticated
		    ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
		    NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
		    if (!authenticationHeader) return;


		    NSURLSession* session = [NSURLSession
		                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
		                             delegate:nil
		                             delegateQueue:nil];


		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"GET"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

		            NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

		            completion([json objectForKey:@"Payload"], nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}
```

	This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. Nun müssen wird die eingehende Benachrichtigung verarbeiten und den anzuzeigenden Inhalt mit der vorstehenden Methode abrufen. Zunächst müssen festlegen, dass die iOS-App im Hintergrund ausgeführt werden kann, wenn eine Pushbenachrichtigung empfangen wird. Wählen Sie in **XCode** Ihr App-Projekt im linken Bereich aus, und klicken Sie dann im Abschnitt **Targets** des mittleren Fensters auf Ihr Haupt-App-Ziel.

5. Klicken Sie dann oben im mittleren Fenster auf die Registerkarte **Capabilities**, und aktivieren Sie das Kontrollkästchen **Remote Notifications**.

	![][IOS1]


6. Fügen Sie in **AppDelegate.m** die folgende Methode zur Verarbeitung von Pushbenachrichtigungen hinzu:

		-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
		{
		    NSLog(@"%@", userInfo);

		    [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
		        if (!error) {
		            // show local notification
		            UILocalNotification* localNotification = [[UILocalNotification alloc] init];
		            localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
		            localNotification.alertBody = payload;
		            localNotification.timeZone = [NSTimeZone defaultTimeZone];
		            [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

		            completionHandler(UIBackgroundFetchResultNewData);
		        } else {
		            completionHandler(UIBackgroundFetchResultFailed);
		        }
		    }];

		}

	Beachten Sie, dass eine fehlende Authentifizierungsheader-Eigenschaft oder eine Ablehnung durch das Back-End nach Möglichkeit behandelt werden sollte. Wie diese Fälle im Einzelnen zu behandeln sind, hängt größtenteils von der Umgebung des Zielbenutzers ab. Eine Möglichkeit besteht darin, eine Benachrichtigung mit einer generischen Eingabeaufforderung anzuzeigen, damit sich der Benutzer zum Abrufen der eigentlichen Benachrichtigung authentifiziert.

## Ausführen der Anwendung

Gehen Sie zum Ausführen der Anwendung folgendermaßen vor:

1. Unter XCode führen Sie die App auf einem physischen iOS-Gerät aus (im Simulator funktionieren Pushbenachrichtigungen nicht).

2. Geben Sie in der iOS-App-UI einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben.

3. Klicken Sie in der iOS-App-UI auf **Log in**. Klicken Sie anschließend auf **Send push**. Die sichere Benachrichtigung sollte nun in der Mitteilungszentrale angezeigt werden.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
 

<!---HONumber=August15_HO6-->