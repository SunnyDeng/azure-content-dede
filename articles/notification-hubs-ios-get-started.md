<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/24/2014" ms.author="krisragh" />

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine iOS-App senden.
Sie erstellen in diesem Lernprogramm eine leere iOS-App, die Pushbenachrichtigungen über den Apple Push Notification Service (APNS) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Erstellen der Zertifikatsignieranforderungsdatei][Erstellen der Zertifikatsignieranforderungsdatei]
2.  [Registrieren der App und Aktivieren von Pushbenachrichtigungen][Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3.  [Erstellen eines Bereitstellungsprofils für die App][Erstellen eines Bereitstellungsprofils für die App]
4.  [Konfigurieren Ihres Notification Hub][Konfigurieren Ihres Notification Hub]
5.  [Verbinden Ihrer App mit dem Notification Hub][Verbinden Ihrer App mit dem Notification Hub]
6.  [Senden von Benachrichtigungen von Ihrem Back-End aus][Senden von Benachrichtigungen von Ihrem Back-End aus]

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Benachrichtigungshubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Mobile Services iOS SDK][Mobile Services iOS SDK]
-   [XCode 4.5][XCode 4.5]
-   Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
-   iOS-Entwicklerprogramm-Mitgliedschaft

	<div class="dev-callout">
	<b>Hinweis</b><br />
	<p>Pushbenachrichtigungen m&uuml;ssen aufgrund von Konfigurationsanforderungen auf einem iOS-f&auml;higen Ger&auml;t (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.</p>
	</div>

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für iOS-Apps.<br></br><br></br>
<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschlie&szlig;en. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## <a name="configure-hub"></a>Konfigurieren Ihres Notification Hub

1.  Klicken Sie in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neue Zertifikat der Schnellstart-App unter **Meine Zertifikate**. Klicken Sie auf **Exportieren**, benennen Sie die Datei, wählen Sie das Format **.p12** aus, und klicken Sie dann auf **Sichern**.

    ![][0]

Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

> [WACOM.NOTE] In diesem Lernprogramm wird eine QuickStart.p12-Datei erstellt. Name und Ort Ihrer Datei können verschieden sein.

1.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und klicken Sie unten auf dem Bildschirm auf **+NEU**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

    ![][1]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

    ![][2]

4.  Klicken Sie auf den soeben erstellten Namespace (für gewöhnlich ***Name des Notification Hub*-ns**), und anschließend oben auf die Registerkarte **Configure**.

    ![][3]

5.  Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

    ![][4]

6.  Wählen Sie oben die Registerkarte **Configure**, und klicken Sie dann für die Apple-Benachrichtigungseinstellungen auf **Upload**. Wählen Sie anschließend das zuvor exportierte **.p12**-Zertifikat und das Kennwort für das Zertifikat aus. Wählen Sie aus, ob Sie den Pushdienst **Production** (wenn Sie Pushbenachrichtigungen an Benutzer versenden möchten, die Ihre App aus dem Store erworben haben) oder **Sandbox** (während der Entwicklung) verwenden möchten.

    ![][5]

7.  Wählen Sie oben die Registerkarte **Dashboard**, und klicken Sie danach auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

    ![][6]

Der Notification Hub ist nun konfiguriert, um mit APNs arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

## <a name="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

1.  Erstellen Sie in XCode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

    ![][7]

2.  Klicken Sie unter **Targets** auf den Projektnamen, erweitern Sie **Code Signing Identity**, und wählen Sie unter **Debug** das Identitätsprofil für die Codesignierung aus. Wenn Sie eine neuere Version von XCode verwenden, können Sie zusätzlich für **Levels** von **Basic** zu **All** wechseln und das Zeilenelement **Provisioning Profile** zum Bereitstellungsprofil ändern.

    ![][8]

3.  Laden Sie den Azure Mobile SDK für iOS herunter. Öffnen Sie die .zip-Datei, und ziehen Sie den Ordner "WindowsAzureMessaging.framework" in den Framework-Ordner Ihres XCode-Projekts. Wählen Sie **Copy items in destination group's folder**, und klicken Sie dann auf **OK**.

    ![][9]

4.  Fügen Sie in der Datei "AppDelegate.h" die folgende Importdirektive hinzu:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  Fügen Sie in der Datei "AppDelegate.m" folgenden Code der Methode `didFinishLaunchingWithOptions` hinzu:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  Fügen Sie in derselben Datei die folgende Methode hinzu:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                      @"<connection string>" notificationHubPath:@"mynh"];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

