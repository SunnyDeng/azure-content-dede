<properties 
	pageTitle="Erste Schritte mit Azure Notification Hubs" 
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.tgt_pltfrm="mobile-baidu" 
	ms.workload="mobile" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# Erste Schritte mit Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Übersicht

Baidu Cloud Push ist ein chinesischer Cloud-Dienst, mit dem Sie Pushbenachrichtigungen an mobile Geräte senden können. Dieser Dienst ist besonders nützlich in China, da hier aufgrund der Existenz von verschiedenen App-Stores, Push-Diensten und Android-Geräten, die in der Regel nicht mit GCM (Google Cloud Messaging) verbunden sind, die Übermittlung von Pushbenachrichtigungen an Android recht komplex ist. 

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Android SDK (es wird davon ausgegangen, dass Sie Eclipse verwenden), das Sie <a href="http://go.microsoft.com/fwlink/?LinkId=389797">hier</a> herunterladen können.
+ [Mobile Services Android SDK]
+ [Baidu Push Android SDK]

>[AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [1 Monat kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##Erstellen eines Baidu-Kontos

Zum Verwenden von Baidu müssen Sie ein Konto erstellen. Wenn Sie bereits ein Konto haben, melden Sie sich mit Ihrem Baidu-Konto im [Baidu-Portal] an, und fahren Sie mit dem nächsten Schritt fort. Andernfalls finden Sie nachfolgend die entsprechenden Anweisungen zum Erstellen eines neuen Baidu-Kontos.  

1. Wechseln Sie zum [Baidu-Portal], und klicken Sie auf den Link 登录 (Anmelden). Klicken Sie auf 立即注册, um den Registrierungsprozess für ein neues Konto zu starten. 

   	![][1]

2. Geben Sie die erforderlichen Details (Telefon, E-Mail-Adresse, Kennwort und Überprüfungscode) ein, und klicken Sie auf die Anmeldeschaltfläche.

   	![][2]

3. Sie erhalten eine E-Mail mit einem Link zur Aktivierung Ihres Baidu-Kontos an die eingegebene E-Mail-Adresse. 

   	![][3]

4. Melden Sie sich bei Ihrem E-Mail-Konto an, öffnen Sie die Baidu-Aktivierungsmail, und klicken Sie auf den Aktivierungslink, um Ihr Baidu-Konto zu aktivieren. 

   	![][4]

Nachdem Sie Ihr Baidu-Konto aktiviert haben, melden Sie sich mit Ihrem Konto im [Baidu-Portal] an. 

##Registrieren als Baidu-Entwickler

1. Nachdem Sie sich beim [Baidu-Portal] angemeldet haben, klicken Sie auf **更多>> (mehr)**.

  	![][5]

2. Führen Sie im Abschnitt **站长与开发者服务 (Webmaster- und Entwicklerdienste)** einen Bildlauf nach unten aus, und klicken Sie auf **百度开放云平台 (Offene Baidu-Cloud-Plattform)**. 

  	![][6]

3. Klicken Sie auf der nächsten Seite in der rechten oberen Ecke auf **开发者服务 (Entwicklerdienste)**. 

  	![][7]

4. Klicken Sie auf der nächsten Seite im Menü oben rechts auf **注册开发者 (Registrierte Entwickler)**. 

  	![][8]

5. Geben Sie Ihren Namen, eine Beschreibung und Ihre Mobiltelefonnummer für den Empfang einer Überprüfungsnachricht an, und klicken Sie dann auf **送验证码 (Überprüfungscode senden)**. Beachten Sie, dass Sie bei internationalen Telefonnummern die Landeskennzahl in Klammern setzen müssen (z. B. **(1) 1234567890** bei einer Telefonnummer in den USA).

  	![][9]

6. Sie sollten dann eine Textnachricht mit einer Überprüfungsnummer erhalten, wie im folgenden Beispiel gezeigt:

  	![][10] 

7. Geben Sie die Überprüfungsnummer aus der Nachricht in das Feld **验证码 (Bestätigungscode)** ein. 

8. Schließen Sie die Registrierung als Entwickler ab, indem Sie der Baidu-Vereinbarung zustimmen und auf **提交 (Senden)** klicken. Bei erfolgreicher Registrierung wird die folgende Seite angezeigt:

  	![][11] 

##Erstellen eines Baidu-Cloud-Push-Projekts

Wenn Sie ein Baidu-Cloud-Push-Projekt erstellen, erhalten Sie Ihre App-ID, einen API-Schlüssel und einen geheimen Schlüssel.

1. Nachdem Sie sich beim [Baidu-Portal] angemeldet haben, klicken Sie auf **更多>> (mehr)**.

  	![][5]

2. Führen Sie im Abschnitt **站长与开发者服务 (Webmaster- und Entwicklerdienste)** einen Bildlauf nach unten aus, und klicken Sie auf **百度开放云平台 (Offene Baidu-Cloud-Plattform)**. 

  	![][6]

3. Klicken Sie auf der nächsten Seite in der rechten oberen Ecke auf **开发者服务 (Entwicklerdienste)**. 

  	![][7]

4. Klicken Sie auf der nächsten Seite im Abschnitt **云服务 (Cloud-Dienste)** auf **云推送 (Cloud-Push)**. 

  	![][12]

5. Wenn Sie ein registrierter Entwickler sind, wird im Hauptmenü die Option **管理控制台 (Verwaltungskonsole)** angezeigt. Klicken Sie auf **开发者服务管理 (Entwicklerdienst-Verwaltung)**. 

  	![][13]

6. Klicken Sie auf der nächsten Seite auf **创建工程 (Projekt erstellen)**.

  	![][14]

7. Geben Sie einen Anwendungsnamen ein, und klicken Sie auf **创建 (Erstellen)**.

  	![][15]

8. Nach erfolgreicher Erstellung wird eine Seite mit der **App-ID**, dem **API-Schlüssel** und dem **geheimen Schlüssel** angezeigt. Notieren Sie den **API-Schlüssel** und den **geheimen Schlüssel**, die später verwendet werden. 

  	![][16]

9. Konfigurieren Sie das Projekt für Pushbenachrichtigungen, indem Sie im linken Bereich auf **云推送 (Cloud-Push)** klicken. 

  	![][31]

10. Klicken Sie auf der nächsten Seite auf die Schaltfläche **推送设置 (Push-Einstellungen)**.

	![][32]  

11. Geben Sie auf der Konfigurationsseite den Paketnamen, den Sie im Android-Projekt verwenden möchten, in das Feld **应用包名 (Anwendungspaket)** ein, und klicken Sie auf **保存设置 (Speichern)**.

	![][33]

Die Nachricht **保存成功！ (Erfolgreich gespeichert!)** wird angezeigt.

##Konfigurieren Ihres Notification Hub

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und dann auf **Schnellerfassung**.
 
3. Geben Sie einen Namen für Ihren **Notification Hub** ein, wählen Sie die **Region** und den **Namespace** aus, in dem dieser Notification Hub erstellt wird, und klicken Sie dann auf **Neuen Notification Hub erstellen**.  

  	![][17]

4. Klicken Sie auf den Namespace, in dem Sie den Notification Hub erstellt haben, und klicken Sie dann oben auf **Notification Hubs**. 

  	![][18]

5. Wählen Sie den erstellten Notification Hub aus, und klicken Sie im Hauptmenü auf **Konfigurieren**.

  	![][19]

6. Führen Sie einen Bildlauf nach unten zum Abschnitt **Baidu-Benachrichtigungseinstellungen** aus, und geben Sie den **API-Schlüssel** und den **geheimen Schlüssel** ein, den Sie zuvor über die Baidu-Konsole für Ihr Baidu-Cloud-Push-Projekt abgerufen haben. Klicken Sie nach der Eingabe dieser Werte auf **Speichern**. 

  	![][20]

7. Klicken Sie unter dem Notification Hub oben auf die Registerkarte **Dashboard**, und klicken Sie dann auf **Verbindungszeichenfolge anzeigen**.

  	![][21]

8. Notieren Sie im Fenster "Verbindungsinformationen aufrufen" die Werte für **DefaultListenSharedAccessSignature** und **DefaultFullSharedAccessSignature**. 

    ![][22]

##Verbinden Ihrer App mit dem Notification Hub

1. Erstellen Sie in Eclipse ADT ein neues Android-Projekt (File -> New -> Android Application).

    ![][23]

2. Geben Sie einen **Anwendungsnamen** ein, und stellen Sie sicher, dass die die SDK-Version unter **Minimum Required SDK** auf **API 16: Android 4.1** eingestellt ist.

    ![][24]

3. Klicken Sie auf **Next**, und befolgen Sie die Anweisungen des Assistenten bis zum Fenster **Create Activity**. Stellen Sie sicher, dass **Blank Activity** ausgewählt ist, und wählen Sie abschließend **Finish** aus, um eine neue Android-Anwendung zu erstellen. 

    ![][25]

4. Stellen Sie sicher, dass das **Project Build Target** richtig festgelegt ist.

    ![][26]

5. Laden Sie das [Mobile Services Android SDK] herunter, und entpacken Sie es. Öffnen Sie den Ordner **notificationhubs**, kopieren Sie die Datei **notifications-x.y.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.

6. Laden Sie das [Baidu Push Android SDK] herunter, und entpacken Sie es. Öffnen Sie den Ordner **libs**, und kopieren Sie die JAR-Datei *pushservice-x.y.z* und die Ordner *armeabi* und *mips* in den Ordner **libs** Ihrer Android-Anwendung. 

7. Öffnen Sie die Datei **AndroidManifest.xml** Ihres Android-Projekts, und fügen Sie die Berechtigungen hinzu, die für das Baidu SDK erforderlich sind.

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

8. Fügen Sie dem *application*-Element in der Datei **AndroidManifest.xml** die Eigenschaft *android:name* hinzu, und ersetzen Sie dabei *yourprojectname* durch Ihren Projektnamen, z. B. **com.example.BaiduTest**. Stellen Sie sicher, dass dieser Projektname mit dem Namen übereinstimmt, den Sie in der Baidu-Konsole konfiguriert haben. 

		<application android:name="yourprojectname.DemoApplication"

9. Fügen Sie innerhalb des Anwendungselements nach dem Aktivitätselement ".MainActivity" die folgende Konfiguration hinzu, und ersetzen Sie dabei *yourprojectname* durch Ihren Projektnamen, z. B. **com.example.BaiduTest**:

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

9. Fügen Sie dem Projekt eine neue Klasse namens **ConfigurationSettings.java** hinzu. 

    ![][28]

    ![][29]

10. Fügen Sie der Klasse folgenden Code hinzu:

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	Legen Sie für den Wert von *API_KEY* den Wert fest, den Sie zuvor aus dem Baidu-Cloud-Projekt abgerufen haben, für *NotificationHubName* Ihren Notification Hub-Namen aus dem Azure-Portal und für *NotificationHubConnectionString* die DefaultListenSharedAccessSignature aus dem Azure-Portal. 

11. Fügen Sie eine neue Klasse namens **DemoApplication.java** mit folgendem Code hinzu:

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Fügen Sie eine weitere neue Klasse namens **MyPushMessageReceiver.java** mit dem folgenden Code hinzu. Dies ist die Klasse, die die vom Baidu-Push-Server empfangenen Pushbenachrichtigungen behandelt:

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
		        String notifyString = "title="" + title + "" description=""
		                + description + "" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }
		
		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message="" + message + "" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. Öffnen Sie **MainActivity.java**, und fügen Sie der Methode **OnCreate** Folgendes hinzu:

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

Fügen Sie außerdem am Anfang die folgenden "import"-Anweisungen hinzu:
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##Senden von Benachrichtigungen an Ihre App

Sie können Benachrichtigungen mit Notification Hubs von jedem Back-End aus versenden, das unsere <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> verwendet. In diesem Lernprogramm zeigen wir, wie Sie eine .NET-Konsolenanwendung verwenden. 

1. Erstellen einer neuen Visual C#-Konsolenanwendung:

	![][30]

2. Fügen Sie dem Azure Service Bus SDK mithilfe des <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Pakets</a> einen Verweis hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, **Bibliotheks-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Geben Sie dann Folgendes im Konsolenfenster ein, und drücken Sie die EINGABETASTE:

        Install-Package WindowsAzure.ServiceBus

3. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

4. Fügen Sie der `Program`-Klasse die folgende Methode hinzu, und ersetzen Sie dabei *DefaultFullSharedAccessSignatureSASConnectionString* und *NotificationHubName* durch die Ihnen vorliegenden Werte. 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{"title":"((Notification title))","description":"Hello from Azure"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Fügen Sie dann folgende Zeilen zur Main-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

##Testen der App

Um die Anwendung mit einem Telefon zu testen, müssen Sie dieses über ein USB-Kabel mit Ihrem Computer verbinden.

So testen Sie diese App mit dem Emulator:

1. Klicken Sie in der oberen Eclipse-Symbolleiste auf "Ausführen", und wählen Sie die App aus. 

2. Die App wird entweder direkt auf das angeschlossene Telefon geladen, oder der Emulator wird gestartet, der dann die App lädt und sie ausführt.

3. Die App ruft "userId" und "channelId" vom Baidu-Pushbenachrichtigungsdienst ab und registriert sich beim Benachrichtigungs-Hub.
	
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
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Baidu-Portal]: http://www.baidu.com/




<!--HONumber=49-->