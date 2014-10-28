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

5.  Drücken Sie **F5**, um die App auszuführen.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

6.  (Optional) Sie können den URI der Anfrage an den mobilen Service anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler][Fiddler].

    Beachten Sie, dass die **take(3)**-Methode im Abfrage-URI in die Abfrageoption **$top=3** übersetzt wurde.

7.  Ersetzen Sie die **RefreshTodoItems**-Methode durch den folgenden Code:

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

    <div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm &uuml;bergibt zur Vereinfachung fest codierte Werte an die <strong>Take</strong>- und <strong>Skip</strong>-Methoden. Tats&auml;chliche Anwendungen k&ouml;nnen &auml;hnliche Abfragen mit einem Pagersteuerelement oder einer &auml;hnlichen Benutzersteuerung ausf&uuml;hren, um zur vorherigen bzw. n&auml;chsten Seite zu navigieren.  Alternativ k&ouml;nnen Sie die <strong>includeTotalCount</strong>-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p>
</div>

8.  (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an.

    Beachten Sie, dass die **skip(3)**-Methode im Abfrage-URI in die Abfrageoption **$skip=3** übersetzt wurde.

<!-- URLs -->

  [Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412
