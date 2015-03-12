<properties 
	pageTitle="Aktivieren von Google Cloud Messaging" 
	description="Befolgen Sie dieses Lernprogramm, um einen neuen Dienst mit Azure Mobile Services zu erstellen." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Aktivieren von Google Cloud Messaging

Dieses Thema zeigt, wie Sie die Android-App bereit machen für Pushbenachrichtigung mithilfe von Google Cloud Messaging (GCM). Der erhaltene API-Schlüssel wird verwendet, um die Android-App für Pushbenachrichtigung im [Azure-Verwaltungsportal][Verwaltungsportal] zu registrieren. Ein vollständiges End-to-End-Lernprogramm einschließlich Aktualisierung Ihrer App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen]. 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Jetzt können Sie diesen API-Schlüsselwert verwenden, um Dienste für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express für Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Verwaltungsportal]: https://manage.windowsazure.com/
[.NET-Back-End-Version]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started


<!--HONumber=42-->