7.  *(optional)* Fügen Sie in derselben Datei folgende Methode hinzu, um einen **UIAlert** anzuzeigen, wenn die Benachrichtigung erhalten wird, während die App aktiv ist:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

8.  Führen Sie die App auf Ihrem Gerät aus.

## <a name="send"></a>Senden von Benachrichtigungen von Ihrem Back-End aus

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die [REST-Schnittstelle][REST-Schnittstelle] senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung. Ein Beispiel zum Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End aus finden Sie unter **Erste Schritte mit Pushbenachrichtigungen in Mobile Services** ([.NET-Back-End][.NET-Back-End] | [JavaScript-Back-End][.NET-Back-End]). Ein Beispiel zum Senden von Benachrichtigungen über die REST-APIs finden Sie unter **Verwenden von Notification Hubs von Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  Wählen Sie in Visual Studio im Menü **Datei** die Optionen **Neu** und dann **Projekt...** aus, und klicken Sie anschließend unter **Visual C#** auf **Windows**, **Konsolenanwendung** und **OK**.

    ![][20]

    Damit wird ein neues Konsolenanwendungsprojekt erstellt.

2.  Klicken Sie im Menü **Extras** auf **Bibliothekspaket-Manager** und danach auf **Paket-Manager-Konsole**.

    Daraufhin wird die Paket-Manager-Konsole angezeigt.

3.  Führen Sie im Konsolenfenster folgenden Befehl aus:

        Install-Package WindowsAzure.ServiceBus

    Damit wird ein Verweis auf das Azure Service Bus-SDK mit dem [WindowsAzure.ServiceBus NuGet-Paket][WindowsAzure.ServiceBus NuGet-Paket] hinzugefügt.

4.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

5.  Fügen Sie in der Klasse **Program** folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

    Stellen Sie sicher, dass Sie den Platzhalter "hub name" durch den Namen des Notification Hub ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt "Konfigurieren Ihres Notification Hub" erhalten haben.

    > [WACOM.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

6.  Fügen Sie danach die folgenden Zeilen zur **Main**-Methode hinzu:

         SendNotificationAsync();
         Console.ReadLine();

7.  Drücken Sie die Taste F5, um die Konsolenanwendung auszuführen.

    Sie sollten eine Warnung auf Ihrem Gerät erhalten. Überprüfen Sie die Verbindung, wenn Sie WLAN verwenden.

Sie finden alle möglichen Nutzlasten im [Apple Local and Push Notification Programming Guide][Apple Local and Push Notification Programming Guide] (in englischer Sprache).

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Ihre iOS-Geräte übertragen. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten][Verwenden von Notification Hubs zum Übermitteln von Nachrichten] lesen. Weitere Informationen zur Verwendung von Benachrichtigungshubs finden Sie in der [Benachrichtigungshubs-Anleitung][Benachrichtigungshubs-Anleitung].

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Erstellen der Zertifikatsignieranforderungsdatei]: #certificates
  [Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
  [Erstellen eines Bereitstellungsprofils für die App]: #profile
  [Konfigurieren Ihres Notification Hub]: #configure-hub
  [Verbinden Ihrer App mit dem Notification Hub]: #connecting-app
  [Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
  [Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Enable Apple Push Notifications]: ../includes/enable-apple-push-notifications.md
  [0]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [1]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [6]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [7]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [8]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
  [9]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png
  [REST-Schnittstelle]: http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx
  [.NET-Back-End]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Java]: /de-de/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /de-de/documentation/articles/notification-hubs-php-backend-how-to/
  [WindowsAzure.ServiceBus NuGet-Paket]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Apple Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users-aspnet
  [Verwenden von Notification Hubs zum Übermitteln von Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
  [Benachrichtigungshubs-Anleitung]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
