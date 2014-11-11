<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app (legacy push)." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services (Legacypushvorgang)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
    <a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET-Backend">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript-Backend" class="current">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine iOS-App senden. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Services (APNS) Pushbenachrichtigungen zum Schnellstart-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

> [WACOM.NOTE]In diesem Thema werden *vorhandene* mobile Dienste behandelt, die noch nicht für die Verwendung der Notification Hubs-Integration *aktualisiert* wurden. Wenn Sie einen *neuen* mobilen Dienst erstellen, wird diese integrierte Funktionalität automatisch aktiviert. Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].
>
> Mobile Services werden in Azure Notification Hubs integriert, damit zusätzliche Pushbenachrichtigungsfunktionen wie Vorlagen, mehrere Plattformen und verbesserte Skalierung unterstützt werden können. *Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren*. Nach dem Upgrade finden Sie weitere Informationen in dieser Version von [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].

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
-   [Xcode 4.5][Xcode 4.5]
-   Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
-   iOS-Entwicklerprogramm-Mitgliedschaft

> [WACOM.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## Konfigurieren von Mobile Services zum Senden von Pushanforderungen

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Hinzufügen von Pushbenachrichtigungen zur App

1.  In Xcode öffnen Sie die Datei "QSAppDelegate.h", und fügen Sie die folgende Eigenschaft unter der \***window**-Eigenschaft hinzu:

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, wird automatisch eine neue "deviceToken"-Spalte zur **TodoItem**-Tabelle hinzugefügt, wenn ein neues Element mit dieser Property eingefügt wird.

2.  In QSAppDelegate.m ersetzen Sie die folgende Handlermethode in der Implementierung:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  In QSTodoListViewController.m importieren Sie die Datei "QSAppDelegate.h", sodass Sie den Delegaten zum Erhalten des Gerätetokens verwenden können:

        #import "QSAppDelegate.h"

7.  In QSTodoListViewController.m modifizieren Sie die Aktion **(IBAction)onAdd**, indem Sie die folgende Zeile aufsuchen:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

Ersetzen Sie dies durch den folgenden Code:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    This adds a reference to the **QSAppDelegate** to obtain the device token and then modifies the request payload to include that device token.

    > [WACOM.NOTE] You must add this code before to the call to the <strong>addItem</strong> method.

Ihre App kann Pushbenachrichtigungen nun unterstützen.

## Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][0]

2.  Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![][1]

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Auf diese Weise wird ein neues Einfügeskript registriert, das über das [apns-Objekt][apns-Objekt] eine Pushbenachrichtigung (den eingefügten Text) an das in der Einfügeanforderung angegebene Gerät sendet.

    > [WACOM.NOTE] Dieses Skript verzögert den Versand der Benachrichtigung, um Ihnen Zeit zum Schließen der App für den Empfang einer Popupbenachrichtigung zu geben.

## Testen von Pushbenachrichtigungen in der App

1.  Drücken Sie auf **Run**, um das Projekt zu erstellen, starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren

    ![][2]

    > [WACOM.NOTE] Pushbenachrichtigungen müssen von Ihrer App ausdrücklich akzeptiert werden. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2.  In der App geben Sie eine Beschreibung ein, wie zum Beispiel *Neue Mobile Services-Aufgabe*, und klicken Sie dann auf das Plussymbol (**+**).

    ![][3]

3.  Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

    ![][4]

4.  Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Toast angezeigt wird.

    ![][5]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Nächste Schritte

In diesem einfachen Beispiel empfängt ein Benutzer eine Benachrichtigung mit den soeben eingegebenen Daten. Der von APNS verwendete Gerätetoken wird vom Client in der Anforderung an den mobilen Dienst geliefert. Im nächsten Lernprogramm [Pushbenachrichtigungen an App-Benutzer][Pushbenachrichtigungen an App-Benutzer] werden wir eine eigene Gerätetabelle zum Speichern von Gerätetoken erstellen und beim Auftreten einer Einfügung eine Pushbenachrichtigung an alle gespeicherten Kanäle aussenden.



  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Erstellen der Zertifikatsignieranforderungsdatei]: #certificates
  [Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
  [Erstellen eines Bereitstellungsprofils für die App]: #profile
  [Konfigurieren von Mobile Services]: #configure
  [Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
  [Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [Xcode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
  [Enable Apple Push Notifications]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [apns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-ios
