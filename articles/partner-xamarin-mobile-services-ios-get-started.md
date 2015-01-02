<properties pageTitle="Erste Schritte mit Mobile Services für Xamarin iOS-Apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="11/22/2014" ms.author="craig.dunn@xamarin.com" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Back-End-Dienst zu einer Xamarin.iOS-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache <em>To-Do-Listen-App,</em> die App-Daten im neuen mobilen Dienst speichert.

Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip unten auswählen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.

Video: "Erste Schritte mit Xamarin und Azure Mobile Services" mit Craig Dunn, Developer Evangelist für Xamarin (Dauer: 10:05 Min.)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Für das Abschließen dieses Lernprogramms sind XCode und [Xamarin Studio] für OS X oder das Xamarin Visual Studio-Plug-In für Visual Studio unter Windows erforderlich. Das Beispiel wird unter iOS 5.0 und höher ausgeführt.

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">"Kostenlose</a>Azure-Testversion".</p></div>

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Dienstes

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Erstellen einer neuen Xamarin.iOS-App</h2>

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern. 

In diesem Abschnitt erstellen Sie eine neue Xamarin.iOS-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie soeben erstellt haben.

2. Klicken Sie auf der Schnellstartregisterkarte auf **Xamarin.iOS** unter **Plattform auswählen**, und erweitern Sie die Option **Neue Xamarin.iOS-App erstellen**.

	![][6]

	Dadurch werden drei einfache Schritte zum Erstellen einer Xamarin.iOS-App angezeigt, die mit dem mobilen Dienst verbunden wird.

  	![][7]

3. Falls noch nicht geschehen, führen Sie den Download und die Installation von Xcode (wir empfehlen die neueste Version, Xcode 6.0 oder höher) und [Xamarin Studio] durch.

4. Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

5. Klicken Sie unter **App herunterladen und ausführen** auf **Download**. 

	Dadurch wird das Projekt für die Beispielanwendung der To-do-Liste heruntergeladen, die mit dem mobilen Dienst verbunden ist und auf die Azure Mobile Services-Komponente für Xamarin.iOS verweist. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

<h2>Ausführen der neuen Xamarin.iOS-App</h2>

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie zu dem Speicherort, an dem Sie die komprimierten Projektdateien gespeichert haben, und öffnen Sie die **XamarinTodoQuickStart.iOS.sln**-Lösungsdatei mithilfe von Xamarin Studio oder Visual Studio.

	![][8]

	![][9]

2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten, was die Standardeinstellung für dieses Projekt ist.

3. Geben Sie in der App einen aussagekräftigen Text ein, z. B. "Lernprogramm abschließen", und klicken Sie dann auf das Plussymbol (**+**).

	![][10]

	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	<div class="dev-callout"> 
	<b>Hinweis</b> 
   	<p>Sie können den Code überprüfen, der auf den Mobile Service zugreift, um Daten abzufragen und einzufügen. Sie finden ihn in der C#-Datei "TodoService.cs".</p> 
 	</div>

4. Klicken Sie zurück im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**.

	![][11]

	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

	![][12]

## <a name="next-steps"> </a>Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden: 

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Synchronisierung von Offlinedaten]
  <br/>Erfahren Sie, wie Sie mit der Synchronisierung von Offlinedaten eine reaktionsfähige, stabile App implementieren können.

* [Erste Schritte mit der Authentifizierung]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.


<!-- Anchors. -->
[Erste Schritte mit Mobile Services]:#getting-started
[Erstellen eines neuen mobilen Diensts]:#create-new-service
[Definieren der mobilen Dienstinstanz]:#define-mobile-service-instance
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Erste Schritte mit der Synchronisierung von Offlinedaten]: /de-de/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=35_1-->
