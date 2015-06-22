<properties 
	pageTitle="Erste Schritte mit der Pushbenachrichtigungen (Android) | Mobile Dev Center" 
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
	ms.date="04/07/2015" 
	ms.author="ricksal"/>

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an Ihre Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Google Cloud Messaging (GCM) zu dem Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Für dieses Lernprogramm benötigen Sie außerdem Visual Studio 2013.

>[AZURE.NOTE]Die Eclipse-Version dieses Lernprogramms finden Sie unter [Erste Schritte mit Push-Benachrichtigungen (Eclipse)].
 
##<a id="register"></a>Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Konfigurieren von mobilen Diensten zum Senden von Pushanforderungen

[AZURE.INCLUDE [mobile-services-android-configure-push](../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a name="update-app"></a>Hinzufügen von Pushbenachrichtigungen zur App

###Prüfen der Version des Android-SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss.

Wenn Sie den Testvorgang mit einem älteren Gerät durchführen, konsultieren Sie [Set Up Google Play Services SDK](Einrichten des Google Play Services SDK, in englischer Sprache), um zu ermitteln, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Hinzufügen von Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

##<a name="test-app"></a>Testen der App mit dem veröffentlichten mobilen Dienst

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

###<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../includes/mobile-services-android-push-notifications-test.md)]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Android-App für die Arbeit mit Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen aufgezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Senden von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an authentifizierte Benutzer demonstriert wird.

+ [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] <br/>Erfahren Sie, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an authentifizierte Benutzer gesendet werden.

+ [Senden von Übertragungsbenachrichtigungen an Abonnenten] <br/>Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

+ [Senden vorlagenbasierter Benachrichtigungen an Abonnenten] <br/>Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen von einem mobilen Dienst genutzt werden, ohne dass Sie im Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Hinzufügen von Authentifizierung zur app][Get started with authentication] <br/> Erfahren Sie, wie sich Benutzer Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten authentifizieren können.

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
[Erste Schritte mit Push-Benachrichtigungen (Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-EC.md
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-android-get-started.md
[Erste Schritte mit Daten]: mobile-services-dotnet-backend-android-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[Verwenden der Android-Clientbibliothek für Mobile Services]: mobile-services-android-how-to-use-client-library.md

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: mobile-services-dotnet-backend-android-push-notifications-app-users.md

[Was sind Notification Hubs?]: notification-hubs/notification-hubs-overview.md
[Senden von Übertragungsbenachrichtigungen an Abonnenten]: notification-hubs/notification-hubs-windows-store-dotnet-send-breaking-news.md
[Senden vorlagenbasierter Benachrichtigungen an Abonnenten]: notification-hubs/notification-hubs-windows-store-dotnet-send-localized-breaking-news.md
[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54-->