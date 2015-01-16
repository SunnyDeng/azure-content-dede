<properties urlDisplayName="Using Offline Data" pageTitle="Verwendung der Offline-Datensynchronisierung in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie, wie Sie Azure Mobile Services verwenden, um Offlinedaten in Ihrer iOS-Anwendung zwischenzuspeichern und zu synchronisieren." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Erste Schritte mit der Synchronisierung von Offlinedaten in Mobile Services


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


Dieses Lernprogramm behandelt die Offlinesynchronisierungsfunktion von Mobile Services für iOS. Sie ermöglichen es Entwicklern, Apps zu schreiben, die selbst dann nutzbar sind, wenn der Endbenutzer keinen Netzwerkzugriff hat.

Die Offlinesynchronisierung hat eine Reihe potenzieller Nutzen:

* Verbesserte Reaktionsfähigkeit der App durch das lokale Caching von Serverdaten auf dem Gerät
* Macht Apps unempfindlich gegen Unterbrechungen der Netzwerkverbindung
* Endanwender können Daten selbst dann erstellen und ändern, wenn sie keinen Netzwerkzugriff haben, also in Szenarien mit wenig oder keiner Konnektivität
* Daten auf mehreren Geräten synchronisieren und Konflikte erkennen, wenn derselbe Datensatz von zwei Geräte aus geändert wird

Dieses Lernprogramm zeigt, wie die App aus dem Lernprogramm [Erste Schritte mit Mobile Services] so geändert werden kann, dass die Offlinefunktionen von Azure Mobile Services unterstützt werden. Anschließend fügen Sie Daten in einem Offlineszenario mit getrennter Verbindung hinzu, synchronisieren diese Elemente mit der Onlinedatenbank und melden sich daraufhin beim Azure-Verwaltungsportal an, um Änderungen an den Daten anzuzeigen, die während der Ausführung der Anwendung vorgenommen wurden.

>[WACOM.NOTE] Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.

In diesem Lernprogramm wird erläutert, wie Sie Azure Mobile Services verwenden können, um Daten in einer Windows Store-App zu speichern und abzurufen. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

>[WACOM.NOTE] Sie können diese Abschnitte überspringen und zum Herunterladen einer Version des Erste-Schritte-Projekts wechseln, das bereits Offlineunterstützung und alles, was in diesem Thema beschrieben ist, hat.   Ein Projekt mit aktivierter Offlineunterstützung zum Herunterladen finden Sie unter [Erste Schritte: Offlinebeispiel für iOS].


In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Abrufen der Schnellstart-Beispiel-App]
2. [Herunterladen der SDK-Vorschau und Aktualisieren des Framework]
3. [Einrichten der Kerndaten]
4. [Definieren des zentralen Datenmodells]
5. [Initialisieren und Verwenden von Synchronisierungstabelle und Synchronisierungskonzept]
6. [Testen der App]

## <a name="get-app"></a>Abrufen der Schnellstart-Beispiel-App

Befolgen Sie die Anweisungen unter [Erste Schritte mit Mobile Services], und laden Sie das Schnellstartprojekt herunter.

## <a name="update-app"></a>Herunterladen der SDK-Vorschau und Aktualisieren des Framework

1. Um Offlineunterstützung zu unserer Anwendung hinzuzufügen, laden wir eine Version des Mobile Services iOS SDK herunter, das die Offlinesynchronisierung unterstützt. Da wir sie als Vorschaufunktion starten, ist es noch nicht die offiziell herunterladbare SDK. [Laden Sie die SDK-Vorschau hier herunter].

2. Dann entfernen Sie den **WindowsAzureMobileServices.framework**-Verweis aus dem Projekt in Xcode, indem Sie ihn auswählen und im Menü **Bearbeiten** auf "In Papierkorb verschieben" klicken, um die Dateien wirklich zu löschen.

      ![][update-framework-1]

