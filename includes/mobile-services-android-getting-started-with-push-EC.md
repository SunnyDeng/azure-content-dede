1. Öffnen Sie im App-Projekt die Datei `AndroidManifest.xml`. Ersetzen Sie im Code in den nächsten beiden Schritten _`**my_app_package**`_ durch den Namen des App-Pakets für Ihr Projekt, der dem Wert des `package`-Attributs des `manifest`-Tags entspricht. 

2. Fügen Sie die folgenden neuen Berechtigungen nach dem vorhandenen `uses-permission`-Element ein:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Fügen Sie den folgenden Code nach dem Starttag `application` ein:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Laden Sie den [Mobile Services Android SDK] herunter, und entzippen Sie ihn. Öffnen Sie den Ordner **Benachrichtigungen**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.

    > [AZURE.NOTE]Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.

5.  Öffnen Sie die Datei *ToDoItemActivity.java*, und fügen Sie den folgenden Import-Ausdruck ein:

		import com.microsoft.windowsazure.notifications.NotificationsManager;


6. Fügen Sie der Klasse die folgende private Variable hinzu: Ersetzen Sie _`<PROJECT_NUMBER>`_ durch die Projektnummer, die Ihrer App im vorherigen Vorgang von Google zugewiesen wurde:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. Ändern Sie die Definition von *MobileServiceClient* von **private** in **public static**, sodass sie jetzt wie folgt aussieht:

		public static MobileServiceClient mClient;



9. Als Nächstes müssen wir eine neue Klasse zum Behandeln von Benachrichtigungen hinzufügen. Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und dann auf **Klasse**.

10. Geben Sie `MyHandler` für **Name** ein, geben Sie in **Übergeordnete Klasse** `com.microsoft.windowsazure.notifications.NotificationsHandler` ein, und klicken Sie dann auf **Fertig stellen**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

	Daraufhin wird die neue MyHandler-Klasse erstellt.

11. Fügen Sie die folgenden import-Anweisungen für die `MyHandler`-Klasse hinzu:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. Fügen Sie als Nächstes die folgenden Member für die `MyHandler`-Klasse hinzu:

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. Fügen Sie den folgende Code in die `MyHandler`-Klasse ein, um die **onRegistered**-Methode zu überschreiben, mit der das Gerät im Benachrichtigungs-Hub des mobilen Dienstes registriert wird.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. Fügen Sie in der `MyHandler`-Klasse den folgenden Code zum Überschreiben der **onReceive**-Methode hinzu, wodurch die Benachrichtigung nach ihrem Empfang angezeigt wird.

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
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


15. Aktualisieren Sie in der Datei „TodoActivity.java“ die **onCreate**-Methode der *ToDoActivity*-Klasse, um die Benachrichtigungsbehandlungsklasse zu registrieren. Stellen Sie sicher, dass dieser Code nach der Instanziierung von *MobileServiceClient* hinzufügt wird.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Ihre App kann Pushbenachrichtigungen nun unterstützen.

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=July15_HO3-->