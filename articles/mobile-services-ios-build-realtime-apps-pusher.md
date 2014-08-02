<properties linkid="develop-mobile-tutorials-build-realtime-apps-with-pusher-ios" urlDisplayName="Build Realtime Apps with Pusher" pageTitle="Build Realtime Apps with Pusher (iOS) - Mobile Services" metaKeywords="" description="Learn how to use Pusher to send notifications to your Azure Media Services app on iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Build Real-time Apps with Mobile Services and Pusher" authors="" solutions="" manager="" editor="" />

Erstellen von Echtzeit-Apps mit Mobile Services und Pusher
==========================================================

[iOS]( "iOS")

In diesem Thema erfahren Sie, wie Sie Echtzeitfunktionen zu Ihrer Azure Mobile Services-basierten App hinzufügen können. Abschließend werden die TodoList-Daten in Echtzeit in allen laufenden Instanzen Ihrer App synchronisiert.

Im Lernprogramm [Pushbenachrichtigungen an Benutzer](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios) wird beschrieben, wie Sie Pushbenachrichtigungen verwenden können, um Benutzer über neue Elemente in der Todo-Liste zu informieren. Pushbenachrichtigungen bieten eine hervorragende Möglichkeit, gelegentliche Änderungen anzuzeigen. In einigen Fällen benötigt eine App allerdings regelmäßige Echtzeitbenachrichtigungen. Echtzeitbenachrichtigungen können mithilfe der Pusher API zu Ihrem mobilen Dienst hinzugefügt werden. In diesem Lernprogramm werden Pusher mit Mobile Services eingesetzt, um eine Todo-Liste im synchronisierten Zustand zu belassen, wenn Änderungen an einer beliebigen laufenden Instanz der App vorgenommen werden.

Pusher stellt einen cloudbasierten Dienst dar, der, wie etwa Mobile Services, das Erstellen von Echtzeit-Apps erheblich vereinfacht. Sie können Pusher verwenden, um rasch Live-Umfragen, Chatrooms, Multiplayer-Spiele und gemeinschaftliche Apps einzurichten und um Live-Daten und Inhalte zu übertragen. Und dies ist erst der Anfang! Weitere Informationen finden Sie unter <http://pusher.com>.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zum Hinzufügen einer Echtzeitzusammenarbeit zur Todo-Listen-Anwendung behandelt:

