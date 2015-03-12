<properties 
	pageTitle="Erste Schritte mit Notification Hubs für Xamarin iOS-Apps" 
	description="Erfahren Sie, wie Sie Notification Hubs verwenden, um Pushbenachrichtigungen an eine Xamarin iOS-Anwendung zu senden." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine iOS-App senden. 
In diesem Lernprogramm erstellen Sie eine leere Xamarin.iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNs) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der fertige Code ist im Beispiel zu [NotificationHubs-App][GitHub] verfügbar.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Generieren der Zertifikatsignieranforderung] 
2. [Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3. [Erstellen eines Bereitstellungsprofils für die App]
4. [Konfigurieren des Notification Hubs]
5. [Verbinden der App mit dem Notification Hub]
6. [Senden von Benachrichtigungen vom Back-End aus]

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Für dieses Lernprogramm ist Folgendes erforderlich:

+ [XCode 5.0][Installieren von Xcode]
+ Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
+ iOS-Entwicklerprogramm-Mitgliedschaft
+ [Xamarin.iOS]
+ [Azure Mobile Services-Komponente]

   > [AZURE.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Simulator bereitgestellt und getestet werden.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Xamarin.iOS-Apps. 

> [AZURE.IMPORTANT] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F"%20target="_blank).

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihres mobilen Diensts. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Merkmale finden Sie unter [Apple Push Notification Service].



<h2><a name="certificates"></a>Generieren der Zertifikatsignieranforderungsdatei</h2>

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1. Führen Sie das Programm "Schlüsselbundverwaltung" im Ordner "Dienstprogramme" aus.

2. Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat von einer Zertifizierungsinstanz anfordern ...**.

  	![][5]

3. Wählen Sie die **E-Mail-Adresse des Benutzers**, geben Sie Werte für **Allgemeiner Name** und **E-Mail der Zert.-Instanz** ein, stellen sicher, dass **Auf der Festplatte sichern** ausgewählt ist, und klicken Sie dann auf **Weiter**.

  	![][6]

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

  	![][7]
  
  	Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei Schreibtisch der Standardort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR-Datei hochladen, um ein Pushzertifikat zu erstellen.

<h2><a name="register"></a>Registrieren der App für Pushbenachrichtigungen</h2>

Um von Mobile Services Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.  

1. Falls Sie Ihre App noch nicht registriert haben, gehen Sie im Apple Developer Center zum <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Zeichen **+**, um eine neue App zu registrieren.

   	![][105] 

2. Geben Sie in **Description** einen Namen für Ihre App sowie in **Bundle Identifier** einen Wert ein, aktivieren Sie im Abschnitt "App Services "die Option "Push Notifications", und klicken Sie dann auf **Continue**.

   	![][106]

   	![][107] 

   	![][108]
   

	Hierdurch wird Ihre App-ID generiert, und Sie werden zum Übermitteln der Daten aufgefordert. Klicken Sie auf **Submit**.
   
   	![][109] 
   
	Nach dem Klicken auf **Submit** wird der Bildschirm **Registration complete** geöffnet, wie nachstehend gezeigt. Klicken Sie auf **Done**.
   
   	![][110]

	> [AZURE.NOTE] Wenn Sie sich dafür entscheiden, einen anderen **Bundle Identifier**-Wert als **MobileServices.Quickstart** anzugeben, müssen Sie auch den Bundle-ID-Wert in Ihrem Xcode-Projekt aktualisieren.

3. Lokalisieren Sie die soeben erstellte App-ID und klicken Sie auf deren Zeile. 

   	![][111]
   
	Durch Anklicken der App-ID werden Einzelheiten zur App und die App-ID angezeigt:
   
   	![][112] 
   
   	![][113]

4. Klicken Sie auf **Edit**, führen Sie einen Bildlauf zum unteren Bildschirmrand aus, und klicken Sie unter dem Abschnitt **Development Push SSL Certificate** auf **Create Certificate...**.

   	![][114] 

	Hierdurch wird der Assistent "Add iOS Certificate" angezeigt.
   
   	![][115] 

	> [AZURE.NOTE] In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.

5. Klicken Sie auf **Choose File**, navigieren Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei, und klicken Sie dann auf **Generate**. 

  	![][116]
  
6. Nach der Erstellung des Zertifikats durch das Portal klicken Sie auf **Download** und dann auf **Done**.
 
  	![][118]

  	![][119]  
  
   	Hierdurch wird das Signaturzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Ordner **Downloads** gespeichert. 

  	![][9] 

    > [AZURE.NOTE] Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens **aps_development.cer**.

7. Doppelklicken Sie auf das heruntergeladene Pushzertifikat **aps_development.cer**.

	Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

   	![][10]

	> [AZURE.NOTE]
	> Der Name in Ihrem Zertifikat kann abweichen, weist jedoch das Präfix <strong>Apple Development iOS Push Notification Services:</strong> auf.

	Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und in Ihr Notification Hub hochladen, um Pushbenachrichtigungen mit APNS zu ermöglichen.

<h2><a name="profile"></a>Erstellen eines Bereitstellungsprofils für die App</h2>
 
1. Zurück im <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> wählen Sie **Provisioning Profiles**, wählen **All** und klicken anschließend auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisioning Profile** angezeigt.

   	![][120]

2. Wählen Sie unter **Development** die Option **iOS App Development** als Bereitstellungsprofiltyp, und klicken Sie dann auf **Continue**.

   	![][121]

3. Wählen Sie anschließend die App-ID für die Mobile Services Quickstart-App aus der Dropdownliste **App ID** aus, und klicken Sie dann auf **Continue**.

   	![][122]

4. Wählen Sie im Bildschirm **Select certificates** das zuvor erstellte Zertifikat aus, und klicken Sie auf **Continue**.
  
   	![][123]

5. Anschließend wählen Sie die zum Testen zu verwendenden **Devices**, und klicken Sie auf **Continue**.

   	![][124]

6. Wählen Sie schließlich unter **Profile Name** einen Namen für das Profil aus, klicken Sie auf **Generate** und auf **Done**.

   	![][125]
   
   	![][126]
	
  	Dadurch wird ein neues Bereitstellungsprofil erstellt.

7. Öffnen Sie in Xcode den Organizer, wählen Sie die Geräteansicht, dann im Abschnitt **Library** im linken Bereich **Provisioning Profiles**, und importieren Sie das gerade erstellte Bereitstellungsprofil.

8. Wählen Sie links das Gerät aus, und importieren Sie erneut das Bereitstellungsprofil. 

9. Klicken Sie in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neue Zertifikat, klicken Sie auf **Export**, geben Sie einen Namen für das Zertifikat ein, wählen Sie das **.p12**-Format, und klicken Sie dann auf **Save**.

   	![][18]

  	Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

Hierdurch wird sichergestellt, dass das Xcode-Projekt das neue Profil für Codesignierung verwendet. Als Nächstes laden Sie das Zertifikat in Ihr Notification Hub hoch.

<h2><a name="configure-hub"></a>Konfigurieren des Notification Hubs</h2>

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie dann am unteren Bildschirmrand auf **+NEU**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und dann auf **Schnellerfassung**.

   	![][27]

3. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Neuen Notification Hub erstellen**.

   	![][28]

4. Klicken Sie auf den gerade erstellten Namespace (normalerweise ***Notification Hub-Name*-ns**), und klicken Sie dann oben auf die Registerkarte **Konfigurieren**.

   	![][29]

5. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

   	![][210]

6. Wählen Sie oben die Registerkarte **Konfigurieren** aus, und klicken Sie dann für die Apple-Benachrichtigungseinstellungen auf **Hochladen**. Wählen Sie anschließend das zuvor exportierte **.p12**-Zertifikat und das Kennwort für das Zertifikat aus. Wählen Sie aus, ob Sie den Pushdienst **Produktion** (wenn Sie Pushbenachrichtigungen an Benutzer versenden möchten, die Ihre App aus dem Store erworben haben) oder **Sandkasten** (während der Entwicklung) verwenden möchten.

   	![][211]

7. Klicken Sie oben auf die Registerkarte **Dashboard**, und klicken Sie dann auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][212]

