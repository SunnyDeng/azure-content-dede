<properties 
	pageTitle="Erste Schritte mit Pushbenachrichtigungen (Android) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre Android-App." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services (Legacypushvorgang)

<div class="dev-center-tutorial-selector sublanding"> <a href="/de-de/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a> <a href="/de-de/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a> <a href="/de-de/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a> <a href="/de-de/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a> <a href="/de-de/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a> <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">.NET-Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">JavaScript-Back-End</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe des Google Cloud Messaging (GCM)-Service zu dem Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

>[AZURE.NOTE]Dieses Thema behandelt <em>vorhandene</em> mobile Dienste, die für den Einsatz der Integration von Notification Hubs <em>noch nicht aktualisiert</em> worden sind. Wenn Sie einen <em>neuen</em> mobilen Dienst erstellen, ist diese integrierte Funktionalität automatisch integriert. Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)..
>
>Mobile Services werden in Azure Notification Hubs integriert, damit zusätzliche Pushbenachrichtigungsfunktionen wie Vorlagen, mehrere Plattformen und verbesserte Skalierung unterstützt werden können. <em>Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren</em>. Nach dem Upgrade finden Sie weitere Informationen in dieser Version von [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)..

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

* [Aktivieren von Google Cloud Messaging](#register)
* [Konfigurieren von Mobile Services](#configure)
* [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
* [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen](#update-scripts)
* [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services Android SDK]
+ Ein aktives Google-Konto

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] abschließen. 

##<a id="register"></a>Aktivieren von Google Cloud Messaging


[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Als Nächstes verwenden Sie diesen API-Schlüsselwert, um Mobile Services für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

##<a id="configure"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

   	![][18]

2. Klicken Sie auf die Registerkarte **Push**, geben Sie den Wert **API-Schlüssel** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

   	![][19]

Ihre Mobile Service ist jetzt konfiguriert, um mit GCM Pushbenachrichtigungen zu senden.

##<a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App


###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Hinzufügen von Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

1. Öffnen Sie die Projektdatei **AndroidManifest.xml**. Google Cloud Messaging verfügt über einige Mindestanforderungen an die API-Ebene in Bezug auf Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss. Weitere Informationen finden Sie unter [Setting Up Google Play Services] (Einrichten des Google Play Services, in englischer Sprache). Dort erfahren Sie, wie niedrig Sie diesen Wert einstellen können, wenn Sie aufgrund des Einsatzes eines älteren Geräts einen Wert unter 16 einstellen müssen. Legen Sie die Eigenschaft entsprechend fest.

2. Stellen Sie im Element `uses-sdk` sicher, dass für **targetSdkVersion** die Nummer einer SDK-Plattform festgelegt ist, die installiert wurde (Schritt 1). Es ist vorzuziehen, die neueste verfügbare Version zu verwenden. 

3. Das Tag **uses-sdk** sieht möglicherweise folgendermaßen aus, je nachdem, welche Optionen Sie in den vorherigen Schritten ausgewählt haben:

	    <uses-sdk
	        android:minSdkVersion="17"
	        android:targetSdkVersion="19" />
	
4. Ersetzen Sie im Code in den nächsten beiden Schritten _`**my_app_package**`_ durch den Namen des App-Pakets für Ihr Projekt, der dem Wert des Attributs `package` des Tags `manifest` entspricht. 

5. Fügen Sie die folgenden neuen Berechtigungen nach dem vorhandenen Element `uses-permission` ein:
     <permission android:name="**my_app_package**.permission.C2D_MESSAGE"          android:protectionLevel="signature" />     <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />      <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />     <uses-permission android:name="android.permission.GET_ACCOUNTS" />     <uses-permission android:name="android.permission.WAKE_LOCK" />

6. Fügen Sie den folgenden Code nach dem Starttag `application` ein: 
     <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"         android:permission="com.google.android.c2dm.permission.SEND">         <intent-filter>             <action android:name="com.google.android.c2dm.intent.RECEIVE" />             <category android:name="**my_app_package**" />         </intent-filter>     </receiver>



7. Öffnen Sie die Datei "ToDoItem.java", und fügen Sie den folgenden Code zur Klasse **TodoItem** hinzu:

			@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
		
	Dieser Code erstellt eine neue Eigenschaft, die die Registrierungs-ID enthält.
 > [AZURE.NOTE] Wenn für Ihren mobilen Dienst ein dynamisches Schema aktiviert ist, wird automatisch eine neue Spalte **Ziehpunkt** zur Tabelle **TodoItem** hinzugefügt, wenn ein neues Element, das diese Eigenschaft enthält, eingefügt wird.

8. Laden Sie das [Mobile Services Android SDK] herunter, und entpacken Sie es. Öffnen Sie den Ordner **notifications**, kopieren Sie die Datei **notifications-1.0.1.jar** in den Ordner *libs* Ihres Eclipse-Projekts, und aktualisieren Sie den Ordner *libs*.
 > [AZURE.NOTE] Die Nummern am Ende des Dateinamens können sich in den nachfolgenden SDK-Versionen ändern.

9.  Öffnen Sie die Datei *ToDoItemActivity.java*, und fügen Sie die folgende Import-Anweisung ein:

		import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Fügen Sie die folgende private Variable der Klasse hinzu, wobei _`<PROJECT_NUMBER>`_ für die Projektnummer steht, die Ihrer App im vorherigen Vorgang durch Google zugewiesen wurde:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. Bevor der MobileServiceClient instanziiert wird, fügen Sie in der Methode **onCreate** diesen Code hinzu. Er registriert die Benachrichtigungsbehandlung für das Gerät:

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);



12. Fügen Sie die folgende Codezeile der Methode **addItem** hinzu, bevor das Element in die Tabelle eingefügt wird:

		item.setHandle(MyHandler.getHandle());

	Dieser Code legt für die Eigenschaft `handle` des Elements die Registrierungs-ID des Geräts fest.

13. Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und dann auf **Klasse**.

14. Geben Sie unter **Name** `MyHandler` ein, geben Sie in **Übergeordnete Klasse** `com.microsoft.windowsazure.notifications.NotificationsHandler` ein, und klicken Sie dann auf **Fertig stellen**.

	![][6]

	Daraufhin wird die neue MyHandler-Klasse erstellt.

15. Fügen Sie die folgenden Import-Anweisungen ein:

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
	

17. Ersetzen Sie die vorhandene Methodenüberschreibung **onRegistered** durch den folgenden Code:

		@Override
		public void onRegistered(Context context, String gcmRegistrationId) {
			super.onRegistered(context, gcmRegistrationId);
			
			setHandle(gcmRegistrationId);
		}
		

Ihre App kann Pushbenachrichtigungen nun unterstützen.


##<a id="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**. 

   	![][21]

2. Klicken Sie in **todoitem** auf die Registerkarte **Skript** und dann auf **Einfügen**.
   
  	![][22]

   	Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **TodoItem** eine Einfügung auftritt.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

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

   	Auf diese Weise wird ein neues Einfügeskript registriert, das über das [gcm object] eine Pushbenachrichtigung (den eingefügten Text) an das in der Einfügeanforderung angegebene Gerät sendet. 

##<a id="test"></a>Testen von Pushbenachrichtigungen in der App

> [AZURE.NOTE] Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1. Starten Sie Eclipse neu, klicken Sie dann im Paket-Explorer mit der rechten Maustaste auf **Eigenschaften**, klicken Sie anschließend auf **Android**, aktivieren Sie die Option **Google APIs**, und klicken Sie abschließend auf **OK**.

	![][23]

  	Dies gibt das Projekt für die Google-APIs vor.

2. Wählen Sie unter **Window** **Android Virtual Device Manager** aus, wählen Sie Ihr Gerät aus, und klicken Sie auf **Bearbeiten**.

	![][24]

3. Wählen Sie unter **Target** **Google APIs** aus, und klicken Sie dann auf "OK".

   	![][25]

	Dies gibt das AVD zur Verwendung von Google-APIs vor.

4. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten.

5. Geben Sie in der App einen sinnvollen Text ein, beispielsweise "_Eine neue Mobile Services-Aufgabe hinzufügen_", und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

  	![][26]

6. Im unteren Bereich der Anzeige wird Ihnen kurz ein schwarzes Benachrichtigungsfeld angezeigt. 

  	![][28]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

  ![][27]-->

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

In diesem einfachen Beispiel empfängt ein Benutzer eine Pushbenachrichtigung mit den soeben eingegebenen Daten. Der von GCM verwendete Gerätetoken wird durch den Client in der Anforderung an den Mobile Service übermittelt. Im nächsten Lernprogramm [Pushbenachrichtigungen an App-Benutzer] werden Sie eine eigene Gerätetabelle zum Speichern von Gerätetoken erstellen und beim Auftreten einer Einfügung eine Pushbenachrichtigung an alle gespeicherten Kanäle aussenden. 


<!-- Images. -->

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Google-Cloudkonsole]: https://cloud.google.com/console
[Google-APIs]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android-Bereitstellungsportal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Einrichten des Google Play Services SDKs]: http://go.microsoft.com/fwlink/?LinkId=389801
[Verweisen auf ein Bibliothekprojekt]: http://go.microsoft.com/fwlink/?LinkId=389800
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-android
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-android
[Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-android
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-android

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services: Android-Konzept]: /de-de/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645




<!--HONumber=42-->
