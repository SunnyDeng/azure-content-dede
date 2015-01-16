<properties urlDisplayName="Get Started with Data (HTML5)" pageTitle="Erste Schritte mit Daten (HTML 5) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer HTML-App." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Hinzufügen von Mobile Services zu einer vorhandenen App

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in HTML-Apps. In diesem Lernprogramm laden Sie eine App herunter, speichern die Daten im Speicher, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in die App, und melden sich am Azure-Verwaltungsportal an, um die beim Ausführen der App an den Daten vorgenommenen Änderungen anzuzeigen.

>[WACOM.NOTE]Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit Mobile Services in Azure Daten aus einer HTML-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Wenn dies Ihre erste Erfahrung mit mobilen Diensten ist, sollten Sie abwägen, zunächst das Lernprogramm <a href="/de-de/develop/mobile/tutorials/get-started-html">Erste Schritte mit Mobile Services</a> durchzuführen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des HTML-App-Projekts]
2. [Erstellen des mobilen Dienstes]
3. [Erstellen einer Datentabelle als Datenspeicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

<div class="dev-callout"><strong>Hinweis:</strong> <p>Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div> 

###Weitere Anforderungen

Sie können die GetStartedWithData-App auf jedem beliebigen Webserver hosten. Wir haben jedoch praktische Skripts bereitgestellt, mit denen Sie die App auf `http://localhost:8000` ausführen können.
 
+ Um localhost für dieses Lernprogramm verwenden zu können, muss einer der folgenden Webserver auf Ihrem lokalen Computer laufen:

	+  **Unter Windows**: IIS Express. IIS Express wird mit dem [Microsoft-Webplattform-Installer] installiert.    
	+  **Unter MacOS X**: Python, das bereits installiert sein sollte.
	+  **Unter Linux**: Python. Die [neueste Version von Python] muss installiert werden. 
	
	Die App kann zwar auf jedem Webserver gehostet werden, aber dies sind die von den heruntergeladenen Skripts unterstützen Webserver.  

+ Ein Webbrowser, der HTML5 unterstützt.

<h2><a name="download-app"></a>Download des GetStartedWithData-Projekts</h2>

Dieses Lernprogramm verwendet die HTML5-App [GetStartedWithData app]. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden. 

1. [Download der HTML-App-Projektdateien][GetStartedWithData-App].

2. Öffnen Sie das heruntergeladene Projekt in einem HTML-Editor und sehen Sie sich die Datei app.js an.

   	Beachten Sie, dass hinzugefügte Elemente in einem speicherinternen **Array**-Objekt (**staticItems**) gespeichert werden. Aktualisieren Sie die Seite, und die Daten verschwinden. Die Daten werden nicht persistent gespeichert.

3. Starten Sie eine der folgenden Befehlsdateien aus dem **server**-Unterordner.

	+ **launch-windows** (Windows-Computer) 
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	<div class="dev-callout"><b>Hinweis</b>
		<p>Bei einem Windows-Computer geben Sie `R` ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.</p>
	</div>
	
	Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

4. Öffnen Sie die URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in einem Webbrowser, um die App zu starten.

5. Geben Sie in der App unter **Neue Aufgabe eingeben**eine Beschreibung ein, wie zum Beispiel _Lernprogramm ausführen_ und klicken Sie anschließend auf **Hinzufügen**.

   	![][0]  

   	  Beachten Sie, dass der gespeicherte Text dem **staticItems**-Array hinzugefügt wird. Anschließend wird die Seite aktualisiert, um das neue Element anzuzeigen.

<h2><a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst</h2>

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

   	![][5]

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3. Geben Sie in **Tabellenname** den Namen _TodoItem_ ein, und klicken Sie auf die Schaltfläche "Prüfen".

  	![][6]

  	Daraufhin wird eine neue Speichertabelle namens **TodoItem** mit Standardberechtigungen erstellt. Dies bedeutet, dass jeder mit dem Anwendungsschlüssel (der mit Ihrer App verteilt wird) auf die Daten in der Tabelle zugreifen und diese ändern kann.

    <div class="dev-callout"> 
	<b>Hinweis</b> 
	<p>Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Auf diese Weise werden Datenkollisionen verhindert, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.</p> 
	</div>

4. Klicken Sie auf die neue **TodoItem**-Tabelle, und vergewissern Sie sich, dass sie keine Datenzeilen enthält.

5. Klicken Sie auf die Registerkarte**Spalten**. Überprüfen Sie, dass folgende Standardspalten automatisch erstellt werden: 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>Spaltenname</th>
 	<th>Typ</th>
 	<th>Index</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>string</td>
 	<td>Indiziert</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>date</td>
 	<td>Indiziert</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>date</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 

  	Dies ist die Mindestanforderung für eine Tabelle in Mobile Services. 

    <div class="dev-callout"><b>Hinweis</b>
	<p>Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.</p>
    </div>

6. Vergewissern Sie sich auf der Registerkarte **Konfigurieren**, dass `localhost` bereits im Feld **Anforderungen von Hostnamen zulassen** unter **Cross-Origin Resource Sharing (CORS)** eingetragen ist. Falls nicht, geben Sie `localhost` im Feld **Hostname** ein, und klicken Sie dann auf **Speichern**.

  	![][11]

	<div class="dev-callout"><b>Hinweis</b>
		<p>Wenn Sie die Schnellstart-App auf einem anderen Webserver als "localhost" bereitstellen, müssen Sie den Hostnamen des Webservers der Liste <strong>Anforderungen von Hostnamen zulassen</strong> hinzufügen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn155871.aspx" target="_blank">Cross-Origin Resource Sharing (CORS)</a>.</p>
	</div>

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

<h2><a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste</h2>

Ihr mobiler Dienst ist nun bereit, und Sie können die App aktualisieren, sodass Elemente in Mobile Services anstatt in einer lokalen Sammlung gespeichert werden. 

3. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

4. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie die **Site-URL**. Klicken Sie anschließend auf **Schlüssel verwalten,**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![][8]

  	Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

1. Öffnen Sie die Projektdatei index.html in Ihrem Web-Editor und fügen Sie den folgenden Code zu den Skriptverweisen der Seite hinzu:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

5. Öffnen Sie die Datei app.js im Editor und fügen Sie den folgenden Code hinzu, um die **MobileServiceClient**-Variable zu definieren und übergeben Sie Anwendungs-URL und -Schlüssel in dieser Reihenfolge an den **MobileServiceClient**-Konstruktor:

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	Dieser Code erstellt eine neue MobileServiceClient-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird.

6. Entfernen Sie die Kommentare in den folgenden Codezeilen:

		var itemCount = 0;
		var staticItems = [];

	Die Daten werden im mobilen Dienst gespeichert, und nicht im speicherinternen Array.

6. Entfernen Sie die Kommentare in den folgenden Codezeilen:

        todoItemTable = client.getTable('todoitem');

   	Dieser Code erstellt ein Proxyobjekt (**todoItemTable**) für die SQL-Datenbank **TodoItem**. 

7. Ersetzen Sie den **$('#add-item').submit**-Ereignishandler durch den folgenden Code:

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

8. Ersetzen Sie die Methode **refreshTodoItems** durch den folgenden Code:

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}
	   

   Dieser Code schickt eine Abfrage an den mobilen Dienst, die alle Elemente zurückgibt. Das Ergebnis wird durchlaufen und die Daten auf der Seite angezeigt. 

