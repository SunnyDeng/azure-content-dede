<properties
	pageTitle="Hinzufügen von Pushbenachrichtigungen zur iOS-App mit Azure Mobile Apps"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Apps zum Senden von Pushbenachrichtigungen an Ihre iOS-App."
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="krisragh"/>


# Hinzufügen von Pushbenachrichtigungen zu Ihrer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Übersicht
In diesem Tutorial fügen Sie Pushbenachrichtigungen zum [iOS-Schnellstartprojekt] hinzu, damit jedes Mal, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung gesendet wird. Dieses Tutorial baut auf dem [iOS-Schnellstart]-Tutorial auf, das Sie zuerst abschließen müssen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Pushbenachrichtigungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

Der [iOS-Simulator bietet keine Unterstützung für Pushbenachrichtigungen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), sodass Sie für dieses Tutorial ein physisches iOS-Gerät und eine [Apple-Entwicklerprogramm-Mitgliedschaft](https://developer.apple.com/programs/ios/) benötigen.

##<a name="create-hub"></a>Erstellen eines Notification Hubs

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>Registrieren der App für Pushbenachrichtigungen

[AZURE.INCLUDE [Aktivieren von Apple-Pushbenachrichtigungen](../../includes/enable-apple-push-notifications.md)]

## Konfigurieren von Azure zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Aktualisieren des Serverprojekts zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="publish-the-service"></a>Bereitstellen des Serverprojekts in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## <a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

[AZURE.INCLUDE [Hinzufügen von Pushbenachrichtigungen zur App](../../includes/app-service-add-push-notifications-to-app.md)]

## <a id="test"></a>Testen von Pushbenachrichtigungen in einer App

[AZURE.INCLUDE [Testen von Pushbenachrichtigungen in einer App](../../includes/test-push-notifications-in-app.md)]

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

<!-- Images. -->

<!-- URLs. -->
[iOS-Schnellstart]: app-service-mobile-ios-get-started.md
[iOS-Schnellstartprojekt]: app-service-mobile-ios-get-started.md

<!---HONumber=Nov15_HO2-->