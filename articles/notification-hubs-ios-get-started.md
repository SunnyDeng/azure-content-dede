<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Erste Schritte mit Notification Hubs
====================================

[Windows Store C\#](/de-de/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/de-de/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/de-de/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/de-de/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/de-de/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine iOS-App senden. In diesem Lernprogramm erstellen Sie eine leere iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNs) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Erstellen der Zertifikatsignieranforderungsdatei](#certificates)
2.  [Registrieren der App und Aktivieren von Pushbenachrichtigungen](#register)
3.  [Erstellen eines Bereitstellungsprofils für die App](#profile)
4.  [Konfigurieren Ihres Notification Hub](#configure-hub)
5.  [Verbinden Ihrer App mit dem Notification Hub](#connecting-app)
6.  [Senden von Benachrichtigungen von Ihrem Back-End aus](#send)

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Notification Hubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Mobile Services iOS SDK](http://go.microsoft.com/fwLink/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
-   iOS-Entwicklerprogramm-Mitgliedschaft

    **Hinweis**

    Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für iOS-Apps.

**Hinweis**

Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihres mobilen Dienstes. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Merkmale finden Sie unter [Apple Push Notification Service].

Generieren der CSR-DateiGenerieren der Zertifikatsignieranforderungsdatei
-------------------------------------------------------------------------

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1.  Führen Sie das Programm "Schlüsselbundverwaltung" im Ordner "Dienstprogramme" aus.

2.  Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern …**.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png)

3.  Machen Sie entsprechende Angaben für die Felder **E-Mail des Benutzers** sowie **Allgemeiner Name**, vergewissern Sie sich, dass **Auf der Festplatte sichern** gewählt ist, und klicken Sie dann auf **Fortfahren**. Lassen Sie das Feld **E-Mail der Zert.-Instanz** leer, da hier keine Eingabe benötigt wird.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png)

4.  Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png)

Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei der Desktop der Standardspeicherort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR-Datei hochladen, um ein Pushzertifikat zu erstellen.

Registrieren Ihrer AppRegistrieren Ihrer App für Pushbenachrichtigungen
-----------------------------------------------------------------------

Um von mobilen Diensten Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.

1.  Falls Sie Ihre App noch nicht registriert haben, gehen Sie im Apple Developer Center zum [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456), melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Zeichen **+**, um eine neue App zu registrieren.

    ![][B102] 

2.  Geben Sie in **Description** einen Namen für Ihre App ein, tragen Sie den Wert *MobileServices.Quickstart* in **Bundle Identifier** ein, markieren Sie die Option "Push Notifications" im Abschnitt "App Services", und klicken Sie dann auf **Continue**. In diesem Beispiel wird die ID **MobileServices.Quickstart** verwendet. Dieselbe ID kann jedoch nicht erneut benutzt werden, da App-IDs über alle Benutzer einzigartig sein müssen. Daher empfiehlt es sich, dass Sie Ihren vollen Namen oder Ihre Initialen an den App-Namen anhängen.

    ![][B103]
           
    Hierdurch wird Ihre App-ID generiert, und Sie werden zum **Übermitteln** der Daten aufgefordert. Klicken Sie auf **Submit**.
       
    ![][B104] 
       
    Nachdem Sie **Submit** angeklickt haben, erscheint die Registrierabschlussanzeige **Registration complete**, wie unten gezeigt. Klicken Sie auf **Done**.
       
    ![][B105]

    > [WACOM.NOTE] Wenn Sie sich dazu entscheiden, einen anderen **Bundle Identifier**-Wert als *MobileServices.Quickstart* vorzugeben, müssen Sie auch die Bundle-ID in Ihrem Xcode-Projekt aktualisieren.

3.  Lokalisieren Sie die soeben erstellte App-ID, und klicken Sie auf deren Zeile.

    ![][B106]
       
    Durch Anklicken der App-ID werden Einzelheiten zur App und die App-ID angezeigt. Klicken Sie auf die Schaltfläche **Settings**.
       
    ![][B107] 

4.  Scrollen Sie bis zur Unterseite des Bildschirms, und klicken Sie auf die Schaltfläche **Create Certificate...** unter dem Abschnitt **Development Push SSL Certificate**.

    ![][B108] 

    Hierdurch wird der Assistent "Add iOS Certificate" angezeigt.
       
    ![][B108] 

    > [WACOM.NOTE] In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.

5.  Klicken Sie auf **Choose File**, browsen Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei, und klicken Sie dann auf **Generate**.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png)

1.  Nach Erstellung des Zertifikats durch das Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png)
 	
	Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert. 
	
	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens **aps_development.cer**.

1.  Doppelklicken Sie auf das heruntergeladene Pushzertifikat **aps\_development.cer**.

    Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

    ![][B10]

    > [WACOM.NOTE] Der Name in Ihrem Zertifikat kann verschieden sein, aber er wird den Vorsatz **Apple Development iOS Push Notification Services:** haben.

Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und zu Mobile Services hochladen, um Authentifizierung mit APNS zu ermöglichen.

Bereitstellen der AppErstellen eines Bereitstellungsprofils für die App
-----------------------------------------------------------------------

1.  Zurück im [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisioning Profile** gestartet.

    ![][120]

2.  Unter **Development** wählen Sie **iOS App Development** als Bereitstellungsprofiltyp, und klicken Sie dann auf **Continue**.

    ![][121]

3.  Anschließend wählen Sie die App-ID für die Mobile Services Quickstart-App aus der **App ID**-Dropdown-Liste, und klicken Sie auf **Continue**.

    ![][122]

4.  Auf dem Bildschirm **Select certificates** wählen Sie das zuvor erstellte Zertifikat, und klicken Sie auf **Continue**.

    ![][123]

5.  Anschließend wählen Sie die zum Testen zu verwendenden **Devices**, und klicken auf **Continue**.

    ![][124]

6.  Geben Sie schließlich einen Namen für das Profil im Feld **Profile Name** ein, klicken Sie auf **Generate**, dann auf **Done**.

    ![][125]
       
    ![][126]

    Dadurch wird ein neues Bereitstellungsprofil erstellt.

7.  Öffnen Sie in Xcode den Organisator, wählen Sie die Geräteansicht, dann **Provisioning Profiles** im Abschnitt **Library** im linken Bereich, und importieren Sie das gerade erstellte Bereitstellungsprofil.

8.  Wählen Sie links das Gerät aus, und importieren Sie erneut das Bereitstellungsprofil.

9.  In der Schlüsselbundverwaltung klicken Sie mit der rechten Maustaste auf das neue Zertifikat, klicken Sie auf **Exportieren**, geben Sie einen Namen für Ihr Zertifikat ein, wählen Sie das **.p12**-Format, und klicken Sie dann auf **Sichern**.

    ![][18]

    Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

Konfigurieren Ihres Notification HubKonfigurieren Ihres Notification Hub
------------------------------------------------------------------------

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie unten auf dem Bildschirm auf **+NEU**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

    ![][27]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

    ![][28]

4.  Klicken Sie auf den soeben erstellten Namespace (für gewöhnlich ***Name des Notification Hub*-ns**), und anschließend oben auf die Registerkarte **Konfigurieren**.

    ![][29]

5.  Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

    ![][210]

6.  Wählen Sie oben die Registerkarte **Configure**, und klicken Sie dann für die Apple-Benachrichtigungseinstellungen auf **Upload**. Wählen Sie anschließend das zuvor exportierte **.p12**-Zertifikat und das Kennwort für das Zertifikat aus. Wählen Sie aus, ob Sie den Pushdienst **Production** (wenn Sie Pushbenachrichtigungen an Benutzer versenden möchten, die Ihre App aus dem Store erworben haben) oder **Sandbox** (während der Entwicklung) verwenden möchten.

    ![][211]

7.  Wählen Sie oben die Registerkarte **Dashboard**, und klicken Sie danach auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

    ![][212]

Der Notification Hub ist nun konfiguriert, um mit APNs arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

Verbinden Ihrer AppVerbinden Ihrer App mit dem Notification Hub
---------------------------------------------------------------

1.  Erstellen Sie in XCode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

    ![][31]

2.  Klicken Sie unter **Targets** auf den Projektnamen, erweitern Sie **Code Signing Identity**, und wählen Sie unter **Debug** das Identitätsprofil für die Codesignierung aus. Wenn Sie eine neuere Version von XCode verwenden, können Sie zusätzlich für **Levels** von **Basic** zu **All** wechseln und das Zeilenelement **Provisioning Profile** zum Bereitstellungsprofil ändern.

    ![][32]

3.  Laden Sie den Azure Mobile SDK für iOS herunter. Öffnen Sie die .zip-Datei, und ziehen Sie den Ordner "WindowsAzureMessaging.framework" in den Framework-Ordner Ihres XCode-Projekts. Wählen Sie **Copy items in destination group's folder**, und klicken Sie dann auf **OK**.

    ![][33]

4.  Fügen Sie in der Datei "AppDelegate.h" die folgende Importdirektive hinzu:

          #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  Fügen Sie in der Datei "AppDelegate.m" folgenden Code zur Methode `didFinishLaunchingWithOptions` hinzu:

          [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  Fügen Sie in derselben Datei die folgende Methode hinzu:

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken(NSData *) deviceToken {    
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

Senden einer BenachrichtigungSenden einer Benachrichtigung von Ihrem Back-End aus
---------------------------------------------------------------------------------

Sie können Benachrichtigungen mit Notification Hubs von jedem beliebigen Back-End aus mithilfe der [REST-Schnittstelle](http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx) senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem Mobile Service, der ein Knotenskript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1.  Erstellen einer neuen Visual C\#-Konsolenanwendung:

    ![](./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png)

2.  Fügen Sie mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets](http://nuget.org/packages/WindowsAzure.ServiceBus/) eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Paket-Managerkonsole**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

         Install-Package WindowsAzure.ServiceBus

    und drücken Sie die Eingabetaste.

3.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

         using Microsoft.ServiceBus.Notifications;

4.  Fügen Sie in der Klasse `Program` die folgende Methode hinzu. Stellen Sie sicher, dass Sie den Platzhalter "Hub-Name" durch den Namen des Notification Hub ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird (wie etwa **mynotificationhub2** im vorigen Beispiel):

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }

5.  Fügen Sie dann die folgende Zeile zur `Main`-Methode hinzu:

          SendNotificationAsync();
          Console.ReadLine();

6.  Drücken Sie die Taste F5, um die App auszuführen. Sie sollten eine Warnung auf Ihrem Gerät erhalten. Überprüfen Sie die Verbindung, wenn Sie WLAN verwenden.

Sie finden alle möglichen Nutzlasten im Apple [Local and Push Notification Programming Guide](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1) (in englischer Sprache).

Zum Senden einer Benachrichtigung mit Mobile Service folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-ios). Gehen Sie dann wie folgt vor:

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und wählen Sie Ihren Mobile Service aus.

2.  Wählen Sie oben die Registerkarte **Scheduler**.

    ![][215]

3.  Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.

    ![][216]

4.  Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend in der Kopfleiste auf die Registerkarte **Skript**.

5.  Fügen Sie das folgende Skript in Ihre Scheduler-Funktion ein. Stellen Sie sicher, dass Sie die Platzhalter durch den Notification Hub-Namen und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* ersetzen, die Sie zuvor erhalten haben. Klicken Sie auf **Speichern**.

		var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
        	null,
    		{"aps":
        		{
                "alert": "Hello from Mobile Services!"
                }
	    	}, 
			function(error)
			{
				if (!error) {
	    			console.warn("Notification successful");
	            }
	    	}
		);

6.  Klicken Sie in der unteren Leiste auf **Einmal ausführen**. Sie sollten eine Warnung auf Ihrem Gerät erhalten.

Nächste Schritte
----------------

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Ihre iOS-Geräte übertragen. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users-aspnet). Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten](/de-de/manage/services/notification-hubs/breaking-news-dotnet) lesen. Weitere Informationen über die Verwendung von Notification Hubs finden Sie unter [Notification Hubs-Leitfaden](http://msdn.microsoft.com/de-de/library/jj927170.aspx) und [Notification Hubs-Informationen für iOS](http://msdn.microsoft.com/de-de/library/jj927168.aspx).


<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png


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
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/de-de/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Notification Hubs to push notifications to users]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /de-de/manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
