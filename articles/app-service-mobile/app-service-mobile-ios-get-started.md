<properties
	pageTitle="Erstellen einer iOS-App für mobile Azure App Service-Apps | Microsoft Azure"
	description="Befolgen Sie dieses Tutorial für die ersten Schritte bei der Verwendung mobiler Azure-App-Back-Ends für die iOS-Entwicklung in Objective-C oder Swift."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="11/30/2015"
	ms.author="krisragh"/>

#Erstellen einer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Übersicht

Dieses Tutorial zeigt, wie Sie einen cloudbasierten Back-End-Dienst ([Azure Mobile Apps](app-service-mobile-value-prop.md)) einer mobilen iOS-App hinzufügen. Sie erstellen ein neues mobiles Back-End und verwenden eine einfache _Todo list-iOS_-App, die App-Daten in Azure speichert.

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein [aktives Azure-Konto](https://azure.microsoft.com/pricing/free-trial/)

* Einen PC mit [Visual Studio Community 2013] oder höher

* Einen Mac mit Xcode, Version 7.0 oder höher

* [Azure Mobile iOS-Framework](https://go.microsoft.com/fwLink/?LinkID=529823), das automatisch als Teil des heruntergeladenen Schnellstartprojekts enthalten ist

## Erstellen eines neuen mobilen Azure-App-Back-Ends

Führen Sie die folgenden Schritte aus, um ein neues mobiles App-Back-End zu erstellen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Sie haben nun ein Azure-Back-End für mobile Apps bereitgestellt, das von Ihren mobilen Clientanwendungen verwendet werden kann. Als Nächstes laden Sie ein Serverprojekt für ein einfaches "Aufgabenlisten"-Back-End herunter und veröffentlichen es in Azure.

## Herunterladen des Serverprojekts

1. Besuchen Sie auf dem PC das [Azure-Portal]. Klicken Sie auf **Alle durchsuchen** > **Mobile Apps**, und klicken Sie dann auf das mobile App-Back-End, das Sie gerade erstellt haben.

2. Klicken Sie auf dem Blatt "Mobile App" auf **Einstellungen**, und klicken Sie dann unter **Mobile App** auf **Schnellstart** > **iOS (Objective-C)**. Wenn Sie Swift bevorzugen, klicken Sie stattdessen auf **Schnellstart** > **iOS (Swift)**.

## Konfigurieren des Serverprojekts

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]


## Herunterladen und Ausführen der iOS-App

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure-Portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0128_2016-->