<properties 
	pageTitle="Azure Mobile Engagement iOS-SDK für die Reach-Integration" 
	description="Neueste Updates und Verfahren für das iOS-SDK für Azure Mobile Engagement"
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

# So integrieren Sie Engagement Reach auf iOS

> [AZURE.IMPORTANT] Bevor Sie die in diesem Leitfaden beschriebenen Schritte ausführen, müssen Sie die im Dokument "So integrieren Sie Engagement auf iOS" beschriebenen Verfahren ausführen.

## Schritte für die Integration

### Einbetten des Engagement Reach-SDK in Ihr iOS-Projekt

-   Fügen Sie das Reach-SDK in Ihr Xcode-Projekt ein. Wechseln Sie in Xcode zur **Project > Add to project**, und wählen Sie den Ordner `EngagementReach`.

### Ändern des Anwendungsdelegaten

-   Importieren Sie am Beginn Ihrer Implementierungsdatei das Engagement Reach-Modul:

			[...]
			#import "AEReachModule.h"

-   Erstellen Sie in der Methode `applicationDidFinishLaunching:` oder `application:didFinishLaunchingWithOptions:` ein Reach-Modul, und übergeben Sie es an Ihre vorhandene Zeile für die Engagement-Initialisierung:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			  [...]
			
			  return YES;
			}

-   Legen Sie die Zeichenfolge **'icon.png'** auf den Namen der Bilddatei fest, die Sie als Benachrichtigungssymbol verwenden möchten.
-   Wenn Sie die Option  *Badgewert aktualisieren* in Reach-Kampagnen verwenden oder systemeigene Pushkampagnen - </SaaS/Reach-API/Kampagnenformat/Systemeigener Push> - einsetzen möchten, müssen Sie dem Reach-Modul die Verwaltung des Badgesymbols gestatten (Anwendungsbadge wird automatisch gelöscht und der von Engagement gespeicherte Wert wird bei jedem Start bzw. bei jeder Aktivierung der Anwendung zurückgesetzt). Dies geschieht durch Hinzufügen der folgenden Codezeile nach der Initialisierung des Reach-Moduls:

			[reach setAutoBadgeEnabled:YES];

-   Wenn Sie Pushvorgänge für Reach-Daten verarbeiten möchten, muss der Anwendungsdelegat mit dem `AEReachDataPushDelegate`-Protokoll konform sein. Fügen Sie nach der Initialisierung des Reach-Moduls die folgende Zeile ein:

			[reach setDataPushDelegate:self];

