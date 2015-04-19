<properties 
	pageTitle="Erste Schritte mit Notification Hubs für Xamarin.Android-Apps" 
	description="Erfahren Sie, wie Sie Azure Notification Hubs verwenden, um Pushbenachrichtigungen an eine Xamarin Android-Anwendung zu senden. 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.Android-App senden. 
In diesem Lernprogramm erstellen Sie eine leere Xamarin.Android-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der fertige Code ist im Beispiel zu [NotificationHubs-App][GitHub] verfügbar.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Aktivieren von Google Cloud Messaging]
2. [Konfigurieren des Notification Hubs]
3. [Verbinden der App mit dem Notification Hub]
4. [Ausführen der App mit dem Emulator]
5. [Senden von Benachrichtigungen vom Back-End aus]

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Xamarin.Android]
+ Ein aktives Google-Konto
+ [Azure Mobile Services-Komponente]
+ [Google Cloud Messaging-Komponente]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Xamarin.Android-Apps. 

> [AZURE.IMPORTANT] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F"%20target="_blank).

<h2><a name="register"></a>Aktivieren von Google Cloud Messaging</h2>

<p></p>

> [AZURE.IMPORTANT] Sie benötigen ein Google-Konto mit bestätigter E-Mail-Adresse, um das in diesem Thema beschriebenen Verfahren abzuschließen. Um ein neues Google-Konto zu erstellen, wechseln Sie zu [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302"%20target="_blank). 

1. Navigieren Sie zur <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google APIs</a>-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, und klicken Sie auf **Create project**.

   	![][1]   
	
	> [AZURE.NOTE] Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite **Dashboard** weitergeleitet. Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie **API Project**, klicken Sie unter **Other projects** auf **Create...**, geben Sie einen Projektnamen ein, und klicken Sie auf **Create project**.

2. Klicken Sie in der linken Spalte auf **Overview**, und notieren Sie sich die Projektnummer im Abschnitt **Dashboard**. 

	Zu einem späteren Zeitpunkt im Lernprogramm legen Sie diesen Wert im Client als PROJECT_ID-Variable fest.

3. Klicken Sie auf der Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> auf **Services**, klicken Sie dann auf die Umschaltfläche, um **Google Cloud Messaging for Android** zu aktivieren, und akzeptieren Sie die Nutzungsbedingungen. 

4. Klicken Sie auf **API Access** und dann auf **Create new Server key...**. 

   	![][2]

5. Klicken Sie unter **Configure Server Key for API Project** auf **Create**.

   	![][3]

6. Notieren Sie sich den Wert von **API key**.

   	![][4] 

Zunächst verwenden Sie diesen API-Schlüsselwert, um den Notification Hub für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

<h2><a name="configure-hub"></a>Konfigurieren des Notification Hubs</h2>

1. Melden Sie sich im [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEU**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und dann auf **Schnellerfassung**.

   	![][7]

3. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Neuen Notification Hub erstellen**.

   	![][8]

4. Klicken Sie auf den soeben erstellten Namespace (normalerweise ***Notification Hub-Name*-ns**), und klicken Sie dann oben auf **Konfigurieren**.

   	![][9]

5. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

   	![][10]

6. Klicken Sie oben auf die Registerkarte **Konfigurieren**, geben Sie unter **API-Schlüssel** den Wert aus dem vorherigen Abschnitt ein, und klicken Sie auf **Speichern**.

   	![][11]

7. Wählen Sie oben die Registerkarte **Dashboard** aus, und klicken Sie auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][12]

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App zu registrieren und Pushbenachrichtigungen zu versenden.

<h2><a name="connecting-app"></a>Verbinden der App mit dem Notification Hub</h2>

### Erstellen eines neuen Projekts

1. Erstellen Sie in Xamarin Studio (oder Visual Studio) ein neues Android-Projekt (File, New, Solution, Android Application).

   	![][13]   
   	![][14]

2. Klicken Sie in der Projektmappenansicht mit der rechten Maustaste auf Ihr neues Projekt, und wählen Sie **Options** aus, um die Projekteigenschaften zu öffnen. Wählen Sie im Abschnitt **Build** das Element **Android Application** aus.

   	![][15]

