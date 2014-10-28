<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS" class="current">iOS</a><a href="/de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a><!---<a href="/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET-Back-End" >.NET-Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="JavaScript-Back-End" class="current">JavaScript-Back-End</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine iOS-App senden. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Services (APNS) Pushbenachrichtigungen zum Schnellstart-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

> [WACOM.NOTE] In diesem Lernprogramm wird die Mobile Services-Integration in Notification Hubs erläutert, über die Sie Pushbenachrichtigungen von Ihrem mobilen Dienst aus senden können. Wenn Sie einen älteren mobilen Dienst verwenden, der herkömmliche Pushbenachrichtigungen verwendet und noch nicht auf Notification Hubs aktualisiert wurde, *empfehlen wir ein Upgrade* als Teil dieses Lernprogramms. Wenn Sie jetzt kein Upgrade durchführen möchten, verwenden Sie diese Version des Lernprogramms: [Erste Schritte mit Pushbenachrichtigungen (Legacy)][Erste Schritte mit Pushbenachrichtigungen (Legacy)].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Erstellen der Zertifikatsignieranforderungsdatei][Erstellen der Zertifikatsignieranforderungsdatei]
2.  [Registrieren der App und Aktivieren von Pushbenachrichtigungen][Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3.  [Erstellen eines Bereitstellungsprofils für die App][Erstellen eines Bereitstellungsprofils für die App]
4.  [Konfigurieren von Mobile Services][Konfigurieren von Mobile Services]
5.  [Hinzufügen von Pushbenachrichtigungen zur App][Hinzufügen von Pushbenachrichtigungen zur App]
6.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
7.  [Einfügen von Daten zum Empfangen von Benachrichtigungen][Einfügen von Daten zum Empfangen von Benachrichtigungen]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Mobile Services iOS SDK][Mobile Services iOS SDK]
-   [XCode 4.5][XCode 4.5]
-   Ein Gerät, das auf iOS 6.0 (oder einer neueren Version) laufen kann
-   iOS-Entwicklerprogramm-Mitgliedschaft

> [WACOM.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

[WACOM.INCLUDE [Aktivieren von Apple Pushbenachrichtigungen][Aktivieren von Apple Pushbenachrichtigungen]]

## Konfigurieren von Mobile Services zum Senden von Pushanforderungen

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

## Hinzufügen von Pushbenachrichtigungen zur App

1.  In QSAppDelegate.m ersetzen Sie die folgende Handlermethode in der Implementierung:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

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

5.  In QSTodoListViewController.m importieren Sie die Datei "QSAppDelegate.h", sodass Sie den Delegaten zum Erhalten des Gerätetokens verwenden können:

        #import "QSAppDelegate.h"

6.  In QSTodoListViewController.m modifizieren Sie die Aktion **(IBAction)onAdd**, indem Sie die folgende Zeile aufsuchen:

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

## Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][]

2.  Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![][1]

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Auf diese Weise wird ein neues Einfügeskript registriert, das über das [apns-Objekt][apns-Objekt] eine Pushbenachrichtigung (den eingefügten Text) zum in der Einfügeanforderung angegebenen Gerät sendet.

    > [WACOM.NOTE] Dieses Skript verzögert die Übermittlung der Benachrichtigung, um Ihnen Zeit zum Schließen der App für Empfang einer Pushbenachrichtigung zu geben.

## Testen von Pushbenachrichtigungen in der App

1.  Drücken Sie auf **Run**, um das Projekt zu erstellen, starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren

    ![][2]

    > [WACOM.NOTE] Pushbenachrichtigungen von Ihrer App müssen ausdrücklich akzeptiert werden. Diese Anforderung tritt nur beim ersten Ausführen der App auf.

2.  In der App geben Sie eine Beschreibung ein, wie zum Beispiel *Neue Mobile Services-Aufgabe*, und klicken Sie dann auf das Plussymbol (**+**).

    ![][3]

3.  Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

    ![][4]

4.  Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Push angezeigt wird.

    ![][5]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen aufgezeigt. Führen Sie als Nächstes eines der folgenden Lernprogramme aus:

-   [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer]
    Erfahren Sie, wie Sie Tags verwenden, um Pushbenachrichtigungen von einem Mobile Service nur an authentifizierte Benutzer zu senden.

-   [Senden von Benachrichtigungen an Abonnenten][Senden von Benachrichtigungen an Abonnenten]
    Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien empfangen können, für die sie sich interessieren.
    <!--- + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->
    Weitere Informationen zu Mobile Services und Notification Hubs finden Sie in den folgenden Themen:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Notification Hubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

-   [Mobile Services Objective-C-Anleitungen: Konzeptionelle Referenz][Mobile Services Objective-C-Anleitungen: Konzeptionelle Referenz]
    Erfahren Sie mehr über die Verwendung von Mobile Services mit Objective-C und iOS.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Erfahren Sie mehr über die Implementierung von Geschäftslogik in Ihren mobilen Dienst.

<!-- Anchors. --> <!-- Images. --> <!-- URLs.   -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android"
  [.NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ ".NET-Back-End"
  [JavaScript-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "JavaScript-Back-End"
  [Erste Schritte mit Pushbenachrichtigungen (Legacy)]: /de-de/documentation/articles/mobile-services-ios-get-started-push/
  [Erstellen der Zertifikatsignieranforderungsdatei]: #certificates
  [Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
  [Erstellen eines Bereitstellungsprofils für die App]: #profile
  [Konfigurieren von Mobile Services]: #configure
  [Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
  [Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-ios-get-started
  [Aktivieren von Apple Pushbenachrichtigungen]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  []: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [apns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/
  [Senden von Benachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-ios-send-breaking-news/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-ios-get-started-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-ios-get-started-users
  [Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
  [Mobile Services Objective-C-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
