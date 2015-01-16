<properties urlDisplayName="Get Started" pageTitle="Erste Schritte mit Azure Notification Hubs" metaKeywords="" description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="11/21/2014" ms.author="ricksal" />
# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Android-App senden. 
In diesem Lernprogramm erstellen Sie eine leere Android-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

* [Aktivieren von Google Cloud Messaging](#register)
* [Konfigurieren Ihres Notification Hub](#configure-hub)
* [Verbinden Ihrer App mit dem Notification Hub](#connecting-app)
* [So senden Sie Ihrer App eine Benachrichtigung](#send)
* [Testen Ihrer App](#run-app)

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Notification Hubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. 

Für dieses Lernprogramm ist Folgendes erforderlich:

+ das Android-SDK (es wird davon ausgegangen, dass Sie Eclipse verwenden), das Sie <a href="http://go.microsoft.com/fwlink/?LinkId=389797">hier herunterladen können</a>
+ das [Mobile Services Android-SDK]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Android-Apps. 

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose</a>Azure-Testversion.</p></div>

##<a id="register"></a>Aktivieren von Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Zunächst verwenden Sie diesen API-Schlüsselwert, um den Notification Hub für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

##<a id="configure-hub"></a>Konfigurieren Ihres Notification Hub

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie dann im unteren Bereich des Bildschirms auf **+NEU**.

2. Klicken Sie auf **App Services**, **Service Bus**, **Notification Hub** und dann auf **Schnellerfassung**.

   	![][7]

3. Geben Sie einen Namen für den Notification Hub ein, wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Neuen Notification Hub erstellen**.

   	![][8]

4. Klicken Sie auf den soeben erstellten Namespace (normalerweise ***Name des Notification Hub*-ns**), und klicken Sie dann im oberen Bereich auf **Konfigurieren**.

   	![][9]

5. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellten Notification Hub.

   	![][10]

6. Klicken Sie oben auf die Registerkarte **Konfigurieren**, geben Sie den Wert für den **API-Schlüssel** ein, den Sie im vorherigen Abschnitt erhalten haben, und klicken Sie dann auf **Speichern**.

   	![][11]

7. Wählen Sie im oberen Bereich die Registerkarte **Dashboard** aus, und klicken Sie dann auf **Verbindungszeichenfolge anzeigen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.


Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App zu registrieren und Pushbenachrichtigungen zu versenden.

##<a id="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

###Erstellen eines neuen Android-Projekts

1. Erstellen Sie in Eclipse ADT ein neues Android-Projekt (Datei, Neu, Android-App).

   	![][13]

2. Achten Sie darauf, dass **Minimum Required SDK** auf *API 8: Android 2.2 (Froyo)* festgelegt ist und die nächsten zwei SDK-Einträge auf die neueste verfügbare Version eingestellt sind. Wählen Sie "Weiter" aus, folgen Sie dem Assistenten, und stellen Sie sicher, dass **Aktivität erstellen** ausgewählt ist, um eine leere Aktivität zu erstellen. Aktivieren Sie im nächsten Feld das Standardsymbol für das Startprogramm, und klicken Sie im letzten Feld auf **Fertig stellen**.

   	![][14]

###Hinzufügen von Google Play Services zum Projekt

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

1. Laden Sie das Notification Hubs Android SDK <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">hier</a>herunter. Extrahieren Sie die .zip-Datei, und kopieren Sie die Datei "notificationhubs\notification-hubs-0.1.jar" in das Verzeichnis \libs des Projekts im Paket-Explorer.

2. Laden Sie das [Mobile Services Android SDK] herunter, und entzippen Sie es. Öffnen Sie den Ordner **Benachrichtigungen**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner "libs" Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner "libs".

    <div class="dev-callout"><b>Hinweis</b>
	<p>Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.</p>
    </div>

	Richten Sie jetzt ein, dass die Anwendung eine registrationId von GCM erhält, und verwenden Sie diese, um die App-Instanz beim Notification Hub zu registrieren.

3. Fügen Sie in der Datei "AndroidManifest.xml" die folgende Zeile direkt unter dem Element <uses-sdk/> ein. Ersetzen Sie <your package> durch das Paket, das Sie in Schritt 1 für die App ausgewählt haben (com.yourCompany.wams_notificationhubs in diesem Beispiel).

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. Fügen Sie in der Klasse **MainActivity** die folgenden Anweisungen hinzu.

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Fügen Sie folgende private Member oben in der Klasse hinzu.

	<div class="dev-callout"><b>Hinweis</b>
    <p>Stellen Sie für die SENDER_ID die zuvor erhaltene Projektnummer ein.</p>
    </div> 

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. Fügen Sie in der Methode **OnCreate** den folgenden Code hinzu, und ersetzen Sie die Platzhalter durch die Verbindungszeichenfolge mit Abhörzugriff, die Sie im vorherigen Abschnitt erhalten haben, und durch den Namen des Notification Hub, der oben auf der Seite in Azure für den Hub angezeigt wird (**nicht** die vollständige URL).

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<your listen access connection string>";
		hub = new NotificationHub("<your notification hub name>", connectionString, this);
		
		registerWithNotificationHubs();

7. Erstellen Sie in "MainActivity.java" folgende Methode:

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

8. Da Android keine Benachrichtigungen anzeigt, müssen Sie einen eigenen Empfänger schreiben. Fügen Sie in **AndroidManifest.xml** das folgende Element im Element <application/> hinzu.

	<div class="dev-callout"><b>Hinweis</b>
    <p>Ersetzen Sie den Platzhalter mit dem Namen Ihres Pakets.</p>
    </div> 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten "src"), und klicken Sie auf **Neu** und dann auf **Klasse**.

10. Geben Sie unter **Name** "MyHandler" und unter **Superclass** "com.microsoft.windowsazure.notifications.NotificationsHandler" ein, und klicken Sie dann auf **Fertig stellen**

	![][6]

	Daraufhin wird die neue MyHandler-Klasse erstellt.

11. Fügen Sie die folgenden Import-Anweisungen ein:

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
	

##<a name="send"></a>Versenden von Benachrichtigungen an die App

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End über die <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a>senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung. Ein Beispiel für das Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End finden Sie unter **Erste Schritte mit Pushbenachrichtigungen in Mobile Services** ([.NET-Back-End](/de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/) | [JavaScript-Back-End](/de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)).  Ein Beispiel für das Senden von Benachrichtigungen über die REST-APIs finden Sie unter **Verwenden von Notification Hubs von Java/PHP** ([Java](/de-de/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/de-de/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Wählen Sie in Visual Studio im Menü **Datei** **Neu** und dann **Projekt...** aus, und klicken Sie dann unter **Visual C#** auf **Windows**, **Konsolenanwendung** und **OK**.  

   	![][20]

	Damit wird ein neues Konsolenanwendungsprojekt erstellt.

2. Klicken Sie im Menü **Extras** auf **Bibliothekspaket-Manager** und dann auf **Paket-Manager-Konsole**. 

	Daraufhin wird die Paket-Manager-Konsole angezeigt.

3. Führen Sie im Konsolenfenster folgenden Befehl aus:

        Install-Package WindowsAzure.ServiceBus
    
	Damit wird ein Verweis auf das Azure Service Bus SDK mit dem <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Paket</a>hinzugefügt. 

4. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

5. Fügen Sie in der Klasse **Program** die folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	Stellen Sie sicher, dass Sie den Platzhalter "hub name" durch den Namen des Notification Hub ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt "Konfigurieren Ihres Notification Hub" erhalten haben. 

	>[WACOM.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht die mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

5. Fügen Sie dann die folgenden Zeilen in der Methode **Main** hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Testen der App

Bevor Sie die App mit einem Emulator testen, müssen Sie den Emulator wir folgt konfigurieren (kann bei Tests auf einem physischen Gerät übersprungen werden):

1. Überprüfen Sie, dass Sie ein Android Virtual Device (AVD) verwenden, das Google APIs unterstützt.

2. Klicken Sie unter **Windows** auf **Android Virtual Device Manager**, wählen Sie Ihr Gerät aus, und klicken Sie dann auf **Edit**.

   	![][18]

3. Wählen Sie **Google APIs** in **Target** aus, und klicken Sie dann auf **OK**.

   	![][19]

4. 	Um Pushbenachrichtigungen zu erhalten, müssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (Navigieren Sie im Emulator zu <strong>Setting,</strong> und klicken Sie auf <strong>Konto hinzufügen</strong>). Überprüfen Sie zudem, dass der Emulator mit dem Internet verbunden ist.


Gehen Sie wie folgt vor, um die App auf einem Gerät oder dem Emulator auszuführen:

1. Klicken Sie in der oberen Eclipse-Symbolleiste auf **Run**, und wählen Sie dann Ihre App aus. 
 
	Damit wird der Emulator gestartet (falls verwendet) und die App geladen und ausgeführt. Die App ruft die registrationId von GCM ab und registriert sich beim Notification Hub.

3. Drücken Sie in Visual Studio die Taste F5, um die Konsolenanwendung auszuführen. 

	An Ihre App wird eine Benachrichtigung gesendet.  
 
5. Wenn im Benachrichtigungsbereich (obere linke Ecke) ein Symbol angezeigt wird, ziehen Sie die Benachrichtigungsschublade nach unten, um die Benachrichtigung anzuzeigen.  

   	![][21]

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Android-Geräte versendet. Informationen zum Adressieren bestimmter Benutzer finden Sie im Lernprogramm [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]. Wenn Sie Benutzer nach Interessengruppen unterteilen möchten, lesen Sie [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]. Weitere Informationen zum Verwenden von Notification Hubs finden Sie im [Notification Hubs-Leitfaden].


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
[Mobile Services Android-SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-android
[Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-android
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js
[Referenzieren eines Bibliothekprojekts]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[wns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx

[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet


<!--HONumber=35.1-->