3. Wählen Sie unter **Minimum Android version** den Eintrag "API Level 8" aus.

4. Legen Sie **Target Android version** auf Ihre gewünschte API-Zielversion fest (mindestens "API Level 8").

5. **Package name** muss mit einem Kleinbuchstaben beginnen.

	> [AZURE.IMPORTANT] Der Paketname muss mit einem ein Kleinbuchstaben beginnen. Andernfalls erhalten Sie beim Registrieren von **BroadcastReceiver** und **IntentFilter** für Pushbenachrichtigungen unten Anwendungsmanifest-Fehler.

### Hinzufügen des Google Cloud Messaging Client zum Projekt

Der im Xamarin Component Store erhältliche Google Cloud Messaging Client vereinfacht das Vorgehen bei der Bereitstellung von Pushbenachrichtigungen in Xamarin.Android.

1. Klicken Sie in der Xamarin.Android-App mit der rechten Maustaste auf den Ordner "Components", und wählen Sie **Get More Components...** aus.

2. Suchen Sie die Komponente **Google Cloud Messaging Client**.

3. Erweitern Sie die Xamarin.Android-App um diese Komponente. Die erforderlichen Assemblyverweise werden automatisch hinzugefügt.

### Hinzufügen von Xamarin.NotificationHub zum Projekt

Diese Assembly vereinfacht die Registrierung bei Azure Notification Hubs. Sie kann anhand der unten stehenden Anweisungen heruntergeladen oder aus dem [Beispieldownload][GitHub] abgerufen werden.

1. Öffnen Sie die [Xamarin.NotificationHub Github-Seite], laden Sie den Quellordner herunter, und kompilieren Sie die Quellen.

2. Erstellen Sie im Xamarin.Android-Projektordner einen Ordner **_external**, und kopieren Sie dann die kompilierte **ByteSmith.WindowsAzure.Messaging.Android.dll** in den Ordner.

3. Öffnen Sie Ihr Xamarin.Android-Projekt in Xamarin Studio (oder Visual Studio).

4. Klicken Sie mit der rechten Maustaste auf den Projektordner **References**, und wählen Sie **Edit References...**.

5. Wechseln Sie zur Registerkarte **.Net Assembly**, navigieren Sie in Ihrem Projekt zum Ordner **_external**, wählen Sie die zuvor erstellte **ByteSmith.WindowsAzure.Messaging.Android.dll** aus, und klicken Sie auf **Add**. Klicken Sie auf OK, um das Dialogfenster zu schließen. 

### Einrichten von Notification Hubs in Ihrem Projekt

1. Erstellen Sie eine **Constants.cs**-Klasse, und definieren Sie die folgenden Konstantenwerte (ersetzen Sie die Platzhalter durch Werte):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. Fügen Sie **MainActivity.cs** die folgenden using-Anweisungen hinzu:

		using ByteSmith.WindowsAzure.Messaging;
		using Gcm.Client;

3. Erstellen Sie in der **MainActivity**-Klasse die folgende Methode:

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Erstellen Sie eine neue **MyBroadcastReceiver**-Klasse.

	> [AZURE.NOTE] Die grundlegende Erstellung von **BroadcastReceiver** wird unten ausführlich erläutert. Eine schnelle Alternative zur manuellen Erstellung von **MyBroadcastReceiver.cs** besteht jedoch darin, auf die Datei **GcmService.cs** im Xamarin.Android-Beispielprojekt auf GitHub zu verweisen. Sie können für den Anfang auch einfach **GcmService.cs** duplizieren und die Klassennamen ändern.

5. Fügen Sie die folgenden using-Anweisungen in **MyBroadcastReceiver.cs** ein (mit Verweisen auf die zuvor hinzugefügte Komponente und Assembly):

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

6. Ändern Sie in **MyBroadcastReceiver.cs** die **MyBroadcastReceiver**-Klasse wie folgt:

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }
        
7. Fügen Sie in **MyBroadcastReceiver.cs** eine weitere Klasse mit dem Namen **PushHandlerService** hinzu, die von **PushHandlerServiceBase** abgeleitet wird. Verwenden Sie unbedingt die **Service**-Direktive für die Klasse:

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


