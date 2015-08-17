In diesem Abschnitt ändern wir das Modell unserer Datenbank, indem wir ein neues Zeitstempelfeld namens **CompleteDate** hinzufügen. In diesem Feld wird die Zeit aufgezeichnet, zu der das todo-Element zuletzt abgeschlossen wurde. Entity Framework aktualisiert die Datenbank auf Grundlage unserer Modelländerung mittels einer von [DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621) abgeleiteten Standard-Datenbankinitialisiererklasse.

1. Erweitern Sie im Projektmappen-Explorer für Visual Studio den Ordner **App\_Start** im Aufgabenlistenserviceprojekt. Öffnen Sie die Datei "WebApiConfig.cs".

2. Beachten Sie in der Datei "WebApiConfig.cs", dass die Standard-Datenbankinitialisiererklasse von der `DropCreateDatabaseIfModelChanges`-Klasse abgeleitet wird. Dies bedeutet, dass jede Änderung am Modell dazu führt, dass die Tabelle gelöscht und dem neuen Modell entsprechend erneut erstellt wird. Die Daten in der Tabelle gehen also verloren, und es wird ein erneutes Seeding für die Tabelle ausgeführt. Ändern Sie die Seed-Methode des Datenbankinitialisierers so, dass die Seed-Daten wie folgt sind, und speichern Sie die Datei "WebApiConfig.cs".

    >[AZURE.NOTE]Bei Verwendung des Standard-Datenbankinitialisierers löscht Entity Framework die Datenbank und erstellt sie erneut, sobald es eine Datenmodelländerung in der Code First-Modelldefinition erkennt. Um eine Datenmodelländerung durchzuführen und bestehende Daten in der Datenbank beizubehalten, müssen Sie Code First-Migrationen verwenden. Weitere Informationen finden Sie unter [Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. Erweitern Sie im Projektmappen-Explorer für Visual Studio den Ordner **DataObjects** im Aufgabenlistenserviceprojekt. Öffnen Sie die Datei TodoItem.cs, und aktualisieren Sie die TodoItem-Klasse so, dass das Feld CompleteDate enthalten ist, wie folgt. Speichern Sie dann die TodoItem.cs-Datei.

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. Erweitern Sie im Projektmappen-Explorer für Visual Studio den Ordner **Controller** im Aufgabenlistenserviceprojekt. Öffnen Sie die Datei "TodoItemController.cs", und aktualisieren Sie die `PatchTodoItem`-Methode, sodass **CompleteDate** festgelegt wird, wenn sich die Eigenschaft **Complete** von "False" zu "True" ändert. Speichern Sie dann die Datei "TodoItemController.cs".

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. Erstellen Sie das Aufgabenlisten-.NET-Backend-Serviceprojekt erneut, und vergewissern Sie sich, dass keine Buildfehler vorliegen.

Als Nächstes aktualisieren Sie die Client-App, um die neuen **CompleteDate**-Daten anzuzeigen.

<!---HONumber=August15_HO6-->