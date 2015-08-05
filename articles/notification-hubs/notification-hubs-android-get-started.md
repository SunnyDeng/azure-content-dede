<properties 
	pageTitle="Erste Schritte mit Azure-Benachrichtigungs-Hubs" 
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="wesmc"/>

# Erste Schritte mit Notification Hubs

[AZURE.INCLUDE [Notification-Hubs-Auswahl-Erste-Schritte](../../includes/notification-hubs-selector-get-started.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Android-App senden. In diesem Lernprogramm erstellen Sie eine leere Android-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Notification Hubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.


##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Android Studio, das Sie <a href="http://go.microsoft.com/fwlink/?LinkId=389797">hier</a> herunterladen können.
+ Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).


Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Android-Apps.


##Erstellen eines Projekts, das Google Cloud Messaging unterstützt

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]


##Konfigurieren eines neuen Notification Hubs

[AZURE.INCLUDE [Notification-Hubs-Android-konfigurieren-Push](../../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

###Erstellen eines neuen Android-Projekts

1. Starten Sie in Android Studio ein neues Android Studio-Projekt.

   	![][13]

2. Wählen Sie den Formfaktor **Phone and Tablet** und das **Minimum SDK** aus, die unterstützt werden sollen. Klicken Sie auf **Next**.

   	![][14]

3. Wählen Sie **Blank Activity** als Hauptaktivität aus. Klicken Sie dann auf **Next** und anschließend auf **Finish**.

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Hinzufügen von Play Services](../../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

1. Laden Sie <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">hier</a> das Notification Hubs Android SDK herunter. Extrahieren Sie die ZIP-Datei, und kopieren Sie die Dateien **notificationhubs\notification-hubs-0.4.jar** und **notifications\notifications-1.0.1.jar** in das Verzeichnis **app\libs** Ihres Projekts. Dazu können Sie die Dateien direkt in den Ordner **libs** im Fenster "Project View" von Android Studio ziehen. Aktualisieren Sie den Ordner "libs".



	Die Referenzdokumentation für diese zwei Pakete finden Sie unter den folgenden Links: * [com.microsoft.windowsazure.messaging](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/messaging/package-summary.html) * [com.microsoft.windowsazure.notifications](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/notifications/package-summary.html)


    > [AZURE.NOTE]Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.

2. Als Nächstes richten Sie die Anwendung so ein, dass eine Registrierungs-ID von GCM abgerufen wird, und verwenden diese zum Registrieren der App-Instanz beim Notification Hub.

	Fügen Sie in der Datei "AndroidManifest.xml" die folgenden Berechtigungen unter der Markierung `</application>` ein. Ersetzen Sie dabei `<your package>` durch den oben in der Datei "AndroidManifest.xml" angegebenen Paketnamen (`com.example.testnotificationhubs` in diesem Beispiel).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. Fügen Sie in der Klasse **MainActivity** die folgenden `import`-Anweisungen über der Klassendeklaration hinzu.

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


4. Fügen Sie folgende private Member oben in der Klasse hinzu.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";


	Aktualisieren Sie die drei Platzhalter: * **SENDER_ID**: Setzen Sie `SENDER_ID` auf die Projektnummer, die Sie zuvor von dem in der [Google Cloud Console](http://cloud.google.com/console) erstellten Projekt abgerufen haben. * **HubListenConnectionString**: Legen Sie für `HubListenConnectionString` die Verbindungszeichenfolge **DefaultListenAccessSignature** für den Hub fest. Sie können diese Verbindungszeichenfolge kopieren, indem Sie im [Azure-Verwaltungsportal] auf der Registerkarte **Dashboard** Ihres Hubs auf **Verbindungszeichenfolge anzeigen** klicken. * **HubName**: Der Name Ihres Notification Hubs, der oben auf der Seite in Azure angezeigt wird (**nicht** die vollständige URL). Beispiel: `"myhub"`.



5. Fügen Sie in der **OnCreate**-Methode der **MainActivity**-Klasse den folgenden Code zum Ausführen der Registrierung bei der Erstellung der Aktivität hinzu.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. Fügen Sie in **MainActivity.java** den folgenden Code für die **registerWithNotificationHubs()**-Methode hinzu. Diese Methode meldet die erfolgreiche Durchführung nach der Registrierung bei Google Cloud Messaging und beim Notification Hub:

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    DialogNotify("Registered Successfully","RegId : " + 
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	DialogNotify("Exception",e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}

		
		/**
		  * A modal AlertDialog for displaying a message on the UI thread
		  * when theres an exception or message to report.
		  * 
		  * @param title   Title for the AlertDialog box. 
		  * @param message The message displayed for the AlertDialog box.
		  */
    	public void DialogNotify(final String title,final String message)
    	{
        	final AlertDialog.Builder dlg;
        	dlg = new AlertDialog.Builder(this);

        	runOnUiThread(new Runnable() {
            	@Override
            	public void run() {
                	AlertDialog dlgAlert = dlg.create();
                	dlgAlert.setTitle(title);
                	dlgAlert.setButton(DialogInterface.BUTTON_POSITIVE, 
						(CharSequence) "OK", 
						new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
                	dlgAlert.setMessage(message);
                	dlgAlert.setCancelable(false);
                	dlgAlert.show();
            	}
        	});
    	}

7. Da Android keine Benachrichtigungen anzeigt, müssen Sie einen eigenen Empfänger schreiben. Fügen Sie in **AndroidManifest.xml** das folgende Element im `<application>`-Element ein.

	> [AZURE.NOTE]Ersetzen Sie den Platzhalter mit dem Namen Ihres Pakets.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


8. Erweitern Sie in der Projektansicht die Knoten **app** -> **src** -> **main** -> **java**. Klicken Sie mit der rechten Maustaste auf Ihren Paketordner unter **java**, klicken Sie auf **Neu** und dann auf **Java Class**.

	![][6]

9. Geben Sie im Feld **Name** für den neuen Klassentyp den Namen **MyHandler** ein, und klicken Sie dann auf **OK**
	

10. Fügen Sie die folgenden Importanweisungen am Anfang der Datei **MyHandler.java** ein:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

11. Aktualisieren Sie die Klassendeklaration wie folgt, um `MyHandler` wie unten dargestellt als Unterklasse von `com.microsoft.windowsazure.notifications.NotificationsHandler` festzulegen.

		public class MyHandler extends NotificationsHandler {


12. Fügen Sie den folgenden Code für die `MyHandler`-Klasse hinzu.

	Mit diesem Code wird die `OnReceive`-Methode überschrieben, sodass der Handler ein `AlertDialog` einblendet, in dem empfangene Benachrichtigungen angezeigt werden. Der Handler sendet die Benachrichtigung mit der `sendNotification()`-Methode auch an den Android-Benachrichtigungs-Manager.

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
        	mainActivity.DialogNotify("Received Notification",nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}
	
13. Klicken Sie in Android Studio auf der Menüleiste auf **Build** -> **Rebuild Project**, um sicherzustellen, dass keine Fehler gefunden werden.

##Senden von Benachrichtigungen



Sie können den Empfang von Benachrichtigungen in Ihrer App testen, indem Sie wie im Bildschirm unten abgebildet Benachrichtigungen im Azure-Portal über die Registerkarte zum Debuggen im Notification Hub senden.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. Erweitern Sie in der Projektansicht von Android Studio **App** -> **src** -> **main** -> **res** -> **layout**. Öffnen Sie die Layoutdatei **activity_main.xml**, und klicken Sie auf die Registerkarte **Text**, um den Textinhalt der Datei zu aktualisieren. Ändern Sie ihn in den folgenden Code, mit dem ein neues `Button`- und `EditText`-Steuerelement zum Senden von Benachrichtigungsmeldungen an den Notification Hub hinzugefügt wird. Fügen Sie diesen Code am Ende der Datei unmittelbar vor `</RelativeLayout>` hinzu.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Erweitern Sie in der Projektansicht von Android Studio **App** -> **src** -> **main** -> **res** -> **values**. Öffnen Sie die Datei **strings.xml**, und fügen Sie die Zeichenfolgenwerte hinzu, auf die von dem neuen `Button`- und `EditText`-Steuerelement verwiesen wird. Fügen Sie sie am Ende der Datei unmittelbar vor `</resources>` ein.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Fügen Sie in der Datei **MainActivity.java**, die folgenden `import`-Anweisungen über der `MainActivity`-Klasse hinzu.

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;
		
		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


3. Fügen Sie in der Datei **MainActivity.java** die folgenden Member oben in der `MainActivity`-Klasse hinzu.

	Geben Sie für `HubName` den Namen des Hubs und nicht den Namespace ein, z. B. "myhub". Geben Sie außerdem die Verbindungszeichenfolge **DefaultFullSharedAccessSignature** ein. Diese Verbindungszeichenfolge kann im [Azure-Verwaltungsportal] kopiert werden. Klicken Sie dazu auf **Verbindungzeichenfolge anzeigen** auf der Registerkarte **Dashboard** für Ihren Notification Hub.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. Ihre Aktivität enthält den Namen des Hubs und die vollständige Verbindungszeichenfolge des gemeinsamen Zugriffs für den Hub. Sie müssen ein Software Access Signature (SaS)-Token erstellen, um eine POST-Anforderung zum Senden von Nachrichten an den Notification Hub zu authentifizieren. Dies erfolgt durch das Analysieren der wichtigsten Daten aus der Verbindungszeichenfolge und dem anschließenden Erstellen des SaS-Tokens (siehe dazu die REST-API-Referenz unter [Allgemeine Konzepte](http://msdn.microsoft.com/library/azure/dn495627.aspx)).

	Fügen Sie in **MainActivity.java** der `MainActivity`-Klasse die folgende Methode hinzu, um die Verbindungszeichenfolge zu analysieren.

	    /**
    	 * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
    	 * to parse the connection string so a SaS authentication token can be 
    	 * constructed.
    	 * 
    	 * @param connectionString This must be the DefaultFullSharedAccess connection
    	 *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);
	
	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }

5. Fügen Sie in **MainActivity.java** der `MainActivity`-Klasse die folgende Methode hinzu, um ein SaS-Authentifizierungstoken zu erstellen.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         * 
         * @param uri The un-encoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example, 
         *            "http://<namespace>.servicebus.windows.net/<hubName>" 
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
            	// Using android.util.Base64 for Android Studio instead of 
	            // Apache commons codec.
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // construct authorization string
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }
    
            return null;
        }


6. Fügen Sie in **MainActivity.java** der `MainActivity`-Klasse die folgende Methode hinzu, um das Click-Ereignis der Schaltfläche **Send Notification** zu verarbeiten und die Benachrichtigungsmeldung über die REST-API an den Hub zu senden.

        /**
         * Send Notification button click handler. This method parses the 
         * DefaultFullSharedAccess connection string and generates a SaS token. The 
         * token is added to the Authorization header on the POST request to the 
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         * 
         * @param v 
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        HttpClient client = new DefaultHttpClient();
    
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);
    
                        // Authenticate the POST request with the SaS token.
                        post.setHeader("Authorization", generateSasToken(url));
    
                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");
    
                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));
    
                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##Testen der App

####Testen mit einem Emulator
Wenn Sie die App mit einem Emulator testen möchten, müssen Sie sicherstellen, dass das Emulatorabbild die Google-API-Ebene unterstützt, die Sie für die API auswählen. Wenn das Abbild die Google-APIs nicht unterstützt, wird die Ausnahme **SERVICE_NOT_AVAILABLE** ausgegeben.

Stellen Sie außerdem sicher, dass Ihr Google-Konto dem ausgeführten Emulator unter **Einstellungen** -> **Konten** hinzugefügt ist. Andernfalls führt die Registrierung bei GCM möglicherweise zur Ausnahme **AUTHENTICATION_FAILED**.

####Testen der App     

1. Führen Sie die App aus, und prüfen Sie, ob die Registrierungs-ID als erfolgreiche Registrierung gemeldet wird.

   	![][18]

2. Geben Sie eine Benachrichtigungsmeldung ein, die an alle für den Hub registrierten Android-Geräte gesendet werden soll.

   	![][19]

3. Tippen Sie auf **Send Notification**. Auf allen Geräten, auf denen die App ausgeführt wird, wird ein `AlertDialog` mit der Benachrichtigungsmeldung angezeigt. Auf Geräten, auf denen die App nicht ausgeführt wird, die jedoch zuvor für Benachrichtigungen registriert wurden, wird im Benachrichtigungs-Manager eine Benachrichtigung hinzugefügt. Benachrichtigungen können durch Streichen von der linken oberen Ecke nach unten angezeigt werden.

   	![][21]

##Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an alle Android-Geräte versendet. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs]. Wenn Sie Benutzer nach Interessengruppen segmentieren möchten, können Sie [Verwenden von Notification Hubs zum Übermitteln von Nachrichten] lesen. Weitere Informationen zur Verwendung von Benachrichtigungshubs finden Sie in der [Benachrichtigungshubs-Anleitung].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Benachrichtigungshubs-Anleitung]: http://msdn.microsoft.com/library/jj927170.aspx
[Benachrichtigen von Benutzern mit Notification Hubs]: notification-hubs-aspnet-backend-android-notify-users.md
[Verwenden von Notification Hubs zum Übermitteln von Nachrichten]: notification-hubs-aspnet-backend-android-breaking-news.md
 

<!---HONumber=July15_HO4-->