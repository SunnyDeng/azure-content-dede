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

    <div class="dev-callout"><b>Hinweis</b>
<p>Falls Sie einen anderen Identit&auml;tsanbieter als Facebook verwenden, &auml;ndern Sie den an <strong>oginWithProvider</strong> oben &uuml;bergebenen Wert auf einen der folgenden Werte: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> oder <em>windowsazureactivedirectory</em>.</p>
</div>

3.  Klicken Sie auf die Schaltfläche **Run**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

    Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.


