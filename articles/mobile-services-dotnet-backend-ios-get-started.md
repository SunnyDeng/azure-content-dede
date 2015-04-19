<properties 
	pageTitle="Erste Schritte mit Azure Mobile Services für iOS-Apps" 
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Services für die iOS-Entwicklung." 
	services="" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Back-End-Dienst zu einer iOS-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache To-Do-Listen-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Back-End-Version] dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Zum Abschließen dieses Lernprogramms sind XCode 4.5 und iOS 5.0 oder neuere Versionen erforderlich.

> [AZURE.NOTE] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20target="_blank").

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Herunterladen des mobilen Dienstes auf den lokalen Computer

Da Sie den mobilen Dienst jetzt erstellt haben, laden Sie das Projekt für den personalisierten mobilen Dienst herunter, den Sie auf dem lokalen oder dem virtuellen Computer ausführen können.

1. Klicken Sie auf den gerade erstellten mobilen Dienst, und klicken Sie dann auf der Schnellstartregisterkarte auf **iOS** unter **Plattform** auswählen. Erweitern Sie dann **Erstellen einer neuen iOS-App**.

	![][1]

2. Wenn Sie dies noch nicht durchgeführt haben, laden Sie Visual Studio Professional 2013 oder eine neuere Version herunter, und installieren Sie diese.

3. Klicken Sie auf **Herunterladen** unter **Service in der Cloud herunterladen und veröffentlichen**.

	Dadurch wird das Visual Studio-Projekt, das den mobilen Dienst implementiert, heruntergeladen. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

4. Laden Sie außerdem das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen Dienstes

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Veröffentlichen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Erstellen einer neuen iOS-App

In diesem Abschnitt erstellen Sie eine neue iOS-App, die mit dem mobilen Dienst verbunden ist.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf der Registerkarte "Quickstart" unter **Plattform auswählen** auf **iOS**, und erweitern Sie die Option **Erstellen einer neuen iOS-App**.

3. Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Xcode] v4.4 oder eine neuere Version herunter, und installieren Sie diese.

4. Klicken Sie unter **App herunterladen und ausführen** auf **Herunterladen**.

  	Dadurch wird das Projekt für die Beispielanwendung der _To do list_ heruntergeladen, die zusammen mit dem Mobile Services iOS SDK mit dem mobilen Dienst verbunden ist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

## Ausführen der neuen iOS-App

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Hier wird das Ausführen der neuen Client-App für den mobilen Dienst unter Azure demonstriert. Bevor Sie die iOS-App mit dem mobilen Dienst auf einem lokalen Computer testen können, müssen Sie den Webserver und die Firewall so konfigurieren, dass der Zugriff vom iOS-Entwicklungscomputer aus zulässig ist. Weitere Informationen finden Sie unter [Konfigurieren des lokalen Webservers für Verbindungen mit einem lokalen mobilen Dienst](mobile-services-dotnet-backend-how-to-configure-iis-express.md)..

## <a name="next-steps"> </a>Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Offline-Datensynchronisierung]
  <br/>Erfahren Sie, wie Sie mit der Synchronisierung von Offlinedaten eine reaktionsfähige, stabile App implementieren können.

* [Erste Schritte mit der Authentifizierung]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen]
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
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Erste Schritte mit der Offline-Datensynchronisierung]: /de-de/documentation/articles/mobile-services-ios-get-started-offline-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
[Problembehandlung beim Mobile Services .NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Verwaltungsportal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript-Back-End-Version]: /de-de/documentation/articles/mobile-services-ios-get-started


<!--HONumber=42-->