Der Notification Hub ist nun konfiguriert, um mit APNs arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

<h2><a name="connecting-app"></a>Verbinden der App mit dem Notification Hub</h2>

### Herunterladen der WindowsAzure.Messaging-Bibliothek

Diese Assembly vereinfacht die Registrierung bei Azure Notification Hubs. Sie kann anhand der unten stehenden Anweisungen heruntergeladen oder aus dem [Beispieldownload][GitHub] abgerufen werden.

1. Laden Sie die Quelle für [WindowsAzure.Messaging] von GitHub herunter.

2. Kompilieren Sie das Projekt, und suchen Sie die Ausgabeassembly **WindowsAzure.Messaging.dll**. Sie benötigen diese, wenn Sie die Xamarin.iOS-Anwendung unten einrichten.


### Erstellen eines neuen Projekts

1. Erstellen Sie in Xamarin Studio ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

   	![][31]

2. Fügen Sie zuerst einen Verweis auf die Azure Mobile Services-Komponente hinzu. Klicken Sie in der Projektmappenansicht mit der rechten Maustaste auf den Ordner **Components** für Ihr Projekt, und wählen Sie **Get More Components** aus. Suchen Sie nach der Komponente **Azure Mobile Services**, und fügen Sie die Komponente Ihrem Projekt hinzu.

