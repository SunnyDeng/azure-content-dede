<properties 
	pageTitle="Erste Schritte mit Azure Notification Hubs" 
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/15/2014" 
	ms.author="ricksal"/>

# Erste Schritte mit Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Android-App senden. 
In diesem Lernprogramm erstellen Sie eine leere Android-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

* [Aktivieren von Google Cloud Messaging](#register)
* [Konfigurieren Ihres Notification Hub](#configure-hub)
* [Verbinden Ihrer App mit dem Notification Hub](#connecting-app)
* [Senden von Benachrichtigungen an die App](#send)
* [Testen der App](#run-app)

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Notification Hubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. 

Für dieses Lernprogramm ist Folgendes erforderlich:

+ das Android-SDK (es wird davon ausgegangen, dass Sie Eclipse verwenden), das Sie <a href="http://go.microsoft.com/fwlink/?LinkId=389797">hier</a> herunterladen können
+ das [Mobile Services Android SDK]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Android-Apps. 

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).

##<a id="register"></a>Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure-hub"></a>Konfigurieren Ihres Notification Hubs

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

###Erstellen eines neuen Android-Projekts

1. Erstellen Sie in Eclipse ADT ein neues Android-Projekt (Datei, Neu, Android-App).

   	![][13]

2. Das **Minimum Required SDK** muss auf  *API 8: Android 2.2 (Froyo)* festgelegt sein, und die nächsten zwei SDK-Einträge müssen auf die neueste verfügbare Version eingestellt sein. Folgen Sie dem Assistenten, und stellen Sie für **Aktivität erstellen** ein, dass eine leere Aktivität erstellt wird. Akzeptieren Sie im nächsten Feld das Standardsymbol für das Startprogramm, und klicken Sie im letzten Feld auf **Fertig stellen**.

   	![][14]

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

1. Laden Sie <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">hier</a> das Notification Hubs Android SDK herunter. Extrahieren Sie die .zip-Datei, und kopieren Sie die Datei "notificationhubs\notification-hubs-0.1.jar" in das Verzeichnis \libs des Projekts im Paket-Explorer.

2. Laden Sie das [Mobile Services Android SDK] herunter, und öffnen Sie es. Öffnen Sie den Ordner **notifications**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.

    > [AZURE.NOTE] Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.

	Richten Sie jetzt ein, dass die Anwendung eine *registrationId* von GCM erhält, und verwenden Sie diese, um die App-Instanz beim Notification Hub zu registrieren.

3. Fügen Sie in der Datei "AndroidManifest.xml" die folgende Zeile direkt unter dem Element <uses-sdk/> ein. Ersetzen Sie `<your package>` durch das Paket, das Sie in Schritt 1 für die App ausgewählt haben (in diesem Beispiel `com.yourCompany.wams_notificationhubs`).

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. Fügen Sie in der Klasse **MainActivity** folgende Anweisungen hinzu:

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Fügen Sie folgende private Member oben in der Klasse hinzu.

	> [AZURE.NOTE] Stellen Sie für die SENDER_ID die zuvor erhaltene Projektnummer ein.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. Fügen Sie in der **OnCreate**-Methode folgenden Code hinzu, und ersetzen Sie die Platzhalter durch die Verbindungszeichenfolge mit Abhörzugriff, die Sie im vorherigen Abschnitt erhalten haben, und durch den Namen des Notification Hub, der oben auf der Seite in Azure für den Hub angezeigt wird (**nicht** die vollständige URL).

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

8. Da Android keine Benachrichtigungen anzeigt, müssen Sie einen eigenen Empfänger schreiben. Fügen Sie in **AndroidManifest.xml** das folgende Element in das `<application/>`-Element ein.

	> [AZURE.NOTE] Ersetzen Sie den Platzhalter mit dem Namen Ihres Pakets.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und anschließend auf **Klasse**.

10. Geben Sie für **Name** den Wert `MyHandler` und in **Übergeordnete Klasse** den Wert `com.microsoft.windowsazure.notifications.NotificationsHandler` ein, und klicken Sie auf **Fertig stellen**.

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
	

##<a name="send"></a>Senden einer Benachrichtigung an Ihre App

>[AZURE.NOTE]Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung. Ein Beispiel für das Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End finden Sie unter [Erste Schritte mit Pushbenachrichtigungen in Mobile Services](mobile-services-javascript-backend-android-get-started-push.md).  Ein Beispiel zum Senden von Benachrichtigungen über die REST-APIs finden Sie unter [Verwenden von Notification Hubs von Java aus](notification-hubs-java-backend-how-to.md) sowie unter [Verwenden von Notification Hubs von PHP aus](notification-hubs-php-backend-how-to.md).

1. Wählen Sie in Visual Studio aus dem Menü **Datei** die Option **Neu** und dann **Projekt** aus, klicken Sie dann unter **Visual C#** auf **Windows** und **Konsolenanwendung**, und klicken Sie auf **OK**.  

   	![][20]

	Damit wird ein neues Konsolenanwendungsprojekt erstellt.

2. Klicken Sie im Menü **Extras** auf **Bibliothekspaket-Manager** und danach auf **Paket-Manager-Konsole**. 

	Daraufhin wird die Paket-Manager-Konsole angezeigt.

3. Führen Sie im Konsolenfenster folgenden Befehl aus:

        Install-Package WindowsAzure.ServiceBus
    
	Damit wird ein Verweis auf das Azure Service Bus-SDK mit dem <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Paket</a> hinzugefügt. 

4. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

5. Fügen Sie in der Klasse **Program** folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	Stellen Sie sicher, dass Sie den Platzhalter "hub name" durch den Namen des Notification Hub ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt "Konfigurieren Ihres Notification Hub" erhalten haben. 

	>[AZURE.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht die mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

5. Fügen Sie danach der **Main**-Methode die folgenden Zeilen hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Testen der App

Bevor Sie die App mit einem Emulator testen, müssen Sie den Emulator wir folgt konfigurieren (kann bei Tests auf einem physischen Gerät übersprungen werden):

1. Überprüfen Sie, dass Sie ein Android Virtual Device (AVD) verwenden, das Google APIs unterstützt.

2. Klicken Sie unter **Fenster** auf **Android Virtual Device Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Bearbeiten**.

   	![][18]

3. Wählen Sie in **Target** die Option **Google APIs** aus, und klicken Sie dann auf **OK**.

   	![][19]

4. 	Um Pushbenachrichtigungen zu erhalten, müssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (Navigieren Sie im Emulator zu <strong>Einstellungen</strong>, und klicken Sie auf <strong>Konto hinzufügen</strong>). Überprüfen Sie zudem, ob der Emulator mit dem Internet verbunden ist.


Gehen Sie wie folgt vor, um die App auf einem Gerät oder dem Emulator auszuführen:

1. Klicken Sie in der oberen Eclipse-Symbolleiste auf **Ausführen**, und wählen Sie die App aus. 
 
	Damit wird der Emulator gestartet (falls verwendet) und die App geladen und ausgeführt. Die App ruft die *registrationId* von GCM ab und registriert sich beim Notification Hub.

3. Drücken Sie in Visual Studio die Taste F5, um die Konsolenanwendung auszuführen. 

	An Ihre App wird eine Benachrichtigung gesendet.  
 
5. Wenn im Benachrichtigungsbereich (obere linke Ecke) ein Symbol angezeigt wird, ziehen Sie die Benachrichtigungsschublade nach unten, um die Benachrichtigung anzuzeigen.  

   	![][21]

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Android-Geräte versendet. Informationen zum Adressieren bestimmter Benutzer finden Sie im Lernprogramm [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] lesen. Weitere Informationen zur Verwendung von Notification Hubs finden Sie in der [Notification Hubs-Leitfaden].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

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
[Verweisen auf ein Bibliothekprojekt]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx

[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: notification-hubs-aspnet-backend-android-notify-users.md
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: notification-hubs-aspnet-backend-android-breaking-news.md


<!--HONumber=49-->