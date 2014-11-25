<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Behandeln von Konflikten mit Offlinedaten in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Lernen Sie, wie Sie Azure Mobile Services zum Behandeln von Konflikten beim Synchronisieren von Offlinedaten in Ihrer iOS-Anwendung verwenden" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Behandeln von Konflikten mit Offlinedaten in Mobile Services" authors="krisragh" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Behandeln von Konflikten bei der Synchronisierung von Offlinedaten in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS" class="current">iOS</a>
</div>

In diesem Thema erfahren Sie, wie Sie Daten synchronisieren und Konflikte behandeln können, wenn Sie die Offlinefunktionen von Azure Mobile Services verwenden. Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Offlinedaten][Erste Schritte mit Offlinedaten] auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie [Erste Schritte mit Offlinedaten][Erste Schritte mit Offlinedaten] abschließen.

> [WACOM.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

## Abschließen des Lernprogramms zu ersten Schritten mit Offlinedaten

Befolgen Sie die Anweisungen im Lernprogramm [Erste Schritte mit Offlinedaten][Erste Schritte mit Offlinedaten], und schließen Sie das Projekt ab. Wir verwenden das abgeschlossene Projekt aus dem Lernprogramm als Ausgangspunkt für dieses Lernprogramm.

## Aktualisieren des App-Projekts zwecks Bearbeitung

Wir aktualisieren das abgeschlossenen Projekt aus [Erste Schritte mit Offlinedaten][Erste Schritte mit Offlinedaten], um Elemente bearbeiten zu können. Wenn Sie diese App derzeit auf zwei Telefonen ausführen, das gleiche Element auf beiden Telefonen lokal ändern und die Änderungen per Push-Vorgang auf den Server übertragen, schlägt dies mit einem Konflikt fehl.

Anhand der Offlinefunktionen für die Synchronisierung im SDK können Sie solche Konflikte über Code behandeln und dynamisch entscheiden, wie bei in Konflikt stehenden Elementen vorgegangen wird. Durch Änderung des Schnellstart-Projekts können wir mit dieser Funktion experimentieren.

### Aktualisieren des Todo-Listen-View-Controller

1.  Aktualisieren wir das iPhone-Storyboard. Falls Sie mit einem iPad arbeiten, befolgen Sie dieselben Schritte für das iPad-Storyboard.

2.  Wählen Sie im Xcode Project Navigator **MainStoryboard\_iPhone.storyboard** und dann **Todo List View Controller (Todo-Listen-View-Controller)**. Klicken Sie im oberen Menü auf **Editor (Editor) -\> Embed In (Einbetten in) -\> Navigation Controller (Navigationssteuerung)**.

    ![][0]

3.  Wählen Sie als nächstes unter **Todo List View Controller** (Todo-Listen-View-Controller) die Tabellenansichtszelle, und wählen Sie für den Modus **Accessory** (Extra) die Option **Disclosure indicator** (Anzeigenindikator). Der Anzeigenindikator gibt Benutzern an, dass bei Tippen auf den zugehörigen Tabellenansichts-Controller eine neue Ansicht angezeigt wird. Der Anzeigenindikator löst kein Ereignis aus.

    ![][1]

4.  Entfernen Sie unter **TodoListViewController.m** die folgenden Vorgänge und ihre Inhalte. Diese werden nicht benötigt:

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### Hinzufügen des Todo-Eintrags-View-Controller

1.  Fügen Sie Ihrem Projekt eine neue Objective-C-Klasse mit dem Namen **QSTodoItemViewController** hinzu, die von **UIViewController** abgeleitet wurde:

    ![][2]

    ![][3]

2.  Fügen Sie in **QSTodoItemViewController.h** eine Eigenschaft hinzu, um das zu ändernde Element zu speichern:

        @property (nonatomic, weak) NSMutableDictionary *item;

3.  Fügen Sie in **QSTodoItemViewController.m** zwei private Eigenschaften für die beiden Felder des zu bearbeitenden Todo-Eintrags hinzu – den Abschlussstatus und den Text des Todo-Eintrags selbst:

        @interface QSTodoItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

4.  Aktualisieren Sie in **QSTodoItemViewController.m** die Stub-Implementierung von **viewDidLoad** auf den folgenden Code:

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

5.  Fügen Sie in **QSTodoItemViewController.m** vier zusätzliche Methoden zur Behandlung mehrerer Ereignisse hinzu:

        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

        - (void)viewWillDisappear:(BOOL)animated {
            [self.item setValue:[self.itemText text] forKey:@"text"];
            [self.item setValue:[NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0] forKey:@"complete"];
        }

### Hinzufügen von Todo-Eintrags-View-Controller und Segue zum Storyboard

1.  Kehren Sie mit dem Project Navigator zur Datei **MainStoryboard\_iPhone.storyboard** zurück.

2.  Fügen Sie einen neuen View Controller für den Todo-Eintrag zum Storyboard hinzu, und zwar rechts vom vorhandenen **Todo-Listen-View-Controller**. Legen Sie für die benutzerdefinierte Klasse dieses neuen View Controller **QSTodoItemViewController** fest. Weitere Informationen finden Sie unter [Adding a Scene to a Storyboard][Adding a Scene to a Storyboard] (in englischer Sprache).

3.  Fügen Sie einen Push-Segue aus dem **Todo-Listen-View-Controller** zum **Todo-Eintrags-View-Controller** hinzu, und benennen Sie den Segue mit **detailSegue**. Weitere Informationen finden Sie unter [Adding a Segue Between Scenes in a Storyboard][Adding a Segue Between Scenes in a Storyboard] (in englischer Sprache). Erstellen Sie diesen Segue nicht über eine beliebige Zelle oder eine Schaltfläche im ursprünglichen View Controller. Drücken Sie stattdessen STRG, und ziehen Sie vom View-Controller-Symbol unter dem **Todo-Listen-View-Controller** in der Storyboard-Oberfläche zum **Todo-Eintrags-View-Controller**. Wenn Sie den Segue unbeabsichtigt aus einer Zelle erstellen, wird er beim Ausführen einer App zweimal ausgelöst, was zu folgendem Fehler führt:

        Nested push animation can result in corrupted navigation bar

4.  Fügen Sie dem neuen **Todo-Eintrags-View-Controller** ein Textfeld für Eintragstext und ein segmentiertes Steuerelement für den Abschlussstatus sowie Label hinzu. Setzen Sie im segmentierten Steuerelement den Titel von **Segment 0** auf **Yes** (Ja) und den Titel für **Segment 1** auf **No** (Nein). Verbinden Sie diese neuen Felder mit Outlets in Code. Weitere Informationen finden Sie unter [Build a User Interface][Build a User Interface] und [Segmented Controls][Segmented Controls] (in englischer Sprache).

    ![][4]

5.  Verbinden Sie diese neuen Felder mit den entsprechenden Outlets, die Sie bereits **QSTodoItemViewController.m** hinzugefügt haben. Verbinden Sie das Eintragstextfeld mit dem Outlet **itemText** und das segmentierte Steuerelement des Abschlussstatus mit dem Outlet **itemComplete**. Weitere Informationen finden Sie unter [Creating an Outlet Connection][Creating an Outlet Connection] (in englischer Sprache).

6.  Legen Sie den Delegat des Textfelds auf den View Controller fest. Dadurch wird das Textfeld niedergelegt, wenn Sie einen Eintrag bearbeiten und die EINGABETASTE drücken. Drücken Sie STRG, und ziehen Sie vom View-Controller-Symbol unter dem **Todo-Eintrags-View-Controller** in der Storyboard-Oberfläche nach unten. Wählen Sie dann das Delegat-Outlet. Dadurch wird dem Storyboard angegeben, dass der Delegat dieses Textfelds dieser View Controller ist.

7.  Überprüfen Sie, ob die App mit den bisher vorgenommenen Änderungen funktioniert. Führen Sie die App nun im Simulator aus. Fügen Sie der Todo-Liste Einträge hinzu, und klicken Sie auf diese. Der (derzeit leere) Eintrags-View-Controller wird angezeigt.

    ![][5]

    ![][6]

### Hinzufügen von Eintragsdetails zum Todo-Eintrags-View-Controller

1.  Wir verweisen von **QSTodoListViewController.m** auf **QSTodoItemViewController**. Dafür fügen wir in **QSTodoListViewController.m** eine Zeile zum Importieren von **QSTodoItemViewController.h** hinzu.

        #import "QSTodoItemViewController.h"

2.  Fügen Sie der Oberfläche **QSTodoListViewController** in **QSTodoListViewController.m** zwei neue Eigenschaften zum Speichern des zu bearbeitenden Eintrags hinzu:

        @property (nonatomic)           NSInteger       editedItemIndex;
        @property (strong, nonatomic)   NSMutableDictionary *editedItem;

3.  Implementieren Sie **tableView:didSelectRowAtIndexPath:** in **QSTodoListViewController.m**, um den zu bearbeitenden Eintrag zu speichern, und rufen Sie dann den Segue auf, um die Detailansicht anzuzeigen.

          - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
              self.editedItemIndex = [indexPath row];
              self.editedItem = [[self.todoService.items objectAtIndex:[indexPath row]] mutableCopy];

              [self performSegueWithIdentifier:@"detailSegue" sender:self];
          }