8. **GcmServiceBase** implementiert die Methoden **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** und **OnError()**. Die Implementierungsklasse **GcmService** muss diese Methoden überschreiben, die als Reaktion auf die Interaktion mit dem Notification Hub ausgelöst werden.

9. Überschreiben Sie die **OnRegistered()**-Methode in **PushHandlerService** mit dem folgenden Code:

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

	> [AZURE.NOTE] Beachten Sie im **OnRegistered()**-Code oben die Möglichkeit, Tags für die Registrierung bestimmter Nachrichtenkanäle anzugeben.
    
10. Überschreiben Sie die **OnMessage**-Methode in **PushHandlerService** mit dem folgenden Code:

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

11. Add the following **createNotification** method to **PushHandlerService** for notifying users as used above:

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
        
12. Überschreiben Sie die abstrakten Member **OnUnRegistered()**, **OnRecoverableError()** und **OnError()**, um Ihren Code kompilieren zu können.


<h2><a name="run-app"></a>Ausführen der App im Emulator</h2>

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1. Klicken Sie unter **Tools** auf **Open Android Emulator Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Edit**.

   	![][18]

2. Wählen Sie in **Target** die Option **Google APIs** aus, und klicken Sie dann auf **OK**.

   	![][19]

3. Klicken Sie in der oberen Symbolleiste auf **Run**, und wählen Sie Ihre App aus. Daraufhin wird der Emulator gestartet und die App ausgeführt.

4. Die App ruft *registrationId* von GCM ab und registriert sich beim Notification Hub.

	> [AZURE.IMPORTANT] Um Pushbenachrichtigungen zu erhalten, müssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (navigieren Sie im Emulator zu **Settings**, und klicken Sie auf **Add Account**). Überprüfen Sie zudem, ob der Emulator mit dem Internet verbunden ist.

<h2><a name="send"></a>Senden von Benachrichtigungen vom Back-End aus</h2>

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem Mobile Service, der ein Knotenskript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1. Erstellen einer neuen Visual C#-Konsolenanwendung: 

   	![][20]

2. Fügen Sie dem Azure Service Bus SDK mithilfe des <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Pakets</a> einen Verweis hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Bibliotheks-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

        Install-Package WindowsAzure.ServiceBus

    und drücken Sie die Eingabetaste.

2. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

3. Fügen Sie in der `Program`-Klasse die folgende Methode hinzu:

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

Zum Senden einer Benachrichtigung mit einem mobilen Dienst folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services]. Gehen Sie dann wie folgt vor:

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und wählen Sie Ihren mobilen Dienst aus.

2. Wählen Sie oben die Registerkarte **Scheduler**.

   	![][22]

3. Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und wählen Sie **Bedarfsgesteuert**.

   	![][23]

4. Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend auf der oberen Leiste auf die Registerkarte **Skript**.

5. Fügen Sie das folgende Skript in Ihre Scheduler-Funktion ein. Stellen Sie sicher, dass Sie die Platzhalter durch den Notification Hub-Namen und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* ersetzen, die Sie zuvor erhalten haben. Klicken Sie auf **Speichern**.

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

6. Klicken Sie auf der unteren Leiste auf **Einmal ausführen**. Sie sollten dann eine Popupbenachrichtigung erhalten.

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Android-Geräte versendet. Informationen zum Adressieren bestimmter Benutzer finden Sie im Lernprogramm [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] lesen. Mehr zur Verwendung von Notification Hubs erfahren Sie im [Notification Hubs-Leitfaden] und unter [Notification Hubs - Anleitungen für Android].

<!-- Anchors. -->
[Aktivieren von Google Cloud Messaging]: #register
[Konfigurieren des Notification Hubs]: #configure-hub
[Verbinden der App mit dem Notification Hub]: #connecting-app
[Ausführen der App mit dem Emulator]: #run-app
[Senden von Benachrichtigungen vom Back-End aus]: #send
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
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[wns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs - Anleitungen für Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
[GCMClient-Komponentenseite]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub Github-Seite]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
[Google Cloud Messaging-Komponente]: http://components.xamarin.com/view/GCMClient/

<!--HONumber=45--> 
