1.  Öffnen Sie die Datei `AndroidManifest.xml`. Ersetzen Sie im Code in den nächsten beiden Schritten *`**my_app_package**`* durch den Namen des App-Pakets für Ihr Projekt, der dem Wert des Attributs `package` des Tags `manifest` entspricht.

2.  Fügen Sie die folgenden neuen Berechtigungen nach dem vorhandenen Element `uses-permission` ein:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  Fügen Sie den folgenden Code nach dem Starttag `application` ein:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

4.  Laden Sie den [Mobile Services Android SDK][Mobile Services Android SDK] herunter, und entzippen Sie ihn. Öffnen Sie den Ordner **Benachrichtigungen**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.

    <div class="dev-callout"><b>Hinweis</b>
<p>Die Nummern am Ende des Dateinamens k&ouml;nnen sich in den nachfolgenden SDK-Versionen &auml;ndern.</p>
</div>

5.  Öffnen Sie die Datei *ToDoItemActivity.java*, und fügen Sie den folgenden Import-Ausdruck ein:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  Fügen Sie die folgende private Variable zu der Klasse hinzu. *`<PROJECT_NUMBER>`* steht für die Projektnummer, die Ihrer App im vorherigen Vorgang durch Google zugewiesen wurde:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  Bevor der MobileServiceClient instanziiert wird, fügen Sie in der Methode **onCreate** diesen Code hinzu. Er registriert die Benachrichtigungsbehandlung für das Gerät:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Zu einem späteren Zeitpunkt definieren wir die MyHandler-Klasse, auf die in diesem Code verwiesen wird.

8.  Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und dann auf **Klasse**.

9.  Geben Sie für **Name** `MyHandler` ein, geben Sie in **Übergeordnete Klasse** `com.microsoft.windowsazure.notifications.NotificationsHandler` ein, und klicken Sie dann auf **Fertig stellen**.

    ![][0]

    Daraufhin wird die neue MyHandler-Klasse erstellt.

10. Fügen Sie die folgenden import-Anweisungen für die Klasse `MyHandler` ein:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

        import com.microsoft.windowsazure.mobileservices.Registration;
        import com.microsoft.windowsazure.mobileservices.RegistrationCallback;

11. Fügen Sie als Nächstes die folgenden Member für die Klasse `MyHandler` ein:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. In der `MyHandler`-Klasse fügen Sie den folgenden Code zum Überschreiben der **onRegistered**-Methode hinzu: wodurch Ihr Gerät im Benachrichtigungshub des mobilen Diensts registriert wird.

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // handle error
                      }
                }
            });
        }

13. In der `MyHandler`-Klasse fügen Sie den folgenden Code zum Überschreiben der **onReceive**-Methode hinzu, wodurch die Benachrichtigung nach ihrem Empfang angezeigt wird.

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

Ihre App kann Pushbenachrichtigungen nun unterstützen.



  [Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
