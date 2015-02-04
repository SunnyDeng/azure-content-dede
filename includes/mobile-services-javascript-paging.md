

1. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm **Erste Schritte mit Daten** erstellt haben.

2. Drücken Sie die Taste **F5**, um die App auszuführen, geben Sie dann einen Text in **Insert a TodoItem** ein, und klicken Sie auf **Save**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

2. Ersetzen Sie in der Datei "default.js" die **RefreshTodoItems**-Methode durch folgenden Code:

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

3. Drücken Sie die Taste **F5**, um die App auszuführen.

  	Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

4. (Optional) Sie können den URI der Anfrage an den mobilen Service anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, z. B. Browser-Entwicklertools oder [Fiddler]. 

Beachten Sie, dass die **take(3)**-Methode in der Abfrage-URI in die Abfrageoption **$top=3** übersetzt wurde.

5. Aktualisieren Sie die **RefreshTodoItems**-Methode erneut mit folgendem Code:
            
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

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die drei darauffolgenden zurück. Dabei handelt es sich effektiv um die zweite "Seite" von Daten, wobei die Seitengröße hier drei Elemente beträgt.

    <div class="dev-callout"><b>Hinweis</b>
    <p>In diesem Lernprogramm werden der Einfachheit halber fest codierte Werte an die Methoden<strong>Take</strong> und <strong>Skip</strong> übergeben. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.  Sie können auch die <strong>includeTotalCount</strong>-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p>
    </div>

6. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an. 

   	Beachten Sie, dass die **skip(3)**-Methode in der Abfrage-URI in die Abfrageoption **$skip=3** übersetzt wurde.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412
