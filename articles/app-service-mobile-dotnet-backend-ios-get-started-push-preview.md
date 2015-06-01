<properties 
	pageTitle="Hinzufügen von Pushbenachrichtigungen für die iOS-App mit Azure App Service" 
	description="Erfahren Sie, wie Azure App Service zum Senden von Pushbenachrichtigungen an Ihre iOS-App verwendet wird." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="yuaxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# Hinzufügen von Pushbenachrichtigungen zu Ihrer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

In diesem Thema wird veranschaulicht, wie mit Azure App Service Pushbenachrichtigungen an eine iOS-App mit Apple Push Notification Service (APNS) gesendet werden. Anschließend sendet Ihr .NET-Back-End jedes Mal eine Pushbenachrichtigung an Ihre Schnellstart-ToDo-App, wenn ein Eintrag eingefügt wird. Die App ist mit iOS 8 und früheren Versionen kompatibel.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Generieren einer iOS-Zertifikatsignieranforderung]
2. [Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3. [Erstellen eines Bereitstellungsprofils für die App]
4. [Konfigurieren des mobilen Back-Ends zum Senden von Pushanforderungen]
5. [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen](#update-server)
6. [Veröffentlichen des mobilen Back-Ends in Azure]
7. [Hinzufügen von Pushbenachrichtigungen zur App]
8. [Testen Ihrer App]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [App Service-SDK für mobile iOS-Apps]
+ [Azure Notification Hubs Nuget]
+ [XCode 4.5][Installieren von Xcode]
+ Ein Gerät, das auf iOS 6.0 (oder einer neueren Version) laufen kann
+ iOS-Entwicklerprogramm-Mitgliedschaft

   > [AZURE.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Dieses Lernprogramm baut auf dem App Service-Schnellstart für mobile Apps auf. Sie müssen zuerst [Erste Schritte mit mobilen App Service-Apps] ausführen, bevor Sie mit diesem Lernprogramm beginnen.

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## Konfigurieren der mobilen App zum Senden von Pushanforderungen

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**.

2. Suchen Sie nach **Microsoft.Azure.NotificationHubs**, und klicken Sie für alle Projekte in der Lösung auf **Installieren**.

3. Erweitern Sie im Projektmappen-Explorer von Visual Studio den Ordner **Controller** im Projekt für das mobile Back-End. Öffnen Sie "TodoItemController.cs". Fügen Sie am Anfang der Datei die folgenden `using`-Anweisungen hinzu:

		using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Fügen Sie der `PostTodoItem`-Methode nach dem Aufruf von **InsertAsync** den folgenden Codeausschnitt hinzu:  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        try
        {
            await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);
        }
        catch (System.Exception ex)
        {
            throw;
        }

    Dieser Code weist den mit dieser mobilen App verknüpften Notification Hub an, nach dem Einfügen eines Todo-Elements eine Pushbenachrichtigung zu senden.


<h2><a name="publish-the-service"></a>Veröffentlichen des mobilen Back-Ends in Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Hinzufügen von Pushbenachrichtigungen zur App
1. Herunterladen und Hinzufügen eines Verweises auf das App Service-Client-SDK für mobile Apps in XCode.

2. Fügen Sie in **QSAppDelegate.m** in **application:didFinishLaunchingWithOptions** den folgenden Code hinzu, um den Client für APNS (Apple Push Notification Service) zu registrieren:

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        }
        else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. Fügen Sie in derselben Datei innerhalb der **QSAppDelegate**-Implementierung die folgende Handlermethode hinzu:

        // registration with APNs is successful
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. Fügen Sie anschließend innerhalb der Implementierung die Fehlerbehandlungsmethode hinzu:

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:  

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = [userInfo objectForKey:@"aps"];
            NSString *alertString = [apsPayload objectForKey:@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                          alertString delegate:nil cancelButtonTitle:
                          @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

Ihre App kann Pushbenachrichtigungen nun unterstützen.

## Testen von Pushbenachrichtigungen in Ihrer App

1. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App in einem iOS-fähigen Gerät zu starten. Klicken Sie dann auf **OK**, um Pushbenachrichtigung zu akzeptieren.

  	![][23]

    > [AZURE.NOTE] Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. Geben Sie in der App einen aussagekräftigen Text ein, z. B. _Neue Mobile Services-Aufgabe_, und klicken Sie dann auf das Pluszeichen (**+**).

  	![][24]

3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

  	![][25]

4. Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Push angezeigt wird.

  	![][26]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

<!-- Anchors.  -->
[Generieren einer iOS-Zertifikatsignieranforderung]: #certificates
[Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
[Erstellen eines Bereitstellungsprofils für die App]: #profile
[Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
[Konfigurieren des mobilen Back-Ends zum Senden von Pushanforderungen]: #configure
[Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]: #update-server
[Veröffentlichen des mobilen Back-Ends in Azure]: #publish-mobile-service
[Testen Ihrer App]: #test-the-service

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
[App Service-SDK für mobile iOS-Apps]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure Notification Hubs Nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service (APNS)]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[apns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=49-->