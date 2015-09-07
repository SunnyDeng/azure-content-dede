<properties
	pageTitle="Erste Schritte mit Azure Mobile Engagement für iOS in Objective C"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für iOS-Apps."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

# Erste Schritte mit Azure Mobile Engagement für iOS-Apps in Objective C

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen und Pushbenachrichtigungen an segmentierte Benutzer einer iOS-Anwendung senden können. In diesem Lernprogramm erstellen Sie eine leere iOS-App, die einfache Daten erfasst und Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNS) empfängt. Nach Abschluss dieses Lernprogramms können Sie Pushbenachrichtigungen an alle Geräte oder zielspezifische Benutzer basierend auf ihren Geräteeigenschaften übertragen.

In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Mobile Engagement zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Xcode, den Sie aus dem MAC App Store installieren können
+ Das [Mobile Engagement iOS SDK]
+ Ein Pushbenachrichtigungszertifikat (P12), das Sie im Apple Dev Center abrufen können.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für iOS-Apps.

> [AZURE.IMPORTANT]Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für iOS-Apps. Damit Sie es abschließen können, ist ein aktives Azure-Konto erforderlich. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-DE%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

1. Melden Sie sich beim Azure-Portal an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste**, dann auf **Mobile Engagement** und anschließend auf **Erstellen**.

	![][7]

3. Geben Sie in das Popupfenster, das angezeigt wird, die folgenden Informationen ein:

   ![][8]

   - **Name der Anwendung**: Geben Sie den Namen der Anwendung ein. Sie können jedes beliebige Zeichen verwenden.
   - **Platform**: Wählen Sie die Zielplattform (**iOS**) für die App (Wenn Ihre App auf mehrere Plattformen ausgerichtet ist, wiederholen Sie dieses Lernprogramm für jede Plattform).
   - **Ressourcenname der Anwendung**: Dies ist der Name, mit dem Sie über APIs und URLs auf diese Anwendung zugreifen können. Sie können ausschließlich konventionelle URL-Zeichen verwenden. Der automatisch generierte Name sollte Ihnen eine starke Basis bieten. Außerdem sollen Sie den Plattformnamen anfügen, um einen Namenskonflikt zu vermeiden, da dieser Name eindeutig sein muss.
   - **Standort**: Wählen Sie das Rechenzentrum, in denen diese App (und vor allem die Sammlung) gehostet werden soll.
   - **Sammlung**: Wenn Sie bereits eine Anwendung erstellt haben, wählen Sie eine zuvor erstellte **Sammlung**; wählen Sie andernfalls **Neue Sammlung**.
   - **Name der Sammlung**: Dieser steht für die Anwendungsgruppe. Dadurch wird außerdem sichergestellt, dass sich alle Ihre Apps in einer Gruppe befinden, wodurch aggregierte Berechnungen von Metriken möglich sind. Sie sollten hier ggf. Ihren Firmenname oder Ihre Abteilung verwenden.

4. Wählen Sie die Anwendung aus, die Sie soeben in der Registerkarte **Anwendungen** erstellt haben.

5. Klicken Sie auf **Verbindungsinformationen**, um die Verbindungseinstellungen für die SDK-Integration ihrer mobilen App anzuzeigen.

	![][10]

6. Kopieren Sie die **Verbindungszeichenfolge** – Diese benötigen Sie zum Identifizieren dieser App in Ihrem Anwendungscode und zum Herstellen der Verbindung mit Mobile Engagement von Ihrer Phone-App aus.

	![][11]

##<a id="connecting-app"></a>Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine "einfache Integration" dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Dokumentation zur Integration finden Sie in der Dokumentation zum Mobile Engagement iOS SDK.

Wir erstellen eine einfache App mit Xcode, um die Integration zu veranschaulichen.

###Erstellen eines neuen iOS-Projekts

Sie können diesen Schritt überspringen, wenn Sie bereits eine App besitzen und mit der iOS-Entwicklung vertraut sind.

1. Starten Sie Xcode, und wählen Sie im Popupfenster **Erstellen eines neuen Xcode-Projekts** aus.

	![][12]

