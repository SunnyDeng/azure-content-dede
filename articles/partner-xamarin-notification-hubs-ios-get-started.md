<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="Get Started" pageTitle="Get Started with Notification Hubs for Xamarin iOS apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="donnam" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine iOS-App senden.
Sie erstellen in diesem Lernprogramm eine leere Xamarin.iOS-App, die Pushbenachrichtigungen über den Apple Push Notification Service (APNS) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der komplette Code ist verfügbar in der [NotificationHubs-Beispielapp][NotificationHubs-Beispielapp].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Erstellen der Zertifikatsignieranforderungsdatei][Erstellen der Zertifikatsignieranforderungsdatei]
2.  [Registrieren der App und Aktivieren von Pushbenachrichtigungen][Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3.  [Erstellen eines Bereitstellungsprofils für die App][Erstellen eines Bereitstellungsprofils für die App]
4.  [Konfigurieren Ihres Notification Hub][Konfigurieren Ihres Notification Hub]
5.  [Verbinden Ihrer App mit dem Notification Hub][Verbinden Ihrer App mit dem Notification Hub]
6.  [Senden von Benachrichtigungen von Ihrem Back-End aus][Senden von Benachrichtigungen von Ihrem Back-End aus]

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Für dieses Lernprogramm ist Folgendes erforderlich:

-   [XCode 5,0][XCode 5,0]
-   Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
-   iOS-Entwicklerprogramm-Mitgliedschaft
-   [Xamarin.iOS][1]
-   [Azure Mobile Services-Komponente][Azure Mobile Services-Komponente]

   <div class="dev-callout"><b>Hinweis</b><br />
   <p>Pushbenachrichtigungen m&uuml;ssen aufgrund von Konfigurationsanforderungen auf einem iOS-f&auml;higen Ger&auml;t (iPhone oder iPad) anstatt im Simulator bereitgestellt und getestet werden.</p>
   </div>

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Xamarin.iOS-Apps.

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschlie&szlig;en. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihres mobilen Dienstes. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Merkmale finden Sie unter [Apple Push Notification Service][Apple Push Notification Service].

## <a name="certificates"></a><span class="short-header">Generieren der CSR-Datei</span>Generieren der Zertifikatsignieranforderungsdatei

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1.  Führen Sie das Programm "Schlüsselbundverwaltung" im Ordner "Dienstprogramme" aus.

2.  Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern …**.

    ![][]

3.  Machen Sie entsprechende Angaben für die Felder **E-Mail des Benutzers** sowie **Allgemeiner Name** und **E-Mail der Zert.-Instanz**, vergewissern Sie sich, dass **Auf der Festplatte sichern ausgewählt ist**, und klicken Sie dann auf **Fortfahren**.

    ![][2]

4.  Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

    ![][3]

    Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei der Desktop der Standardspeicherort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR-Datei hochladen, um ein Pushzertifikat zu erstellen.

## <a name="register"></a><span class="short-header">Registrieren Ihrer App</span>Registrieren Ihrer App für Pushbenachrichtigungen

Um von Mobile Services Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.

1.  Falls Sie Ihre App noch nicht registriert haben, gehen Sie im Apple Developer Center zum [iOS Provisioning Portal][iOS Provisioning Portal], melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Zeichen **+**, um eine neue App zu registrieren.

    ![][4]

2.  Geben Sie in **Description** einen Namen für Ihre App ein, tragen Sie den Wert MobileServices.Quickstart in **Bundle Identifier** ein, markieren Sie die Option "Push Notifications" im Abschnitt "App Services", und klicken Sie dann auf **Continue**.

    ![][5]

    ![][6]

    ![][7]

    Hierdurch wird Ihre App-ID generiert, und Sie werden zum Übermitteln der Daten aufgefordert. Klicken Sie auf **Senden**.

    ![][8]

    Nachdem Sie **Submit** angeklickt haben, wird der **Registration complete**-Bildschirm geöffnet, wie unten gezeigt. Klicken Sie auf **Done**.

    ![][9]

    <div class="dev-callout"><b>Hinweis</b>
<p>Wenn Sie sich daf&uuml;r entscheiden, einen anderen <strong>Bundle Identifier</strong>-Wert als <b>MobileServices.Quickstart</b> anzugeben, m&uuml;ssen Sie auch die Bundle-ID in Ihrem Xcode-Projekt aktualisieren.</p>
 </div>

3.  Lokalisieren Sie die soeben erstellte App-ID und klicken Sie auf deren Zeile.

    ![][10]

    Durch Anklicken der App-ID werden Einzelheiten zur App und die App-ID angezeigt:

    ![][11]

    ![][12]

4.  Klicken Sie auf **Bearbeiten**, blättern Sie zum unteren Rand des Bildschirms, und klicken Sie auf die Schaltfläche **Create Certificate...** unter dem Abschnitt **Development Push SSL Certificate**.

    ![][13]

    Hierdurch wird der Assistent "Add iOS Certificate" zur Anzeige gebracht.

    ![][14]

    <div class="dev-callout"><b>Hinweis</b>
<p>In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgef&uuml;hrt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.</p>
</div>

5.  Klicken Sie auf **Choose File**, navigieren Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei, und klicken Sie dann auf **Generate**.

    ![][15]

6.  Nach Erstellung des Zertifikats durch das Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.

    ![][16]

    ![][17]

    Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem **Download**-Ordner gespeichert.

    ![][18]

    <div class="dev-callout"><b>Hinweis</b>
<p>Standardm&auml;&szlig;ig ist die heruntergeladene Datei ein Entwicklungszertifikat namens <strong>aps_development.cer</strong>.</p>
</div>

7.  Doppelklicken Sie auf dem heruntergeladenen Pushzertifikat **aps\_development.cer**.

    Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

    ![][19]

    > [WACOM.NOTE]
    > Der Name in Ihrem Zertifikat kann abweichen, er wird aber das Präfix **Apple Development iOS Push Notification Services:** aufweisen.

    Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und in Ihr Notification Hub hochladen, um Pushbenachrichtigungen mit APNS zu ermöglichen.

## <a name="profile"></a><span class="short-header">Bereitstellen der App</span>Erstellen eines Bereitstellungsprofils für die App

1.  Zurück im [iOS Provisioning Portal][iOS Provisioning Portal] wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisioning Profile** gestartet.

    ![][20]

2.  Unter **Development** wählen Sie **iOS App Development als Bereitstellungsprofiltyp**, und klicken Sie dann auf **Continue**.

    ![][21]

3.  Anschließend wählen Sie die App-ID für die Mobile Services Quickstart-App aus der **App ID**-Dropdown-Liste, und klicken Sie auf **Continue**.

    ![][22]

4.  Auf dem Bildschirm **Select certificates** wählen Sie das zuvor erstellte Zertifikat, und klicken Sie auf **Continue**.

    ![][23]

5.  Anschließend wählen Sie die zum Testen zu verwendenden **Devices**, und klicken Sie auf **Continue**.

    ![][24]

6.  Geben Sie schließlich einen Namen für das Profil im Feld **Profile Name** ein, klicken Sie auf **Generate**, dann auf **Done**.

    ![][25]

    ![][26]

    Dadurch wird ein neues Bereitstellungsprofil erstellt.

7.  Öffnen Sie in Xcode den Organisator, wählen Sie die Geräteansicht, dann **Provisioning Profiles** im Abschnitt **Library** im linken Bereich, und importieren Sie das gerade erstellte Bereitstellungsprofil.

8.  Wählen Sie links das Gerät aus, und importieren Sie erneut das Bereitstellungsprofil.

9.  In der Schlüsselbundverwaltung klicken Sie mit der rechten Maustaste auf das neue Zertifikat, klicken Sie auf **Export**, geben Sie einen Namen für Ihr Zertifikat ein, wählen Sie das **.p12**-Format, und klicken Sie dann auf **Save**.

    ![][27]

    Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

Hierdurch wird sichergestellt, dass das Xcode-Projekt das neue Profil für Codesignierung verwendet. Als Nächstes laden Sie das Zertifikat in Ihr Notification Hub hoch.

## <a name="configure-hub"></a><span class="short-header">Konfigurieren Ihres Notification Hub</span>Konfigurieren Ihres Notification Hub

1.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und klicken Sie unten auf dem Bildschirm auf **+NEU**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

    ![][28]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

    ![][29]

4.  Klicken Sie auf den soeben erstellten Namespace (für gewöhnlich ***Name des Notification Hub*-ns**), und anschließend oben auf die Registerkarte **Configure**.

    ![][30]

5.  Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

    ![][31]

6.  Wählen Sie oben die Registerkarte **Configure**, und klicken Sie dann für die Apple-Benachrichtigungseinstellungen auf **Upload**. Wählen Sie anschließend das zuvor exportierte **.p12**-Zertifikat und das Kennwort für das Zertifikat aus. Wählen Sie aus, ob Sie den Pushdienst **Production** (wenn Sie Pushbenachrichtigungen an Benutzer versenden möchten, die Ihre App aus dem Store erworben haben) oder **Sandbox** (während der Entwicklung) verwenden möchten.

    ![][32]

7.  Wählen Sie oben die Registerkarte **Dashboard**, und klicken Sie danach auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

    ![][33]

Der Notification Hub ist nun konfiguriert, um mit APNs arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

## <a name="connecting-app"></a><span class="short-header">Verbinden Ihrer App</span>Verbinden Ihrer App mit dem Notification Hub

### Herunterladen der WindowsAzure.Messaging-Bibliothek

Diese Assembly vereinfacht die Registrierung bei Azure Notification Hubs. Sie kann anhand der unten stehenden Anweisungen heruntergeladen oder aus dem [Beispieldownload][NotificationHubs-Beispielapp] abgerufen werden.

1.  Laden Sie die Quelle für [WindowsAzure.Messaging][WindowsAzure.Messaging] von GitHub herunter.

2.  Kompilieren Sie das Projekt, und suchen Sie die Ausgabeassembly **WindowsAzure.Messaging.dll**. Sie benötigen diese, wenn Sie die Xamarin.iOS-Anwendung unten einrichten.

### Erstellen eines neuen Projekts

1.  Erstellen Sie in Xamarin Studio ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

    ![][34]

2.  Fügen Sie zuerst einen Verweis auf die Azure Mobile Services-Komponente hinzu. Klicken Sie in der Lösungsansicht mit der rechten Maustaste auf den Ordner **Components** für Ihr Projekt und wählen Sie **Get More Components** aus. Suchen Sie nach der Komponente **Azure Mobile Sevices** und fügen Sie die Komponente zu Ihrem Projekt hinzu.

3.  Fügen Sie nun einen Verweis auf die zuvor heruntergeladene WindowsAzure.Messaging-Bibliothek hinzu. Klicken Sie mit der rechten Maustaste auf den Ordner **References**, und wählen Sie **Edit References...** aus. Suchen Sie auf der Registerkarte **.Net Assembly** nach **WindowsAzure.Messaging.dll**.

4.  Fügen Sie der Datei **AppDelegate.cs** folgende using-Anweisungen hinzu:

        using Microsoft.WindowsAzure.MobileServices;

    using WindowsAzure.Messaging;

5.  Erstellen Sie eine Instanz von **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

6.  Erstellen Sie eine Klasse **Constants.cs** mit den folgenden Variablen:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";

7.  Ändern Sie in **AppDelegate.cs** die Methode **FinishedLaunching()** auf folgende Weise:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8.  Überschreiben Sie die Methode **RegisteredForRemoteNotifications()** in **AppDelegate.cs**:

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

9.  Überschreiben Sie die Methode **ReceivedRemoteNotification()** in **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

10. Erstellen Sie die folgende Methode **ProcessNotification()** in **AppDelegate.cs**:

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

    <div class="dev-callout"><b>Hinweis</b>
<p>Sie k&ouml;nnen **FailedToRegisterForRemoteNotifications()** &uuml;berschreiben, um Situationen wie z. B. eine fehlende Netzwerkverbindung zu behandeln.</p>
</div>

11. Führen Sie die App auf Ihrem Gerät aus.

## <a name="send"></a><span class="short-header">Senden einer Benachrichtigung</span>Senden einer Benachrichtigung von Ihrem Back-End aus

Sie können Benachrichtigungen mit Notification Hubs von jedem beliebigen Back-End aus mithilfe der [REST-Schnittstelle][REST-Schnittstelle] senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem Mobile Service, der ein Knotenskript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1.  Erstellen einer neuen Visual C#-Konsolenanwendung:

    ![][35]

2.  Fügen Sie mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets][WindowsAzure.ServiceBus NuGet-Pakets] eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Package Manager Console**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

        Install-Package WindowsAzure.ServiceBus and press Enter.

