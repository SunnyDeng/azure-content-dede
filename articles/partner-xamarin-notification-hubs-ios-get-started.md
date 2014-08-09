<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="Get Started" pageTitle="Get Started with Notification Hubs for Xamarin iOS apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="" />

Erste Schritte mit Notification Hubs
====================================

[Windows Store C\#](/de-de/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/de-de/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/de-de/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/de-de/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/de-de/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine iOS-App senden. In diesem Lernprogramm erstellen Sie eine leere iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNs) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der komplette Code ist verfügbar in der [NotificationHubs-Beispielapp](http://go.microsoft.com/fwlink/p/?LinkId=331329).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Erstellen der Zertifikatsignieranforderungsdatei](#certificates)
2.  [Registrieren der App und Aktivieren von Pushbenachrichtigungen](#register)
3.  [Erstellen eines Bereitstellungsprofils für die App](#profile)
4.  [Konfigurieren Ihres Notification Hub](#configure-hub)
5.  [Verbinden Ihrer App mit dem Notification Hub](#connecting-app)
6.  [Senden von Benachrichtigungen von Ihrem Back-End aus](#send)

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Für dieses Lernprogramm ist Folgendes erforderlich:

-   [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
-   iOS-Entwicklerprogramm-Mitgliedschaft
-   [Xamarin.iOS]
-   [Azure Mobile Services-Komponente](http://components.xamarin.com/view/azure-mobile-services/)

    **Hinweis**

    Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für iOS-Apps.

**Hinweis**

Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F).

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihres mobilen Dienstes. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Merkmale finden Sie unter [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Generieren der CSR-DateiGenerieren der Zertifikatsignieranforderungsdatei
-------------------------------------------------------------------------

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1.  Führen Sie das Programm "Schlüsselbundverwaltung" im Ordner "Dienstprogramme" aus.

2.  Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern...**.

  	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png)

1.  Machen Sie entsprechende Angaben für die Felder **E-Mail des Benutzers** sowie **Allgemeiner Name** und **E-Mail der Zert.-Instanz**, vergewissern Sie sich, dass **Auf der Festplatte sichern ausgewählt ist**, und klicken Sie dann auf **Fortfahren**.

  	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png)

1.  Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort unter **Ort** und klicken Sie auf **Speichern**.

  	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png)
  	Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei der Desktop der Standardspeicherort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR-Datei hochladen, um ein Pushzertifikat zu erstellen.

Registrieren Ihrer AppRegistrieren Ihrer App für Pushbenachrichtigungen
-----------------------------------------------------------------------

Um von mobilen Diensten Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.

1.  Falls Sie Ihre App noch nicht registriert haben, gehen Sie im Apple Developer Center zum [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456), melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Zeichen **+**, um eine neue App zu registrieren.

  	![][105] 

2.  Geben Sie in **Description** einen Namen für Ihre App ein, tragen Sie den Wert MobileServices.Quickstart in **Bundle Identifier** ein, markieren Sie die Option "Push Notifications" im Abschnitt "App Services", und klicken Sie dann auf **Continue**.

  	![][106]

  	![][107] 

  	![][108]

    Hierdurch wird Ihre App-ID generiert, und Sie werden zum Übermitteln der Daten aufgefordert. Klicken Sie auf **Submit**.

  	![][109] 

    Nachdem Sie **Submit** angeklickt haben, wird der **Registration complete**-Bildschirm geöffnet, wie unten gezeigt. Klicken Sie auf **Done**.

   	![][110]

    **Hinweis**

    Wenn Sie sich dafür entscheiden, einen anderen **Bundle Identifier**-Wert als **MobileServices.Quickstart** anzugeben, müssen Sie auch die Bundle-ID in Ihrem Xcode-Projekt aktualisieren.

3.  Lokalisieren Sie die soeben erstellte App-ID, und klicken Sie auf deren Zeile.

   	![][111]

    Durch Anklicken der App-ID werden Einzelheiten zur App und die App-ID angezeigt:

   	![][112] 
           
   	![][113]

4.  Klicken Sie auf **Bearbeiten**, blättern Sie zum unteren Rand des Bildschirms, und klicken Sie auf die Schaltfläche **Create Certificate...** unter dem Abschnitt **Development Push SSL Certificate**.

   	![][114] 

    Hierdurch wird der Assistent "Add iOS Certificate" angezeigt.

   	![][115] 

    **Hinweis**

    In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.

5.  Klicken Sie auf **Choose File**, navigieren Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei, und klicken Sie dann auf **Generate**.

   	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png)

1.  Nach Erstellung des Zertifikats durch das Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.

   	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png)

   	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png)

 Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem **Download**-Ordner gespeichert.

   	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png)

    <div class="dev-callout"><b>Note</b>
	<p>Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens **aps_development.cer**.</p>
    </div>

1.  Doppelklicken Sie auf das heruntergeladene Pushzertifikat **aps\_development.cer**.

    Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

   	![][10]

    > [WACOM.NOTE] Der Name in Ihrem Zertifikat kann verschieden sein, aber er wird den Vorsatz **Apple Development iOS Push Notification Services:** haben.

    Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und in Ihr Notification Hub hochladen, um Pushbenachrichtigungen mit APNS zu ermöglichen.

Bereitstellen der AppErstellen eines Bereitstellungsprofils für die App
-----------------------------------------------------------------------

1.  Zurück im [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisioning Profile** gestartet.

   	![][120]

2.  Unter **Development** wählen Sie **iOS App Development als Bereitstellungsprofiltyp**, und klicken Sie dann auf **Continue**.

   	![][121]

3.  Anschließend wählen Sie die App-ID für die Mobile Services Quickstart-App aus der **App ID**-Dropdown-Liste, und klicken Sie auf **Continue**.

   	![][122]

4.  Auf dem Bildschirm **Select certificates** wählen Sie das zuvor erstellte Zertifikat, und klicken Sie auf **Continue**.

   	![][123]

5.  Anschließend wählen Sie die zum Testen zu verwendenden **Devices**, und klicken Sie auf **Continue**.

   	![][124]

6.  Geben Sie schließlich einen Namen für das Profil im Feld **Profile Name** ein, klicken Sie auf **Generate**, dann auf **Done**.

   	![][125]
           
   	![][126]

  	Dadurch wird ein neues Bereitstellungsprofil erstellt.

1.  Öffnen Sie in Xcode den Organisator, wählen Sie die Geräteansicht, dann **Provisioning Profiles** im Abschnitt **Library** im linken Bereich, und importieren Sie das gerade erstellte Bereitstellungsprofil.

2.  Wählen Sie links das Gerät aus, und importieren Sie erneut das Bereitstellungsprofil.

3.  In der Schlüsselbundverwaltung klicken Sie mit der rechten Maustaste auf das neue Zertifikat, klicken Sie auf **Export**, geben Sie einen Namen für Ihr Zertifikat ein, wählen Sie das **.p12**-Format, und klicken Sie dann auf **Save**.

  	![][18]

  	Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

Hierdurch wird sichergestellt, dass das Xcode-Projekt das neue Profil für Codesignierung verwendet. Als Nächstes laden Sie das Zertifikat in Ihr Notification Hub hoch.

Konfigurieren Ihres Notification HubKonfigurieren Ihres Notification Hub
------------------------------------------------------------------------

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie unten auf dem Bildschirm auf **+NEU**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

  	![][27]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

  	![][28]

4.  Klicken Sie auf den soeben erstellten Namespace (für gewöhnlich ***Name des Notification Hub*-ns**), und anschließend oben auf die Registerkarte **Configure**.

  	![][29]

5.  Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

  	![][210]

6.  Wählen Sie oben die Registerkarte **Configure**, und klicken Sie dann für die Apple-Benachrichtigungseinstellungen auf **Upload**. Wählen Sie anschließend das zuvor exportierte **.p12**-Zertifikat und das Kennwort für das Zertifikat aus. Wählen Sie aus, ob Sie den Pushdienst **Production** (wenn Sie Pushbenachrichtigungen an Benutzer versenden möchten, die Ihre App aus dem Store erworben haben) oder **Sandbox** (während der Entwicklung) verwenden möchten.

   	![][211]

7.  Wählen Sie oben die Registerkarte **Dashboard**, und klicken Sie danach auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

  	![][212]

Der Notification Hub ist nun konfiguriert, um mit APNs arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

Verbinden Ihrer AppVerbinden Ihrer App mit dem Notification Hub
---------------------------------------------------------------

1.  Erstellen Sie in XCode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

   	![][31]

2.  Nun benötigen Sie einen Verweis auf die Azure Mobile Services-Komponente. Klicken Sie in der Lösungsansicht mit der rechten Maustaste auf den Ordner **Components** für Ihr Projekt und wählen Sie **Get More Components** aus. Suchen Sie nach der Komponente **Azure Mobile Sevices** und fügen Sie die Komponente zu Ihrem Projekt hinzu.

3.  Fügen Sie die folgende Anweisung zur Datei **AppDelegate.cs** hinzu:

 		using WindowsAzure.Messaging;

4.  Erstellen Sie eine Instanz von **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }

5.  Erstellen Sie eine Klasse **Constants.cs** mit den folgenden Variablen:

         // Verbindungszeichenfolge und Hub-Pfad für die spezielle Azure-App
         public const string ConnectionString = "<Azure connection string>";
         public const string NotificationHubPath = "<Azure hub path>";

6.  Ändern Sie in **AppDelegate.cs** die Methode **FinishedLaunching()** auf folgende Weise:

         public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
         {
             // HINWEIS: Niemals die Basisimplementierung einer Modellklasse aufrufen
             // siehe http://docs.xamarin.com/guides/ios/application_fundamentals/delegates,_protocols,_and_events 

             UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                 UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
             UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

             return true;
         }

7.  Überschreiben Sie die Methode **RegisteredForRemoteNotifications()** in **AppDelegate.cs**:

         public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
         {
             // HINWEIS: Niemals die Basisimplementierung einer Modellklasse aufrufen
             // siehe http://docs.xamarin.com/guides/ios/application_fundamentals/delegates,_protocols,_and_events 

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

8.  Überschreiben Sie die Methode **ReceivedRemoteNotification()** in **AppDelegate.cs**:

         public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
         {
             ProcessNotification(userInfo, false);
         }

9.  Erstellen Sie die folgende Methode **ProcessNotification()** in **AppDelegate.cs**:

         void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
         {
             // Anklicken, um nachzusehen, ob der aps-Schlüssel im Schlüsselverzeichnis existiert.  This is the notification payload you would have sent
             if (null != options && options.ContainsKey(new NSString("aps")))
             {
                 //aps-Schlüsselverzeichnis abrufen
                 NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                 string alert = string.Empty;

                 //Warnungstext extrahieren
                 //HINWEIS: Sie können eine einfache Warnung in Form von "  aps:{alert:"Warnmeldung"}  "
                 //ausgeben.  Für komplexere Warnungen mit Lokalisierungsschlüssel usw. 
                 //ist Ihr "alert"-Objekt aus dem aps-Schlüsselverzeichnis ein weiteres NSDictionary...
                 //Der json-Code wird in ein NSDictionary geschrieben
                 if (aps.ContainsKey(new NSString("alert")))
                     alert = (aps [new NSString("alert")] as NSString).ToString();

                 //Falls dies während der Ausführung der App von ReceivedRemoteNotification kam,
                 // müssen wir Dinge wie Ton, Signal und Warnung manuell verwalten.
                 if (!fromFinishedLaunching)
                 {
                     //Warnung manuell anzeigen
                     if (!string.IsNullOrEmpty(alert))
                     {
                         UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                         avAlert.Show();
                     }
                 }           
             }
         }

    **Hinweis**

    Sie können **FailedToRegisterForRemoteNotifications()** überschreiben, um Situationen wie z. B. eine fehlende Netzwerkverbindung zu behandeln.

10. Führen Sie die App auf Ihrem Gerät aus.

Senden einer BenachrichtigungSenden einer Benachrichtigung von Ihrem Back-End aus
---------------------------------------------------------------------------------

Sie können Benachrichtigungen mit Notification Hubs von jedem beliebigen Back-End aus mithilfe der [REST-Schnittstelle](http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx) senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem Mobile Service, der ein Knotenskript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1.  Erstellen einer neuen Visual C\#-Konsolenanwendung:

   	![][213]

2.  Fügen Sie mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets](http://nuget.org/packages/WindowsAzure.ServiceBus/) eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Package Manager Console**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

         Install-Package WindowsAzure.ServiceBus und drücken Sie die Eingabetaste.

3.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

         using Microsoft.ServiceBus.Notifications;

4.  Fügen Sie in der Klasse `Program` die folgende Methode hinzu:

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
          }
5.  Fügen Sie dann folgende Zeilen zur `Main`-Methode hinzu:

          SendNotificationAsync();
          Console.ReadLine();

6.  Drücken Sie die Taste F5, um die App auszuführen. Sie sollten eine Warnung auf Ihrem Gerät erhalten. Überprüfen Sie die Verbindung, wenn Sie WLAN verwenden.

Sie finden alle möglichen Nutzlasten im [Apple Local and Push Notification Programming Guide](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1) (in englischer Sprache).

Zum Senden einer Benachrichtigung mit Mobile Service folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-xamarin-ios). Gehen Sie dann wie folgt vor:

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und wählen Sie Ihren Mobile Service aus.

2.  Wählen Sie oben die Registerkarte **Scheduler**.

   	![][215]

3.  Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.

   	![][216]

4.  Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend in der Kopfleiste auf die Registerkarte **Script**.

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
    		function (error)
    		{
                 if (!error) {
                 	console.warn("Notification successful");
                 }
    		}
        );

6.  Klicken Sie in der unteren Leiste auf **Run Once**. Sie sollten eine Warnung auf Ihrem Gerät erhalten.

Nächste Schritte
----------------

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Ihre iOS-Geräte übertragen. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users-aspnet). Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten](/de-de/manage/services/notification-hubs/breaking-news-dotnet) lesen. Weitere Informationen über die Verwendung von Notification Hubs finden Sie unter [Notification Hubs-Leitfaden](http://msdn.microsoft.com/de-de/library/jj927170.aspx) und [Notification Hubs-Informationen für iOS](http://msdn.microsoft.com/de-de/library/jj927168.aspx).






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

