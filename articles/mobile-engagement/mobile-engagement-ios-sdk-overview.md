<properties
	pageTitle="Übersicht über das iOS-SDK für Azure Mobile Engagement"
	description="Neueste Updates und Verfahren für das iOS-SDK für Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

#iOS SDK für Azure Mobile Engagement

Beginnen Sie hier, um alle Details zur Integration von Azure Mobile Engagement in eine iOS-App zu erhalten. Wenn Sie es zunächst nur probieren möchten, stellen Sie sicher, dass Sie das [15-Minuten-Lernprogramm](mobile-engagement-ios-get-started.md) durchführen

Klicken Sie, um den [SDK-Inhalt](mobile-engagement-ios-sdk-content.md) anzuzeigen

##Integrationsverfahren
1. Beginnen Sie hier: [Integration von Mobile Engagement in Ihre iOS-App](mobile-engagement-ios-integrate-engagement.md)

2. Für Benachrichtigungen: [Integration von Reach (Benachrichtigungen) in Ihre iOS-App](mobile-engagement-ios-integrate-engagement-reach.md)

3. Tag-Plan-Implementierung: [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer iOS-App](mobile-engagement-ios-use-engagement-api.md)


##Versionshinweise

###3\.1.0 (26.08.2015)

-   Behebung von iOS 9-Kompatibilitätsproblemen mit einer Drittanbieterbibliothek. Beim Senden von Umfrageergebnissen, Anwendungsinformationen oder extrahierten Daten wurden Abstürze verursacht.

Eine frühere Version finden Sie unter [Vollständige Versionshinweise](mobile-engagement-ios-release-notes.md)

##Upgrade-Verfahren

Wenn Sie bereits eine ältere Version von Engagement in Ihre Anwendung integriert haben, müssen Sie die folgenden Punkte beim Aktualisieren des SDK beachten.

Möglicherweise müssen Sie mehrere Verfahren ausführen, wenn Sie mehrere SDK-Versionen übersprungen haben. Siehe [Upgradeverfahren](mobile-engagement-ios-upgrade-procedure.md) für vollständige Informationen

Für jede neue SDK-Version müssen Sie zunächst die Ordner "EngagementSDK" und "EngagementReach" ersetzen (entfernen und in Xcode neu importieren).

###Von 2.0.0 zu 3.0.0
Unterstützung für iOS 4.X eingestellt. Ab dieser Version muss das Bereitstellungsziel Ihrer Anwendung mindestens über iOS 6 verfügen.

Wenn Sie Reach in Ihrer Anwendung verwenden, müssen Sie den `remote-notification`-Wert zum `UIBackgroundModes`-Array in Ihrer Datei "Info.plist" hinzufügen, um Remotebenachrichtigungen zu erhalten.

Die Methode `application:didReceiveRemoteNotification:` muss in Ihrem Anwendungsdelegaten durch `application:didReceiveRemoteNotification:fetchCompletionHandler:` ausgetauscht werden.

"AEPushDelegate.h" ist eine veraltete Schnittstelle, und Sie müssen alle Verweise darauf entfernen. Dies umfasst das Entfernen von `[[EngagementAgent shared] setPushDelegate:self]` und der Delegatmethoden aus Ihrem Anwendungsdelegaten:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

<!---HONumber=August15_HO9-->