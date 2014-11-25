<properties linkid="manage-services-notification-hubs-getting-started-xamarin-android" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.Android-App senden.
Sie erstellen in diesem Lernprogramm eine leere Xamarin.Android-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der komplette Code ist verfügbar in der [NotificationHubs-Beispielapp][NotificationHubs-Beispielapp].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Aktivieren von Google Cloud Messaging][Aktivieren von Google Cloud Messaging]
2.  [Konfigurieren Ihres Notification Hub][Konfigurieren Ihres Notification Hub]
3.  [Verbinden Ihrer App mit dem Notification Hub][Verbinden Ihrer App mit dem Notification Hub]
4.  [Ausführen der App mit dem Emulator][Ausführen der App mit dem Emulator]
5.  [Senden von Benachrichtigungen von Ihrem Back-End aus][Senden von Benachrichtigungen von Ihrem Back-End aus]

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Xamarin.Android][1]
-   Ein aktives Google-Konto
-   [Azure Mobile Services-Komponente][Azure Mobile Services-Komponente]
-   [Google Cloud Messaging-Komponente][Google Cloud Messaging-Komponente]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Xamarin.Android-Apps.

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschlie&szlig;en. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

## <a name="register"></a><span class="short-header">Aktivieren von Google Cloud Messaging</span>Aktivieren von Google Cloud Messaging

<div class="dev-callout"><b>Hinweis</b>
<p>Sie ben&ouml;tigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschlie&szlig;en. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.</p>
</div>

1.  Navigieren Sie zur [Google APIs][Google APIs]-Website, melden Sie sich mit Ihren Google-Anmeldeinformationen an, und klicken Sie auf **Create project**.

    ![][0]

    <div class="dev-callout"><b>Hinweis</b>
<p>Wenn Sie bereits &uuml;ber ein Projekt verf&uuml;gen, werden Sie nach der Anmeldung zur Seite <strong>Dashboard</strong> weitergeleitet. Wenn Sie &uuml;ber das Dashboard ein neues Projekt erstellen m&ouml;chten, erweitern Sie <strong>API Project</strong>, klicken Sie unter <strong>Other projects</strong> auf <strong>Create...</strong>, geben Sie einen Projektnamen ein, und klicken Sie auf <strong>Create project</strong>.</p>
</div>

2.  Klicken Sie in der linken Spalte auf **Overview**, und notieren Sie sich die Projektnummer im **Dashboard**-Bereich.

    Später im Lernprogramm legen Sie diesen Wert als die Variable "PROJECT\_ID" im Client fest.

3.  Klicken Sie auf der Seite [Google apis][Google APIs] auf **Services**, und klicken Sie auf den Schalter, um **Google Cloud Messaging for Android** zu aktivieren. Akzeptieren Sie die Nutzungsbedingungen.

4.  Klicken Sie auf **API Access** und dann auf **Create new Server key...**

    ![][2]

5.  Klicken Sie unter **Configure Server Key for API Project** auf **Create**.

    ![][3]

6.  Notieren Sie sich den Wert **API key**.

    ![][4]

Zunächst verwenden Sie diesen API-Schlüsselwert, um den Notification Hub für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

## <a name="configure-hub"></a><span class="short-header">Konfigurieren Ihres Notification Hub</span>Konfigurieren Ihres Notification Hub

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

    ![][5]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

    ![][6]

4.  Klicken Sie auf den gerade erstellten Namespace (normalerweise ***Name des Notification Hub*-ns**), und klicken Sie dann oben auf **Configure**.

    ![][7]

5.  Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

    ![][8]

6.  Klicken Sie auf die Registerkarte **Configure**, geben Sie den Wert **API Key** ein, den Sie im vorherigen Abschnitt erhalten haben, und klicken Sie auf **Save**.

    ![][9]

7.  Wählen Sie oben die Registerkarte **Dashboard** aus und klicken Sie auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

    ![][10]

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App zu registrieren und Pushbenachrichtigungen zu versenden.

## <a name="connecting-app"></a><span class="short-header">Verbinden Ihrer App</span>Verbinden Ihrer App mit dem Notification Hub

### Erstellen eines neuen Projekts

1.  Erstellen Sie in Xamarin Studio (oder Visual Studio) ein neues Android-Projekt (File, New, Solution, Android Application).

    ![][11]
    ![][12]

2.  Klicken Sie in der Lösungsansicht mit der rechten Maustaste auf Ihr neues Projekt und wählen Sie **Options** aus, um die Projekteingenschaften zu öffnen. Wählen Sie das Element **Android Application** im Bereich **Build** aus.

    ![][13]

3.  Wählen Sie unter **Minimum Android version** den Eintrag API Level 8 aus.

4.  Wählen Sie unter **Target Android version** Ihre gewünschte Zielversion der API aus (API Level 8 oder höher).

