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
	ms.date="10/28/2015"
	ms.author="krisragh"/>

#Erstellen einer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Übersicht

Dieses Tutorial zeigt Ihnen, wie Sie einen cloudbasierten Back-End-Dienst mithilfe eines mobilen Azure-App-Back-Ends zu einer mobilen iOS-App hinzufügen. Sie erstellen sowohl ein neues mobiles App-Back-End als auch eine einfache _Todo list_-iOS-App, die App-Daten in Azure speichert.

Das Absolvieren dieses Tutorials ist Voraussetzung für alle anderen iOS-Tutorials zur Verwendung des Features "Mobile Apps" von Azure App Service.

## Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein [aktives Azure-Konto](http://azure.microsoft.com/pricing/free-trial/)

* Einen PC mit [Visual Studio Community 2013] oder höher

* Einen Mac mit Xcode, Version 7.0 oder höher

* [Azure Mobile iOS-Framework](https://go.microsoft.com/fwLink/?LinkID=529823), das automatisch als Teil des heruntergeladenen Schnellstartprojekts enthalten ist

## Erstellen eines neuen mobilen Azure-App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Herunterladen des Serverprojekts

1. Besuchen Sie auf dem PC das [Azure-Portal]. Klicken Sie auf **Alle durchsuchen** > **Mobile Apps**, und klicken Sie dann auf das mobile App-Back-End, das Sie gerade erstellt haben.

2. Klicken Sie auf dem Blatt "Mobile App" auf **Einstellungen**, und klicken Sie dann unter **Mobile App** auf **Schnellstart** > **iOS (Objective-C)**. Wenn Sie Swift bevorzugen, klicken Sie stattdessen auf **Schnellstart** > **iOS (Swift)**.

3. Klicken Sie unter **Serverprojekt herunterladen und ausführen** auf **Herunterladen**. Extrahieren Sie die komprimierten Projektdateien auf Ihrem PC, und öffnen Sie die Projektmappe in Visual Studio.

## Veröffentlichen des Serverprojekts in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## Herunterladen und Ausführen der iOS-App

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure-Portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Nov15_HO2-->