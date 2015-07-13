

1. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm **Erste Schritte mit Daten** geändert haben.

2. Drücken Sie die Taste **F5**, um die App auszuführen, geben Sie dann einen Text in **Insert a TodoItem** ein, und klicken Sie auf **Save**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

2. Ersetzen Sie in der Datei MainPage.xaml.cs die vorhandene **RefreshTodoItems**-Methode durch den folgenden Code:

        private async void RefreshTodoItems()
        {
            // Define a filtered query that returns the top 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                            .Where(todoItem => todoItem.Complete == false)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

  	Diese Abfrage gibt bei Ausführung während der Datenbindung die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

3. Drücken Sie **F5**, um die App auszuführen.

  	Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

4. (Optional) Sie können den URI der Anfrage an den mobilen Service anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler].

   	Beachten Sie, dass die `Take(3)`-Methode im Abfrage-URI in die Abfrageoption `$top=3` übersetzt wurde.

5. Ersetzen Sie die **RefreshTodoItems**-Methode durch den folgenden Code:
            
        private async void RefreshTodoItems()
        {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                           .Where(todoItem => todoItem.Complete == false)
                           .Skip(3)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dabei handelt es sich effektiv um die zweite "Seite" von Daten, die Seitengröße beträgt dabei drei Elemente.

    >[AZURE.NOTE]Dieses Lernprogramm übergibt zur Vereinfachung fest codierte Werte an die Methoden <strong>Take</strong> und <strong>Skip</strong>. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die <strong>IncludeTotalCount</strong>-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

6. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an.

   	Beachten Sie, dass die `Skip(3)`-Methode im Abfrage-URI in die Abfrageoption `$skip=3` übersetzt wurde.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412

<!---HONumber=62-->