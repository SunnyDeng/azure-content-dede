<properties 
	pageTitle="Erste Schritte mit Pushbenachrichtigungen (iOS) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre iOS-App." 
	services="mobile-services, notification-hubs" 
	documentationCenter="ios" 
	manager="dwrede" editor="" 
	authors="krisragh"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="krisragh"/>


# Hinzufügen von Pushbenachrichtigungen zu einer Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine iOS-App senden. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Services (APNS) Pushbenachrichtigungen zum Schnellstart-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.


In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Erstellen der Zertifikatsignieranforderungsdatei]
2. [Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3. [Erstellen eines Bereitstellungsprofils für die App]
4. [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen](#update-server)
5. [Veröffentlichen des mobilen Dienstes in Azure]
6. [Hinzufügen von Pushbenachrichtigungen zur App]
7. [Aktivieren von Pushbenachrichtigungen für lokale Tests](#local-testing)
8. [Testen der App mit dem veröffentlichten mobilen Dienst]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK]
+ [XCode 4.5][Installieren von Xcode]
+ Ein Gerät, das auf iOS 6.0 (oder einer neueren Version) laufen kann
+ iOS-Entwicklerprogramm-Mitgliedschaft

   > [AZURE.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zur App][Erste Schritte mit Daten] abschließen, bevor Sie mit diesem Lernprogramm beginnen.


[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]


## Konfigurieren von Mobile Services zum Senden von Pushanforderungen

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]


##<a id="update-server"></a>Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

1. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie "TodoItemController.cs". Fügen Sie am Anfang der Datei die folgenden  `using`-Anweisungen hinzu:


		using System;
		using System.Collections.Generic;

2. Aktualisieren Sie die  `PostTodoItem`-Methode mit dem folgenden Code:  

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


<h2><a name="publish-the-service"></a>Veröffentlichen des mobilen Dienstes in Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Hinzufügen von Pushbenachrichtigungen zur App

1. Fügen Sie in QSAppDelegate.m den folgenden Codeausschnitt ein, um das Mobile Services iOS SDK zu importieren:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. In QSAppDelegate.m ersetzen Sie die folgende Handlermethode in der Implementierung:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. Fügen Sie in QSAppDelegate.m die folgende Handlermethode in der Implementierung hinzu. Kopieren Sie unbedingt die Werte für die Mobile Service-URL und den Anwendungsschlüssel, und ersetzen Sie die Platzhalter damit:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
            MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:  

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

   > [AZURE.NOTE] </strong>Sie müssen diesen Code hinzufügen, bevor Sie die <strong>addItemaddItem-Methode aufrufen.

Ihre App kann Pushbenachrichtigungen nun unterstützen.

##<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## Testen von Pushbenachrichtigungen in der App

1. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen, starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren.

  	![][23]

    > [AZURE.NOTE] Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. Geben Sie in der App sinnvollen Text ein, z. B. _A new Mobile Services task_, und klicken Sie dann auf das Pluszeichen (**+**).

  	![][24]

3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

  	![][25]

4. Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Push angezeigt wird.

  	![][26]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen aufgezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.

<!--+ [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]
	<br/>Erfahren Sie, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer gesendet werden.

+ [Senden von Übertragungsbenachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie sich Benutzer registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

+ [Senden von vorlagenbasierten Benachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.
-->
Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Erfahren Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie mehr über die Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen über Mobile Services.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anweisungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

<!-- Anchors.  -->
[Erstellen der Zertifikatsignieranforderungsdatei]: #certificates
[Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
[Erstellen eines Bereitstellungsprofils für die App]: #profile
[Konfigurieren von Mobile Services]: #configure
[Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
[Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
[Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
[Testen der App mit dem veröffentlichten mobilen Dienst]: #test-app
[Nächste Schritte]:#next-steps
[Lokales Herunterladen des Diensts]: #download-the-service-locally
[Testen des mobilen Dienstes]: #test-the-service
[Veröffentlichen des mobilen Dienstes in Azure]: #publish-mobile-service

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
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
[Senden von Übertragungsbenachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-ios-send-breaking-news/
[Senden von vorlagenbasierten Benachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-ios-send-localized-breaking-news/


<!--HONumber=42-->
