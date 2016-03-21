1. Öffnen Sie im **App**-Projekt die Datei `AndroidManifest.xml`. Ersetzen Sie im Code in den nächsten beiden Schritten _`**my_app_package**`_ durch den Namen des App-Pakets für Ihr Projekt, der dem Wert des `package`-Attributs des `manifest`-Tags entspricht.

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


4. Öffnen Sie die Datei *ToDoActivity.java*, und fügen Sie die folgende Import-Anweisung ein:

		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Fügen Sie der Klasse die folgende private Variable hinzu: Ersetzen Sie _`<PROJECT_NUMBER>`_ durch die Projektnummer, die Ihrer App im vorherigen Vorgang von Google zugewiesen wurde:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

6. Ändern Sie die Definition von *MobileServiceClient* von **private** in **public static**, sodass sie jetzt wie folgt aussieht:

		public static MobileServiceClient mClient;

7. Als Nächstes müssen wir eine neue Klasse zum Behandeln von Benachrichtigungen hinzufügen. Öffnen Sie im Projektexplorer den **src** => **main** => **java**-Knoten, und klicken Sie mit der rechten Maustaste auf den Paketnamenknoten: Klicken Sie auf **Neu** und dann auf **Java-Klasse**.

8. Geben Sie in **Name** die Zeichenfolge `MyHandler` ein, und klicken Sie dann auf **OK**.


	![](./media/mobile-services-android-get-started-push/android-studio-create-class.png)


9. Ersetzen Sie in der Datei "MyHandler" die Klassendeklaration durch

		public class MyHandler extends NotificationsHandler {


10. Fügen Sie die folgenden import-Anweisungen für die `MyHandler`-Klasse hinzu:

		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;


11. Als Nächstes fügen Sie der `MyHandler`-Klasse dieses Element hinzu:

		public static final int NOTIFICATION_ID = 1;


12. Fügen Sie den folgende Code in die `MyHandler`-Klasse ein, um die **onRegistered**-Methode zu überschreiben, mit der das Gerät im Benachrichtigungs-Hub des mobilen Dienstes registriert wird.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);

		    new AsyncTask<Void, Void, Void>() {

		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId);
		    		    return null;
	    		    }
	    		    catch(Exception e) {
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}


13. Fügen Sie in der `MyHandler`-Klasse den folgenden Code zum Überschreiben der **onReceive**-Methode hinzu, wodurch die Benachrichtigung nach ihrem Empfang angezeigt wird.

		@Override
		public void onReceive(Context context, Bundle bundle) {
        		String msg = bundle.getString("message");

        		PendingIntent contentIntent = PendingIntent.getActivity(context,
                		0, // requestCode
                		new Intent(context, ToDoActivity.class),
                		0); // flags

        		Notification notification = new NotificationCompat.Builder(context)
                		.setSmallIcon(R.drawable.ic_launcher)
                		.setContentTitle("Notification Hub Demo")
                		.setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                		.setContentText(msg)
                		.setContentIntent(contentIntent)
                		.build();

        		NotificationManager notificationManager = (NotificationManager)
                		context.getSystemService(Context.NOTIFICATION_SERVICE);
        		notificationManager.notify(NOTIFICATION_ID, notification);
		}


14. Aktualisieren Sie in der Datei „TodoActivity.java“ die **onCreate**-Methode der *ToDoActivity*-Klasse, um die Benachrichtigungsbehandlungsklasse zu registrieren. Stellen Sie sicher, dass dieser Code nach der Instanziierung von *MobileServiceClient* hinzufügt wird.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Ihre App kann Pushbenachrichtigungen nun unterstützen.

<!---HONumber=AcomDC_0309_2016-->