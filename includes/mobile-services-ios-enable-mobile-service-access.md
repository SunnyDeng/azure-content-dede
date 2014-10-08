Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Wenn Sie das [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) noch nicht installiert haben, können Sie es nun installieren.

2.  Öffnen Sie im Project Navigator unter Xcode sowohl die Datei "TodoService.m" als auch die Datei "TodoService.h", die sich im Schnellstart-Ordner befinden. Fügen Sie außerdem den folgenden Import-Ausdruck hinzu:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  Ermitteln Sie in der Datei "ToDoService.h" die folgende kommentierte Codezeile:

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        @property (nonatomic, strong)   MSClient *client;

    Dadurch wird eine Eigenschaft erstellt, die den MSClient repräsentiert, welcher eine Verbindung zu dem Dienst herstellt.

4.  Ermitteln Sie in der Datei "TodoService.m" die folgende kommentierte Codezeile:

        // Create an MSTable property for your items. 

    Fügen Sie nach diesem Kommentar die folgende Codezeile innerhalb der @interface-Deklaration hinzu:

        @property (nonatomic, strong)   MSTable *table;

    Dadurch wird eine Eigenschaftsdarstellung für die Tabelle der mobilen Dienste erstellt.

5.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

6.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

    ![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

    Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

7.  Öffnen Sie erneut unter Xcode die Datei "TodoService.m", und ermitteln Sie die folgende kommentierte Codezeile:

        // Initialize the Mobile Service client with your URL and key.

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    Hierdurch wird eine Instanz des Mobile Services-Client erstellt.

8.  Ersetzen Sie die Werte von **APPURL** und **APPKEY** in diesem Code durch die URL und den Anwendungsschlüssel aus dem mobilen Dienst, den Sie in Schritt 6 erworben haben.

9.  Ermitteln Sie die folgende kommentierte Codezeile:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        self.table = [self.client getTable:@"TodoItem"];

    Hierdurch wird die TodoItem-Tabelleninstanz erstellt.

10. Ermitteln Sie die folgende kommentierte Codezeile:

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Hierdurch wird eine Abfrage zur Rückgabe sämtlicher Aufgaben gestellt, die noch nicht abgeschlossen wurden.

11. Ermitteln Sie die folgende kommentierte Codezeile:

        // Query the TodoItem table and update the items property with the results from the service.

    Ersetzen Sie diesen Kommentar und den nachfolgenden **Fertigstellung**-Blockaufruf durch den folgenden Code:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. Ermitteln Sie die Methode **addItem**, und fügen Sie dem Text der Methode den folgenden Code hinzu:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Dieser Code sendet eine Einfügeanforderung an den mobilen Dienst.

13. Ermitteln Sie die Methode **completeItem**, und fügen Sie dem Text der Methode den folgenden Code hinzu:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    Durch diesen Code werden die TodoItems entfernt, nachdem diese als abgeschlossen markiert sind.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

  