9. Ersetzen Sie die Ereignishandler **$(document.body).on('change', '.item-text')** und **$(document.body).on('change', '.item-complete')** durch den folgenden Code:
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	Dieser Code sendet eine Elementaktualisierung an den mobilen Dienst, wenn Text geändert oder das Kontrollkästchen aktiviert wird.

10. Ersetzen Sie den **$(document.body).on('click', '.item-delete')**-Ereignishandler durch den folgenden Code:

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	Dieser Code schickt eine Löschanfrage an den Server, wenn die **Delete**-Schaltfläche angeklickt wird.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

<h2><a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst</h2>

4. Laden Sie die URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> erneut in einem Webbrowser, um die App zu starten.

    <div class="dev-callout"><b>Hinweis</b>
	<p>Wiederholen Sie die Schritte im ersten Abschnitt, falls Sie den Webserver neu starten müssen.</p>
    </div>

2. Geben Sie wie zuvor Text in das Feld **Neue Aufgabe eingeben** ein und klicken Sie auf **Hinzufügen**. 

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Services**, und anschließend auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten**,und dann auf **Durchsuchen**.

   	![][9]
  
   	Beachten Sie, dass die **TodoItem**Tabelle nun Daten mit von Mobile Services generierten ID-Werten enthält. Der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

5. Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**. 

  	Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

6. Suchen Sie die Methode **RefreshTodoItems** in der Projektdatei app.js und ersetzen Sie die Codezeile, in der `query` definiert wird, durch die folgende Zeile:

   		var query = todoItemTable.where({ complete: false });

7. Laden Sie die Seite erneut und markieren Sie eines der anderen Elemente in der Liste.

   	Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Änderung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen für die Integration von Daten in HTML-Apps in Mobile Services behandelt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.
 
Nach Abschluss der Daten-Lernprogramme können Sie herausfinden, wie Sie Benutzer Ihrer App authentifizieren können. Probieren Sie eines der anderen Lernprogramme aus, z. B. [Erste Schritte mit Authentifizierung].

<!-- Anchors. -->
[Herunterladen des HTML-App-Projekts]: #download-app
[Erstellen des mobilen Dienstes]: #create-service
[Erstellen einer Datentabelle als Datenspeicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-html
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[GetStartedWithData-App]:  http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-html-js-client

[Cross-Origin Resource Sharing]: http://msdn.microsoft.com/de-de/library/windowsazure/dn155871.aspx

