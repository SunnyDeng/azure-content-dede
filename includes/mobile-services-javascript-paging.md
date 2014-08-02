

1.  Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm **Erste Schritte mit Daten** geändert haben.

2.  Drücken Sie die Taste **F5**, um die App auszuführen, geben Sie dann einen Text in **Insert a TodoItem** ein, und klicken Sie auf **Save**.

3.  Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Ersetzen Sie in der Datei default.js die vorhandene **RefreshTodoItems**-Methode durch den folgenden Code:

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

1.  Drücken Sie **F5**, um die App auszuführen.

	Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

1.  (Optional) Sie können den URI der Anfrage an den mobilen Service anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler](http://go.microsoft.com/fwlink/?LinkID=262412).

        Beachten Sie, dass die **take(3)**-Methode in der Abfrage-URI als **$top=3** übersetzt wurde.

2.  Ersetzen Sie die **RefreshTodoItems**-Methode durch den folgenden Code:

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

	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    **Hinweis**

    Dieses Lernprogramm übergibt zur Vereinfachung fest codierte Werte an die **Take**- und **Skip**-Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die **includeTotalCount**-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

3.  (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an.

	Beachten Sie, dass die **skip(3)**-Methode in der Abfrage-URI als **$skip=3** übersetzt wurde.


