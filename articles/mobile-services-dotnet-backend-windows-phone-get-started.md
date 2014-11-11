<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm wird das Hinzufügen eines cloudbasierten Back-End-Diensts zu einer Windows Phone 8-App mit Azure Mobile Services erläutert. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Backend-Version][JavaScript-Backend-Version] dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

> [WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].
> Für dieses Lernprogramm ist [Visual Studio Professional 2013][Visual Studio Professional 2013] erforderlich. Dazu ist eine kostenlose Testversion verfügbar. Zum Erstellen einer neuen Windows Phone 8.1-App müssen Sie über Visual Studio 2013 Update 2 oder eine höhere Version verfügen.

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Erstellen einer neuen Windows Phone-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue Windows Phone 8-App, die mit Ihrem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Registerkarte "Schnellstart" unter **Eine Plattform wählen** auf **Windows Phone 8**, und erweitern Sie **Eine neue Windows Phone 8-App erstellen**.

    ![][1]

    Daraufhin werden die drei einfachen Schritte zum Erstellen einer Windows Phone-App, die mit Ihrem mobilen Dienst verbunden ist, angezeigt.

    ![][2]

3.  Falls noch nicht geschehen, sollten Sie [Visual Studio Professional 2013][Visual Studio Professional 2013] auf den lokalen Computer oder virtuellen Computer herunterladen und installieren.

4.  Klicken Sie unter **Download and publish you service to the cloud** auf **Download**.

    Daraufhin wird eine Lösung heruntergeladen, die Projekte für den mobilen Dienst und für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, enthält. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

5.  Laden Sie unter **Publish your service to the cloud** das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich, wo Sie sie gespeichert haben.

## Testen des mobilen Dienstes auf dem lokalen Computer

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE]Zum Ausführen einer Windows Phone-App, die eine Verbindung zu einem lokalen Dienst herstellt, sind zusätzliche Konfigurationsschritte erforderlich. Informationen dazu finden Sie nicht in diesem Thema, sondern in [How to connect to a local web service from the Windows Phone 8 emulator][How to connect to a local web service from the Windows Phone 8 emulator].

## Veröffentlichen des mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  Öffnen Sie im Windows Phone-App-Projekt die Datei "App.xaml.cs", suchen Sie den Code, durch den eine [MobileServiceClient][MobileServiceClient]-Instanz erstellt wird, kommentieren Sie den Code aus, der diesen Client mit *localhost* erstellt, und heben Sie die Auskommentierung für den Code auf, der den Client mithilfe der mobilen Remotedienst-URL erstellt und in etwa folgendermaßen aussieht:

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    Der Client greift jetzt auf den in Azure veröffentlichten mobilen Dienst zu.

2.  (Optional) Wenn Sie eine Windows Phone 8.1-App erstellen, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, klicken auf **Eigenschaften**, legen **Windows Phone OS-Zielversion** auf **Windows Phone 8.1** fest und klicken dann auf **Ja**, um das Projekt zu aktualisieren.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App sinnvollen Text, wie z. B. *Lernprogramm fertigstellen*, ein, und klicken Sie dann auf **Save**.

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

    <div class="dev-callout"> 
    <b>Hinweis</b> 
    <p>Sie k&ouml;nnen den Code &uuml;berpr&uuml;fen, der auf Ihren Mobile Service zugreift, um Daten abzufragen und einzuf&uuml;gen. Sie finden ihn in der Datei &quot;MainPage.xaml.cs&quot;.</p> 
    </div>

![][3]

Hier wird das Ausführen der neuen Client-App für den mobilen Dienst unter Azure demonstriert. Bevor Sie die Windows Phone-App mit dem mobilen Dienst auf einem lokalen Computer testen können, müssen Sie den Webserver und die Firewall so konfigurieren, dass der Zugriff vom Windows Phone-Gerät oder -Emulator aus zulässig ist. Weitere Informationen finden Sie unter [Configure the local web server to allow connections to a local mobile service][Configure the local web server to allow connections to a local mobile service].

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



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [JavaScript-Backend-Version]: /de-de/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [How to connect to a local web service from the Windows Phone 8 emulator]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [MobileServiceClient]: http://msdn.microsoft.com/de-de/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [Configure the local web server to allow connections to a local mobile service]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Erste Schritte mit der Synchronisierung von Offlinedaten]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [Beheben von Problemen bei einem Mobile Services .NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
