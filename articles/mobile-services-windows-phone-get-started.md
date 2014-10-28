<properties pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Lernprogramm wird das Hinzuf&uuml;gen eines cloudbasierten Back-End-Diensts zu einer Windows Phone 8-App mit Azure Mobile Services erl&auml;utert. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache <em>To-Do-Listen</em>-App, die App-Daten im neuen mobilen Dienst speichert.</p>
<p>Wenn Sie lieber ein Video zu diesem Thema ansehen m&ouml;chten, k&ouml;nnen Sie den Clip auf der rechten Seite ausw&auml;hlen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm. Nick Harris gibt in diesem Video eine Einf&uuml;hrung in Mobile Services und erkl&auml;rt, wie Sie Ihren ersten mobilen Dienst erstellen und ihn mit einer Windows Store-App verbinden k&ouml;nnen.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">13:18:00</span></div>

</div>

In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen zu erstellende mobile Dienst verwendet JavaScript für die serverseitige Geschäftslogik. Informationen zum Erstellen eines mobile Diensts, dessen serverseitige Geschäftslogik Sie in den unterstützen .NET-Sprachen mit Visual Studio schreiben können, finden Sie in der [.NET-Back-End-Version][.NET-Back-End-Version] dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][]

> [WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion]. Zum Erstellen einer neuen Windows Phone 8.1-App müssen Sie über Visual Studio 2013 Update 2 oder eine höhere Version verfügen.

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## 

## <span class="short-header">Erstellen einer neuen App</span>Erstellen einer neuen Windows Phone-App

</h2>
Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue Windows Phone 8-App, die mit Ihrem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Registerkarte "Schnellstart" unter **Eine Plattform wählen** auf **Windows Phone 8**, und erweitern Sie **Eine neue Windows Phone 8-App erstellen**.

    ![][1]

    Daraufhin werden die drei einfachen Schritte zum Erstellen einer Windows Phone-App, die mit Ihrem mobilen Dienst verbunden ist, angezeigt.

    ![][2]

3.  Laden Sie [Visual Studio 2012 Express für Windows Phone][Visual Studio 2012 Express für Windows Phone] herunter, und installieren Sie es auf Ihrem lokalen Computer, falls noch nicht geschehen.

    > [WACOM.NOTE]Zum Erstellen einer Windows Phone 8.1-App müssen Sie über eine Installation von Visual Studio 2013 Update 2 verfügen.

4.  Klicken Sie auf **TodoItem-Tabelle erstellen**, um eine Tabelle zum Speichern von App-Daten zu erstellen.

5.  Klicken Sie unter **App herunterladen und ausführen** auf **Herunterladen**.

    Das Projekt für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, wird heruntergeladen. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Ausführen Ihrer Windows Phone-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1.  Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Projektmappen-Datei in Visual Studio.

2.  (Optional) Wenn Sie eine Windows Phone 8.1-App erstellen, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, klicken auf **Eigenschaften**, legen **Windows Phone OS-Zielversion** auf **Windows Phone 8.1** fest und klicken dann auf **Ja**, um das Projekt zu aktualisieren.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App sinnvollen Text, wie z. B. *Tutorial fertigstellen* ein, und klicken Sie dann auf **Save**.

    ![][3]

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

    > [WACOM.NOTE]Sie können den Code überprüfen, der auf Ihren mobilen Dienst zum Abfragen und Einfügen von Daten zugreift. Der Code befindet sich in der Datei "MainPage.xaml.cs".

5.  Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][4]

    Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

    ![][5]

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
  [Lernprogramm ansehen]: http://go.microsoft.com/fwlink/?LinkId=290816
  [.NET-Back-End-Version]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  []: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFde-de%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Visual Studio 2012 Express für Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [3]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-data/
  [Erste Schritte mit der Synchronisierung von Offlinedaten]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-users/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
