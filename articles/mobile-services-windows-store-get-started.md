<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store" class="current">Windows Store</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
    <a href="/de-de/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
    <a href="/de-de/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
    <a href="/de-de/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
    <a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title=".NET-Back-End">.NET-Back-End</a> | 
    <a href="/de-de/documentation/articles/mobile-services-windows-store-get-started/"  title="JavaScript-Back-End" class="current">JavaScript-Back-End</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Backend-Dienst zu einer Windows Store-App mithilfe von Azure Mobile Services hinzuf&uuml;gen.</p>
<p>Wenn Sie lieber ein Video zu diesem Thema ansehen m&ouml;chten, k&ouml;nnen Sie den Clip auf der rechten Seite ausw&auml;hlen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm. Im Video gibt Scott Guthrie eine Einf&uuml;hrung in Mobile Services und f&uuml;hrt Sie durch das Erstellen Ihres ersten mobilen Dienstes und das Verbinden dieses Diensts &uuml;ber eine Windows Store-App.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">10:08:00</span></div>

</div>

In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen zu erstellende mobile Dienst verwendet JavaScript für die serverseitige Geschäftslogik. Informationen zum Erstellen eines mobilen Diensts, mit dem Sie serverseitige Geschäftslogik in den unterstützten .NET-Sprachen mithilfe von Visual Studio verfassen können, finden Sie in der .NET-Back-End-Version dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Services-Lernprogramme für Windows Store-Apps.

> [WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

> Dieses Lernprogramm erfordert Visual Studio 2013. Um eine Windows Store-App mithilfe von Visual Studio 2012 zu verbinden, folgen Sie den Schritten im Thema [Erste Schritte mit Daten in Mobile Services mithilfe von Visual Studio 2012][Erste Schritte mit Daten in Mobile Services mithilfe von Visual Studio 2012].

## Erstellen eines neuen mobilen Dienstes

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Erstellen einer neuen Windows Store-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue Windows Store-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Windows**. Erweitern Sie dann **Neue Windows Store-App erstellen**.

    ![][1]

    Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Windows Store-App, die mit dem mobilen Dienst verbunden ist, angezeigt.

    ![][2]

3.  Falls noch nicht geschehen, sollten Sie [Visual Studio 2013 Express für Windows][Visual Studio 2013 Express für Windows] auf den lokalen Computer oder virtuellen Computer herunterladen und installieren.

4.  Klicken Sie auf **TodoItem-Tabelle erstellen**, um eine Tabelle zum Speichern von App-Daten zu erstellen.

5.  Wählen Sie unter **App herunterladen und ausführen** eine Sprache für die App aus. Klicken Sie dann auf **Herunterladen**.

    Das Projekt für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, wird heruntergeladen. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Ausführen der Windows-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1.  Wechseln Sie zu dem Speicherort, an dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Lösungsdatei in Visual Studio 2013 Express für Windows.

2.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

3.  Geben Sie in der App einen beschreibenden Text ein, zum Beispiel *Lernprogramm abschließen*, unter **TodoItem einfügen**. Klicken Sie dann auf **Speichern**.

    ![][3]

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

    > [WACOM.NOTE]Sie können den Code überprüfen, der auf den mobilen Dienst zugreift, um Daten abzufragen und einzufügen, die in der Datei "MainPage.xaml.cs" (C#/XAML-Projekt) oder "default.js" (JavaScript/HTML-Projekt) vorhanden sind.

4.  Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][4]

    Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

    ![][5]

## Nächste Schritte

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   **Erste Schritte mit Daten** ([C#][C#]/[JavaScript][JavaScript])
    Erhalten Sie weitere Informationen zum Speichern und Abfragen von Daten mithilfe von Mobile Services.

-   **Erste Schritte mit der Authentifizierung** ([C#][6]/[JavaScript][7])
    Erhalten Sie Informationen zum Authentifizieren von Benutzern der App mit einem Identitätsanbieter.

-   **Erste Schritte mit Pushbenachrichtigungen** ([C#][8]/[JavaScript][9])
    Erhalten Sie Informationen zum Senden einer sehr grundlegenden Pushbenachrichtigung an die App.



  [0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Erste Schritte mit Daten in Mobile Services mithilfe von Visual Studio 2012]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
  [2]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
  [Visual Studio 2013 Express für Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [3]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
  [4]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png
  [C#]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet
  [JavaScript]: /de-de/develop/mobile/tutorials/get-started-with-data-js
  [6]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
  [7]: /de-de/develop/mobile/tutorials/get-started-with-users-js
  [8]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet
  [9]: /de-de/develop/mobile/tutorials/get-started-with-push-js
