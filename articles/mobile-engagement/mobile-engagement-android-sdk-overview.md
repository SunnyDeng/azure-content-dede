<properties
	pageTitle="Integration des Azure Mobile Engagement Android SDKs"
	description="Neueste Updates und Verfahren für das Android SDK für Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="piyushjo" />


#Android SDK für Azure Mobile Engagement

Beginnen Sie hier, um alle Details zum Integrieren von Azure Mobile Engagement in eine Android-App zu erhalten. Wenn Sie es zunächst nur probieren möchten, stellen Sie sicher, dass Sie das [15-Minuten-Lernprogramm](mobile-engagement-android-get-started.md) durchführen

Klicken Sie, um den [SDK-Inhalt](mobile-engagement-android-sdk-content.md) anzuzeigen.

##Integrationsverfahren
1. Beginnen Sie hier: [Integrieren von Mobile Engagement in Ihre Android-App](mobile-engagement-android-integrate-engagement.md)

2. Für Benachrichtigungen: [Integration von Reach (Benachrichtigungen) in Ihre Android-App](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [Integrieren von GCM mit Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Integrieren von ADM mit Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Tag-Plan-Implementierung: [Verwenden der erweiterten Mobile Engagement-Tagging-API in Ihrer Android-App](mobile-engagement-android-use-engagement-api.md)


##Versionshinweise

##4\.2.0 (03/11/2016)

- Das SDK ist jetzt unter MIT lizenziert.
- Erlauben Sie die Angabe eines benutzerdefinierten Gerätebezeichners während der SDK-Initialisierung.

##4\.1.0 (25.08.2015)

- Behandlung eines neuen Berechtigungsmodells für Android M.
- Konfiguration von Standortfunktionen zur Laufzeit ist nun möglich, anstatt `AndroidManifest.xml` zu verwenden.
- Korrektur eines Berechtigungsfehlers: bei Verwendung von `ACCESS_FINE_LOCATION` wird `ACCESS_COARSE_LOCATION` nicht mehr benötigt.
- Verbesserungen der Stabilität.

Alle Versionen finden Sie unter [Vollständige Versionshinweise](mobile-engagement-android-release-notes.md)

##Upgrade-Verfahren

Wenn Sie bereits eine ältere Version unseres SDKs in die Anwendung integriert haben, finden Sie Informationen dazu in [Upgrade-Verfahren](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_0316_2016-->