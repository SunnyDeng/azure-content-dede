<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services (Legacypushvorgang)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/de-de/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET-Backend">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript-Backend" class="current">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe des Google Cloud Messaging (GCM)-Service zu dem Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

> [WACOM.NOTE]In diesem Thema werden *vorhandene* mobile Dienste behandelt, die noch nicht für die Verwendung der Notification Hubs-Integration *aktualisiert* wurden. Wenn Sie einen *neuen* mobilen Dienst erstellen, wird diese integrierte Funktionalität automatisch aktiviert. Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].
>
> Mobile Services werden in Azure Notification Hubs integriert, damit zusätzliche Pushbenachrichtigungsfunktionen wie Vorlagen, mehrere Plattformen und verbesserte Skalierung unterstützt werden können. *Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren*. Nach dem Upgrade finden Sie weitere Informationen in dieser Version von [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

-   [Aktivieren von Google Cloud Messaging][Aktivieren von Google Cloud Messaging]
-   [Konfigurieren von Mobile Services][Konfigurieren von Mobile Services]
-   [Hinzufügen von Pushbenachrichtigungen zur App][Hinzufügen von Pushbenachrichtigungen zur App]
-   [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
-   [Einfügen von Daten zum Empfangen von Benachrichtigungen][Einfügen von Daten zum Empfangen von Benachrichtigungen]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Mobile Services Android SDK][Mobile Services Android SDK]
-   Ein aktives Google-Konto

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

## <span id="register"></span></a>Aktivieren von Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Als Nächstes verwenden Sie diesen API-Schlüsselwert, um Mobile Services für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

## <span id="configure"></span></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

    ![][0]

2.  Klicken Sie auf die Registerkarte **Push**, geben Sie den Wert **API Key** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

    ![][1]

Ihre Mobile Service ist jetzt konfiguriert, um mit GCM Pushbenachrichtigungen zu senden.

## <span id="add-push"></span></a>Hinzufügen von Pushbenachrichtigungen zur App

### Hinzufügen von Google Play Services zum Projekt

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Hinzufügen des Codes

1.  Öffnen Sie die Projektdatei **AndroidManifest.xml**. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss. Konsultieren Sie [Set Up Google Play Services SDK][Set Up Google Play Services SDK] (Einrichten des Google Play Services SDK, in englischer Sprache), um zu ermitteln, wie niedrig Sie diesen Wert einstellen können, bzw. ob Sie einen Wert unter 16 einstellen müssen, weil Sie ein älteres Gerät verwenden. Legen Sie die Eigenschaft entsprechend fest.

2.  Stellen Sie im Element `uses-sdk` sicher, dass für **targetSdkVersion** die Nummer einer SDK-Plattform festgelegt ist, die installiert wurde (Schritt 1). Es ist vorzuziehen, die neueste verfügbare Version zu verwenden.

3.  Der Tag **uses-sdk** sieht möglicherweise folgendermaßen aus, je nachdem, welche Auswahlen Sie in den vorherigen Schritten getroffen haben:

        <uses-sdk
            android:minSdkVersion="17"
            android:targetSdkVersion="19" />

4.  Ersetzen Sie im Code in den nächsten beiden Schritten *`**my_app_package**`* durch den Namen des App-Pakets für Ihr Projekt, der dem Wert des Attributs `package` des Tags `manifest` entspricht.

5.  Fügen Sie die folgenden neuen Berechtigungen nach dem vorhandenen Element `uses-permission` ein:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

6.  Fügen Sie den folgenden Code nach dem Starttag `application` ein:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

7.  Öffnen Sie die Datei ToDoItem.java, fügen Sie den folgenden Code zur Klasse **TodoItem** hinzu:

            @com.google.gson.annotations.SerializedName("handle")
            private String mHandle;

            public String getHandle() {
                return mHandle;
            }

            public final void setHandle(String handle) {
                mHandle = handle;
            }

    Dieser Code erstellt eine neue Eigenschaft, die die Registrierungs-ID enthält.

    <div class="dev-callout"><b>Hinweis</b>
<p>Wenn f&uuml;r Ihren Mobile Services ein dynamisches Schema aktiviert ist, wird automatisch eine neue Spalte <strong>Ziehpunkt</strong> zur Tabelle <strong>TodoItem</strong> hinzugef&uuml;gt, wenn ein neues Element, das diese Eigenschaft enth&auml;lt, eingef&uuml;gt wird.</p>
</div>

8.  Laden Sie den [Mobile Services Android SDK][Mobile Services Android SDK] herunter, und entzippen Sie ihn. Öffnen Sie den Ordner **Benachrichtigungen**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.

    <div class="dev-callout"><b>Hinweis</b>
<p>Die Nummern am Ende des Dateinamens k&ouml;nnen sich in den nachfolgenden SDK-Versionen &auml;ndern.</p>
</div>

9.  Öffnen Sie die Datei *ToDoItemActivity.java*, und fügen Sie den folgenden Import-Ausdruck ein:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Fügen Sie die folgende private Variable zu der Klasse hinzu. *`<PROJECT_NUMBER>`* steht für die Projektnummer, die Ihrer App im vorherigen Vorgang durch Google zugewiesen wurde:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. Bevor der MobileServiceClient instanziiert wird, fügen Sie in der Methode **onCreate** diesen Code hinzu. Er registriert die Benachrichtigungsbehandlung für das Gerät:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. Fügen Sie die folgende Codezeile zur Methode **addItem** hinzu, bevor das Element in die Tabelle eingefügt wird:

        item.setHandle(MyHandler.getHandle());

    Dieser Code legt für die Eigenschaft `handle` des Elements die Registrierungs-ID des Geräts fest.

13. Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und dann auf **Klasse**.

14. Geben Sie für **Name** `MyHandler` ein, geben Sie in **Übergeordnete Klasse** `com.microsoft.windowsazure.notifications.NotificationsHandler` ein, und klicken Sie dann auf **Fertig stellen**.

    ![][2]

    Daraufhin wird die neue MyHandler-Klasse erstellt.

15. Fügen Sie die folgenden Import-Ausdrücke ein:

        import android.content.Context;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

16. Fügen Sie den folgenden Code hinzu:

        @com.google.gson.annotations.SerializedName("handle")
        private static String mHandle;

        public static String getHandle() {
            return mHandle;
        }

        public static final void setHandle(String handle) {
            mHandle = handle;
        }

17. Ersetzen Sie die vorhandene Methodenüberschreibung **onRegistered** mit dem folgenden Code:

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            setHandle(gcmRegistrationId);
        }

