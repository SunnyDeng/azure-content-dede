<properties pageTitle="Get started with push notifications (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Appcelerator app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender"></tags>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services (Legacy Push)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Windows Azure Mobile Services eine Pushbenachrichtigung sowohl an eine iOS- als auch an eine Android-App senden, die in Appcelerator Titanium Studio entwickelt wurde. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Services (APNS) und Google Cloud Messaging Pushbenachrichtigungen zum Schnellstart-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

> [WACOM.NOTE] Mobile Services ist in Azure Notification Hubs integriert und unterstützt zusätzliche Pushbenachrichtigungsfunktionen wie Vorlagen, mehrere Plattformen und verbesserte Skalierung. Dieses Thema behandelt vorhandene mobile Dienste, die noch nicht für den Einsatz der Integration von Notification Hubs aktualisiert worden sind. Wenn Sie einen neuen mobilen Dienst erstellen, ist diese integrierte Funktionalität automatisch integriert. Sie sollten nach Möglichkeit Ihren Dienst im Hinblick auf die Verwendung von Notification Hubs aktualisieren. **Wir arbeiten an einem Lernprogramm für die Pushübertagung mit Notification Hubs mit Appcelerator. Es ist in Kürze verfügbar.**

1.  [Erstellen der Zertifikatsignieranforderungsdatei][Erstellen der Zertifikatsignieranforderungsdatei]
2.  [Registrieren der App und Aktivieren von Pushbenachrichtigungen][Registrieren der App und Aktivieren von Pushbenachrichtigungen]
3.  [Erstellen eines Bereitstellungsprofils für die App][Erstellen eines Bereitstellungsprofils für die App]
4.  [Aktivieren von Google Cloud Messaging][Aktivieren von Google Cloud Messaging]
5.  [Das GCM-Modul für Titanium erstellen][Das GCM-Modul für Titanium erstellen]
6.  [Konfigurieren von Mobile Services][Konfigurieren von Mobile Services]
7.  [Hinzufügen von Pushbenachrichtigungen zur App][Hinzufügen von Pushbenachrichtigungen zur App]
8.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
9.  [Einfügen von Daten zum Empfangen von Benachrichtigungen][Einfügen von Daten zum Empfangen von Benachrichtigungen]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Appcelerator Azure Mobile Services-Modul
-   Appcelerator Titanium Studio 3.2.1 oder neuer
-   Ein für iOS 7.0 (oder neuer) und ein Android 4.3 (oder neuer) taugliches Gerät
-   iOS-Entwicklerprogramm-Mitgliedschaft
-   Ein aktives Google-Konto

