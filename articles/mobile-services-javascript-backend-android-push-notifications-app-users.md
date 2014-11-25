<properties linkid="/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/" title=".NET-Back-End">.NET-Back-End</a> | 
<a href="/de-de/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/"  title="JavaScript-Back-End" class="current">JavaScript-Back-End</a>
</div>

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Pushbenachrichtigungen] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Außerdem wird die Registrierung geändert, um einen Tag basierend auf der zugewiesenen Benutzer-ID hinzuzufügen. Und schließlich wird das Serverskript aktualisiert, um die Benachrichtigung nur an den authentifizierten Benutzer statt an alle Registrierungen zu senden.

In diesem Lernprogramm werden Sie durch den folgenden Prozess geführt:

-   [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung][Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]
-   [Aktualisieren der App zum Anmelden vor der Registrierung][Aktualisieren der App zum Anmelden vor der Registrierung]
-   [Testen der App][Testen der App]

Dieses Lernprogramm unterstützt Apps für Android.

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Fügt eine Anmeldeanforderung zur Beispiel-App „TodoList“ hinzu.

-   [Erste Schritte mit Pushbenachrichtigungen][Pushbenachrichtigungen]
    Konfiguriert die Beispiel-App „TodoList“ für Pushbenachrichtigungen durch die Verwendung von Notification Hubs.

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

## <a name="register"></a>Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

1.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {

            // Define a payload for the Google Cloud Messaging toast notification.
            var payload = 
                '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

            // Get the ID of the logged-in user.
            var userId = user.userId;       

            request.execute({
                success: function() {
                    // If the insert succeeds, send a notification to all devices 
                    // registered to the logged-in user as a tag.
                    push.gcm.send(userId, payload, {
                        success: function(pushResponse) {
                            console.log("Sent push with " + userId + " tag:", pushResponse, payload);
                            request.respond();
                            },              
                            error: function (pushResponse) {
                                    console.log("Error Sending push:", pushResponse);
                                request.respond(500, { error: pushResponse });
                                }
                            });
                        },
                        error: function(err) {
                            console.log("request.execute error", err)
                            request.respond();
                        }
                    });
        }

    Dieses Einfügeskript sendet mithilfe des Benutzer-ID-Tags eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Google Cloud Messaging-Registrierungen, die vom angemeldeten Benutzer erstellt wurden.

## <a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)]

## <a name="test"></a>Testen der App

[WACOM.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)]

<!---## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->  

  [Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]: #register
  [Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
  [Testen der App]: #test
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-android-get-started-users/