Ihre App kann Pushbenachrichtigungen nun unterstützen.

## <span id="update-scripts"></span></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][3]

2.  Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![][4]

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
                    request.respond();
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) {
                            console.log('Push notification sent: ', response);
                        }, error: function(error) {
                            console.log('Error sending push notification: ', error);
                        }
                    });
                }
            });
        }

    Auf diese Weise wird ein neues Einfügeskript registriert, das über das [gcm object][gcm object] eine Pushbenachrichtigung (den eingefügten Text) zum in der Einfügeanforderung angegebenen Gerät sendet.

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

<div class="dev-callout"><b>Hinweis</b>
    <p>Wenn Sie diese App im Emulator ausf&uuml;hren, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterst&uuml;tzt.</p>
</div>

1.  Starten Sie Eclipse neu, und klicken Sie dann im Paket-Explorer mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Eigenschaften**, dann auf **Android**, überprüfen Sie **Google APIs**, und klicken Sie dann auf **OK**.

    ![][5]

    Dies gibt das Projekt für die Google-APIs vor.

2.  Wählen Sie unter **Fenster** **Android Virtual Device Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Bearbeiten**.

    ![][6]

3.  Wählen Sie **Google APIs** in **Ziel** aus, und klicken Sie auf **OK**.

    ![][7]

    Dies gibt das AVD zur Verwendung von Google-APIs vor.

4.  Klicken Sie im Menü **Run** auf **Run**, um die App zu starten.

5.  Geben Sie in der App einen sinnvollen Text ein, beispielsweise *Eine neue Mobile Services-Aufgabe hinzufügen*, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

    ![][8]

6.  Im unteren Bereich der Anzeige wird Ihnen kurz ein schwarzes Benachrichtigungsfeld angezeigt.

    ![][9]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.    ![][27]-->

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

In diesem einfachen Beispiel empfängt ein Benutzer eine Benachrichtigung mit den soeben eingegebenen Daten. Der von GCM verwendete Gerätetoken wird durch den Client in der Anforderung an den Mobile Service übermittelt. Im nächsten Lernprogramm [Pushbenachrichtigungen an App-Benutzer][Pushbenachrichtigungen an App-Benutzer] werden wir eine eigene Gerätetabelle zum Speichern von Gerätetoken erstellen und beim Auftreten einer Einfügung eine Pushbenachrichtigung an alle gespeicherten Kanäle aussenden.

<!-- Images. --> <!-- URLs. -->

  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Aktivieren von Google Cloud Messaging]: #register
  [Konfigurieren von Mobile Services]: #configure
  [Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
  [Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-android
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [Set Up Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [8]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [9]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
  [Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-android
