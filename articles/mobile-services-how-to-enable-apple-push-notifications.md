<properties 
	pageTitle="Aktivieren von Apple-Pushbenachrichtigungen" 
	description="Befolgen Sie dieses Lernprogramm, um einen neuen Dienst mit Azure Mobile Services zu erstellen." 
	services="mobile-services, notification-hubs" 
	documentationCenter="ios" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Aktivieren von Apple-Pushbenachrichtigungen

Dieses Thema zeigt, wie Sie die iOS-App bereit machen für Pushbenachrichtigung mithilfe von Apple Push Notification Service (APNS). Das erhaltene Zertifikat wird verwendet, um die iOS-App für Pushbenachrichtigung im [Azure-Verwaltungsportal][Verwaltungsportal] zu registrieren. Ein vollständiges End-to-End-Lernprogramm einschließlich Aktualisierung Ihrer App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen]. 

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

Jetzt können Sie diesen .p12-Zertifikat verwenden, um Dienste für die Authentifizierung mit APNS zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Verwaltungsportal]: https://manage.windowsazure.com/



<!--HONumber=42-->
