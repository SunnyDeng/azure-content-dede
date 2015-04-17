<properties
	pageTitle="Erste Schritte mit Mobile App-Back-Ends für Windows Store-Apps | Mobile Dev Center"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile App-Back-Ends für die Windows Store-Entwicklung in C#, VB oder JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Erstellen einer Windows-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

In diesem Lernprogramm wird gezeigt, wie Sie einer universellen Windows-App einen cloudbasierten Back-End-Dienst unter Verwendung eines Azure Mobile App-Back-Ends hinzufügen. Universelle Windows-App-Lösungen beinhalten Projekte für Windows Store 8.1, Windows Phone Store 8.1-Apps und ein gemeinsames, geteiltes Projekt.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../includes/app-service-mobile-windows-universal-get-started-preview.md)]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Wenn Sie in Azure App Service einsteigen möchten, ohne sich zuvor für ein Azure-Konto zu registrieren, wechseln Sie zur Website [App Service ausprobieren](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), auf der Sie eine mobile App mit kurzer Gültigkeitsdauer für Einsteiger in App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Erstellen einer neuen universellen Windows-App

Sobald Sie das Mobile App-Back-End erstellt haben, können Sie einem einfachen Schnellstart im Azure-Portal folgen, um entweder eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen App-Back-End zu ändern.

In diesem Abschnitt erstellen Sie eine neue universelle Windows-App, die sich mit Ihrem mobilen App-Back-End verwendet.

1. Klicken Sie im Azure-Portal auf **Mobile App** und anschließend auf die soeben erstellte mobile App.

2. Klicken Sie im oberen Bereich des Blatts auf **Client hinzufügen**, und erweitern Sie **Windows (C#)**.

   ![Mobile App quickstart steps](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

   Auf diese Weise werden die drei einfachen Schritte zum Erstellen einer Windows Store-App angezeigt, die mit Ihrer mobilen App verbunden ist.

3. Falls noch nicht geschehen, sollten Sie <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> auf dem lokalen Computer oder virtuellen Computer herunterladen und installieren.

4. Wählen Sie unter **App und Service herunterladen und lokal ausführen** eine Sprache für die Windows Store-App aus. Klicken Sie dann auf **Herunterladen**.

   Daraufhin wird eine Lösung heruntergeladen, die Projekte für das mobile App-Back-End und die To-Do-Listen-Beispielanwendung enthält, die mit dem mobilen App-Back-End verbunden ist. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Testen der mobilen App

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Veröffentlichen des mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ausführen der Windows-App

Nachdem das mobile App-Back-End veröffentlicht wurde und der Client mit dem in Azure gehosteten Mobile App-Remote-Back-End verbunden ist, kann die App ausgeführt und Azure zum Speichern von Elementen verwendet werden.

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[Erste Schritte mit Mobile App-Back-Ends]:#getting-started
[Erstellen eines neuen Mobile App-Back-Ends]:#create-new-service
[Definieren der Mobile App-Back-End-Instanz]:#define-mobile-app-backend-instance
[Nächste Schritte]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Erste Schritte mit der Authentifizierung]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile App-SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure-Portal]: https://portal.azure.com/

<!--HONumber=49-->