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
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="piyushjo" />
	
# Erste Schritte mit Azure Mobile Engagement für Android-Apps

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen und Pushbenachrichtigungen an segmentierte Benutzer einer Android-Anwendung senden können. In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Darin erstellen Sie eine leere Android-App, die einfache Daten erfasst und Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Nach Abschluss dieses Lernprogramms können Sie Pushbenachrichtigungen an alle Geräte oder zielspezifische Benutzer basierend auf ihren Geräteeigenschaften übertragen. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Mobile Engagement zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.


Für dieses Lernprogramm ist Folgendes erforderlich:

+ Das Android SDK (es wird davon ausgegangen, dass Sie Android Studio verwenden), das Sie [hier herunterladen](http://go.microsoft.com/fwlink/?LinkId=389797) können.
+ Das [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT]Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für Android-Apps. Damit Sie es abschließen können, ist ein aktives Azure-Konto erforderlich. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

<!--
## <a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

## <a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste**, dann auf **Mobile Engagement** und anschließend auf **Erstellen**.

   	![][7]

3. Geben Sie in das Popupfenster, das angezeigt wird, die folgenden Informationen ein:
 
   	![][8]

	1. **Name der Anwendung**: Sie können den Namen der Anwendung eingeben. Sie können jedes beliebige Zeichen verwenden.
	2. **Platform**: Wählen Sie die Zielplattform für die App (Wenn Ihre App auf mehrere Plattformen ausgerichtet ist, wiederholen Sie dieses Lernprogramm für jede Plattform).
	3. **Ressourcenname der Anwendung**: Dies ist der Name, mit dem Sie über APIs und URLs auf diese Anwendung zugreifen können. Wir empfehlen die ausschließliche Verwendung von konventionellen URL-Zeichen: Der automatisch generierte Name sollte eine sichere Basis bereitstellen. Wir empfehlen auch das Anfügen des Plattformnamens, um einen Namenskonflikt zu vermeiden, da dieser Name eindeutig sein muss.
	4. **Standort**: Wählen Sie das Rechenzentrum, in denen diese App (und vor allem die Sammlung – siehe unten) gehostet werden soll.
	5. **Sammlung**: Wenn Sie bereits eine Anwendung erstellt haben, wählen Sie eine zuvor erstellte Sammlung; wählen Sie andernfalls „Neue Sammlung“.
	6. **Name der Sammlung**: Dieser steht für die Anwendungsgruppe. Dadurch wird außerdem sichergestellt, dass sich alle Ihre Apps in einer Gruppe befinden, wodurch aggregierte Berechnungen möglich sind. Es wird dringend empfohlen, den Firmennamen oder die Abteilung zu verwenden.


	Wenn Sie fertig sind, klicken Sie auf das Kontrollkästchen, um die Erstellung Ihrer App zu beenden.

4. Klicken Sie auf/wählen Sie die Anwendung aus, die Sie soeben in der Registerkarte **Anwendung** erstellt haben.
 
   	![][9]

5. Klicken Sie dann auf **Verbindungsinformationen**, um die Verbindungseinstellungen für die SDK-Integration anzuzeigen.
 
   	![][10]

6. Notieren Sie abschließend die **Verbindungszeichenfolge**, die Sie benötigen, um diese Anwendung aus dem Anwendungscode zu identifizieren.

   	![][11]

	>[AZURE.TIP]Sie können auch das Symbol „Kopieren“ auf der rechten Seite der Verbindungszeichenfolge verwenden, um diese der Einfachheit halber in die Zwischenablage zu kopieren.

## <a id="connecting-app"></a>Verbinden Ihrer App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine „einfache Integration“ dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Dokumentation zur Integration finden Sie in der [Dokumentation zum Mobile Engagement Android SDK].

Wir werden eine einfache App mit Android Studio erstellen, um die Integration zu veranschaulichen.

### Erstellen eines neuen Android-Projekts

Sie können diesen Schritt überspringen, wenn Sie bereits eine App besitzen und mit der Android-Entwicklung vertraut sind.

1. Starten Sie Android Studio, und wählen Sie im Popupfenster **Start a new Android Studio project** aus.

   	![][12]

2. Geben Sie den App-Namen und die Unternehmensdomäne ein. Notieren Sie sich diese Informationen, die später erneut benötigt werden, und klicken Sie dann auf **Next**.

   	![][13]

3. Wählen Sie jetzt den Zielformfaktor und die API-Ebene aus, und klicken Sie dann auf **Next**.
	>[AZURE.NOTE]Mobile Engagement erfordert mindestens „API Level 10“ (Android 2.3.3).

   	![][14]

4. Jetzt wird eine Aktivität zur einfachen App hinzugefügt, bei der es sich um ihren hauptsächlichen und einzigen Bildschirm handelt. Stellen Sie sicher, dass **Blank Activity** ausgewählt ist, und klicken Sie dann auf **Next**.

   	![][15]

5. Für den Zweck dieses Lernprogramms können Sie auf der letzten Seite des Assistenten alle Optionen belassen, und klicken Sie dann auf **Finish**.

   	![][16]

Android Studio erstellt jetzt die Demo-App, in die wir Mobile Engagement integrieren.

### Schließen Sie die SDK-Bibliothek in Ihr Projekt ein

Herunterladen und Integrieren der SDK-Bibliothek

1. Laden Sie das [Mobile Engagement Android SDK] herunter.
2. Extrahieren Sie die Archivdatei in einem Ordner auf Ihrem Computer.
3. Ermitteln Sie die JAR-Bibliothek für die aktuelle Version dieses SDKs (diese Dokumentation wurde für Version 3.0.0 vorbereitet), und kopieren Sie sie in die Zwischenablage.

	![][17]

4. Navigieren Sie zum Projektabschnitt (1), und fügen Sie die JAR-Datei in den Ordner „libs“ (2) ein.

	![][18]

5. Synchronisieren Sie das Projekt, um die Bibliothek zu laden.

	![][19]


### Verbinden der App mit Mobile Engagement-Back-End mithilfe der Verbindungszeichenfolge

1. Kopieren Sie die folgenden Codezeilen in die Erstellung der Aktivität (muss nur an einer Stelle der App vorgenommen werden, in der Regel in der Hauptaktivität).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Wechseln Sie zurück zum Azure-Portal auf der Seite **Verbindungsinformationen** Ihrer App und kopieren Sie die **Verbindungszeichenfolge**.

	![][11]

3. Fügen Sie diese in den `setConnectionString`-Parameter ein, um das bereitgestellte Beispiel wie folgt zu ersetzen („AppId“ und „Sdkkey“ wurden unten ausgeblendet).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. „EngagementConfiguration“ und „EngagementAgent“ werden möglicherweise als „nicht aufgelöst“ angezeigt (im Code rot dargestellt). Klicken Sie auf die einzelnen nicht aufgelösten Klassen, und drücken Sie zur automatischen Auflösung ALT+EINGABE.

	![][20]

### Hinzufügen von Berechtigungen und Dienstdeklarationen

1. Fügen Sie diese Berechtigungen unmittelbar vor oder nach dem `<application>`-Tag zur Datei „Manifest.xml“ des Projekts hinzu:
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

	Das Ergebnis sollte wie unten dargestellt angezeigt werden:

	![][21]

2. Fügen Sie folgendes zwischen den Tags < application > und </application > hinzu, um den Agentdienst zu deklarieren:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>Service"
 			android:process=":Engagement"/>

3. Ersetzen Sie im soeben eingefügten Code die Bezeichnung „< Your application name>“. Zum Beispiel:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="MySuperAppService"
 			android:process=":Engagement"/>

### Senden eines Bildschirms an Mobile Engagement

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken. Dies wird dadurch erreicht, dass für unsere Aktivität mit der vom SDK bereitgestellten Klasse „EngagementActivity“ eine Unterklasse erstellt wird. Ersetzen Sie dazu die übergeordnete Klasse von „MainActivity“, die sich vor „ActionBarActivity“ befindet, durch „EngagementActivity“, wie unten veranschaulicht:

![][22]

>[AZURE.NOTE]Vergessen Sie nicht, die Klasse aufzulösen, wenn sie rot dargestellt wird, indem Sie auf die Klasse klicken und ALT+EINGABE drücken.

## <a id="monitor"></a>So überprüfen Sie, ob Ihre App mit der Echtzeitüberwachung verbunden ist

In diesem Abschnitt erfahren Sie, wie Sie sicherstellen, dass Ihre App eine Verbindung mit dem Mobile Engagement-Back-End mithilfe der Echtzeitüberwachungsfunktion von Mobile Engagement herstellt.

1. Navigieren Sie zum Mobile Engagement-Portal.

	Stellen Sie im Azure-Portal sicher, dass Sie sich in der App befinden, die für dieses Projekt verwendet wird, und klicken Sie dann unten auf die Schaltfläche **Beginnen**.

	![][26]

2. Sie gelangen zu der Seite „Einstellungen“ des Engagement-Portals Ihrer App. Klicken Sie dort auf die Registerkarte **Überwachen**, wie unten dargestellt.![][30]

3. Die Überwachung ist bereit, in Echtzeit jedes Gerät anzuzeigen, auf dem Ihre App gestartet wird.![][31]

4. Wechseln Sie wieder zu Android Studio, starten Sie die App entweder in der Überwachung oder auf einem verbundenen Gerät, indem Sie auf das grüne Dreieck klicken und dann das Gerät auswählen.![][32]

5. Wenn es funktioniert hat, sollte jetzt eine Sitzung in der Überwachung angezeigt werden!![][33]

**Glückwunsch!** Sie haben den ersten Schritt dieses Lernprogramms mit einer App abgeschlossen, die eine Verbindung zum Mobile Engagement-Back-End herstellt, das bereits Daten sendet.


## <a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen ERREICHEN. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten wird Ihre App für den Empfang eingerichtet.

### Aktivieren von In-App-Messaging

1. Kopieren Sie die nachfolgenden In-App-Messaging-Ressourcen in die Datei „Manifest.xml“ zwischen die Tags < application > und </application >.

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
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>

2. Kopieren Sie die Ressourcen mithilfe der folgenden Schritte in das Projekt:
	1. Navigieren Sie wieder zum heruntergeladenen SDK-Inhalt, und öffnen Sie den „res“-Ordner.
	2. Wählen Sie die beiden Ordner aus, und kopieren Sie sie in die Zwischenablage.

		![][23]

	4. Wechseln Sie wieder zum Android Studio, wählen Sie den „res“-Abschnitt des Projekts aus, und fügen Sie die Ressourcen durch Einfügen zum Projekt hinzu.

		![][24]

### Angeben eines Standardsymbols in Benachrichtigungen
Der folgende Code definiert das Standardsymbol, das mit Benachrichtigungen angezeigt wird. Hier haben wir das Symbol verwendet, das mit dem in Android Studio erstellten Projekt bereitgestellt wird. Dieser XML-Codeausschnitt muss in die Datei „Manifest.xml“ zwischen die Tags < application > und </application > eingefügt werden. Stellen Sie sicher, dass ic_launcher in Ihrer Anwendung vorhanden ist, oder verwenden Sie eine andere Symboldatei da andernfalls die Benachrichtigung nicht angezeigt wird.

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

### Aktivieren der App für den Empfang von GCM-Pushbenachrichtigungen

1. Geben Sie die „gcm:sender“-Metadaten durch Kopieren und Einfügen des folgenden Codes in die Datei „Manifest.xml“ zwischen den Tags < application > und </application > ein. Der unten ausgeblendete Wert (mit Sternen) ist die `project number`, die von Ihrer Google Play-Konsole abgerufen wird. \\n ist beabsichtigt, stellen Sie daher sicher, dass Sie die Projektnummer damit endet. 

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Fügen Sie den Code unten in die Datei „Manifest.xml“ zwischen den Tags < application > und </application > ein. Beachten Sie, dass in `<category android:name="com.mycompany.mysuperapp" />` der Paketname des Projekts verwendet wurde. Dieser unterscheidet sich in Ihrem eigenen Produktionsprojekt.

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
				<category android:name="com.mycompany.mysuperapp" />
			</intent-filter>
		</receiver>

3. Fügen Sie die letzte Berechtigungsgruppe, die unten hervorgehoben ist, vor oder nach dem < application>-Tag hinzu. Hier wurde erneut der Projektpaketname verwendet, den Sie für Ihre Produktions-App ersetzen müssen.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

### Gewähren des Zugriffs auf den GCM-API-Schlüssel für Mobile Engagement

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden kann, müssen Sie Mobile Engagement den Zugriff auf Ihren API-Schlüssel gewähren. Dies wird durch Konfigurieren und Eingeben Ihres Schlüssels im Mobile Engagement-Portal erreicht.

1. Navigieren zum Mobile Engagement-Portal

	Stellen Sie im Azure-Portal sicher, dass Sie sich in der App befinden, die für dieses Projekt verwendet wird, und klicken Sie dann unten auf die Schaltfläche **Beginnen**.

	![][26]

2. Sie befinden sich jetzt auf der Einstellungsseite des Mobile Engagement-Portals. Klicken Sie hier auf den Abschnitt **Systemeigener Push**, um Ihren GCM-Schlüssel einzugeben:![][27]

3. Klicken Sie im Abschnitt der GCM-Einstellungen auf das Bearbeitungssymbol vor dem **API-Schlüssel**, wie unten gezeigt:![][28]

4. Fügen Sie den im Abschnitt [Aktivieren von Google Cloud Messaging](#register) erhaltenen GCM-Serverschlüssel ein, und klicken Sie dann auf **OK**.

	![][29]

Fertig. Jetzt stellen wir sicher, dass diese Basisintegration richtig durchgeführt wurde.

> [AZURE.IMPORTANT]Stellen Sie sicher, dass Sie für die Erstellung und den Start diesen neuen Code verwenden, beenden Sie dann die App und warten Sie ungefähr eine Minute, bevor Sie Folgendes ausführen:

## <a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen nun eine einfache Push-Benachrichtigungskampagne, die eine Push-Benachrichtigung an die App sendet.

1. Navigieren Sie zu der Registerkarte **REACH** in Ihrem Mobile Engagement-Portal.![][34]

2. Klicken Sie auf **Neue Ankündigung**, um die Push-Kampagne zu erstellen.![][35]

3. Richten Sie das erste Feld der Kampagne mit den folgenden Schritten ein:![][36]

	1. Wählen Sie einen beliebigen Namen für die Kampagne.
	2. Wählen Sie für  **Übermittlungstyp** *Systembenachrichtigung/Einfach*: Dies ist der einfache Android-Pushbenachrichtigungstyp, der einen Titel und eine kleine Textzeile unterstützt.
	3. Wählen Sie für die **Lieferzeit** *Jederzeit*, um eine Benachrichtigung darüber zu erhalten, ob die App gestartet wurde oder nicht.
	4. Geben Sie in den Benachrichtigungstext den Titel ein, der im Push-Vorgang fett gedruckt erscheint
	5. Geben Sie dann Ihre Nachricht ein.

4. Scrollen Sie nach unten und wählen Sie im Inhaltsabschnitt **Nur Benachrichtigung**.![][37]

5. Sie haben das Festlegen der Basiskampagne abgeschlossen, scrollen Sie nun nach unten und erstellen Sie Ihre Kampagne, um sie zu speichern!![][38]

6. Letzter Schritt, aktivieren Sie die Kampagne.![][39]


<!-- URLs. -->
[Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9863935
[Dokumentation zum Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[7]: ./media/mobile-engagement-common/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-common/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-common/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-common/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[21]: ./media/mobile-engagement-android-get-started/permissions.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-common/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-common/clic-monitor-tab.png
[31]: ./media/mobile-engagement-common/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-common/reach-tab.png
[35]: ./media/mobile-engagement-common/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-common/campaign-content.png
[38]: ./media/mobile-engagement-common/campaign-create.png
[39]: ./media/mobile-engagement-common/campaign-activate.png

<!--HONumber=54-->