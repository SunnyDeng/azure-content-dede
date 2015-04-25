<properties 
	pageTitle="Aufrufen einer benutzerdefinierten API aus einem HTML-Client - Mobile Services" 
	description="Erfahren Sie, wie Sie eine benutzerdefinierte API definieren und dann von einer HTML-App aufrufen, die Microsoft Azure Mobile Services verwendet." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	writer="jparrel" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

In diesem Thema wird beschrieben, wie Sie eine benutzerdefinierte API aus einer HTML-Anwendung aufrufen können. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf  `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, welche Sie beim Abschluss entweder des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] erstellen. Dafür führen Sie die folgenden Schritte aus:

1. [Definieren der benutzerdefinierten API]
2. [Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3. [Testen der App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] abschließen, bevor Sie mit diesem Lernprogramm beginnen.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

<h2><a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API</h2>

1. Öffnen Sie die Datei index.html in Ihrem Text-Editor, suchen Sie das **button**-Element mit dem Namen  `buttonRefresh`, und fügen Sie das folgende neue Element direkt im Anschluss ein: 

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

2. Geben Sie in der App in **TodoItem einfügen** Text ein, und klicken Sie dann auf **Speichern**.

3. Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4. Klicken Sie auf die Schaltfläche **Alle abgeschlossen**. Ein Meldungsdialogfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, um alle Elemente aus der Liste zu löschen.

## Nächste Schritte

Sie haben eine benutzerdefinierte API erstellt und diese über Ihre HTML-App aufgerufen und können nun weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

* [Mobile Services: Serverskriptreferenz]
  <br/>Weitere Informationen zum Erstellen von benutzerdefinierten APIs.

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


<!--HONumber=42-->
