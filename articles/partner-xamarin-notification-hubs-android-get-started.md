<properties urlDisplayName="" pageTitle="Erste Schritte mit Notification Hubs für Xamarin.Android-Apps" metaKeywords="" description="Erfahren Sie, wie Sie Notification Hubs verwenden, um Pushbenachrichtigungen an eine Xamarin Android-Anwendung zu senden." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.Android-App senden. 
In diesem Lernprogramm erstellen Sie eine leere Xamarin.Android-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der komplette Code ist verfügbar in der [NotificationHubs-App(][GitHub]-Beispiel).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Aktivieren von Google Cloud Messaging]
2. [Konfigurieren Ihres Notification Hub]
3. [Verbinden Ihrer App mit dem Notification Hub]
4. [Ausführen der App mit dem Emulator]
5. [Senden von Benachrichtigungen von Ihrem Back-End aus]

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Xamarin.Android]
+ Ein aktives Google-Konto
+ [Azure Mobile Services-Komponente]
+ [Google Cloud Messaging-Komponente]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Xamarin.Android-Apps. 

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

<h2><a name="register"></a>Aktivieren von Google Cloud Messaging</h2>

<p></p>

<div class="dev-callout"><b>Hinweis</b>
<p>Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Um ein neues Google-Konto zu erstellen, besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Navigieren Sie zur <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a>-Website, melden Sie sich mit Ihren Google-Anmeldeinformationen an, und klicken Sie auf **Create project...**.

   	![][1]   
	
	<div class="dev-callout"><b>Hinweis</b>
	<p>Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite <strong>Dashboard</strong> weitergeleitet. 	Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie <strong>API Project</strong>, klicken Sie unter <strong>Other projects</strong> auf <strong>Create...</strong>, geben Sie einen Projektnamen ein, und klicken Sie auf <strong>Create project</strong>.</p>
    </div>

2. Klicken Sie in der linken Spalte auf **Overview**, und notieren Sie sich die Projektnummer im **Dashboard**-Bereich. 

	Später im Lernprogramm legen Sie diesen Wert als die Variable "PROJECT_ID" im Client fest.

3. Klicken Sie auf der Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> auf **Services**, und klicken Sie auf den Schalter, um **Google Cloud Messaging for Android** zu aktivieren. Akzeptieren Sie die Nutzungsbedingungen. 

4. Klicken Sie auf **API Access** und dann auf **Create new Server key...** 

   	![][2]

5. Klicken Sie unter **Configure Server Key for API Project** auf **Create**.

   	![][3]

6. Notieren Sie sich den Wert **API key**.

   	![][4] 

Zunächst verwenden Sie diesen API-Schlüsselwert, um den Notification Hub für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

<h2><a name="configure-hub"></a>Konfigurieren Ihres Notification Hub</h2>

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste** dann auf **Servicebus** dann auf **Benachrichtigungshub** und dann auf **Schnellerfassung**.

   	![][7]

3. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub** (Neuen Notification Hub erstellen).

   	![][8]

4. Klicken Sie auf den gerade erstellten Namespace (normalerweise ***Name des Notification Hub*-ns**), und klicken Sie dann oben auf **Configure**.

   	![][9]

5. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

   	![][10]

6. Klicken Sie auf die Registerkarte **Configure** (Konfigurieren), geben Sie den Wert **API Key** (API-Schlüssel) ein, den Sie im vorherigen Abschnitt erhalten haben, und klicken Sie auf **Save** (Speichern).

   	![][11]

7. Klicken Sie oben auf die Registerkarte **Dashboard**, und klicken Sie danach auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][12]

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App zu registrieren und Pushbenachrichtigungen zu versenden.

<h2><a name="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub</h2>

### Erstellen eines neuen Projekts

1. Erstellen Sie in Xamarin Studio (oder Visual Studio) ein neues Android-Projekt (File, New, Solution, Android Application).

   	![][13]   
   	![][14]

2. Klicken Sie in der Lösungsansicht mit der rechten Maustaste auf Ihr neues Projekt und wählen Sie **Options** aus, um die Projekteigenschaften zu öffnen. Wählen Sie das Element **Android Application** im Bereich **Build** aus.

   	![][15]

3. Wählen Sie unter **Minimum Android version** den Eintrag API Level 8 aus.

4. Wählen Sie unter **Target Android version** Ihre gewünschte Zielversion der API aus (API Level 8 oder höher).

