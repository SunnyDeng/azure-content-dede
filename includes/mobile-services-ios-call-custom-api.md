## <a name="update-app"></a><span class="short-header">Aktualisieren der App </span>Aktualisieren der App zum Aufruf der benutzerdefinierten API

1.  Erstellen Sie eine Schaltfläche, sodass Sie auf diese klicken können, um die benutzerdefinierte API aufzurufen. Ziehen Sie eine **abgerundete Rechteckschaltfläche** aus der **Objektbibliothek** an der Unterseite des **Dienstprogramme**Bereichs an eine Stelle unter oder neben dem Textfeld. Doppelklicken Sie, um den Text **"Alle"** hinzuzufügen.

    Dadurch wird eine neue Schaltfläche **"Alle"** hinzugefügt.

2.  Öffnen Sie die Codedatei **QSTodoService.m**, suchen Sie die `refreshDataOnSuccess`-Methode, und stellen Sie sicher, dass diese den folgenden Code enthält:

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion
        {          
            // Create a predicate that finds items where complete is false
            NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

            // Query the TodoItem table and update the items property with the results from the service
            [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
            {
                [self logErrorIfNotNil:error];

                items = [results mutableCopy];

                // Let the caller know that we finished
                completion();
            }];                                 
        }

    Hierdurch werden die Elemente so gefiltert, dass abgeschlossene Elemente von der Abfrage nicht zurückgegeben werden.

3.  Nun verbinden wir dieses Objekt mit dem View Controller-Quellcode. **Klicken Sie mit gedrückter STRG-TASTE** auf die neue Schaltfläche **"Alle"**, und ziehen Sie sie mit der Maus vor die `@end`-Zeile in **QSTodoListViewController.h**. Verbinden Sie das Objekt mit einer neuen **Aktion** namens `onCompleteAll` in **QSTodoListViewController**. Xcode fügt automatisch die folgende Zeile vor der `@end`-Zeile ein:

           - (IBAction)onCompleteAll:(id)sender;

4.  Das Ziel dieser `onCompleteAll`-Methode ist die Verarbeitung des Click-Ereignisses für die neue Schaltfläche. Sie ruft eine neue `completeAll`-Methode auf, die wir unserer benutzerdefinierten Klasse hinzufügen, die wiederum eine POST-Anforderung an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Meldungsdialogfeld angezeigt, ebenso wie jegliche Fehler. Bearbeiten Sie **QSTodoListViewController.m**, indem Sie die folgende Implementierung vor der `@end`-Zeile einfügen:

           - (IBAction)onCompleteAll:(id)sender {
            [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
             {
                 if (error)
                 {
                     NSString* errorMessage = @"There was a problem! ";
                     errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Error!"
                                             message:errorMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 } else {
                     NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];                   
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Success!"
                                             message:successMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 }
             }];
           }

5.  Beachten Sie, dass sich der obige Code auf eine neue Methode `completeAll` bezieht, die noch nicht in **QSTodoService** definiert wurde. Bearbeiten Sie **QSTodoService.h**, und fügen Sie die folgende Zeile vor der `@end`-Zeile ein:

        - (void) completeAll:(MSAPIBlock)completion;

6.  Fügen Sie die entsprechende Implementierung von `completeAll` in **QSTodoService.m** vor der `@end`-Zeile ein. Wie JavaScript unterstützt iOS keine JSON-Serialisierung beliebiger Typen. Daher verfügt es auch über eine ziemlich einfache API zum Aufrufen benutzerdefinierter APIs, die aus der `invokeAPI`-Methode besteht.

        - (void) completeAll:(MSAPIBlock)completion
        {
            [self.client
             invokeAPI:@"completeall"
             body:nil
             HTTPMethod:@"POST"
             parameters:nil
             headers:nil
             completion:completion ];
        }

## <a name="test-app"></a>Testen der App

1.  Wählen Sie unter Xcode einen Emulator, den Sie bereitstellen (entweder iPhone oder iPad). Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste **Command+R**), um das Projekt neu zu erstellen und die App zu starten. Hierdurch wird der mit dem iOS SDK erstellte Azure Mobile Services-Client ausgeführt, der Einträge von Ihrem mobilen Service abfragt.

2.  Geben Sie Text in das Textfeld ein, und klicken Sie dann auf die Schaltfläche **+**. Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere Einträge hinzugefügt haben.

4.  Tippen Sie auf die Schaltfläche **Alle**. Ein Warnfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, wodurch alle Elemente aus der Liste gelöscht werden.

    ![][]

  []: ./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png
