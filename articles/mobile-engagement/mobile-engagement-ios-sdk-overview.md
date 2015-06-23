<properties 
	pageTitle="Übersicht über das iOS-SDK für Azure Mobile Engagement" 
	description="Neueste Updates und Verfahren für das iOS-SDK für Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="piyushjo" />

# iOS SDK für Azure Mobile Engagement

Beginnen Sie hier, um alle Details zur Integration von Azure Mobile Engagement in eine iOS-App zu erhalten. Wenn Sie es zunächst nur probieren möchten, stellen Sie sicher, dass Sie das [15-Minuten-Lernprogramm](mobile-engagement-ios-get-started.md) durchführen

Klicken Sie, um den [SDK-Inhalt](mobile-engagement-ios-sdk-content.md) anzuzeigen

## Integrationsverfahren
1. Beginnen Sie hier: [Integration von Mobile Engagement in Ihre iOS-App](mobile-engagement-ios-integrate-engagement.md)

2. Für Benachrichtigungen: [Integration von Reach (Benachrichtigungen) in Ihre iOS-App](mobile-engagement-ios-integrate-engagement-reach.md)

3. Tag-Plan-Implementierung: [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer iOS-App](mobile-engagement-ios-use-engagement-api.md)


## Versionshinweise

### 2.1.0 (24.04.2015)

-   Hinzufügen der Swift-Kompatibilität.
-   Wenn Sie auf eine Benachrichtigung klicken, wird die Aktions-URL nun direkt nach dem Öffnen der Anwendung ausgeführt.
-   Fehlende Headerdatei im SDK-Paket hinzugefügt.
-   Behebung eine Fehlers, wenn der Mobile Engagement-Absturzmelder deaktiviert wurde.

Eine frühere Version finden Sie unter [Vollständige Versionshinweise](mobile-engagement-ios-release-notes.md)

## Upgrade-Verfahren

Wenn Sie bereits eine ältere Version von Engagement in Ihrer Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Möglicherweise müssen Sie mehrere Verfahren ausführen, wenn Sie mehrere SDK-Versionen übersprungen haben. Siehe [Upgradeverfahren](mobile-engagement-ios-upgrade-procedure.md) für vollständige Informationen

Für jede neue SDK-Version müssen Sie zunächst die Ordner "EngagementSDK" und "EngagementReach" ersetzen (entfernen und in Xcode neu importieren).

### Version 2.0.0 bis 2.1.0
Keiner.

<!--HONumber=54--> 