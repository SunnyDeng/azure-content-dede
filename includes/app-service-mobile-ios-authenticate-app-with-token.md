
Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, die erfordert, dass der Client bei jedem Start der App sowohl den Identitätsanbieter als auch App Service kontaktiert. Dieses Verfahren ist ineffizient. Ein besserer Ansatz ist es daher, den von App Service zurückgegebenen Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden.

1. Als Methode zum Verschlüsseln und Speichern von Authentifizierungstokens auf einem iOS-Client wird der iOS-Schlüsselbund empfohlen. In diesem Lernprogramm wird [SSKeychain](https://github.com/soffes/sskeychain) verwendet – ein einfacher Wrapper für den iOS-Schlüsselbund. Folgen Sie den Anweisungen auf der SSKeychain-Seite und fügen Sie ihn Ihrem Projekt hinzu. Überprüfen Sie, ob die Einstellung für das **Aktivieren der Module** in den **Buildeinstellungen** des Projekts aktiviert ist (Abschnitt **Apple LLVM - Sprachen - Module**.)

2. Öffnen Sie **QSTodoListViewController.m**, und fügen Sie folgenden Code hinzu:


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. Ändern Sie in der `loginAndGetData`-Methode den Abschlussblock des `loginWithProvider:controller:animated:completion:`-Aufrufs, indem Sie `saveAuthInfo` direkt vor der Zeile `[self refresh]` einen Aufruf hinzufügen. Mit diesem Aufruf werden die Eigenschaften für Benutzer-ID und Token gespeichert:

				[self saveAuthInfo];

4. Sie sollten außerdem die Benutzer-ID und den Token beim Starten der App laden. Fügen Sie in der `viewDidLoad`-Methode in **QSTodoListViewController.m** einen Aufruf von "loadAuthInfo" hinzu, direkt nachdem `self.todoService` initialisiert wurde.

				[self loadAuthInfo];

<!---HONumber=July15_HO4-->