<properties 
	pageTitle="Erste Schritte mit Azure Mobile Engagement für iOS" 
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen unter iOS." 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# Erste Schritte mit Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen und Pushbenachrichtigungen an segmentierte Benutzer einer iOS-Anwendung senden können. 
In diesem Lernprogramm erstellen Sie eine leere iOS-App, die einfache Daten erfasst und Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNS) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie Pushbenachrichtigungen an alle Geräte oder auf Basis ihrer Geräteeigenschaften an bestimmte Benutzer senden.

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Mobile Engagement. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Mobile Engagement zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. 

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Xcode, den Sie aus dem MAC App Store installieren können
+ Das [Mobile Engagement iOS SDK]
+ Ein Pushbenachrichtigungszertifikat (P12), das Sie im Apple Dev Center abrufen können

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für iOS-Apps. 

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

<!--
## <a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

## <a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

1. Melden Sie sich im [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEU**.

2. Klicken Sie auf **App Services**, dann auf **Mobile Engagement** und anschließend auf **Erstellen**.

   	![][7]

3. Im angezeigten Popupfenster müssen Sie einige Felder ausfüllen:
 
   	![][8]

	1. *Anwendungsname*: Sie können den Namen Ihrer Anwendung eingeben. Sie können dazu beliebige Zeichen verwenden.
	2. *Plattform*: Wählen Sie die Zielplattform für die App aus (wenn die App auf mehrere Plattformen ausgerichtet ist, wiederholen Sie dieses Lernprogramm für die einzelnen Plattformen).
	3. *Anwendungsressourcenname*: Dies ist der Name, über den Sie mithilfe von APIs und URLs auf diese Anwendung zugreifen können. Es wird empfohlen, dass Sie ausschließlich herkömmliche URL-Zeichen verwenden: der automatisch generierte Name sollte einen entsprechenden Ansatzpunkt bieten. Zudem wird empfohlen, dass der Plattformname angehängt wird, um mögliche Namenskonflikte zu vermeiden, da dieser Name eindeutig sein muss.
	4. *Speicherort*: Wählen Sie das Rechenzentrum aus, in dem diese App (und noch wichtiger, seine Auflistung - siehe unten) gehostet wird.
	5. *Auflistung*: Wenn Sie bereits eine Anwendung erstellt haben, wählen Sie eine zuvor erstellte Auflistung aus. Andernfalls wählen Sie "Neue Auflistung" aus.


	Wenn Sie fertig sind, klicken Sie auf die Schaltfläche "Prüfen", um die Erstellung der App abzuschließen.

4. Klicken Sie jetzt auf die App, die Sie soeben über die Registerkarte "Anwendung" erstellt haben.
 
   	![][9]

5. Klicken Sie dann auf "Verbindungsinformationen", um die Verbindungseinstellungen anzuzeigen, die für Ihre SDK-Integration übernommen werden.
 
   	![][10]

6. Abschließend notieren Sie sich die "Verbindungszeichenfolge", die Sie zum Identifizieren dieser App im Anwendungscode benötigen.

   	![][11]

	>[AZURE.TIP] Sie können rechts neben der Verbindungszeichenfolge auf das Symbol "Kopieren" klicken, um die Zeichenfolge für den schnellen Zugriff in die Zwischenablage zu kopieren.

## <a id="connecting-app"></a>Verbinden der App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine "einfache Integration" beschrieben, bei der es sich um die Mindestanforderung zum Erfassen von Daten sowie zum Senden einer Pushbenachrichtigung handelt. Die vollständige Dokumentation zur Integration finden Sie in der [Dokumentation zum Mobile Engagement iOS SDK].

Wir werden eine einfache App mit Android Studio erstellen, um die Integration zu veranschaulichen.

### Erstellen eines neuen iOS-Projekts

Sie können diesen Schritt überspringen, wenn Sie bereits eine App besitzen und mit der iOS-Entwicklung vertraut sind.

1. Starten Sie Xcode, und wählen Sie im Popupfenster "Create a new Xcode project" aus.

   	![][12]

2. Geben Sie den App-Namen, den Firmennamen und den Bezeichner ein. Notieren Sie sich diese Informationen, die später erneut benötigt werden, und klicken Sie dann auf "Next".

   	![][13]

3. Wählen Sie jetzt "Single View Application" aus, und klicken Sie dann auf "Next".

   	![][14]


Xcode erstellt die Demo-App, in die wir Mobile Engagement integrieren.

### Einbeziehen der SDK-Bibliothek in ein Projekt

Herunterladen und Integrieren der SDK-Bibliothek

1. Laden Sie das [Mobile Engagement iOS SDK] herunter.
2. Extrahieren Sie die ".tar.gz"-Datei in einem Ordner auf Ihrem Computer.
3. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "Add files to ..." aus.

	![][17]

4. Navigieren Sie zu dem Ordner, in dem Sie das SDK extrahiert haben, und wählen Sie den Ordner `EngagementSDK` aus. Anschließend drücken Sie auf "OK".

	![][18]

5. Öffnen Sie die Registerkarte `Build Phases`, und fügen Sie im Menü `Link Binary With Libraries` wie unten gezeigt die Frameworks hinzu:

	![][19]


### Verbinden der App mit dem Mobile Engagement-Back-End mithilfe der Verbindungszeichenfolge

1. Kopieren Sie die folgenden Codezeilen an den Anfang der Implementierungsdatei für die Anwendungsstellvertretung.

		#import "EngagementAgent.h"

2. Wechseln Sie wieder zum Azure-Portal zur Seite *Verbindungsinformationen* Ihrer App, und kopieren Sie die "Verbindungszeichenfolge".

	![][11]

3. Fügen Sie sie in die `didFinishLaunchingWithOptions`-Stellvertretung ein.		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

## Senden eines Bildschirms an Mobile Engagement

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End senden. Dies wird dadurch erreicht, dass die `UIViewController`-Klasse mit der vom SDK bereitgestellten Klasse `EngagementViewController` überladen wird.
Öffnen Sie dazu die Datei `ViewController.h`, importieren Sie `EngagementViewController.h`, und ersetzen Sie dann die übergeordnete Klasse der  `ViewController`-Schnittstelle durch `EngagementViewController`, wie nachfolgend veranschaulicht:

![][22]

## <a id="monitor"></a>Überprüfen, ob die App in Echtzeit überwacht wird

In diesem Abschnitt wird veranschaulicht, wie Sie mithilfe der Echtzeit-Überwachungsfunktion von Mobile Engagement sicherstellen können, dass Ihre App eine Verbindung zum Mobile Engagement-Back-End herstellt.

1. Navigieren zum Mobile Engagement-Portal

	Stellen Sie über das Azure-Portal sicher, dass Sie sich in der App befinden, die wir für dieses Projekt verwenden, und klicken Sie dann am unteren Rand auf die Schaltfläche zum "Einstellen":

	![][26]

2. Sie gelangen dann zur Einstellungsseite des Mobile Engagement-Portals für Ihre App. Klicken Sie hier auf die Registerkarte "Überwachen":

	![][30]

3. Die Überwachung kann Ihnen ein beliebiges Gerät in Echtzeit anzeigen, das Ihre App startet.

	![][31]

4. Wenn Sie sich wieder in Xcode befinden, starten Sie Ihre App im Simulator oder auf einem angeschlossenen Gerät.

5. Wenn alles funktioniert hat, sollte jetzt eine Sitzung in der Überwachung angezeigt werden. 

**Herzlichen Glückwunsch!** Sie haben den ersten Schritt dieses Lernprogramms abgeschlossen und verfügen über eine App, die eine Verbindung zum Mobile Engagement-Back-End herstellt, das bereits Daten sendet.

6. Wenn Sie auf die Starttaste des Simulators klicken, wird die Anzahl der Sitzungen im Monitor auf 0 zurückgesetzt, wie oben gezeigt.

	![][33]



## <a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging

Mobile Engagement ermöglicht Ihnen die Interaktion und das Erreichen Ihrer Benutzer mit Pushbenachrichtigungen und In-App-Messaging im Kontext von Kampagnen. Dieses Modul wird im Mobile Engagement-Portal als REACH bezeichnet.
In den folgenden Abschnitten wird Ihre App für den entsprechenden Empfang eingerichtet.

### Fügen Sie die Reach-Bibliothek zum Projekt hinzu.

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt.
2. Wählen Sie `Add file to...` aus.
3. Navigieren Sie zu dem Ordner, in dem Sie das SDK extrahiert haben.
4. Wählen Sie den Ordner `EngagementReach` aus.
5. Klicken Sie auf "Hinzufügen".

### Ändern der Anwendungsstellvertretung

1. Importieren Sie am Anfang der Implementierungsdatei das Engagement Reach-Modul.

		#import "AEReachModule.h"
	
2. Erstellen Sie innerhalb von `application:didFinishLaunchingWithOptions` ein Reach-Modul, und übergeben Sie es an die vorhandene Engagement-Initialisierungszeile:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}	