5.  Ihr **Package name** muss mit einem Kleinbuchstaben beginnen.

    <div class="dev-callout"><b>Hinweis</b>
    <p>Der erste Buchstabe im Paketnamen muss ein Kleinbuchstabe sein. Ansonsten erhalten Sie Anwendungsmanifest-Fehler, wenn Sie Ihre **BroadcastReceiver** und **IntentFilter** weiter unten f&uuml;r Pushbenachrichtigungen registrieren.</p>
    </div>

### Hinzufügen des Google Cloud Messaging Client zum Projekt

Der im Xamarin Component Store erhältliche Google Cloud Messaging Client vereinfacht das Vorgehen bei der Bereitstellung von Pushbenachrichtigungen in Xamarin.Android.

1.  Klicken Sie in der Xamarin.Android-App mit der rechten Maustaste auf den Ordner "Components", und wählen Sie **Get More Components...** aus.

2.  Suchen Sie nach der Komponente **Google Cloud Messaging Client**.

3.  Erweitern Sie die Xamarin.Android-App um diese Komponente. Die erforderlichen Assemblyverweise werden automatisch hinzugefügt.

### Hinzufügen von Xamarin.NotificationHub zum Projekt

Diese Assembly vereinfacht die Registrierung bei Azure Notification Hubs. Sie kann anhand der unten stehenden Anweisungen heruntergeladen oder aus dem [Beispieldownload][NotificationHubs-Beispielapp] abgerufen werden.

1.  Öffnen Sie die [Xamarin.NotificationHub][Xamarin.NotificationHub] Github-Seite, laden Sie den Quellordner herunter und kompilieren Sie die Quellen.

2.  Erstellen Sie im Xamarin.Android-Projektordner einen Ordner namens \*\*\_external\*\*, und kopieren Sie die kompilierte **ByteSmith.WindowsAzure.Messaging.Android.dll** in diesen Ordner.

3.  Öffnen Sie Ihr Xamarin.Android-Projekt in Xamarin Studio (oder Visual Studio).

4.  Klicken Sie mit der rechten Maustaste auf den Ordner **References**, und wählen Sie **Edit References...**

5.  Navigieren Sie auf der Registerkarte **.Net Assembly** zum Ordner \*\*\_external\*\* des Projekts, wählen Sie die zuvor erstellte **ByteSmith.WindowsAzure.Messaging.Android.dll** aus, und klicken Sie auf **Add**. Klicken Sie auf OK, um das Dialogfenster zu schließen.

### Einrichten von Notification Hubs in Ihrem Projekt

1.  Erstellen Sie eine Klasse **Constants.cs** und definieren Sie die folgenden konstanten Werte (Ersetzen Sie die Platzhalter durch die entsprechenden Werte):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2.  Fügen Sie die folgenden using-Anweisungen zu **MainActivity.cs** hinzu:

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

3.  Fügen Sie der **MainActivity**-Klasse die folgende Methode hinzu:

        private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4.  Erstellen Sie eine neue Klasse **MyBroadcastReceiver**.

    <div class="dev-callout"><b>Hinweis</b>
<p>Die Erstellung eines **BroadcastReceivers** wird unten von Grund auf besprochen. Eine schnelle Alternative zur manuellen Erstellung von **MyBroadcastReceiver.cs** ist jedoch ein Verweis auf die Datei **GcmService.cs** im Xamarin.Android-Beispielprojekt auf GitHub. Sie k&ouml;nnen f&uuml;r den Anfang auch **GcmService.cs** duplizieren und die Klassennamen &auml;ndern.</p>
</div>

5.  Fügen Sie die folgenden using-Anweisungen in **MyBroadcastReceiver.cs** ein (mit Verweis auf die zuvor hinzugefügte Komponente und Assembly):

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

6.  Fügen Sie die folgenden Berechtigungsanforderungen zwischen den **using**-Anweisungen und der **namespace**-Deklaration hinzu:

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  Ändern Sie in **MyBroadcastReceiver.cs** die Methode **MyBroadcastReceiver** auf folgende Weise:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

8.  Fügen Sie eine weitere Klasse in **MyBroadcastReceiver.cs** mit dem Namen **PushHandlerService** hinzu, die von **PushHandlerServiceBase** abgeleitet wird. Verwenden Sie unbedingt die **Service**-Direktive in der Klasse:

        [Service] //Must use the service tag
        public class GcmService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public GcmService() : base(Constants.SenderID) 
            {
                Log.Info(MyBroadcastReceiver.TAG, "GcmService() constructor"); 
            }
        }

9.  **GcmServiceBase** implementiert die Methoden **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** und **OnError()**. Die Implementierungsklasse **GcmService** muss diese Methoden überschreiben, die als Reaktion auf die Interaktion mit dem Notification Hub aufgerufen werden.

10. Überschreiben Sie die Methode **OnRegistered()** in **PushHandlerService** mit dem folgenden Code:

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                await Hub.UnregisterAllAsync(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message); 
                Debugger.Break();
            }
        }

    <div class="dev-callout"><b>Hinweis</b>
