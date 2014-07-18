<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services
============================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe des Google Cloud Messaging (GCM)-Service zu dem Schnellstartprojekt hinzu. Sobald Sie diesen Vorgang abgeschlossen haben, sendet Ihr Mobile Service jedes Mal eine Pushbenachrichtigung, wenn ein Eintrag eingefügt wird.

> [WACOM.NOTE] In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen mithilfe der Legacyunterstützung von Mobile Services aktivieren. Azure Notification Hubs ist in Mobile Services integriert, damit Sie vorlagenbasierte und plattformübergreifende Pushbenachrichtigungen an Millionen von Geräte senden können. Standardmäßig sind Pushbenachrichtigungen unter Verwendung von Notification Hubs nicht aktiviert, und es gibt derzeit keine Notification Hub-Unterstützung für Android in den Mobile Services-Bibliotheken. Sie können jedoch unter Verwendung der Notification Hub-Bibliotheken Pushbenachrichtigungen von Ihrem Mobile Service senden. Weitere Informationen finden Sie unter [Erste Schritte mit Notification Hubs](/en-us/documentation/articles/notification-hubs-android-get-started/).

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen:

-   [Aktivieren von Google Cloud Messaging](#register)
-   [Konfigurieren von Mobile Services](#configure)
-   [Hinzufügen von Pushbenachrichtigungen zu Ihrer App](#add-push)
-   [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen](#update-scripts)
-   [Einfügen von Daten zum Erhalten von Benachrichtigungen](#test)

Voraussetzungen für dieses Lernprogramm:

-   [Mobile Services Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)
-   Ein aktives Google-Konto

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart. Bevor Sie dieses Lernprogramm starten, müssen Sie zunächst [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started-android) abschließen.

Aktivieren von Google Cloud Messaging
-------------------------------------

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Zunächst verwenden Sie diesen API-Schlüsselwert, um Mobile Services für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

Konfigurieren von Mobile Services zum Senden von Pushanforderungen
------------------------------------------------------------------

1.  Melden Sie sich beim [Azure Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

        ![][18]

2.  Klicken Sie auf die Registerkarte **Push**, geben Sie den Wert **API Key** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

        ![][19]

Ihre Mobile Service ist jetzt konfiguriert, um mit GCM Pushbenachrichtigungen zu senden.

Hinzufügen von Pushbenachrichtigungen zu Ihrer App
--------------------------------------------------

### Hinzufügen von Google Play Services zum Projekt

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Code hinzufügen

1.  Öffnen Sie die Projektdatei **AndroidManifest.xml**. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss. Konsultieren Sie [Set Up Google Play Services SDK](http://go.microsoft.com/fwlink/?LinkId=389801) (Einrichten des Google Play Services SDK, in englischer Sprache), um zu ermitteln, wie niedrig Sie diesen Wert einstellen können, bzw. ob Sie einen Wert unter 16 einstellen müssen, weil Sie ein älteres Gerät verwenden. Legen Sie die Eigenschaft entsprechend fest.

2.  Stellen Sie im Element `uses-sdk` sicher, dass für **targetSdkVersion** die Nummer einer SDK-Plattform festgelegt ist, die installiert wurde (Schritt 1). Es ist vorzuziehen, die neueste verfügbare Version zu verwenden.

3.  Der Tag **uses-sdk** sieht möglicherweise folgendermaßen aus, je nachdem, welche Auswahlen Sie in den vorherigen Schritten getroffen haben:

         <uses data-morhtml="true"-sdk android:minSdkVersion="17" android:targetSdkVersion="19" />

4.  Ersetzen Sie im Code in den nächsten beiden Schritten *`**my_app_package**`* durch den Namen des App-Pakets für Ihr Projekt, der dem Wert des Attributs `package` des Tags `manifest` entspricht.

5.  Fügen Sie die folgenden neuen Berechtigungen nach dem vorhandenen Element `uses-permission` ein:

         <permission android:name="**my_app_package**.permission.C2D_MESSAGE" android:protectionLevel="signature" /><uses data-morhtml="true"-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /><uses data-morhtml="true"-permission android:name="com.google.android.c2dm.permission.RECEIVE" /><uses data-morhtml="true"-permission android:name="android.permission.GET_ACCOUNTS" /><uses data-morhtml="true"-permission android:name="android.permission.WAKE_LOCK" />

6.  Fügen Sie den folgenden Code nach dem Starttag `application` ein:

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver" android:permission="com.google.android.c2dm.permission.SEND"><intent data-morhtml="true"-filter><action android:name="com.google.android.c2dm.intent.RECEIVE" /><category android:name="**my_app_package**" /></intent-filter></receiver>

7.  Öffnen Sie die Datei ToDoItem.java, fügen Sie den folgenden Code zur Klasse **TodoItem** hinzu:

             @com.google.gson.annotations.SerializedName("handle") private String mHandle; public String getHandle() { return mHandle; } public final void setHandle(String handle) { mHandle = handle; }

    Dieser Code erstellt eine neue Eigenschaft, die die Registrierungs-ID enthält.

    **Hinweis**

    Wenn für Ihren Mobile Services ein dynamisches Schema aktiviert ist, wird automatisch eine neue Spalte **Ziehpunkt** zur Tabelle **TodoItem** hinzugefügt, wenn ein neues Element, das diese Eigenschaft enthält, eingefügt wird.

8.  Laden Sie den [Mobile Services Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409) herunter, und entzippen Sie ihn. Öffnen Sie den Ordner **Benachrichtigungen**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.

    **Hinweis**

    Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.

9.  Öffnen Sie die Datei *ToDoItemActivity.java*, und fügen Sie den folgenden Import-Ausdruck ein:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Fügen Sie die folgende private Variable zu der Klasse hinzu. *`<PROJECT data-morhtml="true"_NUMBER>`* steht für die Projektnummer, die Ihrer App im vorherigen Vorgang durch Google zugewiesen wurde:

        public static final String SENDER_ID = "<PROJECT data-morhtml="true"_NUMBER>";

11. Bevor der MobileServiceClient instanziiert wird, fügen Sie in der Methode **onCreate** diesen Code hinzu. Er registriert die Benachrichtigungsbehandlung für das Gerät:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. Fügen Sie die folgende Codezeile zur Methode **addItem** hinzu, bevor das Element in die Tabelle eingefügt wird:

        item.setHandle(MyHandler.getHandle());

    Dieser Code legt für die Eigenschaft `handle` des Elements die Registrierungs-ID des Geräts fest.

13. Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und dann auf **Klasse**.

14. Geben Sie unter **Name** `MyHandler` ein, unter **Übergeordnete Klasse** `com.microsoft.windowsazure.notifications.NotificationsHandler`, und klicken Sie dann auf **Fertig stellen**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

    Daraufhin wird die neue MyHandler-Klasse erstellt.

15. Fügen Sie die folgenden Import-Ausdrücke ein:

        import android.content.Context; import com.microsoft.windowsazure.notifications.NotificationsHandler;

16. Fügen Sie den folgenden Code hinzu:

        @com.google.gson.annotations.SerializedName("handle") private static String mHandle; public static String getHandle() { return mHandle; } public static final void setHandle(String handle) { mHandle = handle; }

17. Ersetzen Sie die vorhandene Methodenüberschreibung **onRegistered** mit dem folgenden Code:

        @Override public void onRegistered(Context context, String gcmRegistrationId) { super.onRegistered(context, gcmRegistrationId); setHandle(gcmRegistrationId); }

Ihr App ist nun aktualisiert und unterstützt Pushbenachrichtigungen.

Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal
-------------------------------------------------------------------

1.  Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie auf die Tabelle **TodoItem**.

        ![][21]

2.  Klicken Sie in **todoitem** auf die Registerkarte **Skript**, und wählen Sie dann **Einfügen**.

![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png) Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

1.  Ersetzen Sie die Einfügefunktion mit dem folgenden Code, und klicken Sie auf **Speichern**:

         function insert(item, user, request) { request.execute({ success: function() { // Write to the response and then send the notification in the background request.respond(); push.gcm.send(item.handle, item.text, { success: function(response) { console.log('Push notification sent: ', response); }, error: function(error) { console.log('Error sending push notification: ', error); } }); } }); } Daraufhin wird ein neues Einfügeskript registriert, das [gcm object] zum Senden einer Pushbenachrichtigung (des eingefügten Texts) an das in der Einfügeanforderung angegebene Gerät verwendet.

Testen von Pushbenachrichtigungen in Ihrer App
----------------------------------------------

**Hinweis**

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1.  Starten Sie Eclipse neu, und klicken Sie dann im Paket-Explorer mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Eigenschaften**, dann auf **Android**, überprüfen Sie **Google APIs**, und klicken Sie dann auf **OK**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

Dies gibt das Projekt für die Google-APIs vor.

1.  Wählen Sie unter **Fenster** **Android Virtual Device Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Bearbeiten**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2.  Wählen Sie **Google APIs** in **Ziel** aus, und klicken Sie auf **OK**.

        ![][25]

    Dies gibt das AVD zur Verwendung von Google-APIs vor.

3.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten.

4.  Geben Sie in der App einen sinnvollen Text ein, beispielsweise *Eine neue Mobile Services-Aufgabe hinzufügen*, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

1.  Im unteren Bereich der Anzeige wird Ihnen kurz ein schwarzes Benachrichtigungsfeld angezeigt.

![](./media/mobile-services-android-get-started-push/mobile-push-icon.png)

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

Nächste Schritte
----------------

In diesem einfachen Beispiel erhält ein Benutzer eine Pushbenachrichtigung mit den Daten, die gerade eingefügt wurden. Der von GCM verwendete Gerätetoken wird durch den Client in der Anforderung an den Mobile Service übermittelt. Im nächsten Lernprogramm [Pushbenachrichtigungen an App-Benutzer](/en-us/develop/mobile/tutorials/push-notifications-to-users-android) erstellen Sie eine separate Gerätetabelle, in der Gerätetoken gespeichert werden, und senden eine Pushbenachrichtigung an alle gespeicherten Channels, wenn etwas eingefügt wird.

