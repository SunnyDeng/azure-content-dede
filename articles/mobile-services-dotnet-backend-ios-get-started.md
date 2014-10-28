<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer iOS-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Backend-Version][JavaScript-Backend-Version] dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][]

Zum Abschließen dieses Lernprogramms sind XCode 4.5 und iOS 5.0 oder neuere Versionen erforderlich.

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Falls Sie kein Konto besitzen, k&ouml;nnen Sie sich f&uuml;r eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen k&ouml;nnen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Herunterladen des mobilen Dienstes auf den lokalen Computer

Da Sie den mobilen Dienst jetzt erstellt haben, laden Sie das Projekt für den personalisierten mobilen Dienst herunter, den Sie auf dem lokalen oder dem virtuellen Computer ausführen können.

1.  Klicken Sie auf den gerade erstellten mobilen Dienst, und klicken Sie dann auf der Schnellstartregisterkarte auf **iOS** unter **Plattform auswählen**. Erweitern Sie dann **Erstellen einer neuen iOS-App**.

    ![][1]

2.  Wenn Sie dies noch nicht durchgeführt haben, laden Sie Visual Studio Professional 2013 oder eine neuere Version herunter, und installieren Sie diese.

3.  Klicken Sie auf **Download** unter **Download and publish your service to the cloud**.

    Dadurch wird das Visual Studio-Projekt, das den mobilen Dienst implementiert, heruntergeladen. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

4.  Laden Sie außerdem das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen Dienstes

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## Veröffentlichen des mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Erstellen einer neuen iOS-App

In diesem Abschnitt erstellen Sie eine neue iOS-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Registerkarte "Quickstart" auf **iOS** unter der **Auswahlplattform**, und erweitern Sie die Option **Erstellen einer neuen iOS-App**.

3.  Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Xcode][Xcode] v4.4 oder eine neuere Version herunter, und installieren Sie diese.

4.  Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

    Dadurch wird das Projekt für die Beispielanwendung der *To-do-List* heruntergeladen, die zusammen mit dem Mobile Services iOS SDK mit dem mobilen Dienst verbunden ist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

## Ausführen der neuen iOS-App

[WACOM.INCLUDE [mobile-services-ios-run-app][mobile-services-ios-run-app]]

Hier wird das Ausführen der neuen Client-App für den mobilen Dienst unter Azure demonstriert. Bevor Sie die iOS-App mit dem mobilen Dienst auf einem lokalen Computer testen können, müssen Sie den Webserver und die Firewall so konfigurieren, dass der Zugriff vom iOS-Entwicklungscomputer aus zulässig ist. Weitere Informationen finden Sie unter [Configure the local web server to allow connections to a local mobile service][Configure the local web server to allow connections to a local mobile service].

## <a name="next-steps"> </a>Nächste Schritte

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Synchronisierung von Offlinedaten][Erste Schritte mit der Synchronisierung von Offlinedaten]
    Erfahren Sie, wie Sie die Offlinedatensynchronisierung nutzen, um eine reaktionsfähige, stabile App zu implementieren.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Beheben von Problemen bei einem Mobile Services .NET-Back-End][Beheben von Problemen bei einem Mobile Services .NET-Back-End]
     Erfahren Sie, wie Sie Probleme diagnostizieren und beheben, die in Zusammenhang mit einem Mobile Services .NET-Back-End auftreten.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [JavaScript-Backend-Version]: /de-de/documentation/articles/mobile-services-ios-get-started
  []: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [mobile-services-ios-run-app]: ../includes/mobile-services-ios-run-app.md
  [Configure the local web server to allow connections to a local mobile service]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Erste Schritte mit der Synchronisierung von Offlinedaten]: /de-de/documentation/articles/mobile-services-ios-get-started-offline-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [Beheben von Problemen bei einem Mobile Services .NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
