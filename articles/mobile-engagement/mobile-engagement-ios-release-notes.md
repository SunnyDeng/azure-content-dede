<properties
	pageTitle="Versionshinweise zum iOS-SDK für Azure Mobile Engagement"
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

#Versionshinweise

##3\.2.1 (11.12.2015)

-   Es wurde eine Verzögerung behoben, wenn eine neue Instanz der App durch eine Benachrichtigung mit Deep-Links ausgelöst wird. 

##3\.2.0 (08.10.2015)

-   Bitcode im SDK aktiviert, um die Verwendung mit **Xcode 7** zu ermöglichen.
-   Fehler im Zusammenhang mit In-App-Benachrichtigungen behoben.
-   Die Zuverlässigkeit von In-App-Benachrichtigungen bei niedriger Akkukapazität und in ähnlichen Szenarien verbessert.
-   Zusätzliche Konsolenprotokolle entfernt, die von Drittanbieterbibliothek generiert werden.

##3\.1.0 (26.08.2015)

-   Behebung von iOS 9-Kompatibilitätsproblemen mit einer Drittanbieterbibliothek. Beim Senden von Umfrageergebnissen, Anwendungsinformationen oder extrahierten Daten wurden Abstürze verursacht.

##3\.0.0 (19.06.2015)

-   Mobile Engagement verwendet stille Pushbenachrichtigungen.
-   Unterstützung für iOS 4.X eingestellt. Ab dieser Version muss das Bereitstellungsziel Ihrer Anwendung mindestens über iOS 6 verfügen.

##2\.2.0 (21.05.2015)

-   Die Mobile Engagement-Geräte-ID für Geräte mit einer früheren Version als iOS 6 basiert jetzt auf einer während der Installation generierten GUID.

##2\.1.0 (24.04.2015)

-   Swift-Kompatibilität hinzugefügt.
-   Wenn Sie auf eine Benachrichtigung klicken, wird die Aktions-URL nun direkt nach dem Öffnen der Anwendung ausgeführt.
-   Fehlende Headerdatei im SDK-Paket hinzugefügt.
-   Behebung eine Fehlers, wenn der Mobile Engagement-Absturzmelder deaktiviert wurde.

##2\.0.0 (17.02.2015)

-   Erste Version von Azure Mobile Engagement
-   appId/sdkKey-Konfiguration wird durch die Konfiguration einer Verbindungszeichenfolge ersetzt.
-   API entfernt, um beliebige XMPP-Nachrichten über beliebige XMPP-Entitäten zu senden und zu empfangen.
-   API entfernt, um Nachrichten zwischen Geräten zu senden und zu empfangen.
-   Verbesserungen der Sicherheit.
-   SmartAd-Verfolgung entfernt.

<!---HONumber=AcomDC_1217_2015-->