<properties
	pageTitle="Hinzufügen von Pushbenachrichtigungen zur Android-App mit Azure Mobile Apps"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Apps zum Senden von Pushbenachrichtigungen an Ihre Android-App."
	services="app-service\mobile"
	documentationCenter="android"
	manager="dwrede"
	editor=""
	authors="ysxu"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="yuaxu"/>

# Hinzufügen von Pushbenachrichtigungen zu Ihrer Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Übersicht
In diesem Lernprogramm fügen Sie Pushbenachrichtigungen zum [Android-Schnellstart]projekt hinzu, damit jedes Mal, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung gesendet wird. Dieses Lernprogramm baut auf dem [Android-Schnellstart]lernprogramm auf, das Sie zuerst abschließen müssen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Pushbenachrichtigungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* [Google-Konto](http://go.microsoft.com/fwlink/p/?LinkId=268302) mit einer verifizierten E-Mail-Adresse.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) &ndash; für ein Node.js-Back-End-Projekt nicht erforderlich.
* Schließen Sie das [Schnellstart-Lernprogramm](app-service-mobile-android-get-started.md) ab.

##<a name="create-hub"></a>Erstellen eines Notification Hubs

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Konfigurieren des mobilen App-Back-Ends zum Senden von Pushanforderungen

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>Aktualisieren des Serverprojekts zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## Hinzufügen von Pushbenachrichtigungen zur App

Sie müssen sicherstellen, dass Ihr Android-App-Projekt zur Verarbeitung von Pushbenachrichtigungen bereit ist.

###Prüfen der Version des Android-SDK

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss.

Wenn Sie den Testvorgang mit einem älteren Gerät durchführen, konsultieren Sie [Set Up Google Play Services SDK] (Einrichten des Google Play Services SDK, in englischer Sprache), um zu ermitteln, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Hinzufügen von Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

###Hinzufügen des Codes

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## Testen der App mit dem veröffentlichten mobilen Dienst

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

##<a id="more"></a>Weitere Informationen

* Mit Tags können Sie für segmentierte Kunden Pushvorgänge auswählen. In [Arbeiten Sie mit der .NET-Back-End-Server-SDK für Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) wird erklärt, wie Sie einer Geräteinstallation Tags hinzufügen.

<!-- URLs -->
[Android-Schnellstart]: app-service-mobile-android-get-started.md

[Set Up Google Play Services SDK]: https://developers.google.com/android/guides/setup

<!---HONumber=AcomDC_0211_2016-->