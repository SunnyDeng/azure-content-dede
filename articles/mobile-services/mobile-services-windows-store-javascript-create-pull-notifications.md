<properties 
	pageTitle="Definieren einer benutzerdefinierten API, die Pullbenachrichtigungen unterstützt – Azure Mobile Services" 
	description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren, die regelmäßige Benachrichtigungen in Windows Store-Apps unterstützt, die Azure Mobile Services verwenden." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="glenga"/>
# Definieren benutzerdefinierter APIs mit Unterstützung für periodische Benachrichtigungen

<div class="dev-center-tutorial-selector"> 
	<a href="/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Windows Store C#">Windows Store C#</a><a href="/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
</div>

In diesem Thema lernen Sie, wie Sie eine benutzerdefinierte API für periodische Benachrichtigungen aus einer Windows Store-App aufrufen. Durch die Aktivierung von periodischen Benachrichtigungen greift Windows periodisch auf Ihren benutzerdefinierten API-Endpunkt zu und verwendet die zurückgegebene XML, in einem kachelspezifischen Format, um die App-Kachel im Startmenü zu aktualisieren. Weitere Informationen finden Sie unter [Periodische Benachrichtigungen].

Sie fügen diese Funktionalität der App hinzu, die Sie im Lernprogramm [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] erstellt haben. Dafür führen Sie die folgenden Schritte aus:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aktivieren periodischer Benachrichtigungen]
3. [Testen der App] 

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] abschließen.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

   	![][0]

2. Klicken Sie auf die Registerkarte **API** und dann auf **Create a custom API**.

   	![][1]

   	Das Dialogfeld **Create a new custom API** wird angezeigt.

3. Ändern Sie **Get permission** zu **Everyone**, geben Sie _tiles_ unter **API name** ein und klicken Sie auf das Kontrollkästchen.

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

	Dieser Code gibt die ersten drei nicht abgeschlossenen Elemente aus der Tabelle "TodoItem" zurück und lädt sie in ein JSON-Objekt, das an die **wns**.**createTileSquareText01**-Funktion übergeben wird. Diese Funktion gibt das folgende XML für tile-Vorlagen zurück:

		<tile>
			<visual>
				<binding template="TileSquareText01">
					<text id="1">My todo list</text>
					<text id="2">Task 1</text>
					<text id="3">Task 2</text>
					<text id="4">Task 3</text>
				</binding>
			</visual>
		</tile>

	Die Funktion **exports.get** wird verwendet, da der Client eine GET-Anfrage für den Zugriff auf die tile-Vorlage sendet.

   	> [AZURE.NOTE]Dieses benutzerdefinierte API-Skript verwendet das [wns-Modul](http://go.microsoft.com/fwlink/p/?LinkId=306750) für Node.js, auf das durch die Verwendung der **require**-Funktion verwiesen wird. Dieses Modul unterscheidet sich von dem [wns-Objekt](http://go.microsoft.com/fwlink/p/?LinkId=260591), das vom [push-Objekt](http://msdn.microsoft.com/library/windowsazure/jj554217.aspx) zurückgegeben wird, das wiederum für den Versand von Pushbenachrichtigungen in Serverskripts verwendet wird.

Als Nächstes ändern Sie die Quickstart-App, um periodische Benachrichtigungen zu verschicken, die das Live Tile durch Anfragen an die neue benutzerdefinierte API aktualisiert.

##<a name="update-app"></a>Aktualisieren der App zum Aktivieren periodischer Benachrichtigungen

1. Drücken Sie in Visual Studio die Taste F5, um die Schnellstart-App aus dem vorherigen Lernprogramm auszuführen.

2. Stellen Sie sicher, dass mindestens ein Element angezeigt wird. Falls kein Element angezeigt wird, geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

3. Erweitern Sie in Visual Studio im Projektmappen-Explorer den Ordner `\js`, öffnen Sie das "default.js"-Projekt, und fügen Sie die folgenden Codezeilen nach dem Code ein, der die **client**-Variable definiert:

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

	Dieser Code aktiviert periodische Benachrichtigungen zur Abfrage von Tile-Vorlagendaten aus der neuen **tiles**-API. Wählen Sie einen passenden **PeriodicUpdateRecurrance**-Wert für das Aktualisierungsintervall Ihrer Daten aus.

## <a name="test-app"></a>Testen der App

1. Drücken Sie in Visual Studio die Taste F5, um die App erneut auszuführen.

	Daraufhin werden die periodischen Benachrichtigungen aktiviert.

2. Navigieren Sie zum Startbildschirm, suchen Sie das Live Tile der App und beachten Sie, dass dieses Tile nun Elementdaten enthält.

 	![][4]

## Nächste Schritte

Da Sie nun eine periodische Benachrichtigung erstellt haben, können Sie weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

* [Erste Schritte mit Pushbenachrichtigungen] <br/>Periodische Benachrichtigungen werden von Windows verwaltet und erfolgen nach einem vordefinierten Zeitplan. Pushbenachrichtigungen können bedarfsgesteuert vom mobilen Dienst gesendet werden und vom Typ Popup, Tile oder reine Benachrichtigungen sein.

* [Mobile Services: Serverskriptreferenz] <br/>Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

<!-- Anchors. -->
[Definieren der benutzerdefinierten API]: #define-custom-api
[Aktualisieren der App zum Aktivieren periodischer Benachrichtigungen]: #update-app
[Testen der App]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Erste Schritte mit Mobile Services]: mobile-services-javascript-backend-windows-store-javascript-get-started.md
[Hinzufügen von Mobile Services zu einer vorhandenen App]: ../mobile-services-windows-store-javascript-get-started.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Periodische Benachrichtigungen]: http://msdn.microsoft.com/library/windows/apps/jj150587.aspx

 

<!---HONumber=July15_HO2-->