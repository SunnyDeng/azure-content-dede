<properties
	pageTitle="Erste Schritte mit Azure Mobile Services für iOS-Apps"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Services für die iOS-Entwicklung."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="2/11/2015"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Back-End-Dienst zu einer iOS-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache To-Do-Listen-App, die App-Daten im neuen mobilen Dienst speichert. Der mobile Dienst verwendet .NET und Visual Studio für die serverseitige Logik. Informationen zum Erstellen eines mobilen Diensts mit serverseitiger Geschäftslogik in JavaScript finden Sie in der [JavaScript-Back-End-Version] dieses Themas.

> [AZURE.NOTE] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie [kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können](http://azure.microsoft.com/pricing/details/mobile-services/).  Einzelheiten finden Sie unter [1 Monat kostenlose Testversion](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20target="_blank").

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Herunterladen des mobilen Diensts und der App auf den lokalen Computer

Nachdem Sie den mobilen Dienst erstellt haben, laden Sie Projekte herunter, die Sie lokal ausführen können.

1. Klicken Sie auf den gerade erstellten mobilen Dienst, und klicken Sie dann auf der Schnellstartregisterkarte auf **iOS** unter **Plattform** auswählen. Erweitern Sie dann **Erstellen einer neuen iOS-App**.

2. Klicken Sie auf Ihrem Windows-Computer unter **Herunterladen und Veröffentlichen des Diensts in der Cloud** auf **Download**. Dadurch wird das Visual Studio-Projekt, das den mobilen Dienst implementiert, heruntergeladen. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

3. Klicken Sie auf Ihrem Mac unter **App herunterladen und ausführen** auf **Herunterladen**. Dadurch wird das Projekt für die Beispielanwendung der To-do-List heruntergeladen, die zusammen mit dem Mobile Services iOS SDK mit dem mobilen Dienst verbunden ist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Veröffentlichen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


## Ausführen der neuen iOS-App

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]


## <a name="next-steps"> </a>Nächste Schritte

Hier wird das Ausführen der neuen Client-App für den mobilen Dienst unter Azure demonstriert. Bevor Sie die iOS-App mit dem mobilen Dienst auf einem lokalen Computer testen können, müssen Sie den Webserver und die Firewall so konfigurieren, dass der Zugriff vom iOS-Entwicklungscomputer aus zulässig ist. Weitere Informationen finden Sie unter [Konfigurieren des lokalen Webservers für Verbindungen mit einem lokalen mobilen Dienst](mobile-services-dotnet-backend-how-to-configure-iis-express.md).

Erfahren Sie, wie Sie weitere wichtige Aufgaben in Mobile Services durchführen:

* [Hinzufügen von Mobile Services zu einer vorhandenen App]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Offline-Datensynchronisierung]
  <br/>Erfahren Sie, wie Sie mit der Synchronisierung von Offlinedaten eine reaktionsfähige, stabile App implementieren können.

* [Hinzufügen von Authentifizierung zu einer vorhandenen App]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Hinzufügen von Pushbenachrichtigungen zu einer vorhandenen App]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Problembehandlung beim Mobile Services .NET-Back-End]
  <br/> Erfahren Sie mehr zur Diagnose und zum Beheben von Problemen, die mit einem Mobile Services .NET-Back-End auftreten können.

<!-- Anchors. -->
[Erste Schritte mit Mobile Services]:#getting-started
[Erstellen eines neuen mobilen Diensts]:#create-new-service
[Definieren der mobilen Dienstinstanz]:#define-mobile-service-instance
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Hinzufügen von Mobile Services zu einer vorhandenen App]: mobile-services-dotnet-backend-ios-get-started-data.md
[Erste Schritte mit der Offline-Datensynchronisierung]: mobile-services-ios-get-started-offline-data.md
[Hinzufügen von Authentifizierung zu einer vorhandenen App]: mobile-services-dotnet-backend-ios-get-started-users.md
[Hinzufügen von Pushbenachrichtigungen zu einer vorhandenen App]: mobile-services-dotnet-backend-ios-get-started-push.md
[Problembehandlung beim Mobile Services .NET-Back-End]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Verwaltungsportal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript-Back-End-Version]: mobile-services-ios-get-started.md


<!--HONumber=52--> 