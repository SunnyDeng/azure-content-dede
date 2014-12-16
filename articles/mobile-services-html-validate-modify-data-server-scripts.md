<properties urlDisplayName="Validate Data - HTML5" pageTitle="Verwenden von Serverskripts zum Überprüfen und Ändern von Daten (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts 

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre HTML-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Überprüfung der Zeichenfolgenlänge hinzufügen]
2. [Client zur Unterstützung der Überprüfung aktualisieren]
3. [Zeitstempel beim Einfügen hinzufügen]
4. [Client zum Anzeigen des Zeitstempels aktualisieren]

Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten] auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten] abschließen.  

## <a name="string-length-validation"></a>Überprüfung hinzufügen

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihre App. 

   	![][0]

2. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**.

   	![][1]

3. Klicken Sie auf **Skript**, und dann auf den Vorgang **Einfügen**.

   	![][2]

4. Erstetzen Sie das vorhandene Skript durch folgende Funktion, und klicken Sie dann auf **Speichern**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: "Text cannot exceed 10 characters"
                });
            } else {
                request.execute();
            }
        }

    Das Skript überprüft die Länge der **TodoItem.text**-Eigenschaft und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die Funktion **execute** aufgerufen, um das Einfügen abzuschließen.

    <div class="dev-callout"> 
	<b>Hinweis</b> 
	<p>Sie können ein registriertes Skript auf der Registerkarte <strong>Script</strong> entfernen, indem Sie auf <strong>Löschen</strong> und dann auf <strong>Speichern</strong> klicken.</p></div>	

## <a name="update-client-validation"></a>Aktualisieren des Clients

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Nun müssen Sie Ihre App aktualisieren, damit sie Fehlerantworten der Überprüfung verarbeiten kann.

1. Starten Sie eine der folgenden Befehlsdateien aus dem Unterordner **server** des Projekts aus, das Sie während der Bearbeitung des Lernprogramms [Erste Schritte mit Daten] geändert haben.

	+ **launch-windows** (Windows-Computer) 
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	<div class="dev-callout"><b>Hinweis</b>
		<p>Bei einem Windows-Computer geben Sie `R` ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.</p>
	</div>

	Daraufhin wird ein Webserver auf Ihrem lokalen Computer zum Hosten der App gestartet.

1. 	Öffnen Sie die Datei app.js und ersetzen Sie den **$('#add-item').submit()**-Ereignishandler durch den folgenden Code:

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems, function(error){
					alert(JSON.parse(error.request.responseText).error);
				});
			}
			textbox.val('').focus();
			evt.preventDefault();
		});

2. Navigieren Sie in einem Webbrowser zu <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, geben Sie in **Add new task** Text ein, und klicken Sie auf **Add**.

   	Die Operation schlägt fehl, und die Fehlerbehandlung zeigt die Fehlermeldung in einem Dialogfeld an.

## <a name="add-timestamp"></a>Hinzufügen eines Zeitstempels

In der vorherigen Aufgabe wurde eine Einfügung überprüft und entweder akzeptiert oder abgelehnt. Nun aktualisieren Sie die eingefügten Daten mithilfe eines Serverskripts, das eine Zeitstempeleigenschaft zu dem Objekt hinzufügt, bevor es eingefügt wird.

<div class="dev-callout"><b>Hinweis</b>
<p>Die hier demonstrierte Zeitstempeleigenschaft <b>createdAt</b> ist nun redundant. Mobile Services erstellt automatisch eine Systemeigenschaft <b>__createdAt</b> für jede Tabelle.</p>
</div>

1. Ersetzen Sie auf der Registerkarte **Scripts** im [Verwaltungsportal] das aktuelle Skript **Insert** durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: 'Text length must be under 10'
                });
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

Diese Funktion erweitert das vorherige insert-Skript, indem es eine neue Zeitstempeleigenschaft     createdAt** zum Objekt hinzufügt, bevor es durch den **request**.**execute -Aufruf eingefügt wird.

    <div class="dev-callout"><b>Hinweis</b>
	<p>Bei der ersten Ausführung dieses Skripts muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services automatisch die Spalte <strong>createdAt</strong> in die Tabelle <strong>TodoItem</strong> bei der ersten Ausführung ein. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App veröffentlicht wird.</p>
    </div>

2. Aktualisieren Sie die Seite im Webbrowser, geben Sie einen Text mit weniger als 10 Zeichen unter **Add new Task** ein und klicken Sie auf **Add**.

   	Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3. Klicken Sie im Verwaltungsportal auf die Registerkarte **Durchsuchen** in der Tabelle **todoitem**.
   
   	Beachten Sie, dass es nun ein Spalte **createdAt** gibt und dass das neu eingefügte Element über einen Zeitstempelwert verfügt.
  
Nun müssen Sie die App aktualisieren, um diese neue Spalte anzuzeigen.

## <a name="update-client-timestamp"></a>Den Client erneut aktualisieren

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1. Öffnen Sie die Datei app.js in Ihrem Editor und ersetzen Sie die vorhandene Funktion **refreshTodoItems** durch den folgenden Code:

		function refreshTodoItems() {
			var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

			query.read().then(function(todoItems) {
				var listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">')
							.prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text))
						.append($('<span class="timestamp">' 
							+ (item.createdAt && item.createdAt.toDateString() + ' '
							+ item.createdAt.toLocaleTimeString() || '') 
							+ '</span>')));

				});

				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}

   	Dadurch wird der Datumsteil der neuen **createdAt**-Eigenschaft angezeigt. 

2. Öffnen Sie die Datei style.css in Ihrem Editor und ersetzen Sie die Stile in der `item-text`-Klasse durch den folgenden Code:

		.item-text { width: 70%; height: 26px; line-height: 24px; 
			border: 1px solid transparent; background-color: transparent; }
		.timestamp { width: 30%; height: 40px; font-size: .75em; }

	Dieser Code ändert die Größe des Textfelds und wendet einen Stil auf den neuen Zeitstempeltext an.
	
6. Laden Sie die Seite neu. 	

   	Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

7. Ersetzen Sie in der Funktion **refreshTodoItems** die Codezeile, in der die Abfrage definiert wird, durch den folgenden Code:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

   	Diese Funktion aktualisiert die Abfrage, damit auch Elemente herausgefiltert werden, die nicht über einen Zeitstempelwert verfügen.
	
8. Laden Sie die Seite neu.

   	Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging].

Weitere Informationen finden Sie unter [Verwenden von Serverskripts] und [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]


<!-- Anchors. -->
[Überprüfung der Zeichenfolgenlänge hinzufügen]: #string-length-validation
[Client zur Unterstützung der Überprüfung aktualisieren]: #update-client-validation
[Zeitstempel beim Einfügen hinzufügen]: #add-timestamp
[Client zum Anzeigen des Zeitstempels aktualisieren]: #update-client-timestamp
[Nächste Schritte]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Arbeiten mit Serverskripts]: /de-de/develop/mobile/how-to-guides/work-with-server-scripts
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-html
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-html
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-html
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-html
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html

[Verwaltungsportal]: https://manage.windowsazure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-html-js-client
