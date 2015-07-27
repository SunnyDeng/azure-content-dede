<properties 
	pageTitle="Erstellen von Echtzeit-Apps mit Pusher (iOS) - Mobile Services" 
	description="Erfahren Sie, wie Sie mithilfe von Pusher Benachrichtigungen an Ihre Azure Media Services-App für iOS senden." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="06/05/2015" 
	ms.author="donnam"/>


# Erstellen von Echtzeit-Apps mit Mobile Services und Pusher
<div class="dev-center-tutorial-selector sublanding">
	<a href="" title="iOS" class="current">iOS</a>
</div>

In diesem Thema erfahren Sie, wie Sie Echtzeitfunktionen zu Ihrer Azure Mobile Services-basierten App hinzufügen können. Abschließend werden die TodoList-Daten in Echtzeit in allen laufenden Instanzen Ihrer App synchronisiert.

Im Lernprogramm [Pushbenachrichtigungen an Benutzer][] wird beschrieben, wie Sie Pushbenachrichtigungen verwenden können, um Benutzer über neue Elemente in der Todo-Liste zu informieren. Pushbenachrichtigungen bieten eine hervorragende Möglichkeit, gelegentliche Änderungen anzuzeigen. In einigen Fällen benötigt eine App allerdings regelmäßige Echtzeitbenachrichtigungen. Echtzeitbenachrichtigungen können mithilfe der Pusher API zu Ihrem mobilen Dienst hinzugefügt werden. In diesem Lernprogramm werden Pusher mit Mobile Services eingesetzt, um eine Todo-Liste im synchronisierten Zustand zu belassen, wenn Änderungen an einer beliebigen laufenden Instanz der App vorgenommen werden.

Pusher stellt einen cloudbasierten Dienst dar, der, wie etwa Mobile Services, das Erstellen von Echtzeit-Apps erheblich vereinfacht. Sie können Pusher verwenden, um rasch Live-Umfragen, Chatrooms, Multiplayer-Spiele und gemeinschaftliche Apps einzurichten und um Live-Daten und Inhalte zu übertragen. Und dies ist erst der Anfang! Weitere Informationen finden Sie unter [http://pusher.com](http://pusher.com).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zum Hinzufügen einer Echtzeitzusammenarbeit zur Todo-Listen-Anwendung behandelt:

1. [Erstellen eines neuen Pusher-Kontos][]
2. [Aktualisieren Ihrer App][]
3. [Installieren von Serverskripts][]
4. [Testen Ihrer App][]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services][] abschließen.

## <a name="sign-up"></a>Erstellen eines neuen Pusher-Kontos

[AZURE.INCLUDE [Pusher-Anmeldung](../../includes/pusher-sign-up.md)]

## <a name="update-app"></a>Aktualisieren Ihrer App

Nachdem Sie nun das Pusher-Konto eingerichtet haben, müssen Sie im nächsten Schritt den iOS-App-Code für die neuen Funktionen modifizieren.

###Installieren der libPusher-Bibliothek

Mit der [libPusher][]-Bibliothek erhalten Sie Zugriff auf den Pusher von iOS aus.

1. Laden Sie die libPusher-Bibliothek [von hier][libPusherDownload] herunter.

2. Erstellen Sie eine Gruppe mit der Bezeichnung _libPusher_ in Ihrem Projekt.

3. Entzippen Sie unter "Finder" die heruntergeladene ZIP-Datei, wählen Sie die Ordner **libPusher-combined.a** und **/headers**, und ziehen Sie diese Elemente in die Gruppe **libPusher** in Ihrem Projekt.

4. Markieren Sie die Option **Copy items into destination group's folder**, und klicken Sie anschließend auf **Finish**.

	![][add-files-to-group]

   Dadurch werden die libPusher-Dateien in das Projekt kopiert.

5. Klicken Sie im Projektverzeichnis im Projekt-Explorer auf **Build Phases**. Klicken Sie danach auf **Add Build Phase** und **Add Copy Files**.

6. Ziehen Sie die Datei **libPusher-combined.a** vom Projekt-Explorer in die neue Build-Phase.

7. Ändern Sie **Destination** in **Frameworks**, und klicken Sie auf **Copy only when installing**.

	![][add-build-phase]

8. Fügen Sie innerhalb des Bereichs **Binärdatei mit Bibliotheken verknüpfen** die folgenden Bibliotheken hinzu:

	- libicucore.dylib
	- CFNetwork.framework
	- Security.framework
	- SystemConfiguration.framework

9. Ermitteln Sie innerhalb der **Build Settings** abschließend die Build-Zieleinstellung **Other Linker Flags**, und fügen Sie das Kennzeichen **-all_load** hinzu.

	![][add-linker-flag]

	Dadurch wird das Kennzeichen **-all_load** angezeigt, welches für das Debug-Buildziel eingerichtet ist.

