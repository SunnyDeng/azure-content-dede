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
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/11/2015"
	ms.author="krisragh"/>

#Erstellen einer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Übersicht

Dieses Tutorial zeigt Ihnen, wie Sie einen cloudbasierten Back-End-Dienst mithilfe eines mobilen Azure-App-Back-Ends zu einer mobilen iOS-App hinzufügen. Sie erstellen sowohl ein neues mobiles App-Back-End als auch eine einfache _Todo list_-iOS-App, die App-Daten in Azure speichert.

Das Absolvieren dieses Tutorials ist Voraussetzung für alle anderen iOS-Tutorials zur Verwendung des Features "Mobile Apps" von Azure App Service.

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] oder höher.

* Ein Mac, auf dem Xcode Version 7. 0 oder höher installiert ist.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile). Dort können Sie direkt eine kurzzeitige mobile Start-App in App Service erstellen – keine Kreditkarte erforderlich, keine weiteren Verpflichtungen.

## Erstellen eines neuen mobilen Azure-App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Herunterladen des Serverprojekts

1. Besuchen Sie auf dem PC das [Azure-Portal]. Klicken Sie auf **Alle durchsuchen** > **Mobile Apps**, und klicken Sie dann auf das mobile App-Back-End, das Sie gerade erstellt haben.

2. Klicken Sie auf dem Blatt "Mobile App" auf **Einstellungen**, und klicken Sie dann unter **Mobile App** auf **Schnellstart** > **iOS (Objective-C)**. Wenn Sie Swift bevorzugen, klicken Sie stattdessen auf **Schnellstart** > **iOS (Swift)**.

3. Klicken Sie unter **Herunterladen und Serverprojekt ausführen** auf **Herunterladen**. Extrahieren Sie die komprimierten Projektdateien auf Ihrem PC, und öffnen Sie die Projektmappe in Visual Studio.

## Veröffentlichen des Serverprojekts in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Herunterladen und Ausführen der iOS-App

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Azure-Portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=September15_HO1-->