5. Ihr **Package name** muss mit einem Kleinbuchstaben beginnen.

	<div class="dev-callout"><b>Hinweis</b>
    <p>Der erste Buchstabe im Paketnamen muss ein Kleinbuchstabe sein. Andernfalls werden bei der Registrierung von **BroadcastReceiver** und **IntentFilter** für Pushbenachrichtigungen Anwendungsmanifestfehler ausgegeben.</p>
    </div> 

### Hinzufügen des Google Cloud Messaging Client zum Projekt

Der im Xamarin Component Store erhältliche Google Cloud Messaging Client vereinfacht das Vorgehen bei der Bereitstellung von Pushbenachrichtigungen in Xamarin.Android.

1. Klicken Sie in der Xamarin.Android-App mit der rechten Maustaste auf den Ordner "Components", und wählen Sie **Get More Components...** aus.

2. Suchen Sie nach der Komponente **Google Cloud Messaging Client**.

3. Erweitern Sie die Xamarin.Android-App um diese Komponente. Die erforderlichen Assemblyverweise werden automatisch hinzugefügt.

### Hinzufügen von Xamarin.NotificationHub zum Projekt

Diese Assembly vereinfacht die Registrierung bei Azure Notification Hubs. Sie kann anhand der unten stehenden Anweisungen heruntergeladen oder aus dem [Beispieldownload][GitHub] abgerufen werden.

1. Öffnen Sie die [Xamarin.NotificationHub]Github-Seite, laden Sie den Quellordner herunter und kompilieren Sie die Quellen.

2. Erstellen Sie im Xamarin.Android-Projektordner einen Ordner namens **_external**, und kopieren Sie die kompilierte **ByteSmith.WindowsAzure.Messaging.Android.dll** in diesen Ordner.

3. Öffnen Sie Ihr Xamarin.Android-Projekt in Xamarin Studio (oder Visual Studio).

4. Klicken Sie mit der rechten Maustaste auf den Ordner **References**, und wählen Sie **Edit References...**

5. Navigieren Sie zur Registerkarte **.Net Assembly**, navigieren Sie zum Ordner **_external** in Ihrem Projekt, wählen Sie die zuvor erstellte **ByteSmith.WindowsAzure.Messaging.Android.dll**, und klicken Sie auf **Add**. Klicken Sie auf OK, um das Dialogfenster zu schließen. 

### Einrichten von Notification Hubs in Ihrem Projekt

1. Erstellen Sie eine Klasse **Constants.cs** und definieren Sie die folgenden konstanten Werte (Ersetzen Sie die Platzhalter durch die entsprechenden Werte):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. Fügen Sie die folgenden using-Anweisungen zu **MainActivity.cs** hinzu:

		using ByteSmith.WindowsAzure.Messaging;
		using Gcm.Client;

3. Erstellen Sie in der Klasse **MainActivity** die folgende Methode:

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Erstellen Sie eine neue Klasse **MyBroadcastReceiver**.

	<div class="dev-callout"><b>Hinweis</b>
    <p>Die Erstellung eines **BroadcastReceiver** wird von Grund auf besprochen. Eine schnelle Alternative zur manuellen Erstellung von **MyBroadcastReceiver.cs** ist jedoch ein Verweis auf die Datei **GcmService.cs** im Xamarin.Android-Beispielprojekt auf GitHub. Sie können für den Anfang auch **GcmService.cs** duplizieren und die Klassennamen ändern.</p>
    </div> 

5. Fügen Sie die folgenden using-Anweisungen in **MyBroadcastReceiver.cs** ein (mit Verweis auf die zuvor hinzugefügte Komponente und Assembly):

		using ByteSmith.WindowsAzure.Messaging;
		using Gcm.Client;

5. Fügen Sie die folgenden Berechtigungsanforderungen zwischen den **using**-Anweisungen und der **namespace**-Deklaration hinzu:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. In **MyBroadcastReceiver.cs** change the **MyBroadcastReceiver** class to match the following:

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }
        
7. Add another class in **MyBroadcastReceiver.cs** named **PushHandlerService** which derives from **PushHandlerServiceBase**. Make sure to use the **Service** directive on the class:

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


8. **GcmServiceBase** implementiert die Methoden **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** und **OnError()**. Die Implementierungsklasse **GcmService** muss diese Methoden überschreiben, die als Reaktion auf die Interaktion mit dem Notification Hub aufgerufen werden.

