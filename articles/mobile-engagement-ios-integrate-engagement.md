<properties 
	pageTitle="Integration des Azure Mobile Engagement iOS SDKs" 
	description="Neueste Updates und Verfahren für das iOS SDK für Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS" class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>


# Integrieren von Mobile Engagement unter iOS

> [AZURE.IMPORTANT] Das Engagement SDK erfordert iOS4+: Das Bereitstellungsziel Ihre Anwendung muss mindestens über iOS 4 verfügen.

In diesem Verfahren wird die einfachste Art der Aktivierung der Analyse- und Überwachungsfunktionen von Mobile Engagement in Ihrer iOS-Anwendung beschrieben.

Mithilfe der folgenden Schritte kann der Protokollbericht aktiviert werden, der zum Berechnen aller Statistiken zu Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Informationen erforderlich ist. Der zum Berechnen anderer Statistiken zu Ereignissen, Fehlern und Aufträgen erforderliche Protokollbericht muss mithilfe der Engagement-API (siehe "ios-sdk-engagement-advanced") manuell ausgeführt werden, da diese Statistiken anwendungsabhängig sind.

## Einbetten des Engagement-SDKs in Ihr iOS-Projekt

Fügen Sie das Engagement-SDK zum iOS-Projekt hinzu. Klicken Sie in Xcode 4 mit der rechten Maustaste auf das Projekt, und wählen Sie **"Add files to ..."** und anschließend den Ordner `EngagementSDK` aus.

Engagement erfordert, dass zusätzliche Frameworks funktionieren: Öffnen Sie im Projektexplorer Ihren Projektbereich, und wählen Sie das richtige Ziel aus. Öffnen Sie dann die Registerkarte **"Build phases"**. Fügen Sie anschließend im Menü **"Link Binary With Libraries"** die folgenden Frameworks hinzu:

> -   `AdSupport.framework` - Legen Sie den Link als  `Optional` fest.
> -   `SystemConfiguration.framework`
> -   `CoreTelephony.framework`
> -   `CFNetwork.framework`
> -   `CoreLocation.framework`
> -   `libxml2.dylib`

> [AZURE.NOTE] Das AdSupport-Framework kann entfernt werden. Dieses Framework ist für Engagement zum Erfassen der IDFA erforderlich. Die IDFA-Erfassung kann jedoch deaktiviert werden ("\<ios-sdk-engagement-idfa\>"), um der neuen Apple-Richtlinie zu dieser ID zu entsprechen.

## Initialisieren des Engagement-SDKs

Sie müssen Ihre Anwendungsstellvertretung ändern:

-   Importieren Sie am Anfang der Implementierungsdatei den Engagement-Agent.

			[...]
			#import "EngagementAgent.h"

-   Initialisieren Sie Engagement innerhalb der Methode "**applicationDidFinishLaunching:**" oder "**application:didFinishLaunchingWithOptions:**":

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
			  [...]
			}

## Einfache Berichterstellung

### Empfohlene Methode: überladen Sie Ihre `UIViewController`-Klassen.

Um den Bericht für alle Protokolle zu aktivieren, die von Engagement zum Berechnen von Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Statistiken erforderlich sind, müssen Sie einfach dafür sorgen, dass all Ihre `UIViewController`-Unterklassen von den entsprechenden `EngagementViewController`-Klassen abgeleitet werden (gleiche Regel für `UITableViewController` -\> `EngagementTableViewController`).

**Ohne Engagement:**

			#import <UIKit/UIKit.h>
			
			@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Mit Engagement:**

			#import <UIKit/UIKit.h>
			#import "EngagementViewController.h"
			
			@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### Alternative Methode:  `startActivity()` manuell aufrufen

Wenn Sie die `UIViewController`-Klassen nicht überladen können oder möchten, können Sie die Aktivitäten stattdessen durch direktes Aufrufen der Methoden von `EngagementAgent` starten.

