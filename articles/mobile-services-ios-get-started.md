<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Mobile Services
==================================

[Windows Store](/en-us/documentation/articles/mobile-services-windows-store-get-started "Windows Store") [Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-android-get-started "Android") [HTML](/en-us/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/en-us/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/en-us/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

[.NET-Backend](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ ".NET-Backend") | [JavaScript-Backend](/en-us/documentation/articles/mobile-services-ios-get-started/ "JavaScript-Backend")

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer iOS-App hinzufügen können.

Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip auf der rechten Seite auswählen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.

[Lernprogramm ansehen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart) [Abspielen des Videos](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart) 9:38

In diesem Lernprogramm erstellen Sie sowohl einen neuen mobilen Dienst als auch eine einfache *To-do-List*-App, welche App-Daten in dem neuen mobilen Dienst speichert. Der von Ihnen zu erstellende mobile Dienst verwendet JavaScript für die serverseitige Geschäftslogik. Zum Erstellen eines mobilen Dienstes, der es Ihnen erlaubt, Ihre serverseitige Geschäftslogik mit Visual Studio in den unterstützten .NET-Sprachen zu schreiben, können Sie sich die [.NET Backend-Version] zu diesem Thema ansehen.

Unten finden Sie einen Screenshot der erstellten App:

![][0]

Zum Abschließen dieses Lernprogramms sind XCode 4.5 und iOS 5.0 oder neuere Versionen erforderlich.

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie bislang über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F).

Erstellen eines neuen mobilen Dienstes
--------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Erstellen einer neuen AppErstellen einer neuen iOS-App
------------------------------------------------------

Sobald Sie den mobilen Dienst erstellt haben, können Sie einen leicht nachvollziehbaren Quickstart im Verwaltungsportal durchführen, um entweder eine neue App zu erstellen oder um eine vorhandene App zu modifizieren, damit Sie eine Verbindung zum mobilen Dienst herstellen können.

In diesem Abschnitt erstellen Sie eine neue iOS-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Registerkarte "Quickstart" auf **iOS** unter der **Auswahlplattform**, und erweitern Sie die Option **Erstellen einer neuen iOS-App**.

        ![][6]

        Dadurch werden drei einfache Schritte zum Erstellen einer iOS-App angezeigt, die mit dem mobilen Dienst verbunden wird.

![][7]

1.  Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Xcode] v4.4 oder eine neuere Version herunter, und installieren Sie diese.

2.  Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

3.  Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

Dadurch wird das Projekt für die Beispielanwendung der *To-do-List* heruntergeladen, die zusammen mit dem Mobile Services iOS SDK mit dem mobilen Dienst verbunden ist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

Ausführen der neuen iOS-App
---------------------------

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

1.  Klicken Sie im Verwaltungsportal erneut auf die Registerkarte **Daten** und danach auf die Tabelle **TodoItems**.

    ![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

    So können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

    ![](./media/mobile-services-ios-get-started/mobile-data-browse.png)

    ## Nachdem Sie Quickstart abgeschlossen haben, können Sie nun in den nächsten Schritten erfahren, wie Sie zusätzliche wichtige Aufgaben unter Mobile Services durchführen: \* [Erste Schritte mit Daten]
    Informationen über das Speichern und Abfragen von Daten mit Mobile Services. \* [Erste Schritte mit Authentifizierung]
    Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter. \* [Erste Schritte mit Pushbenachrichtigungen]
    Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App. [Erste Schritte mit Mobile Services]:\#getting-started [Erstellen eines neuen mobilen Dienstes]:\#create-new-service [Definieren der mobilen Dienstinstanz]:\#define-mobile-service-instance [Nächste Schritte]:\#next-steps [0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png [6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png [7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png [8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png [10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png [11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png [12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png [Erste Schritte mit Daten]: /en-us/develop/mobile/tutorials/get-started-with-data-ios [Erste Schritte mit Authentifizierung]: /en-us/develop/mobile/tutorials/get-started-with-users-ios [Erste Schritte mit Pushbenachrichtigungen]: /en-us/develop/mobile/tutorials/get-started-with-push-ios [Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533 [Verwaltungsportal]: https://manage.windowsazure.com/ [XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532 [.NET-Backend-Version]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started