1.  [Erstellen eines neuen Pusher-Kontos](#sign-up)
2.  [Aktualisieren Ihrer App](#update-app)
3.  [Installieren von Serverskripts](#install-scripts)
4.  [Testen Ihrer App](#test-app)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started) abschließen.

Erstellen eines neuen Pusher-Kontos
-----------------------------------

[WACOM.INCLUDE [pusher-sign-up](../includes/pusher-sign-up.md)]

Aktualisieren Ihrer App
-----------------------

Nachdem Sie nun das Pusher-Konto eingerichtet haben, müssen Sie im nächsten Schritt den iOS-App-Code für die neuen Funktionen modifizieren.

### Installieren der libPusher-Bibliothek

Mit der [libPusher](http://go.microsoft.com/fwlink/p?LinkId=276999)-Bibliothek erhalten Sie Zugriff auf den Pusher von iOS aus.

1.  Laden Sie die libPusher-Bibliothek [von hier](http://go.microsoft.com/fwlink/p/?LinkId=276998) herunter.

2.  Erstellen Sie eine Gruppe mit der Bezeichnung *libPusher* in Ihrem Projekt.

3.  Entzippen Sie unter "Finder" die heruntergeladene ZIP-Datei, wählen Sie die Ordner **libPusher-combined.a** und **/headers**, und ziehen Sie diese Elemente in die Gruppe **libPusher** in Ihrem Projekt.

4.  Markieren Sie die Option **Copy items into destination group's folder**, und klicken Sie anschließend auf **Finish**.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png)

    Dadurch werden die libPusher-Dateien in das Projekt kopiert.

5.  Klicken Sie im Projektverzeichnis im Projekt-Explorer auf **Build Phases**. Klicken Sie danach auf **Add Build Phase** und **Add Copy Files**.

6.  Ziehen Sie die Datei **libPusher-combined.a** vom Projekt-Explorer in die neue Build-Phase.

7.  Ändern Sie **Destination** in **Frameworks**, und klicken Sie auf **Copy only when installing**.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png)

8.  Fügen Sie innerhalb des Bereichs **Binärdatei mit Bibliotheken verknüpfen** die folgenden Bibliotheken hinzu:

    -   libicucore.dylib
    -   CFNetwork.framework
    -   Security.framework
    -   SystemConfiguration.framework

9.  Ermitteln Sie innerhalb der **Build Settings** abschließend die Build-Zieleinstellung **Other Linker Flags**, und fügen Sie das Kennzeichen **-all\_load** hinzu.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png)

    Dadurch wird das Kennzeichen **-all\_load** angezeigt, welches für das Debug-Buildziel eingerichtet ist.

Die Bibliothek ist nun installiert und einsatzbereit.

### Hinzufügen von Code zur Anwendung

1.  Öffnen Sie unter Xcode die Datei **QSTodoService.h**, und fügen Sie die folgenden Methodendeklarationen hinzu:

         // Ermöglicht den Abruf von Elementen per ID
         - (NSUInteger) getItemIndex:(NSDictionary *)item;

         // Ist aufzurufen, wenn Elemente von anderen Benutzern hinzugefügt werden
         - (NSUInteger) itemAdded:(NSDictionary *)item;
            
         // Ist aufzurufen, wenn Elemente von anderen Benutzern abgeschlossen werden
         - (NSUInteger) itemCompleted:(NSDictionary *)item;

2.  Ersetzen Sie die vorhandenen Deklarationen von **addItem** und **completeItem** durch Folgendes:

         - (void) addItem:(NSDictionary *) item;
         - (void) completeItem: (NSDictionary *) item;

3.  Fügen Sie in **QSTodoService.m** den folgenden Code hinzu, um die neuen Methoden zu implementieren:

         // Ermöglicht den Abruf von Elementen per ID
         - (NSUInteger) getItemIndex:(NSDictionary *)item
         {
             NSInteger itemId = [[item objectForKey: @"id"] integerValue];

             return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                  {
                      return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                  }];
         }

         // Ist aufzurufen, wenn Elemente von anderen Benutzern hinzugefügt werden
         -(NSUInteger) itemAdded:(NSDictionary *)item
         {
             NSUInteger index = [self getItemIndex:item];

             // Aktion ist nur dann abzuschließen, wenn das Element noch nicht in der Liste vorhanden ist
             if(index == NSNotFound)
             {
                NSUInteger newIndex = [items count];
                 [(NSMutableArray *)items insertObject:item atIndex:newIndex];
                 return newIndex;
             }
             else
                return -1;
         }

         // Ist aufzurufen, wenn Elemente von anderen Benutzern abgeschlossen werden
         - (NSUInteger) itemCompleted:(NSDictionary *)item
         {
             NSUInteger index = [self getItemIndex:item];

             // Aktion ist nur dann abzuschließen, wenn Element in Elementliste vorhanden ist
             if(index != NSNotFound)
             {
                 NSMutableArray *mutableItems = (NSMutableArray *) items;
                 [mutableItems removeObjectAtIndex:index];
             }          
             return index;
         }

    Mithilfe von QSTodoService können Sie nun Elemente über die **ID** finden und Elemente lokal ohne das Versenden expliziter Anforderungen an den Remote-Dienst hinzufügen und abschließen.

4.  Ersetzen Sie die vorhandenen Methoden **addItem** und **completeItem** durch den folgenden Code:

         -(void) addItem:(NSDictionary *)item
         {
             // Fügen Sie das Element in die Tabelle TodoItem ein, und fügen Sie diese bei Abschluss zum Element-Array hinzu
             [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
                [self logErrorIfNotNil:error];
             }];
         }

         -(void) completeItem:(NSDictionary *)item
         {
             // Stellen Sie das Element als abgeschlossen ein (eine veränderliche Kopie ist erforderlich)
             NSMutableDictionary *mutable = [item mutableCopy];
             [mutable setObject:@(YES) forKey:@"complete"];

             // Aktualisieren Sie das Element in der TodoItem-Tabelle, und entfernen Sie diese bei Abschluss aus dem Element-Array
             [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
                 [self logErrorIfNotNil:error];
             }];
         }

    Beachten Sie, dass Elemente nun zusammen mit Aktualisierungen für die GUI hinzugefügt und abgeschlossen werden, wenn Ereignisse vom Pusher empfangen werden, und nicht, wenn die Datentabelle aktualisiert wird.

5.  Fügen Sie in der Datei **QSTodoListViewController.h** die folgenden Import-Ausdrücke hinzu:

         #import "PTPusherDelegate.h"
         #import "PTPusher.h"
         #import "PTPusherEvent.h"
         #import "PTPusherChannel.h"

6.  Modifizieren Sie die Schnittstellen-Deklaration zum Hinzufügen von **PTPusherDelegate**, sodass diese etwa wie folgt aussieht:

         @interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7.  Fügen Sie die folgende neue Eigenschaft hinzu:

         @property (nonatomic, strong) PTPusher *pusher;

8.  Fügen Sie den folgenden Code hinzu, welcher eine neue Methode deklariert:

         // Richtet den Pusher-Client ein
         - (void) setupPusher;

9.  Fügen Sie in **QSTodoListViewController.m** die folgende Zeile unter den anderen **@synthesise**-Zeilen hinzu, um die neue Eigenschaft zu implementieren:

         @synthesize pusher = _pusher;

10. Fügen Sie nun den folgenden Code hinzu, um die neue Methode zu implementieren:

        // Richtet den Pusher-Client ein
        - (void) setupPusher {
            
            // Pusher-Client erstellen unter Verwendung des Pusher-App-Schlüssels als Anmeldeinformation
            // TODO: Pusher-App-Schlüssel in Konfigurationsdatei verschieben
            self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
            self.pusher.reconnectAutomatically = YES;
                
            // Anmelden am Kanal 'todo-updates'
            PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];
                
            // Verknüpfen mit dem Ereignis 'todo-added'
            [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {
                
                // Fügen Sie das Element zur todo-Liste hinzu
                NSUInteger index = [self.todoService itemAdded:channelEvent.data];
            
                // Wenn das Element noch nicht in der Liste ist, fügen Sie es zur GUI hinzu
                if(index != -1)
                {
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
                    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
                }
            }];

            // Verknüpfen mit dem Ereignis 'todo-completed'
            [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {
                
                // Aktualisieren Sie das abzuschließende Element
                NSUInteger index = [self.todoService itemCompleted:channelEvent.data];
                    
                // Solange das Element in der Liste vorhanden ist, aktualisieren Sie die GUI
                if(index != NSNotFound)
                {
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
                    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
                }               
            }];
        }

