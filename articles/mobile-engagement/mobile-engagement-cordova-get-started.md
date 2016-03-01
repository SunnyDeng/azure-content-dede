<properties
	pageTitle="Erste Schritte mit Azure Mobile Engagement für Cordova/Phonegap"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für Cordova-/Phonegap-Apps."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="article" 
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für Cordova/Phonegap

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Engagement Ihre App-Nutzung nachvollziehen und Pushbenachrichtigungen an nach Segmenten eingeteilte Benutzer einer mit Cordova entwickelten mobilen Anwendung senden können.

In diesem Lernprogramm erstellen Sie auf dem Mac eine leere Cordova-App und integrieren dann das Mobile Engagement-SDK. Dieses erfasst grundlegende Analysedaten und empfängt Pushbenachrichtigungen über Apple Push Notification System (APNS) für iOS und Google Cloud Messaging (GCM) für Android. Danach erfolgt die Bereitstellung auf einem iOS- oder Android-Gerät zu Testzwecken.

> [AZURE.IMPORTANT] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-DE%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Xcode, den Sie aus dem Mac App Store installieren können (für die Bereitstellung in iOS)
+ [Android-SDK & Emulator](http://developer.android.com/sdk/installing/index.html) (für die Bereitstellung in Android)
+ Pushbenachrichtigungszertifikat (P12), das Sie im Apple Dev Center für APNS abrufen können
+ GCM-Projektnummer, die Sie in der Google-Entwicklerkonsole für GCM abrufen können
+ [Cordova-Plug-In für Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] Sie finden den Quellcode und die Infodatei für das Cordova-Plug-In auf [Github](https://github.com/Azure/azure-mobile-engagement-cordova).

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre Cordova-App

[AZURE.INCLUDE [Erstellen einer Mobile Engagement-App im Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Verbinden Ihrer App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine „einfache Integration“ dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung.

Wir erstellen eine einfache App mit Cordova, um die Integration zu veranschaulichen:

###Erstellen eines neuen Cordova-Projekts

1. Starten Sie das *Terminal*-Fenster auf dem Macintosh-Computer, und geben Sie Folgendes ein, um ein neues Cordova-Projekt auf Grundlage der Standardvorlage zu erstellen. Stellen Sie sicher, dass das Veröffentlichungsprofil, mit dem Sie die iOS-App später bereitstellen, als App-ID. "com.mycompany.myapp" verwendet. 

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

2. Führen Sie Folgendes aus, um das Projekt für **iOS** zu konfigurieren und im iOS-Simulator auszuführen:

		$ cordova platform add ios 
		$ cordova run ios

3. Führen Sie Folgendes aus, um das Projekt für **Android** zu konfigurieren und im Android-Emulator auszuführen. Stellen Sie sicher, dass in den Einstellungen für den Android-SDK-Emulator Google-APIs (Google Inc.) als Ziel und CPU/ABI als Google-API-ARM eingerichtet sind.

		$ cordova platform add android
		$ cordova run android

4. 	Fügen Sie das Plug-In für die Cordova-Konsole hinzu.

		$ cordova plugin add cordova-plugin-console 

###Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End.

1. Installieren Sie das Cordova-Plug-In für Azure Mobile Engagement und stellen Sie dabei die Variablenwerte zum Konfigurieren des Plug-Ins bereit:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			 --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

*Android Reach Icon* : muss der Name der Ressource ohne Erweiterung oder ziehbares Präfix sein (z. B. "mynotificationicon"), und die Symboldatei muss in Ihr Android-Projekt ("platforms/android/res/drawable") kopiert werden

*iOS Reach Icon* : muss der Name der Ressource mit Erweiterung sein (z. B. "mynotificationicon.png"), und die Symboldatei muss mit XCode (mithilfe des Menüs "Dateien hinzufügen") dem iOS-Projekt hinzugefügt werden

##<a id="monitor"></a>Aktivieren der Überwachung in Echtzeit

1. Bearbeiten Sie im Cordova-Projekt **www/js/index.js**, um den Aufruf Mobile Engagement hinzuzufügen und eine neue Aktivität zu deklarieren, sobald das *deviceReady*-Ereignis empfangen wird.

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. Führen Sie die Anwendung aus.
		
	- **Für iOS:**
	
		Starten Sie im `Terminal`-Fenster Ihre App in einer neuen Simulatorinstanz, indem Sie Folgendes ausführen:

			cordova run ios

	- **Für Android:**
		
		Starten Sie im `Terminal`-Fenster Ihre App in einer neuen Emulatorinstanz, indem Sie Folgendes ausführen:

			cordova run android

3. In den Konsolenprotokollen wird Folgendes angezeigt:

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>Verbinden der App mit Überwachung in Echtzeit

[AZURE.INCLUDE [Verbinden der App mit Überwachung in Echtzeit](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern über Pushbenachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen interagieren. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten wird Ihre App für den Empfang eingerichtet.

###Konfigurieren von Push-Anmeldeinformationen für Mobile Engagement

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden darf, müssen Sie den Zugriff auf Ihr Apple iOS-Zertifikat oder den GCM-Server-API-Schlüssel zulassen.
	
1. Navigieren Sie zum Mobile Engagement-Portal. Stellen Sie sicher, dass Sie sich in der App befinden, die für dieses Projekt verwendet wird, und klicken Sie dann unten auf die Schaltfläche **Einbinden**:
	
	![][1]
	
2. Sie gelangen jetzt auf die Einstellungsseite des Mobile Engagement-Portals. Klicken Sie hier auf den Abschnitt **Systemeigener Push**:
	
	![][2]

3. Konfigurieren des iOS-Zertifikats/GCM-Server-API-Schlüssels

	**[iOS]**

	a. Wählen Sie Ihr P12-Zertifikat aus, laden Sie es hoch, und geben Sie anschließend Ihr Kennwort ein:
	
	![][3]

	**[Android]**

	a. Klicken Sie vor **API-Schlüssel** im Abschnitt "GCM Settings" auf das Bearbeitungssymbol, fügen Sie im angezeigten Popupfenster den GCM-Serverschlüssel ein, und klicken Sie auf **OK**.
		
	![][4]

###Aktivieren von Pushbenachrichtigungen in der Cordova-App

Bearbeiten Sie **www/js/index.js**, um den Aufruf Mobile Engagement hinzuzufügen und Pushbenachrichtigungen anfordern und einen Handler deklarieren zu können:

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###Ausführen der App

**[iOS]**

1. Wir erstellen mit XCode die App und stellen sie auf dem Gerät bereit, um Pushbenachrichtigungen zu testen, da iOS nur Pushbenachrichtigungen an ein echtes Gerät unterstützt. Wechseln Sie zu dem Speicherort, an dem das Cordova-Projekt erstellt wurde, und navigieren Sie zu **...\\platforms\\ios**. Öffnen Sie die systemeigene Datei ".xcodeproj" in XCode. 
	
2. Erstellen Sie die Cordova-App und stellen Sie sie auf dem iOS-Gerät bereit. Verwenden Sie hierzu das Konto, das über das Bereitstellungsprofil mit dem Zertifikat, das Sie gerade in das Mobile Engagement-Portal hochgeladen haben, und die App-ID verfügt, die mit der beim Erstellen der Cordova-App bereitgestellten ID übereinstimmt. Sie können den *Bundle identifier* in der Datei **Resources*-info.plist** in XCode nachschlagen, damit die IDs übereinstimmen.

3. Auf Ihrem Gerät wird das iOS-Standardpopup mit der Information angezeigt, dass die App die Berechtigung zum Senden von Benachrichtigungen anfordert. Erteilen Sie die Berechtigung.

**[Android]**

Sie können einfach den Emulator zum Ausführen der Android-App verwenden, da GCM-Benachrichtigungen im Android-Emulator unterstützt werden.

	cordova run android

##<a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen jetzt eine einfache Pushbenachrichtigungskampagne, die eine Pushbenachrichtigung an die auf dem Gerät ausgeführte App sendet.

1. Navigieren Sie zu der Registerkarte **Reach** in Ihrem Mobile Engagement-Portal.

2. Klicken Sie auf **Neue Ankündigung**, um die Push-Kampagne zu erstellen.

	![][6]

3. Nehmen Sie die Eingaben vor, um Ihre Kampagne **[Android]** zu erstellen.
	
	- Geben Sie einen Text für **Name** für die Kampagne an. 
	- Wählen Sie unter **Übermittlungstyp** für *Systembenachrichtigung* die Option *Einfach* aus.
	- Wählen Sie für die Übermittlungszeit *Immer* aus.
	- Geben Sie einen **Titel** für die Benachrichtigung an, der in der ersten Zeile der Pushbenachrichtigung angezeigt wird.
	- Geben Sie eine **Nachricht** für die Benachrichtigung an, die als Nachrichtentext dient. 

	![][11]

4. Nehmen Sie die Eingaben vor, um Ihre Kampagne **[iOS]** zu erstellen.

	- Geben Sie einen Text für **Name** für die Kampagne an. 
	- Wählen Sie für die Übermittlungszeit *Out of app only* aus.
	- Geben Sie einen **Titel** für die Benachrichtigung an, der in der ersten Zeile der Pushbenachrichtigung angezeigt wird.
	- Geben Sie eine **Nachricht** für die Benachrichtigung an, die als Nachrichtentext dient. 
 
	![][12]

5. Scrollen Sie nach unten, und wählen Sie im Inhaltsbereich die Option **Nur Benachrichtigung** aus.

	![][8]

6. [Optional] Sie können auch eine Aktions-URL angeben. Stellen Sie sicher, dass dabei ein URL-Schema verwendet wird, das beim Konfigurieren der Variablen **AZME REDIRECT URL** bereitgestellt wurde, z. B. **myapp://test*.

7. Sie haben das Festlegen der Basiskampagne abgeschlossen. Scrollen Sie nun wieder nach unten, und klicken Sie auf die Schaltfläche **Erstellen**, um Ihre Kampagne zu speichern.

8. Als letzten Schritt **Aktivieren** Sie die Kampagne.
	
	![][10]

9. Auf Ihrem Gerät oder im Emulator sollte nun eine Pushbenachrichtigung im Rahmen dieser Kampagne angezeigt werden.

##<a id="next-steps"></a>Nächste Schritte
[Übersicht über alle Methoden mit dem Cordova Mobile Engagement SDK verfügbaren Methoden](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png

[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

<!---HONumber=AcomDC_0218_2016-->