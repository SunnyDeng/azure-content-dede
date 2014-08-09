<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

Erste Schritte mit Notification Hubs
====================================

[Windows Store C\#](/de-de/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/de-de/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/de-de/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/de-de/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/de-de/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Android-App senden. In diesem Lernprogramm erstellen Sie eine leere Android-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

-   [Aktivieren von Google Cloud Messaging](#register)
-   [Konfigurieren Ihres Notification Hub](#configure-hub)
-   [Verbinden Ihrer App mit dem Notification Hub](#connecting-app)
-   [Versenden von Benachrichtigungen an die App](#send)
-   [Testen der App](#run-app)

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Notification Hubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.

Für dieses Lernprogramm ist Folgendes erforderlich:

-   das Android-SDK (es wird davon ausgegangen, dass Sie Eclipse verwenden), das Sie [hier](http://go.microsoft.com/fwlink/?LinkId=389797) herunterladen können
-   das [Mobile Services Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Android-Apps.

**Hinweis**

Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Aktivieren von Google Cloud Messaging
-------------------------------------

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Zunächst verwenden Sie diesen API-Schlüsselwert, um den Notification Hub für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

Konfigurieren Ihres Notification Hub
------------------------------------

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie im unteren Teil des Bildschirms auf **+NEU**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

    ![][7]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

    ![][8]

4.  Klicken Sie auf den gerade erstellten Namespace (normalerweise ***Name des Notification Hub*-ns**), und klicken Sie dann oben auf **Konfigurieren**.

    ![][9]

5.  Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

    ![][10]

6.  Klicken Sie auf die Registerkarte **Configure**, geben Sie den Wert **API Key** ein, den Sie im vorherigen Abschnitt erhalten haben, und klicken Sie auf **Speichern**.

    ![][11]

7.  Wählen Sie oben die Registerkarte **Dashboard** aus, und klicken Sie anschließend auf **View Connection String**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App zu registrieren und Pushbenachrichtigungen zu versenden.

Verbinden Ihrer App mit dem Notification Hub
--------------------------------------------

### Erstellen eines neuen Android-Projekts

1.  Erstellen Sie in Eclipse ADT ein neues Android-Projekt (Datei, Neu, Android-App).

    ![][13]

2.  Für das **Minimum Required SDK** muss *API 8: Android 2.2 (Froyo)* festgelegt sein, und die nächsten zwei SDK-Einträge müssen auf die neueste verfügbare Version eingestellt sein. Folgen Sie dem Assistenten, und stellen Sie für **Create activity** ein, dass eine leere Aktivität erstellt wird. Akzeptieren Sie im nächsten Feld das Standardsymbol für das Startprogramm, und klicken Sie im letzten Feld auf **Finish**.

    ![][14]

### Hinzufügen von Google Play Services zum Projekt

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Code hinzufügen

1.  Laden Sie [hier](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409) das Notification Hubs Android SDK herunter. Extrahieren Sie die .zip-Datei, und kopieren Sie die Datei "notificationhubs\\notification-hubs-0.1.jar" in das Verzeichnis \\libs des Projekts im Paket-Explorer.

2.  Laden Sie den [Mobile Services Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409) herunter, und entzippen Sie ihn. Öffnen Sie den Ordner **Benachrichtigungen**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.

    **Hinweis**

    Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.

    Richten Sie jetzt ein, dass die Anwendung eine *registrationId* von GCM erhält, und verwenden Sie diese, um die App-Instanz beim Notification Hub zu registrieren.

3.  Fügen Sie in der Datei "AndroidManifest.xml" folgende Zeile direkt unter das Element &lt;uses-sdk\> hinzu. Ersetzen Sie `<your package>` mit dem Paket, das Sie in Schritt 1 für die App ausgewählt haben (`com.yourCompany.wams_notificationhubs` in diesem Beispiel).

         <uses-permission android:name="android.permission.INTERNET"/>
         <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
         <uses-permission android:name="android.permission.WAKE_LOCK"/>
         <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

         <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
         <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  Fügen Sie in der Klasse **MainActivity** folgende Anweisungen hinzu:

         import android.os.AsyncTask;    
         import com.google.android.gms.gcm.*;
         import com.microsoft.windowsazure.messaging.*;
         import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  Fügen Sie folgende private Member oben in der Klasse hinzu.

    **Hinweis**

    Stellen Sie für die SENDER\_ID die zuvor erhaltene Projektnummer ein.

         private String SENDER_ID = "<your project number>";
         private GoogleCloudMessaging gcm;
         private NotificationHub hub;

6.  Fügen Sie in der Methode **OnCreate** folgenden Code hinzu, und ersetzen Sie die Platzhalter mit der Verbindungszeichenfolge mit Hörzugang, die Sie im vorherigen Abschnitt erhalten haben, sowie mit dem Namen des Notification Hub, der oben auf der Seite in Azure für den Hub angezeigt wird (**nicht** die vollständige URL).

         NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

         gcm = GoogleCloudMessaging.getInstance(this);
            
         String connectionString = "<your listen access connection string>";
         hub = new NotificationHub("<your notification hub name>", connectionString, this);
            
         registerWithNotificationHubs();

7.  Erstellen Sie in "MainActivity.java" folgende Methode:

         @SuppressWarnings("unchecked")
         private void registerWithNotificationHubs() {
            new AsyncTask() {
               @Override
               protected Object doInBackground(Object... params) {
                  try {
                     String regid = gcm.register(SENDER_ID);
                     hub.register(regid);
                  } catch (Exception e) {
                     return e;
                  }
                  return null;
              }
            }.execute(null, null, null);
         }

8.  Da Android keine Benachrichtigungen anzeigt, müssen Sie einen eigenen Empfänger schreiben. Fügen Sie in **AndroidManifest.xml** das folgende Element zum Element `<application/>` hinzu.

    **Hinweis**

    Ersetzen Sie den Platzhalter mit dem Namen Ihres Pakets.

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

9.  Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und dann auf **Klasse**.

10. Geben Sie unter **Name** `MyHandler` ein, unter **Übergeordnete Klasse** `com.microsoft.windowsazure.notifications.NotificationsHandler`, und klicken Sie dann auf **Fertig stellen**.

    ![](./media/notification-hubs-android-get-started/notification-hub-android-new-class.png)

    Daraufhin wird die neue MyHandler-Klasse erstellt.

11. Fügen Sie die folgenden Import-Ausdrücke ein:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

12. Fügen Sie folgenden Code zur Klasse hinzu:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

            
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("msg");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

Versenden von Benachrichtigungen an die App
-------------------------------------------

Sie können Benachrichtigungen mit Notification Hubs von jedem Back-End aus versenden, das unsere [REST-Schnittstelle](http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx) verwendet. In diesem Lernprogramm zeigen wir zwei Möglichkeiten für den Versand von Benachrichtigungen: mit einer .NET-Konsolenanwendung und mit einem Mobile Service unter Verwendung eines Knotenskripts.

### Senden von Benachrichtigungen mit einer .NET-Konsolenanwendung:

1.  Erstellen einer neuen Visual C\#-Konsolenanwendung:

    ![][20]

2.  Fügen Sie mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets](http://nuget.org/packages/WindowsAzure.ServiceBus/) eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Paket-Managerkonsole**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

         Install-Package WindowsAzure.ServiceBus

    und drücken Sie die Eingabetaste.

3.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

         using Microsoft.ServiceBus.Notifications;

4.  Fügen Sie in Ihrer Klasse `Program` die folgende Methode hinzu:

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
         }

5.  Fügen Sie dann folgende Zeilen zur Main-Methode hinzu:

          SendNotificationAsync();
          Console.ReadLine();

### Versenden einer Benachrichtigung über einen Mobile Service

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und wählen Sie Ihren Mobile Service aus. Wenn Sie noch keinen Mobile Service haben, folgen Sie der Anleitung [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started/#create-new-service).

2.  Wählen Sie oben die Registerkarte **Scheduler**.

    ![][22]

3.  Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.

    ![][23]

4.  Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend in der Kopfleiste auf die Registerkarte **Skript**.

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

Testen der App
--------------

Um die Anwendung mit einem Telefon zu testen, müssen Sie dieses über ein USB-Kabel mit Ihrem Computer verbinden.

So testen Sie diese App mit dem Emulator:

1.  Überprüfen Sie, dass Sie ein Android Virtual Device (AVD) verwenden, das Google APIs unterstützt.

2.  Klicken Sie unter **Fenster** auf **Android Virtual Device Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Bearbeiten**.

    ![][18]

3.  Wählen Sie **Google APIs** in **Ziel** aus, und klicken Sie auf **OK**.

    ![][19]

4.  Damit Sie Pushbenachrichtigungen erhalten, müssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (Navigieren Sie im Emulator zu **Einstellungen**, und klicken Sie auf **Konto hinzufügen**). Überprüfen Sie zudem, dass der Emulator mit dem Internet verbunden ist.

Unabhängig vom Gerät folgt folgender Schritt:

1.  Klicken Sie in der oberen Eclipse-Symbolleiste auf **Ausführen**, und wählen Sie die App aus. Die App wird entweder direkt auf das verbundene Telefon geladen, oder der Emulator wird gestartet und lädt die App und führt sie aus.

2.  Die App ruft die *registrationId* von GCM ab, und registriert sich beim Notification Hub.

3.  Verwenden Sie jetzt eine der Methoden aus dem vorherigen Abschnitt, um eine Benachrichtigung an die App zu senden:

    -   Wenn Sie eine .Net-Konsolenanwendung verwenden, drücken Sie die Taste F5 in Visual Studio, um die Anwendung auszuführen, wodurch eine Benachrichtigung versendet wird.
    -   Wenn Sie ein Mobile Services-Skript verwenden, klicken Sie in der unteren Leiste des Mobile Service-Bildschirms auf **Einmal ausführen**, damit das Skript eine Benachrichtigung versendet.

4.  Im Benachrichtigungsbereich (obere linke Ecke) wird ein Symbol angezeigt. Ziehen Sie die Benachrichtigungsschublade nach unten, um die Benachrichtigung anzuzeigen.

    ![][21]

Nächste Schritte
----------------

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Android-Geräte versendet. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users-aspnet). Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten](/de-de/manage/services/notification-hubs/breaking-news-dotnet) lesen. Mehr zur Verwendung von Notification Hubs erfahren Sie in [Notification Hubs-Leitfaden](http://msdn.microsoft.com/de-de/library/jj927170.aspx) und in [Notification Hubs für Android](http://msdn.microsoft.com/de-de/library/dn282661.aspx).



<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /de-de/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /de-de/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /de-de/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript and HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/de-de/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