2. Wählen Sie **Single View Application** aus, und klicken Sie dann auf **Weiter**.

	![][14]

3. Geben Sie **Produktname**, **Organisationsname** und **Organisations-ID** ein. Stellen Sie sicher, dass im Feld **Sprache** **Objective-C** ausgewählt haben.

	![][13]

Xcode erstellt die Demo-App, in die wir Mobile Engagement integrieren.

###Verbinden der App mit dem Mobile Engagement-Back-End

1. Laden Sie das [Mobile Engagement iOS-SDK] herunter.
2. Extrahieren Sie die ".tar.gz"-Datei in einen Ordner auf Ihrem Computer.
3. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Add files to** aus.

	![][17]

4. Navigieren Sie zu dem Ordner, in dem Sie das SDK extrahiert haben, und wählen Sie den Ordner `EngagementSDK` aus. Anschließend drücken Sie auf **OK**.

	![][18]

5. Öffnen Sie die Registerkarte **Build Phases**, und fügen Sie im Menü **Link Binary With Libraries** wie unten gezeigt die Frameworks hinzu:

	![][19]

6. Wechseln Sie zurück zum Azure-Portal auf der Seite **Verbindungsinformationen** Ihrer App und kopieren Sie die Verbindungszeichenfolge.

	![][11]

7. Öffnen Sie Ihre Implementierungsdatei für den Anwendungsdelegaten und fügen Sie die folgende Codezeile hinzu.

		#import "EngagementAgent.h"

8. Fügen Sie die Verbindungszeichenfolge in den `didFinishLaunchingWithOptions` Delegaten ein.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##<a id="monitor"></a>Aktivieren der Überwachung in Echtzeit

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken.

- Öffnen Sie die Datei **ViewController.h**, importieren Sie **EngagementViewController.h**, und ersetzen Sie die übergeordnete Klasse der **ViewController**-Schnittstelle durch **EngagementViewController**, wie unten dargestellt:

![][22]

###Stellen Sie sicher, dass Ihre App mit der Echtzeitüberwachung verbunden ist.

In diesem Abschnitt erfahren Sie, wie Sie sicherstellen, dass Ihre App eine Verbindung mit dem Mobile Engagement-Back-End mithilfe der Echtzeitüberwachungsfunktion von Mobile Engagement herstellt.

1. Navigieren Sie zum Mobile Engagement-Portal.

	Stellen Sie über das Azure-Portal sicher, dass Sie sich in der App befinden, die wir für dieses Projekt verwenden, und klicken Sie dann am unteren Rand auf die Schaltfläche **Einbinden**.

	![][26]

2. Sie gelangen dann zur Seite **Einstellungen** des Mobile Engagement-Portals für Ihre App. Klicken Sie hier auf die Registerkarte **Überwachen**:

	![][30]

3. Die Überwachung kann Ihnen ein beliebiges Gerät in Echtzeit anzeigen, das Ihre App startet:

	![][31]

4. Wenn Sie sich wieder in Xcode befinden, starten Sie Ihre App im Simulator oder auf einem angeschlossenen Gerät.

5. Wenn es funktioniert hat, sollte jetzt eine Sitzung in der Überwachung angezeigt werden!

**Glückwunsch!** Sie haben den ersten Schritt dieses Lernprogramms abgeschlossen und verfügen über eine App, die eine Verbindung zum Mobile Engagement-Back-End herstellt und bereits Daten sendet.

6. Wenn Sie auf die Taste **Start** des Simulators klicken, wird die Anzahl der Sitzungen im Monitor auf 0 zurückgesetzt, wie oben gezeigt.

	![][33]

##<a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mit Mobile Engagement können Sie mit Ihren Benutzern und mit REACH mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen interagieren. Dieses Modul nennt sich REACH im Mobile Engagement-Portal. In den folgenden Abschnitten richten Sie Ihre App für den Empfang ein.

### Aktivieren der App für den Empfang von stillen Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]


### Fügen Sie die Reach-Bibliothek zum Projekt hinzu.

