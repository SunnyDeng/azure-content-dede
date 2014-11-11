<properties pageTitle="Get Started with Mobile Services for Xamarin iOS apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="craigd" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Back-End-Dienst zu einer Xamarin.iOS-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert.

Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip unten auswählen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.

Video: "Erste Schritte mit Xamarin und Azure Mobile Services" mit Craig Dunn, Developer Evangelist für Xamarin (Dauer: 10:05 Min.)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Zum Abschließen dieses Lernprogramms sind XCode 4.5 und iOS 5.0 oder neuere Versionen sowie [Xamarin Studio][Xamarin Studio] für OS X oder das Xamarin Visual Studio-Plug-in für Visual Studio unter Windows erforderlich.

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Falls Sie kein Konto besitzen, k&ouml;nnen Sie sich f&uuml;r eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen k&ouml;nnen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">Erstellen einer neuen App</span>Erstellen einer neuen Xamarin.iOS-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue Xamarin.iOS-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Xamarin.iOS**, und erweitern Sie **Create a new Xamarin.iOS app**.

    ![][1]

    Dadurch werden drei einfache Schritte zum Erstellen einer Xamarin.iOS-App angezeigt, die mit dem mobilen Dienst verbunden wird.

    ![][2]

3.  Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Xcode] v4.4 oder eine neuere Version und [Xamarin Studio][Xamarin Studio] herunter, und installieren Sie diese.

4.  Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

5.  Klicken Sie unter **App herunterladen und ausführen** auf **Herunterladen**.

    Dadurch wird das Projekt für die Beispielanwendung der *To-do-Liste* heruntergeladen, die mit dem mobilen Dienst verbunden ist und auf die Azure Mobile Services-Komponente für Xamarin.iOS verweist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

## <span class="short-header">Ausführen der App</span>Ausführen der neuen Xamarin.iOS-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1.  Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Projektmappen-Datei **XamarinTodoQuickStart.iOS.sln** mithilfe von Xamarin Studio oder Visual Studio.

    ![][3]

    ![][4]

2.  Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten, was die Voreinstellung bei diesem Projekt darstellt.

3.  Geben Sie in der App einen sinnvollen Text, wie z. B. *Tutorial fertigstellen* ein, und klicken Sie dann auf das Plus-Symbol (**+**).

    ![][5]

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Sie k&ouml;nnen den Code &uuml;berpr&uuml;fen, der auf den Mobile Service zugreift, um Daten abzufragen und einzuf&uuml;gen. Sie finden ihn in der C#-Datei &quot;TodoService.cs&quot;.</p> 
</div>

4.  Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][6]

    Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

    ![][7]

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



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Xamarin Studio]: http://xamarin.com/download
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
  [5]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
  [6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Erste Schritte mit der Synchronisierung von Offlinedaten]: /de-de/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios
