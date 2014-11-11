<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />


# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!-- <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="JavaScript-Backend">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine iOS-App senden. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Services (APNS) Pushbenachrichtigungen zum Schnellstart-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.


In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Erstellen der Zertifikatsignieranforderungsdatei][Erstellen der Zertifikatsignieranforderungsdatei]
2. [Registrieren der App und Aktivieren von Pushbenachrichtigungen][Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3. [Erstellen eines Bereitstellungsprofils für die App][Erstellen eines Bereitstellungsprofils für die App]
4. [Lokaler Download des Diensts][Lokaler Download des Diensts]
5. [Testen des mobilen Diensts][Testen des mobilen Diensts]
6. [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen][Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]
7. [Veröffentlichen des mobilen Diensts in Azure][Veröffentlichen des mobilen Diensts in Azure]
8. [Hinzufügen von Pushbenachrichtigungen zur App][Hinzufügen von Pushbenachrichtigungen zur App]
9. [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
10. [Aktivieren von Pushbenachrichtigungen für lokale Tests][Aktivieren von Pushbenachrichtigungen für lokale Tests]
11. [Testen der App mit dem veröffentlichten mobilen Dienst][Testen der App mit dem veröffentlichten mobilen Dienst]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK][Mobile Services iOS SDK]
+ [XCode 4.5][XCode 4.5]
+ Ein Gerät, das auf iOS 6,0 (oder einer neueren Version) laufen kann
+ iOS-Entwicklerprogramm-Mitgliedschaft

   > [WACOM.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.


[WACOM.INCLUDE [Pushbenachrichtigungen aktivieren](../includes/enable-apple-push-notifications.md)]


## Konfigurieren von Mobile Services zum Senden von Pushanforderungen

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

<h2><a name="download-the-service"></a><span class="short-header">Herunterladen des Diensts</span>Herunterladen des Diensts auf Ihren lokalen Computer</h2>

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

<h2><a name="test-the-service"></a><span class="short-header">Testen des Diensts</span>Testen des mobilen Diensts</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a>Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

1. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie TodoItemController.cs. Fügen Sie am Anfang der Datei die folgenden `using`-Anweisungen hinzu:


		using System;
		using System.Collections.Generic;

2. Ersetzen Sie die `PostTodoItem`-Methode durch den folgenden Code:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Dieser Code sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags), nachdem ein todo-Eintrag eingefügt wurde. Falls ein Fehler auftritt, wird vom Code ein Fehlerprotokolleintrag hinzugefügt, der auf der Registerkarte **Protokolle** des mobilen Diensts im Verwaltungsportal angezeigt werden kann.


<h2><a name="publish-the-service"></a><span class="short-header">Veröffentlichen des Diensts</span>Veröffentlichen des mobilen Diensts in Azure</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Hinzufügen von Pushbenachrichtigungen zur App

1. In QSAppDelegate.m ersetzen Sie die folgende Handlermethode in der Implementierung:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
			client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5. In QSTodoListViewController.m importieren Sie die Datei "QSAppDelegate.h", sodass Sie den Delegaten zum Erhalten des Gerätetokens verwenden können:

        #import "QSAppDelegate.h"

6. In QSTodoListViewController.m modifizieren Sie die Aktion **(IBAction)onAdd**, indem Sie die folgende Zeile aufsuchen:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 
 
   Ersetzen Sie dies durch den folgenden Code:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];
    
        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

   Auf diese Weise wird eine Referenz zum **QSAppDelegate** hinzugefügt, um den Gerätetoken zu erhalten, und die Anforderungsnutzlast dann modifiziert, um diesen Gerätetoken einzubeziehen.

   > [WACOM.NOTE] Dieser Code muss vor dem Aufruf der **addItem**-Methode hinzugefügt werden.

Ihre App kann Pushbenachrichtigungen nun unterstützen.

## <span id="local-testing"></span></a>Aktivieren von Pushbenachrichtigungen für lokale Tests

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## Testen von Pushbenachrichtigungen in der App

1. Drücken Sie auf **Run**, um das Projekt zu erstellen, starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren

    ![][0]

    > [WACOM.NOTE] Pushbenachrichtigungen von Ihrer App müssen ausdrücklich akzeptiert werden. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. In der App geben Sie eine Beschreibung ein, wie zum Beispiel *Neue Mobile Services-Aufgabe*, und klicken Sie dann auf das Plussymbol (**+**).

    ![][1]

3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

    ![][2]

4. Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Push angezeigt wird.

    ![][3]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Nächste Schritte

Dieses Lernprogramm zeigt die Grundlagen, mit denen eine iOS-App Mobile Services und Benachrichtigungs-Hubs für das Senden von Pushbenachrichtigungen nutzen kann. Nun sollten Sie sich das nächste Lernprogramm ansehen, [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer], das zeigt, wie Sie Tags einsetzen, um Pushbenachrichtigungen von einem Mobile Services ausschließlich an authentifizierte Benutzer zu senden.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  
+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.  
+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

* [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

* [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

<!-- Anchors.  --> 
<!-- Images. --> 
<!-- URLs. -->

[Windows Store C#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Windows Store C#"
[Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Windows Store JavaScript"
[Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
[iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
[Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
[.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ ".NET-Backend"
[JavaScript-Backend]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/ "JavaScript-Backend"
[Erstellen der Zertifikatsignieranforderungsdatei]: #certificates
[Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
[Erstellen eines Bereitstellungsprofils für die App]: #profile
[Lokaler Download des Diensts]: #download-the-service-locally
[Testen des mobilen Diensts]: #test-the-service
[Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]: #update-server
[Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
[Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
[Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
[Aktivieren von Pushbenachrichtigungen für lokale Tests]: #local-testing
[Testen der App mit dem veröffentlichten mobilen Dienst]: #test-app
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Pushbenachrichtigungen aktivieren]: ../includes/enable-apple-push-notifications.md
[mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
[mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
[mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
[mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
[mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
[0]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
