<properties
	pageTitle="Senden von Pushbenachrichtigungen an authentifizierte Benutzer in iOS (JavaScript-Back-End)"
	description="Erfahren Sie mehr über das Senden von Pushbenachrichtigungen an bestimmte Benutzer"
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>


<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

In diesem Thema erfahren Sie, wie Sie unter iOS Pushbenachrichtigungen an einen authentifizierten Benutzer senden. Bevor Sie dieses Lernprogramm starten, bearbeiten Sie zunächst [Erste Schritte mit Authentifizierung] und [Erste Schritte mit Pushbenachrichtigungen].

In diesem Lernprogramm müssen sich Benutzer zuerst authentifizieren. Sie registrieren Sie dazu beim Notification Hub für Pushbenachrichtigungen und aktualisieren Serverskripts, damit diese Benachrichtigungen nur authentifizierte Benutzer gesendet werden.


##<a name="register"></a>Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

Ersetzen Sie die `insert`-Funktion durch den folgenden Code. Klicken Sie dann auf **Speichern**. Das Einfügeskript verwendet das Benutzer-ID-Tag, um eine Pushbenachrichtigung an alle iOS-App-Registrierungen des angemeldeten Benutzers zu senden:

```
// Get the ID of the logged-in user.
var userId = user.userId;

function insert(item, user, request) {
    request.execute();
    setTimeout(function() {
        push.apns.send(userId, {
            alert: "Alert: " + item.text,
            payload: {
                "Hey, a new item arrived: '" + item.text + "'"
            }
        });
    }, 2500);
}
```

##<a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Test App

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Erste Schritte mit Authentifizierung]: mobile-services-ios-get-started-users.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-javascript-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!---HONumber=AcomDC_1203_2015-->