<p>Beachten Sie im **OnRegistered()**-Code die M&ouml;glichkeit, Tags f&uuml;r bestimmte Nachrichtenkan&auml;le zu registrieren.</p>
</div>

11. Überschreiben Sie die Methode **OnMessage** in **PushHandlerService** mit dem folgenden Code:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

12. Fügen Sie die folgende **createNotification**-Methode zu **PushHandlerService**, um Benutzer wie oben gezeigt zu benachrichtigen:

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

13. Überschreiben Sie die abstrakten Mitglieder **OnUnRegistered()**, **OnRecoverableError()** und **OnError()**, um Ihren Code kompilieren zu können.

## <a name="run-app"></a><span class="short-header">Ausführen der App</span>Ausführen der neuen App im Emulator

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1.  Klicken Sie unter **Tools** auf **Open Android Emulator Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Edit**.

    ![][14]

2.  Wählen Sie **Google APIs** unter **Ziel** aus, und klicken Sie auf **OK**.

    ![][15]

3.  Klicken Sie in der oberen Symbolleiste auf **Run**, und wählen Sie Ihre App aus. Daraufhin wird der Emulator gestartet und die App ausgeführt.

4.  Die App ruft die *registrationId* von GCM ab und registriert sich beim Notification Hub.

    <div class="dev-callout"><b>Hinweis</b>
<p>Um Pushbenachrichtigungen zu erhalten, m&uuml;ssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (Navigieren Sie im Emulator zu **Settings**, und klicken Sie auf **Add Account**). &Uuml;berpr&uuml;fen Sie zudem, dass der Emulator mit dem Internet verbunden ist.</p>
</div>

## <a name="send"></a><span class="short-header">Senden einer Benachrichtigung</span>Senden einer Benachrichtigung von Ihrem Back-End aus

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die [REST-Schnittstelle][REST-Schnittstelle] senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem Mobile Service, der ein Knotenskript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1.  Erstellen einer neuen Visual C#-Konsolenanwendung:

    ![][16]

2.  Fügen Sie mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets][WindowsAzure.ServiceBus NuGet-Pakets] eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Package Manager Console**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

        Install-Package WindowsAzure.ServiceBus

    und drücken Sie die Eingabetaste.

3.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

4.  Fügen Sie in der Klasse `Program` die folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5.  Fügen Sie dann folgende Zeilen zur Main-Methode hinzu:

         SendNotificationAsync();
         Console.ReadLine();

6.  Drücken Sie die Taste F5, um die App auszuführen. Sie sollten dann eine Popupbenachrichtigung erhalten.

    ![][17]

Zum Senden einer Benachrichtigung mit Mobile Service folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services]. Gehen Sie dann wie folgt vor:

1.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und wählen Sie Ihren Mobile Service aus.

2.  Wählen Sie oben die Registerkarte **Scheduler**.

    ![][18]

3.  Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.

    ![][19]

4.  Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend in der Kopfleiste auf die Registerkarte **Script**.

5.  Fügen Sie das folgende Skript in Ihre Scheduler-Funktion ein. Stellen Sie sicher, dass Sie die Platzhalter durch den Notification Hub-Namen und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* ersetzen, die Sie zuvor erhalten haben. Klicken Sie auf **Speichern**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6.  Klicken Sie in der unteren Leiste auf **Run Once**. Sie sollten dann eine Popupbenachrichtigung erhalten.

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Android-Geräte versendet. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten][Verwenden von Notification Hubs zum Übermitteln von Nachrichten] lesen. Mehr zur Verwendung von Notification Hubs erfahren Sie im [Notification Hubs-Leitfaden][Notification Hubs-Leitfaden] und unter [Notification Hubs für Android][Notification Hubs für Android].

 
 


  [Xamarin.Android]: /de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [NotificationHubs-Beispielapp]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Aktivieren von Google Cloud Messaging]: #register
  [Konfigurieren Ihres Notification Hub]: #configure-hub
  [Verbinden Ihrer App mit dem Notification Hub]: #connecting-app
  [Ausführen der App mit dem Emulator]: #run-app
  [Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
  [1]: http://xamarin.com/download/
  [Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
  [Google Cloud Messaging-Komponente]: http://components.xamarin.com/view/GCMClient/
  [Google APIs]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  [0]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
  [2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
  [3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
  [4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [5]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [6]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
  [10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png
  [11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
  [12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
  [13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
  [Xamarin.NotificationHub]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
  [14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [REST-Schnittstelle]: http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx
  [16]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet-Pakets]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [17]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
  [18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
  [19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png
  [Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users-aspnet
  [Verwenden von Notification Hubs zum Übermitteln von Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
  [Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
  [Notification Hubs für Android]: http://msdn.microsoft.com/de-de/library/dn282661.aspx
