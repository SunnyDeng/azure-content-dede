<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Backend-Dienst mithilfe von Azure Mobile Services zu einer universellen Windows-App hinzufügen.

In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen zu erstellende mobile Dienst verwendet JavaScript für die serverseitige Geschäftslogik. Informationen zum Erstellen eines mobilen Diensts, mit dem Sie serverseitige Geschäftslogik in den unterstützten .NET-Sprachen mithilfe von Visual Studio verfassen können, finden Sie in der .NET-Back-End-Version dieses Themas.

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Für dieses Lernprogramm benötigen Sie Folgendes:

-   Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].
-   [Visual Studio 2013 Express für Windows][Visual Studio 2013 Express für Windows]

## Erstellen eines neuen mobilen Dienstes

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Erstellen einer neuen universellen Windows-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue universelle Windows-App zu erstellen oder eine vorhandene Windows Store- oder Windows Phone-App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue universelle Windows-App, die sich mit dem mobilen Dienst verbindet.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Windows**. Erweitern Sie dann **Neue Windows Store-App erstellen**.

   	![][0]

   	Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Windows Store-App, die mit dem mobilen Dienst verbunden ist, angezeigt.

   	![][1]

3.  Laden Sie [Visual Studio 2013][Visual Studio 2013 Express für Windows] auf Ihren lokalen Computer oder virtuellen Computer herunter und installieren Sie es, falls noch nicht geschehen.

4.  Klicken Sie auf **TodoItem-Tabelle erstellen**, um eine Tabelle zum Speichern von App-Daten zu erstellen.

5.  Wählen Sie unter **App herunterladen und ausführen** eine Sprache für die App aus. Klicken Sie dann auf **Herunterladen**.

   	Das Projekt für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, wird heruntergeladen. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Ausführen der Windows-App

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

> [WACOM.NOTE]Sie können den Code überprüfen, der auf Ihren mobilen Dienst zum Abfragen und Einfügen von Daten zugreift. Der Code befindet sich in der Datei "default.js".

## Nächste Schritte

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

Weitere Informationen zu universellen Windows-Apps finden Sie unter [Unterstützen mehrerer Geräteplattformen aus einem einzigen Mobile Service heraus][Unterstützen mehrerer Geräteplattformen aus einem einzigen Mobile Service heraus].

<!-- Anchors. -->  

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [mobile-services-windows-universal-get-started]: ../includes/mobile-services-windows-universal-get-started.md
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio 2013 Express für Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
<!-- Images. -->
  [0]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
<!-- URLs. -->
  [mobile-services-javascript-backend-run-app]: ../includes/mobile-services-javascript-backend-run-app.md
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push
  [Unterstützen mehrerer Geräteplattformen aus einem einzigen Mobile Service heraus]: /de-de/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
