
##<a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API

1. Öffnen Sie die Datei "default.html" in Ihrem Schnellstartprojekt in Visual Studio, suchen Sie die **Schaltfläche** Element mit dem Namen `buttonRefresh`, und fügen Sie das folgende neue Element direkt dahinter: 

		<button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

	Auf diese Weise wird eine neue Schaltfläche zur Seite hinzugefügt. 

2. Öffnen Sie die Codedatei "default.js" im Projektordner  `js`, suchen Sie die **refreshTodoItems**-Funktion, und stellen Sie sicher, dass diese Funktion den folgenden Code enthält:

	    todoTable.where({ complete: false })
	       .read()
	       .done(function (results) {
	           todoItems = new WinJS.Binding.List(results);
	           listItems.winControl.itemDataSource = todoItems.dataSource;
	       });            

	Hierdurch werden die Elemente so gefiltert, dass abgeschlossene Elemente von der Abfrage nicht zurückgegeben werden.

3. Nach der **refreshTodoItems**-Funktion fügen Sie den folgenden Code hinzu:

		var completeAllTodoItems = function () {
		    var okCommand = new Windows.UI.Popups.UICommand("OK");
		
		    // Asynchronously call the custom API using the POST method. 
		    mobileService.invokeApi("completeall", {
		        body: null,
		        method: "post"
		    }).done(function (results) {
		        var message = results.result.count + " item(s) marked as complete.";
		        var dialog = new Windows.UI.Popups.MessageDialog(message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done(function () {
		            refreshTodoItems();
		        });
		    }, function (error) {
		        var dialog = new Windows.UI.Popups
		            .MessageDialog(error.message);
		        dialog.commands.append(okCommand);
		        dialog.showAsync().done();
		    });
		};

        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });

	Diese Methode verarbeitet das **Click**-Ereignis für die neue Schaltfläche. Die **InvokeApiAsync**-Methode wird auf dem Client aufgerufen und sendet eine POST-Anforderung an die neue benutzerdefinierte API. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Meldungsdialogfeld angezeigt, ebenso wie jegliche Fehler.

## <a name="test-app"></a>Testen der App

1. Drücken Sie in Visual Studio die **F5**-Taste, um das Projekt neu zu erstellen, und starten Sie die App.

2. Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Speichern**.

3. Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4. Klicken Sie auf die Schaltfläche **Complete All**.

  	![](./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png)

	Ein Meldungsdialogfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, um alle Elemente aus der Liste zu löschen.

<!--HONumber=52-->