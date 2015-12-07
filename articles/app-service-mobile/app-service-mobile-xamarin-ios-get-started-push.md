<properties 
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Ihrer Xamarin iOS-App mit Azure App Service" 
	description="Erfahren Sie, wie Azure App Service zum Senden von Pushbenachrichtigungen an Ihre Xamarin iOS-App verwendet wird." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="11/23/2015" 
	ms.author="wesmc"/>

# Hinzufügen von Pushbenachrichtigungen zu Ihrer Xamarin iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Übersicht

Dieses Tutorial baut auf dem [Xamarin iOS-Schnellstart-Tutorial](app-service-mobile-xamarin-ios-get-started.md) auf, das Sie zuerst absolvieren müssen. Sie fügen dem „Xamarin iOS-Schnellstartprojekt“ Pushbenachrichtigungen hinzu, damit jedes Mal, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung gesendet wird. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Pushbenachrichtigungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

Die [iOS-Simulator unterstützt keine Pushbenachrichtigungen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), daher müssen Sie ein physisches iSO-Gerät verwenden. Sie müssen sich außerdem für eine [Apple-Entwicklerprogramm-Mitgliedschaft](https://developer.apple.com/programs/ios/) anmelden.

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps. Diese können Sie auch nach Ablauf der Testversion weiter nutzen. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

* Einen Mac, auf dem [Xamarin Studio] und [Xcode] ab Version 4.4 installiert sind. Sie können die Xamarin iOS-App auf Wunsch mithilfe von Visual Studio auf Ihrem Windows-Computer ausführen. Allerdings ist dies etwas kompliziert, da Sie eine Verbindung zu einem vernetzten Mac benötigen, auf dem der Xamarin.iOS Build Host ausgeführt wird. Wenn Sie daran interessiert sind, finden Sie Informationen dazu unter [Installation von Xamarin.iOS auf Windows].

* Ein physisches iOS-Gerät. Pushbenachrichtigungen werden vom iOS-Simulator nicht unterstützt.

* Durchlaufen Sie das [Xamarin.iOS-Schnellstart-Tutorial](app-service-mobile-xamarin-ios-get-started.md).


##Registrieren der App für Pushbenachrichtigungen im Apple-Entwicklerportal

[AZURE.INCLUDE [Notification Hubs Xamarin – Aktivieren von Apple-Pushbenachrichtigungen](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##Konfigurieren Ihrer mobilen App für das Senden von Pushbenachrichtigungen

Um Ihre App für das Senden von Benachrichtigungen zu konfigurieren, erstellen Sie einen neuen Hub, und konfigurieren Sie ihn für die Plattformbenachrichtigungsdienste, die Sie verwenden.

1. Klicken Sie im Azure-Portal auf **Durchsuchen** > **Mobile Apps** > Ihre mobile App > **Einstellungen** > **Mobile App** > **Push** > **Notification Hub** > **+ Notification Hub**, geben Sie einen Namen und einen Namespace für einen neuen Notification Hub ein, und klicken Sie dann auf die Schaltfläche **OK**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. Klicken Sie auf dem Blatt **Notification Hub** erstellen auf „Erstellen“.

3. Klicken Sie auf **Push** > **Apple (APNS)** > **Zertifikat hochladen**. Laden Sie die P12-Pushzertifikatsdatei hoch, die Sie zuvor exportiert haben. Stellen Sie sicher, dass **Sandkasten** auswählen, wenn Sie eine Pushzertifikat für Entwicklung und Tests erstellt haben. Wählen Sie andernfalls **Produktion**. Ihr Dienst ist jetzt so konfiguriert, dass er mit Pushbenachrichtigungen unter iOS arbeitet.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

##Aktualisieren des Serverprojekts für das Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Bereitstellen des Serverprojekts in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##Konfigurieren des Xamarin.Forms-Projekts

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##Hinzufügen von Pushbenachrichtigungen zur App

1. Fügen Sie in **QSTodoService** die folgende Eigenschaft hinzu, damit **AppDelegate** den mobilen Client abrufen kann:
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Fügen Sie am Anfang der Datei **AppDelegate.cs** die folgende `using`-Anweisung hinzu.

        using Microsoft.WindowsAzure.MobileServices;

2. Überschreiben Sie in **AppDelegate** das Ereignis **FinishedLaunching**:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. Setzen Sie in derselben Datei das Ereignis **RegisteredForRemoteNotifications** außer Kraft:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. Setzen Sie anschließend das Ereignis **DidReceivedRemoteNotification** außer Kraft:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Ihre App kann Pushbenachrichtigungen nun unterstützen.

## <a name="test"></a>Testen von Pushbenachrichtigungen in der App

1. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen. Starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren.
	
	> [AZURE.NOTE]Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen (**+**).

3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

4. Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob eine Benachrichtigung angezeigt wird.

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installation von Xamarin.iOS auf Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1125_2015--->