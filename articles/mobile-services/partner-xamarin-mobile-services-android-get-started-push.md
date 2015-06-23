<properties 
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Mobile Services-Apps – Mobile Services" 
	description="Erfahren Sie mehr über die Verwendung von Pushbenachrichtigungen in Xamarin.Android-Apps mit Azure Mobile Services." 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/12/2015" 
	ms.author="glenga"/>

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Xamarin.Android-App senden. In diesem Lernprogramm fügen Sie mithilfe des Google Cloud Messaging (GCM)-Diensts Pushbenachrichtigungen zum Projekt [Erste Schritte mit Mobile Services]. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Aktivieren von Google Cloud Messaging](#register)
2. [Konfigurieren von Mobile Services](#configure)
4. [Konfigurieren des Projekts für Pushbenachrichtigungen](#configure-app)
5. [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
6. [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Ein aktives Google-Konto
+ [Google Cloud Messaging Client-Komponente] Sie fügen diese Komponente während des Lernprogramms hinzu.

Sie haben die Komponenten [Xamarin.Android] und [Azure Mobile Services][Azure Mobile Services Component] bereits in Ihrem Projekt installiert, wenn Sie entweder [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] abgeschlossen haben.

## <a id="register"></a>Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <a id="configure"></a>Konfigurieren von mobilen Diensten zum Senden von Pushanforderungen

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## <a id="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts zum Senden von Benachrichtigungen

>[AZURE.NOTE]Die folgenden Schritte veranschaulichen die Vorgehensweise beim Aktualisieren des Skripts, das für den Einfügevorgang in der TodoItem-Tabelle im Azure-Verwaltungsportal registriert ist. Der Zugriff und die Bearbeitung dieses Mobile Service-Skripts ist auch direkt in Visual Studio am Azure-Knoten im Server-Explorer möglich.

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


## <a id="configure-app"></a>Konfigurieren vorhandener Projekte für Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

## <a id="test"></a>Testen von Pushbenachrichtigungen in der App

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Hinzufügen von Mobile Services zu einer vorhandenen App]
  <br/>Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung](mobile-services-android-get-started-users.md)
  <br/>Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

* [Was sind Notification Hubs?](../notification-hubs-overview.md)
  <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  <br/>Hier finden Sie die Anleitungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen.

* [Verwenden der .NET-Clientbibliothek für Mobile Services](mobile-services-windows-dotnet-how-to-use-client-library.md)
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit Xamarin C#-Code.

* [Mobile Services: Serverskriptreferenz](mobile-services-how-to-use-server-scripts.md)
  <br/>Erfahren Sie mehr über das Implementieren von Geschäftslogik in Ihren mobilen Dienst.

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: mobile-services-ios-get-started.md
[Hinzufügen von Mobile Services zu einer vorhandenen App]: mobile-services-android-get-started-data.md

[Google Cloud Messaging Client-Komponente]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
<!--HONumber=54--> 