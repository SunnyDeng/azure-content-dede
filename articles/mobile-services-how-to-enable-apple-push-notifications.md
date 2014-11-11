<properties pageTitle="How to enable Apple push notifications" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to Apple push notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Aktivieren von Apple-Pushbenachrichtigungen

Dieses Thema zeigt, wie Sie die iOS-App bereit machen für Pushbenachrichtigung mithilfe von Apple Push Notification Service (APNS). Das erhaltene Zertifikat wird verwendet, um die iOS-App für Pushbenachrichtigung im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] zu registrieren. Ein vollständiges End-to-End-Lernprogramm einschließlich Aktualisierung Ihrer App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].

[WACOM.INCLUDE [Pushbenachrichtigungen aktivieren](../includes/enable-apple-push-notifications.md)]

Jetzt können Sie diesen .p12-Zertifikat verwenden, um Dienste für die Authentifizierung mit APNS zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[Pushbenachrichtigungen aktivieren]: ../includes/enable-apple-push-notifications.md
