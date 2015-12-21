<properties
	pageTitle="Erste Schritte mit Azure Notification Hubs für Android-Apps | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an Android-Geräte senden."
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
	ms.topic="hero-article"
	ms.date="11/25/2015"
	ms.author="wesmc"/>

# Erste Schritte mit Notification Hubs für Android-Apps

[AZURE.INCLUDE [Notification-Hubs-Auswahl-Erste-Schritte](../../includes/notification-hubs-selector-get-started.md)]

##Übersicht

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Android-App senden. Sie erstellen eine leere Android-App, die Pushbenachrichtigungen mithilfe von Google Cloud Messaging (GCM) empfängt. Sobald Sie dieses Tutorial abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

Das Tutorial zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. Bearbeiten Sie auch das nachfolgende Tutorial, um mehr über die Verwendung von Notification Hubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.


## Voraussetzungen

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Den vollständigen Code für dieses Tutorial finden Sie [hier](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted) auf GitHub.


##Voraussetzungen

Für dieses Tutorial ist Folgendes erforderlich:

+ Android Studio, das Sie von der <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android-Website</a> herunterladen können.
+ Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).


Das Abschließen dieses Tutorial ist eine Voraussetzung für alle anderen Notification Hubs-Tutorials für Android-Apps.


##Erstellen eines Projekts, das Google Cloud Messaging unterstützt

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##Konfigurieren eines neuen Notification Hubs


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;7. Klicken Sie auf die Registerkarte **Configure**, geben Sie den Wert **API Key** ein, den Sie im vorherigen Abschnitt erhalten haben, und klicken Sie auf **Save**.

&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App für den Empfang von Benachrichtigungen zu registrieren und Pushbenachrichtigungen zu versenden.

##<a id="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

###Erstellen eines neuen Android-Projekts

1. Starten Sie in Android Studio ein neues Android Studio-Projekt.

   	![][13]

2. Wählen Sie den Formfaktor **Phone and Tablet** und das **Minimum SDK** aus, das unterstützt werden soll. Klicken Sie auf **Next**.

   	![][14]

3. Wählen Sie **Blank Activity** als Hauptaktivität aus, klicken Sie auf **Weiter**, und anschließend auf **Fertig stellen**.

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Hinzufügen von Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Hinzufügen des Codes

