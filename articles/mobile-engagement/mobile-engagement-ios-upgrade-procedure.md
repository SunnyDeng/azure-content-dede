<properties
	pageTitle="Upgradeverfahren für das iOS-SDK für Azure Mobile Engagement"
	description="Neueste Updates und Verfahren für das iOS-SDK für Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr" />

#Upgrade-Verfahren

Wenn Sie bereits eine ältere Version von Engagement in Ihrer Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Für jede neue SDK-Version müssen Sie zunächst die Ordner "EngagementSDK" und "EngagementReach" ersetzen (entfernen und in Xcode neu importieren).

##Von 2.0.0 zu 3.0.0
Unterstützung für iOS 4.X eingestellt. Ab dieser Version muss das Bereitstellungsziel Ihrer Anwendung mindestens über iOS 6 verfügen.

Wenn Sie Reach in Ihrer Anwendung verwenden, müssen Sie den `remote-notification`-Wert zum `UIBackgroundModes`-Array in Ihrer Datei "Info.plist" hinzufügen, um Remotebenachrichtigungen zu erhalten.

Die Methode `application:didReceiveRemoteNotification:` muss in Ihrem Anwendungsdelegaten durch `application:didReceiveRemoteNotification:fetchCompletionHandler:` ausgetauscht werden.

"AEPushDelegate.h" ist eine veraltete Schnittstelle, und Sie müssen alle Verweise darauf entfernen. Dies umfasst das Entfernen von `[[EngagementAgent shared] setPushDelegate:self]` und der Delegatmethoden aus Ihrem Anwendungsdelegaten:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##Version 1.16.0 bis 2.0.0
Im Folgenden wird beschrieben, wie Sie die Migration einer SDK-Integration vom Capptain-Dienst, der von Capptain-SAS angeboten wird, in eine App von Azure Mobile Engagement durchführen. Wenn Sie von einer früheren Version migrieren, sehen Sie auf der Capptain-Website nach, wie eine Migration auf Version 1.16 durchgeführt wird. Führen Sie anschließend das folgende Verfahren aus.

>[Azure.IMPORTANT]Capptain und Mobile Engagement sind nicht dieselben Dienste, und die unten beschriebene Vorgehensweise hebt nur hervor, wie die Migration der Clientapp durchzuführen ist. Bei der Migration des SDK in die App werden Ihre Daten NICHT von den Capptain-Servern zu den Mobile Engagement-Servern migriert

### Agent

Die Methode `registerApp:` wurde durch die neue Methode `init:` ersetzt. Ihr Anwendungsdelegat muss entsprechend aktualisiert werden und eine Verbindungszeichenfolge verwenden:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Die SmartAd-Verfolgung wurde aus dem SDK entfernt, und Sie müssen lediglich alle Instanzen der Klasse `AETrackModule` entfernen.

### Änderungen von Klassennamen

Im Rahmen der Umfirmierung müssen verschiedene Klassen-/Dateinamen geändert werden.

Alle Klassen mit dem Präfix "CP" erhalten stattdessen das Präfix "AE".

Beispiel:

-   `CPModule.h` wird umbenannt in `AEModule.h`.

Alle Klassen mit dem Präfix "Capptain" erhalten stattdessen das Präfix "Engagement".

Beispiele:

-   Die Klasse `CapptainAgent` wird umbenannt in `EngagementAgent`.
-   Die Klasse `CapptainTableViewController` wird umbenannt in `EngagementTableViewController`.
-   Die Klasse `CapptainUtils` wird umbenannt in `EngagementUtils`.
-   Die Klasse `CapptainViewController` wird umbenannt in `EngagementViewController`.

<!---HONumber=Oct15_HO3-->