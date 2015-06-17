<properties
	pageTitle="Erste Schritte mit mobilen Azure App Service-iOS-Apps"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure App Service für die iOS-Entwicklung."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="get-started-article"
	ms.date="02/20/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>Erstellen einer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

In diesem Lernprogramm erfahren Sie, wie eine iOS-App mit mobilen Azure App Service-Apps erstellt wird. Sie erstellen ein neues mobiles App-Back-End und eine einfache _To-Do-Listen_-App, in der App-Daten gespeichert werden. Das Lernprogramm verwendet .NET und Visual Studio für die serverseitige Logik.

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile) sofort kostenlos eine kurzlebige mobile Starter-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen App-Back-Ends

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Erstellen einer neuen iOS-App

Sobald Sie das mobile Back-End erstellt haben, können Sie einem einfachen Schnellstart im Azure-Vorschauportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen App-Back-End zu ändern.

1. Klicken Sie im Azure-Portal auf **Mobile App** und anschließend auf das mobile App-Back-End, das Sie gerade erstellt haben.

2. Klicken Sie oben auf dem Blatt auf "Client hinzufügen", und erweitern Sie iOS.

	![][6]

	Dadurch werden Schritte zum Erstellen einer iOS-App angezeigt, die mit dem mobilen App-Back-End verbunden wird.

3. Falls noch nicht geschehen, müssen Sie <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> auf den lokalen oder virtuellen Computer herunterladen und installieren.

4. Laden Sie [Xcode] V. 4.4 oder neuer herunter sowie [Xamarin Studio]. Sie können auch Xamarin für Visual Studio verwenden.

5. Klicken Sie unter **Service in der Cloud herunterladen und veröffentlichen** auf **Herunterladen**.

 Daraufhin wird eine Lösung heruntergeladen, die Projekte für das mobile App-Back-End und für die beispielhafte _To-Do-Listen_-Anwendung enthält, die mit dem mobilen App-Back-End verbunden ist. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

6. Laden Sie das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen der mobilen App

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Veröffentlichen der mobilen App

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ausführen der iOS-App

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->