<properties
	pageTitle="Erste Schritte mit Mobile Apps in Xamarin iOS"
	description="Erste Schritte mit Xamarin iOS zum Erstellen einer mobilen Azure-App mit Azure App Service."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Erstellen einer Xamarin iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit Azure Mobile App einen cloudbasierten Backend-Dienst zu einer Xamarin iOS-App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen .NET-Dienst und eine einfache To-Do-Listen-App, die App-Daten im .NET-Back-End speichert.

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Wenn Sie in Azure App Service einsteigen möchten, ohne sich zuvor für ein Azure-Konto zu registrieren, wechseln Sie zur Website [App Service ausprobieren](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), auf der Sie eine mobile App mit kurzer Gültigkeitsdauer für Einsteiger in App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen eines neuen mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Erstellen einer neuen Xamarin iOS-App

Sobald Sie das Mobile App-Back-End erstellt haben, können Sie einem einfachen Schnellstart im Azure-Portal folgen, um entweder eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen App-Back-End zu ändern.

In diesem Abschnitt laden Sie eine neue Xamarin iOS-App und ein Dienstprojekt für Ihr mobiles App-Back-End herunter.

1. Klicken Sie im Azure-Portal auf **Mobile App** und anschließend auf das soeben erstellte mobile App-Back-End.

2. Klicken Sie oben im Blatt auf **Client hinzufügen**, und erweitern Sie **Xamarin iOS**.

	![][6]

	Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Xamarin iOS-App angezeigt, die mit Ihrem mobilen App-Back-End verbunden ist.

3. Falls noch nicht geschehen, sollten Sie <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> auf dem lokalen Computer oder virtuellen Computer herunterladen und installieren.  

4. Laden Sie [Xcode] V. 4.4 oder neuer sowie [Xamarin Studio] herunter. Sie können auch Xamarin für Visual Studio verwenden.

5. Klicken Sie unter **Dienst herunterladen und in der Cloud veröffentlichen** auf **Herunterladen**.

 Daraufhin wird eine Lösung heruntergeladen, die Projekte für das mobile App-Back-End und die To-Do-Listen-Beispielanwendung enthält, die mit dem mobilen App-Back-End verbunden ist. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

6. Laden Sie das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Veröffentlichen des mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ausführen der Xamarin iOS-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie entweder in Visual Studio oder in Xamarin Studio zum Clientprojekt innerhalb der Mobile App-Back-End-Lösung.

	![][8]

	![][9]

2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Client-Projekt zu erstellen und die App im iPhone-Simulator zu starten.

3. Geben Sie in der App einen sinnvollen Text, wie z. B. _Lernprogramm abschließen_ ein, und klicken Sie dann auf das Plus-Symbol (**+**).

	![][10]

	Dadurch wird eine POST-Anforderung an das neue, in Azure gehosteten mobile App-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden vom mobilen App-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

>[AZURE.NOTE]Sie können den Code überprüfen, der zum Abfragen und Einfügen von Daten auf das mobile App-Back-End zugreift. Der Code befindet sich in der C#-Datei "QSTodoService.cs".


<!-- Anchors. -->
[Erste Schritte mit Mobile App-Back-Ends]:#getting-started
[Erstellen eines neuen Mobile App-Back-Ends]:#create-new-service
[Nächste Schritte]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Erste Schritte mit der Offline-Datensynchronisierung]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Erste Schritte mit der Authentifizierung]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Erste Schritte mit Pushbenachrichtigungen]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile App-SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure-Portal]: https://portal.azure.com/
[JavaScript-Back-End-Version]: partner-xamarin-mobile-services-ios-get-started.md
[Erste Schritte mit Daten in App Services mit Visual Studio 2012]: app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Problembehandlung für das .NET-Back-End einer mobilen App]: app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin für Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=49-->