<properties pageTitle="Senden von Pushbenachrichtigungen an authentifizierte Benutzer" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Erfahren Sie mehr über das Senden von Pushbenachrichtigungen an bestimmte " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Die Registrierung wird ebenfalls geändert; es wird ein Tag auf Grundlage der zugewiesenen Benutzer-ID hinzugefügt. Schließlich wird der Servercode so geändert, dass die Benachrichtigung nur an den authentifizierten Benutzer statt an alle Registrierungen gesendet wird.

In diesem Lernprogramm werden Sie durch den folgenden Prozess geführt:

+ [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]
+ [Aktualisieren der App zum Anmelden vor der Registrierung]
+ [Testen der App]
 
Dieses Lernprogramm unterstützt Apps für Windows Phone 8.0 und Windows Phone 8.1 ("Silverlight"). Informationen für Windows Phone 8.1 Store-Apps finden Sie in der [Windows Store-Version dieses Themas](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users).

##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App "TodoList" hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen]<br/>Konfiguriert die Beispiel-App "TodoList" für Pushbenachrichtigungen durch die Verwendung von Notification Hubs. 

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

##<a name="register"></a>Aktualisieren des Diensts zur Verwendung der Authentifizierung für die Registrierung

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

##<a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[WACOM.INCLUDE [mobile-services-windows-phone-push-notifications-app- md)] users](../includes/mobile-services-windows-phone-push-notifications-app-users.


##<a name="test"></a>Testen der App

[WACOM.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Dienstseitige Autorisierung von Mobile Services-Benutzern][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET - Erstellen einer konzeptionellen Referenz]-->

<!-- Anchors. -->
[Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]: #register
[Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
[Testen der App]: #test
[Nächste Schritte]:#next-steps


<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