4.  Implementieren Sie **prepareForSegue:sender:** in **QSTodoListViewController.m**, um den Eintrag an den **Todo-Eintrags-View-Controller** zu übergeben.

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSTodoItemViewController *ivc = (QSTodoItemViewController *)[segue destinationViewController];
                ivc.item = self.editedItem;
            }
        }

5.  Überprüfen Sie, ob die App mit den bisher vorgenommenen Änderungen funktioniert. Führen Sie die App nun im Simulator aus. Fügen Sie der Todo-Liste Einträge hinzu, und klicken Sie auf diese. Der View Controller ist nun nicht mehr leer, sondern zeigt die Details der Todo-Einträge an.

    ![][7]

### Hinzufügen von Unterstützung für das Speichern von Bearbeitungen

1.  Wenn Sie in der Navigationsansicht auf die Schaltfläche **Zurück** klicken, gehen die Bearbeitungen verloren. Wir haben Daten an die Detailansicht gesendet, sie werden jedoch nicht zurück an die Masteransicht gesendet. Da wir bereits einen Zeiger an eine Kopie des Eintrags übergeben haben, können wir anhand dieses Zeigers die Liste der an dem Eintrag vorgenommenen Aktualisierungen abrufen und ihn auf dem Server aktualisieren. Aktualisieren Sie zunächst die Server-Wrapper-Klasse von **QSTodoService** in **QSTodoService.m**, indem Sie den Vorgang **completeItem** entfernen und einen neuen Vorgang **updateItem** hinzufügen. Das ist notwendig, weil mit **completeItem** Einträge lediglich als abgeschlossen markiert werden. Mit **updateItem** hingegen werden die Einträge aktualisiert.

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Replace the original in the items array
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:item completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                NSInteger index = -1;
                if (!error) {
                    BOOL isComplete = [[item objectForKey:@"complete"] boolValue];
                    NSString *remoteId = [item objectForKey:@"id"];
                    index = [items indexOfObjectPassingTest:^BOOL(id obj, NSUInteger idx, BOOL *stop) {
                        return [remoteId isEqualToString:[obj objectForKey:@"id"]];
                    }];

                    if (index != NSNotFound && isComplete)
                    {
                        [mutableItems removeObjectAtIndex:index];
                    }
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });
            }];
        }

