
<properties
	pageTitle="Senden von Pushbenachrichtigungen an authentifizierte Benutzer"
	description="Erfahren Sie mehr über das Senden von Pushbenachrichtigungen an bestimmte"
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="wesmc"/>


# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Get started with push notifications] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Außerdem wird die Registrierung geändert, um einen Tag basierend auf der zugewiesenen Benutzer-ID hinzuzufügen. Schließlich wird das Serverskript aktualisiert, um Benachrichtigungen nur an authentifizierte Benutzer zu senden, und nicht an alle Registrierungen.

Dieses Lernprogramm unterstützt Apps für Android.

##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Hinzufügen von Authentifizierung zur App]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App „TodoList“ hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen]<br/>Konfiguriert die Beispiel-App „TodoList“ für Pushbenachrichtigungen durch die Verwendung von Notification Hubs.

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

##Aktualisieren des Diensts zur Verwendung der Authentifizierung für die Registrierung

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf <strong>Speichern</strong>:</p>
<pre><code>function insert(item, user, request) {

    // Definieren Sie eine Nutzlast für die Google Cloud Messaging-Toastbenachrichtigung.
    var payload =
        '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

    // Rufen Sie die ID des angemeldeten Benutzers ab.
    var userId = user.userId;

    request.execute({
        success: function() {
            // Wenn der Einfügevorgang erfolgreich war, senden Sie eine Benachrichtigung an alle Geräte,
            // die für den angemeldeten Benutzer als Tag registriert sind.
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
}</code></pre>

<p>Dieses Einfügeskript sendet mithilfe des Benutzer-ID-Tags eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Google Cloud Messaging-Registrierungen, die vom angemeldeten Benutzer erstellt wurden.</p></li></ol>

##Aktualisieren der App zum Anmelden vor der Registrierung

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../../includes/mobile-services-android-push-notifications-app-users.md)]

##Testen der App

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)]

<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users](mobile-services-javascript-backend-service-side-authorization.md), you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: mobile-services-android-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-android-get-started-push.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-javascript-backend-android-get-started-push.md

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=September15_HO1-->