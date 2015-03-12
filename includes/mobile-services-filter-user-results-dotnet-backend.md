

Da Authentifizierung erforderlich ist, um auf Daten in der TodoItem-Tabelle zuzugreifen, können Sie den mobilen Services zugewiesenen userID-Wert zum Filtern der zurückgegebenen Daten verwenden.

>[AZURE.NOTE]Für die unten aufgeführten Methoden muss das Attribut **AuthorizeLevel** bei **Authorizationlevel** des **Benutzers** angewandt werden. Dadurch wird nur authentifizierten Benutzer Tabellenzugang gewährt.

1. In Visual Studio 2013 öffnen Sie Ihr Projekt für den mobilen Service, erweitern Sie den DataObjects-Ordner und öffnen Sie dann die Projektdatei TodoItem.cs.

	Die TodoItem-Klasse definiert das Datenobjekt, und Sie müssen eine UserId-Eigenschaft für Filterzwecke hinzufügen.

2. Fügen Sie die folgende neue UserId-Eigenschaft zur **TodoItem**-Klasse hinzu:

		public string UserId { get; set; }

	>[AZURE.NOTE] Bei Verwendung des Standard-Datenbankinitialisierers löscht Entity Framework die Datenbank und erstellt sie erneut, sobald es eine Datenmodelländerung in der Code First-Modelldefinition erkennt. Um eine Datenmodelländerung durchzuführen und bestehende Daten in der Datenbank beizubehalten, müssen Sie Code First-Migrationen verwenden. Der Standard-Initialisierer kann nicht für eine SQL-Datenbank in Azure angewendet werden. Weitere Informationen finden Sie unter [Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells](/ de-de/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).

3. Erweitern Sie im Projektmappen-Explorer den Ordner "Controller", öffnen Sie die Projektdatei "TodoItemController.cs", und fügen Sie die folgende **Mit Hilfe von**-Anweisung hinzu:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

	Die Klasse **TodoItemController** führt den Datenzugriff auf die Tabelle "TodoItem" aus. 
 
4. Ermitteln Sie die Methode **PostTodoItem**, und fügen Sie den folgenden Code am Anfang der Methode hinzu:

		// Get the logged-in user.
	    var currentUser = User as ServiceUser;
	
	    // Set the user ID on the item.
	    item.UserId = currentUser.Id;

    Dieser Code fügt einen UserId-Wert zum Element hinzu, bei dem es sich um die Benutzer-ID des authentifizierten Benutzers vor Einfügen in die TodoItem-Tabelle handelt. 
	

5. Ermitteln Sie die Methode **GetAllTodoItems**, und ersetzen Sie die vorhandene Anweisung **Zurückgeben** durch die folgenden Codezeile:

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

   	Diese Abfrage filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält. 

6. Das Projekt für den mobilen Service erneut auf Azure veröffentlichen.
<!--HONumber=42-->
