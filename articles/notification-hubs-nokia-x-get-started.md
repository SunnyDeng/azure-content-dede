<properties linkid="develop-notificationhubs-tutorials-get-started-nokia-x" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="kirillg" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo" />

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von **Azure Notification Hubs** eine Pushbenachrichtigung an eine Android-App auf **Nokia X** senden. In diesem Lernprogramm erstellen Sie eine leere Android-App, die Pushbenachrichtigungen über den Nokia Mitteilungsdienst empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

-   [Konfigurieren des Nokia Mitteilungsdiensts][Konfigurieren des Nokia Mitteilungsdiensts]
-   [Konfigurieren Ihres Notification Hub][Konfigurieren Ihres Notification Hub]
-   [Verbinden Ihrer App mit dem Notification Hub][Verbinden Ihrer App mit dem Notification Hub]
-   [Versenden von Benachrichtigungen an die App][Versenden von Benachrichtigungen an die App]
-   [Testen der App][Testen der App]

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschlie&szlig;en. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

Für dieses Lernprogramm ist Folgendes erforderlich:

1.  Nokia X Entwicklungsumgebung, die Sie mit den [hier][hier] aufgeführten Anweisungen konfigurieren können. Achten Sie darauf, die für Nokia X spezifischen Pakete zu installieren, und folgen Sie der Anleitung zu Einrichten des Nokia X Emulators.
2.  Nokia X Geräteeinrichtung (optional), die Sie mit den [hier][1] aufgeführten Anweisungen konfigurieren können.
3.  Android-SDK (es wird davon ausgegangen, dass Sie Eclipse verwenden), das Sie [hier][2] herunterladen können.
4.  Mobile Services Android SDK, das Sie [hier][3]<a> herunterladen können.

## <span id="register"></span></a>Konfigurieren des Nokia Mitteilungsdiensts

1.  Melden Sie sich an der [Nokia Notifications API Developer Console][Nokia Notifications API Developer Console] an.

2.  Gehen Sie zur Registerkarte **Create services**, und erstellen Sie einen neuen Dienstanbieter, indem Sie eine **Sender ID** und eine **Service description** angeben.

    ![][0]

3.  Notieren Sie die **Sender ID** und den **Authorization Key**, nachdem der Dienst erfolgreich erstellt wurde.

4.  Sie können zur Registerkarte **My services** gehen, wo alle Ihre erstellten Dienste mit der jeweiligen **Sender ID** und dem **Authorization Key** aufgeführt sind.

    ![][4]

5.  Weitere Details finden Sie unter diesem [Link][Link].

## <span id="configure-hub"></span></a>Konfigurieren Ihres Notification Hub

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

    ![][5]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region und optional den Namespace aus, und klicken Sie dann auf **Neuen Benachrichtigungs-Hub erstellen**.

    ![][6]

4.  Gehen Sie zum eben erstellten Notification Hub. Klicken Sie auf die Registerkarte „Service Bus“ links und dann auf den Namespace, in dem Sie den Notification Hub erstellt haben, und klicken Sie dann auf die Registerkarte „Benachrichtigungs-Hubs“.

    ![][7]

5.  Wenn Sie auf dem von Ihnen erstellten Notification Hub sind, klicken Sie auf die Registerkarte **Konfigurieren** oben.

    ![][8]

6.  Scrollen Sie nach unten, suchen Sie nach **nokia x notification settings**, und fügen Sie den Autorisierungsschlüssel ein, den Sie vom Nokia Notification Service erhalten haben; klicken Sie dann auf die Schaltfläche **Speichern** unten auf der Seite.

    ![][9]

7.  Wählen Sie die Registerkarte **Dashboard** oben aus, und klicken Sie unten auf der Seite auf **Verbindungsinformation**.

    ![][10]

8.  Notieren Sie sich die beiden SAS-Verbindungszeichenfolgen für **DefaultListenSharedAccessSignature** und **DefaultFullSharedAccessSignature**, die Sie später im Lernprogramm benötigen.

## <span id="connect-hub"></span></a>Verbinden Ihrer App mit dem Notification Hub

### Erstellen eines neuen Android-Projekts

1.  Erstellen Sie in Eclipse ADT ein neues Android-Projekt (File -\> New -\> Android Application).

2.  Achten Sie darauf, dass das Minimum Required SDK auf **API 16: Android 4.1 (Jelly Bean)** festgelegt ist und dass die nächsten zwei SDK-Einträge auf die neueste verfügbare Version festgelegt sind. Wählen Sie „Next“, und folgen Sie dem Assistenten. Vergewissern Sie sich, dass **Create activity** ausgewählt ist, um eine **leere Aktivität** zu erstellen. Akzeptieren Sie im nächsten Feld das Standardsymbol für das Startprogramm, und klicken Sie im letzten Feld auf **Finish**.

    ![][11]

3.  Stellen Sie sicher, dass das Build-Ziel des Projekts korrekt eingestellt ist (Platform = 4.1.2 und API Level = 16 für dieses Beispiel). Klicken Sie mit der rechten Maustaste auf „Projekt“, wählen Sie „Eigenschaften“ aus, und wählen Sie „Android“ im Dialogfeld „Projekteigenschaften“ aus.

    ![][12]