### Aktivieren Ihrer App für den Empfang von APNS-Pushbenachrichtigungen

1. Fügen Sie die folgende Zeile in die Methode `application:didFinishLaunchingWithOptions` ein:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Fügen Sie die `application:didRegisterForRemoteNotificationsWithDeviceToken`-Methode wie folgt hinzu:
 
		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 		   [[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Fügen Sie die `didReceiveRemoteNotification`-Methode wie folgt hinzu:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

### Erteilen Sie Ihrem Pushzertifikat den Zugriff auf Mobile Engagement.

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden darf, müssen Sie den Zugriff auf Ihr Zertifikat gestatten. Dies wird durch Konfigurieren und Eingeben Ihres Zertifikats im Mobile Engagement-Portal erreicht. Stellen Sie sicher, dass Sie Ihr P12-Zertifikat erhalten, wie in der Apple-Dokumentation beschrieben.

1. Navigieren zum Mobile Engagement-Portal

	Stellen Sie über das Azure-Portal sicher, dass Sie sich in der App befinden, die wir für dieses Projekt verwenden, und klicken Sie dann am unteren Rand auf die Schaltfläche zum "Einstellen":

	![][26]

2. Sie gelangen jetzt auf die Einstellungsseite des Mobile Engagement-Portals. Klicken Sie hier auf den Abschnitt "Systemeigener Push", um Ihr P12-Zertifikat einzugeben:

	![][27]

3. Wählen Sie Ihr P12-Zertifikat aus, laden Sie es hoch, und geben Sie anschließend Ihr Kennwort ein:

	![][28]

4. Fügen Sie jetzt Ihr Bereitstellungsprofil hinzu, und erstellen Sie Ihre App für ein Zielgerät.

Sie sind fertig und wir werden jetzt überprüfen, ob Sie diese einfache Integration ordnungsgemäß durchgeführt haben.

## <a id="send"></a>Senden einer Benachrichtigung an Ihre App

Wir erstellen jetzt eine einfache Pushbenachrichtigungskampagne, die eine Pushbenachrichtigung an die App sendet.

1. Navigieren Sie im Mobile Engagement-Portal zur Registerkarte "REACH".

2. Klicken Sie auf "Neue Ankündigung", um Ihre Pushkampagne zu erstellen.
	
	![][35]

3. Richten Sie das erste Feld der Kampagne ein:

	![][36]

	1. Benennen Sie Ihre Kampagne nach Belieben.
	2. Wählen Sie für "Übermittlungszeit" die Option "Nur außerhalb der App" aus: dies ist der einfache Apple-Pushbenachrichtigungstyp, der Text unterstützt.
	3. Geben Sie in den Benachrichtigungstext zunächst den Titel ein, der bei der Pushübertragung als erste Zeile angezeigt wird.
	4. Geben Sie dann Ihre Nachricht ein, die die zweite Zeile darstellt.

4. Navigieren Sie nach unten, und wählen Sie im Inhaltsbereich "Nur Benachrichtigung" aus.

	![][37]

5. Sie haben somit die Einrichtung einer möglichst einfachen Kampagne abgeschlossen. Navigieren Sie jetzt erneut nach unten, und erstellen Sie die Kampagne, um sie zu speichern.
![][38]

6. Als letzten Schritt aktivieren Sie die Kampagne.
![][39]

7. Jetzt sollte eine Pushbenachrichtigung auf dem Gerät angezeigt werden.

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Dokumentation zum Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9874682
[Azure-Verwaltungsportal]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
