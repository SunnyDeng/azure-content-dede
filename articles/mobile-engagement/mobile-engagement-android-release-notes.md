<properties 
	pageTitle="Integration des Azure Mobile Engagement Android SDKs"
	description="Neueste Updates und Verfahren für das Android SDK für Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="piyushjo"/>


#Versionshinweise

##4\.1.0 (25.08.2015)

- Behandlung eines neuen Berechtigungsmodells für Android M.
- Konfiguration von Standortfunktionen zur Laufzeit ist nun möglich, anstatt `AndroidManifest.xml` zu verwenden.
- Korrektur eines Berechtigungsfehlers: bei Verwendung von `ACCESS_FINE_LOCATION` wird `ACCESS_COARSE_LOCATION` nicht mehr benötigt.
- Verbesserungen der Stabilität.

##4\.0.0 (06.07.2015)

-   Interne Protokolländerungen zur Steigerung der Zuverlässigkeit von Analysen und Push
-   Ein systemeigener Push (GCM/ADM) wird jetzt auch für In-App-Benachrichtigungen verwendet, Sie müssen daher die Anmeldeinformationen für den systemeigenen Push für jeden Push-Kampagnentyp konfigurieren.
-   Korrektur für Überblick-Benachrichtigung: Es wurde nach dem Push nur 10 angezeigt.
-   Beheben eines Fehlers in der Webansicht: durch Klicken auf einen Link wurde auch die Standardaktions-URL ausgeführt.
-   Korrektur für seltenen Absturz im Zusammenhang mit der lokalen Speicherverwaltung.
-   Korrektur der Zeichenfolgenverwaltung für die dynamische Konfiguration.
-   Aktualisierung des Endbenutzer-Lizenzvertrags.

##3\.0.0 (17.02.2015)

-   Erste Version von Azure Mobile Engagement
-   Die „appId“-Konfiguration wird durch eine Verbindungszeichenfolgen-Konfiguration ersetzt.
-   API entfernt, um beliebige XMPP-Nachrichten über beliebige XMPP-Entitäten zu senden und zu empfangen.
-   API entfernt, um Nachrichten zwischen Geräten zu senden und zu empfangen.
-   Verbesserungen der Sicherheit.
-   Google Play und die SmartAd-Nachverfolgung wurden entfernt.

 

<!---HONumber=August15_HO9-->