3. Fügen Sie nun einen Verweis auf die zuvor heruntergeladene WindowsAzure.Messaging-Bibliothek hinzu. Klicken Sie mit der rechten Maustaste auf den Ordner **References**, und wählen Sie **Edit References...** aus. Suchen Sie auf der Registerkarte **.Net Assembly** nach **WindowsAzure.Messaging.dll**. 

4. Fügen Sie in **AppDelegate.cs** folgende using-Anweisungen hinzu:

		using Microsoft.WindowsAzure.MobileServices;
    using WindowsAzure.Messaging;

5. Deklarieren Sie eine Instanz von **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }
		
6. Erstellen Sie eine **Constants.cs**-Klasse mit den folgenden Variablen:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
	
	
7. Aktualisieren Sie in **AppDelegate.cs** den **FinishedLaunching()**-Code auf folgende Weise:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8. Überschreiben Sie die **RegisteredForRemoteNotifications()**-Methode in **AppDelegate.cs**:

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

9. Überschreiben Sie die **ReceivedRemoteNotification()**-Methode in **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
        
10. Create the following **ProcessNotification()** method in **AppDelegate.cs**:

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
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc., 
                // your "alert" object from the aps dictionary will be another NSDictionary. 
                // Basically the json gets dumped right into a NSDictionary, 
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

    > [AZURE.NOTE] Sie können **FailedToRegisterForRemoteNotifications()** überschreiben, um Situationen wie z. B. eine fehlende Netzwerkverbindung zu behandeln.

	
11. Führen Sie die App auf Ihrem Gerät aus.

<h2><a name="send"></a>Senden von Benachrichtigungen vom Back-End aus</h2>

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem Mobile Service, der ein Knotenskript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1. Erstellen einer neuen Visual C#-Konsolenanwendung: 

   	![][213]

2. Fügen Sie dem Azure Service Bus SDK mithilfe des <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Pakets</a> einen Verweis hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Bibliotheks-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

3. Fügen Sie in der  `Program`-Klasse die folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Fügen Sie dann in der `Main`-Methode folgende Zeile hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

5. Drücken Sie die Taste F5, um die App auszuführen. Sie sollten eine Warnung auf Ihrem Gerät erhalten. Überprüfen Sie die Verbindung, wenn Sie WLAN verwenden.

Sie finden alle möglichen Nutzlasten im [Local and Push Notification Programming Guide] von Apple.

Zum Senden einer Benachrichtigung mit einem mobilen Dienst folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services]. Gehen Sie dann wie folgt vor:

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und wählen Sie Ihren mobilen Dienst aus.

2. Wählen Sie oben die Registerkarte **Scheduler**.

   	![][215]

3. Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und wählen Sie **Bedarfsgesteuert**.

   	![][216]

4. Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend auf der oberen Leiste auf die Registerkarte **Skript**.

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


6. Klicken Sie auf der unteren Leiste auf **Einmal ausführen**. Sie sollten eine Warnung auf Ihrem Gerät erhalten.

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Ihre iOS-Geräte übertragen. Informationen zum Adressieren bestimmter Benutzer finden Sie im Lernprogramm [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] lesen. Mehr zur Verwendung von Notification Hubs erfahren Sie im [Notification Hubs-Leitfaden] und unter [Notification Hubs - Anleitungen für iOS].

<!-- Anchors. -->
[Generieren der Zertifikatsignieranforderung]: #certificates
[Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
[Erstellen eines Bereitstellungsprofils für die App]: #profile
[Konfigurieren des Notification Hubs]: #configure-hub
[Verbinden der App mit dem Notification Hub]: #connecting-app
[Senden von Benachrichtigungen vom Back-End aus]: #send
[Nächste Schritte]:#next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-ios
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs - Anleitungen für iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Installieren von Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS-Bereitstellungsportal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!--HONumber=45--> 
