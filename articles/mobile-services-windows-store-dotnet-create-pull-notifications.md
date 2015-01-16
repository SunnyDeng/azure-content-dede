<properties urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Definieren einer benutzerdefinierten API, die Pullbenachrichtigungen unterstützt - Azure Mobile Services" metaKeywords="" description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren, die regelmäßige Benachrichtigungen in Windows Store-Apps unterstützt, die Azure Mobile Services verwenden." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Definieren benutzerdefinierter APIs mit Unterstützung für periodische Benachrichtigungen

<div class="dev-center-tutorial-selector"> 
	<a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Windows Store JavaScript">Windows Store JavaScript</a>
</div>

In diesem Thema lernen Sie, wie Sie eine benutzerdefinierte API für periodische Benachrichtigungen aus einer Windows Store-App aufrufen. Durch die Aktivierung von periodischen Benachrichtigungen greift Windows periodisch auf Ihren benutzerdefinierten API-Endpunkt zu und verwendet die zurückgegebene XML, in einem kachelspezifischen Format, um die App-Kachel im Startmenü zu aktualisieren. Weitere Informationen finden Sie unter [Periodische Benachrichtigungen]. 

Sie fügen diese Funktionalität der App hinzu, die Sie nach Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] erstellt haben. Dafür führen Sie die folgenden Schritte aus:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aktivieren periodischer Benachrichtigungen]
3. [Testen der App] 

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] oder das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App] abschließen.  

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

   	![][0]

2. Klicken Sie auf die Registerkarte **API**, und klicken Sie dann auf **Create a custom API**.

   	![][1]

	Damit wird das Dialogfeld **Create a new custom API** angezeigt.

3. Ändern Sie **Get permission** zu **Everyone**, geben Sie "tiles" als **API-Name** an, und klicken Sie dann auf die Häkchenschaltfläche.

   	![][2]

	Daraufhin wird die neue API mit öffentlichem GET-Zugriff erstellt.

4. Klicken Sie auf den neuen Eintrag tiles in der API-Tabelle.

	![][3]

5. Klicken Sie auf die Registerkarte **Skript**, und ersetzen Sie den vorhandenen Code durch den folgenden:

		exports.get = function(request, response) {
		    var wns = require('wns');
		    var todoItems = request.service.tables.getTable('TodoItem');
		    todoItems.where({
		        complete: false
		    }).read({
		        success: sendResponse
		    });
		
		    function sendResponse(results) {
		        var tileText = {
		            text1: "My todo list"
		        };
		        var i = 0;
		        console.log(results)
		        results.forEach(function(item) {
		            tileText["text" + (i + 2)] = item.text;
		            i++;
		        });
		        var xml = wns.createTileSquareText01(tileText);
		        response.set('content-type', 'application/xml');
		        response.send(200, xml);
		    }
		};

	Dieser Code gibt die ersten 3 nicht abgeschlossenen Elemente aus der Tabelle "TodoItem" zurück und lädt sie in ein JSON-Objekt, das an die Funktion **wns**.**createTileSquareText01** übergeben wird. Diese Funktion gibt das folgende XML für tile-Vorlagen zurück:

		<tile>
			<visual>
				<binding template="TileSquareText01">




				</binding>
			</visual>
		</tile>

	Die Funktion **exports.get** wird verwendet, da der Client eine GET-Anfrage für den Zugriff auf die tile-Vorlage sendet.

   	<div class="dev-callout"><b>Hinweis</b>
   		<p>Dieses benutzerdefinierte API-Skript verwendet das "Node.js <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">wns"-Modul, das</a>über die Funktion <strong>require</strong> referenziert wird. Dieses Modul unterscheidet sich vom <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">wns-Objekt,</a> das vom <a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj554217.aspx">push-Objekt</a>zurückgegeben und verwendet wird, um Pushbenachrichtigungen von Serverskripts zu senden.</p>
   	</div>

Als Nächstes ändern Sie die Quickstart-App, um periodische Benachrichtigungen zu verschicken, die das Live Tile durch Anfragen an die neue benutzerdefinierte API aktualisiert.

<h2><a name="update-app"></a>Aktualisieren der App zum Aktivieren periodischer Benachrichtigungen</h2>

1. Drücken Sie in Visual Studio die Taste F5, um die Schnellstart-App aus dem vorherigen Lernprogramm auszuführen.

2. Stellen Sie sicher, dass mindestens ein Element angezeigt wird. Falls kein Element angezeigt wird, geben Sie einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

3. Öffnen Sie in Visual Studio die Projektdatei "App.xaml.cs". Fügen Sie die folgende using-Anweisung hinzu:

		using Windows.UI.Notifications;

4. Fügen Sie den folgenden Code zum **OnLaunched**-Ereignishandler hinzu:

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

	Mit diesem Code werden periodische Benachrichtigungen zur Abfrage von tile-Vorlagendaten aus der neuen benutzerdefinierten **tiles**-API aktiviert. Wählen Sie unter [PeriodicUpdateRecurrance] einen passenden Wert für das Aktualisierungsintervall Ihrer Daten aus.

## <a name="test-app"></a>Testen der App

1. Drücken Sie in Visual Studio die Taste F5, um die App erneut auszuführen.

	Daraufhin werden die periodischen Benachrichtigungen aktiviert.

2. Navigieren Sie zum Startbildschirm, suchen Sie das Live Tile der App und beachten Sie, dass dieses Tile nun Elementdaten enthält.

 	![][4]

## Nächste Schritte

Da Sie nun eine periodische Benachrichtigung erstellt haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

* [Erste Schritte mit Pushbenachrichtigungen]
	<br/>Periodische Benachrichtigungen werden von Windows verwaltet und erfolgen nach einem vordefinierten Zeitplan. Pushbenachrichtigungen können bedarfsgesteuert vom mobilen Dienst gesendet werden und vom Typ Popup, Tile oder reine Benachrichtigungen sein.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über die Erstellung benutzerdefinierter APIs.

* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. -->
[Definieren der benutzerdefinierten API]: #define-custom-api
[Aktualisieren der App zum Aktivieren periodischer Benachrichtigungen]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows-Pushbenachrichtigungen und Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Meine Apps-Dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started
[Hinzufügen von Mobile Services zu einer vorhandenen App]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Periodische Benachrichtigungen]: http://msdn.microsoft.com/de-de/library/windows/apps/jj150587.aspx

[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


<!--HONumber=35.1-->
