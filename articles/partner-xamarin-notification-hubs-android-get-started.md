<properties linkid="" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="elioda" solutions="" manager="" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

Erste Schritte mit Notification Hubs
====================================

[Windows Store C\#](/de-de/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/de-de/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/de-de/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/de-de/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/de-de/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.Android-App senden. In diesem Lernprogramm erstellen Sie eine leere Xamarin.Android-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der komplette Code ist verfügbar in der [NotificationHubs-Beispielapp](http://go.microsoft.com/fwlink/p/?LinkId=331329).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Aktivieren von Google Cloud Messaging](#register)
2.  [Konfigurieren Ihres Notification Hub](#configure-hub)
3.  [Verbinden Ihrer App mit dem Notification Hub](#connecting-app)
4.  [Ausführen der App mit dem Emulator](#run-app)
5.  [Senden von Benachrichtigungen von Ihrem Back-End aus](#send)

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Für dieses Lernprogramm ist Folgendes erforderlich:

-   Xamarin.Android
-   Ein aktives Google-Konto

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Android-Apps.

**Hinweis**

Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F).

Aktivieren von Google Cloud MessagingAktivieren von Google Cloud Messaging
--------------------------------------------------------------------------

**Hinweis**

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302), um ein neues Google-Konto zu erstellen.

1.  Navigieren Sie zur [Google APIs](http://go.microsoft.com/fwlink/p/?LinkId=268303)-Webseite, melden Sie sich mit Ihren Google-Anmeldeinformationen an und klicken Sie auf **Projekt erstellen...**.

   	![][1]   

    **Hinweis**

    Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite **Dashboard** weitergeleitet. Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie **API Project**, klicken Sie unter **Other projects** auf **Create...**, geben Sie einen Projektnamen ein, und klicken Sie auf **Create project**.

2.  Klicken Sie in der linken Spalte auf **Overview**, und notieren Sie sich die Projektnummer im **Dashboard**-Bereich.

    Später im Lernprogramm legen Sie diesen Wert als die Variable PROJECT\_ID im Client fest.

3.  Klicken Sie auf der Seite [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) auf **Services**, und klicken Sie auf den Schalter, um **Google Cloud Messaging for Android** zu aktivieren. Akzeptieren Sie die Nutzungsbedingungen.

4.  Klicken Sie auf **API Access** und dann auf **Create new Server key...**

   	![][2]

5.  Klicken Sie unter **Configure Server Key for API Project** auf **Create**.

   	![][3]

6.  Notieren Sie sich den Wert **API key**.

   	![][4] 

Zunächst verwenden Sie diesen API-Schlüsselwert, um den Notification Hub für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

Konfigurieren Ihres Notification HubKonfigurieren Ihres Notification Hub
------------------------------------------------------------------------

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

   	![][7]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

   	![][8]

4.  Klicken Sie auf den gerade erstellten Namespace (normalerweise ***Name des Notification Hub*-ns**), und klicken Sie dann oben auf **Configure**.

   	![][9]

5.  Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

   	![][10]

6.  Klicken Sie auf die Registerkarte **Configure**, geben Sie den Wert **API Key** ein, den Sie im vorherigen Abschnitt erhalten haben, und klicken Sie auf **Save**.

   	![][11]

7.  Wählen Sie oben die Registerkarte **Dashboard** aus und klicken Sie auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][12]

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App zu registrieren und Pushbenachrichtigungen zu versenden.

Verbinden Ihrer AppVerbinden Ihrer App mit dem Notification Hub
---------------------------------------------------------------

### Erstellen eines neuen Projekts

1.  Erstellen Sie in Xamarin Studio (oder Visual Studio) ein neues Android-Projekt (File, New, Solution, Android Application).

   	![][13]   
   	![][14]

2.  Klicken Sie in der Lösungsansicht mit der rechten Maustaste auf Ihr neues Projekt und wählen Sie **Options** aus, um die Projekteingenschaften zu öffnen. Wählen Sie das Element **Android Application** im Bereich **Build** aus.

   	![][15]

3.  Wählen Sie unter **Minimum Android version** den Eintrag API Level 8 aus.

4.  Wählen Sie unter **Target Android version** Ihre gewünschte Zielversion der API aus (API Level 8 oder höher).

5.  Ihr **Package name** muss mit einem Kleinbuchstaben beginnen.

    **Hinweis**

    Der erste Buchstabe im Paketnamen muss ein Kleinbuchstabe sein. Ansonsten erhalten Sie Anwendungsmanifest-Fehler, wenn Sie Ihre **BroadcastReceiver** und **IntentFilter** weiter unten für Pushbenachrichtigungen registrieren.

### Hinzufügen von PushSharp zum Projekt

1.  Zunächst fügen Sie **PushSharp** als Referenz zu Ihrem Projekt hinzu. Hierzu müssen wir die aktuellste Version von PushSharp kompilieren und die kompilierte DLL als Referenz zu unserem Xamarin.Android-Projekt hinzufügen.

2.  Laden Sie sich von der [PushSharp](https://github.com/Redth/PushSharp) Github-Seite die neueste Version herunter. Sobald Sie die Sammlung von Dateien extrahiert haben, navigieren Sie zum folgenden Beispiel-Projektordner:

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. und öffnen sie die folgende Projektdatei in Xamarin Studio (oder Visual Studio):

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  Erstellen Sie das PushSharp-Clientbeispiel "MonoForAndroid" im Modus **Release**.

4.  Erstellen Sie in Ihrem Xamarin.Android-Projekt einen Ordner **\_external**

5.  Kopieren Sie die folgende Datei aus dem PushSharp-Client-Beispiel "MonoForAndroid" in den neu erstellten Ordner **\_external** in Ihrem Xamarin.Android-Projektordner:

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Öffnen Sie Ihr Xamarin.Android-Projekt in Xamarin Studio (oder Visual Studio).

7.  Klicken Sie mit der rechten Maustaste auf den Ordner **References**, und wählen Sie **Edit References...**

8.  Navigieren Sie zur Registerkarte **.Net Assembly**, navigieren Sie zum Ordner **\_external** in Ihrem Projekt, wählen Sie die zuvor erstellte **PushSharp.Client.MonoForAndroid.dll**, und klicken Sie auf **Add**. Klicken Sie auf OK, um das Dialogfenster zu schließen.

### Hinzufügen von Xamarin.NotificationHub zum Projekt

1.  Nun müssen Sie Bibliotheken hinzufügen, um die NotificationHub-Klasse zu unterstützen. Öffnen Sie die [Xamarin.NotificationHub](https://github.com/SaschaDittmann/Xamarin.NotificationHub) Github-Seite, laden Sie den Quellordner herunter und kompilieren Sie die Quellen.

2.  Kopieren Sie die kompilierte **ByteSmith.WindowsAzure.Messaging.Android.dll** in den **\_external**-Ordner in Ihrem Xamarin.Android-Projektordner.

3.  Öffnen Sie Ihr Xamarin.Android-Projekt in Xamarin Studio (oder Visual Studio).

4.  Klicken Sie mit der rechten Maustaste auf den Ordner **References**, und wählen Sie **Edit References...**

5.  Navigieren Sie zur Registerkarte **.Net Assembly**, navigieren Sie zum Ordner **\_external** in Ihrem Projekt, wählen Sie die zuvor erstellte **ByteSmith.WindowsAzure.Messaging.Android.dll**, und klicken Sie auf **Add**. Klicken Sie auf OK, um das Dialogfenster zu schließen.

### Einrichten von Notification Hubs in Ihrem Projekt

1.  Erstellen Sie eine Klasse **Constants.cs** und definieren Sie die folgenden konstanten Werte (Ersetzen Sie die Platzhalter durch die entsprechenden Werte):

         public const string SenderID = "<GoogleProjectNumber>"; // Google API-Projektnummer

         // Verbindungszeichenfolge und Hub-Pfad für die spezielle Azure-App
         public const string ConnectionString = "<Azure connection string>"";
         public const string NotificationHubPath = "<hub path>";

2.  Fügen Sie die folgenden using-Anweisungen zu **MainActivity.cs** hinzu:

         using ByteSmith.WindowsAzure.Messaging;
         using PushSharp.Client;

3.  Fügen Sie der **MainActivity**-Klasse die folgende Methode hinzu:

         private void RegisterWithGCM()
         {
             // Korrekte Einrichtung prüfen
             PushClient.CheckDevice(this);
             PushClient.CheckManifest(this);

             // Pushbenachrichtigungen registrieren
             System.Diagnostics.Debug.WriteLine("Registering...");
             PushClient.Register(this, Constants.SenderID);
         }

4.  Erstellen Sie eine neue Klasse **MyBroadcastReceiver**.

    **Hinweis**

    Wir werden die Erstellung eines **BroadcastReceivers** von Grund auf besprechen. Eine schnelle Alternative zur Erstellung der hier besprochenen Klasse **MyBroadcastReceiver.cs** ist ein Verweis auf die Datei **PushService.cs** im PushSharp-Beispielprojekt Xamarin.Android auf GitHub. Sie können für den Anfang auch **PushService.cs** duplizieren und die Klassennamen ändern.

5.  Fügen Sie die folgenden using-Anweisungen zu **MyBroadcastReceiver.cs** hinzu:

         using ByteSmith.WindowsAzure.Messaging;
         using PushSharp.Client;

6.  Fügen Sie die folgenden Berechtigungsanforderungen zwischen den **using**-Anweisungen und der **namespace**-Deklaration hinzu:

         [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
         [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
         [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

         //GET_ACCOUNTS wird nur für die Android-Versionen 4.0.3 und niedriger benötigt
         [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
         [assembly: UsesPermission(Name = "android.permission.INTERNET")]
         [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  Ändern Sie in **MyBroadcastReceiver.cs** die Methode **MyBroadcastReceiver** auf folgende Weise:

    [BroadcastReceiver(Permission=GCMConstants.PERMISSION\_GCM\_INTENTS)]

         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
         public class MyBroadcastReceiver : PushHandlerBroadcastReceiverBase<PushHandlerService>
         {
             public static string[] SENDER_IDS = new string[] { Constants.SenderID };

             public const string TAG = "MyBroadcastReceiver-GCM";
         }

8.  Fügen Sie eine weitere Klasse in **MyBroadcastReceiver.cs** mit dem Namen **PushHandlerService** hinzu, die von **PushHandlerServiceBase** abgeleitet wird. Verwenden Sie unbedingt die **Service**-Direktive in der Klasse:

    	[Service] //Muss die folgende Dienstanweisung verwenden:
    	public class PushHandlerService : PushHandlerServiceBase
    	{
             public static string RegistrationID { get; private set; }
             private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID) 
                {
                	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor"); 
            }

    }

9.  **PushHandlerServiceBase** implementiert die Methoden **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** und **OnError()**. Unsere Implementierungsklasse **PushHandlerService** muss diese Methoden überschreiben, die als Reaktion auf die Interaktion mit dem Notification Hub aufgerufen werden.

10. Überschreiben Sie die Methode **OnRegistered()** in **PushHandlerService** mit dem folgenden Code:

         protected override async void OnRegistered(Context context, string registrationId)
         {
             Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
             RegistrationID = registrationId;

             createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

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

    **Hinweis**

    Beachten Sie im **OnRegistered()**-Code die Möglichkeit, Tags für bestimmte Nachrichtenkanäle zu registrieren.

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
                //Benachrichtigung erstellen
                var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
            
                //Intent für die GUI-Anzeige erstellen
                var uiIntent = new Intent(this, typeof(MainActivity));
            
                //Benachrichtigung erstellen
                var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
            
                //AutoCancel entfernt die Benachrichtigung, sobald der Benutzer diese berührt
                notification.Flags = NotificationFlags.AutoCancel;
            
                //Benachrichtigungsinfo setzen
                //Wir verwenden den ausstehenden Intent und übergeben unseren GUI-Intent,
                //der wiederum beim Anklicken der Benachrichtigung aufgerufen wird.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
            
                //Benachrichtigung anzeigen
                notificationManager.Notify(1, notification);
            }

13. Überschreiben Sie die abstrakten Mitglieder **OnUnRegistered()**, **OnRecoverableError()** und **OnError()**, um Ihren Code kompilieren zu können.

Ausführen der AppAusführen der neuen App im Emulator
----------------------------------------------------

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1.  Klicken Sie unter **Tools** auf **Open Android Emulator Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Edit**.

   	![][18]

2.  Wählen Sie **Google APIs** unter **Ziel** aus, und klicken Sie auf **OK**.

   	![][19]

3.  Klicken Sie in der oberen Symbolleiste auf **Run**, und wählen Sie Ihre App aus. Daraufhin wird der Emulator gestartet und die App ausgeführt.

4.  Die App ruft die *registrationId* von GCM ab, und registriert sich beim Notification Hub.

    **Hinweis**

    Um Pushbenachrichtigungen zu erhalten, müssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (Navigieren Sie im Emulator zu **Settings**, und klicken Sie auf **Add Account**). Überprüfen Sie zudem, dass der Emulator mit dem Internet verbunden ist.

Senden einer BenachrichtigungSenden einer Benachrichtigung von Ihrem Back-End aus
---------------------------------------------------------------------------------

Sie können Benachrichtigungen mit Notification Hubs von jedem beliebigen Back-End aus mithilfe der [REST-Schnittstelle](http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx) senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem Mobile Service, der ein Knotenskript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1.  Erstellen einer neuen Visual C\#-Konsolenanwendung:

   	![][20]

2.  Fügen Sie mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets](http://nuget.org/packages/WindowsAzure.ServiceBus/) eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Package Manager Console**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

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

   	![][21]

Zum Senden einer Benachrichtigung mit Mobile Service folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service). Gehen Sie dann wie folgt vor:

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und wählen Sie Ihren Mobile Service aus.

2.  Wählen Sie oben die Registerkarte **Scheduler**.

   	![][22]

3.  Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.

   	![][23]

4.  Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend in der Kopfleiste auf die Registerkarte **Script**.

5.  Fügen Sie das folgende Skript in Ihre Scheduler-Funktion ein. Stellen Sie sicher, dass Sie die Platzhalter durch den Notification Hub-Namen und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* ersetzen, die Sie zuvor erhalten haben. Klicken Sie auf **Speichern**.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
         notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',

    	  function (error) {

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

Nächste Schritte
----------------

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Android-Geräte versendet. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users-aspnet). Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten](/de-de/manage/services/notification-hubs/breaking-news-dotnet) lesen. Mehr zur Verwendung von Notification Hubs erfahren Sie im [Notification Hubs-Leitfaden](http://msdn.microsoft.com/de-de/library/jj927170.aspx) und unter [Notification Hubs für Android](http://msdn.microsoft.com/de-de/library/dn282661.aspx).






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