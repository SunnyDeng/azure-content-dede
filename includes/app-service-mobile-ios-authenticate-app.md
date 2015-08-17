

1. Öffnen Sie **QSTodoListViewController.m**, und fügen Sie folgende Methode hinzu:


        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }


    > [AZURE.NOTE]Wenn Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an **loginWithProvider** übergebenen Wert. Folgende Werte werden unterstützt: _microsoftaccount_, _facebook_, _twitter_, _google_ oder _windowsazureactivedirectory_.


2. Ändern Sie `viewDidLoad`, indem Sie `[self refresh]` am Ende durch Folgendes ersetzen:

        [self loginAndGetData];

3. Klicken Sie auf **Ausführen**, um die App zu starten, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an. Nach der Anmeldung sollten Sie die Todo-Liste anzeigen und Änderungen vornehmen können.

<!---HONumber=August15_HO6-->