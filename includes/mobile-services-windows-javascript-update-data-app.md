

1. Fügen Sie in der Skriptdatei "default.js" direkt unterhalb der Codezeile, in der die Liste "todoItems" definiert wird, folgende Funktionsdefinition ein:
 
        // Add a filter that adds a header to prevent caching. This makes sure that the 
		// latest data is returned when the 'Refresh; button is clicked.        
        var noCachingFilter = function (request, next, callback) {
            if (request.type === 'GET' && !request.headers['If-Modified-Since']) {
                request.headers['If-Modified-Since'] = 'Mon, 27 Mar 1972 00:00:00 GMT';
            }
            next(request, callback);
        };

	Dadurch wird eine Filterfunktion definiert, die den  `If-Modified-Since`-Header hinzufügt, um die clientseitige Zwischenspeicherung zu verhindern.
 
2. Heben Sie dann die Auskommentierung der folgenden Codezeile auf, oder fügen Sie eine entsprechende Codezeile hinzu, und ersetzen Sie `<yourClient>` durch die Variable, die der Datei "service.js" beim Verbinden Ihres Projekts mit dem mobilen Dienst hinzugefügt wurde:

		var todoTable = <yourClient>.withFilter(noCachingFilter).getTable('TodoItem');

   	This code creates a proxy object (**todoTable**) for the new database table, using the caching filter. 

3. Replace the **InsertTodoItem** function with the following code:

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

3. Ersetzen Sie die Funktion **RefreshTodoItems** durch den folgenden Code:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	Damit wird die Bindung an die Sammlung von Objekten in der Tabelle "todoTable" festgelegt, die alle vom mobilen Dienst zurückgegebenen **TodoItem**-Objekte enthält. 

4. Ersetzen Sie die Funktion **UpdateCheckedTodoItem** durch den folgenden Code:
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.<!--HONumber=42-->