4.  Fügen Sie die JAR-Datei des Nokia Mitteilungsdiensts dem Projekt hinzu. Die Nokia Notifications-Hilfsbibliothek **push.jar** stellt eine API bereit, die mit der GCM-Hilfsbibliothek vergleichbar ist.
    Navigieren Sie zu „Project Properties“ -\> „Java Build Path“ -\> „Libraries“ -\> „Add External JARs“, und fügen Sie **push.jar** hinzu, das in **\\extras\\nokia\\nokia\_x\_services\\libs\\nna\\push.jar** verfügbar ist. Das Javadoc für diese Bibliothek befindet sich im Verzeichnis **\\extras\\nokia\\nokia\_x\_services\\javadocs\\nna**, in dem Eclipse IDE installiert ist.

5.  Achten Sie darauf, dass Sie im Paket-Explorer auch diese push-jar-Bibliothek in das Verzeichnis \\libs Ihres Projekts kopieren.

6.  Laden Sie [hier][3] das Notification Hubs Android SDK herunter. Extrahieren Sie die .zip-Datei, und kopieren Sie die Datei "notificationhubs\\notification-hubs-0,3.jar" in das Verzeichnis \\libs des Projekts im Paket-Explorer.

    <div class="dev-callout"><b>Hinweis</b>
<p>Die Nummern am Ende des Dateinamens k&ouml;nnen sich in den nachfolgenden SDK-Versionen &auml;ndern.</p>
</div>

### Hinzufügen des Codes

1.  Öffnen Sie die Datei **AndroidManifest.xml**, und ersetzen Sie das Anwendungselement durch folgende Codezeilen. Achten Sie dabei darauf, dass Sie `[YourPackageName]` durch den Namen Ihres Pakets ersetzen, den Sie beim Erstellen der Android-Anwendung festgelegt haben.

        <!-- Push Notifications connects to Internet services. -->
        <uses-permission android:name="android.permission.INTERNET" />

        <!-- Keeps the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

         <!--
         Creates a custom permission so only this app can receive its messages.

         NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
               where PACKAGE is the application's package name.
        -->
        <permission 
            android:name="[YourPackageName].permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />

        <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />

        <application
            android:allowBackup="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name="[YourPackageName].MainActivity"
                android:label="@string/app_name" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>

            <receiver
                android:name="com.nokia.push.PushBroadcastReceiver"
                android:permission="com.nokia.pushnotifications.permission.SEND" >
                <intent-filter>

                    <!-- Receives the actual messages. -->
                    <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
                    <!-- Receives the registration id. -->
                    <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
                    <category android:name="[YourPackageName]" />
                </intent-filter>
            </receiver>

            <service android:name="[YourPackageName].PushIntentService" />
        </application> 

2.  Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem `src`-Knoten), klicken Sie auf **New**, dann auf **Class**, und erstellen Sie eine neue Klasse namens **ConfigurationSettings.java**.
    ![][13]

    Fügen Sie dann folgenden Code hinzu, der die im Beispielcode verwendeten Konstanten definiert:

        public class ConfigurationSettings {
            public static String NotificationHubName = "";
            public static String NotificationHubConnectionString = "";
            public static String SenderId = "";
        }

    Füllen Sie die obigen Konstanten mit der **SenderId** Ihrer Konfiguration von der Nokia Pushkonsole und **NotificationHubName** sowie **NotificationHubConnectionString** (DefaultListenSharedAccessSignature) aus dem Verwaltungsportal.

3.  Fügen Sie in der Datei **MainActivity.java** die folgende import-Anweisung hinzu:

        import com.nokia.push.PushRegistrar;

    Fügen Sie dann folgenden Code in der onCreate-Methode hinzu:

        // Make sure the device has the proper dependencies.
        PushRegistrar.checkDevice(this);

        // Make sure the manifest was properly set - comment out this line
        // while developing the app, then uncomment it when it's ready
        PushRegistrar.checkManifest(this);

        // Register the device with the Nokia Notification service
        PushRegistrar.register(this, ConfigurationSettings.SenderId);

