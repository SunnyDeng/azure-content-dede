<properties 
	pageTitle="Senden von Pushbenachrichtigungen an authentifizierte Benutzer der universellen Windows-App | Azure Mobile Services" 
	description="Erfahren Sie, wie Sie mit Azure Mobile Services Pushbenachrichtigungen an bestimmte Benutzer Ihrer universellen Windows C#-App senden können." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/02/2015" 
	ms.author="glenga"/>

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Übersicht
In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm [Hinzufügen von Pushbenachrichtigungen zur App] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Die Registrierung wird auch verändert, um ein Tag basierend auf der zugewiesenen Benutzer-ID hinzuzufügen. Schließlich wird das Serverskript aktualisiert, um Benachrichtigungen nur an authentifizierte Benutzer zu senden, und nicht an alle Registrierungen.

In diesem Lernprogramm werden Sie durch den folgenden Prozess geführt:

1. [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]
2. [Aktualisieren der App zum Anmelden vor der Registrierung]
3. [Testen der App]
 
Dieses Lernprogramm unterstützt Apps für Windows Store und Windows Phone Store.

##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Hinzufügen von Authentifizierung zur App]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App "TodoList" hinzu.

+ [Hinzufügen von Pushbenachrichtigungen zur App] <br/>Konfiguriert die Beispiel-App "TodoList" für Pushbenachrichtigungen durch die Verwendung von Notification Hubs.

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

##<a name="register"></a>Aktualisieren des Diensts zur Verwendung der Authentifizierung für die Registrierung

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

&nbsp;&nbsp;5. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

	function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
    '<binding template="ToastText01"><text id="1">' +
    item.text + '</text></binding></visual></toast>';

    // Rufen Sie die ID des angemeldeten Benutzers ab.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // Wenn der Einfügevorgang erfolgreich war, senden Sie eine Benachrichtigung an alle Geräte, 
	    	// die für den angemeldeten Benutzer als Tag registriert sind.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
	}

&nbsp;&nbsp;Dieses Einfügeskript sendet mithilfe des Benutzer-ID-Tags eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Windows Store-App-Registrierungen, die vom angemeldeten Benutzer erstellt wurden.

##<a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Testen der App

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]: #register
[Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
[Testen der App]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Hinzufügen von Pushbenachrichtigungen zur App]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
 

<!---HONumber=Nov15_HO2-->