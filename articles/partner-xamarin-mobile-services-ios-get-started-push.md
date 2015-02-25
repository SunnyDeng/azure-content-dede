<properties 
	pageTitle="Erste Schritte mit Pushbenachrichtigungen (Xamarin.iOS) - Mobile Services" 
	description="Erfahren Sie mehr über die Verwendung von Pushbenachrichtigungen in Xamarin.iOS-Apps mit Azure Mobile Services." 
	documentationCenter="xamarin" 
	authors="ysxu" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="yuaxu"/>

# Hinzufügen von Pushbenachrichtigungen zu Ihrer Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push-xamarin](../includes/mobile-services-selector-get-started-push-xamarin.md)]

<p>In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Xamarin.iOS 8-App senden. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Services (APNS) Pushbenachrichtigungen zum Projekt [Erste Schritte mit Mobile Services] hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.</p>

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Generieren der Zertifikatsignieranforderung]
2. [Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3. [Erstellen eines Bereitstellungsprofils für die App]
4. [Konfigurieren von Mobile Services]
5. [Konfigurieren der Xamarin.iOS-App]
6. [Hinzufügen von Pushbenachrichtigungen zur App]
7. [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
8. [Einfügen von Daten zum Empfangen von Benachrichtigungen]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Ein iOS 8-Gerät
+ iOS-Entwicklerprogramm-Mitgliedschaft
+ [Xamarin.iOS Studio]
+ [Azure Mobile Services-Komponente]

   > [AZURE.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihres mobilen Diensts. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Merkmale finden Sie unter [Apple Push Notification Service].

## <a name="certificates"></a>Generieren der Zertifikatsignieranforderungsdatei

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1. Führen Sie unter den Hilfsprogrammen die **Schlüsselbundverwaltung** aus.

2. Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern...**.

    ![][5]

3. Machen Sie Angaben in den Feldern **E-Mail des Benutzers** und **Allgemeiner Name**. Vergewissern Sie sich, dass **Auf der Festplatte sichern** ausgewählt ist, und klicken Sie dann auf **Fortfahren**.

    ![][6]

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

    ![][7]
  
    Merken Sie sich den ausgewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR hochladen, um ein Pushzertifikat zu erstellen.

## <a name="register"></a>Registrieren der App für Pushbenachrichtigungen

Um von Mobile Services Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und für Pushbenachrichtigungen angemeldet werden. 

1. Falls Sie Ihre App noch nicht registriert haben, gehen Sie im Apple Developer Center zum <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Zeichen **+**, um eine App-ID für Ihre App zu erstellen.
    
    ![][102]

2. Geben Sie in **Description** einen Namen für Ihre App ein, tragen Sie den eindeutigen **Bundle Identifier** ein, und merken Sie ihn sich, markieren Sie die Option "Push Notifications" im Abschnitt "App Services", und klicken Sie dann auf **Continue**. In diesem Beispiel wird die ID **MobileServices.Quickstart** verwendet. Dieselbe ID kann jedoch nicht erneut benutzt werden, da App-IDs über alle Benutzer hinweg eindeutig sein müssen. Daher empfiehlt es sich, dass Sie Ihren vollen Namen oder Ihre Initialen an den App-Namen anhängen. 

    ![][103]
   
    Hierdurch wird Ihre App-ID generiert, und Sie werden aufgefordert, die Daten per **Submit** zu übermitteln. Klicken Sie auf **Submit**.
   
    ![][104] 
   
    Nach dem Klicken auf **Submit** wird der Bildschirm **Registration complete** geöffnet, wie nachstehend gezeigt. Klicken Sie auf **Done**.
   
    ![][105]    

3. Lokalisieren Sie die soeben erstellte App-ID und klicken Sie auf deren Zeile. 

    ![][106]
   
    Durch Klicken auf die App-ID werden Einzelheiten zur App und die App-ID angezeigt. Klicken Sie auf die Schaltfläche **Settings**.
   
    ![][107] 
   
4. Führen Sie einen Bildlauf bis zum unteren Bildschirmbereich durch, und klicken Sie auf die Schaltfläche **Create Certificate...** unterhalb des Abschnitts **Development Push SSL Certificate**.

    ![][108] 

    Hierdurch wird der Assistent "Add iOS Certificate" zur Anzeige gebracht.
   
    Hinweis: In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.

5. Klicken Sie auf **Choose File**, wechseln Sie zum Speicherort der CSR-Datei, und klicken Sie dann auf **Generate**. 

    ![][110]
  
6. Nach Erstellung des Zertifikats durch das Portal klicken Sie auf die Schaltfläche **Download** und anschließend auf **Done**.
 
    ![][111]  

    Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert. 

    ![][9] 

    Hinweis: Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens <strong>aps_development.cer</strong>.

7. Doppelklicken Sie auf das heruntergeladene Pushzertifikat **aps_development.cer**.

    Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

    ![][10]

    Hinweis: Der Name in Ihrem Zertifikat kann verschieden sein, aber er wird das Präfix <strong>Apple Development iOS Push Notification Services:</strong> aufweisen.

Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und zu Mobile Services hochladen, um Authentifizierung mit APNS zu ermöglichen.

## <a name="profile"></a>Erstellen eines Bereitstellungsprofils für die App
 
1. Zurück im <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> wählen Sie **Provisioning Profiles**, aktivieren die Option **All** und klicken anschließend auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisiong Profile** gestartet.

    ![][112]

2. Wählen Sie unter **Development** den Punkt **iOS App Development** als Bereitstellungsprofiltyp, und klicken Sie dann auf **Continue**.

3. Wählen Sie anschließend die App-ID für die Mobile Services Quickstart-App aus der Dropdownliste **App ID** aus, und klicken Sie dann auf **Continue**.

    ![][113]

4. Wählen Sie im Bildschirm **Select certificates** das zuvor erstellte Zertifikat aus, und klicken Sie auf **Continue**.

    ![][114]

5. Anschließend wählen Sie die zum Testen zu verwendenden **Devices**, und klicken Sie auf **Continue**.
  
    ![][115]

6. Wählen Sie abschließend in **Profile Name** einen Namen für das Profil, klicken Sie auf **Generate** und anschließend auf **Done**.

    ![][116]

    Dadurch wird ein neues Bereitstellungsprofil erstellt.

    ![][117]

## <a name="configure-mobileServices"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

Nachdem Sie Ihre App bei APNS registriert und Sie Ihr Projekt konfiguriert haben, müssen Sie als Nächstes Ihren mobilen Dienst für Integration mit APNS konfigurieren.

1. Klicken Sie in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neue Zertifikat, klicken Sie auf **Exportieren**, benennen Sie Ihre Datei, wählen Sie das **.p12**-Format, und klicken Sie dann auf **Sichern**.

    ![][28]

    Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

2. Melden Sie sich am [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

    ![][18]

3. Klicken Sie auf die Registerkarte **Push** und dann unter **Apple-Push-Benachrichtigungseinstellungen** auf **Hochladen**.

    ![][19]

    Der Dialog "Zertifikat hochladen" wird angezeigt.

4. Klicken Sie auf **Datei**, wählen Sie die exportierte .p12-Zertifikatdatei aus, und geben Sie das **Kennwort** ein. Stellen Sie dann sicher, dass der richtige **Modus** ausgewählt ist, klicken Sie auf das Häkchensymbol und anschließend auf **Speichern**.

    ![][20] 

Ihr mobiler Dienst ist nun für APNS konfiguriert.

## <a name="configure-app"></a>Konfigurieren der Xamarin.iOS-App

1. Öffnen Sie in Xamarin.Studio **Info.plist**, und aktualisieren Sie die **Bundle-IDs** mit der ID, die Sie zuvor erstellt haben.

    ![][121]

2. Führen Sie einen Bildlauf nach unten bis **Hintergrundmodi** durch, und aktivieren Sie die Felder **Hintergrundmodi aktivieren** und **Remotebenachrichtigungen**. 

    ![][122]

3. Doppelklicken Sie im Projektmappen-Bereich auf Ihr Projekt, um die **Projektoptionen** zu öffnen.

4.  Aktivieren Sie unter **Build** die Option **iOS-Bundle signieren**, und wählen Sie die **Identität** und das **Bereitstellungsprofil**, die bzw. das Sie für dieses Projekt eingerichtet haben. 

    ![][120]

    Dadurch wird sichergestellt, dass das Xamarin-Projekt das neue Profil für die Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin-Gerätebereitstellung].

## <a name="add-push"></a>Hinzufügen von Pushbenachrichtigungen zu Ihrer App

1. Öffnen Sie in Xamarin.Studio die Datei "AppDelegate.cs", und fügen Sie die folgende Eigenschaft hinzu:

        public string DeviceToken { get; set; }

2. Öffnen Sie die Klasse **TodoItem**, und fügen Sie die folgende Eigenschaft hinzu:

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. Setzen Sie in **QSTodoService** die vorhandene Clientdeklaration außer Kraft:
        
        public MobileServiceClient client { get; private set; }

4. Fügen Sie dann die folgende Methode hinzu, sodass **AppDelegate** den Client später für die Registrierung von Pushbenachrichtigungen abrufen kann:

        public MobileServiceClient GetClient {
            get{ 
                return client;
            }
        }

5. Setzen Sie in **AppDelegate** das Ereignis **FinishedLaunching** außer Kraft: 

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

6. Setzen Sie in **AppDelegate** das Ereignis **RegisteredForRemoteNotifications** außer Kraft:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. Setzen Sie in **AppDelegate** das Ereignis **ReceivedRemoteNotification** außer Kraft:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

8. Modifizieren Sie in **TodoListViewController** die Aktion **OnAdd**, um das in **AppDelegeate** gespeicherte Gerätetoken abzurufen. Speichern Sie das Token im hinzugefügten **TodoItem**.

        string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem() 
        {
            Text = itemText.Text, 
            Complete = false,
            DeviceToken = deviceToken
        };

Ihre App kann Pushbenachrichtigungen nun unterstützen.

## <a name="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**. 

    ![][21]

2. Klicken Sie in **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.
   
    ![][22]

    Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **TodoItem** eine Einfügung erfolgt.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Damit wird ein neues Einfügeskript registriert, das über das [apns-Objekt] eine Pushbenachrichtigung (den eingefügten Text) an das in der Einfügeanforderung angegebene Gerät sendet. 

   > [AZURE.NOTE] Dieses Skript verzögert die Übermittlung der Benachrichtigung, um Ihnen Zeit zum Schließen der App für den Empfang einer Toastbenachrichtigung zu geben.

## <a name="test"></a>Testen von Pushbenachrichtigungen in der App

1. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen. Starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren.

    ![][23]

   > [AZURE.NOTE] Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. Geben Sie in der App einen aussagekräftigen Text ein, z. B. _Neue Mobile Services-Aufgabe_, und klicken Sie dann auf das Pluszeichen (**+**).

    ![][24]

3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

    ![][25]

4. Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Toast angezeigt wird.

    ![][26]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

<!-- Anchors. -->
[Generieren der Zertifikatsignieranforderung]: #certificates
[Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
[Erstellen eines Bereitstellungsprofils für die App]: #profile
[Konfigurieren von Mobile Services]: #configure-mobileServices
[Konfigurieren der Xamarin.iOS-App]: #configure-app
[Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
[Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
[Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Installieren von Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS-Bereitstellungsportal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-ios
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Senden von Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Xamarin-Gerätebereitstellung]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[apns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
[Abgeschlossenes Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331303




<!--HONumber=42-->
