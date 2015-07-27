<properties
	pageTitle="Erste Schritte mit Mobile Services für Xamarin iOS-Apps - Azure Mobile Services"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Services für die Xamarin iOS-Entwicklung."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="donnam"/>

# <a name="getting-started"></a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer Xamarin iOS-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache _To-Do-Listen_-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, mit dem Sie serverseitige Geschäftslogik in JavaScript schreiben können, finden Sie in der [JavaScript-Back-End-Version] dieses Themas.

>[AZURE.NOTE]In diesem Thema wird das Erstellen eines neuen mobilen Dienstprojekts und einer universellen Windows-App mithilfe des Azure-Verwaltungsportals erläutert. Unter Verwendung von Visual Studio 2013 Update 2 können Sie außerdem neue mobile Dienstprojekte zu einer vorhandenen Visual Studio-Lösung hinzufügen. Weitere Informationen finden Sie unter [Schnellstart: Hinzufügen von mobilen Diensten (.NET-Back-End)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]


Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Services-Lernprogramme für Xamarin iOS-Apps.

>[AZURE.NOTE]Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen erhalten Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Kostenlose Azure-Testversion</a>.<br />Für dieses Lernprogramm ist <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> erforderlich. Dazu ist eine kostenlose Testversion verfügbar.

## Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Erstellen einer neuen Xamarin iOS-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt laden Sie eine neue Xamarin iOS-App und ein Dienstprojekt für Ihren mobilen Dienst herunter.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf der Registerkarte "Schnellstart" auf **Xamarin** unter **Plattform auswählen** und erweitern Sie **Erstellen einer neuen Xamarin iOS-App**.

   	![][6]

   	Dadurch werden drei einfache Schritte zum Erstellen einer Xamarin iOS-App angezeigt, die mit dem mobilen Dienst verbunden wird.

  	![][7]

3. Falls noch nicht geschehen, müssen Sie <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> auf den lokalen oder virtuellen Computer herunterladen und installieren.

4. Laden Sie [Xcode] V. 4.4 oder neuer herunter sowie [Xamarin Studio]. Sie können auch Xamarin für Visual Studio verwenden.

5. Wählen Sie unter **Service in der Cloud herunterladen und veröffentlichen** die Option **iOS** und klicken Sie auf **Herunterladen**.

  	Daraufhin wird eine Lösung heruntergeladen, die Projekte für den mobilen Dienst und für die beispielhafte _To-Do-Listen_-Anwendung, die mit dem mobilen Dienst verbunden ist, enthält. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

6. Laden Sie das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Veröffentlichen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Ausführen der Xamarin iOS-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie entweder in Visual Studio oder in Xamarin Studio zum Client-Projekt mit der mobilen Dienstlösung.

	![][8]

	![][9]

2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Client-Projekt zu erstellen und die App im iPhone-Simulator zu starten.

3. Geben Sie in der App einen sinnvollen Text ein, wie z. B. _Lernprogramm abschließen_, und klicken Sie dann auf das Plus-Symbol (**+**).

	![][10]

	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

>[AZURE.NOTE]Sie können den Code überprüfen, der auf den mobilen Dienst zum Abfragen und Einfügen von Daten zugreift; Sie finden ihn in der C#-Datei "QSTodoService.cs C#".


## Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

* [Erste Schritte mit der Synchronisierung von Offlinedaten] <br/>Erfahren Sie, wie Sie die Offlinedatensynchronisierung nutzen, um eine flexible und stabile App zu erstellen.

* [Erste Schritte mit der Authentifizierung] <br/>Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen] <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

* [Beheben von Problemen bei einem Mobile Services .NET-Back-End] <br/> Erfahren Sie, wie Sie Probleme diagnostizieren und beheben, die in Zusammenhang mit einem Mobile Services .NET-Back-End auftreten.

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Erste Schritte mit der Synchronisierung von Offlinedaten]: mobile-services-xamarin-ios-get-started-offline-data.md
[Erste Schritte mit der Authentifizierung]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[JavaScript-Back-End-Version]: mobile-services-ios-get-started.md
[Get started with data in Mobile Services using Visual Studio 2012]: ../mobile-services-windows-store-dotnet-get-started-data-vs2012.md
[Beheben von Problemen bei einem Mobile Services .NET-Back-End]: mobile-services-dotnet-backend-how-to-troubleshoot.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=July15_HO3-->