Die Bibliothek ist nun installiert und einsatzbereit.

### Hinzufügen von Code zur Anwendung

1. Öffnen Sie unter Xcode die Datei **QSTodoService.h**, und fügen Sie die folgenden Methodendeklarationen hinzu:

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2. Ersetzen Sie die vorhandenen Deklarationen von **addItem** und **completeItem** durch Folgendes:

		- (void) addItem:(NSDictionary *) item;
		- (void) completeItem: (NSDictionary *) item;

3. Fügen Sie in **QSTodoService.m** den folgenden Code hinzu, um die neuen Methoden zu implementieren:

        // Allows retrieval of items by id
		- (NSUInteger) getItemIndex:(NSDictionary *)item
		{
		    NSInteger itemId = [[item objectForKey: @"id"] integerValue];

		    return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
		}

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item not already in list
		    if(index == NSNotFound)
		    {
        		NSUInteger newIndex = [items count];
		        [(NSMutableArray *)items insertObject:item atIndex:newIndex];
		        return newIndex;
		    }
		    else
        		return -1;
		}

        // To be called when items are completed by other users
		- (NSUInteger) itemCompleted:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item exists in items list
		    if(index != NSNotFound)
		    {
		        NSMutableArray *mutableItems = (NSMutableArray *) items;
		        [mutableItems removeObjectAtIndex:index];
		    }
		    return index;
		}

	Mithilfe von QSTodoService können Sie nun Elemente über die **ID** finden und Elemente lokal ohne das Versenden expliziter Anforderungen an den Remote-Dienst hinzufügen und abschließen.

4. Ersetzen Sie die vorhandenen Methoden **addItem** und **completeItem** durch den folgenden Code:

		-(void) addItem:(NSDictionary *)item
		{
		    // Insert the item into the TodoItem table and add to the items array on completion
		    [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
        		[self logErrorIfNotNil:error];
		    }];
		}

		-(void) completeItem:(NSDictionary *)item
		{
		    // Set the item to be complete (we need a mutable copy)
		    NSMutableDictionary *mutable = [item mutableCopy];
		    [mutable setObject:@(YES) forKey:@"complete"];

		    // Update the item in the TodoItem table and remove from the items array on completion
		    [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
		        [self logErrorIfNotNil:error];
		    }];
		}


	Beachten Sie, dass Elemente nun zusammen mit Aktualisierungen für die GUI hinzugefügt und abgeschlossen werden, wenn Ereignisse vom Pusher empfangen werden, und nicht, wenn die Datentabelle aktualisiert wird.

5. Fügen Sie in der Datei **QSTodoListViewController.h** die folgenden Import-Ausdrücke hinzu:

		#import "PTPusherDelegate.h"
		#import "PTPusher.h"
		#import "PTPusherEvent.h"
		#import "PTPusherChannel.h"

6. Modifizieren Sie die Schnittstellen-Deklaration zum Hinzufügen von **PTPusherDelegate**, sodass diese etwa wie folgt aussieht:

		@interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7. Fügen Sie die folgende neue Eigenschaft hinzu:

		@property (nonatomic, strong) PTPusher *pusher;

8. Fügen Sie den folgenden Code hinzu, welcher eine neue Methode deklariert:

		// Sets up the Pusher client
		- (void) setupPusher;

9. Fügen Sie in **QSTodoListViewController.m** die folgende Zeile unter den anderen **@synthesise**-Zeilen hinzu, um die neue Eigenschaft zu implementieren:

		@synthesize pusher = _pusher;

10. Fügen Sie nun den folgenden Code hinzu, um die neue Methode zu implementieren:

		// Sets up the Pusher client
		- (void) setupPusher {

			// Create a Pusher client, using your Pusher app key as the credential
		    // TODO: Move Pusher app key to configuration file
		    self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
		    self.pusher.reconnectAutomatically = YES;

    		// Subscribe to the 'todo-updates' channel
		    PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

    		// Bind to the 'todo-added' event
		    [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Add item to the todo list
        		NSUInteger index = [self.todoService itemAdded:channelEvent.data];

        		// If the item was not already in the list, add the item to the UI
		        if(index != -1)
        		{
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];

    		// Bind to the 'todo-completed' event
		    [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Update the item to be completed
		        NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

        		// As long as the item did exit in the list, update the UI
		        if(index != NSNotFound)
		        {
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];
		}

11. Ersetzen Sie den Platzhalter `**your_app_key**` durch den app_key-Wert, den Sie zuvor aus dem Dialogfeld "Verbindungsinformationen" kopiert haben.

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

		// Ask the todoService to set the item's complete value to YES
	    [self.todoService completeItem:item];

Die App kann nun Ereignisse vom Pusher empfangen und die lokale Todo-Liste entsprechend aktualisieren.



##<a name="install-scripts"></a>Installieren von Serverskripts



Mit den verbliebenen Objekten werden die Serverskripts eingerichtet. Ein Skript wird für den Fall eingefügt, dass ein Element in die TodoList-Tabelle eingefügt oder dort aktualisiert wird.



1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.


2. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

	![][1]



3. Klicken Sie unter **TodoItem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.


	![][2]



	Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.


4. Ersetzen Sie die Einfügefunktion durch den folgenden Code:


		var Pusher = require('pusher');

		function insert(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been inserted, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemCreatedEvent(item);
			    }
			});

			function publishItemCreatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-added', item );
			}
		}



