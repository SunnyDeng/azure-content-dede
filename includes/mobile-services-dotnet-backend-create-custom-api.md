

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf den Controller-Ordner, erweitern Sie **Hinzufügen**, und klicken Sie dann auf **New Scaffolded Item**.

    Der Dialog "Add Scaffold" wird angezeigt.

2.  Erweitern Sie **Azure Mobile Services**, klicken Sie auf **Azure Mobile Services Custom Controller**, klicken Sie dann auf **Hinzufügen**, geben Sie einen **Controllernamen** für `CompleteAllController` an, und klicken Sie schließlich erneut auf **Hinzufügen**.

    ![](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

    Dadurch wird eine neue leere Controllerklasse namens **CompleteAllController** erstellt.

> [WACOM.NOTE]Wenn Ihr Dialog keine Mobile Services-spezifischen Gerüste umfasst, erstellen Sie stattdessen einen neuen **Web API Controller - Empty**. Fügen Sie in dieser neuen Controllerklasse eine öffentliche Eigenschaft **Services** hinzu, die den **ApiServices**-Typ zurückgibt. Diese Eigenschaft wird zum Zugriff auf Server-spezifische Einstellungen vom Controller aus verwendet.

1.  In der neuen Projektdatei CompleteAllController.cs fügen Sie die folgenden **using**-Anweisungen hinzu:

         using System.Threading.Tasks;
         using todolistService.Models;

    Ersetzen Sie im obigen Code `todolistService` durch den Namespace Ihres Projekts für den mobilen Service, der aus dem Namen des mobilen Services und dem Anhang `Service` bestehen sollte.

2.  Fügen Sie folgenden Code zum neuen Controller hinzu:

         // POST api/completeall        
         public Task<int> Post()
         {
             using (todolistContext context = new todolistContext())
             {
                 // Get the database from the context.
                 var database = context.Database;

                 // Create a SQL statement that sets all uncompleted items
                 // to complete and execute the statement asynchronously.
                 var sql = @"UPDATE TodoItems SET Complete = 1 " +
                             @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";
                 var result = database.ExecuteSqlCommandAsync(sql);

                 // Log the result.
                 Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                     result.ToString()));
                    
                 return result;
             }
         }

    Ersetzen Sie im obigen Code `todolistContext` durch den Namen von DbContext für Ihr Datenmodell, der aus dem Namen des mobilen Services und dem Anhang `Context` bestehen sollte. Dieser Code verwendet die [Datenbankklasse](http://msdn.microsoft.com/de-de/library/system.data.entity.database(v=vs.113).aspx), um direkt auf die **TodoItems**-Tabelle zuzugreifen und alle Elemente als abgeschlossen zu markieren. Diese Methode unterstützt eine POST-Anfrage, und die Anzahl der geänderten Zeilen wird an den Client als Ganzzahl zurückgegeben.

    > [WACOM.NOTE] Es werden Standardberechtigungen eingerichtet, das heißt, jeder Benutzer der App kann die benutzerdefinierte API aufrufen. Der Anwendungsschlüssel wird jedoch nicht verteilt bzw. sicher gespeichert und gilt daher nicht als sichere Anmeldeinformation. Deshalb sollten Sie in Erwägung ziehen, den Zugriff für Vorgänge, durch die Daten geändert werden oder die sich auf den mobilen Service auswirken, auf authentifizierte Benutzer zu beschränken.

Als Nächstes ändern Sie die Quickstart-App so, dass eine neue Schaltfläche und neuer Code hinzugefügt wird, der die neue benutzerdefinierte API asynchron aufruft.