11. Ersetzen Sie den Platzhalter `**your_app_key**` durch den Wert app\_key, den Sie zuvor aus dem Verbindungsinformationsdialog kopiert haben.

12. Ersetzen Sie die **onAdd**-Methode durch den folgenden Code:

        - (IBAction)onAdd:(id)sender
        {
            if (itemText.text.length  == 0) {
                return;
            }

            NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
            [self.todoService addItem:item];

            itemText.text = @"";
        }

13. Ermitteln Sie in der Datei **QSTodoListViewController.m** die Methode (void)viewDidLoad, und fügen Sie einen Aufruf zur **setupPusher**-Methode hinzu, sodass die ersten Zeilen wie folgt lauten:

        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self setupPusher];

14. Ersetzen Sie am Ende der **tableView:commitEditingStyle:forRowAtIndexPath**-Methode den Aufruf nach **completeItem** durch den folgenden Code:

        // Fordern Sie den todoService auf, den Wert "complete" des Elements auf "JA" zu setzen
        [self.todoService completeItem:item];

Die App kann nun Ereignisse vom Pusher empfangen und die lokale Todo-Liste entsprechend aktualisieren.

Installieren von Serverskripts
------------------------------

Mit den verbliebenen Objekten werden die Serverskripts eingerichtet. Ein Skript wird für den Fall eingefügt, dass ein Element in die TodoList-Tabelle eingefügt oder dort aktualisiert wird.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

2.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png)

3.  Klicken Sie unter **TodoItem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png)

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