1. Klicken Sie mit der rechten Maustaste auf das Projekt.
2. Wählen Sie **Add file to** aus.
3. Navigieren Sie zu dem Ordner, in dem Sie das SDK extrahiert haben.
4. Wählen Sie den Ordner `EngagementReach`.
5. Klicken Sie auf **Hinzufügen**.

### Ändern des Anwendungsdelegaten

1. Importieren Sie am Beginn Ihrer Implementierungsdatei das Engagement Reach-Modul.

		#import "AEReachModule.h"

2. Erstellen Sie innerhalb von **application:didFinishLaunchingWithOptions** ein Reach-Modul, und übergeben Sie es an die vorhandene Engagement-Initialisierungszeile:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###Aktivieren Ihrer App für den Empfang von APNS-Pushbenachrichtigungen.

1. Fügen Sie folgende Zeile in die **application:didFinishLaunchingWithOptions**-Methode ein:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Fügen Sie die **application:didRegisterForRemoteNotificationsWithDeviceToken**-Methode wie folgt hinzu:

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Fügen Sie die **didReceiveRemoteNotification:fetchCompletionHandler**-Methode wie folgt hinzu:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

###Mobile Engagement den Zugriff auf Pushbenachrichtigungen erteilen

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden darf, müssen Sie den Zugriff auf Ihr Zertifikat gestatten. Dies wird durch Konfigurieren und Eingeben Ihres Zertifikats im Mobile Engagement-Portal erreicht. Stellen Sie sicher, dass Sie Ihr P12-Zertifikat erhalten, wie in der Apple-Dokumentation beschrieben.

1. Navigieren Sie zum Mobile Engagement-Portal. Stellen Sie sicher, dass Sie sich in der App befinden, die für dieses Projekt verwendet wird, und klicken Sie dann unten auf die Schaltfläche **Beginnen**:

	![][26]

2. Sie gelangen jetzt auf die Seite **Einstellungen** des Mobile Engagement-Portals. Klicken Sie hier auf den Abschnitt **Systemeigener Push**, um Ihr P12-Zertifikat einzugeben:

	![][27]

3. Wählen Sie Ihr P12-Zertifikat aus, laden Sie es hoch, und geben Sie anschließend Ihr Kennwort ein:

	![][28]

4. Fügen Sie jetzt Ihr Bereitstellungsprofil hinzu, und erstellen Sie Ihre App für ein Zielgerät.

Sie sind fertig, und wir werden jetzt überprüfen, ob Sie diese einfache Integration ordnungsgemäß durchgeführt haben.

##<a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen jetzt eine einfache Pushbenachrichtigungskampagne, die eine Pushbenachrichtigung an die App sendet:

1. Navigieren Sie zu der Registerkarte **Reach** in Ihrem Mobile Engagement-Portal.

2. Klicken Sie auf **Neue Ankündigung**, um die Push-Benachrichtigungskampagne zu erstellen.

	![][35]

3. Richten Sie das erste Feld der Kampagne ein:

	![][36]

	- 	Wählen Sie einen beliebigen Namen für die Kampagne.
	- 	Wählen Sie für **Übermittlungszeit** die Option **Nur außerhalb der App** aus: Dies ist der einfache Apple-Pushbenachrichtigungstyp, der Text unterstützt.
	- 	Geben Sie in den Benachrichtigungstext zunächst den Titel ein, der bei der Pushübertragung als erste Zeile angezeigt wird.
	- 	Geben Sie dann Ihre Nachricht ein, die die zweite Zeile darstellt.


4. Navigieren Sie nach unten, und wählen Sie im **Inhaltsbereich** die Option "Nur Benachrichtigung" aus.

	![][37]

5. Sie haben das Festlegen der Basiskampagne abgeschlossen. Scrollen Sie nun wieder nach unten, und erstellen Sie dann die Kampagne, um sie zu speichern! ![][38]

6. Als letzten Schritt aktivieren Sie die Kampagne. ![][39]

7. Jetzt sollte eine Pushbenachrichtigung auf dem Gerät angezeigt werden.

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement iOS-SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->