<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="" solutions="" manager="" editor="" />

Pushbenachrichtigungen an Benutzer mithilfe von Mobile Services
===============================================================

[Windows Phone](/de-de/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/de-de/develop/mobile/tutorials/push-notifications-to-users-android "Android")

Mit diesem Thema wird das [vorherige Pushbenachrichtigungs-Lernprogramm](/de-de/develop/mobile/tutorials/get-started-with-push-ios) erweitert, indem eine neue Tabelle zur Speicherung von Apple Push Notification Service (APNS)-Token hinzugefügt wird. Die Token können verwendet werden, um Pushbenachrichtigungen an Benutzer der iPhone- oder iPad-App zu senden.

Dieses Lernprogramm zeigt Ihnen die entsprechenden Schritte zur Aktualisierung von Pushbenachrichtigungen in Ihrer App:

1.  [Erstellen der Gerätetabelle](#create-table)
2.  [Aktualisieren der App](#update-app)
3.  [Aktualisieren von Serverskripts](#update-scripts)
4.  [Verifizieren des Pushbenachrichtigungs-Verhaltens](#test-app)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf und führt das vorherige Lernprogram [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-ios) weiter fort. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-ios) abschließen.


## <a name="create-table"></a><h2><span class="short-header">Erstellen der Tabelle</span>Erstellen der neuen Gerätetabelle</h2>


1.  Melden Sie sich beim[Azure-Verwaltungsportal](https://manage.windowsazure.com/) an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Erstellen**.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png)

     Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Behalten Sie die Standardeinstellung **Anybody with the application key** für alle Berechtigungen bei. Geben Sie *Devices* in das Feld **Tabellenname** ein, und klicken Sie dann auf den Häkchenknopf.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png)

    Dadurch wird die Tabelle **Devices** erstellt, in welcher die Geräte-Token gespeichert werden, die zum Versenden von Pushbenachrichtigungen getrennt von Eintragsdaten verwendet werden.

Als Nächstes modifizieren Sie die Pushbenachrichtigungs-App zur Speicherung von Daten in dieser neuen Tabelle anstatt in der Tabelle **TodoItem**.

Aktualisieren Ihrer App
-----------------------

1.  Öffnen Sie unter Xcode die Datei "QSTodoService.h", und fügen Sie die folgende Methoden-Deklaration hinzu:

         // Deklarieren der Methode zur Registrierung von Geräte-Token für weitere Benutzer
         - (void)registerDeviceToken:(NSString *)deviceToken;

    Dadurch kann der App-Bevollmächtigte ein Geräte-Token am Mobile Service anmelden.

2.  Fügen Sie in der Datei "QSTodoService.m" die folgende Instanzmethode hinzu:

         // Instanzmethode zur Anmeldung des Geräte-Token in der Gerätetabelle.
         // Aufruf in AppDelegate.m bei erfolgreicher APNS-Registrierung.
         - (void)registerDeviceToken:(NSString *)deviceToken
         {
             MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
             NSDictionary *device = @{ @"deviceToken" : deviceToken };

             // Einfügen des Eintrags in die Gerätetabelle und Hinzufügen des Eintrags-Array bei Abschluss
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

         // Die Registrierung ist erfolgt, nun soll das Geräte-Token (als Zeichenfolge) in der AppDelegate-Instanz gespeichert werden.
         // Zunächst sind die eckigen Klammern zu entfernen.
         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
         (NSData *)deviceToken {

            // Registrieren des APNS-Geräte-Token mit der Mobile Service-Gerätetabelle.
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
                
            QSTodoService *instance = [QSTodoService defaultService];
            [instance registerDeviceToken:token];
         }

5.  Ermitteln Sie in der Datei "QSTodoListViewController.m" die Methode **(IBAction)onAdd**, und *entfernen* Sie den folgenden Code:

         // Beschaffen eines Verweises auf den App-Bevollmächtigten zum einfachen Abrufen des Geräte-Token
         QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

         NSDictionary *item = @{
             @"text" : itemText.text,
             @"complete" : @(NO),
             // add the device token property to our todo item payload
             @"deviceToken" : delegate.deviceToken
         };
         
    Ersetzen Sie dies durch den folgenden Code:

         // Entfernung des Bevollmächtigten ist erfolgt; die Anwendung genehmigt das Geräte-Token an dieser Stelle nicht mehr.
         // Entfernen des Geräte-Token aus der Nutzlast
         NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

Ihre App wurde nun zur Verwendung der neuen Gerätetabelle aktualisiert, um Geräte-Token zu speichern, die zum Versenden von Pushbenachrichtigungen zurück an das Gerät eingesetzt werden.

Aktualisieren von Serverskripts
-------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **Devices**.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png)

2.  Klicken Sie unter **Devices** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png)

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

4.  Klicken Sie auf **TodoItem**, dann auf **Skript**, und wählen Sie **Einfügen** aus.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png)

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
                       // Zeitlimitüberschreitung zur Verzögerung von Benachrichtigungen einstellen, 
                       // Zeit zum Schließen der App bereitstellen 
                       // Am Gerät zum Darstellen von Popupbenachrichtigungen.
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

Testen der AppTesten von Pushbenachrichtigungen in Ihrer App
------------------------------------------------------------

1.  Wählen Sie die Schaltfläche **Ausführen** aus, um das Projekt zu erstellen. Starten Sie die App in einem iOS-fähigen Gerät. Geben Sie in der App danach einen sinnvollen Text ein, wie etwa *Eine neue Mobile Services-Aufgabe*, und klicken Sie danach auf das Plus-Symbol (**+**).

   ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png)

1.  Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

   ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png)

1.  Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Toast angezeigt wird.

   ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png)

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Pushbenachrichtigungen veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-ios)
    
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-ios)
    
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.


