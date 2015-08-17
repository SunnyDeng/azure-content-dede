<properties 
	pageTitle="Aufrufen einer benutzerdefinierten API aus einem HTML-Client - Mobile Services" 
	description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer HTML-App aufrufen, die Azure Mobile Services verwendet." 
	services="mobile-services" 
	documentationCenter="" 
	authors="bureado"  
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird beschrieben, wie Sie eine benutzerdefinierte API aus einer HTML-Anwendung aufrufen können. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` festlegt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] abschließen.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

##<a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API

1. Öffnen Sie die Datei index.html in Ihrem Text-Editor, suchen Sie das **button**-Element mit dem Namen `buttonRefresh`, und fügen Sie das folgende neue Element direkt im Anschluss ein: 

		<button id="buttonCompleteAll">Complete All</button> 

	Auf diese Weise wird eine neue Schaltfläche zur Seite hinzugefügt.

2. Fügen Sie in page.js im Anschluss an die Funktion **refreshTodoItems** den folgenden Code ein:

		var completeAllTodoItems = function () {
			// Asynchronously call the custom API using the POST method.
			client.invokeApi("completeall", {
				body: null,
				method: "post"
			}).done(function (results) {
				var message = results.result.count + " item(s) marked as complete.";
				alert(message);
				refreshTodoItems();
			}, function(error) {
				alert(error.message);
			});
		};

		$('#buttonCompleteAll').click(function () {
			completeAllTodoItems();
		});

	Diese Methode verarbeitet das **Click**-Ereignis für die neue Schaltfläche. Die **invokeApi**-Methode wird beim Client aufgerufen, der eine POST-Anfrage an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Meldungsdialogfeld angezeigt, ebenso wie jegliche Fehler.

## <a name="test-app"></a>Testen der App

1. Aktualisieren Sie Ihren Browser.

2. Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

3. Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4. Klicken Sie auf die Schaltfläche **Complete All**. Ein Meldungsdialogfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, um alle Elemente aus der Liste zu löschen.

## Nächste Schritte

In diesem Thema wurde gezeigt, wie Sie mithilfe der **invokeApi**-Funktion eine relativ einfache benutzerdefinierte API Ihrer HTML/JavaScript-App aufrufen. Weitere Informationen zum Verwenden der **invokeApi**-Funktion finden Sie im Beitrag [Custom API in Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) (in englischer Sprache).

Weitere Informationen finden Sie in den folgenden Mobile Services-Themen:

* [Mobile Services: Serverskriptreferenz] <br/>Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

* [Speichern von Serverskripts in der Quellcodeverwaltung] <br/> Erfahren Sie, wie Sie die Quellcodeverwaltungsfunktion nutzen können, um benutzerdefinierten API-Skript-Code einfacher und sicherer zu entwickeln und zu veröffentlichen.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Mobile Services]: mobile-services-html-get-started.md
[Hinzufügen von Mobile Services zu einer vorhandenen App]: mobile-services-html-get-started-data.md
[Speichern von Serverskripts in der Quellcodeverwaltung]: mobile-services-store-scripts-source-control.md

<!---HONumber=August15_HO6-->