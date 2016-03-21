<properties
	pageTitle="Erste Schritte mit Pushbenachrichtigungen (Android) | Microsoft Azure"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre Android-.NET-App."
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="ricksal"/>

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App
[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Informationen für die entsprechende Mobile Apps-Version dieses Themas finden Sie unter [Hinzufügen von Pushbenachrichtigungen zu Ihrer Android-App](../app-service-mobile/app-service-mobile-android-get-started-push.md).
 
&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an Ihre Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Google Cloud Messaging (GCM) zu dem Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen, um Ihr Projekt mit dem mobilen Dienst zu verbinden. Für dieses Lernprogramm benötigen Sie außerdem Visual Studio 2013.

## Beispielcode
Den vollständigen Quellcode finden Sie [hier](https://github.com/RickSaling/mobile-services-samples/tree/push/GettingStartedWithPush).

## Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## Konfigurieren des mobilen Diensts zum Senden von Pushanforderungen

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## Aktualisieren des mobilen Diensts zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

## Hinzufügen von Pushbenachrichtigungen zur App

###Prüfen der Version des Android-SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss.

Wenn Sie auf einem älteren Gerät testen, konsultieren Sie Einrichten des Google Play Services SDK, um herauszufinden, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Hinzufügen von Play Services](../../includes/mobile-services-add-google-play-services.md)]

###Hinzufügen des Codes

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]

## Testen der App mit dem veröffentlichten mobilen Dienst

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

### Aktivieren von Pushbenachrichtigungen für lokale Tests

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Android-App für die Arbeit mit Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen aufgezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Senden von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an authentifizierte Benutzer demonstriert wird.

+ [Senden von Übertragungsbenachrichtigungen an Abonnenten] <br/>Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

+ [Senden vorlagenbasierter Benachrichtigungen an Abonnenten] <br/>Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen von einem mobilen Dienst genutzt werden, ohne dass Sie im Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Was sind Notification Hubs?] <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Hier finden Sie die Anleitungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen.

* [Verwenden der Android-Clientbibliothek für Mobile Services] <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit Android.

<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications (Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-ec.md
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-android-get-started.md
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[Verwenden der Android-Clientbibliothek für Mobile Services]: mobile-services-android-how-to-use-client-library.md

[Was sind Notification Hubs?]: ../notification-hubs-overview.md
[Senden von Übertragungsbenachrichtigungen an Abonnenten]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[Senden vorlagenbasierter Benachrichtigungen an Abonnenten]: ../notification-hubs-windows-store-dotnet-send-localized-breaking-news.md

<!---HONumber=AcomDC_0309_2016-->