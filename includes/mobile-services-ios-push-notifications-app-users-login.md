
Als Nächstes müssen Sie die Methode zur Registrierung von Pushbenachrichtigungen ändern, um sicherzustellen, dass der Benutzer vor einem Registrierungsversuch authentifiziert wird.

1. Entfernen Sie aus **QSAppDelegate.m** die Implementierung von **didFinishLaunchingWithOptions** vollständig:

2. Öffnen Sie **QSTodoListViewController.m** und fügen Sie folgenden Code an das Ende der **viewDidLoad**-Methode hinzu:

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!---HONumber=August15_HO6-->