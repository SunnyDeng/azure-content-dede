
1. Fügen Sie in der Datei "MainPage.xaml.cs" die folgenden "using"-Anweisungen hinzu, oder heben Sie deren Auskommentierung auf: 

		using Microsoft.WindowsAzure.MobileServices;

2. Ersetzen Sie die TodoItem-Klassendefinition durch den folgenden Code: 

	    public class TodoItem
	    {
	        public string Id { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
	        public string Text { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
	        public bool Complete { get; set; }
	    }
	
	Mit dem **JsonPropertyAttribute**-Attribut wird die Zuordnung von Eigenschaftsnamen im Clienttyp zu Spaltennamen in der zugrunde liegenden Datentabelle definiert.

	>[AZURE.NOTE] In einem universellen Windows-App-Projekt wird die TodoItem-Klasse in der separaten Codedatei im gemeinsam genutzten Ordner "DataModel" definiert.

3. Löschen Sie in der Datei "MainPage.xaml.cs" die Zeile mit der Definition der vorhandenen Eintragssammlung, oder kommentieren Sie diese Zeile aus. Heben Sie dann die Auskommentierung der folgenden Zeilen auf, oder fügen Sie die entsprechenden Zeilen hinzu, und ersetzen Sie _&lt;yourClient&gt;_ durch das Feld  `MobileServiceClient`, das der Datei "App.xaml.cs" beim Verbinden Ihres Projekts mit dem mobilen Dienst hinzugefügt wurde: 

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	Dieser Code erstellt eine mobile dienstunterstützende Bindungssammlung (items) und eine Proxyklasse für die Datenbanktabelle (todoTable). 

4. Entfernen Sie aus der **InsertTodoItem**-Methode die Codezeile, in der die **TodoItem.Id**-Eigenschaft festgelegt wird, fügen Sie der Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf: 

		await todoTable.InsertAsync(todoItem);


	Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt. 

5. Ersetzen Sie die **RefreshTodoItems**-Methode durch den folgenden Code: 

		private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

	Damit wird die Bindung an die Sammlung der Objekte in  `todoTable` festgelegt, die alle vom mobilen Dienst zurückgegebenen **TodoItem**-Objekte enthält. Wenn beim Ausführen der Abfrage ein Problem auftritt, wird ein Meldungsfeld geöffnet, in dem die Fehler angezeigt werden. 

6. Fügen Sie der **UpdateCheckedTodoItem**-Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf: 

		await todoTable.UpdateAsync(item);

	Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet. 

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.


<!--HONumber=42-->
