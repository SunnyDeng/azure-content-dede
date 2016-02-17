<properties 
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Ihrer Xamarin.Forms-App mit Azure App Service" 
	description="Erfahren Sie, wie Azure App Service zum Senden von Pushbenachrichtigungen an Ihre Xamarin.Forms-App verwendet wird." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="12/19/2015" 
	ms.author="wesmc"/>

# Hinzufügen von Pushbenachrichtigungen zur Xamarin Forms-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Übersicht

Dieses Tutorial baut auf dem [Xamarin.Forms-Schnellstart-Tutorial](app-service-mobile-xamarin-forms-get-started.md) auf, das Sie zuerst absolvieren müssen. Sie fügen Unterstützung für Pushbenachrichtigungen für jedes Projekt hinzu, das im Xamarin.Forms-Schnellstart-Projekt unterstützt werden soll. Bei jedem Einfügen eines Datensatzes wird eine Pushbenachrichtigung gesendet.

Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Pushbenachrichtigungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

Die [iOS-Simulator unterstützt keine Pushbenachrichtigungen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), daher müssen Sie ein physisches iSO-Gerät verwenden. Sie müssen sich außerdem für eine [Apple-Entwicklerprogramm-Mitgliedschaft](https://developer.apple.com/programs/ios/) anmelden.

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps. Diese können Sie auch nach Ablauf der Testversion weiter nutzen. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* Einen Mac, auf dem [Xamarin Studio] und [Xcode] ab Version 4.4 installiert sind. Sie können die Xamarin.Forms-App auf Wunsch mithilfe von Visual Studio auf Ihrem Windows-Computer ausführen. Allerdings ist dies etwas kompliziert, da Sie eine Verbindung zu einem vernetzten Mac benötigen, auf dem der Xamarin.iOS-Buildhost ausgeführt wird. Wenn Sie daran interessiert sind, finden Sie Informationen dazu unter [Installation von Xamarin.iOS auf Windows].

* Ein physisches iOS-Gerät. Pushbenachrichtigungen werden vom iOS-Simulator nicht unterstützt.

* Durchlaufen Sie das [Xamarin.Forms-Schnellstart-Tutorial](app-service-mobile-xamarin-forms-get-started.md).

##Erstellen eines Notification Hubs für Ihre mobile App

Um Ihre App für das Senden von Benachrichtigungen zu konfigurieren, erstellen Sie einen neuen Notification Hub und konfigurieren ihn für die Plattformbenachrichtigungsdienste, die Sie verwenden.

Mit diesen Schritten erstellen Sie einen neuen Notification Hub. Wenn Sie bereits einen Hub erstellt haben, können Sie ihn einfach auswählen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Klicken Sie auf **Durchsuchen** > **Mobile Apps** > Ihr Back-End > **Einstellungen** > **Mobil** > **Push** > **Notification Hub** > **+ Notification Hub**, und geben Sie einen Namen und einen Namespace für einen neuen Notification Hub ein. Klicken Sie anschließend auf die Schaltfläche **OK**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. Klicken Sie auf dem Blatt „Notification Hub erstellen“ auf **Erstellen**.


##Aktualisieren des Serverprojekts zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Optional) Konfigurieren und Ausführen des Android-Projekts

Dieser Abschnitt bezieht sich auf das Ausführen des Xamarin Android-Projekts für Android. Wenn Sie nicht mit Android-Geräten arbeiten, können Sie diesen Abschnitt überspringen.


####Aktivieren von Google Cloud Messaging (GCM)


[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


####Konfigurieren des Notification Hubs für GCM

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Klicken Sie auf **Durchsuchen** > **Mobile Apps** > Ihre mobile App > **Einstellungen** > **Push** > **Google (GCM)**. Fügen Sie den zuvor erstellten Server-API-Schlüssel ein und klicken Sie auf **Speichern**. Ihr Dienst ist jetzt so konfiguriert, dass er mit Pushbenachrichtigungen für Android funktioniert.

	![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


####Hinzufügen von Pushbenachrichtigungen zum Droid-Projekt

1. Klicken Sie mit der rechten Maustaste auf den Komponentenordner, klicken Sie auf „Get More Components...“, suchen Sie nach der Komponente **Google Cloud Messaging Client** und fügen Sie sie dem Projekt hinzu. Diese Komponente vereinfacht die Arbeit mit Pushbenachrichtigungen mit einem Xamarin Android-Projekt.

2. Öffnen Sie die Projektdatei „MainActivity.cs“, und fügen Sie am Anfang der Datei die folgende using-Anweisung hinzu:

		using Gcm.Client;

3. Fügen Sie nach dem Aufruf von `LoadApplication` den folgenden Code in die Methode `OnCreate` ein.

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);
	
	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Fügen Sie den folgenden Code für die `CreateAndShowDialog`-Hilfsmethode hinzu.

		private void CreateAndShowDialog(String message, String title) 
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Fügen Sie in der `MainActivity`-Klasse den folgenden Code ein, um das aktuelle `MainActivity`-Element verfügbar zu machen, sodass wir UI-Befehle im Haupt-UI-Thread ausführen können:
		
		// Create a new instance field for this activity.
		static MainActivity instance = null;
		
		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

6. Initialisieren Sie die `instance`-Variable am Anfang der `MainActivity.OnCreate`-Methode.

		// Set the current instance of MainActivity.
		instance = this;

7. Fügen Sie dem **Droid**-Projekt eine neue Klassendatei hinzu. Nennen Sie die neue Klassendatei **GcmService**.

8. Stellen Sie sicher, dass am Anfang der Datei die folgenden `using`-Anweisungen enthalten sind.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Android.App;
		using Android.Content;
		using System.Collections.Generic;
		using System.Diagnostics;
		using Android.Util;
		using Newtonsoft.Json.Linq;
		using System.Text;
		using System.Linq;

9. Fügen Sie am Anfang der Datei die folgenden Berechtigungsanforderungen nach den `using`-Anweisungen und vor der `namespace`-Deklaration ein.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
		
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. Fügen Sie dem Namespace die folgende Klassendefinition hinzu. Ersetzen Sie **<PROJECT_NUMBER>** durch die zuvor notierte Projektnummer.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]		
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{		
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };		
		}

