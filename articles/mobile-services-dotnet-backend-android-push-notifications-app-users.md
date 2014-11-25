<properties linkid="/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/" title=".NET-Back-End" class="current">.NET-Back-End</a> | 
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/"  title="JavaScript-Back-End">JavaScript-Back-End</a>
</div>

In diesem Thema wird erläutert, wie Sie Pushbenachrichtigungen an einen authentifizierten Benutzer auf einem beliebigen registrierten Gerät senden. Anders als beim vorherigen Lernprogramm zur [Pushbenachrichtigung][Pushbenachrichtigung] ändern Sie hier Ihren mobilen Dienst dahingehend, dass sich ein Benutzer authentifizieren muss, ehe der Client sich am Benachrichtigungs-Hub für Pushbenachrichtungen registrieren kann. Die Registrierung wird ebenfalls geändert; es wird ein Tag auf Grundlage der zugewiesenen Benutzer-ID hinzugefügt. Schließlich wird der Servercode so geändert, dass die Benachrichtigung nur an den authentifizierten Benutzer statt an alle Registrierungen gesendet wird.

In diesem Lernprogramm werden folgende Prozesse behandelt:

+ [Aktualisieren des Diensts, sodass für die Registrierung eine Authentifizierung benötigt wird][Aktualisieren des Diensts, sodass für die Registrierung eine Authentifizierung benötigt wird]
+ [Aktualisieren der App zum Anmelden vor der Registrierung][Aktualisieren der App zum Anmelden vor der Registrierung]
+ [Testen der App][Testen der App]

Dieses Lernprogramm unterstützt Android-Apps.

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst die folgenden Mobile Services-Lernprogramme abschließen:

+ [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]<br/>Fügt der To-Do-Listen-Beispielapp das Erfordernis einer Anmeldung hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen][Pushbenachrichtigung]<br>Konfiguriert die To-Do-Listen-Beispielapp für Pushbenachrichtigungen mithilfe von Benachrichtigungs-Hubs.

Nachdem Sie beide Lernprogramm abgeschlossen haben, können Sie verhindern, dass nicht authentifizierte Benutzer sich für Pushbenachrichtigung ihres mobilen Diensts registrieren.

## <a name="register"></a>Aktualisieren des Diensts, sodass für die Registrierung eine Authentifizierung benötigt wird

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

## <a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)]

## <a name="test"></a>Testen der App

[WACOM.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)]

## <a name="next-steps"></a>Next steps  
In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]

 


[Windows Store C#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "Windows Store C#"
[Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "Windows Store JavaScript"
[Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
[iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
[Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
[.NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/ ".NET-Back-End"
[JavaScript-Back-End]: /de-de/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/ "JavaScript-Back-End"
[Pushbenachrichtigung]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/
[Aktualisieren des Diensts, sodass für die Registrierung eine Authentifizierung benötigt wird]: #register
[Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
[Testen der App]: #test
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
[mobile-services-android-push-notifications-app-users]: ../includes/mobile-services-android-push-notifications-app-users.md
[mobile-services-android-test-push-users]: ../includes/mobile-services-android-test-push-users.md
[Mobile Services .NET How-to Conceptual Reference]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