3. Entpacken Sie den Inhalt des neuen Vorschau-SDK, und ziehen Sie das neue **WindowsAzureMobileServices.framework**-SDK an die Stelle des alten SDK. Stellen Sie sicher, dass "Copy items into destination group's folder (if needed)" ausgewählt ist.

      ![][update-framework-2]


## <a name="setup-core-data"></a>Einrichten der Kerndaten

1. Mit dem Mobile Services iOS SDK können Sie alle permanenten Speicher verwenden, sofern sie dem **MSSyncContextDataSource**-Protokoll entsprechen. Im SDK ist eine Datenquelle enthalten, welche dieses Protokoll basierend auf [Kerndaten] implementiert.

2. Da die App Kerndaten verwendet, wechseln Sie zu **Ziele** --> **Build-Phasen**, und fügen Sie unter **Binärdatei mit Bibliotheken verknüpfen** den Eintrag **CoreData.framework** hinzu.

      ![][core-data-1]

      ![][core-data-2]

3. Wir fügen einem vorhandenen Projekt in Xcode Kerndaten hinzu, das nicht bereits Kerndaten unterstützt. Daher müssen wir zusätzlichen Standardcode zu den verschiedenen Teilen des Projekts hinzufügen. Fügen Sie zuerst den folgenden Code in **QSAppDelegate.h** hinzu:

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. Als Nächstes ersetzen Sie den Inhalt von **QSAppDelegate.m** durch den folgenden Code. Dies ist fast derselbe Code, den Sie erhalten, wenn Sie in Xcode eine neue Anwendung erstellen und das Kontrollkästchen "Kerndaten verwenden" aktivieren, außer dass Sie einen privaten Warteschlangen-Parallelitätstyp beim Initialisieren von **_managedObjectContext** verwenden. Durch diese Änderung Sie sind nahezu bereit zur Verwendung der Kerndaten, aber Sie haben noch nichts damit zu tun.

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>Definieren des zentralen Datenmodells

1. Richten Sie die Anwendung weiter mit Kerndaten ein, indem Sie das Datenmodell definieren. Wir werden dieses Datenmodell noch nicht verwenden. Zuerst definieren wir das Kerndatenmodell oder das Schema. Um zu beginnen, klicken Sie auf **Datei** -> **Neue Datei**, und wählen Sie unter **Kerndaten** den Bereich **Datenmodell** aus. Verwenden Sie bei Aufforderung zur Eingabe eines Dateinamens **QSTodoDataModel.xcdatamodeld**.

      ![][defining-core-data-main-screen]

2. Als Nächstes definieren wir die eigentlich benötigten Entitäten (Tabellen). Wir erstellen drei Tabellen (Entitäten) im Kerndaten-Modelleditor. Weitere Informationen finden Sie in der [Hilfe zum Kerndaten-Modelleditor].

  * TodoItem: Zum Speichern der eigentlichen Elemente
  * MS_TableOperations: Zum Nachverfolgen der Elemente, die mit dem Server synchronisiert werden müssen (erforderlich für die Offlinefunktion)
  * MS_TableOperationErrors: Zum Nachverfolgen von Fehlern, die während der Offlinesynchronisierung auftreten (erforderlich für die Offlinefunktion)

      ![][defining-core-data-model-editor]

3. Definieren Sie die drei Entitäten wie unten dargestellt. Speichern Sie das Modell, und erstellen Sie das Projekt, um sicherzustellen, dass alles ordnungsgemäß funktioniert. Jetzt haben wir die Anwendung zur Verwendung von Kerndaten eingerichtet, aber die App verwendet sie noch nicht.

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | Attribut  |  Typ   |
    |----------- |  ------ |
    | id         | String  |
    | complete   | Boolescher Wert |
    | text       | String  |
    | ms_version | String  |

    **MS_TableOperations**

    | Attribut  |    Typ     |
    |----------- |   ------    |
    | id         | Ganzzahl 64  |
    | properties | Binärdaten |
    | itemId     | String      |
    | table      | String      |

    **MS_TableOperationErrors**

    | Attribut  |    Typ     |
    |----------- |   ------    |
    | id         | String      |
    | properties | Binärdaten |

