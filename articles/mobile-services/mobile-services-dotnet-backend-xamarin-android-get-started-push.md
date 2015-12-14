<properties
	pageTitle="Erste Schritte mit Mobile Services für Xamarin Android-Apps | Microsoft Azure"
	description="Erfahren Sie, wie Sie Azure Mobile Services und Notification Hubs verwenden, um Pushbenachrichtigungen an Ihre Xamarin Android-App zu senden."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015"
	ms.author="glenga"/>

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Xamarin.Android-App senden. In diesem Lernprogramm fügen Sie mithilfe des Google Cloud Messaging (GCM)-Diensts Pushbenachrichtigungen zum Projekt [Erste Schritte mit Mobile Services] hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Ein aktives Google-Konto
+ [Google Cloud Messaging Client-Komponente] Sie fügen diese Komponente während des Lernprogramms hinzu.

Sie haben die Komponenten [Xamarin.Android] und [Azure Mobile Services][Azure Mobile Services Component] bereits in Ihrem Projekt installiert, als Sie entweder [Erste Schritte mit Mobile Services] bearbeitet haben.

##<a id="register"></a>Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [Aktivieren von GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Konfigurieren von mobilen Diensten zum Senden von Pushanforderungen

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a id="configure-app"></a>Konfigurieren vorhandener Projekte für Pushbenachrichtigungen

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a name="test-app"></a>Testen der App mit dem veröffentlichten mobilen Dienst

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

###<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-xamarin-android-get-started.md


[Google Cloud Messaging Client-Komponente]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_1203_2015-->