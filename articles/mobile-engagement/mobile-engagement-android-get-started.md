<properties
	pageTitle="Erste Schritte mit Azure Mobile Engagement"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für Android-Apps."
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Erste Schritte mit Azure Mobile Engagement für Android-Apps

[AZURE.INCLUDE [Umschalter für das Hero-Tutorial](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen können und wie Sie Pushbenachrichtigungen an segmentierte Benutzer einer Android-Anwendung senden. In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Darin erstellen Sie eine leere Android-App, die einfache Daten erfasst und Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Das Android SDK (es wird davon ausgegangen, dass Sie Android Studio verwenden), das Sie [hier herunterladen](http://go.microsoft.com/fwlink/?LinkId=389797) können.
+ Das [Mobile Engagement Android SDK]

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

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

	>[AZURE.NOTE] Mobile Engagement erfordert mindestens „API Level 10“ (Android 2.3.3).

    ![][3]

4. Wählen Sie hier **Blank Activity** aus (dies ist der einzige Bildschirm für diese App), und klicken Sie auf **Next**.

    ![][4]

5. Übernehmen Sie schließlich die Standardeinstellungen, und klicken Sie auf **Finish**.

    ![][5]

Android Studio erstellt jetzt die Demo-App, in die wir Mobile Engagement integrieren.

###Schließen Sie die SDK-Bibliothek in Ihr Projekt ein

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

3. Wechseln Sie auf der Seite **Verbindungsinformationen** Ihrer App zurück zum klassischen Azure-Portal, und kopieren Sie die **Verbindungszeichenfolge**.

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

3. Ersetzen Sie in dem Code, den Sie gerade eingefügt haben, `"<Your application name>"` in der Beschriftung (in "label"). Dies wird im Menü **Einstellungen** angezeigt. Hier können Benutzer die auf dem Gerät ausgeführten Dienste sehen. Sie können z. B. das Wort "Dienst" in der Beschriftung hinzufügen.

###Senden eines Bildschirms an Mobile Engagement

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken.

Wechseln Sie zu **Mainactivity.java**, und fügen Sie Folgendes hinzu, um die Basisklasse von **MainActivity** durch **EngagementActivity** zu ersetzen:

	public class MainActivity extends EngagementActivity {

Für dieses einfache Beispielszenario können Sie die folgende Zeile auskommentieren (ausschließen):

    // setSupportActionBar(toolbar);

Wenn Sie diese um beibehalten möchten, sollten Sie das Szenario „Grundlegende Berichterstellung“ in unserem Artikel zur [erweiterten Android-Integration](mobile-engagement-android-integrate-engagement.md/#basic-reporting) lesen.

##<a id="monitor"></a>Verbinden der App mit Überwachung in Echtzeit

[AZURE.INCLUDE [Verbinden der App mit Überwachung in Echtzeit](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen erreichen. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten richten Sie Ihre App für den Empfang ein.

### Kopieren von SDK-Ressourcen in Ihrem Projekt
	
1. Navigieren Sie wieder zum heruntergeladenen SDK-Inhalt, und kopieren Sie den Ordner **res**.

	![][10]

2. Wechseln Sie zurück zu Android Studio, und wählen Sie das Verzeichnis **main** mit den Projektdateien aus. Fügen Sie dieses anschließend ein, um die Ressourcen zum Projekt hinzuzufügen.

	![][11]

[AZURE.INCLUDE [Aktivieren von Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Aktivieren von In-App-Messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Senden von Benachrichtigungen vom Portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- URLs. -->
[Mobile Engagement Android SDK]: https://aka.ms/vq9mfn

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
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png

<!---HONumber=AcomDC_0330_2016-->