11. Aktualisieren Sie Ihre `GcmService`-Klasse, sodass sie den neuen Übertragungsempfänger verwendet.

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }
		
		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Fügen Sie folgenden Code zur GcmService-Klasse hinzu, mit dem der OnRegistered-Ereignishandler überschrieben und eine `Register`-Methode implementiert wird.

	Dieser Code registriert einen Vorlagentext zum Empfangen von Vorlagenbenachrichtigungen mithilfe des `messageParam`-Parameters. Mithilfe von Vorlagenbenachrichtigungen können Sie plattformübergreifende Benachrichtigungen senden. Weitere Informationen finden Sie unter [Vorlagen](https://msdn.microsoft.com/library/azure/dn530748.aspx).
		
		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;
		
		    createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");
		
            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
		
		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
		
		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyGCM}
                };
                
                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

13. Sie müssen `OnMessage` implementieren, um eingehende Pushbenachrichtigungen verarbeiten zu können. In diesem Code verarbeiten wir die Benachrichtigung und senden sie an den Benachrichtigungs-Manager.

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");
		
		    var msg = new StringBuilder();
		
		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }
		
		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();
		
		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }
		
		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }
		
		    createNotification("Unknown message details", msg.ToString());
		}
		
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
	
14. Sie müssen darüber hinaus den `OnUnRegistered`- und den `OnError`-Handler für den Empfänger implementieren.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}



####Testen von Pushbenachrichtigungen in der Android-App

1. Klicken Sie in Visual Studio oder Xamarin Studio mit der rechten Maustaste auf das **droid**-Projekt und klicken Sie dann auf **Als Startprojekt festlegen**.
 
2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen. Starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren.
	
	> [AZURE.NOTE] Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen (**+**).

3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.





##(Optional) Konfigurieren und Ausführen des iOS-Projekts

Dieser Abschnitt bezieht sich auf das Ausführen des Xamarin iOS-Projekts für iOS-Geräte. Wenn Sie nicht mit iOS-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

