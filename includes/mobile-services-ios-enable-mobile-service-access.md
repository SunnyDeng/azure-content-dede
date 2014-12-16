
Ihr mobiler Dienst ist nun bereit, und Sie können die App aktualisieren, sodass Elemente in Mobile Services anstatt in einer lokalen Sammlung gespeichert werden.

1. Sofern Sie das [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) noch nicht installiert haben, installieren Sie es jetzt. Kopieren Sie nach der Installation das Verzeichnis "WindowsAzureMobileServices.framework", und überschreiben Sie damit das Verzeichnis "WindowsAzureMobileServices.framework" aus dem heruntergeladenen Projekt. Auf diese Weise wird sichergestellt, dass Sie das neueste Azure Mobile Services SDK verwenden.

2. Suchen Sie in der Datei "TodoService.h" die folgende Codezeile mit vorangestellten Kommentarzeichen:

        // TODO - create an MSClient proeprty

   	Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        @property (nonatomic, strong)   MSClient *client;

   	Dadurch wird eine Eigenschaft erstellt, die den MSClient repräsentiert, welcher eine Verbindung zu dem Dienst herstellt.

3. Ermitteln Sie in der Datei "TodoService.m" die folgende kommentierte Codezeile:

        // TODO - create an MSTable property for your items

   	Fügen Sie nach diesem Kommentar die folgende Codezeile innerhalb der @interface-Deklaration hinzu:

        @property (nonatomic, strong)   MSTable *table;

   	Dadurch wird eine Eigenschaftsdarstellung für die Tabelle der mobilen Dienste erstellt.

4. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

5. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie die **Site-URL**. Klicken Sie anschließend auf **Schlüssel verwalten,**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

6. Öffnen Sie erneut unter Xcode die Datei "TodoService.m", und ermitteln Sie die folgende kommentierte Codezeile:

        // Initialize the Mobile Service client with your URL and key.

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Hierdurch wird eine Instanz des Mobile Services-Client erstellt.

7. Ersetzen Sie die Werte von **APPURL** und **APPKEY** in diesem Code durch die URL und den Anwendungsschlüssel aus dem mobilen Dienst, den Sie in Schritt 6 bezogen haben.

8. Ermitteln Sie die folgende kommentierte Codezeile:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        self.table = [self.client tableWithName:@"TodoItem"];

    Hierdurch wird die TodoItem-Tabelleninstanz erstellt.

9. Ermitteln Sie die folgende kommentierte Codezeile:

 	    // Create a predicate that finds items where complete is false

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Hierdurch wird eine Abfrage zur Rückgabe sämtlicher Aufgaben gestellt, die noch nicht abgeschlossen wurden.

10. Ermitteln Sie die folgende kommentierte Codezeile:

        // Query the TodoItem table and update the items property with the results from the service

   	Ersetzen Sie diesen Kommentar und den nachfolgenden **completion**-Blockaufruf durch den folgenden Code:

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. Suchen Sie die **addItem**-Methode, und ersetzen Sie den Text der Methode durch den folgenden Code:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Dieser Code sendet eine Einfügeanforderung an den mobilen Dienst.

12. Suchen Sie in der **completeItem**-Methode die folgende Codezeile mit vorangestellten Kommentarzeichen:

        // Update the item in the TodoItem table and remove from the items array on completion

    Ersetzen Sie den Text der Methode ab diesem Punkt bis zum Methodenende durch den folgenden Code:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	Durch diesen Code werden die TodoItems entfernt, nachdem diese als abgeschlossen markiert sind.

13. Suchen Sie in der Datei "TodoListController.m" die **onAdd**-Methode, und überschreiben Sie sie mit folgendem Code:

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


Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.
