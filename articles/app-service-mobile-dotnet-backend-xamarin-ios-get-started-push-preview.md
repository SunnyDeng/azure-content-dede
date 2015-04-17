<properties 
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Ihrer Xamarin iOS-App mit Azure App Service" 
	description="Erfahren Sie, wie Azure App Service zum Senden von Pushbenachrichtigungen an Ihre Xamarin iOS-App verwendet wird." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="yuaxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="02/22/2015" 
	ms.author="yuaxu"/>

# Hinzufügen von Pushbenachrichtigungen zu Ihrer Xamarin iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

In diesem Thema wird erläutert, wie Sie Azure App Service zum Senden von Pushbenachrichtigungen an eine Xamarin iOS 8-App verwenden. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Service (APNS) Pushbenachrichtigungen zum Projekt [Erste Schritte mit mobilen App Service-Apps] hinzu. Anschließend sendet Ihr mobiles Back-End immer dann eine Pushbenachrichtigung, wenn ein Datensatz eingefügt wird.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Ein iOS 8-Gerät
+ iOS-Entwicklerprogramm-Mitgliedschaft
+ [Xamarin.iOS Studio]
+ [Azure Mobile Services-Komponente]

   > [AZURE.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Der Apple Push Notification Service (APNS) verwendet Zertifikate, um Ihre mobile App zu authentifizieren. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen der Zertifikate in Ihre mobile App. Die offizielle Dokumentation der APNS-Funktionen finden Sie unter [Apple Push Notification Service].

## <a name="certificates"></a>Generieren der Zertifikatsignieranforderungsdatei

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1. Führen Sie unter den Hilfsprogrammen die **Schlüsselbundverwaltung** aus.

2. Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat von einer Zertifizierungsinstanz anfordern ...**.

    ![][5]

3. Machen Sie Angaben in den Feldern **E-Mail des Benutzers** und **Allgemeiner Name**. Vergewissern Sie sich, dass **Auf der Festplatte sichern** ausgewählt ist, und klicken Sie dann auf **Fortfahren**.

    ![][6]

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

    ![][7]
  
    Merken Sie sich den ausgewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR hochladen, um ein Pushzertifikat zu erstellen.

## <a name="register"></a>Registrieren der App für Pushbenachrichtigungen

Um von Ihrer mobilen App aus Pushbenachrichtigungen an ein iOS-Gerät senden zu können, muss die Anwendung bei Apple und für Pushbenachrichtigungen registriert werden. 

1. Wenn Sie Ihre App noch nicht registriert haben, navigieren Sie zum iOS-Bereitstellungsportal</a> im Apple Developer Center. Melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers** und dann auf **App IDs**. Klicken Sie dann auf das Pluszeichen **+**, um eine App-ID für Ihre App zu erstellen.
    
    ![][102]

2. Geben Sie in **Description** einen Namen für Ihre App ein, tragen Sie den eindeutigen **Bundle Identifier** ein, und merken Sie ihn sich, markieren Sie die Option "Push Notifications" im Abschnitt "App Services", und klicken Sie dann auf **Continue**. In diesem Beispiel wird die ID **MobileServices.Quickstart** verwendet. Dieselbe ID kann jedoch nicht erneut benutzt werden, da App-IDs über alle Benutzer einzigartig sein müssen. Daher empfiehlt es sich, dass Sie Ihren vollen Namen oder Ihre Initialen an den App-Namen anhängen. 

    ![][103]
   
    Hierdurch wird Ihre App-ID generiert und Sie werden aufgefordert, die Daten per **Senden** zu übermitteln. Klicken Sie auf **Submit**.
   
    ![][104] 
   
    Nach dem Klicken auf **Senden** wird der Bildschirm **Registrierung abgeschlossen** geöffnet, wie nachstehend gezeigt. Klicken Sie auf **Done**.
   
    ![][105]    

3. Lokalisieren Sie die soeben erstellte App-ID und klicken Sie auf deren Zeile. 

    ![][106]
   
    Durch Klicken auf die App-ID werden Einzelheiten zur App und die App-ID angezeigt. Klicken Sie auf die Schaltfläche **Einstellungen**.
   
    ![][107] 
   
4. Führen Sie einen Bildlauf bis zum unteren Bildschirmbereich durch, und klicken Sie auf die Schaltfläche **Create Certificate...** unterhalb des Abschnitts **Development Push SSL Certificate**.

    ![][108] 

    Hierdurch wird der Assistent "Add iOS Certificate" angezeigt.
   
    Hinweis: In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Stellen Sie lediglich sicher, dass Sie beim Hochladen des Zertifikats in Ihre mobile App denselben Zertifikattyp festlegen.

5. Klicken Sie auf **Choose File**, wechseln Sie zum Speicherort der CSR-Datei, und klicken Sie dann auf **Generate**. 

    ![][110]
  
6. Nach Erstellung des Zertifikats durch das Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.
 
    ![][111]  

    Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert. 

    ![][9] 

    Hinweis: Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens <strong>aps_development.cer</strong>.

7. Doppelklicken Sie auf das heruntergeladene Pushzertifikat **aps_development.cer**.

    Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

    ![][10]

    Hinweis: Der Name in Ihrem Zertifikat kann abweichen, erhält aber das Präfix <strong>Apple Development iOS Push Notification Services:</strong>.

Später können Sie mit diesem Zertifikat eine .p12-Datei generieren und in Ihre mobile App hochladen, um die Authentifizierung mit APNS zu ermöglichen.

## <a name="profile"></a>Erstellen eines Bereitstellungsprofils für die App
 
1. Zurück im <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisiong Profile** gestartet.

    ![][112]

2. Wählen Sie unter **Development** den Punkt **iOS App Development** als Bereitstellungsprofiltyp, und klicken Sie dann auf **Continue**.

3. Wählen Sie anschließend die App-ID für die Mobile App-Schnellstart-App aus der Dropdownliste **App ID** aus, und klicken Sie auf **Continue**.

    ![][113]

4. Wählen Sie im Bildschirm **Select certificates** das zuvor erstellte Zertifikat aus, und klicken Sie auf **Continue**.

    ![][114]

5. Anschließend wählen Sie die zum Testen zu verwendenden **Devices**, und klicken Sie auf **Continue**.
  
    ![][115]

6. Wählen Sie abschließend in **Profile Name** einen Namen für das Profil, klicken Sie auf **Generate** und anschließend auf **Done**.

    ![][116]

    Dadurch wird ein neues Bereitstellungsprofil erstellt.

    ![][117]

## <a name="configure-appServiceMobile"></a>Konfigurieren des mobilen App Service-Back-Ends zum Senden von Pushanforderungen

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
        var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";

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

## <a name="configure-app"></a>Konfigurieren der Xamarin.iOS-Anwendung

1. Öffnen Sie in Xamarin.Studio **Info.plist**, und aktualisieren Sie die **Bundle-IDs** mit der ID, die Sie zuvor erstellt haben.

    ![][121]

2. Führen Sie einen Bildlauf nach unten bis **Hintergrundmodi** durch, und aktivieren Sie die Felder **Hintergrundmodi aktivieren** und **Remotebenachrichtigungen**. 

    ![][122]

3. Doppelklicken Sie im Projektmappen-Bereich auf Ihr Projekt, um die **Projektoptionen** zu öffnen.

4.  Aktivieren Sie unter **Build** die Option **iOS-Bundle signieren**, und wählen Sie die **Identität** und das **Bereitstellungsprofil**, die bzw. das Sie für dieses Projekt eingerichtet haben. 

    ![][120]

    Dadurch wird sichergestellt, dass das Xamarin-Projekt das neue Profil für die Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin-Gerätebereitstellung].

## <a name="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

1. Setzen Sie in **QSTodoService** die vorhandene Clientdeklaration außer Kraft, sodass **AppDelegate** den mobilen Client abrufen kann:
        
        public MobileServiceClient client { get; private set; }

2. Setzen Sie in **AppDelegate** das Ereignis **FinishedLaunching** außer Kraft: 

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


<h2><a name="publish-the-service"></a>Veröffentlichen des mobilen Back-Ends in Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>Testen von Pushbenachrichtigungen in Ihrer App

1. Drücken Sie die Schaltfläche **Ausführen**, um das Projekt zu erstellen, und starten Sie die App auf einem iOS-fähigen Gerät, klicken Sie anschließend auf**OK**, um die Pushbenachrichtigungen zu akzeptieren.
	
	> [AZURE.NOTE] Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen (**+**).

3. Vergewissern Sie sich, dass eine Benachrichtigung empfangen wird, und klicken Sie dann auf **OK**, um die Benachrichtigung zu schließen.

4. Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob eine Benachrichtigung angezeigt wird.

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[Erste Schritte mit mobilen App Service-Apps]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

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
[Apple Push Notification Service (APNS)]: http://go.microsoft.com/fwlink/p/?LinkId=272584
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


<!--HONumber=49-->