<properties linkid="/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone" class="current">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/"  title="JavaScript-Backend">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Pushbenachrichtigungen] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Benachrichtigungshub für Pushbenachrichtigungen registrieren kann. Außerdem wird die Registrierung geändert, um einen Tag basierend auf der zugewiesenen Benutzer-ID hinzuzufügen. Und schließlich wird der Servercode aktualisiert, um die Benachrichtigung nur an den authentifizierten Benutzer statt an alle Registrierungen zu senden.

In diesem Lernprogramm werden Sie durch den folgenden Prozess geführt:

+ [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung][Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]
+ [Aktualisieren der App zum Anmelden vor der Registrierung][Aktualisieren der App zum Anmelden vor der Registrierung]
+ [Testen der App][Testen der App]

Dieses Lernprogramm unterstützt Apps für Windows Phone 8.0 und Windows Phone 8.1 („Silverlight“). Informationen für Windows Phone 8.1 Store-Apps finden Sie in der [Windows Store-Version dieses Themas][Windows Store-Version dieses Themas].

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App „TodoList“ hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen][Pushbenachrichtigungen]<br/>Konfiguriert die Beispiel-App „TodoList“ für Pushbenachrichtigungen durch die Verwendung von Benachrichtigungshubs.

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

## <a name="register"></a>Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

## <a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[WACOM.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)]


## <a name="test"></a>Testen der App

[WACOM.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)]

<!---## <a name="next-steps"></a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->

 


[Windows Store C#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "Windows Store C#"
[Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "Windows Store JavaScript"
[Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
[iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
[Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
[.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/ ".NET-Backend"
[JavaScript-Backend]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/ "JavaScript-Backend"
[Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]: #register
[Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
[Testen der App]: #test
[Windows Store-Version dieses Themas]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
[mobile-services-windows-phone-push-notifications-app-users]: ../includes/mobile-services-windows-phone-push-notifications-app-users.md
[mobile-services-windows-test-push-users]: ../includes/mobile-services-windows-test-push-users.md
[Mobile Services .NET How-to Conceptual Reference]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library