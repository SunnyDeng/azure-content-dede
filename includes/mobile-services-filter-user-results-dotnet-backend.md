

Da Authentifizierung erforderlich ist, um auf Daten in der TodoItem-Tabelle zuzugreifen, können Sie den mobilen Services zugewiesenen userID-Wert zum Filtern der zurückgegebenen Daten verwenden.

> [WACOM.NOTE]**RequiresAuthorizationAttribute** sollte bei den nachfolgenden Methoden auf **User** **Authorizationlevel** angewendet sein. Dadurch wird nur authentifizierten Benutzer Tabellenzugang gewährt.

1.  In Visual Studio 2013 öffnen Sie Ihr Projekt für den mobilen Service, erweitern Sie den DataObjects-Ordner und öffnen Sie dann die Projektdatei TodoItem.cs.

    Die TodoItem-Klasse definiert das Datenobjekt, und Sie müssen eine UserId-Eigenschaft für Filterzwecke hinzufügen.

2.  Fügen Sie die folgende neue UserId-Eigenschaft zur **TodoItem**-Klasse hinzu:

         public string UserId { get; set; }

    > [WACOM.NOTE] Bei Verwendung des Standard-Datenbankinitialisierers löscht Entity Framework die Datenbank und erstellt sie erneut, sobald es eine Datenmodelländerung in der Code First-Modelldefinition erkennt. Um eine Datenmodelländerung durchzuführen und bestehende Daten in der Datenbank beizubehalten, müssen Sie Code First-Migrationen verwenden. Der Standard-Initialisierer kann nicht für eine SQL-Datenbank in Azure angewendet werden. Weitere Informationen finden Sie unter [Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells](/en-us/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations).

3.  Erweitern Sie im Projektmappen-Explorer den Controller-Ordner, öffnen Sie die Projektdatei TodoItemController.cs, und fügen Sie die folgende **using**-Anweisung hinzu:

         using Microsoft.WindowsAzure.Mobile.Service.Security;

    Die **TodoItemController**-Klasse implementiert Datenzugriff für die TodoItem-Tabelle.

4.  Suchen Sie die **PostTodoItem**-Methode, und fügen Sie den folgenden Code am Ende direkt vor der **return**-Anweisung ein:

         // Get the logged-in user.
         var currentUser = User as ServiceUser;

         // Set the user ID on the item.
         item.UserId = currentUser.Id;

    Dieser Code fügt einen UserId-Wert zum Element hinzu, bei dem es sich um die Benutzer-ID des authentifizierten Benutzers vor Einfügen in die TodoItem-Tabelle handelt.

5.  Suchen Sie die **GetAllTodoItems**-Methode und ersetzen Sie die vorhandene **return**-Anweisung durch die folgende Codezeile:

         // Get the logged-in user.
         var currentUser = User as ServiceUser;

         return Query().Where(todo => todo.UserId == currentUser.Id);

	Diese Abfrage filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält. Optional können Sie 

6.  das Projekt für den mobilen Service erneut auf Azure veröffentlichen.