## <a name="setup-sync"></a> Initialisieren und Verwenden von Synchronisierungstabelle und Synchronisierungskonzept

1. Zum Offline-Zwischenspeichern von Daten ersetzen wir **MSTable** durch **MSSyncTable**, um auf den mobilen Dienst zuzugreifen. Im Gegensatz zu einer regulären **MSTable** funktioniert eine Synchronisierungstabelle wie eine lokale Tabelle, die die Möglichkeit hinzufügt, die lokal vorgenommenen Änderungen an eine Remotetabelle zu übertragen und diese Änderungen lokal abzurufen. Entfernen Sie in **QSTodoService.h** die Definition der **table**-Eigenschaft:

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. Entfernen Sie in **QSTodoService.m** die folgenden beiden Zeilen in **init**:

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. Als Nächstes initialisieren wir in **QSTodoService.m** den Synchronisierungskontext in **MSClient** mit der obigen Kerndaten-basierten Speicherimplementierung. Der Kontext ist verantwortlich für das Nachverfolgen, welche Elemente lokal geändert wurden, und diese an den Server zu senden, wenn ein Push-Vorgang gestartet wird. Zum Initialisieren des Kontexts benötigen wir eine Datenquelle (die **MSCoreDataStore**-Implementierung des Protokolls) und eine optionale **MSSyncContextDelegate**-Implementierung. Fügen Sie diese Zeilen direkt über den beiden Zeilen ein, die Sie oben eingefügt haben.

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. Als Nächstes aktualisieren wir die Vorgänge in **QSTodoService.m**, um die Synchronisierungstabelle anstelle der normalen Tabelle zu verwenden. Ersetzen Sie zuerst **refreshDataOnSuccess** durch folgende Implementierung. Dies ruft Daten aus dem Dienst ab; daher aktualisieren wir es zur Verwendung einer Synchronisierungstabelle, wobei die Synchronisierungstabelle nur Elemente abrufen soll, die mit unseren Kriterien übereinstimmen, und starten das Laden von Daten aus der lokalen Synchronisierungstabelle in die **items**-Eigenschaft des Diensts. Mit diesem Code ruft   **refreshDataOnSuccess** die Daten aus der Remotetabelle in die lokale (Synchronisierungs-)Tabelle ab. Wir rufen im Allgemeinen nur eine Teilmenge der Tabelle ab, sodass wir den Client nicht mit Informationen überladen, die er möglicherweise nicht benötigt.

    Für diese und die verbleibenden Vorgänge weiter unten schließen wir die Aufrufe an die Fertigstellungsblöcke in einem **dispatch_async**-Aufruf an den Hauptthread an. Wenn der Synchronisierungskontext initialisiert wird, übergeben wir keinen Rückrufparameter, sodass das Framework eine serielle Standardwarteschlange erstellt, die die Ergebnisse aller syncTable-Vorgänge in einem Hintergrundthread verteilt. Beim Ändern von UI-Komponenten müssen wir den Code wieder an den UI-Thread verteilen.

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. Als Nächstes ersetzen Sie **addItem** in **QSTodoService.m** wie folgt. Durch diese Änderung stellen Sie den Vorgang in die Warteschlange, sodass Sie die Änderungen an den Remotedienst übertragen und für alle Benutzer sichtbar machen können:

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. Aktualisieren Sie **completeItem** in **QSTodoService.m** wie folgt. Im Gegensatz zu **MSTable** enthält der completion-Block des **update**-Vorgangs für **MSSyncTable** kein aktualisiertes Element. Mit **MSTable** ändert der Server das zu aktualisierende Element, und diese Änderung wirkt sich auf den Client aus. Mit **MSSyncTable** werden die aktualisierten Elemente nicht geändert, und der completion-Block hat keine Parameter.

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. Fügen Sie die folgende Vorgangsdeklaration von **syncData** zu **QSTodoService.h** hinzu:

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. Ersetzen Sie in **QSTodoListViewController.m** den Aufruf für **[self refresh]** am Ende des Vorgangs **viewDidLoad** durch den folgenden Code:

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. Jetzt testen wir die App wirklich offline. Fügen Sie einige Elemente zur App hinzu, besuchen Sie dann das Azure-Verwaltungsportal und betrachten Sie die Registerkarte **Daten** für die App. Sie sehen, dass noch keine Elemente hinzugefügt wurden.

