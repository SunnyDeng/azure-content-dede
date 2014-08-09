<properties linkid="develop-mobile-tutorials-get-started" urlDisplayName="Get Started" pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Mobile Services
==================================

[Windows Store C\#](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started "Windows Store C#")[Windows Store JavaScript](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started "Windows Store JavaScript")[Windows Phone](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started "Windows Phone")[iOS](/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started "iOS") [Android](/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started "Android")
[.NET-Backend](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/ ".NET-Backend") | [JavaScript-Backend](/de-de/documentation/articles/mobile-services-windows-store-get-started/ "JavaScript-Backend")

Dieses Lernprogramm zeigt Ihnen, wie Sie einen cloudbasierten Backend-Dienst zu einer Windows Store-App mithilfe von Azure Mobile Services hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App in HTML und JavaScript, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Version](/de-de/documentation/articles/mobile-services-windows-store-get-started) dieses Themas.

Unten finden Sie einen Screenshot aus der fertigen App:

![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png)

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Services-Lernprogramme für Windows Store-Apps.

> [WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen erhalten Sie unter [Kostenloses Azure-Testkonto](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started%2F).
> Für dieses Lernprogramm ist [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) erforderlich. Dazu ist eine kostenlose Testversion verfügbar.

Erstellen eines neuen mobilen Diensts
-------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

Erstellen einer neuen Windows Store-App
---------------------------------------

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue Windows Store-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Windows**. Erweitern Sie dann **Neue Windows Store-App erstellen**.

      ![][6]

      Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Windows Store-App, die mit dem mobilen Dienst verbunden ist, angezeigt.

      ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png)

1.  Falls noch nicht geschehen, sollten Sie [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) auf den lokalen Computer oder virtuellen Computer herunterladen und installieren.

2.  Wählen Sie unter **Download and run your app and service locally** eine Sprache für die Windows Store-App aus. Klicken Sie dann auf **Herunterladen**.

    Daraufhin wird eine Lösung heruntergeladen, die Projekte für den mobilen Dienst und für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, enthält. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

1.  Klicken Sie unter **Publish your service to the cloud** zum Herunterladen des Veröffentlichungsprofils. Speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und merken Sie sich, wo Sie sie gespeichert haben.

    **Sicherheitshinweis**

    Nach dem Importieren des Veröffentlichungsprofils sollten Sie die heruntergeladene Datei löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihre Dienste verwendet werden können.

Testen der App mit dem lokalen mobilen Dienst
---------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

> [WACOM.NOTE]Sie können den Code überprüfen, der auf Ihren mobilen Dienst zum Abfragen und Einfügen von Daten zugreift. Der Code befindet sich in der Datei "default.js".

Veröffentlichen des mobilen Diensts
-----------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  Öffnen Sie im Windows Store-App-Projekt die Datei "App.xaml.cs", suchen Sie den Code, der eine [MobileServiceClient](http://msdn.microsoft.com/de-de/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx)-Instanz erstellt, kommentieren Sie den Code aus, der diesen Client mit *localhost* erstellt, und entfernen Sie den Kommentar für den Code, der den Client mit der mobilen Remote-Dienst-URL erstellt. Das Ergebnis ist wie folgt:

    ``` {}
    var client = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
                "XXXXXX-APPLICATION-KEY-XXXXXX"
            );
    ```

    Der Client greift jetzt auf den in Azure veröffentlichten mobilen Dienst zu.

2.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

3.  Geben Sie in der App einen beschreibenden Text ein, zum Beispiel *Lernprogramm abschließen*, unter **TodoItem einfügen**. Klicken Sie dann auf **Speichern**.

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet.

![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png)

Nächste Schritte
----------------

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   [Erste Schritte mit Daten](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data)
    <br/>Weitere Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users)
    <br/>Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push)
    <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Get started with authentication]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Get started with push notifications]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[JavaScript version]: /de-de/documentation/articles/mobile-services-windows-store-get-started
[Get started with data in Mobile Services using Visual Studio 2012]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

