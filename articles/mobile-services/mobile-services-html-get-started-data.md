<properties 
	pageTitle="Hinzufügen von Mobile Services zu einer vorhandenen App (HTML 5) | Microsoft Azure" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services in einer vorhandenen HTML-App." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="glenga"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Übersicht 

Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in HTML-Apps. In diesem Lernprogramm laden Sie eine App herunter, speichern die Daten im Speicher, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in die App und melden sich am Azure-Verwaltungsportal an, um die beim Ausführen der App an den Daten vorgenommenen Änderungen anzuzeigen.

>[AZURE.NOTE]Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit Mobile Services in Azure Daten aus einer HTML-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Mobile Services-Schnellstart abgenommen werden. Falls Sie noch keine Erfahrung mit mobilen Diensten haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit mobilen Diensten] abschließen.

> [AZURE.IMPORTANT]Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2documentation%2Farticles%2Fmobile-services-html-get-started-data).

###Weitere Anforderungen

Sie können die GetStartedWithData-App auf jedem beliebigen Webserver hosten. Wir haben jedoch praktische Skripts bereitgestellt, mit denen Sie die App auf `http://localhost:8000` ausführen können.
 
+ Um localhost für dieses Lernprogramm verwenden zu können, muss einer der folgenden Webserver auf Ihrem lokalen Computer laufen:

	+  **Unter Windows**: IIS Express. IIS Express wird mit dem [Microsoft-Webplattform-Installer] installiert.   
	+  **Unter Mac OS X**: Python, das bereits installiert sein sollte.
	+  **Unter Linux**: Python. Sie müssen die [neueste Version von Python] installieren. 
	
	Die App kann zwar auf jedem Webserver gehostet werden, aber dies sind die von den heruntergeladenen Skripts unterstützen Webserver.

+ Ein Webbrowser, der HTML5 unterstützt.

##<a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm verwendet die HTML5-App [GetStartedWithData]. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1. [Download der HTML-App-Projektdateien][GetStartedWithData app].

2. Öffnen Sie das heruntergeladene Projekt in einem HTML-Editor und sehen Sie sich die Datei app.js an.

   	Beachten Sie, dass hinzugefügte Elemente in einem speicherinternen **Array**-Objekt (**staticItems**) gespeichert werden. Aktualisieren Sie die Seite, und die Daten verschwinden. Die Daten werden nicht persistent gespeichert.

3. Starten Sie eine der folgenden Befehlsdateien aus dem **server**-Unterordner.

	+ **launch-windows** (Windows-Computer) 
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	> [AZURE.NOTE]Bei einem Windows-Computer geben Sie `R` ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt
	
	Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

4. Öffnen Sie die URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in einem Webbrowser, um die App zu starten.

5. Geben Sie in der App in _Neue Aufgabe eingeben_ eine Beschreibung ein, wie zum Beispiel **Lernprogramm ausführen**, und klicken Sie dann auf **Hinzufügen**.

   	![][0]

   	Beachten Sie, dass der gespeicherte Text dem **staticItems**-Array hinzugefügt wird. Anschließend wird die Seite aktualisiert, um das neue Element anzuzeigen.

##<a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Hinzufügen neuer Tabellen zum mobilen Dienst

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3. Geben Sie unter **Tabellenname** den Namen _TodoItem_ ein und klicken Sie auf das Kontrollkästchen.

  	Dadurch wird die neue Speichertabelle **TodoItem** mit Standardberechtigungen erstellt. Dies bedeutet, dass jeder mit dem Anwendungsschlüssel (der mit Ihrer App verteilt wird) auf die Daten in der Tabelle zugreifen und diese ändern kann. Jede Tabelle wird in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.

4. Klicken Sie auf die neue Tabelle **TodoItem** und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

	>[AZURE.NOTE]Neue Tabellen werden mit den Spalten "Id", "\_\_createdAt", "\_\_updatedAt" und "\_\_version" erstellt. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

6. Vergewissern Sie sich auf der Registerkarte **Konfigurieren**, dass `localhost` bereits im Feld **Anforderungen von Hostnamen zulassen** unter **Cross-Origin Resource Sharing (CORS)** eingetragen ist. Falls nicht, geben Sie `localhost` im Feld **Hostname** ein, und klicken Sie dann auf **Speichern**.


	> [AZURE.IMPORTANT]Wenn Sie die Schnellstart-App auf einem anderen Webserver als "localhost" bereitstellen, müssen Sie den Hostnamen des Webservers der Liste **Anforderungen von Hostnamen zulassen** hinzufügen. Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing (CORS)](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

##<a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

Ihr mobiler Dienst ist nun bereit, und Sie können die App aktualisieren, sodass Elemente in Mobile Services anstatt in einer lokalen Sammlung gespeichert werden.

3. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

4. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Mobile Service-URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

  	Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

1. Öffnen Sie die Projektdatei index.html in Ihrem Web-Editor und fügen Sie den folgenden Code zu den Skriptverweisen der Seite hinzu:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

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

8. Ersetzen Sie die **refreshTodoItems**-Methode durch den folgenden Code:

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

##<a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

4. Laden Sie die URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> erneut in einem Webbrowser, um die App zu starten.

    > [AZURE.NOTE]Wiederholen Sie die Schritte im ersten Abschnitt, falls Sie den Webserver neu starten müssen.

2. Geben Sie wie zuvor Text in das Feld **Neue Aufgabe eingeben** ein und klicken Sie auf **Hinzufügen**.

   	Dieser Code schickt ein neues einzufügendes Element an den mobilen Dienst.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.
  
   	Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

5. Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**.

  	Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

6. Suchen Sie die **RefreshTodoItems**-Methode in der Projektdatei "app.js", und ersetzen Sie die Codezeile, in der `query` definiert wird, durch die folgende Zeile:

   		var query = todoItemTable.where({ complete: false });

7. Laden Sie die Seite erneut und markieren Sie eines der anderen Elemente in der Liste.

   	Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Änderung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen für die Integration von Daten in HTML-Apps in Mobile Services behandelt. Nach Abschluss der Daten-Lernprogramme können Sie herausfinden, wie Sie Benutzer Ihrer App authentifizieren können. Probieren Sie eines der anderen Lernprogramme aus, z. B. [Authentifizierung zu Ihrer App hinzufügen]. Wenn Sie mit einem Cordova oder PhoneGap-App-Projekt arbeiten, erfahren Sie mehr über Push-Benachrichtigungen in [Push-Benachrichtigungen für Cordova-Apps mit Microsoft Azure](https://msdn.microsoft.com/magazine/dn879353.aspx).

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png

<!-- URLs. -->
[Erste Schritte mit mobilen Diensten]: mobile-services-html-get-started.md
[Authentifizierung zu Ihrer App hinzufügen]: mobile-services-html-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[GetStartedWithData app]: http://go.microsoft.com/fwlink/?LinkID=286345
[GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx

 

<!---HONumber=August15_HO7-->