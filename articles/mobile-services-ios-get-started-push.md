<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="Erste Schritte mit Pushbenachrichtigungen (iOS) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre iOS-App (Legacypush)." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Hinzufügen von Pushbenachrichtigungen zu Mobile Services-Apps (Legacy-Push)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
	<a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend">.NET-Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript backend" class="current">JavaScript-Back-End</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine iOS-App senden. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Services (APNS) Pushbenachrichtigungen zum Schnellstart-Projekt hinzu. Nach dem Abschluss sendet der mobile Dienst bei jedem Einfügen eines Datensatzes eine Pushbenachrichtigung.


>[WACOM.NOTE]In diesen Themen werden <em>vorhandene</em> mobile Dienste unterstützt, die <em>noch nicht</em> zum Verwenden der Notification-Hub-Integration aktualisiert wurden. Wenn Sie einen <em>neuen</em> mobilen Dienst erstellen, wird diese integrierte Funktionalität automatisch aktiviert. Weitere Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/).
>
>Mobile Dienste werden in Azure Notification-Hubs für die Unterstützung zusätzlicher Pushbenachrichtigungsfunktionen, wie z. B. Vorlagen, mehreren Plattformen und einer verbesserten Skalierung. <em>Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren</em>. Nach dem Upgrade finden Sie weitere Informationen unter [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Erstellen der Zertifikatsignieranforderungsdatei]
2. [Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3. [Erstellen eines Bereitstellungsprofils für die App]
3. [Konfigurieren von Mobile Services]
4. [Hinzufügen von Pushbenachrichtigungen zur App]
5. [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
6. [Einfügen von Daten zum Empfangen von Benachrichtigungen]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK]
+ [Xcode 4.5][Installieren von Xcode]
+ Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
+ iOS-Entwicklerprogramm-Mitgliedschaft

   > [WACOM.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst das Lernprogramm [Erste Schritte mit mobilen Diensten] abschließen, bevor Sie mit diesem Lernprogramm beginnen.

[WACOM.INCLUDE [Aktivieren von Apple-Pushbenachrichtigungen](../includes/enable-apple-push-notifications.md)]

## Konfigurieren von Mobile Services zum Senden von Pushanforderungen

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Hinzufügen von Pushbenachrichtigungen zur App

1. In Xcode öffnen Sie die Datei "QSAppDelegate.h", und fügen Sie die folgende Eigenschaft unter der ***window**-Eigenschaft hinzu:

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, wird automatisch eine neue "deviceToken"-Spalte zur **TodoItem**-Tabelle hinzugefügt, wenn ein neues Element mit dieser Eigenschaft eingefügt wird.

2. In QSAppDelegate.m ersetzen Sie die folgende Handlermethode in der Implementierung:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:  

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
            @"deviceToken" : delegate.deviceToken
        };

   	Auf diese Weise wird eine Referenz zum **QSAppDelegate** hinzugefügt, um den Gerätetoken zu erhalten, und die Anforderungsnutzlast dann modifiziert, um diesen Gerätetoken einzubeziehen.

   	> [WACOM.NOTE] Dieser Code muss vor dem Aufruf der <strong>addItem</strong>-Methode hinzugefügt werden.

Ihre App kann Pushbenachrichtigungen nun unterstützen.

## Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**.

   	![][21]

2. Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

  	![][22]

   	Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

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

   	Auf diese Weise wird ein neues Einfügeskript registriert, das über das [apns-Objekt] eine Pushbenachrichtigung (den eingefügten Text) an das in der Einfügeanforderung angegebene Gerät sendet.


   	> [WACOM.NOTE] Dieses Skript verzögert die Übermittlung der Benachrichtigung, um Ihnen Zeit zum Schließen der App für den Empfang einer Toastbenachrichtigung zu geben.

## Testen von Pushbenachrichtigungen in der App

1. Drücken Sie auf **Ausführen**, um das Projekt zu erstellen, starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren

  	![][23]

    > [WACOM.NOTE] Pushbenachrichtigungen von Ihrer App müssen ausdrücklich akzeptiert werden. Diese Anforderung tritt nur bei der ersten Ausführung der App auf.

2. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Eine neue mobile Dienstaufgabe_, und klicken Sie dann auf die Schaltfläche **+**.

  	![][24]

3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

  	![][25]

4. Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Toast angezeigt wird.

  	![][26]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Nächste Schritte

IIn diesem einfachen Beispiel empfängt ein Benutzer eine Pushbenachrichtigung mit den soeben eingegebenen Daten. Der von APNS verwendete Gerätetoken wird vom Client in der Anforderung an den mobilen Dienst geliefert. Im nächsten Lernprogramm [Pushbenachrichtigungen an App-Benutzer] werden wir eine eigene Gerätetabelle zum Speichern von Gerätetoken erstellen und beim Auftreten einer Einfügung eine Pushbenachrichtigung an alle gespeicherten Kanäle aussenden.

<!-- Anchors. -->
[Erstellen der Zertifikatsignieranforderungsdatei]: #certificates
[Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
[Erstellen eines Bereitstellungsprofils für die App]: #profile
[Konfigurieren von Mobile Services]: #configure
[Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
[Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
[Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
[Nächste Schritte]:#next-steps

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Installieren von Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS-Bereitstellungsportal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Pushbenachrichtigungsdienst]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios
[Senden von Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[apns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=272333
