<properties pageTitle="Senden von Pushbenachrichtigungen an authentifizierte Benutzer" description="Erfahren Sie mehr über das Senden von Pushbenachrichtigungen an bestimmte" services="mobile-services, notification-hubs" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET backend" class="current">.NET Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript backend">JavaScript-Back-End</a></div>

Dieses Thema zeigt, wie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten iOS-Gerät gesendet werden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Die Registrierung wird ebenfalls geändert, um einen Tag basierend auf der zugewiesenen Benutzer-ID hinzufügen. Schließlich wird der Servercode so geändert, dass die Benachrichtigung nur an den authentifizierten Benutzer statt an alle Registrierungen gesendet wird.

In diesem Lernprogramm werden Sie durch den folgenden Prozess geführt:

+ [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]
+ [Aktualisieren der App zum Anmelden vor der Registrierung]
+ [Testen der App]

Dieses Lernprogramm unterstützt Apps für Windows Store und Windows Phone Store.

##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung der Beispiel-App "TodoList" hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen]<br/>Konfiguriert die Beispiel-App "TodoList" für Pushbenachrichtigungen durch die Verwendung von Notification Hubs.

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

##<a name="register"></a>Aktualisieren des Diensts zur Verwendung der Authentifizierung für die Registrierung

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Testen der App

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]: #register
[Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
[Testen der App]: #test
[Nächste Schritte]:#next-steps


<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