[AZURE.INCLUDE [Notification Hubs Xamarin – Aktivieren von Apple-Pushbenachrichtigungen](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Konfigurieren des Notification Hubs für APNS

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Klicken Sie auf **Durchsuchen** > **Mobile Apps** > Ihre mobile App > **Einstellungen** > **Push** > **Apple (APNS)** > **Zertifikat hochladen**. Laden Sie die P12-Pushzertifikatsdatei hoch, die Sie zuvor exportiert haben. Stellen Sie sicher, dass **Sandkasten** ausgewählt ist, wenn Sie ein Pushzertifikat für Entwicklung und Tests erstellt haben. Wählen Sie andernfalls **Produktion**. Ihr Dienst ist jetzt so konfiguriert, dass er mit Pushbenachrichtigungen für iOS funktioniert.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	Im nächsten Schritt konfigurieren Sie die iOS-Projekteinstellung in Xamarin Studio oder Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Hinzufügen von Pushbenachrichtigungen zu Ihrer iOS-App

1. Fügen Sie am Anfang der Datei **AppDelegate.cs** die folgende `using`-Anweisung hinzu.

        using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;


2. Öffnen Sie im iOS-Projekt die Datei „AppDelegate.cs“ und aktualisieren Sie `FinishedLaunching` wie folgt, um Remotebenachrichtigungen zu unterstützen.

		public override bool FinishedLaunching (UIApplication app, NSDictionary options)
		{
			global::Xamarin.Forms.Forms.Init ();

			Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();

            // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
            // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
            //SQLitePCL.CurrentPlatform.Init();
            
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

			LoadApplication (new App ());

			return base.FinishedLaunching (app, options);
		}


4. Fügen Sie darüber hinaus in „AppDelegate.cs“ eine Überschreibung für das **RegisteredForRemoteNotifications**-Ereignis hinzu, um sich für Benachrichtigungen zu registrieren:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. Zudem fügen Sie in „AppDelegate.cs“ eine Überschreibung für das **DidReceivedRemoteNotification**-Ereignis hinzu, um während der Ausführung der App eingehende Benachrichtigungen zu verarbeiten:

        public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Ihre App kann Pushbenachrichtigungen nun unterstützen.

####Testen von Pushbenachrichtigungen in der iOS-App

1. Klicken Sie mit der rechten Maustaste auf das iOS-Projekt, und klicken Sie auf **Als Startprojekt festlegen**.

2. Klicken Sie in Visual Studio auf die Schaltfläche **Ausführen**, oder drücken Sie die Taste **F5**, um das Projekt zu erstellen, starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren.
	
	> [AZURE.NOTE] Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

3. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen (**+**).

4. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.




##(Optional) Konfigurieren und Ausführen des Windows-Projekts

Dieser Abschnitt bezieht sich auf das Ausführen des Xamarin WinApp-Projekts für Windows-Geräte. Wenn Sie nicht mit Windows-Geräten arbeiten, können Sie diesen Abschnitt überspringen.


####Registrieren der Windows-App für Pushbenachrichtigungen mit WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Konfigurieren des Notification Hubs für WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Hinzufügen von Pushbenachrichtigungen zu Ihrer Windows-App

1. Öffnen Sie in Visual Studio im **WinApp**-Projekt die Datei **App.xaml.cs**. Fügen Sie die folgenden `using`-Anweisungen ein:

		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using WesmcMobileAppGaTest;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. Fügen Sie in „App.xaml.cs“ die folgende `InitNotificationsAsync`-Methode hinzu. Diese Methode ruft den Pushbenachrichtigungskanal ab und registriert eine Vorlage für das Empfangen von Vorlagenbenachrichtigungen vom Notification Hub. Eine Vorlagenbenachrichtigung, die `messageParam` unterstützt, wird an diesen Client übermittelt.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyWNS},
                  {"headers", headers} // Only needed for WNS & MPNS
                };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
        }

3. Aktualisieren Sie in „App.xaml.cs“ den `OnLaunched`-Ereignishandler mit dem `async`-Attribut, und rufen Sie `InitNotificationsAsync` auf.

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            Frame rootFrame = Window.Current.Content as Frame;

            // Do not repeat app initialization when the Window already has content,
            // just ensure that the window is active
            if (rootFrame == null)
            {
                // Create a Frame to act as the navigation context and navigate to the first page
                rootFrame = new Frame();
                // Set the default language
                rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
                rootFrame.NavigationFailed += OnNavigationFailed;
                Xamarin.Forms.Forms.Init(e); 
                if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
                {
                    //TODO: Load state from previously suspended application
                }
                // Place the frame in the current Window
                Window.Current.Content = rootFrame;
            }

            if (rootFrame.Content == null)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(typeof(MainPage), e.Arguments);
            }
            // Ensure the current window is active
            Window.Current.Activate();

            await InitNotificationsAsync();
        }

4. Öffnen Sie im Projektmappen-Explorer für Visual Studio die Datei **Package.appxmanifest** und legen Sie unter **Benachrichtigungen** für **Toastfähig** die Option **Ja** fest.

5. Erstellen Sie die App, und stellen Sie sicher, dass keine Fehler auftreten. Sie sollten nun Ihre Client-App für die Vorlagenbenachrichtigungen vom Back-End für die mobile App registrieren.


####Testen von Pushbenachrichtigungen in Ihrer Windows-App

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das **WinApp**-Projekt und klicken Sie auf **Als Startprojekt festlegen**.


2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen. Starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren.
	
	> [AZURE.NOTE] Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

3. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen (**+**).

4. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.



<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installation von Xamarin.iOS auf Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0128_2016-->