5. Ersetzen Sie die Platzhalter im oben angegebenen Skript durch jene Werte, die Sie zuvor aus dem Verbindungsinformationsdialog kopiert haben:

	- **`**your_app_id**`**: der app&#95;id-Wert
	- **`**your_app_key**`**: der app&#95;key-Wert
	- **`**your_app_key_secret**`**: der geheime App-Schlüssel app&#95;key&#95;secret


6. Klicken Sie auf die Schaltfläche **Save**. Sie haben nun ein Skript konfiguriert, um immer dann ein Ereignis für den Pusher zu veröffentlichen, wenn ein neues Element in die Tabelle **TodoItem** eingefügt wird.


7. Wählen Sie **Update** aus der Dropdown-Liste **Operation** aus.


8. Ersetzen Sie die Aktualisierungsfunktion durch den folgenden Code:

		var Pusher = require('pusher');

		function update(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been updated, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemUpdatedEvent(item);
			    }
			});

			function publishItemUpdatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-completed', item );

			}
		}



9. Wiederholen Sie den Schritt 5 für dieses Skript, um die Platzhalter zu ersetzen.


10. Klicken Sie auf die Schaltfläche **Save**. Sie haben nun ein Skript konfiguriert, um immer dann ein Ereignis für den Pusher zu veröffentlichen, wenn ein neues Element aktualisiert wird.



##<a name="test-app"></a>Testen Ihrer App



Zum Testen der App müssen Sie zwei Instanzen ausführen. Sie können eine Instanz auf einem iOS-Gerät und eine weitere im iOS-Simulator ausführen.

1. Schließen Sie Ihr iOS-Gerät an. Wählen Sie die Schaltfläche **Ausführen** (oder Taste Command+R), um die App auf dem Gerät zu starten. Stoppen Sie danach den Debugging-Vorgang.

	Sie haben die App nun auf dem Gerät installiert.

2. Führen Sie die App auf dem iOS-Simulator aus, und starten Sie die App gleichzeitig auf dem iOS-Gerät.

	Nun laufen aktuell zwei Instanzen der App.

3. Fügen Sie in einer der App-Instanzen ein neues Todo-Element hinzu.

	Überprüfen Sie, ob das hinzugefügte Element in der anderen Instanz auftritt.

4. Prüfen Sie ein Todo-Element, um dieses als abgeschlossen in einer der App-Instanzen zu markieren.

	Stellen Sie sicher, dass das Element aus der anderen Instanz entfernt wird.

Glückwunsch! Sie haben Ihre Mobile Service-App erfolgreich konfiguriert, um für diese eine Synchronisierung auf allen Clients in Echtzeit herzustellen.

## <a name="nextsteps"> </a>Nächste Schritte

Nachdem Sie nun gelernt haben, wie einfach die Verwendung des Pusher-Diensts mit Mobile Services ist, finden Sie unter den folgenden Links weitere Informationen über Pusher.

-   Pusher API-Dokumentation: <http://pusher.com/docs>
-   Pusher-Lernprogramme: <http://pusher.com/tutorials>

Weitere Informationen zu Registrierung und Verwendung von Serverskripts finden Sie unter [Mobile Services: Serverskriptreferenz].

<!-- Anchors. -->
[Erstellen eines neuen Pusher-Kontos]: #sign-up
[Aktualisieren Ihrer App]: #update-app
[Installieren von Serverskripts]: #install-scripts
[Testen Ihrer App]: #test-app

<!-- Images. -->
[1]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png

[add-files-to-group]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
[add-build-phase]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
[add-linker-flag]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png

<!-- URLs. -->
[Pushbenachrichtigungen an Benutzer]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Erste Schritte mit Mobile Services]: /develop/mobile/tutorials/get-started
[libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
[libPusherDownload]: http://go.microsoft.com/fwlink/p/?LinkId=276998


[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
 

<!---HONumber=July15_HO3-->