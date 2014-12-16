

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf den Controller-Ordner für das Mobildienstprojekt, erweitern Sie **Hinzufügen**, und klicken Sie dann auf **New Scaffolded Item**.

	Der Dialog "Add Scaffold" wird angezeigt.

2. Erweitern Sie **Azure Mobile Services**, klicken Sie auf **Azure Mobile Services Custom Controller**, klicken Sie dann auf **Hinzufügen**, geben Sie den **Controllernamen** `CompleteAllController` an, und klicken Sie anschließend erneut auf **Hinzufügen**.

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	Dadurch wird eine neue leere Controllerklasse namens **CompleteAllController** erstellt.

>[WACOM.NOTE]Wenn Ihr Dialogfeld keine Mobile Services-spezifischen Gerüste umfasst, erstellen Sie stattdessen einen neuen Controller vom Typ **Web API Controller - Empty**. Fügen Sie in dieser neuen Controllerklasse eine öffentliche Eigenschaft **Services** hinzu, die den **ApiServices**-Typ zurückgibt. Diese Eigenschaft wird verwendet, um vom Controller aus auf serverspezifische Einstellungen zuzugreifen.

3. Fügen Sie der neuen Projektdatei "CompleteAllController.cs" die folgenden **using**-Anweisungen hinzu:

		using System.Threading.Tasks;
		using todolistService.Models;

	Ersetzen Sie im obigen Code `todolistService` durch den Namespace Ihres Mobildienstprojekts, der aus dem Namen des mobilen Diensts und dem angehängten Zusatz `Service` bestehen sollte. 

4. Fügen Sie dem Namespace in "CompleteAllController.cs" die folgende Klassendefinition hinzu. Diese Klasse umschließt die an den Client gesendete Antwort.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public Int32 count;
        }


5. Fügen Sie folgenden Code zum neuen Controller hinzu:

	    // POST api/completeall        
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolistService.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.count.ToString()));
                
                return result;
            }
        }

	Ersetzen Sie im obigen Code `todolistContext` durch den Namen von DbContext für Ihr Datenmodell, der aus dem Namen des mobilen Diensts und angehängten Zusatz `Context` bestehen sollte. Ersetzen Sie auch den Schemanamen in der UPDATE-Anweisung durch den Namen Ihres mobilen Diensts. 

	In diesem Code wird die [Datenbankklasse](http://msdn.microsoft.com/de-de/library/system.data.entity.database.aspx) verwendet, um direkt auf die **TodoItems**-Tabelle zuzugreifen und alle Elemente als abgeschlossen zu markieren. Diese Methode unterstützt eine POST-Anforderung, und die Anzahl der geänderten Zeilen wird als Ganzzahl an den Client zurückgegeben.

	> [WACOM.NOTE] Die Standardberechtigungen werden festgelegt, und das heißt, dass jeder Benutzer der App die benutzerdefinierte API aufrufen kann. Der Anwendungsschlüssel wird jedoch nicht verteilt bzw. sicher gespeichert und gilt daher nicht als sichere Anmeldeinformation. Deshalb sollten Sie in Erwägung ziehen, den Zugriff für Vorgänge, durch die Daten geändert werden oder die sich auf den mobilen Service auswirken, auf authentifizierte Benutzer zu beschränken. 

Als Nächstes ändern Sie die Quickstart-App so, dass eine neue Schaltfläche und neuer Code hinzugefügt wird, der die neue benutzerdefinierte API asynchron aufruft.