2.  Entfernen Sie die Deklaration für **completeItem** aus **QSTodoService.h**, und fügen Sie diese Deklaration für **updateItem** hinzu:

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion;

3.  Fügen Sie in **QSTodoListViewController.m** den Vorgang **viewWillAppear** hinzu, um die Update-Methode aufzurufen, wenn nach Rückkehr aus dem Detail-View-Controller die Masteransicht angezeigt wird.

        - (void)viewWillAppear:(BOOL)animated {
            if (self.editedItem && self.editedItemIndex >= 0) {
                // Returning from the details view controller
                NSDictionary *item = [self.todoService.items objectAtIndex:self.editedItemIndex];

                BOOL changed = ![item isEqualToDictionary:self.editedItem];
                if (changed) {
                    [self.tableView setUserInteractionEnabled:NO];

                    // Change the appearance to look greyed out until we remove the item
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:self.editedItemIndex inSection:0];

                    UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
                    cell.textLabel.textColor = [UIColor grayColor];

                    // Ask the todoService to update the item, and remove the row if it's been completed
                    [self.todoService updateItem:self.editedItem atIndex:self.editedItemIndex completion:^(NSUInteger index) {
                        if ([[self.editedItem objectForKey:@"complete"] boolValue]) {
                            // Remove the row from the UITableView
                            [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                                  withRowAnimation:UITableViewRowAnimationTop];
                        } else {
                            [self.tableView reloadRowsAtIndexPaths:[NSArray arrayWithObject:indexPath]
                                                  withRowAnimation:UITableViewRowAnimationAutomatic];
                        }

                        [self.tableView setUserInteractionEnabled:YES];

                        self.editedItem = nil;
                        self.editedItemIndex = -1;
                    }];
                } else {
                    self.editedItem = nil;
                    self.editedItemIndex = -1;
                }
            }
        }

