<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Windows Store JavaScript" >Windows Store JavaScript</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS" class="current">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript-Backend">JavaScript-Backend</a></div>

Dieses Thema zeigt, wie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten iOS-Gerät gesendet werden. Anders als beim vorherigen Lernprogramm zur [Pushbenachrichtigung][Pushbenachrichtigung] ändern Sie hier Ihren mobilen Dienst dahingehend, dass sich ein Benutzer authentifizieren muss, ehe der Client sich am Benachrichtigungs-Hub für Pushbenachrichtungen registrieren kann. Die Registrierung wird ebenfalls geändert; es wird ein Tag auf Grundlage der zugewiesenen Benutzer-ID hinzugefügt. Schließlich wird der Servercode so geändert, dass die Benachrichtigung nur an den authentifizierten Benutzer statt an alle Registrierungen gesendet wird.

In diesem Lernprogramm werden folgende Prozesse behandelt:

+ [Aktualisieren des Diensts, sodass für die Registrierung eine Authentifizierung benötigt wird][Aktualisieren des Diensts, sodass für die Registrierung eine Authentifizierung benötigt wird]
+ [Aktualisieren der App zum Anmelden vor der Registrierung][Aktualisieren der App zum Anmelden vor der Registrierung]
+ [Testen der App][Testen der App]

Dieses Lernprogramm unterstützt sowohl Windows Store- als auch Windows Phone Store-Apps.

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst die folgenden Mobile Services-Lernprogramme abschließen:

+ [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]<br/>Fügt der To-Do-Listen-Beispielapp das Erfordernis einer Anmeldung hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen][Pushbenachrichtigung]<br/>Konfiguriert die To-Do-Listen-Beispielapp für Pushbenachrichtigungen mithilfe von Benachrichtigungs-Hubs.

Nachdem Sie beide Lernprogramm abgeschlossen haben, können Sie verhindern, dass nicht authentifizierte Benutzer sich für Pushbenachrichtigung ihres mobilen Diensts registrieren.

## <a name="register"></a>Aktualisieren des Diensts, sodass für die Registrierung eine Authentifizierung benötigt wird

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

## <a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a>Testen der App

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

 


[Windows Store C#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "Windows Store C#"
[Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "Windows Store JavaScript"
[Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
[iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
[Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
[.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/ ".NET-Backend"
[JavaScript-Backend]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/ "JavaScript-Backend"
[Pushbenachrichtigung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Aktualisieren des Diensts, sodass für die Registrierung eine Authentifizierung benötigt wird]: #register
[Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
[Testen der App]: #test
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
[mobile-services-ios-push-notifications-app-users-login]: ../includes/mobile-services-ios-push-notifications-app-users-login.md
[mobile-services-ios-push-notifications-app-users-test-app]: ../includes/mobile-services-ios-push-notifications-app-users-test-app.md
