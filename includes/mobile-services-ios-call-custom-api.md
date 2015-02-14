
<h2><a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API</h2>

1. Erstellen Sie eine Schaltfläche, sodass Sie auf diese klicken können, um die benutzerdefinierte API aufzurufen. Ziehen Sie eine **abgerundete Rechteckschaltfläche** aus der **Objektbibliothek**, die sich am Ende des Bereichs **Dienstprogramme** befindet, und platzieren Sie sie unter oder neben dem Textfeld. Doppelklicken Sie, um den Text **"Alle"** hinzuzufügen. 

	Dadurch wird eine neue Schaltfläche **"Alle"** hinzugefügt.

2. Öffnen Sie die Codedatei **QSTodoService.m**, ermitteln Sie die `refreshDataOnSuccess`-Methode, und stellen Sie sicher, dass sie den folgenden Code enthält:

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

3. Nun verbinden wir dieses Objekt mit dem View Controller-Quellcode. **Klicken Sie mit gedrückter Strg-Taste** auf die neue Schaltfläche **"Alle"**, und ziehen Sie die Maus bis vor die Zeile "@end" in **QSTodoListViewController.h**. Verbinden Sie das Objekt in **QSTodoListViewController** mit einer neuen **Aktion** mit dem Namen `onCompleteAll`. Xcode fügt automatisch die folgende Zeile vor der Zeile "@end" ein:

		   - (IBAction)onCompleteAll:(id)sender;

4. Das Ziel dieser `onCompleteAll`-Methode besteht darin, das Click-Ereignis für die neue Schaltfläche abzuwickeln. Es wird eine neue `completeAll`-Methode aufgerufen, die wir zu unserer benutzerdefinierten Klasse hinzufügen, die wiederum eine POST-Anforderung an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Nachrichtendialog angezeigt, ebenso wie jegliche Fehler. Bearbeiten Sie **QSTodoListViewController.m**, um die folgende Implementierung vor der `@end`-Zeile hinzuzufügen:

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

5. Beachten Sie, dass der obigen Code sich auf eine neue Methode `completeAll` bezieht, die noch nicht in **QSTodoService** definiert wurde. Bearbeiten Sie **QSTodoService.h**, und fügen Sie die folgende Implementierung vor der Zeile "@end" hinzu:

		- (void) completeAll:(MSAPIBlock)completion;

6. Fügen Sie die entsprechende Implementierung von `completeAll` in **QSTodoService.m** vor der Zeile "@end" hinzu. iOS ähnelt JavaScript darin, dass es JSON-Serialisierung beliebiger Typen nicht unterstützt. Es hat auch eine ziemlich einfache API zum Aufrufen benutzerdefinierter APIs, die aus der  `invokeAPI`-Methode besteht. 

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

1. Wählen Sie unter Xcode einen Emulator für die Anwendung aus (entweder iPhone oder iPad), drücken Sie die Schaltfläche **Ausführen** (oder **Befehlstaste+R**), um das Projekt zu erneuern, und starten Sie die App. Hierdurch wird der mit dem iOS SDK erstellte Microsoft Azure Mobile Services-Client ausgeführt, der Einträge von Ihrem mobilen Service abfragt.

2. Geben Sie einen Text in das Textfeld ein, und klicken Sie anschließend auf die **+**-Schaltfläche. Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere Einträge hinzugefügt haben.

4. Tippen Sie auf die Schaltfläche **Alle**. Ein Warnfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, wodurch alle Elemente aus der Liste gelöscht werden.

  	![](./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png)
<!--HONumber=42-->