> [AZURE.IMPORTANT] Das iOS SDK ruft die `endActivity()`-Methode automatisch auf, wenn die Anwendung geschlossen wird. Daher wird *DRINGEND* empfohlen, die `startActivity`-Methode aufzurufen, sobald sich die Aktivität des Benutzers ändert. Zudem darf *NIE* die `endActivity`-Methode aufgerufen werden, da durch das Aufrufen dieser Methode das Beenden der aktuellen Sitzung erzwungen wird.

## Speicherortberichte

Die Apple-Nutzungsbedingungen gestatten es nicht, dass Anwendungen die Standortnachverfolgung nur für statistische Zwecke verwenden. Daher wird empfohlen, die Standortberichte nur zu aktivieren, wenn Ihre Anwendung die Standortnachverfolgung auch aus einem anderen Grund verwendet.

Ab iOS 8 müssen Sie eine Beschreibung für die Verwendung der Standortdienste durch Ihre App bereitstellen, indem Sie für den Schlüssel [NSLocationWhenInUseUsageDescription] oder [NSLocationAlwaysUsageDescription] in der Datei "Info.plist" Ihrer App eine Zeichenfolge festlegen. Wenn Sie mit Engagement im Hintergrund einen Standortbericht erstellen möchten, fügen Sie den Schlüssel "NSLocationAlwaysUsageDescription" hinzu. In allen anderen Fällen fügen Sie den Schlüssel "NSLocationWhenInUseUsageDescription" hinzu.

### Verzögerte Bereichsspeicherortberichte

Die verzögerte Berichterstellung für Bereichsspeicherorte ermöglicht es Ihnen, das Land, die Region und den Ort zu melden, die Geräten zugeordnet sind. Diese Art der Berichterstellung für Speicherorte verwendet ausschließlich Netzwerkspeicherorte (auf Basis von Zell-ID oder WLAN). Der Gerätebereich wird höchstens einmal pro Sitzung gemeldet. Das GPS wird niemals verwendet, daher hat diese Art von Standortbericht sehr geringe (oder fast keine) Auswirkungen auf den Akku.

Die gemeldeten Bereiche werden dazu verwendet, um geografische Statistiken zu Benutzern, Sitzungen, Ereignissen und Fehlern zu berechnen. Sie können auch als Kriterium für Reach-Kampagnen verwendet werden. Der für ein Gerät zuletzt gemeldete bekannte Bereich kann mithilfe der [Geräte-API] abgerufen werden.

Fügen Sie die folgende Zeile hinter der Initialisierung des Engagement-Agent hinzu, um verzögerte Standortberichte zu aktivieren:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
			  [...]
			}

### Echtzeit-Berichterstellung für Speicherorte

Mithilfe der Echtzeit-Berichterstellung für Speicherorte können der Längen- und Breitengrad gemeldet werden, die Geräten zugeordnet sind. Diese Art der Berichterstellung für Speicherorte verwendet ausschließlich Netzwerkspeicherorte (auf Basis von Zell-ID oder WLAN), und die Berichterstellung ist nur aktiv, wenn die Anwendung im Vordergrund ausgeführt wird (d. h. während einer Sitzung).

Echtzeit-Speicherorte werden *NICHT* zum Berechnen von Statistiken verwendet. Ihr einziger Zweck ist es, die Verwendung des
Echtzeit-Geofence-Kriteriums \<Reach-Audience-geofencing\> in Reach-Kampagnen zu ermöglichen.

