

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner "Controller", erweitern Sie **Hinzufügen**, und klicken Sie dann auf **Neu erstelltes Element**. Der Dialog "Add Scaffold" wird angezeigt.

2. Erweitern Sie **Azure Mobile Services**, klicken Sie auf **Benutzerdefinierter Controller Azure Mobile Services**, klicken Sie dann auf **Hinzufügen**, geben Sie den **Controllernamen`CompleteAllController` ** an, und klicken Sie anschließend erneut auf **Hinzufügen**.

	![Web-API-Dialogfeld "Gerüst hinzufügen"](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	Dadurch wird eine neue leere Controllerklasse namens **CompleteAllController** erstellt.

	>[AZURE.NOTE]Wenn Ihr Dialogfeld keine Mobile Services-spezifischen Scaffolds umfasst, erstellen Sie stattdessen einen neuen Controller vom Typ **Web API Controller - Empty**. Fügen Sie in dieser neuen Controllerklasse eine öffentliche Eigenschaft **Services** hinzu, die den **ApiServices**-Typ zurückgibt. Diese Eigenschaft wird zum Zugriff auf Server-spezifische Einstellungen vom Controller aus verwendet.

3. Fügen Sie der Datei **CompleteAllController.cs** folgende **using**-Anweisungen hinzu. Ersetzen Sie `todolistService` durch den Namespace Ihres Projekts für den mobilen Dienst, der aus dem Namen des mobilen Diensts und dem Präfix `Service` bestehen sollte.

		using System.Threading.Tasks;
		using todolistService.Models;

4. Fügen Sie in **CompleteAllController.cs** die folgende Klasse als Wrapper für die an den Client gesendete Antwort hinzu.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }

5. Fügen Sie folgenden Code zum neuen Controller hinzu. Ersetzen Sie `todolistContext` durch den Namen von DbContext für Ihr Datenmodell, der aus dem Namen des mobilen Diensts und dem Präfix `Context` bestehen sollte. Ersetzen Sie ebenso den Schemanamen in der UPDATE-Anweisung durch den Namen Ihres mobilen Diensts. Dieser Code verwendet die [Datenbankklasse](http://msdn.microsoft.com/library/system.data.entity.database.aspx), um direkt auf die **TodoItems**-Tabelle zuzugreifen und alle Elemente als abgeschlossen zu markieren. Diese Methode unterstützt eine POST-Anfrage, und die Anzahl der geänderten Zeilen wird an den Client als Ganzzahl zurückgegeben.


	    // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

	> [AZURE.NOTE]Mit den Standardberechtigungen kann jeder Benutzer mit dem App-Schlüssel die benutzerdefinierte API aufrufen. Der Anwendungsschlüssel wird jedoch nicht verteilt bzw. sicher gespeichert und gilt daher nicht als sichere Anmeldeinformation. Erwägen Sie, den Zugriff nur authentifizierten Benutzern zu gestatten, um die Sicherheit zu erhöhen.

<!---HONumber=62-->