<properties 
	pageTitle="Erste Schritte mit Azure Notification Hubs" 
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# Erste Schritte mit Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine iOS-App senden.
In diesem Lernprogramm erstellen Sie eine leere iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNs) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. 

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK]
+ [XCode 4.5][Installieren von Xcode]
+ Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
+ iOS-Entwicklerprogramm-Mitgliedschaft

   > [AZURE.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für iOS-Apps.

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [1 Monat kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

##Konfigurieren Ihres Notification Hub

1. Klicken Sie in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neue Zertifikat der Schnellstart-App unter **Meine Zertifikate**. Klicken Sie auf **Exportieren**, benennen Sie die Datei, wählen Sie das Format **.p12** aus, und klicken Sie dann auf **Speichern**.

    ![][26]

  Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

>[AZURE.NOTE] In diesem Lernprogramm wird eine QuickStart.p12-Datei erstellt. Name und Ort Ihrer Datei können verschieden sein.

2. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie dann im unteren Bereich des Bildschirms auf **+NEU**.

3. Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und dann auf **Schnellerfassung**.

   	![][27]

4. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Neuen Notification Hub erstellen**.

   	![][28]

5. Klicken Sie auf den soeben erstellten Namespace (für gewöhnlich ***Name des Notification Hub*-ns**), und anschließend oben auf die Registerkarte **Konfigurieren**.

   	![][29]

6. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

   	![][210]

7. Wählen Sie oben die Registerkarte **Configure** aus, und klicken Sie dann für die Apple-Benachrichtigungseinstellungen auf **Upload**. Wählen Sie anschließend das zuvor exportierte **.p12**-Zertifikat und das Kennwort für das Zertifikat aus. Wählen Sie aus, ob Sie den Pushdienst **Production** (wenn Sie Pushbenachrichtigungen an Benutzer versenden möchten, die Ihre App aus dem Store erworben haben) oder **Sandbox** (während der Entwicklung) verwenden möchten.

   	![][211]

8. Klicken Sie oben auf die Registerkarte **Dashboard**, und klicken Sie danach auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][212]

Der Notification Hub ist nun konfiguriert, um mit APNs arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

##Verbinden Ihrer App mit dem Notification Hub

1. Erstellen Sie in XCode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

   	![][31]

2. Klicken Sie unter **Targets** auf den Projektnamen, erweitern Sie **Code Signing Identity**, und wählen Sie unter **Debug** das Identitätsprofil für die Codesignierung aus. Wenn Sie eine neuere Version von XCode verwenden, können Sie zusätzlich für **Levels** von **Basic** zu **All** wechseln und das Zeilenelement **Provisioning Profile** zum Bereitstellungsprofil ändern.

   	![][32]

3. Laden Sie den Azure Mobile SDK für iOS herunter. Öffnen Sie die .zip-Datei, und ziehen Sie den Ordner "WindowsAzureMessaging.framework" in den Framework-Ordner Ihres XCode-Projekts. Wählen Sie **Copy items in destination group's folder** aus, und klicken Sie dann auf **OK**.

   	![][33]

4. Fügen Sie in der Datei "AppDelegate.h" die folgende Importdirektive hinzu:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. Fügen Sie in der Datei "AppDelegate.m" der Methode `didFinishLaunchingWithOptions` folgenden Code hinzu:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	Für iOS 8
   
	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                            UIUserNotificationTypeAlert |
                                            UIUserNotificationTypeBadge
					    					categories:nil];
 
    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

6. Fügen Sie in derselben Datei die folgende Methode hinzu:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *(optional)* Fügen Sie in derselben Datei folgende Methode hinzu, um einen **UIAlert** anzuzeigen, wenn die Benachrichtigung erhalten wird, während die App aktiv ist:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

8. Führen Sie die App auf Ihrem Gerät aus.

##Senden von Benachrichtigungen vom Back-End aus

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die [REST-Schnittstelle](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung. Ein Beispiel für das Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End finden Sie unter **Erste Schritte mit Pushbenachrichtigungen in Mobile Services** ([.NET-Back-End](mobile-services-javascript-backend-ios-get-started-push.md) | [JavaScript-Back-End](mobile-services-javascript-backend-ios-get-started-push.md)).  Ein Beispiel für das Senden von Benachrichtigungen über die REST-APIs finden Sie unter **Verwenden von Notification Hubs von Java/PHP aus** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Wählen Sie in Visual Studio aus dem Menü **Datei** die Option **Neu** und dann **Projekt...** aus, klicken Sie unter **Visual C#** auf **Windows** und **Konsolenanwendung**, und klicken Sie dann auf **OK**.  

   	![][20]

	Damit wird ein neues Konsolenanwendungsprojekt erstellt.

2. Klicken Sie im Menü **Extras** auf **Bibliothekspaket-Manager** und danach auf **Paket-Manager-Konsole**.

	Daraufhin wird die Paket-Manager-Konsole angezeigt.

6. Führen Sie im Konsolenfenster folgenden Befehl aus:

        Install-Package WindowsAzure.ServiceBus

	Damit wird ein Verweis auf das Azure Service Bus-SDK mit dem <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Paket</a> hinzugefügt.

5. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

6. Fügen Sie in der Klasse **Program** folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

	Stellen Sie sicher, dass Sie den Platzhalter "hub name" durch den Namen des Notification Hub ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt "Konfigurieren Ihres Notification Hub" erhalten haben.

	>[AZURE.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht die mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

5. Fügen Sie danach der **Main**-Methode die folgenden Zeilen hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

5. Drücken Sie die Taste F5, um die Konsolenanwendung auszuführen.

	Sie sollten eine Warnung auf Ihrem Gerät erhalten. Überprüfen Sie die Verbindung, wenn Sie WLAN verwenden.

Sie finden alle möglichen Nutzlasten im [Local and Push Notification Programming Guide] von Apple.

##Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Ihre iOS-Geräte übertragen. Informationen zum Adressieren bestimmter Benutzer finden Sie im Lernprogramm [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] lesen. Weitere Informationen zur Verwendung von Notification Hubs finden Sie in der [Notification Hubs-Anleitung][Notification Hubs-Leitfaden].



<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[26]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Erste Schritte mit Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Installieren von Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS-Bereitstellungsportal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: notification-hubs-ios-mobile-services-register-user-push-notifications.md
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: notification-hubs-ios-send-breaking-news.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1


<!--HONumber=49-->