-   Anschließend können Sie die Methoden `onDataPushStringReceived:` und `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` im Anwendungsdelegaten implementieren:

			-(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
			{
			   NSLog(@"String data push message with category <%@> received: %@", category, body);
			   return YES;
			}
			
			-(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
			{
			   NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
			   // Do something useful with decodedBody like updating an image view
			   return YES;
			}

### Kategorie

Der category-Parameter ist optional, wenn Sie eine Datenpushkampagne erstellen und eine Filterung von Datenpushvorgängen ermöglichen. Dies ist nützlich, wenn Sie unterschiedliche Arten von `Base64`-Daten pushen und vor der Analyse deren Typ ermitteln möchten.

**Ihre Anwendung ist nun bereit, Reach-Inhalte zu empfangen und anzuzeigen!**

## Jederzeitiges Empfangen von Ankündigungen und Umfragen

Engagement kann über APNS (Apple Push Notification Service) jederzeit Reach-Benachrichtigungen an Ihre Endbenutzer senden.

Zur Aktivierung dieser Funktionalität müssen Sie Ihre Anwendung auf Apple-Pushbenachrichtigungen vorbereiten und Ihren Anwendungsdelegaten ändern.

### Vorbereiten Ihrer Anwendung auf Apple-Pushbenachrichtigungen

Befolgen Sie die Anleitung: Vorbereiten Ihrer Anwendung auf Apple-Pushbenachrichtigungen

### Hinzufügen des erforderlichen Clientcodes

*Jetzt sollte Ihre Anwendung über ein registriertes Apple-Pushzertifikat im Engagement-Front-End verfügen.*

Sofern nicht bereits geschehen, müssen Sie Ihre Anwendung für den Empfang von Pushbenachrichtigungen registrieren. Fügen Sie die folgende Zeile beim Start der Anwendung ein (typischerweise in `application:didFinishLaunchingWithOptions:`):

			if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			  [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			  [application registerForRemoteNotifications];
			}
			else {
			
			  [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
			}

Anschließend müssen Sie für Engagement das von Apple-Servern zurückgegebene Gerätetoken bereitstellen. Dies wird mit der Methode `application:didRegisterForRemoteNotificationsWithDeviceToken:` in Ihrem Anwendungsdelegaten erreicht:

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
			{
			    [[EngagementAgent shared] registerDeviceToken:deviceToken];
			}

Anschließend muss das Engagement-SDK darüber informiert werden, wenn Ihre Anwendung eine Remotebenachrichtigung erhält. Hierzu rufen Sie die Methode `applicationDidReceiveRemoteNotification:` in Ihrem Anwendungsdelegaten auf:

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
			{
			    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
			}

### Letzter Schritt

Nachdem Sie all diese Schritte durchgeführt haben, kann Ihre Anwendung jederzeit Engagement-Pushnachrichten empfangen.

Möglicherweise möchten Sie dem Endbenutzer jedoch Daten anzeigen, wenn Ihre Anwendung als Reaktion auf den Empfang einer Engagement-Pushnachricht gestartet wird. Hierzu werden im  `AEPushDelegate`-Protokoll drei weitere Delegate-Methoden bereitgestellt:

			-(void)willRetrieveLaunchMessage;
			-(void)didFailToRetrieveLaunchMessage;
			-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

`willRetrieveLaunchMessage`

> Wenn der Benutzer die Anwendung als Reaktion auf das Eintreffen einer Pushnachricht gestartet hat, wird diese Methode kurz nach dem Anwendungsstart aufgerufen, um anzuzeigen, dass die vollständige Nachricht abgerufen wurde. Dies ist eine gute Gelegenheit, dem Endbenutzer eine Meldung zum Ladevorgang anzuzeigen.

`didFailToRetrieveLaunchMessage`

> Diese Methode wird aufgerufen, wenn beim Nachrichtenabruf ein Fehler aufgetreten ist. An diesem Punkt sollte der Endbenutzer in einer Meldung darüber informiert werden, dass ein Fehler aufgetreten ist.

`didReceiveLaunchMessage:`

> Diese Methode wird aufgerufen, wenn die Pushnachricht zum Start der Anwendung empfangen wurde. Nutzen Sie diese Gelegenheit, um Meldungen zum Ladevorgang auszublenden und dem Endbenutzer geeignete Inhalte anzuzeigen.

Sie benötigen einen Pushdelegaten, nachdem Engagement initialisiert wurde:

			[[EngagementAgent shared] setPushDelegate:self];

### Vollständiges Beispiel

Hier sehen Sie ein vollständiges Beispiel für die Integration:

			#pragma mark -
			#pragma mark Application lifecycle
			
			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  /* Reach module */
			  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			  [reach setAutoBadgeEnabled:YES];
			
			  /* Engagement initialization */
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			  [[EngagementAgent shared] setPushDelegate:self];
			
			  /* Views */
			  [window addSubview:[tabBarController view]];
			  [window makeKeyAndVisible];
			
			  [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
			  return YES;
			}
			
			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
			{
			  [[EngagementAgent shared] registerDeviceToken:deviceToken];
			}
			
			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
			{
			  [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
			}
			
			
			#pragma mark -
			#pragma mark Engagement push delegate
			
			-(void)willRetrieveLaunchMessage
			{
			  [UIApplication sharedApplication].networkActivityIndicatorVisible = YES;
			}
			
			-(void)didReceiveLaunchMessage:(AEPushMessage *)launchMessage
			{
			  /* Hide network activity indicator */
			  [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;
			}
			
			-(void)didFailToRetrieveLaunchMessage
			{
			  /* Hide network activity indicator */
			  [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;
			
			  /* Display an error alert */
			  UIAlertView* alert = [[UIAlertView alloc] initWithTitle:NSLocalizedString(@"Sorry", nil)
			                         message:NSLocalizedString(@"Could not retrieve message.", nil)
			                         delegate:nil
			                         cancelButtonTitle:NSLocalizedString(@"Close", nil)
			                         otherButtonTitles:nil];
			  [alert show];
			  [alert release];
			}

## Anpassen von Kampagnen

### Benachrichtigungen

Es gibt zwei Arten von Benachrichtigungen: System- und anwendungsinterne Benachrichtigungen.

Systembenachrichtigungen werden von iOS verarbeitet und können nicht angepasst werden.

Anwendungsinterne Benachrichtigungen bestehen aus einer Ansicht, die dynamisch in das aktuelle Anwendungsfenster eingefügt wird. Dies wird als Benachrichtigungsoverlay bezeichnet. Benachrichtigungsoverlays eignen sich besonders für eine schnelle Integration, da für sie keine Änderung von Ansichten in Ihrer Anwendung erforderlich ist.

#### Layout

Um das Layout Ihrer anwendungsinternen Benachrichtigungen zu ändern, können Sie einfach die Datei `AENotificationView.xib` an Ihre Anforderungen anpassen. Sie müssen hierbei jedoch darauf achten, dass die Tagwerte und -typen der vorhandenen Unteransichten beibehalten werden.

Standardmäßig werden anwendungsinterne Benachrichtigungen am unteren Bildschirmrand angezeigt. Wenn Sie eine Anzeige der Benachrichtigungen am oberen Bildschirmrand bevorzugen, bearbeiten Sie die bereitgestellte Datei `AENotificationView.xib`, und ändern Sie die Eigenschaft `AutoSizing` der Hauptansicht, sodass die Benachrichtigungen weiterhin im obereen Bereich der übergeordneten Ansicht angezeigt werden können.

#### Kategorien

Wenn Sie das bereitgestellte Layout ändern, ändern Sie das Aussehen aller Ihrer Benachrichtigungen. Mithilfe von Kategorien können Sie verschiedene zielgerichtete Layouts (Verhaltensweisen) für Benachrichtigungen definieren. Eine Kategorie kann beim Erstellen einer Reach-Kampagne angegeben werden. Bedenken Sie, dass Sie mithilfe von Kategorien auch Ankündigungen und Umfragen anpassen können (dies wird weiter unten in diesem Dokument beschrieben).

Um einen Kategorie-Handler für Ihre Benachrichtigungen zu registrieren, müssen Sie nach der Initialisierung des Reach-Moduls einen Aufruf hinzufügen.

			AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[reach registerNotifier:myNotifier forCategory:@"my_category"];
			...

Bei `myNotifier` muss es sich um eine Instanz eines Objekts handeln, das mit dem `AENotifier`-Protokoll konform ist.

Sie können die Protokollmethoden selbst implementieren oder erneut die vorhandene Klasse `AEDefaultNotifier` implementieren, mit der bereits die meisten Aufgaben ausgeführt werden.

Wenn Sie beispielsweise die Benachrichtigungsansicht für eine spezifische Kategorie neu definieren möchten, können Sie diesem Beispiel folgen:

			#import "AEDefaultNotifier.h"
			#import "AENotificationView.h"
			@interface MyNotifier : AEDefaultNotifier
			@end
			
			@implementation MyNotifier
			
			-(NSString*)nibNameForCategory:(NSString*)category
			{
			  return "MyNotificationView";
			}
			
			@end

Dieses einfache Beispiel einer Kategorie setzt voraus, dass Sie in Ihrem Hauptanwendungspaket über eine Datei namens `MyNotificationView.xib` verfügen. Wenn die Methode keine entsprechende `.xib`-Datei findet, wird die Benachrichtigung nicht angezeigt und Engagement gibt eine entsprechende Meldung in der Konsole aus.

Die bereitgestellte NIB-Datei sollte den folgenden Regeln entsprechen:

-   Sie sollte nur eine Ansicht enthalten.
-   Unteransichten sollten dieselben Typen aufweisen wie diejenigen in der bereitgestellten NIB-Datei `AENotificationView.xib`
-   Unteransichten sollten dieselben Tags aufweisen wie diejenigen in der bereitgestellten NIB-Datei `AENotificationView.xib`

> [AZURE.TIP] Kopieren Sie einfach die bereitgestellte NIB-Datei namens `AENotificationView.xib`, und beginnen Sie Ihre Arbeit mit dieser Datei. Seien Sie jedoch vorsichtig, die Ansicht in dieser NIB-Datei ist mit der Klasse `AENotificationView` verknüpft. Diese Klasse definiert die Methode `layoutSubViews` neu, um die zugehörigen Unteransichten gemäß Kontext zu verschieben und in der Größe zu ändern. Sie können die Klasse durch `UIView` oder eine benutzerdefinierte Ansichtenklasse ersetzen.

Wenn Sie eine umfangreichere Anpassung der Benachrichtigungen benötigen (wenn Sie beispielsweise Ihre Ansicht direkt aus dem Code laden möchten), sollten Sie einen Blick auf den bereitgestellten Quellcode und die Klassendokumentation von `Protocol ReferencesDefaultNotifier` und `AENotifier` werfen.

Beachten Sie, dass Sie dieselben Notifier für mehrere Kategorien verwenden können.

Sie können auch den standardmäßigen Notifier folgendermaßen neu definieren:

			AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### Benachrichtigungsverarbeitung

Bei Verwendung der Standardkategorie werden für das `AEReachContent`-Objekt einige Lebenszyklusmethoden aufgerufen, um Statistiken bereitzustellen und den Kampagnenstatus zu aktualisieren:

-   Bei Anzeige der Benachrichtigung in der Anwendung wird von `AEReachModule` die `displayNotification`-Methode aufgerufen (diese liefert Statistiken), wenn `handleNotification:` den Wert `YES` zurückgibt.
-   Wird die Benachrichtigung geschlossen, wird die Methode `exitNotification` aufgerufen, es werden Statistiken bereitgestellt, und es können weitere Kampagnen verarbeitet werden.
-   Wird auf die Benachrichtigung geklickt, wird `actionNotification` aufgerufen, es werden Statistiken bereitgestellt, und die verknüpfte Aktion wird ausgeführt.

Wenn Ihre Implementierung von `AENotifier` das Standardverhalten umgeht, müssen Sie diese Lebenszyklusmethoden selbst aufrufen. Das folgende Beispiel zeigt einige Fälle, in denen das Standardverhalten umgangen wird:

-   Es erfolgt keine Erweiterung von `AEDefaultNotifier`, d. h. Sie haben die Kategorieverarbeitung von Grund auf implementiert.
-   Sie haben `prepareNotificationView:forContent:` außer Kraft gesetzt und sichergestellt, dass mindestens`onNotificationActioned` oder `onNotificationExited` einem Ihrer Benutzeroberflächen-Steuerelemente zugeordnet ist.

> [AZURE.WARNING] Wenn `handleNotification:` eine Ausnahme generiert, wird der Inhalt gelöscht, und es erfolgt ein Aufruf von `drop`. Dies wird in der Statistik berichtet, und es können weitere Kampagnen verarbeitet werden.

#### Einschließen von Benachrichtigungen als Bestandteil einer vorhandenen Ansicht

Overlays eignen sich hervorragend für eine schnelle Integration, sind aber gelegentlich nicht praktisch oder haben unerwünschte Nebeneffekte.

Wenn Sie mit dem Overlaysystem in einigen Ihrer Ansichten nicht zufrieden sind, können Sie es für diese Ansichten anpassen.

Sie können unser Benachrichtigungslayout in Ihre vorhandenen Ansichten einfügen. Die Implementierung kann auf zwei Arten erfolgen:

1.  Fügen Sie die Benachrichtigungsansicht mit dem Interface Builder hinzu.

	-   Öffnen Sie *Interface Builder*.
	-   Platzieren Sie ein  `UIView`-Element der Größe 320 x 60 (oder 768 x 60 für das iPad) an der Stelle, an der die Benachrichtigung angezeigt werden soll.
	-   Legen Sie den Tagwert für diese Ansicht auf diesen Wert fest: **36822491**

2.  Fügen Sie die Benachrichtigungsansicht programmatisch hinzu. Fügen Sie einfach nach dem Initialisieren Ihrer Ansicht den folgenden Code ein:

			UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
			notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
			[self.view addSubview:notificationView];

Das Makro `NOTIFICATION_AREA_VIEW_TAG` befindet sich in `AEDefaultNotifier.h`.

> [AZURE.NOTE] Der standardmäßige Notifier erkennt automatisch, dass das Benachrichtigungslayout in dieser Ansicht enthalten ist und fügt kein Overlay hinzu.

### Ankündigungen und Umfragen

#### Layouts

Sie können die Dateien `AEDefaultAnnouncementView.xib` und `AEDefaultPollView.xib` ändern, solange Sie die Tagwerte und -typen der vorhandenen Unteransichten beibehalten.

#### Kategorien

##### Alternative Layouts

Wie Benachrichtigungen können die Kampagnenkategorien dazu verwendet werden, alternative Layouts für Ankündigungen und Umfragen bereitzustellen.

Um eine Kategorie für eine Ankündigung zu erstellen, müssen Sie **AEAnnouncementViewController** erweitern und nach der Initialisierung des Reach-Moduls registrieren:

			AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Immer dann, wenn ein Benutzer auf eine Benachrichtigung für eine Ankündigung mit der Kategorie "my_category" klickt, wird Ihr registrierter Ansichtencontroller (in diesem Fall `MyCustomAnnouncementViewController`) durch einen Aufruf der Methode `initWithAnnouncement:` initialisiert, und die Ansicht wird dem aktuellen Anwendungsfenster hinzugefügt.

In Ihrer Implementierung der Klasse `AEAnnouncementViewController` müssen Sie die Eigenschaft `announcement` lesen, um Ihre Unteransichten zu initialisieren. Siehe hierzu das nachfolgende Beispiel, in dem mithilfe der Eigenschaften `title` und `body` der Klasse `AEReachAnnouncement` zwei Labels initialisiert werden:

			-(void)loadView
			{
			    [super loadView];
			
			    UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
			    titleLabel.font = [UIFont systemFontOfSize:32.0];
			    titleLabel.text = self.announcement.title;
			
			    UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
			    bodyLabel.font = [UIFont systemFontOfSize:24.0];
			    bodyLabel.text = self.announcement.body;
			
			    [self.view addSubview:titleLabel];
			    [self.view addSubview:bodyLabel];
			}

Wenn Sie Ihre Ansichten nicht selbst laden, sondern lediglich das Ansichtenlayout der Standardankündigung wiederverwenden möchten, können Sie einfach über Ihren benutzerdefinierten Ansichtencontroller die bereitgestellte Klasse `AEDefaultAnnouncementViewController` erweitern. In diesem Fall duplizieren Sie die NIB-Datei `AEDefaultAnnouncementView.xib`, und benennen Sie sie um, sodass Sie von Ihrem benutzerdefinierten Ansichtencontroller geladen werden kann (wenn Ihr Controller beispielsweise `CustomAnnouncementViewController` heißt, sollten Sie Ihrer NIB-Datei den Namen `CustomAnnouncementView.xib` geben).

Um die Standardkategorie für Ankündigungen zu ersetzen, registrieren Sie einfach Ihren benutzerdefinierten Ansichtencontroller für die in  `kAEReachDefaultCategory` definierte Kategorie:

			[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Umfragen können auf die gleiche Weise angepasst werden:

			AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Dieses Mal muss der bereitgestellte `MyCustomPollViewController` zur Erweiterung von `AEPollViewController` verwendet werden. Alternativ können Sie eine Erweiterung über den Standardcontroller durchführen: `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] Vergessen Sie nicht, entweder die Methode `action` (`submitAnswers:` für benutzerdefinierte Ansichtencontroller für Umfragen) oder die Methode  `exit` aufzurufen, bevor der Ansichtencontroller verworfen wird. Andernfalls werden keine Statistiken gesendet (z. B. Analysen der Kampagne) und - noch wichtiger - weitere Kampagnen erhalten erst eine Benachrichtigung, wenn der Anwendungsprozess neu gestartet wurde.

##### Beispiel für die Implementierung

In dieser Implementierung wird die benutzerdefinierte Ankündigungsansicht aus einer externen XIB-Datei geladen.

Wie bei der erweiterten Benachrichtigungsanpassung wird empfohlen, sich den Quellcode der Standardimplementierung anzusehen.

`CustomAnnouncementViewController.h`

			//Interface
			@interface CustomAnnouncementViewController : AEAnnouncementViewController {
			  UILabel* titleLabel;
			  UITextView* descTextView;
			  UIWebView* htmlWebView;
			  UIButton* okButton;
			  UIButton* cancelButton;
			}
			
			@property (nonatomic, retain) IBOutlet UILabel* titleLabel;
			@property (nonatomic, retain) IBOutlet UITextView* descTextView;
			@property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
			@property (nonatomic, retain) IBOutlet UIButton* okButton;
			@property (nonatomic, retain) IBOutlet UIButton* cancelButton;
			
			-(IBAction)okButtonClicked:(id)sender;
			-(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

			//Implementation
			@implementation CustomAnnouncementViewController
			@synthesize titleLabel;
			@synthesize descTextView;
			@synthesize htmlWebView;
			@synthesize okButton;
			@synthesize cancelButton;
			
			-(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
			{
			  self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
			  if (self != nil) {
			    self.announcement = anAnnouncement;
			  }
			  return self;
			}
			
			- (void) dealloc
			{
			  [titleLabel release];
			  [descTextView release];
			  [htmlWebView release];
			  [okButton release];
			  [cancelButton release];
			  [super dealloc];
			}
			
			- (void)viewDidLoad {
			  [super viewDidLoad];
			
			  /* Init announcement title */
			  titleLabel.text = self.announcement.title;
			
			  /* Init announcement body */
			  if(self.announcement.type == AEAnnouncementTypeHtml)
			  {
			    titleLabel.hidden = YES;
			    htmlWebView.hidden = NO;
			    [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
			  }
			  else
			  {
			    titleLabel.hidden = NO;
			    htmlWebView.hidden = YES;
			    descTextView.text = self.announcement.body;
			  }
			
			  /* Set action button label */
			  if([self.announcement.actionLabel length] > 0)
			    [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];
			
			  /* Set exit button label */
			  if([self.announcement.exitLabel length] > 0)
			    [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
			}
			
			#pragma mark Actions
			
			-(IBAction)okButtonClicked:(id)sender
			{
			    [self action];
			}
			
			-(IBAction)cancelButtonClicked:(id)sender
			{
			    [self exit];
			}
			
			@end

<!--HONumber=47-->