4.  Testen wir nun die App. Überprüfen Sie, ob die App mit den bisher vorgenommenen Änderungen funktioniert. Führen Sie die App nun im Simulator aus. Fügen Sie der Todo-Liste Einträge hinzu, und klicken Sie auf diese. Versuchen Sie, einen Eintrag zu bearbeiten, und gehen Sie dann wieder zurück. Überprüfen Sie, ob die Beschreibung des Eintrags in der Masteransicht der App aktualisiert wird. Aktualisieren Sie die App, indem Sie nach unten ziehen, und überprüfen Sie, ob die Bearbeitung in die Cloud übernommen wurde.

### Problem bei der Konfliktbehandlung

1.  Untersuchen wir, was passiert, wenn zwei unterschiedliche Clients versuchen, gleichzeitig dieselben Daten zu bearbeiten. Die Beispielliste unten enthält den Eintrag „Hello world 3“. Ändern wir ihn auf einem Gerät in „Hello world 13“ und auf einem anderen Gerät in „Hello world 23“.

    ![][8]

2.  Starten Sie die App an zwei Stellen: auf zwei iOS-Geräten oder im Simulator und auf einem iOS-Gerät. Sollten Sie kein physisches Gerät für den Test besitzen, starten Sie eine Instanz im Simulator, und senden Sie über einen REST-Client eine PATCH-Anforderung an den mobilen Dienst. Die URL der PATCH-Anforderung enthält dem Namen des Mobile Service, den Namen der Todo-Eintragstabelle und die ID der Todo-Eintragstabelle, die Sie bearbeiten. Der x-zumo-application-Header ist der Anwendungsschlüssel:

        PATCH https://todolist.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: shYOoDFdKhmzLEbnMQqPYrCLhwGOVA10

        {
            "id": "D265929E-B17B-42D1-8FAB-D0ADF26486FA",
            "text": "Hello world 23"
        }

3.  Aktualisieren Sie nun die Einträge in den beiden Instanzen der App. Im Ausgabeprotokoll in Xcode wird ein Fehler angezeigt:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

Das liegt daran, dass im Aufruf für **pullWithQuery:completion:** im completion-Block der Fehlerparameter „non-nil“ lautet, sodass der Fehler über **NSLog** ausgegeben wird.

### Aktualisieren von QSTodoService zur Unterstützung der Konfliktbehandlung

1.  Lassen wir den Benutzer entscheiden, wie er den Konflikt im Client behandelt. Dafür implementieren wir das Protokoll **MSSyncContextDelegate**. Ändern Sie in **QSTodoService.h** und **QSTodoService.m** die Factorymethodendeklaration \*\*(QSTodoService \*)defaultService;\*\* auf die unten stehende Anweisung, sodass der Synchronisierungskontextdelegat als Parameter ausgegeben wird:

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2.  Ändern Sie in **QSTodoService.m** die Zeile **init** wie unten dargestellt, um wieder den Synchronisierungskontextdelegat als Parameter zu erhalten:

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3.  Ändern Sie in **QSTodoService.m** den Aufruf **init** in **defaultServiceWithDelegate** stattdessen zu **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4.  Ändern Sie in **QSTodoService.m** die Initialisierung von **self.client.syncContext**, um für den Delegat **syncDelegate** anstelle von **nil** anzugeben:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### Hinzufügen der UI Alert View-Hilfe zur Unterstützung der Konfliktbehandlung

