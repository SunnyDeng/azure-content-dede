<properties 
	pageTitle="Upgradeverfahren für das iOS-SDK für Azure Mobile Engagement" 
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

# Upgradeverfahren

Wenn Sie bereits eine ältere Engagement-Version in Ihre Anwendung integriert haben, müssen Sie beim SDK-Upgrade die folgenden Punkte berücksichtigen.

Für jede neue SDK-Version müssen Sie zunächst die Ordner "EngagementSDK" und "EngagementReach" ersetzen (entfernen und in Xcode neu importieren).

## Version 1.16.0 bis 2.0.0
Nachfolgend wird beschrieben, wie Sie eine SDK-Integration aus dem Capptain-Dienst von Capptain SAS in eine Azure Mobile Engagement-App migrieren. 

>[Azure.IMPORTANT] Capptain und Mobile Engagement sind nicht dieselben Dienste, und das nachstehende Verfahren zeigt nur, wie die Client-App migriert wird. Eine SDK-Migration in der App führt NICHT zu einer Migration Ihrer Daten von den Capptain-Servern auf die Mobile Engagement-Server.

Wenn Sie von einer früheren Version migrieren, sehen Sie auf der Capptain-Website nach, wie eine Migration auf Version 1.16 durchgeführt wird. Führen Sie anschließend das folgende Verfahren aus.

### Agent

Die Methode `registerApp:` wurde durch die neue Methode `init` ersetzt. Ihr Anwendungsdelegat muss entsprechend aktualisiert werden und eine Verbindungszeichenfolge verwenden:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Die SmartAd-Verfolgung wurde aus dem SDK entfernt, und Sie müssen lediglich alle Instanzen der  `AETrackModule`-Klasse entfernen.

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

<!--HONumber=47-->
