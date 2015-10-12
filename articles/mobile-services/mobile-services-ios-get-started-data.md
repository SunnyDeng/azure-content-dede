<properties
	pageTitle="Hinzufügen von Mobile Services zu einer vorhandenen App (iOS) | Microsoft Azure"
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer iOS-App."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

In diesem Lernprogramm laden Sie eine vorhandene Anwendung, die Daten im Arbeitsspeicher speichert, herunter und ändern sie, sodass sie mit einem Azure Mobile Service zusammenarbeiten kann.

Bevor Sie dieses Lernprogramm starten, müssen Sie den [Schnellstart] bearbeitet haben. Sie verwenden den mobilen Dienst, den Sie in Schnellstart erstellt haben.

##<a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [iOS-App GetStartedWithData-App] auf. Die App unterscheidet sich vom [Schnellstart] nur dadurch, dass hinzugefügte Elemente im Arbeitsspeicher gespeichert werden.

Laden Sie die [iOS-App GetStartedWithData] herunter. Öffnen Sie in Xcode das heruntergeladene Projekt, und prüfen Sie die Datei **TodoService.m**. Es sind acht **// TODO**-Kommentare mit den Schritten vorhanden, die erforderlich sind, damit diese App funktionieren kann.

##<a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Test-App

1. Klicken Sie in Xcode auf **Run**, um die App zu starten. Fügen Sie Elemente der TODO-Liste hinzu, indem Sie Text eingeben und auf **+** klicken.

2. Klicken Sie im Azure-Verwaltungsportal auf **Mobile Services** und dann auf Ihren mobilen Dienst. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**. Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784


[Schnellstart]: ../mobile-services-javascript-backend-ios-get-started.md
[iOS-App GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[iOS-App GetStartedWithData-App]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!---HONumber=Oct15_HO1-->