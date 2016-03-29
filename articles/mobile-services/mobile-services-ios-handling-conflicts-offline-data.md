<properties
	pageTitle="Behandeln von Konflikten mit Offlinedaten in Mobile Services (iOS) | Mobile Dev Center"
	description="Lernen Sie, wie Sie Azure Mobile Services zum Behandeln von Konflikten beim Synchronisieren von Offlinedaten in Ihrer iOS-Anwendung verwenden"
	documentationCenter="ios"
	authors="krisragh"
	manager="erikre"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="krisragh;donnam"/>


# Behandeln von Konflikten bei der Synchronisierung von Offlinedaten in Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

In diesem Thema erfahren Sie, wie Sie Daten synchronisieren und Konflikte behandeln können, wenn Sie die Offlinefunktionen von Azure Mobile Services verwenden. Dieses Lernprogramm baut auf dem Lernprogramm [Erste Schritte mit Offlinedaten] auf.

>[AZURE.NOTE] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.


## Herunterladen des iOS-Projekts

Laden Sie für dieses Lernprogramm [ein aktualisiertes Xcode-Projekt von GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS) herunter. Wir haben das Xcode-Projekt vom Ende des Lernprogramms [Erste Schritte mit Offlinedaten] als Ausgangspunkt verwendet und dann aktualisiert, damit Elemente bearbeitet werden können. Wir haben außerdem unterstützende Klassen und Methoden hinzugefügt, damit im nächsten Abschnitt eine Konfliktbehandlung hinzugefügt werden kann.

Wenn Sie Sie diese App am Ende dieses Lernprogramms auf zwei Telefonen ausführen, das gleiche Element auf beiden Telefonen lokal ändern und die Änderungen zurück an den Server übertragen, lassen Sie den Benutzer an jedem Telefon auswählen, welche Version beibehalten werden soll:
  * die Client-Version behalten (überschreibt die Version auf dem Server)
  * die Serverversion behalten (aktualisiert die lokale Client-Tabelle)
  * keine der beiden Versionen behalten (bricht die Übertragung ab, der Vorgang steht aus)

Jetzt fügen wir die Konfliktbehandlung hinzu, um diese Funktion zu aktivieren.

## <a name="add-conflict-handling"></a>Hinzufügen der Konfliktbehandlung zum Todo-Listen-View-Controller

1. Bearbeiten Sie **viewDidLoad** in **QSTodoListViewController.m**. Ersetzen Sie den Aufruf von **defaultService** durch einen Aufruf von **defaultServiceWithDelegate**:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. Fügen Sie in **QSTodoListViewController.h** **&lt;MSSyncContextDelegate&gt;** zur Oberflächendeklaration hinzu, um das Protokoll **MSSyncContextDelegate** zu implementieren.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. Fügen Sie oben in **QSTodoListViewController.m** die folgende Importanweisung hinzu:

        #import "QSUIAlertViewWithBlock.h"

4. Fügen wir **QSTodoListViewController.m** zuletzt die folgenden beiden Vorgänge hinzu, um diese Hilfsklasse zu verwenden und den Benutzer aufzufordern, den Konflikt auf eine von drei Arten zu lösen.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

## <a name="test-app"></a>Testen der App

Testen wir die Anwendung mit Konflikten. Bearbeiten Sie denselben Eintrag in zwei unterschiedlichen Instanzen der App, die gleichzeitig ausgeführt werden, oder verwenden Sie die App und einen REST-Client.

Führen Sie eine Aktualisierung in den App-Instanzen durch, indem Sie von oben nach unten ziehen. Ihnen wird eine Eingabeaufforderung zum Auflösen des Konflikts angezeigt:

![][conflict-ui]

<!-- URLs. -->

[Update the App Project to Allow Editing]: #update-app
[Update Todo List View Controller]: #update-list-view
[Add Todo Item View Controller]: #add-view-controller
[Add Todo Item View Controller and Segue to Storyboard]: #add-segue
[Add Item Details to Todo Item View Controller]: #add-item-details
[Add Support for Saving Edits]: #saving-edits
[Conflict Handling Problem]: #conflict-handling-problem
[Update QSTodoService to Support Conflict Handling]: #service-add-conflict-handling
[Add UI Alert View Helper to Support Conflict Handling]: #add-alert-view
[Add Conflict Handler to Todo List View Controller]: #add-conflict-handling
[Test the App]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Segmented Controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Erste Schritte mit Offlinedaten]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_0316_2016-->