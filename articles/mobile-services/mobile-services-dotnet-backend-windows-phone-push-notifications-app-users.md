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
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="glenga"/>

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm [Hinzufügen von Pushbenachrichtigungen zur App] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Die Registrierung wird auch verändert, um ein Tag basierend auf der zugewiesenen Benutzer-ID hinzuzufügen. Abschließend wird der Servercode aktualisiert, um die Benachrichtigung nur an den authentifizierten Benutzer anstatt an alle Registrierungen zu senden.

Dieses Lernprogramm unterstützt Apps für Windows Phone 8.0 und Windows Phone 8.1 Silverlight. Informationen für Windows Phone 8.1 Store-Apps finden Sie in der [Windows Store-Version dieses Themas](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).

##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Hinzufügen von Authentifizierung zur App]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App "TodoList" hinzu.

+ [Hinzufügen von Pushbenachrichtigungen zur App] <br/>Konfiguriert die Beispiel-App "TodoList" für Pushbenachrichtigungen durch die Verwendung von Notification Hubs.

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

##<a name="register"></a>Aktualisieren des Diensts zur Verwendung der Authentifizierung für die Registrierung

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Aktualisieren der App zum Anmelden vor der Registrierung

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../../includes/mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>Testen der App

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Hinzufügen von Pushbenachrichtigungen zur App]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO1-->