12. Als Nächstes führen Sie die Aktualisierungsbewegung in der App aus, indem Sie sie nach unten ziehen. Dann besuchen Sie das Azure-Verwaltungsportal erneut und überprüfen die Registerkarte **Daten**. Sie sehen, dass die Daten jetzt in der Cloud gespeichert wurden. Sie können die App nach dem Hinzufügen eines Elements auch schließen (oder nach dem Bearbeiten, wenn die App die Funktionalität zum Bearbeiten von Elementen aktiviert hat). Sobald die App neu gestartet wird, wird sie mit dem Server synchronisiert und die Änderungen gespeichert.

13. Wenn der Client einige Änderungen an den Elementen lokal ausführt, werden diese Änderungen im Synchronisierungskontext gespeichert, der an den Server gesendet wird. Ein *Push*-Vorgang sendet die verfolgten Änderungen an den Remoteserver, aber hier haben wir keine Push-Aufrufe an den Server. Jedoch werden *vor der Ausführung eines Abrufvorgangs alle ausstehenden Vorgänge im Allgemeinen an den Server übertragen*, sodass ein Push-Vorgang trotzdem automatisch durchgeführt wird, um Konflikte zu vermeiden. Daher werden keine expliziten Aufrufe per *push* in dieser App übertragen.

## <a name="test-app"></a>Testen der App

Abschließend testen wir nun die Anwendung offline. Fügen Sie einige Elemente zur Anwendung hinzu. Besuchen Sie dann das Portal und durchsuchen Sie die Daten (oder verwenden Sie ein Netzwerktool wie z. B. PostMan oder Fiddler, um die Tabelle direkt abzufragen).

Sie sehen, dass die Elemente noch nicht zum Dienst hinzugefügt wurden. Führen Sie nun eine Aktualisierung in der App durch, indem Sie sie von oben nach unten ziehen. Sie sehen, dass die Daten jetzt in der Cloud gespeichert wurden. Sie können die App schließen, nachdem Sie einige Elemente hinzugefügt haben. Beim erneuten Starten der Anwendung wird sie mit dem Server synchronisiert, und die Änderungen werden gespeichert.

## Nächste Schritte

* [Behandeln von Konflikten mit Offlineunterstützung für Mobile Services]

<!-- URLs. -->

[Abrufen der Schnellstart-Beispiel-App]: #get-app
[Herunterladen der SDK-Vorschau und Aktualisieren des Framework]: #update-app
[Einrichten der Kerndaten]: #setup-core-data
[Definieren des zentralen Datenmodells]: #defining-core-data
[Initialisieren und Verwenden von Synchronisierungstabelle und Synchronisierungskonzept]: #setup-sync
[Testen der App]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[Hilfe zum Kerndaten-Modelleditor]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Herstellen einer Outlet-Verbindung]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Erstellen einer Benutzeroberfläche]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Hinzufügen eines Übergangs zwischen Szenen in einem Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Hinzufügen einer Szene zu einem Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Kerndaten]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Herunterladen der SDK-Vorschau]: http://aka.ms/Gc6fex
[Verwenden der Mobile Services-Clientbibliothek für iOS]: /de-de/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Erste Schritte: Offlinebeispiel für iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-ios-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-ios-get-started-data/
[Behandeln von Konflikten mit Offlineunterstützung für Mobile Services]: /de-de/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