4.  Fügen Sie eine weitere neue Klasse namens **PushIntentService.java** hinzu, und fügen Sie den folgenden Code hinzu, der bei Ihrem Notification Hub registriert wird und außerdem die empfangenen Nachrichten dieses Notification Hubs verarbeitet.

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import android.util.Log;

        import com.microsoft.windowsazure.messaging.NotificationHub;
        import com.nokia.push.PushBaseIntentService;

        /**
         * IntentService responsible for handling push notification messages.
         */
        public class PushIntentService extends PushBaseIntentService {

            private NotificationManager mNotificationManager;
            private static NotificationHub hub;
            public static final int NOTIFICATION_ID = 1;
            private static final String TAG = "NokiaXPush/PushIntentService";

            /**
             * Constructor.
             */
            public PushIntentService() {
            }

            @Override
            protected String[] getSenderIds(Context context) {
                return new String[] { ConfigurationSettings.SenderId };
            }   

            @Override
            protected void onRegistered(Context context, String registrationId) {
                Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
                RegisterWithNotificationHub(context, registrationId);
            }

            public static void RegisterWithNotificationHub(Context context, String registrationId) {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName, 
                            ConfigurationSettings.NotificationHubConnectionString, 
                            context);
                }
                try {
                    hub.register(registrationId);
                    Log.i(TAG, "Registered with Notification Hub - '" 
                            + ConfigurationSettings.NotificationHubName + "'" 
                            + " with RegistrationID - '"
                            + registrationId + "'");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            protected void onMessage(Context context, Intent intent) {
                String message = intentExtrasToString(intent.getExtras());
                Log.i(TAG, "Received message. Extras: " + message);
                generateNotification(context, message);
            }

            /**
             * Extracts the key-value pairs from the given Intent extras and returns
             * them in a string.
             */
            private String intentExtrasToString(Bundle extras) {
                StringBuilder sb = new StringBuilder();
                sb.append("{ ");

                for (String key : extras.keySet()) {
                    sb.append(sb.length() <= 2 ? "" : ", ");
                    sb.append(key).append("=").append(extras.get(key));
                }

                sb.append(" }");
                return sb.toString();
            }

            /**
             * Issues a notification to inform the user that server has sent a message.
             */
            private void generateNotification(Context context, String message)
            {
                mNotificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);

                PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
                    new Intent(context, MainActivity.class), 0);

                NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(context)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle("Notification Hub Demo")
                    .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(message))
                    .setContentText(message);

                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }

            @Override
            protected void onError(Context arg0, String errorId) {
                Log.i(TAG, "Received error:  " + errorId);
            }

            @Override
            protected void onUnregistered(Context arg0, String errorId) {
                Log.i(TAG, "Received recoverable error: " + errorId);
            }
        }

## <a name="send"></a>Versenden von Benachrichtigungen an die App

Sie können Benachrichtigungen mit Notification Hubs von jedem Back-End aus versenden, das unsere [REST-Schnittstelle][REST-Schnittstelle] verwendet. In diesem Lernprogramm zeigen wir, wie Sie eine .NET-Konsolenanwendung verwenden.

1.  Erstellen einer neuen Visual C#-Konsolenanwendung:

    ![][14]

2.  Fügen Sie mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets][WindowsAzure.ServiceBus NuGet-Pakets] eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Package Manager Console**. Geben Sie dann Folgendes im Konsolenfenster ein, und drücken Sie die EINGABETASTE:

        Install-Package WindowsAzure.ServiceBus

3.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

4.  Fügen Sie in der `Program`-Klasse folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
            await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
        }

5.  Fügen Sie dann folgende Zeilen zur Main-Methode hinzu:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-app"></a>Testen der App

Um die Anwendung mit einem Telefon zu testen, müssen Sie dieses über ein USB-Kabel mit Ihrem Computer verbinden.

So testen Sie diese App mit dem Emulator:

1.  Klicken Sie in der oberen Eclipse-Symbolleiste auf „Ausführen“, und wählen Sie die App aus.

2.  Die App wird entweder direkt auf das verbundene Telefon geladen, oder der Emulator wird gestartet und lädt die App und führt sie aus.

3.  Die App ruft die „registrationId“ vom Nokia Mitteilungsdienst ab und registriert sich beim Notification Hub.

    <div class="dev-callout"><b>Hinweis</b>
<p>
Wenn sich die Android-App erfolgreich beim Notification Hub registrieren kann, sehen Sie in den &bdquo;Eclipse Logcat&ldquo;-Protokollen eine Nachricht wie die folgende:
&bdquo;Registered with Notification Hub - '<yourNotificationHubName>' with RegistrationID - &ldquo;<RegistrationIdReturnedByNokiaNotificationService'' </p>
</div>

4.  Um bei Verwendung der .NET-Konsolenanwendung eine Testbenachrichtigung zu senden, drücken Sie in Visual Studio die Taste F5, um die Anwendung auszuführen. Hierdurch wird eine Benachrichtigung gesendet, die oben im Benachrichtigungsbereich Ihres Geräts oder Emulators angezeigt wird.

 

  [Konfigurieren des Nokia Mitteilungsdiensts]: #register
  [Konfigurieren Ihres Notification Hub]: #configure-hub
  [Verbinden Ihrer App mit dem Notification Hub]: #connect-hub
  [Versenden von Benachrichtigungen an die App]: #send
  [Testen der App]: #test-app
  [hier]: http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html
  [1]: http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html
  [2]: http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409
  [3]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Nokia Notifications API Developer Console]: https://console.push.nokia.com/ncm/Web/index.jsp
  [0]: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
  [4]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
  [Link]: http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [5]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
  [6]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
  [7]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
  [8]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
  [9]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
  [10]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
  [11]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
  [12]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png
  [13]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
  [REST-Schnittstelle]: http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx
  [14]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
  [WindowsAzure.ServiceBus NuGet-Pakets]: http://nuget.org/packages/WindowsAzure.ServiceBus/
