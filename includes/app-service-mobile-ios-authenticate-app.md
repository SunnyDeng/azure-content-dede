**Objective-C**:

1. Öffnen Sie auf dem Mac _QSTodoListViewController.m_ in Xcode, und fügen Sie folgende Methode hinzu. Wenn Sie Google nicht als Identitätsanbieter nutzen, ändern Sie _google_ zu _microsoftaccount_, _twitter_, _facebook_ oder _windowsazureactivedirectory_. Wenn Sie Facebook verwenden, [müssen Sie die Facebook-Domänen der Positivliste in Ihrer App hinzufügen](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. In _QSTodoListViewController.m_ ersetzen Sie `[self refresh]` in `viewDidLoad` durch Folgendes:

            [self loginAndGetData];

3. Klicken Sie _Ausführen_, um die App zu starten und melden Sie sich an. Nach der Anmeldung sollten Sie die Todo-Liste anzeigen und Änderungen vornehmen können.

**Swift**:

1. Öffnen Sie auf dem Mac _ToDoTableViewController.swift_ in Xcode, und fügen Sie folgende Methode hinzu. Wenn Sie Google nicht als Identitätsanbieter nutzen, ändern Sie _google_ zu _microsoftaccount_, _twitter_, _facebook_ oder _windowsazureactivedirectory_. Wenn Sie Facebook verwenden, [müssen Sie die Facebook-Domänen der Positivliste in Ihrer App hinzufügen](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser != nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Entfernen Sie in _ToDoTableViewController.swift_ die Zeilen `self.refreshControl?.beginRefreshing()` und `self.onRefresh(self.refreshControl)` am Ende von `viewDidLoad()`. Fügen Sie an deren Stelle einen Aufruf von `loginAndGetData()` ein:

            loginAndGetData()

3. Klicken Sie _Ausführen_, um die App zu starten und melden Sie sich an. Nach der Anmeldung sollten Sie die Todo-Liste anzeigen und Änderungen vornehmen können.

<!---HONumber=AcomDC_0204_2016-->