9. Überschreiben Sie die Methode **OnRegistered()** in **PushHandlerService** mit dem folgenden Code:

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
    <p>Beachten Sie im **OnRegistered()**-Code die Möglichkeit, Tags für bestimmte Nachrichtenkanäle zu registrieren.</p>
    </div> 
    
10. Überschreiben Sie die Methode **OnMessage** in **PushHandlerService** mit dem folgenden Code:

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

11. Fügen Sie die folgende **createNotification**-Methode zu **PushHandlerService** hinzu, um Benutzer wie oben gezeigt zu benachrichtigen:

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
        
12. Überschreiben Sie die abstrakten Mitglieder **OnUnRegistered()**, **OnRecoverableError()** und **OnError()**, um Ihren Code kompilieren zu können.


<h2><a name="run-app"></a>Ausführen der App im Emulator</h2>

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1. Klicken Sie unter **Tools** auf **Open Android Emulator Manager**, wählen Sie Ihr Gerät, und kicken Sie dann auf **Edit**.

   	![][18]

2. Wählen Sie **Google APIs** unter **Target** aus, und klicken Sie dann auf **OK**.

   	![][19]

3. Klicken Sie in der oberen Symbolleiste auf **Ausführen**, und wählen Sie die App aus. Daraufhin wird der Emulator gestartet und die App ausgeführt.

4. Die App ruft die *registrationId* von GCM ab und registriert sich beim Notification Hub.

	<div class="dev-callout"><b>Hinweis</b>
    <p>Damit Sie Pushbenachrichtigungen erhalten, müssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (Navigieren Sie im Emulator zu **Einstellungen**, und klicken Sie auf **Konto hinzufügen**). Überprüfen Sie außerdem, ob der Emulator mit dem Internet verbunden ist.</p>
    </div> 

<h2><a name="send"></a>Senden von Benachrichtigungen vom Back-End aus</h2>

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem Mobile Service, der ein Knotenskript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1. Erstellen einer neuen Visual C#-Konsolenanwendung: 

   	![][20]

2. Fügen Sie mithilfe des <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Pakets</a> eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Package Manager Console**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

        Install-Package WindowsAzure.ServiceBus

    und drücken Sie die Eingabetaste.

2. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

3. Fügen Sie in der Klasse "Program" die folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

4. Fügen Sie dann folgende Zeilen zur Main-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

5. Drücken Sie die Taste F5, um die App auszuführen. Sie sollten dann eine Popupbenachrichtigung erhalten.

   	![][21]

Zum Senden einer Benachrichtigung mit Mobile Service folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services]. Gehen Sie dann wie folgt vor:

1. Melden Sie sich am [Azure-Verwaltungsportal] an, und wählen Sie Ihren Mobile Service aus.

2. Wählen Sie oben die Registerkarte **Scheduler**.

   	![][22]

3. Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.

   	![][23]

4. Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend in der Kopfleiste auf die Registerkarte **Script**.

5. Fügen Sie das folgende Skript in Ihre Scheduler-Funktion ein. Stellen Sie sicher, dass Sie die Platzhalter durch den Notification Hub-Namen und die Verbindungszeichenfolge für "DefaultFullSharedAccessSignature ersetzen", die Sie zuvor erhalten haben. Klicken Sie auf **Speichern**.

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

6. Klicken Sie in der unteren Leiste auf **Run Once**. Sie sollten dann eine Popupbenachrichtigung erhalten.

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Android-Geräte versendet. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten] lesen. Mehr zur Verwendung von Notification Hubs erfahren Sie im [Notification Hubs-Leitfaden] und unter [Notification Hubs - Anleitungen für Android].

<!-- Anchors. -->
[Aktivieren von Google Cloud Messaging]: #register
[Konfigurieren Ihres Notification Hub]: #configure-hub
[Verbinden Ihrer App mit dem Notification Hub]: #connecting-app
[Ausführen der App mit dem Emulator]: #run-app
[Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
[Nächste Schritte]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- URLs. -->
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[wns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
[Vorgehensweise zu Notification Hubs für iOS]: http://msdn.microsoft.com/de-de/library/dn282661.aspx

[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
[GCMClient-Komponentenseite]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub Github-Seite]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
[Google Cloud Messaging-Komponente]: http://components.xamarin.com/view/GCMClient/
