<properties
	pageTitle="Erste Schritte mit Notification Hubs für Xamarin.iOS-Apps | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.iOS-Anwendung senden."
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/17/2015"
	ms.author="yuaxu"/>

# Erste Schritte mit Notification Hubs

[AZURE.INCLUDE [Notification-Hubs-Auswahl-Erste-Schritte](../../includes/notification-hubs-selector-get-started.md)]

##Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine iOS-App senden. Sie erstellen eine leere Xamarin.iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (Apple Push Notification Service, APNs) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der komplette Code ist verfügbar in der [NotificationHubs-Beispielapp][GitHub].

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs.

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [XCode 6.0][Install Xcode]
+ Ein Gerät, das auf iOS 7.0 (oder einer neueren Version) laufen kann
+ iOS-Entwicklerprogramm-Mitgliedschaft
+ [Xamarin.iOS]
+ [Azure Mobile Services-Komponente]

   >[AZURE.NOTE]Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Simulator bereitgestellt und getestet werden.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hubs-Lernprogramme für Xamarin.iOS-Apps.

> [AZURE.IMPORTANT]Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

[AZURE.INCLUDE [Notification Hubs – Aktivieren von Apple-Pushbenachrichtigungen](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##Konfigurieren Ihres Notification Hub

Dieser Abschnitt führt Sie durch das Erstellen eines neuen Notification Hubs und Konfigurieren der Authentifizierung mit APNs mithilfe des **.p12**-Pushzertifikats, das Sie erstellt haben. Wenn Sie einen bereits erstellten Notification Hub verwenden möchten, können Sie zu Schritt 5 springen.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Klicken Sie oben auf die Registerkarte <b>Konfigurieren</b> und dann auf die Schaltfläche <b>Hochladen</b> in den Benachrichtigungseinstellungen von Apple, um den Zertifikatfingerabdruck hochzuladen. Wählen Sie anschließend das zuvor exportierte <b>.p12</b>-Zertifikat und das Kennwort für das Zertifikat aus.</p>
<p>Wählen Sie unbedingt den Modus <b>Sandkasten</b> aus, da Sie in einer Entwicklungsumgebung arbeiten. Wählen Sie den Modus <b>Produktion</b> nur dann, wenn Sie Pushbenachrichtigungen an Benutzer senden möchten, die Ihre App im Store erworben haben.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-upload-cert.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png)


Der Notification Hub ist nun konfiguriert, um mit APNS arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.





##Verbinden Ihrer App mit dem Notification Hub

#### Erstellen eines neuen Projekts

1. Erstellen Sie in Xamarin Studio ein neues iOS-Projekt, und wählen Sie die Vorlage **Unified API** > **Single View Application** aus.

   	![][31]

2. Fügen Sie einen Verweis auf die Azure Messaging-Komponente hinzu. Klicken Sie in der Lösungsansicht mit der rechten Maustaste auf den Ordner **Components** für Ihr Projekt und wählen Sie **Get More Components** aus. Suchen Sie nach der Komponente **Azure Messaging**, und fügen Sie sie Ihrem Projekt hinzu.

3. Fügen Sie die folgende Anweisung zur Datei **AppDelegate.cs** hinzu:

    	using WindowsAzure.Messaging;

4. Erstellen Sie eine Instanz von **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }

5. Erstellen Sie eine Klasse **Constants.cs** mit den folgenden Variablen:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. Ändern Sie in **AppDelegate.cs** **FinishedLaunching()** auf folgende Weise:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    			var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

			    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
			    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
			} else {
			    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
			    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
			}

            return true;
        }

7. Überschreiben Sie die Methode **RegisteredForRemoteNotifications()** in **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Überschreiben Sie die Methode **ReceivedRemoteNotification()** in **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Erstellen Sie die folgende Methode **ProcessNotification()** in **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE]Sie können **FailedToRegisterForRemoteNotifications()** überschreiben, um Situationen wie z. B. eine fehlende Netzwerkverbindung zu behandeln.


10. Führen Sie die App auf Ihrem Gerät aus.


## Senden von Benachrichtigungen


Sie können den Empfang von Benachrichtigungen in Ihrer App testen, indem Sie wie im Bildschirm unten abgebildet Benachrichtigungen im Azure-Portal über die Registerkarte zum Debuggen im Notification Hub senden.

![](./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png)

Pushbenachrichtigungen werden normalerweise in einem Back-End-Dienst wie z. B. Mobile Services oder ASP.NET mit einer kompatiblen Bibliothek gesendet. Sie können Benachrichtigungsmeldungen zudem direkt über die REST-API senden, wenn für Ihr Back-End keine Bibliothek verfügbar ist.

