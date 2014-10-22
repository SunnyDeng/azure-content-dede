<properties linkid="mobile-services-html-call-custom-api" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from an HTML client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" >iOS</a><a href="/de-de/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/de-de/documentation/articles/mobile-services-html-call-custom-api" title="HTML" class="current">HTML</a></div>

In diesem Thema wird beschrieben, wie Sie eine benutzerdefinierte API aus einer HTML-Anwendung aufrufen können. Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Über die im Rahmen dieses Themas erstellte benutzerdefinierte API erhalten Sie die Möglichkeit, eine einzelne POST-Anforderung zu senden, die das Erledigt-Kennzeichen für sämtliche todo-Einträge in der Tabelle auf `true` setzt. Ohne diese benutzerdefinierte API müsste der Client einzelne Anforderungen senden, um das Kennzeichen für jeden einzelnen todo-Eintrag in der Tabelle zu aktualisieren.

Sie fügen diese Funktionen zu der App hinzu, welche Sie beim Abschluss entweder des Lernprogramms [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] erstellen. Dafür führen Sie die folgenden Schritte aus:

1.  [Definieren der benutzerdefinierten API][Definieren der benutzerdefinierten API]
2.  [Aktualisieren der App zum Aufruf der benutzerdefinierten API][Aktualisieren der App zum Aufruf der benutzerdefinierten API]
3.  [Testen der App][Testen der App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zuerst [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen, bevor Sie mit diesem Lernprogramm beginnen.

## <a name="define-custom-api"></a>Definieren der benutzerdefinierten API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

## <a name="update-app"></a><span class="short-header">Aktualisieren der App </span>Aktualisieren der App zum Aufruf der benutzerdefinierten API

1.  Öffnen Sie die Datei index.html in Ihrem Text-Editor, suchen Sie das **button**-Element mit dem Namen `buttonRefresh`, und fügen Sie das folgende neue Element direkt im Anschluss ein:

        <button id="buttonCompleteAll">Complete All</button> 

    Auf diese Weise wird eine neue Schaltfläche zur Seite hinzugefügt.

2.  Fügen Sie in page.js im Anschluss an die Funktion **refreshTodoItems** den folgenden Code ein:

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

1.  Aktualisieren Sie Ihren Browser.

2.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

3.  Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4.  Klicken Sie auf die Schaltfläche **Complete All**. Ein Meldungsdialogfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, um alle Elemente aus der Liste zu löschen.

## Nächste Schritte

Sie haben eine benutzerdefinierte API erstellt und diese über Ihre HTML-App aufgerufen und können nun weitere Einzelheiten über die folgenden Mobile Services-Themen erfahren:

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Erfahren Sie mehr über das Erstellen von benutzerdefinierten APIs.

<!-- Anchors. --> <!-- URLs. -->

  [Windows Store C\#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-ios-call-custom-api "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-android-call-custom-api "Android"
  [HTML]: /de-de/documentation/articles/mobile-services-html-call-custom-api "HTML"
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-html-get-started
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-html-get-started-data
  [Definieren der benutzerdefinierten API]: #define-custom-api
  [Aktualisieren der App zum Aufruf der benutzerdefinierten API]: #update-app
  [Testen der App]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