3.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

4.  Fügen Sie in der Klasse `Program` die folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

5.  Fügen Sie dann die folgende Zeile zu Ihrer `Main`-Methode hinzu:

         SendNotificationAsync();
         Console.ReadLine();

6.  Drücken Sie die Taste F5, um die App auszuführen. Sie sollten eine Warnung auf Ihrem Gerät erhalten. Überprüfen Sie die Verbindung, wenn Sie WLAN verwenden.

Sie finden alle möglichen Nutzlasten im [Apple Local and Push Notification Programming Guide][Apple Local and Push Notification Programming Guide] (in englischer Sprache).

Zum Senden einer Benachrichtigung mit Mobile Service folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services]. Gehen Sie dann wie folgt vor:

1.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und wählen Sie Ihren Mobile Service aus.

2.  Wählen Sie oben die Registerkarte **Scheduler**.

    ![][36]

3.  Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.

    ![][37]

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

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Ihre iOS-Geräte übertragen. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten][Verwenden von Notification Hubs zum Übermitteln von Nachrichten] lesen. Weitere Informationen über die Verwendung von Notification Hubs finden Sie unter [Notification Hubs-Leitfaden][Notification Hubs-Leitfaden] und [Notification Hubs-Informationen für iOS][Notification Hubs-Informationen für iOS].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Universal]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /de-de/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /de-de/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /de-de/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /de-de/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [NotificationHubs-Beispielapp]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Erstellen der Zertifikatsignieranforderungsdatei]: #certificates
  [Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
  [Erstellen eines Bereitstellungsprofils für die App]: #profile
  [Konfigurieren Ihres Notification Hub]: #configure-hub
  [Verbinden Ihrer App mit dem Notification Hub]: #connecting-app
  [Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
  [XCode 5,0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [1]: http://xamarin.com/download
  [Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F
  [Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  []: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
  [2]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
  [3]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png
  [iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [4]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
  [5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
  [6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
  [7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
  [8]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
  [9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
  [10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
  [11]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
  [12]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
  [13]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
  [14]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
  [15]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png
  [16]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
  [17]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png
  [18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
  [19]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
  [20]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
  [21]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
  [22]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
  [23]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
  [24]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
  [25]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
  [26]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png
  [27]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [30]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
  [34]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [REST-Schnittstelle]: http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx
  [35]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet-Pakets]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Apple Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-ios
  [36]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
  [37]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
  [Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users-aspnet
  [Verwenden von Notification Hubs zum Übermitteln von Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
  [Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
  [Notification Hubs-Informationen für iOS]: http://msdn.microsoft.com/de-de/library/jj927168.aspx