Fügen Sie die folgende Zeile hinter der Initialisierung des Engagement-Agent hinzu, um die Echtzeit-Berichterstellung für Speicherorte zu aktivieren:

			[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### GPS-basierte Berichterstellung

Die Echtzeit-Berichterstellung für Speicherorte verwendet standardmäßig nur netzwerkbasierte Speicherorte. Fügen Sie Folgendes hinzu, um die Verwendung von GPS-basierten Speicherorten (die viel genauer sind) zu aktivieren:

			[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### Berichterstellung im Hintergrund

Die Echtzeit-Berichterstellung für Speicherorte ist standardmäßig nur aktiv, wenn die Anwendung im Vordergrund ausgeführt wird (d. h. während einer Sitzung). Fügen Sie Folgendes hinzu, um die Berichterstellung auch im Hintergrund zu aktivieren:

			[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Wenn die Anwendung im Hintergrund ausgeführt wird, werden nur netzwerkbasierte Speicherorte gemeldet, auch wenn Sie das GPS aktiviert haben.

Durch die Implementierung dieser Funktion wird [startMonitoringSignificantLocationChanges]aufgerufen, wenn die Anwendung in den Hintergrund wechselt. Beachten Sie, dass Ihre Anwendung im Hintergrund automatisch neu gestartet wird, wenn ein neues Standortereignis eintrifft.

## Erweiterte Berichterstellung

Wenn Sie anwendungsspezifische Ereignisse, Fehler und Aufträge optional melden möchten, müssen Sie die Engagement-API über die Methoden der `EngagementAgent`-Klasse verwenden. Ein Objekt dieser Klasse kann durch Aufrufen der statischen`[EngagementAgent shared]`-Methode abgerufen werden.

Die Engagement-API gestattet die Verwendung aller erweiterten Engagement-Funktionen. Sie wird im Abschnitt "Verwenden der
Engagement-API unter Android" (sowie in der technischen Dokumentation der `EngagementAgent`-Klasse) ausführlich beschrieben.

## Deaktivieren der IDFA-Erfassung

Engagement verwendet [IDFA] standardmäßig zum eindeutigen Identifizieren von Benutzern. Wenn Sie nicht an anderer Stelle in der App Werbung verwenden, werden Sie möglicherweise vom App Store-Überprüfungsprozess abgelehnt. Die IDFA-Erfassung kann durch Hinzufügen eines Präprozessormakros `ENGAGEMENT_DISABLE_IDFA` zur PCH-Datei deaktiviert werden (oder in den `Buildeinstellungen` Ihrer Anwendung). Dadurch wird sichergestellt, dass im Anwendungsbuild keine Verweise auf `ASIdentifierManager`, `advertisingIdentifier` oder `isAdvertisingTrackingEnabled` enthalten sind.

Integration in der Datei **prefix.pch**:

			#define ENGAGEMENT_DISABLE_IDFA
			...

Sie können überprüfen, ob die IDFA-Erfassung in Ihrer Anwendung ordnungsgemäß deaktiviert ist, indem Sie die Engagement-Testprotokolle prüfen. Weitere Informationen finden Sie in der Dokumentation zum Integrationstest ("\<ios-sdk-engagement-test-idfa\>").

## Deaktivieren der Protokollberichterstellung

### Verwenden eines Methodenaufrufs

Sie können Folgendes aufrufen, wenn von Engagement keine Protokolle mehr gesendet werden sollen:

			[[EngagementAgent shared] setEnabled:NO];

Dieser Aufruf ist persistent: er verwendet `NSUserDefaults` zum Speichern der Informationen.

Sie können die Protokollberichterstellung erneut aktivieren, indem Sie dieselbe Funktion mit `YES` aufrufen.

### Integration im Einstellungsbündel

Anstatt diese Funktion aufzurufen, können Sie diese Einstellung auch direkt in der vorhandenen Datei `Settings.bundle` integrieren. Die Zeichenfolge `engagement_agent_enabled` muss als Einstellungsbezeichner verwendet und einem Umschalter (`PSToggleSwitchSpecifier`) zugeordnet werden.

Das folgende Beispiel von `Settings.bundle` veranschaulicht die Implementierung:

			<dict>
			    <key>PreferenceSpecifiers</key>
			    <array>
			        <dict>
			            <key>DefaultValue</key>
			            <true/>
			            <key>Key</key>
			            <string>engagement_agent_enabled</string>
			            <key>Title</key>
			            <string>Log reporting enabled</string>
			            <key>Type</key>
			            <string>PSToggleSwitchSpecifier</string>
			        </dict>
			    </array>
			    <key>StringsTable</key>
			    <string>Root</string>
			</dict>

<!-- URLs. -->
[Geräte-API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!--HONumber=47-->
