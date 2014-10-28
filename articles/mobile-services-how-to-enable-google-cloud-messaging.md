<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Aktivieren von Google Cloud Messaging

Dieses Thema zeigt, wie Sie die Android-App bereit machen für Pushbenachrichtigung mithilfe von Google Cloud Messaging (GCM). Der erhaltene API-Schlüssel wird verwendet, um die Android-App für Pushbenachrichtigung im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] zu registrieren. Ein vollständiges End-to-End-Lernprogramm einschließlich Aktualisierung Ihrer App finden Sie unter [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Jetzt können Sie diesen API-Schlüsselwert verwenden, um Dienste für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
