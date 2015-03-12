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
	ms.date="09/29/2014" 
	ms.author="wesmc"/>

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Die Registrierung wird ebenfalls geändert, um einen Tag basierend auf der zugewiesenen Benutzer-ID hinzufügen. Schließlich wird das Serverskript so geändert, dass die Benachrichtigung nur an den authentifizierten Benutzer statt an alle Registrierungen gesendet wird.

In diesem Lernprogramm werden Sie durch den folgenden Prozess geführt:

+ [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]
+ [Aktualisieren der App zum Anmelden vor der Registrierung]
+ [Testen der App]
 
Dieses Lernprogramm unterstützt Apps für Android.

##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung der Beispiel-App "TodoList" hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen]<br/>Konfiguriert die Beispiel-App "TodoList" für Pushbenachrichtigungen durch die Verwendung von Notification Hubs. 

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

##<a name="register"></a>Aktualisieren des Diensts zur Verwendung der Authentifizierung für die Registrierung

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf <strong>Speichern</strong>:</p>
<pre><code>function insert(item, user, request) {

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
}</code></pre>

<p>Dieses Einfügeskript sendet mithilfe des Benutzer-ID-Tags eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Google Cloud Messaging-Registrierungen, die vom angemeldeten Benutzer erstellt wurden.</p></li></ol>

##<a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)] 

##<a name="test"></a>Testen der App

[AZURE.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Serviceseitige Autorisierung von Mobile Services-Benutzern][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz].-->

<!-- Anchors. -->
[Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]: #register
[Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
[Testen der App]: #test
[Nächste Schritte]:#next-steps


<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-android-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
