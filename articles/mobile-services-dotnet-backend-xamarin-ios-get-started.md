<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started" urlDisplayName="Get Started with Mobile Services for Xamarin iOS apps" pageTitle="Get Started with Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin iOS apps" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer Xamarin iOS-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Backend-Version][JavaScript-Backend-Version] dieses Themas.

>[WACOM.NOTE]In diesem Thema wird das Erstellen eines neuen mobilen Dienstprojekts mithilfe des Azure-Verwaltungsportals erläutert. Unter Verwendung von Visual Studio 2013 Update 2 können Sie außerdem neue mobile Dienstprojekte zu einer vorhandenen Visual Studio-Lösung hinzufügen. Weitere Informationen finden Sie unter [Schnellstart: Hinzufügen von mobilen Diensten (.NET-Backend)][Schnellstart: Hinzufügen von mobilen Diensten (.NET-Backend)]

Unten sehen Sie einen Screenshot aus der fertigen App:

![][]


Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Services-Lernprogramme für Xamarin iOS-Apps.

>[WACOM.NOTE]Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen erhalten Sie unter [Kostenloses Azure-Testkonto][Kostenloses Azure-Testkonto].<br/>Für dieses Lernprogramm ist [Visual Studio Professional 2013][Visual Studio Professional 2013] erforderlich. Dazu ist eine kostenlose Testversion verfügbar.

## Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Erstellen einer neuen Xamarin iOS-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt laden Sie eine neue Xamarin iOS-App und ein Dienstprojekt für Ihren mobilen Dienst herunter.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf der Registerkarte „Schnellstart” auf **Xamarin** unter **Plattform auswählen** und erweitern Sie **Erstellen einer neuen Xamarin iOS-App**.

    ![][1]

   	Dadurch werden drei einfache Schritte zum Erstellen einer Xamarin iOS-App angezeigt, die mit dem mobilen Dienst verbunden wird.

    ![][2]

3. Falls noch nicht geschehen, sollten Sie [Visual Studio Professional 2013][Visual Studio Professional 2013] auf den lokalen Computer oder virtuellen Computer herunterladen und installieren.

4. Laden Sie [Xcode][Xcode] V. 4.4 oder neuer herunter sowie [Xamarin Studio][Xamarin Studio]. Sie können auch Xamarin für Visual Studio verwenden.

5. Wählen Sie unter **Service in der Cloud herunterladen und veröffentlichen** die Option **iOS** und klicken Sie auf **Herunterladen**.

  	Daraufhin wird eine Lösung heruntergeladen, die Projekte für den mobilen Dienst und für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, enthält. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

6. Laden Sie das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Veröffentlichen des mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Ausführen der Xamarin iOS-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie entweder in Visual Studio oder in Xamarin Studio zum Client-Projekt mit der mobilen Dienstlösung.

    ![][3]

    ![][4]

2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Client-Projekt zu erstellen und die App im iPhone-Simulator zu starten.

3. Geben Sie in der App einen sinnvollen Text, wie z. B. *Tutorial fertigstellen* ein, und klicken Sie dann auf das Plus-Symbol (**+**).

    ![][5]

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

>[WACOM.NOTE] Sie können den Code überprüfen, der auf den mobilen Dienst zum Abfragen und Einfügen von Daten zugreift; Sie finden ihn in der C#-Datei „QSTodoService.cs C#”.

## Nächste Schritte

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

* [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
  <br/>Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

[mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
[JavaScript-Backend-Version]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started
[Schnellstart: Hinzufügen von mobilen Diensten (.NET-Backend)]: http://msdn.microsoft.com/de-de/library/windows/apps/dn629482.aspx
[]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[Kostenloses Azure-Testkonto]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
[1]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[2]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin Studio]: http://xamarin.com/download
[mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
[mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
[3]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[4]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[5]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push
