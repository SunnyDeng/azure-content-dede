

1.  Öffnen Sie die Projektdatei QSTodoListViewController.m, und entfernen Sie in der **viewDidLoad**-Methode den folgenden Code, der die Daten erneut in die Tabelle lädt:

         [self refresh];

2.  Unmittelbar nach der **viewDidLoad**-Methode fügen Sie den folgenden Code ein:

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;
                
            if (client.currentUser != nil) {
                return;
            }
                
            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

    **Hinweis**

    Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an **oginWithProvider** oben übergebenen Wert auf einen der folgenden Werte: *microsoftaccount*, *facebook*, *twitter* oder *google*.

3.  Klicken Sie auf die Schaltfläche **Run**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

	Wenn Sie erfolgreich angemeldet sind, sollte die App ohne Fehler ausgeführt werden, und Sie sollten in der Lage sein, Mobile Services abzufragen und Daten zu aktualisieren.


