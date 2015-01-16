<properties urlDisplayName="Get Started" pageTitle="Erste Schritte mit Azure Notification Hubs" metaKeywords="" description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="10/03/2014" ms.author="piyushjo" />

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">Baidu</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Baidu Cloud Push ist ein chinesischer Cloud-Dienst, über den Sie Pushbenachrichtigungen an mobile Geräte senden können. Dieser Dienst ist insbesondere in China nützlich, wo die Übermittlung von Pushbenachrichtigungen an Android aufgrund des Vorhandenseins anderer App-Stores, Pushdienste und Verfügbarkeit von Android-Geräten, die in der Regel nicht mit GCM (Google Cloud Messaging) verbunden sind, komplex ist. 

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Android-SDK (es wird davon ausgegangen, dass Sie Eclipse verwenden), das Sie <a href="http://go.microsoft.com/fwlink/?LinkId=389797">hier</a> herunterladen können
+ [Mobile Services Android SDK]
+ [Baidu Push Android-SDK]

>[WACOM.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>..

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

* [Erstellen eines Baidu-Kontos](#createBaiduAccount)
* [Registrieren als Baidu-Entwickler](#registerBaiduDeveloper)
* [Erstellen eines Baidu-Cloud-Pushprojekts](#createBaiduPushProject)
* [Konfigurieren Ihres Notification Hub](#configure-hub)
* [Verbinden Ihrer App mit dem Notification Hub](#connecting-app)
* [Senden von Benachrichtigungen an Ihre App](#send)

##<a id="createBaiduAccount"></a>Erstellen eines Baidu-Kontos

Um Baidu verwenden zu können, müssen Sie ein Konto erstellen. Wenn Sie bereits über ein Konto verfügen, melden Sie sich mit Ihrem Baidu-Konto beim [Baidu-Portal] an und überspringen Sie den nächsten Schritt. Erstellen Sie andernfalls anhand der unten stehenden Anweisungen ein neues Baidu-Konto.  

1. Rufen Sie das [Baidu-Portal] auf, und klicken Sie auf den Link 登录 (Anmeldung). Klicken Sie auf 立即注册, um den Registrierungsprozess für ein neues Konto zu starten. 

   	![][1]

2. Geben Sie die erforderlichen Details Telefonnummer/E-Mail-Adresse, Kennwort und Verifizierungscode ein, und klicken Sie auf "Anmeldung".

   	![][2]

3. Sie erhalten eine E-Mail mit einem Link zur Aktivierung Ihres Baidu-Kontos an die eingegebene E-Mail-Adresse. 

   	![][3]

4. Melden Sie sich bei Ihrem E-Mail-Konto an, öffnen Sie die Baidu-Aktivierungs-E-Mail, und klicken Sie auf den Aktivierungslink, um Ihr Baidu-Konto zu aktivieren. 

   	![][4]

Sobald Ihr Baidu-Konto aktiviert wurde, melden Sie sich darüber beim [Baidu-Portal] an. 

##<a id="registerBaiduDeveloper"></a>Registrieren als Baidu-Entwickler

1. Sobald Sie sich im [Baidu-Portal] angemeldet haben, klicken Sie auf **更多>> (mehr)**.

  	![][5]

2. Führen Sie einen Bildlauf im Abschnitt **站长与开发者服务 (Webmaster- und Entwicklerdienste)** durch, und klicken Sie auf **百度开放云平台 (Offene Baidu-Cloudplattform)**. 

  	![][6]

3. Klicken Sie auf der nächsten Seite auf **开发者服务 (Entwicklerdienste)** in der oberen rechten Ecke. 

  	![][7]

4. Klicken Sie auf der nächsten Seite im Menü oben rechts auf **注册开发者 (Registrierte Entwickler)**. 

  	![][8]

5. Geben Sie Namen, Beschreibung und Mobiltelefonnummer ein, um eine Textnachricht zur Verifizierung zu erhalten, und klicken Sie dann auf **送验证码 (Verifizierungscode senden)**. Beachten Sie, dass Sie bei internationalen Telefonnummern die Landeskennzahl in Klammern hinzufügen müssen, beispielsweise **(1)1234567890** für die Vereinigten Staaten.

  	![][9]

6. Sie sollten dann eine Textnachricht mit einer Verifizierungsnummer wie im folgenden Beispiel erhalten:

  	![][10] 

7. Geben Sie die Verifizierungsnummer aus der Nachricht unter **验证码 (Bestätigungscode)** ein. 

8. Schließen Sie die Entwicklerregistrierung ab, indem Sie die Baidu-Vereinbarung annehmen und auf **提交 (Senden)** klicken. Bei erfolgreichem Abschluss der Registrierung wird die folgende Seite angezeigt:

  	![][11] 

##<a id="createBaiduPushProject"></a>Erstellen eines Baidu-Cloud-Pushprojekts

Wenn Sie ein Baidu-Cloud-Pushprojekt erstellen, erhalten Sie App-ID, API-Schlüssel und geheimen Schlüssel.

1. Sobald Sie sich im [Baidu-Portal] angemeldet haben, klicken Sie auf **更多>> (mehr)**.

  	![][5]

2. Führen Sie einen Bildlauf im Abschnitt **站长与开发者服务 (Webmaster- und Entwicklerdienste)**  durch, und klicken Sie auf **百度开放云平台 (Offene Baidu-Cloudplattform)**. 

  	![][6]

3. Klicken Sie auf der nächsten Seite auf **开发者服务 (Entwicklerdienste)** in der oberen rechten Ecke. 

  	![][7]

4. Klicken Sie auf der nächsten Seite auf **云推送 (Cloud Push)** im Abschnitt **云服务 (Cloud-Dienste)**. 

  	![][12]

5. Sobald Sie ein registrierter Entwickler sind, wird **管理控制台 (Verwaltungskonsole)** im oberen Menü angezeigt. Klicken Sie auf **开发者服务管理 (Entwickler-Dienstverwaltung)**. 

  	![][13]

6. Klicken Sie auf der nächsten Seite auf **创建工程 (Projekt erstellen)**.

  	![][14]

7. Geben Sie einen Anwendungsnamen ein, und klicken Sie auf **创建 (Erstellen)**.

  	![][15]

8. Nach erfolgreicher Erstellung wird eine Seite mit **AppID**, **API-Schlüssel** und **geheimem Schlüssel** angezeigt. Notieren Sie sich den **API-Schlüssel** und den **geheimen Schlüssel**, die wir später verwenden werden. 

  	![][16]

9. Konfigurieren Sie das Projekt für Pushbenachrichtigungen, indem Sie im linken Bereich auf  **云推送 (Cloud Push)** klicken. 

  	![][31]

10. Klicken Sie auf der nächsten Seite auf die Schaltfläche  **推送设置 (Pusheinstellungen)** .

	![][32]  

11. Geben Sie auf der Konfigurationsseite den Paketnamen, den Sie im Android-Projekt verwenden werden, in das Feld  **应用包名 (Anwendungspaket)** ein, und klicken Sie auf **保存设置 (Speichern)**  

	![][33]

Die Nachricht **保存成功！(Erfolgreich gespeichert!)** wird angezeigt.

##<a id="configure-hub"></a>Konfigurieren Ihres Notification Hub

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste** dann auf **Servicebus** dann auf **Benachrichtigungshub** und dann auf **Schnellerfassung**.
 
3. Geben Sie einen Namen für den **Notification Hub** an, wählen Sie die **Region** und den **Namespace** aus, in dem dieser Benachrichtigungshub erstellt wird, und klicken Sie dann auf **Neuen Notification Hub erstellen**  

  	![][17]

4. Klicken Sie auf den Namespace, in dem Sie Ihren Notification Hub erstellt haben, und dann oben auf **Notification Hubs**. 

  	![][18]

5. Wählen Sie den erstellten Notification Hub aus, und klicken Sie im oberen Menü auf **Konfigurieren**.

  	![][19]

6. Führen Sie einen Bildlauf zum Abschnitt **Baidu-Benachrichtigungseinstellungen** durch, und geben Sie den **API-Schlüssel** und den **geheimen Schlüssel** ein, die Sie zuvor für Ihr Baidu-Cloud-Pushprojekt von der Baidu-Konsole erhalten haben. Klicken Sie nach Eingabe dieser Werte auf **Speichern**. 

  	![][20]

7. Klicken Sie oben auf die Registerkarte **Dashboard** für den Notification Hub, und klicken Sie auf **View Connection String** (Verbindungszeichenfolge anzeigen).

  	![][21]

8. Notieren Sie **DefaultListenSharedAccessSignature** und **DefaultFullSharedAccessSignature** aus dem Fenster für Access-Verbindungsinformationen. 

    ![][22]

##<a id="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

1. Erstellen Sie in Eclipse ADT ein neues Android-Projekt (File -> New -> Android Application).

    ![][23]

2. Geben Sie einen **Anwendungsnamen** ein, und stellen Sie sicher, dass die **erforderliche Mindest SDK**-Version auf **API 16: Android 4.1** festgelegt ist.

    ![][24]

3. Klicken Sie auf **Weiter**, und folgen Sie dem Assistenten bis zum Fenster **Aktivität erstellen**. Achten Sie darauf, dass **Leere Aktivität** ausgewählt ist, und wählen Sie zuletzt **Fertig stellen**, um eine neue Android-Anwendung zu erstellen. 

    ![][25]

4. Achten Sie darauf, dass das **Build-Ziel des Projekts** korrekt eingestellt ist.

    ![][26]

5. Laden Sie das [Mobile Services Android-SDK] herunter, und entzippen Sie es. Öffnen Sie den Ordner **notificationhubs**, kopieren Sie die Datei **notification-hubs-x.y.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.

6. Laden Sie das[Baidu Push Android-SDK] herunter, und entzippen Sie es. Öffnen Sie den Ordner **libs**, und kopieren Sie die JAR-Datei *pushservice-x.y.z* und die Ordner *armeabi* und *mips* in den Ordner **libs** Ihrer Android-Anwendung. 

    ![][27]

7. Öffnen Sie die Datei **AndroidManifest.xml** Ihres Android-Projekts, und fügen Sie die für das Baidu-SDK erforderlichen Berechtigungen hinzu.

	    <uses-permission android:name="android.permission.INTERNET" />
	    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
	    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
	    <uses-permission android:name="android.permission.VIBRATE" />
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
	    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
	    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Fügen Sie die Eigenschaft *android:name* zum Element *application* in der Datei **AndroidManifest.xml** hinzu, indem Sie *yourprojectname* ersetzen, z. B. **com.example.BaiduTest**. Stellen Sie sicher, dass dieser Projektname mit dem übereinstimmt, den Sie in der Baidu-Konsole konfiguriert haben. 

		<application android:name="yourprojectname.DemoApplication"

9. Fügen Sie die folgende Konfiguration nach dem .MainActivity-Aktivitätselement hinzu, indem Sie *yourprojectname* ersetzen, z. B. **com.example.BaiduTest**:

		<receiver android:name="yourprojectname.MyPushMessageReceiver">
		    <intent-filter>
		        <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
		        <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
		        <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
		    </intent-filter>
		</receiver>
		
		<receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
		    android:process=":bdservice_v1">
		    <intent-filter>
		        <action android:name="android.intent.action.BOOT_COMPLETED" />
		        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
				<action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
		    </intent-filter>
		</receiver>
        
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>                   
        </receiver>
        
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Fügen Sie dem Projekt eine neue Klasse mit dem Namen **ConfigurationSettings.java** hinzu. 

    ![][28]

    ![][29]

10. Fügen Sie den folgenden Code hinzu:

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	Legen Sie für den Wert von *API_KEY* den Wert fest, den Sie zuvor aus dem Baidu-Cloud-Projekt abgerufen haben, für *NotificationHubName* Ihren Notification Hub-Namen aus dem Azure-Portal und für *NotificationHubConnectionString* DefaultListenSharedAccessSignature aus dem Azure-Portal. 

11. Fügen Sie eine neue Klasse mit dem Namen **DemoApplication.java** hinzu, und fügen Sie ihr den folgenden Code hinzu:

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Fügen Sie eine weitere neue Klasse mit dem Namen **MyPushMessageReceiver.java** hinzu, und fügen Sie ihr den unten stehenden Code hinzu. Dies ist die Klasse, die vom Baidu-Push-Server empfangene Pushbenachrichtigungen behandelt:

		import java.util.List;
		import android.content.Context;
		import android.os.AsyncTask;
		import android.util.Log;
		import com.baidu.frontia.api.FrontiaPushMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
		    /** TAG to Log */
			public static NotificationHub hub = null;
			public static String mChannelId, mUserId;
		    public static final String TAG = MyPushMessageReceiver.class
		            .getSimpleName();
		    
			@Override
		    public void onBind(Context context, int errorCode, String appid,
		            String userId, String channelId, String requestId) {
		        String responseString = "onBind errorCode=" + errorCode + " appid="
		                + appid + " userId=" + userId + " channelId=" + channelId
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		        mChannelId = channelId;
		        mUserId = userId;
		        
		        try {
		       	 if (hub == null) {
		                hub = new NotificationHub(
		                		ConfigurationSettings.NotificationHubName, 
		                		ConfigurationSettings.NotificationHubConnectionString, 
		                		context);
		                Log.i(TAG, "Notification hub initialized");
		            }
		        } catch (Exception e) {
		           Log.e(TAG, e.getMessage());
		        }
		        
		        registerWithNotificationHubs();
			}
		    
		    private void registerWithNotificationHubs() {
		       new AsyncTask<Void, Void, Void>() {
		          @Override
		          protected Void doInBackground(Void... params) {
		             try {
		            	 hub.registerBaidu(mUserId, mChannelId);
		            	 Log.i(TAG, "Registered with Notification Hub - '" 
		     	    			+ ConfigurationSettings.NotificationHubName + "'" 
		     	    			+ " with UserId - '"
		     	    			+ mUserId + "' and Channel Id - '"
		     	    			+ mChannelId + "'");
		             } catch (Exception e) {
		            	 Log.e(TAG, e.getMessage());
		             }
		             return null;
		         }
		       }.execute(null, null, null);
		    }
		    
		    @Override
		    public void onSetTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onSetTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onDelTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onDelTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onListTags(Context context, int errorCode, List<String> tags,
		            String requestId) {
		        String responseString = "onListTags errorCode=" + errorCode + " tags="
		                + tags;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onUnbind(Context context, int errorCode, String requestId) {
		        String responseString = "onUnbind errorCode=" + errorCode
		                + " requestId = " + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onNotificationClicked(Context context, String title,
		            String description, String customContentString) {
		        String notifyString = "title=\"" + title + "\" description=\""
		                + description + "\" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }
		
		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. Öffnen Sie die Datei **MainActivity.java**, und fügen Sie der **onCreate**-Methode Folgendes hinzu:

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

und fügen Sie am Anfang die folgenden Import-Anweisungen hinzu:
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##<a id="send"></a>Senden von Benachrichtigungen an Ihre App

Sie können Benachrichtigungen mit Notification Hubs von jedem Back-End aus versenden, das unsere <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> verwendet. In diesem Lernprogramm zeigen wir, wie Sie eine .NET-Konsolenanwendung verwenden. 

1. Erstellen einer neuen Visual C#-Konsolenanwendung:

	![][30]

2. Fügen Sie mithilfe des <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Pakets</a> eine Referenz zum Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Library Package Manager** und dann auf **Package Manager Console**. Geben Sie dann Folgendes im Konsolenfenster ein, und drücken Sie die EINGABETASTE:

        Install-Package WindowsAzure.ServiceBus

3. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

4. Fügen Sie in Ihrer `Program`-Klasse die folgende Methode hinzu, und ersetzen Sie *DefaultFullSharedAccessSignatureSASConnectionString* und *NotificationHubName* durch die vorhandenen Werte. 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Fügen Sie dann folgende Zeilen zur Main-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>Testen der App

Um die Anwendung mit einem Telefon zu testen, müssen Sie dieses über ein USB-Kabel mit Ihrem Computer verbinden.

So testen Sie diese App mit dem Emulator:

1. Klicken Sie in der oberen Eclipse-Symbolleiste auf "Ausführen", und wählen Sie die App aus. 

2. Die App wird entweder direkt auf das verbundene Telefon geladen, oder der Emulator wird gestartet und lädt die App und führt sie aus.

3. Die App ruft die "userId" und "channelId" aus dem Baidu-Pushbenachrichtigungsdienst ab und registriert sich beim Notification Hub.
	
4.	Um bei Verwendung der .NET-Konsolenanwendung eine Testbenachrichtigung zu senden, drücken Sie in Visual Studio die Taste F5, um die Anwendung auszuführen. Hierdurch wird eine Benachrichtigung gesendet, die oben im Benachrichtigungsbereich Ihres Geräts oder Emulators angezeigt wird. 


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android-SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Baidu-Portal]: http://www.baidu.com/








	
