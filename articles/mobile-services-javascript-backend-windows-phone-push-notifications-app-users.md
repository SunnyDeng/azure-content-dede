<properties 
	pageTitle="Senden von Pushbenachrichtigungen an authentifizierte Benutzer" 
	description="Erfahren Sie mehr über das Senden von Pushbenachrichtigungen an bestimmte" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="glenga"/>

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Get started with push notifications] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Außerdem wird die Registrierung geändert, um einen Tag basierend auf der zugewiesenen Benutzer-ID hinzuzufügen. Schließlich wird das Serverskript aktualisiert, um Benachrichtigungen nur an authentifizierte Benutzer zu senden, und nicht an alle Registrierungen.

In diesem Lernprogramm werden Sie durch den folgenden Prozess geführt:

1. [Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]
2. [Aktualisieren der App zum Anmelden vor der Registrierung]
3. [Testen der App]
 
Dieses Lernprogramm unterstützt Apps für Windows Phone 8.0 und Windows Phone 8.1 Silverlight. Informationen für Windows Phone 8.1 Store-Apps finden Sie in der [Windows Store-Version dieses Themas](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md).

##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App „TodoList“ hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen]<br/>Konfiguriert die Beispiel-App „TodoList“ für Pushbenachrichtigungen durch die Verwendung von Notification Hubs.

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

##<a name="register"></a>Aktualisieren des Diensts zur Verwendung der Authentifizierung für die Registrierung

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf <strong>Speichern</strong>:</p>
<pre><code>function insert(item, user, request) {
	// Nutzlast für Windows Phone-Popupbenachrichtigung definieren.
	var payload = '&lt;?xml version="1.0" encoding="utf-8"?>' +
		'&lt;wp:Notification xmlns:wp="WPNotification">&lt;wp:Toast>' +
		'&lt;wp:Text1>Neues Element&lt;/wp:Text1>&lt;wp:Text2>' + item.text + 
		'&lt;/wp:Text2>&lt;/wp:Toast>&lt;/wp:Notification>';

	// Rufen Sie die ID des angemeldeten Benutzers ab.
	var userId = user.userId;		

	request.execute({
		success: function() {
			// Wenn der Einfügevorgang erfolgreich war, senden Sie eine Benachrichtigung.
			push.mpns.send(userId, payload, 'toast', 22, {
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
}</code></pre>

<p>Dieses Einfügeskript sendet mithilfe des Benutzer-ID-Tags eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Windows Phone (MPNS)-App-Registrierungen, die vom angemeldeten Benutzer erstellt wurden.</p></li></ol>

##<a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)] 


##<a name="test"></a>Testen der App

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->

<!-- Anchors. -->
[Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung]: #register
[Aktualisieren der App zum Anmelden vor der Registrierung]: #update-app
[Testen der App]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: mobile-services-windows-phone-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-phone-get-started-push.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-javascript-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=54-->