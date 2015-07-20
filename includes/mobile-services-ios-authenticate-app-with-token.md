
Im vorherigen Beispiel werden bei jedem Start der App der Identitätsanbieter und der Mobile Service kontaktiert. Sie können jedoch auch das Authentifizierungstoken zwischenspeichern und zunächst versuchen, dieses Token zu verwenden.

* Als Methode zum Verschlüsseln und Speichern von Authentifizierungstokens auf einem iOS-Client wird der iOS-Schlüsselbund empfohlen. Hier wird [SSKeychain](https://github.com/soffes/sskeychain) verwendet – ein einfacher Wrapper für den iOS-Schlüsselbund. Folgen Sie den Anweisungen auf der SSKeychain-Seite und fügen Sie ihn Ihrem Projekt hinzu. Überprüfen Sie, ob die Einstellung für das **Aktivieren der Module** in den **Buildeinstellungen** des Projekts aktiviert ist (Abschnitt **Apple LLVM - Sprachen - Module**.)

* Öffnen Sie **QSTodoListViewController.m**, und fügen Sie folgenden Code hinzu:

```
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
```

* Ändern Sie in `loginAndGetData` den `loginWithProvider:controller:animated:completion:`-completion-Block. Fügen Sie zum Speichern der Benutzer-ID und der Token-Eigenschaften die folgende Zeile direkt vor `[self refresh]` ein:

```
				[self saveAuthInfo];
```

* Sie sollten die Benutzer-ID und den Token beim Starten der App laden. Fügen Sie dies in `viewDidLoad` von **QSTodoListViewController.m** direkt nach der Initialisierung von `self.todoService` hinzu.

```
				[self loadAuthInfo];
```

<!---HONumber=July15_HO2-->