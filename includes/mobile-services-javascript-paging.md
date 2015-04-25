

1. Öffnen Sie in Visual Studio das Projekt, das Sie geändert haben, wenn Sie das Lernprogramm **Erste Schritte mit Daten** abgeschlossen haben.

2. Drücken Sie die **F5**-Taste, um die App auszuführen, geben Sie anschließend einen Text in **Ein TodoItem einfügen**, und klicken Sie auf **Speichern**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

2. Ersetzen Sie in der Datei "default.js" die Methode **RefreshTodoItems** durch den folgenden Code:

        var refreshTodoItems = function () {
            // Define a filtered query that returns the top 3 items.
            todoTable.where({ complete: false })
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

  	Diese Abfrage gibt bei Ausführung während der Datenbindung die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

3. Drücken Sie die **F5**-Taste, um die App auszuführen.

  	Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

4. (Optional) Sehen Sie den URI der an den mobilen Dienst gesendeten Anfrage mithilfe von Nachrichteninspektionssoftware, wie z. B. Browser-Entwicklertools oder [Fiddler] ein. 

   	Beachten Sie, dass die Methode **take(3)** in die Abfrageoption **$top = 3** im Abfrage-URI übertragen wurde.

5. Aktualisieren Sie die Methode **RefreshTodoItems** noch einmal mit dem folgenden Code:
            
        var refreshTodoItems = function () {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            todoTable.where({ complete: false })
                .skip(3)
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die drei darauffolgenden wieder. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    > [AZURE.NOTE] In diesem Lernprogramm wird ein vereinfachtes Szenario verwendet, bei dem hartcodierte Paging-Werte an die Methoden **Nehmen** und **Überspringen** weitergegeben werden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.  Sie können auch die Methode **IncludeTotalCount** aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den ausgelagerten Daten zu erhalten.

6. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an. 

   	Beachten Sie, dass die Methode **take(3)** in die Abfrageoption **$top = 3** im Abfrage-URI übertragen wurde.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412
<!--HONumber=42-->