> [WACOM.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

[WACOM.INCLUDE [Apple-Pushbenachrichtigungen aktivieren][Apple-Pushbenachrichtigungen aktivieren]]

## <a name="register-gcm"></a>Aktivieren von Google Cloud Messaging

> [WACOM.NOTE]Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um diesen Vorgang abzuschließen. Besuchen Sie die Seite [accounts.google.com][accounts.google.com], um ein neues Google-Konto zu erstellen.

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

## <a name="gcm-module"></a>Das GCM-Modul für Titanium erstellen

### Appcelerator Titanium Studio für die Modulerstellung vorbereiten

Wenn Sie Android-Module erstellen, müssen Sie in Appcelerator Titanium Studio eine Java-Unterstützung installieren. Weitere Informationen finden Sie unter [Installieren der Java-Entwicklungstools][Installieren der Java-Entwicklungstools] bei Appcelerator mit einer kurzen Beschreibung, falls Sie das noch nicht getan haben.

Sie müssen Android NDK installieren. Laden Sie die entsprechende .zip-Datei von [][]<http://developer.android.com/sdk/ndk/index.html></a> herunter und extrahieren Sie sie auf der Festplatte. Merken Sie sich diesen Ort.

### Erstellen eines neuen Moduls

1.  Öffnen Sie Appcelerator Titanium Studio.

2.  Klicken Sie auf File -\> New -\> Mobile Module Project.

    ![][]

3.  Geben Sie im nächsten Fenster die Daten der Projekteinstellungen ein:

    -   **Project name:**In unserem Fall verwenden wir „notificationhub“ (kann gleich sein).

    -   **Module Id:**In unserem Fall verwenden wir „com.winwire.notificationhub“. Das muss unserer „application Id“ entsprechen.

    -   **Deployment Targets:**Hier wählen wir Android aus.

    > [WACOM.NOTE] Es ist wichtig, dass der Name unseres WorkSpace keine Leerzeichen enthält, da es sonst zu Problemen beim Erstellen der Kompilierung gibt.

    ![][1]

4.  Klicken Sie auf „Next“ und füllen Sie die Informationen für dieses Modul aus.

    ![][2]

5.  Klicken Sie schließlich auf „Finish“.

6.  Öffnen Sie die Datei timodule.xml. Fügen Sie folgende Änderungen im Android-Tag hinzu.

        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>

> [WACOM.NOTE] Im obigen Code müssen Sie alle Instanzen des Texts *com.winwire.notificationhub* durch den Paketnamen Ihrer Anwendung ersetzen (module Id).

1.  Klicken Sie in Ihrem Notification Hub-Modul mit der rechten Maustaste auf den Ordner „src“ und gehen Sie auf „Neu“ und wählen Sie „Ordner“. Geben Sie dem Ordner einen Namen wie com.google.android.gcm.

> [WACOM.NOTE] Wenn Sie „Ordner“ in „Neu“ nicht sehen können, dann wählen Sie „andere“ und erweitern Sie „Allgemein“ und wählen dort „Ordner“.

1.  Laden Sie die „.java“-Dateien (gcm.zip) von hier herunter und kopieren Sie diese Dateien in den eben erstellten Ordner ().

2.  Sie finden jetzt einen Ordner, der wie Ihre „module Id“ benannt ist; entpacken Sie ihn. In diesem Ordner sehen Sie eine Liste von „.java“-Dateien. Öffnen Sie aus diesen Dateien diejenige mit Ihrem Projektnamen+module.java (wenn der Name Ihres Projekts beispielsweise notificationhub lautet, dann wäre das „NotificationhubModule.java“), und fügen Sie unter den Codezeilen oben ein.

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

3.  Lokalisieren Sie in dieser Datei den Konstruktor und ersetzen Sie ihn mit dem Code unten.

        public NotificationhubModule() {
            super();
            _THIS = this;
        }

4.  Fügen Sie in der Datei die Codezeilen unten am Ende ein.

        @Kroll.method
        public void registerC2dm(HashMap options) {
            successCallback = (KrollFunction) options.get("success");
            errorCallback = (KrollFunction) options.get("error");
            messageCallback = (KrollFunction) options.get("callback");
            String registrationId = getRegistrationId();
            if (registrationId != null && registrationId.length() > 0) {
                sendSuccess(registrationId);
            } else {
                GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
            }
        }
        @Kroll.method
        public void unregister() {
            GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
            return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
            return TiApplication.getInstance().getAppProperties()
            .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
            if (successCallback != null) {
                HashMap data = new HashMap();
                data.put("registrationId", registrationId);
                successCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendError(String error) {
            if (errorCallback != null) {
                HashMap data = new HashMap();
                data.put("error", error);
                errorCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendMessage(HashMap messageData) {
            if (messageCallback != null) {
                HashMap data = new HashMap();
                data.put("data", messageData);
                messageCallback.call(getKrollObject(), data);
            }
        }
        public static NotificationhubModule getInstance() {
            return _THIS;
        }

5.  Laden Sie jetzt „module.zip“ herunter und kopieren Sie die Dateien in den Ordner mit der „module Id“ im Namen.

> [WACOM.NOTE] In den obigen Dateien müssen Sie alle Instanzen des Texts *com.winwire.notificationhub* durch den Paketnamen Ihrer Anwendung ersetzen (module Id). Ersetzen Sie außerdem „NotificationhubModule“ mit ProjektName+Modul (z. B. „NotificationhubModule“).

### Erstellen/Packen eines Moduls

Wählen Sie **Deploy \> Package - Android Module**. Sie können mit Studio kein BlackBerry-Modul erstellen – verwenden Sie entweder die BlackBerry NDK CLI-Tools oder Momentics IDE.

![][3]

Sie können dann wählen, ob Sie das Modul für alle Projekte bereitstellen möchten oder für ein spezifisches. Dies folgt den Installationsregeln, die in [Titanium-Module verwenden][Titanium-Module verwenden] erläutert sind; zusammenfassend:

-   Für alle Projekte: Die Modul-.zip-Datei wird im Stamm der Titanium SDK-Installationsorts abgelegt.

-   Für ein bestimmtes Projekt: Die Modul-.zip-Datei wird im Stamm des Projekts abgelegt.

## <a name="configure"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

1.  Geben Sie den Wert „API-Schlüssel“ ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf „Speichern“.

    ![][4]

Ihr mobiler Dienst ist nun sowohl für APNS als auch für GCM konfiguriert.

## <a name="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

1.  Wählen Sie in tiapp.xml die Registerkarte tiapp.xml (Sie finden ihn unten neben der Registerkarte „Übersicht“) und lokalisieren Sie den Tag `<android>`. Fügen Sie unter dem Tag folgenden Code ein:

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

> [WACOM.NOTE] Sie müssen im Code oben **ModuleId** und **ApplicationId** ersetzen. Die Modul-ID, die Sie bei der Erstellung des GCM-Moduls erhalten, und die Anwendungs-ID, die bei der Projekterstellung eingegeben wird. Achten Sie darauf, dass **ModuleId** und **ApplicationId** gleich sind. Es ist außerdem nötig, **ApplicationId.AppNameActivity** zu ändern. Das sollte dann in etwa so aussehen: com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity.

1.  Kopieren Sie das im Vorfeld erstellte GCM-Modul und platzieren Sie es an den unten genannten Ort.

    <table>
    <tr>
    <td>
    OSX

    </td>
    <td>
    /Library/Application Support/Titanium oder ~/Library/Application Support/Titanium

    </td>
    </tr>
    <td>
    Windows 7

    </td>
    <td>
    C:\\Users\\username\\AppData\\Roaming (oder C:\\ProgramData\\Titanium in Titanium Studio 1.0.1 und früher)

    </td>
    </tr>
    <td>
    Windows XP

    </td>
    <td>
    C:\\Dokumente und Einstellungen\\Benutzername\\Anwendungsdaten (oder C:\\Dokumente und Einstellungen\\All Users\\Anwendungsdaten\\Titanium in Titanium Studio 1.0.1 und früher)

    </td>
    </tr>
    <td>
    Linux

    </td>
    <td>
    ~/.titanium

    </td>
    </tr>
    </tr>
    </table>

> [WACOM.NOTE] In Mac OS ist Library ein ausgeblendeter Ordner. Um ihn sichtbar zu machen, müssen Sie folgenden Befehl ausführen und dann den Finder erneut starten: `**defaults write com.apple.finder AppleShowAllFiles TRUE**`

1.  Öffnen Sie in Appcelerator Titanium Studio die Datei index.js und fügen Sie folgenden Code am Ende ein. Dieser Code registriert Ihr Gerät für Pushbenachrichtigungen.

            Alloy.Globals.tempRegId = '';
                if (OS_ANDROID) {
                var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }

2.  Öffnen Sie jetzt die Datei TableData.js und lokalisieren Sie folgende Zeilen in der Funktion **addOrUpdateDataFromAndroid**

        var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    Ersetzen Sie obigen Code bitte nur in der Bedingung „else“ (für das Einfügen eines neuen Elements).

3.  Ersetzen Sie für Android den obigen Code mit Folgendem:

    var request = {
     'text' : alertTextField.getValue(),
     'complete' : false,
     'handle' : Alloy.Globals.tempRegId
    };

4.  Lokalisieren Sie nun folgende Zeilen in der Funktion **updateOrAddData**

           var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    Ersetzen Sie obigen Code bitte nur in der Bedingung „else“ (für das Einfügen eines neuen Elements).

5.  Ersetzen Sie für iOS den obigen Code mit Folgendem:

    var request = {
     'text' : alertTextField.getValue(),
     'complete' : false,
     'deviceToken' : Alloy.Globals.tempRegId
    };

Ihre App unterstützt nun Pushbenachrichtigungen sowohl für die Plattform iOS als auch Android.

## <a name="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte „Daten“ und dann auf die Tabelle „TodoItems“.

    ![][5]

2.  Klicken Sie in „todoitem“ auf die Registerkarte „Skript“ und dann auf „Einfügen“.

    ![][6]

    Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle „TodoItem“ eine Einfügung auftritt.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

**Für iOS:**

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }

    > [WACOM.NOTE] This script delays sending the notification to give you time to close the app to receive a push notification.  

**Für Android:**

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }

Auf diese Weise wird ein neues Einfügeskript registriert, das über das [Mobile Services Push-Objekt][Mobile Services Push-Objekt] eine Pushbenachrichtigung (den eingefügten Text) zum in der Einfügeanforderung angegebenen Gerät sendet.

<!-- Images. --> <!-- Anchors. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Erstellen der Zertifikatsignieranforderungsdatei]: #certificates
  [Registrieren der App und Aktivieren von Pushbenachrichtigungen]: #register
  [Erstellen eines Bereitstellungsprofils für die App]: #profile
  [Aktivieren von Google Cloud Messaging]: #register-gcm
  [Das GCM-Modul für Titanium erstellen]: #gcm-module
  [Konfigurieren von Mobile Services]: #configure
  [Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [Apple-Pushbenachrichtigungen aktivieren]: ../includes/enable-apple-push-notifications.md
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Installieren der Java-Entwicklungstools]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools
  []: http://developer.android.com/sdk/ndk/index.html
  []: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
  [1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
  [2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
  [3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
  [Titanium-Module verwenden]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
  [5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
  [6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png
  [Mobile Services Push-Objekt]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
