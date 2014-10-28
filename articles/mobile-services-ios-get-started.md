<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="hero-article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer iOS-App hinzuf&uuml;gen k&ouml;nnen.</p>
<p>Wenn Sie lieber ein Video zu diesem Thema ansehen m&ouml;chten, k&ouml;nnen Sie den Clip auf der rechten Seite ausw&auml;hlen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">09:38:00</span></div>

</div>

In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen zu erstellende mobile Dienst verwendet JavaScript für die serverseitige Geschäftslogik. Informationen zum Erstellen eines mobile Diensts, dessen serverseitige Geschäftslogik Sie in den unterstützen .NET-Sprachen mit Visual Studio schreiben können, finden Sie in der [.NET-Back-End-Version][.NET-Back-End-Version] dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][]

Zum Abschließen dieses Lernprogramms sind XCode 4.5 und iOS 5.0 oder neuere Versionen erforderlich.

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Falls Sie kein Konto besitzen, k&ouml;nnen Sie sich f&uuml;r eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen k&ouml;nnen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## <span class="short-header">Erstellen einer neuen App</span>Erstellen einer neuen iOS-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue iOS-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Registerkarte "Quickstart" auf **iOS** unter der **Auswahlplattform**, und erweitern Sie die Option **Erstellen einer neuen iOS-App**.

    ![][1]

    Dadurch werden drei einfache Schritte zum Erstellen einer iOS-App angezeigt, die mit dem mobilen Dienst verbunden wird.

    ![][2]

3.  Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Xcode][Xcode] v4.4 oder eine neuere Version herunter, und installieren Sie diese.

4.  Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

5.  Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

    Dadurch wird das Projekt für die Beispielanwendung der *To-do-List* heruntergeladen, die zusammen mit dem Mobile Services iOS SDK mit dem mobilen Dienst verbunden ist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

## Ausführen der neuen iOS-App

[WACOM.INCLUDE [mobile-services-ios-run-app][mobile-services-ios-run-app]]

1.  

    Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    <p>

</p>
![][3]

Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

</p>
![][4]

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

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Lernprogramm ansehen]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart
  [.NET-Back-End-Version]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  []: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
  [2]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [mobile-services-ios-run-app]: ../includes/mobile-services-ios-run-app.md
  [3]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
  [4]: ./media/mobile-services-ios-get-started/mobile-data-browse.png
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-ios-get-started-data/
  [Erste Schritte mit der Synchronisierung von Offlinedaten]: /de-de/documentation/articles/mobile-services-ios-get-started-offline-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-ios-get-started-users/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