1. Laden Sie die Datei „notification-hubs-0.4.jar“ aus dem [Notification-Hubs-Android-SDK auf Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4) auf der Registerkarte **Files** herunter. Laden Sie auch [notifications-1.0.1.jar](https://bintray.com/microsoftazuremobile/SDK/Notifications-Handler/view) in das Verzeichnis **app\\libs** Ihres Projekts herunter. Dazu können Sie die Dateien direkt in den Ordner **libs** im Fenster "Project View" von Android Studio ziehen. Aktualisieren Sie den Ordner **libs**.


    > [AZURE.NOTE] Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.

2. Richten Sie ein, dass die Anwendung eine Registrierungs-ID von GCM erhält, und verwenden Sie diese, um die App-Instanz beim Notification Hub zu registrieren.

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
	    private static Boolean isVisible = false;


	Aktualisieren Sie die drei Platzhalter:
	* **SENDER\_ID**: Setzen Sie `SENDER_ID` auf die Projektnummer, die Sie zuvor von dem in der [Google Cloud Console](http://cloud.google.com/console) erstellten Projekt abgerufen haben.
	* **HubListenConnectionString**: Legen Sie für `HubListenConnectionString` die Verbindungszeichenfolge **DefaultListenAccessSignature** für den Hub fest. Sie können diese Verbindungszeichenfolge kopieren, indem Sie im [Azure-Portal] auf der Registerkarte **Dashboard** Ihres Hubs auf **Verbindungszeichenfolge anzeigen** klicken.
	* **HubName**: Verwenden Sie den Namen Ihres Notification Hubs, der oben auf der Seite in Azure angezeigt wird (**nicht** die vollständige URL). Verwenden Sie z. B. `"myhub"`.



5. Fügen Sie in der **OnCreate**-Methode der **MainActivity**-Klasse den folgenden Code zur Registrierung beim Notification Hub hinzu, wenn die Aktivität erstellt wird.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. Fügen Sie in **MainActivity.java** den folgenden Code für die **registerWithNotificationHubs()**-Methode hinzu. Diese Methode meldet die erfolgreiche Durchführung nach der Registrierung bei Google Cloud Messaging und beim Notification Hub.

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


7. Fügen Sie der Aktivität die `DialogNotify`-Methode hinzu, um die Benachrichtigung anzuzeigen, wenn die App ausgeführt wird und sichtbar ist. Überschreiben Sie außerdem `onStart`,`onPause`,`onResume` und `onStop`, um zu ermitteln, ob die Aktivität sichtbar ist und das Dialogfeld anzeigen kann.

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }

		/**
		  * A modal AlertDialog for displaying a message on the UI thread
		  * when there's an exception or message to report.
		  *
		  * @param title   Title for the AlertDialog box.
		  * @param message The message displayed for the AlertDialog box.
		  */
    	public void DialogNotify(final String title,final String message)
    	{
	        if (isVisible == false)
	            return;

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

8. Da Android keine Benachrichtigungen anzeigt, müssen Sie einen eigenen Empfänger schreiben. Fügen Sie in **AndroidManifest.xml** das folgende Element im `<application>`-Element ein.

	> [AZURE.NOTE] Ersetzen Sie den Platzhalter mit dem Namen Ihres Pakets.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. Erweitern Sie in der Projektansicht die Knoten **app** > **src** > **main** > **java**. Klicken Sie mit der rechten Maustaste auf Ihren Paketordner unter **java**, klicken Sie auf **Neu** und dann auf **Java Class**.

	![][6]

10. Geben Sie im Feld **Name** für die neue Klasse **MyHandler** ein, und klicken Sie dann auf **OK**.


11. Fügen Sie die folgenden Importanweisungen am Anfang der Datei **MyHandler.java** ein:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


12. Aktualisieren Sie die Klassendeklaration wie folgt, um `MyHandler` wie unten dargestellt als Unterklasse von `com.microsoft.windowsazure.notifications.NotificationsHandler` festzulegen.

		public class MyHandler extends NotificationsHandler {


13. Fügen Sie den folgenden Code für die `MyHandler`-Klasse hinzu.

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

14. Klicken Sie in Android Studio auf der Menüleiste auf **Build** -> **Rebuild Project**, um sicherzustellen, dass keine Fehler gefunden werden.

##Senden von Benachrichtigungen



Sie können den Empfang von Benachrichtigungen in Ihrer App testen, indem Sie wie im Bildschirm unten abgebildet Benachrichtigungen im [klassischen Azure-Portal] über die Registerkarte zum Debuggen im Notification Hub senden.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (Optional) Senden von Benachrichtigungen aus der App


1. Erweitern Sie in der Projektansicht von Android Studio **App** > **src** > **main** > **res** > **layout**. Öffnen Sie die Layoutdatei **activity\_main.xml**, und klicken Sie auf die Registerkarte **Text**, um den Textinhalt der Datei zu aktualisieren. Ändern Sie ihn in den folgenden Code, mit dem ein neues `Button`- und `EditText`-Steuerelement zum Senden von Benachrichtigungsmeldungen an den Notification Hub hinzugefügt wird. Fügen Sie diesen Code am Ende der Datei unmittelbar vor `</RelativeLayout>` hinzu.

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

2. Erweitern Sie in der Projektansicht von Android Studio **App** > **src** > **main** > **res** > **values**. Öffnen Sie die Datei **strings.xml**, und fügen Sie die Zeichenfolgenwerte hinzu, auf die von dem neuen `Button`- und `EditText`-Steuerelement verwiesen wird. Fügen Sie die Werte am Ende der Datei unmittelbar vor `</resources>` ein.

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

	Aktualisieren Sie `HubFullAccess` mit der Verbindungszeichenfolge **DefaultFullSharedAccessSignature** für Ihren Hub. Diese Verbindungszeichenfolge kann aus dem [klassischen Azure-Portal] kopiert werden. Klicken Sie dazu auf der Registerkarte **Dashboard** für Ihren Notification Hub auf **Verbindungszeichenfolge anzeigen**.

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
         * @param uri The unencoded resource URI string for this operation. The resource
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
	            // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

                // Construct authorization string
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

                        // Authenticate the POST request with the SaS token
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



##Testen Ihrer App

####Testen mit einem Emulator

Wenn Sie die App mit einem Emulator testen möchten, müssen Sie sicherstellen, dass das Emulatorabbild die Google-API-Ebene unterstützt, die Sie für die API auswählen. Wenn das Abbild die Google-APIs nicht unterstützt, wird die Ausnahme **SERVICE\_NOT\_AVAILABLE** ausgegeben.

Stellen Sie außerdem sicher, dass Ihr Google-Konto dem ausgeführten Emulator unter **Einstellungen** > **Konten** hinzugefügt ist. Andernfalls führt die Registrierung bei GCM möglicherweise zur Ausnahme **AUTHENTICATION\_FAILED**.

####Testen der App

1. Führen Sie die App aus, und prüfen Sie, ob die Registrierungs-ID als erfolgreiche Registrierung gemeldet wird.

   	![][18]

2. Geben Sie eine Benachrichtigungsmeldung ein, die an alle für den Hub registrierten Android-Geräte gesendet werden soll.

   	![][19]

3. Tippen Sie auf **Send Notification**. Auf allen Geräten, auf denen die App ausgeführt wird, wird ein `AlertDialog` mit der Benachrichtigungsmeldung angezeigt. Auf Geräten, auf denen die App nicht ausgeführt wird, die jedoch zuvor für Benachrichtigungen registriert wurden, wird im Benachrichtigungs-Manager eine Benachrichtigung hinzugefügt. Benachrichtigungen können durch Streichen von der linken oberen Ecke nach unten angezeigt werden.

   	![][21]

##Nächste Schritte

In diesem einfachen Beispiel haben Sie Übertragungsbenachrichtigungen mit dem Portal oder einer Konsolen-App an alle Ihre Windows-Geräte gesendet. Als nächster Schritt wird das Tutorial [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer] empfohlen. Darin wird beschrieben, wie Sie Benachrichtigungen von einem ASP.NET-Back-End für bestimmte Benutzer mithilfe von Tags senden.

Wenn Sie Ihre Benutzer nach Interessengruppen unterteilen möchten, lesen Sie [Verwenden von Benachrichtigungshubs zum Senden von neuesten Nachrichten].

Weitere allgemeine Informationen zu Notification Hubs finden Sie im [Notification Hubs-Leitfaden].




<!-- Images. -->
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
[Erste Schritte mit Pushbenachrichtigungen in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[klassischen Azure-Portal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: notification-hubs-aspnet-backend-android-notify-users.md
[Verwenden von Benachrichtigungshubs zum Senden von neuesten Nachrichten]: notification-hubs-aspnet-backend-android-breaking-news.md

<!---HONumber=AcomDC_1210_2015-->
