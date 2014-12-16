<properties urlDisplayName="Call a custom API from the client" pageTitle="Aufrufen einer benutzerdefinierten API aus einem HTML-Client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird beschrieben, wie Sie eine benutzerdefinierte API aus einer HTML-Anwendung aufrufen können. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügne diese Funktion zur App hinzu, die Sie entweder im [Erste Schritte mit mobilen Diensten] oder im [Erste Schritte mit Daten] Lernprogramm hinzugefügt haben. Führen Sie dazu folgende Schritte durch:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3. [Testen der App] 

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie dieses Lernpgrogramm starten, müssen Sie zuerst  die Lernprogramme [Erste Schritte mit mobilen Diensten] oder [Erste Schritte mit Daten].durchführen

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

<h2><a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API</h2>

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

2. Geben Sie in der App Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

3. Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4. Klicken Sie auf die Schaltfläche **Alle abgeschlossen**. Ein Meldungsdialogfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, wodurch alle Elemente aus der Liste gelöscht werden.

## Nächste Schritte

Sie haben eine benutzerdefinierte API erstellt und diese über Ihre HTML-App aufgerufen und können nun weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr zum Erstellen benutzerdefinierter APIs.

<!-- Anchors. -->
[Definieren der benutzerdefinierten API]: #define-custom-api
[Aktualisieren der App zum Aufruf der benutzerdefinierten API]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]: #next-steps

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Meine Apps-Dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-html-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-html-get-started-data
