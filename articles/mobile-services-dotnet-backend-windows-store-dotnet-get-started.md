<properties linkid="mobile-services-dotnet-backend-windows-store-dotnet-get-started" pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/18/2014" ms.author="glenga" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Backend-Dienst mithilfe von Azure Mobile Services zu einer universellen Windows-App hinzufügen. Universelle Windows-App-Lösungen beinhalten Projekte für Windows Store 8.1, Windows Phone Store 8.1-Apps und ein gemeinsames, geteiltes Projekt. Weitere Informationen finden Sie unter [Erstellen universeller Windows-Apps, die Windows und Windows Phone als Ziel verwenden](http://msdn.microsoft.com/de-de/library/windows/apps/xaml/dn609832.aspx).

In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Back-End-Version dieses Themas.

> [WACOM.NOTE]In diesem Thema wird das Erstellen eines neuen mobilen Dienstprojekts und einer universellen Windows-App mithilfe des Azure-Verwaltungsportals erläutert. Unter Verwendung von Visual Studio 2013 Update 3 können Sie außerdem neue mobile Dienstprojekte zu einer vorhandenen Visual Studio-Lösung hinzufügen. Weitere Informationen finden Sie unter [Schnellstart: Hinzufügen eines mobilen Diensts (.NET-Back-End)](http://msdn.microsoft.com/de-de/library/windows/apps/dn629482.aspx).

> Informationen zum Hinzufügen eines mobilen Diensts zu einem Windows Phone 8.0- oder Windows Phone Silverlight 8.1-App-Projekt finden Sie unter [Erste Schritte mit Daten für Windows Phone](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Für dieses Lernprogramm benötigen Sie Folgendes:

-   Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
-   <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Dazu ist eine kostenlose Testversion verfügbar.

## Erstellen eines neuen mobilen Dienstes

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)

## Erstellen einer neuen universellen Windows-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue universelle Windows-App, die sich mit dem mobilen Dienst verbindet.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Windows**. Erweitern Sie dann **Neue Windows Store-App erstellen**.

    ![][6]

    Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Windows Store-App, die mit dem mobilen Dienst verbunden ist, angezeigt.

    ![][7]

3.  Falls noch nicht geschehen, sollten Sie <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>  auf den lokalen Computer oder virtuellen Computer herunterladen und installieren.

4.  Wählen Sie unter **Download and run your app and service locally** eine Sprache für die Windows Store-App aus. Klicken Sie dann auf **Herunterladen**.

    Daraufhin wird eine Lösung heruntergeladen, die Projekte für den mobilen Dienst und für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, enthält. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Testen der App mit dem lokalen mobilen Dienst

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

> [WACOM.NOTE]Sie können den Code überprüfen, der auf Ihren mobilen Dienst zum Abfragen und Einfügen von Daten zugreift. Der Code befindet sich in der Datei "MainPage.xaml.cs".

## Veröffentlichen des mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="1">
<li><p>Öffnen Sie im Projekt mit freigegebenem Code die Datei "App.xaml.cs", suchen Sie den Code, durch den eine <a href="http://msdn.microsoft.com/de-de/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>-Instanz erstellt wird, kommentieren Sie den Code aus, der diesen Client mit <em>localhost</em> erstellt, und heben Sie die Auskommentierung für den Code auf, der den Client mithilfe der URL des mobilen Remotediensts erstellt und etwa folgendermaßen aussieht:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

	<p>Der Client greift jetzt auf den in Azure veröffentlichten mobilen Dienst zu.</p></li>
</ol>

## Testen der App mit dem in Azure gehosteten mobilen Dienst

Nachdem der mobile Dienst veröffentlicht wurde und der Client mit dem in Azure gehosteten mobilen Remotedienst verbunden ist, kann die App ausgeführt und Azure zum Speichern von Elementen verwendet werden.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

## Nächste Schritte

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   [Erste Schritte mit Daten]
    <br/>Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Synchronisierung von Offlinedaten][Erste Schritte mit der Synchronisierung von Offlinedaten]
    <br/>Erfahren Sie, wie Sie die Offlinedatensynchronisierung nutzen, um eine reaktionsfähige, stabile App zu implementieren.

-   [Erste Schritte mit der Authentifizierung]
    <br/>Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

-   [Erste Schritte mit Pushbenachrichtigungen]
    <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Beheben von Problemen bei einem Mobile Services .NET-Back-End]
    <br/> Erfahren Sie, wie Sie Probleme diagnostizieren und beheben, die in Zusammenhang mit einem Mobile Services .NET-Back-End auftreten.

Weitere Informationen zu universellen Windows-Apps finden Sie unter [Unterstützen mehrerer Geräteplattformen aus einem einzigen Mobile Service heraus](/de-de/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Erstellen universeller Windows-Apps, die Windows und Windows Phone als Ziel verwenden]: http://msdn.microsoft.com/de-de/library/windows/apps/xaml/dn609832.aspx
  [Erste Schritte mit Daten für Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [mobile-services-windows-universal-get-started]: ../includes/mobile-services-windows-universal-get-started.md
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
  [7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
  [mobile-services-dotnet-backend-test-local-service-dotnet]: ../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Erste Schritte mit der Synchronisierung von Offlinedaten]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Beheben von Problemen bei einem Mobile Services .NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
  [Unterstützen mehrerer Geräteplattformen aus einem einzigen Mobile Service heraus]: /de-de/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
