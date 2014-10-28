<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Pushbenachrichtigungen an Benutzer mithilfe von Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS" class="current">iOS</a><a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>

Mit diesem Thema wird das [vorherige Pushbenachrichtigungs-Lernprogramm][vorherige Pushbenachrichtigungs-Lernprogramm] erweitert, indem eine neue Tabelle zur Speicherung von Apple Push Notification Service (APNS)-Token hinzugefügt wird. Die Token können verwendet werden, um Pushbenachrichtigungen an Benutzer der iPhone- oder iPad-App zu senden.

Dieses Lernprogramm zeigt Ihnen die entsprechenden Schritte zur Aktualisierung von Pushbenachrichtigungen in Ihrer App:

1.  [Erstellen der Gerätetabelle][Erstellen der Gerätetabelle]
2.  [Aktualisieren der App][Aktualisieren der App]
3.  [Aktualisieren von Serverskripts][Aktualisieren von Serverskripts]
4.  [Verifizieren des Pushbenachrichtigungs-Verhaltens][Verifizieren des Pushbenachrichtigungs-Verhaltens]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf und führt das vorherige Lernprogram [Erste Schritte mit Pushbenachrichtigungen][vorherige Pushbenachrichtigungs-Lernprogramm] weiter fort. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Pushbenachrichtigungen][vorherige Pushbenachrichtigungs-Lernprogramm] abschließen.

## <a name="create-table"></a>

## <span class="short-header">Erstellen der Tabelle</span>Erstellen der neuen Gerätetabelle

</h2>
1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][]

2.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Erstellen**.

    ![][1]

    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Behalten Sie die Standardeinstellung **Anybody with the application key** für alle Berechtigungen bei. Geben Sie *Devices* in das Feld **Tabellenname** ein, und klicken Sie dann auf den Häkchenknopf.

    ![][2]

    Dadurch wird die Tabelle **Devices** erstellt, in welcher die Geräte-Token gespeichert werden, die zum Versenden von Pushbenachrichtigungen getrennt von Eintragsdaten verwendet werden.

Als Nächstes modifizieren Sie die Pushbenachrichtigungs-App zur Speicherung von Daten in dieser neuen Tabelle anstatt in der Tabelle **TodoItem**.

## <a name="update-app"></a>Aktualisieren Ihrer App

1.  Öffnen Sie unter Xcode die Datei "QSTodoService.h", und fügen Sie die folgende Methoden-Deklaration hinzu:

        // Declare method to register device token for other users
        - (void)registerDeviceToken:(NSString *)deviceToken;

    Dadurch kann der App-Bevollmächtigte ein Geräte-Token am Mobile Service anmelden.

2.  Fügen Sie in der Datei "QSTodoService.m" die folgende Instanzmethode hinzu:

        // Instance method to register deviceToken in Devices table.
        // Called in AppDelegate.m when APNS registration succeeds.
        - (void)registerDeviceToken:(NSString *)deviceToken
        {
            MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
            NSDictionary *device = @{ @"deviceToken" : deviceToken };

            // Insert the item into the devices table and add to the items array on completion
            [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
                if (error) {
                    NSLog(@"ERROR %@", error);
                }
            }];
        }

    Dadurch können weitere Aufrufer das Geräte-Token mit Mobile Services registrieren.

3.  Fügen Sie in der Datei "QSAppDelegate.m" den folgenden Import-Ausdruck hinzu:

        #import "QSTodoService.h"

    Mit diesem Code wird der App-Bevollmächtigte auf die TodoService-Implementierung hingewiesen.

4.  Ersetzen Sie in der Datei "QSAppDelegate.m" die Methode **didRegisterForRemoteNotificationsWithDeviceToken** durch den folgenden Code:

        // We have registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

           // Register the APNS deviceToken with the Mobile Service Devices table.
           NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
           NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];

           QSTodoService *instance = [QSTodoService defaultService];
           [instance registerDeviceToken:token];
        }

5.  Ermitteln Sie in der Datei "QSTodoListViewController.m" die Methode **(IBAction)onAdd**, und *entfernen* Sie den folgenden Code:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    Ersetzen Sie dies durch den folgenden Code:

        // We removed the delegate; this application no longer passes the deviceToken here.
        // Remove the device token from the payload
        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

Ihre App wurde nun zur Verwendung der neuen Gerätetabelle aktualisiert, um Geräte-Token zu speichern, die zum Versenden von Pushbenachrichtigungen zurück an das Gerät eingesetzt werden.

## <a name="update-scripts"></a>Aktualisieren von Serverskripts

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **Devices**.

    ![][3]

2.  Klicken Sie unter **Devices** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![][4]

    Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **Devices** eine Einfügung auftritt.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
           var devicesTable = tables.getTable('Devices');
           devicesTable.where({
               token: item.token
           }).read({
               success: insertTokenIfNotFound
           });

           function insertTokenIfNotFound(existingTokens) {
               if (existingTokens.length > 0) {
                   request.respond(200, existingTokens[0]);
               } else {
                   request.execute();
               }
           }
        }

    Dieses Skript überprüft die **Devices**-Tabelle auf ein vorhandenes Gerät mit demselben Token. Die Einfügung wird nur dann fortgesetzt, wenn kein übereinstimmendes Gerät gefunden wird. So werden doppelte Gerätedatensätze verhindert.

4.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

    ![][5]

5.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
          request.execute({
              success: function() {
                  request.respond();
                  sendNotifications();
              }
          });

          function sendNotifications() {
              var devicesTable = tables.getTable('Devices');
              devicesTable.read({
                  success: function(devices) {
                      // Set timeout to delay the notifications, 
                      // to provide time for the app to be closed 
                      // on the device to demonstrate toast notifications.
                      setTimeout(function() {
                          devices.forEach(function(device) {

                              push.apns.send(device.deviceToken, {
                                  alert: "Toast: " + item.text,
                                  payload: {
                                      inAppMessage: 
                                      "Hey, a new item arrived: '" + 
                                      item.text + "'"
                                  }
                              });
                          });
                      }, 2500);
                  }
              });
          }

    }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Geräte, die in der Tabelle **Devices** gespeichert sind.

## <a name="test"></a><span class="short-header">Testen der App</span>Testen von Pushbenachrichtigungen in Ihrer App

1.  Wählen Sie die Schaltfläche **Ausführen** aus, um das Projekt zu erstellen. Starten Sie die App in einem iOS-fähigen Gerät. Geben Sie in der App danach einen sinnvollen Text ein, wie etwa *Eine neue Mobile Services-Aufgabe*, und klicken Sie danach auf das Plus-Symbol (**+**).

    ![][6]

2.  Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

    ![][7]

3.  Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Toast angezeigt wird.

    ![][8]

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Nächste Schritte

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Pushbenachrichtigungen veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Phone]: /de-de/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone"
  [iOS]: /de-de/develop/mobile/tutorials/push-notifications-to-users-ios "iOS"
  [Android]: /de-de/develop/mobile/tutorials/push-notifications-to-users-android "Android"
  [vorherige Pushbenachrichtigungs-Lernprogramm]: /de-de/develop/mobile/tutorials/get-started-with-push-ios
  [Erstellen der Gerätetabelle]: #create-table
  [Aktualisieren der App]: #update-app
  [Aktualisieren von Serverskripts]: #update-scripts
  [Verifizieren des Pushbenachrichtigungs-Verhaltens]: #test-app
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png
  [3]: ./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png
  [4]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png
  [5]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png
  [7]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png
  [8]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