4.  Ersetzen Sie die Einfügefunktion durch den folgenden Code:

         var Pusher = require('pusher');
            
         function insert(item, user, request) {   

             request.execute({
                success: function() {
                     // Nachdem der Datensatz eingefügt wurde, rufen Sie umgehend den Client ab
                    request.respond();

                     // Veröffentlichen Sie das Ereignis für sämtliche weiteren aktiven Clients
                     publishItemCreatedEvent(item);
                 }
             });
            
             function publishItemCreatedEvent(item) {
            
                 // Idealerweise werden diese Einstellungen aus "config" übernommen
                 var pusher = new Pusher({
                   appId: '**your_app_id**',
                   key: '**your_app_key**',
                   secret: '**your_app_secret**'
                 });     
            
                 // Veröffentlichen Sie das Ereignis über den Pusher-Kanal
                 pusher.trigger( 'todo-updates', 'todo-added', item );  
             }
         }

5.  Ersetzen Sie die Platzhalter im oben angegebenen Skript durch jene Werte, die Sie zuvor aus dem Verbindungsinformationsdialog kopiert haben:

    -   **`**your_app_id**`**: der Wert app\_id
    -   **`**your_app_key**`**: der Wert app\_key
    -   **`**your_app_key_secret**`**: der Wert app\_key\_secret

6.  Klicken Sie auf die Schaltfläche **Save**. Sie haben nun ein Skript konfiguriert, um immer dann ein Ereignis für den Pusher zu veröffentlichen, wenn ein neues Element in die Tabelle **TodoItem** eingefügt wird.

7.  Wählen Sie **Update** aus der Dropdown-Liste **Operation** aus.

8.  Ersetzen Sie die Aktualisierungsfunktion durch den folgenden Code:

         var Pusher = require('pusher');
            
         function update(item, user, request) {   

             request.execute({
                success: function() {
                     // Nachdem der Datensatz aktualisiert wurde, rufen Sie umgehend den Client ab
                    request.respond();

                     // Veröffentlichen Sie das Ereignis für sämtliche weiteren aktiven Clients
                     publishItemUpdatedEvent(item);
                 }
             });
            
             function publishItemUpdatedEvent(item) {
            
                 // Idealerweise werden diese Einstellungen aus "config" übernommen
                 var pusher = new Pusher({
                   appId: '**your_app_id**',
                   key: '**your_app_key**',
                   secret: '**your_app_secret**'
                 });     
            
                 // Veröffentlichen Sie das Ereignis über den Pusher-Kanal
                 pusher.trigger( 'todo-updates', 'todo-completed', item );
            
             }
         }

9.  Wiederholen Sie den Schritt 5 für dieses Skript, um die Platzhalter zu ersetzen.

10. Klicken Sie auf die Schaltfläche **Save**. Sie haben nun ein Skript konfiguriert, um immer dann ein Ereignis für den Pusher zu veröffentlichen, wenn ein neues Element aktualisiert wird.

Testen Ihrer App
----------------

Zum Testen der App müssen Sie zwei Instanzen ausführen. Sie können eine Instanz auf einem iOS-Gerät und eine weitere im iOS-Simulator ausführen.

1.  Schließen Sie Ihr iOS-Gerät an. Wählen Sie die Schaltfläche **Ausführen** (oder Taste Command+R), um die App auf dem Gerät zu starten. Stoppen Sie danach den Debugging-Vorgang.

    Sie haben die App nun auf dem Gerät installiert.

2.  Führen Sie die App auf dem iOS-Simulator aus, und starten Sie die App gleichzeitig auf dem iOS-Gerät.

    Nun laufen aktuell zwei Instanzen der App.

3.  Fügen Sie in einer der App-Instanzen ein neues Todo-Element hinzu.

    Überprüfen Sie, ob das hinzugefügte Element in der anderen Instanz auftritt.

4.  Prüfen Sie ein Todo-Element, um dieses als abgeschlossen in einer der App-Instanzen zu markieren.

    Stellen Sie sicher, dass das Element aus der anderen Instanz entfernt wird.

Glückwunsch! Sie haben Ihre Mobile Service-App erfolgreich konfiguriert, um für diese eine Synchronisierung auf allen Clients in Echtzeit herzustellen.

Nächste Schritte
----------------

Nachdem Sie nun gelernt haben, wie einfach die Verwendung des Pusher-Dienstes mit Mobile Services ist, finden Sie unter den folgenden Links weitere Informationen über Pusher.

-   Pusher API-Dokumentation: &lt;http://pusher.com/docs\>
-   Pusher-Lernprogramme: &lt;http://pusher.com/tutorials\>

Weitere Informationen zu Registrierung und Verwendung von Serverskripts finden Sie unter [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/p/?LinkId=262293).

