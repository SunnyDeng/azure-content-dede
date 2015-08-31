1. Öffnen Sie auf dem Mac **QSTodoListViewController.m** in Xcode, und fügen Sie folgende Methode hinzu. Ändern Sie _Facebook_ zu _MicrosoftAccount_, _Twitter_, _Google_ oder _WindowsAzureActiveDirectory_, wenn Sie Facebook nicht als Identitätsanbieter nutzen.

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

2. Ersetzen Sie `[self refresh]` in `viewDidLoad` durch Folgendes:

        [self loginAndGetData];

3. Klicken Sie **Ausführen**, um die App zu starten und melden Sie sich an. Nach der Anmeldung sollten Sie die Todo-Liste anzeigen und Änderungen vornehmen können.

<!---HONumber=August15_HO8-->