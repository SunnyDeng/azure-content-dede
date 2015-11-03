<properties
	pageTitle="Erste Schritte mit Azure Mobile Engagement"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für Android-Apps."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für Android-Apps

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen können und wie Sie Pushbenachrichtigungen an segmentierte Benutzer einer Android-Anwendung senden. In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Darin erstellen Sie eine leere Android-App, die einfache Daten erfasst und Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Das Android SDK (es wird davon ausgegangen, dass Sie Android Studio verwenden), das Sie [hier herunterladen](http://go.microsoft.com/fwlink/?LinkId=389797) können.
+ Das [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT]Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für Android-Apps. Damit Sie es abschließen können, ist ein aktives Azure-Konto erforderlich. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-DE%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

[AZURE.INCLUDE [Erstellen einer Mobile Engagement-App im Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine "einfache Integration" dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Dokumentation zur Integration finden Sie unter [Android SDK für Mobile Engagement](../mobile-engagement-android-sdk-overview/).

Wir werden eine einfache App mit Android Studio erstellen, um die Integration zu veranschaulichen.

###Erstellen eines neuen Android-Projekts

1. Starten Sie **Android Studio**, und wählen Sie im Popupfenster die Option **Start a new Android Studio project** aus.

    ![][1]

2. Geben Sie einen App-Namen und eine Unternehmensdomäne ein. Notieren Sie sich, was Sie eingeben, da Sie diese Informationen später verwenden. Klicken Sie auf **Weiter**.

    ![][2]

3. Wählen Sie den Zielformfaktor und die API-Ebene aus, und klicken Sie auf **Next**.
	
	>[AZURE.NOTE]Mobile Engagement erfordert mindestens „API Level 10“ (Android 2.3.3).

    ![][3]

4. Wählen Sie hier **Blank Activity** aus (dies ist der einzige Bildschirm für diese App), und klicken Sie auf **Next**.

    ![][4]

5. Übernehmen Sie schließlich die Standardeinstellungen, und klicken Sie auf **Finish**.

    ![][5]

Android Studio erstellt jetzt die Demo-App, in die wir Mobile Engagement integrieren.

###Schließen Sie die SDK-Bibliothek in Ihr Projekt ein

Herunterladen und Integrieren der SDK-Bibliothek

1. Laden Sie das [Mobile Engagement Android SDK] herunter.
2. Extrahieren Sie die Archivdatei in einem Ordner auf Ihrem Computer.
3. Ermitteln Sie die JAR-Bibliothek für die aktuelle Version dieses SDKs, und kopieren Sie sie in die Zwischenablage.

	  ![][6]

4. Navigieren Sie zum **Projektabschnitt** (1), und fügen Sie die JAR-Datei in den Ordner "libs" (2) ein.

	  ![][7]

5. Synchronisieren Sie das Projekt, um die Bibliothek zu laden.

	  ![][8]

###Verbinden der App mit Mobile Engagement-Back-End mithilfe der Verbindungszeichenfolge

1. Kopieren Sie die folgenden Codezeilen in die Erstellung der Aktivität (muss nur an einer Stelle der App vorgenommen werden, in der Regel in der Hauptaktivität). Öffnen Sie für diese Beispiel-App "MainActivity" im Ordner "Src" -> "main" -> "java", und fügen Sie Folgendes hinzu:

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Lösen Sie die Verweise auf, indem Sie ALT+EINGABE drücken oder die folgenden import-Anweisungen hinzufügen:

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Wechseln Sie zurück zum Azure-Portal auf der Seite **Verbindungsinformationen** Ihrer App und kopieren Sie die **Verbindungszeichenfolge**.

	  ![][9]

4. Fügen Sie diese in den `setConnectionString`-Parameter ein, um das bereitgestellte Beispiel wie folgt zu ersetzen:

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###Hinzufügen von Berechtigungen und einer Dienstdeklaration

1. Fügen Sie diese Berechtigungen unmittelbar vor oder nach dem `<application>`-Tag zur Datei „Manifest.xml“ des Projekts hinzu:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Fügen Sie folgendes zwischen den Tags `<application>` und `</application>` hinzu, um den Agentdienst zu deklarieren:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. Ersetzen Sie in dem Code, den Sie gerade eingefügt haben, `"<Your application name>"` in der Beschriftung (in "label"). Dies wird im Menü **Einstellungen** angezeigt. Hier können Benutzer die auf dem Gerät ausgeführten Dienste sehen. Sie können z. B. das Wort "Dienst" in der Beschriftung hinzufügen.

###Senden eines Bildschirms an Mobile Engagement

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken.

Wechseln Sie zu **Mainactivity.java**, und fügen Sie Folgendes hinzu, um die Basisklasse von **MainActivity** durch **EngagementActivity** zu ersetzen:

	public class MainActivity extends EngagementActivity {

Für dieses einfache Beispielszenario können Sie die folgende Zeile auskommentieren (ausschließen):

    // setSupportActionBar(toolbar);

Wenn Sie diese um beibehalten möchten, sollten Sie das Szenario „Grundlegende Berichterstellung“ in unserem Artikel zur [erweiterten Android-Integration] lesen.

##<a id="monitor"></a>Verbinden der App mit Überwachung in Echtzeit

[AZURE.INCLUDE [Verbinden der App mit Überwachung in Echtzeit](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen erreichen. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten richten Sie Ihre App für den Empfang ein.

### Aktivieren von In-App-Messaging

1. Kopieren Sie die nachfolgenden In-App-Messaging-Ressourcen in die Datei „Manifest.xml“ zwischen die Tags `<application>` und `</application>`.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

2. Kopieren Sie die Ressourcen mithilfe der folgenden Schritte in das Projekt:
	1. Navigieren Sie wieder zum heruntergeladenen SDK-Inhalt, und kopieren Sie den Ordner "res".

		 ![][13]

	2. Wechseln Sie zurück zu Android Studio, und wählen Sie das Verzeichnis "main" mit den Projektdateien aus. Fügen Sie dieses anschließend ein, um die Ressourcen zum Projekt hinzuzufügen.

		 ![][14]

###Geben Sie ein Symbol für Benachrichtigungen an.

Fügen Sie den folgenden XML-Codeausschnitt in die Datei „Manifest.xml“ zwischen die Tags `<application>` und `</application>` ein.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Dies definiert das Symbol, das in System- und In-App-Benachrichtigungen angezeigt wird. Es ist für In-App-Benachrichtigungen optional, für Systembenachrichtigungen jedoch obligatorisch. Android weist Systembenachrichtigungen mit ungültigen Symbolen zurück.

Stellen Sie sicher, dass Sie ein Symbol in einem der **ziehbaren** Ordner verwenden (z. B. ``engagement_close.png``). Ordner vom Typ **mipmap** werden nicht unterstützt.

>[AZURE.NOTE]Sie sollten das Symbol für das **Startprogramm** nicht verwenden. Es weist eine andere Auflösung auf und befindet sich in der Regel in den Mipmap-Ordnern, die nicht unterstützt werden.

Für reale Apps können Sie ein Symbol verwenden, das entsprechend den [Android-Entwurfsrichtlinien](http://developer.android.com/design/patterns/notifications.html) für Benachrichtigungen geeignet ist.

>[AZURE.TIP]Um sicherzustellen, dass Sie die richtigen Auflösungen für Symbole verwenden, betrachten Sie [diese Beispiele](https://www.google.com/design/icons). Führen Sie einen Bildlauf nach unten zum Abschnitt **Benachrichtigung** durch, klicken Sie auf ein Symbol, und klicken Sie dann auf `PNGS`, um den ziehbaren Symbolsatz herunterzuladen. Es wird für jede Version des Symbols angezeigt, welche ziehbaren Ordner für welche Auflösung verwendet werden.

##Erstellen eines Google Cloud Messaging-Projekts mit API-Schlüssel 

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Aktivieren der App für den Empfang von GCM-Pushbenachrichtigungen

1. Fügen Sie in Ihrer Datei „Manifest.xml“ Folgendes zwischen die Tags `<application>` und `</application>` ein, nachdem Sie die aus der Google Play-Konsole abgerufene `project number` ersetzt haben. \\n ist beabsichtigt, stellen Sie daher sicher, dass Sie die Projektnummer damit endet.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Fügen Sie den Code unten in die Datei „Manifest.xml“ zwischen den Tags `<application>` und `</application>` ein. Ersetzen Sie den Paketnamen <Your package name>.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Fügen Sie die letzte Berechtigungsgruppe, die hervorgehoben ist, vor dem `<application>`-Tag hinzu. Ersetzen Sie `<Your package name>` durch den tatsächlichen Paketnamen der Anwendung.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Gewähren des Zugriffs für Mobile Engagement auf den GCM-API-Schlüssel

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden kann, müssen Sie Mobile Engagement den Zugriff auf Ihren API-Schlüssel gewähren. Dies wird durch Konfigurieren und Eingeben Ihres Schlüssels im Mobile Engagement-Portal erreicht.

1. Navigieren Sie zum Mobile Engagement-Portal.

	Stellen Sie über das Azure-Portal sicher, dass Sie sich in der App befinden, die wir für dieses Projekt verwenden, und klicken Sie dann am unteren Rand auf die Schaltfläche **Einbinden**:

	![][15]

2. Klicken Sie dann auf den Abschnitt **Einstellungen** -> **Systemeigener Push**, um Ihren GCM-Schlüssel einzugeben:
	  
	![][16]

3. Klicken Sie im Abschnitt **GCM-Einstellungen** auf das Symbol **Bearbeiten** vor **API-Schlüssel**, wie unten gezeigt:
	  
	![][17]

4. Fügen Sie im Popupfenster den GCM-Serverschlüssel ein, den Sie zuvor bezogen haben, und klicken Sie auf **OK**.

	![][18]

##<a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen nun eine einfache Push-Benachrichtigungskampagne, die eine Push-Benachrichtigung an die App sendet.

1. Navigieren Sie zu der Registerkarte **REACH** in Ihrem Mobile Engagement-Portal.
	 
2. Klicken Sie auf **Neue Ankündigung**, um die Pushbenachrichtigungskampagne zu erstellen.
	 
	![][20]

3. Richten Sie das erste Feld der Kampagne mit den folgenden Schritten ein:
	 
	![][21]

	a. Benennen der Kampagne.

	b. Wählen Sie *Systembenachrichtigung/Einfach* für **Übermittlungstyp**: Dies ist der einfache Android-Pushbenachrichtigungstyp, der einen Titel und eine kleine Textzeile unterstützt.

	c. Wählen Sie für **Lieferzeit** die Option *Jederzeit* aus, um eine Benachrichtigung darüber zu erhalten, ob die App gestartet wurde oder nicht.

	d. Geben Sie in den Benachrichtigungstext den **Titel** ein, der im Pushvorgang fett gedruckt erscheint.

	e. Geben Sie dann Ihre **Nachricht** ein.

4. Navigieren Sie nach unten, und wählen Sie im Bereich **Inhalt** die Option **Nur Benachrichtigung** aus.

	![][22]

5. Sie haben das Festlegen der Basiskampagne abgeschlossen. Navigieren Sie nun wieder nach unten, und klicken Sie auf die Schaltfläche **Erstellen**, um Ihre Kampagne zu speichern.

6. Als letzten Schritt klicken Sie auf **Aktivieren**, um die Kampagne zu aktivieren, damit Pushbenachrichtigungen gesendet werden.
    
	![][24]

<!-- URLs. -->
[Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682
[erweiterten Android-Integration]: https://azure.microsoft.com/de-DE/documentation/articles/mobile-engagement-android-integrate-engagement/#basic-reporting

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=Nov15_HO1-->