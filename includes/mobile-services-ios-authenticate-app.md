

1. Öffnen Sie die Projektdatei "QSTodoListViewController.m",  und entfernen Sie in der **viewDidLoad**-Methode den folgenden Code, der die Daten erneut in die Tabelle lädt:

        [self refresh];

2.	Fügen Sie den folgenden Code unmittelbar nach der **viewDidLoad**-Methode hinzu:  

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
	<p>Wenn Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den oben an <strong>loginWithProvider</strong> übergebenen Wert in einen der folgenden Werte: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> der <em>windowsazureactivedirectory</em>.</p>
    </div>
		
3. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.