1.  Lassen wir den Benutzer im Falle eines Konflikts entscheiden, welche Version beibehalten werden soll:

-   die Client-Version behalten (überschreibt die Version auf dem Server)
-   die Serverversion behalten (aktualisiert die lokale Client-Tabelle)
-   keine der beiden Versionen behalten (bricht die Übertragung ab, der Vorgang steht aus)

Da während der Anzeige der Aufforderung ein weiteres Update durchgeführt werden kann, zeigen wir weiterhin Optionen an, bis der Server eine Fehlerantwort zurückgibt. Verwenden wir im Code eine Hilfsklasse, die eine Warnungsansicht anzeigt und einen Delegat verwendet, der bei Anzeige der Warnungsansicht aufgerufen wird. Definieren wir zuerst die Hilfsklasse **QSUIAlertViewWithBlock**.

1.  Fügen Sie diese neue Klasse, **QSUIAlertViewWithBlock**, unter Verwendung von Xcode hinzu, und überschreiben Sie **QSUIAlertViewWithBlock.h** mit den folgenden Inhalten:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

2.  Überschreiben Sie als nächstes **QSUIAlertViewWithBlock.m** mit der folgenden Datei:

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### Hinzufügen der Konfliktbehandlung zum Todo-Listen-View-Controller

1.  Ersetzen Sie in **QSTodoListViewController.m** den Aufruf für **defaultService** in **viewDidLoad** wie unten gezeigt durch einen Aufruf für **defaultServiceWithDelegate**:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2.  Fügen Sie in **QSTodoListViewController.h** **\<MSSyncContextDelegate\>** zur Oberflächendeklaration hinzu, um das Protokoll **MSSyncContextDelegate** zu implementieren.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate>

3.  Fügen Sie oben in **QSTodoListViewController.m** die folgende Importanweisung hinzu:

        #import "QSUIAlertViewWithBlock.h"

4.  Fügen wir **QSTodoListViewController.m** zuletzt die folgenden beiden Vorgänge hinzu, um diese Hilfsklasse zu verwenden und den Benutzer aufzufordern, den Konflikt auf eine von drei Arten zu lösen.

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

### Testen der App

Testen wir die Anwendung mit Konflikten. Bearbeiten Sie denselben Eintrag in zwei unterschiedlichen Instanzen der App, die gleichzeitig ausgeführt werden. Führen Sie nun eine Aktualisierung in den App-Instanzen durch, indem Sie von oben nach unten ziehen. Es wird eine Aufforderung zum Abstimmen der Änderung angezeigt.

### Zusammenfassung

Um das abgeschlossenen Projekt aus [Erste Schritte mit Offlinedaten][Erste Schritte mit Offlinedaten] so einzustellen, dass Konflikte ermittelt werden, haben Sie zunächst die Funktion zum Bearbeiten und Aktualisieren der Todo-Einträge hinzugefügt.

Dafür haben Sie einen neuen Eintragsdetails-View-Controller hinzugefügt, den Haupt-View-Controller und den Detail-View-Controller verbunden und schließlich die Funktion zum Speichern von Bearbeitungen und zu ihrer Übertragung an die Cloud hinzugefügt.

Als nächstes haben Sie festgestellt, was im Falle eines Konflikts geschieht. Sie haben Unterstützung für eine Konfliktbehandlung hinzugefügt, indem Sie das Protokoll **MSSyncContextDelegate** implementiert haben. Sie haben zudem über die Serveroberflächenklasse von **QSTodoService**, **QSTodoListViewController** und unterstützenden Klassen Unterstützung für den Synchronisierungskontextdelegat aktiviert.

Nebenbei haben Sie eine Hilfsklasse **QSUIAlertViewWithBlock** hinzugefügt, damit Benutzern eine Warnung angezeigt wird, und zum Schluss Code zu **QSTodoListViewController** hinzugefügt, um den Benutzer zur Lösung des Konflikts auf eine von drei Arten aufzufordern.



  [Erste Schritte mit Offlinedaten]: /de-de/documentation/articles/mobile-services-ios-get-started-offline-data/
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28
  [0]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-1.png
  [1]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
  [2]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-1.png
  [3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-2.png
  [Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
  [Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
  [Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
  [Segmented Controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
  [4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
  [Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
  [5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
  [6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
  [7]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
  [8]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