In diesem Lernprogramm gehen wir einfach vor und veranschaulichen nur das Testen der Client-App, indem wir Benachrichtigungen mit dem .NET SDK für Notification Hubs in einer Konsolenanwendung senden, anstatt über einen Back-End-Dienst. Es wird empfohlen, das Tutorial [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer](notification-hubs-aspnet-backend-ios-notify-users.md) als nächsten Schritt zum Senden von Benachrichtigungen von einem ASP.NET-Back-End zu nutzen. Sie können aber die folgenden Vorgehensweisen zum Senden von Benachrichtigungen verwenden:

* **REST-Schnittstelle**: Sie können die Benachrichtigung auf allen Back-End-Plattformen unterstützen, indem Sie die [REST-Schnittstelle](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) verwenden.

* **Microsoft Azure Notification Hubs .NET SDK**: Führen Sie im Nuget-Paket-Manager für Visual Studio die Option [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) aus.

* **Node.js** : [Verwenden von Notification Hubs mit Node.js](notification-hubs-nodejs-how-to-use-notification-hubs.md).

* **Azure Mobile Services**: Ein Beispiel zum Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End aus finden Sie unter „Erste Schritte mit Pushbenachrichtigungen in Mobile Services“ ([.NET-Back-End](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [JavaScript-Back-End](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md)).

* **Java/PHP**: Ein Beispiel zum Senden von Benachrichtigungen über die REST-APIs finden Sie unter „Verwenden von Notification Hubs von Java/PHP“ ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).


####(Optional) Senden von Benachrichtigungen aus einer .NET-Konsolen-App

In diesem Abschnitt senden Sie Benachrichtigungen mithilfe einer .NET-Konsolen-App.

1. Erstellen einer neuen Visual C#-Konsolenanwendung:

   	![][213]

2. Klicken Sie in Visual Studio auf **Extras**, auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

	Daraufhin wird in Visual Studio die Paket-Manager-Konsole angezeigt.

3. Legen Sie im Fenster der Paket-Manager-Konsole als **Standardprojekt** das neue Konsolenanwendungsprojekt fest, und führen Sie dann im Konsolenfenster den folgenden Befehl aus:

        Install-Package Microsoft.Azure.NotificationHubs

	Dies fügt mithilfe des <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.NotificationHubs-NuGet-Pakets</a> einen Verweis auf das Azure Notification Hubs-SDK hinzu.

	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Öffnen Sie die Datei „Program.cs“, und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.Azure.NotificationHubs;

3. Fügen Sie in der `Program`-Klasse die folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Fügen Sie folgende Zeilen zur `Main`-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

5. Drücken Sie die Taste F5, um die App auszuführen. Sie sollten eine Warnung auf Ihrem Gerät erhalten. Überprüfen Sie die Verbindung, wenn Sie WLAN verwenden.

Sie finden alle möglichen Nutzlasten im [Apple Local and Push Notification Programming Guide] (in englischer Sprache).


####(Optional) Senden von Benachrichtigungen aus einem mobilen Service

In diesem Abschnitt schicken wir Benachrichtigungen mithilfe eines mobilen Service über einen Knotenskript.

Zum Senden einer Benachrichtigung mithilfe eines mobilen Service folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services]. Gehen Sie dann wie folgt vor:

1. Melden Sie sich am [Azure-Portal] an, und wählen Sie Ihren mobilen Service aus.

2. Wählen Sie oben die Registerkarte **Scheduler** aus.

   	![][215]

3. Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.

   	![][216]

4. Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend in der Kopfleiste auf die Registerkarte **Skript**.

5. Fügen Sie das folgende Skript in Ihre Scheduler-Funktion ein. Stellen Sie sicher, dass Sie die Platzhalter durch den Notification Hub-Namen und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* ersetzen, die Sie zuvor erhalten haben. Klicken Sie auf **Speichern**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6. Klicken Sie in der unteren Leiste auf **Run Once**. Sie sollten eine Warnung auf Ihrem Gerät erhalten.

##Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Ihre iOS-Geräte übertragen. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Benachrichtigungshubs]. Wenn Sie Ihre Benutzer in Interessengruppen einteilen möchten, finden Sie unter [Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten] weitere Informationen. Weitere Informationen über die Verwendung von Notification Hubs finden Sie unter [Notification Hubs-Leitfaden] und [Notification Hubs-Informationen für iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Erste Schritte mit Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure-Portal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs-Informationen für iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Benachrichtigen von Benutzern mit Benachrichtigungshubs]: /manage/services/notification-hubs/notify-users-aspnet
[Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten]: /manage/services/notification-hubs/breaking-news-dotnet

[Apple Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!---HONumber=Nov15_HO4-->