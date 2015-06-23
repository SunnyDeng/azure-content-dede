<properties 
	pageTitle="Senden von Pushbenachrichtigungen an authentifizierte Benutzer" 
	description="Erfahren Sie mehr über das Senden von Pushbenachrichtigungen an bestimmte" 
	services="mobile-services,notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Senden von Pushbenachrichtigungen an authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

## Übersicht

In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen an authentifizierte Benutzer auf einem beliebigen registrierten Gerät senden. Im Gegensatz zum vorherigen Lernprogramm für [Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen] wird in diesem Lernprogramm der mobile Dienst geändert, sodass sich Benutzer authentifizieren müssen, bevor sich der Client beim Notification Hub für Pushbenachrichtigungen registrieren kann. Die Registrierung wird auch verändert, um ein Tag basierend auf der zugewiesenen Benutzer-ID hinzuzufügen. Abschließend wird der Servercode aktualisiert, um die Benachrichtigung nur an den authentifizierten Benutzer anstatt an alle Registrierungen zu senden.


Dieses Lernprogramm unterstützt Apps für Android.

## Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App]<br/>Fügt eine Anmeldeanforderung zur TodoList-Beispiel-App hinzu.

+ [Erste Schritte mit Pushbenachrichtigungen]<br/>Konfiguriert die Beispiel-App "TodoList" für Pushbenachrichtigungen durch die Verwendung von Notification Hubs. 

Nachdem Sie diese beiden Lernprogramme abgeschlossen haben, können Sie verhindern, dass sich nicht authentifizierte Benutzer für Pushbenachrichtigungen von Ihrem mobilen Dienst registrieren.

## Aktualisieren des Diensts zur Verwendung von Authentifizierung für die Registrierung

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

## Aktualisieren der App zum Anmeldung vor der Registrierung

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](mobile-services-android-push-notifications-app-users.md)] 

## Testen der App

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)] 


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=47-->
 