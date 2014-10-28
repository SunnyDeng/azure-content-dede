<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone" >Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS" class="current">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET-Back-End">.NET-Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript-Back-End" class="current">JavaScript-Back-End</a></div>

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem registrierten iOS-Gerät senden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Pushbenachrichtigungen] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der iOS-Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Außerdem wird die Registrierung geändert, um einen Tag basierend auf der zugewiesenen Benutzer-ID hinzuzufügen. Und schließlich wird das Serverskript aktualisiert, um die Benachrichtigung nur an den authentifizierten Benutzer statt an alle Registrierungen zu senden.

In diesem Lernprogramm werden Sie durch den folgenden Prozess geführt:

-   [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung][Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]
-   [Aktualisieren der App zum Anmelden vor der Registrierung][Aktualisieren der App zum Anmelden vor der Registrierung]
-   [Testen der App][Testen der App]

Dieses Lernprogramm unterstützt Apps für Windows Phone 8.0 und Windows Phone 8.1 („Silverlight“). Informationen für Windows Phone 8.1 Store-Apps finden Sie in der [Windows Store-Version dieses Themas][Windows Store-Version dieses Themas].

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Fügt eine Anmeldeanforderung zur Beispiel-App „TodoList“ hinzu.

-   [Erste Schritte mit Pushbenachrichtigungen][Pushbenachrichtigungen]
    Konfiguriert die Beispiel-App „TodoList“ für Pushbenachrichtigungen durch die Verwendung von Notification Hubs.

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

## <a name="register"></a>Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users][mobile-services-javascript-backend-push-notifications-app-users]]

1.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {

                function insert(item, user, request) {
                    request.execute();
                    setTimeout(function() {
                        push.apns.send(null, {
                            alert: "Alert: " + item.text,
                            payload: {
                                "Hey, a new item arrived: '" + item.text + "'"
                            }
                        });
                    }, 2500);
                }

        }

    Dieses Einfügeskript sendet mithilfe des Benutzer-ID-Tags eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Windows Phone (MPNS)-App-Registrierungen, die vom angemeldeten Benutzer erstellt wurden.

## <a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login][mobile-services-ios-push-notifications-app-users-login]]

## <a name="test"></a>Testen der App

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app][mobile-services-ios-push-notifications-app-users-test-app]]

<!-- Anchors. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users "Android"
  [.NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/ ".NET-Back-End"
  [JavaScript-Back-End]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/ "JavaScript-Back-End"
  [Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]: #register
  [Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
  [Testen der App]: #test
  [Windows Store-Version dieses Themas]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-ios-get-started-users/
  [mobile-services-javascript-backend-push-notifications-app-users]: ../includes/mobile-services-javascript-backend-push-notifications-app-users.md
  [mobile-services-ios-push-notifications-app-users-login]: ../includes/mobile-services-ios-push-notifications-app-users-login.md
  [mobile-services-ios-push-notifications-app-users-test-app]: ../includes/mobile-services-ios-push-notifications-app-users-test-app.md
