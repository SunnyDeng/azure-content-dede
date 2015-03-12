

1. Öffnen Sie die Projektdatei "QSTodoListViewController.m", und entfernen Sie in der Methode **ViewDidLoad** den folgenden Code, der die Daten in die Tabelle lädt:

        [self refresh];

2.	Fügen Sie nach der Methode **ViewDidLoad** den folgenden Code hinzu:  

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

    > [AZURE.NOTE] Wenn Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den oben für **LoginWithProvider** eingegebenen Wert auf einen der folgenden: _microsoftaccount_, _facebook_, _twitter_, _google_, oder _windowsazureactivedirectory_.
		
3. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.
\<!--HONumber=42-->
