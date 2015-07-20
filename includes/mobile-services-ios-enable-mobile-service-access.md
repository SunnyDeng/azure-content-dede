
Nun wird die Anwendung aktualisiert, damit sie mobile Dienste zum Speichern von Objekten in Azure Mobile Services anstelle der lokalen, speicherinternen Sammlung verwendet.

* Suchen Sie in **TodoService.h** die folgende Zeile:

```
// TODO - create an MSClient property
```

Ersetzen Sie diesen Kommentar mit der folgenden Zeile. Dadurch wird eine Eigenschaft erstellt, die den `MSClient` repräsentiert, welcher eine Verbindung zu dem Dienst herstellt.

```
@property (nonatomic, strong)   MSClient *client;
```


* Suchen Sie in **TodoService.m** die folgende Zeile:

```
// TODO - create an MSTable property for your items
```

Ersetzen Sie diesen Kommentar durch die folgende Zeile in der `@interface`-Deklaration. Dadurch wird eine Eigenschaftsdarstellung für die Tabelle der mobilen Dienste erstellt.

```
@property (nonatomic, strong)   MSTable *table;
```


* Klicken Sie im Verwaltungsportal auf **Mobile Dienste** und dann auf Ihren mobilen Dienst. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site-URL**. Klicken Sie dann auf **Schlüssel verwalten** und notieren Sie den **Anwendungsschlüssel**. Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.


* Suchen Sie in **TodoService.m** die folgende Zeile:

```
// Initialize the Mobile Service client with your URL and key.
```

Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu. Ersetzen Sie `APPURL` und `APPKEY` mit der Site-URL und dem Anwendungsschlüssel, die bzw. den Sie im vorherigen Schritt abgerufen haben.

```
self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];
```


* Suchen Sie in **TodoService.m** die folgende Zeile:

```
// Create an MSTable instance to allow us to work with the TodoItem table.
```

Ersetzen Sie diesen Kommentar mit der folgenden Zeile. Hierdurch wird die TodoItem-Tabelleninstanz erstellt.

```
self.table = [self.client tableWithName:@"TodoItem"];
```


* Suchen Sie in **TodoService.m** die folgende Zeile:

```
// Create a predicate that finds items where complete is false
```

Ersetzen Sie diesen Kommentar mit der folgenden Zeile. Hierdurch wird eine Abfrage zur Rückgabe sämtlicher Aufgaben gestellt, die noch nicht abgeschlossen wurden.

```
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
```


* Suchen Sie die folgende Zeile:

```
// Query the TodoItem table and update the items property with the results from the service
```

Ersetzen Sie diesen Kommentar und den nachfolgenden **completion**-Blockaufruf durch den folgenden Code:

```
[self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
{
self.items = [results mutableCopy];
   completion();
}];
```


* Suchen Sie die **addItem**-Methode, und ersetzen Sie den Text der Methode durch den folgenden Code: Dieser Code sendet eine Einfügeanforderung an den mobilen Dienst.

```
// Insert the item into the TodoItem table and add to the items array on completion
[self.table insert:item completion:^(NSDictionary *result, NSError *error) {
    NSUInteger index = [items count];
    [(NSMutableArray *)items insertObject:item atIndex:index];

    // Let the caller know that we finished
    completion(index);
}];
```


* Suchen Sie in der **completeItem**-Methode die folgende Codezeile mit vorangestellten Kommentarzeichen:

```
// Update the item in the TodoItem table and remove from the items array on completion
```

Ersetzen Sie den Text der Methode ab diesem Punkt bis zum Methodenende durch den folgenden Code. Durch diesen Code werden die TodoItems entfernt, nachdem diese als abgeschlossen markiert sind.

```
// Update the item in the TodoItem table and remove from the items array on completion
[self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

    // Get a fresh index in case the list has changed
    NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

    [mutableItems removeObjectAtIndex:index];

    // Let the caller know that we have finished
    completion(index);
}];
```


* Suchen Sie in der Datei "TodoListController.m" die **onAdd**-Methode, und überschreiben Sie sie mit folgendem Code:

```
- (IBAction)onAdd:(id)sender
{
    if (itemText.text.length  == 0)
    {
        return;
    }

    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
    UITableView *view = self.tableView;
    [self.todoService addItem:item completion:^(NSUInteger index)
    {
        NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        [view insertRowsAtIndexPaths:@[ indexPath ]
                    withRowAnimation:UITableViewRowAnimationTop];
    }];

    itemText.text = @"";
}
```

<!---HONumber=July15_HO2-->