<properties 
	pageTitle="Behandeln von Konflikten mit Offlinedaten in Mobile Services (iOS) | Mobile Dev Center" 
	description="Lernen Sie, wie Sie Azure Mobile Services zum Behandeln von Konflikten beim Synchronisieren von Offlinedaten in Ihrer iOS-Anwendung verwenden" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="krisragh;donnam"/>


# Behandeln von Konflikten bei der Synchronisierung von Offlinedaten in Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../includes/mobile-services-selector-offline-conflicts.md)]

In diesem Thema erfahren Sie, wie Sie Daten synchronisieren und Konflikte behandeln können, wenn Sie die Offlinefunktionen von Azure Mobile Services verwenden. Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Offlinedaten] auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie [Erste Schritte mit Offlinedaten] abschließen.

>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Aktualisieren des App-Projekts zwecks Bearbeitung]
2. [Aktualisieren des Todo-Listen-View-Controller]
3. [Hinzufügen des Todo-Eintrags-View-Controller]
4. [Hinzufügen von Todo-Eintrags-View-Controller und Segue zum Storyboard]
5. [Hinzufügen von Eintragsdetails zum Todo-Eintrags-View-Controller]
6. [Hinzufügen von Unterstützung für das Speichern von Bearbeitungen]
7. [Problem bei der Konfliktbehandlung]
8. [Aktualisieren von QSTodoService zur Unterstützung der Konfliktbehandlung]
9. [Hinzufügen der UI Alert View-Hilfe zur Unterstützung der Konfliktbehandlung]
10. [Hinzufügen der Konfliktbehandlung zum Todo-Listen-View-Controller]
11. [Testen der App]

## Abschließen des Lernprogramms zu ersten Schritten mit Offlinedaten

Befolgen Sie die Anweisungen im Lernprogramm [Erste Schritte mit Offlinedaten], und schließen Sie das Projekt ab. Wir verwenden das abgeschlossene Projekt aus dem Lernprogramm als Ausgangspunkt für dieses Lernprogramm.

## <a name="update-app"></a>Aktualisieren des App-Projekts zwecks Bearbeitung

Wir aktualisieren das abgeschlossenen Projekt aus [Erste Schritte mit Offlinedaten], um Elemente bearbeiten zu können. Wenn Sie diese App derzeit auf zwei Telefonen ausführen, das gleiche Element auf beiden Telefonen lokal ändern und die Änderungen per Push-Vorgang auf den Server übertragen, schlägt dies mit einem Konflikt fehl.

Anhand der Offlinefunktionen für die Synchronisierung im SDK können Sie solche Konflikte über Code behandeln und dynamisch entscheiden, wie bei in Konflikt stehenden Elementen vorgegangen wird. Durch Änderung des Schnellstart-Projekts können wir mit dieser Funktion experimentieren.

### <a name="update-list-view"></a>Aktualisieren des Todo-Listen-View-Controller

1. Wählen Sie im Xcode Project Navigator **MainStoryboard_iPhone.storyboard** und dann **Todo List View Controller (Todo-Listen-View-Controller)**. Wählen Sie die Tabellenansichtszelle aus, und wählen Sie für den Modus "Accessory" die Option **Disclosure indicator** aus. Der Anzeigenindikator gibt Benutzern an, dass bei Tippen auf den zugehörigen Tabellenansichts-Controller eine neue Ansicht angezeigt wird. Der Anzeigenindikator löst kein Ereignis aus.

      ![][update-todo-list-view-controller-2]

2. Entfernen Sie unter **TodoListViewController.m** die folgenden Vorgänge und ihre Inhalte. Diese werden nicht benötigt:

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### <a name="add-view-controller"></a>Hinzufügen des Todo-Eintrags-View-Controller

1. Erstellen Sie eine neue Cocoa Touch-Klasse mit dem Namen **QSItemViewController**, die von **UIViewController** abgeleitet ist.

2. Fügen Sie in **QSItemViewController.h** die folgende Typdefinition hinzu:

        typedef void (^ItemEditCompletionBlock) (NSDictionary *editedItem);

3. Fügen Sie in **QSItemViewController.h** eine Eigenschaft zum Speichern des zu ändernden Elements hinzu und eine Eigenschaft für den Rückruf, der aufgerufen wird, wenn der Benutzer die Zurück-Schaltfläche in der Detailansicht drückt:

        @property (nonatomic, weak) NSMutableDictionary *item;
        @property (nonatomic, strong) ItemEditCompletionBlock editCompleteBlock;

4. Fügen Sie in **QSItemViewController.m** zwei private Eigenschaften für die beiden Felder des zu bearbeitenden Todo-Eintrags hinzu – den Abschlussstatus und den Text des Todo-Eintrags selbst:

        @interface QSItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

5. Aktualisieren Sie in **QSItemViewController.m** die Stub-Implementierung von **viewDidLoad** auf den folgenden Code:

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

6. Fügen Sie in **QSItemViewController.m** vier zusätzliche Methoden zur Behandlung bearbeiteter Steuerelementereignisse hinzu:

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

7. Fügen Sie in **QSItemViewController** außerdem die folgende Methode hinzu, die aufgerufen wird, wenn der Benutzer die Schaltfläche **Zurück** in der Navigationsleiste drückt. Die Methode kann für andere Ereignisse aufgerufen werden, daher wird zunächst die übergeordnete Ansicht überprüft. Wenn es Änderungen am Element gegeben hat, wird **self.item** geändert, und der Rückruf **EditCompleteBlock** wird aufgerufen:

        - (void)didMoveToParentViewController:(UIViewController *)parent
        {
            if (![parent isEqual:self.parentViewController]) {
                NSNumber *completeValue = [NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0];
                
                Boolean changed =
                    [self.item valueForKey:@"text"] != [self.itemText text] ||
                    [self.item valueForKey:@"complete"] != completeValue;
                
                if (changed) {
                    [self.item setValue:[self.itemText text] forKey:@"text"];
                    [self.item setValue:completeValue forKey:@"complete"];
                    
                    self.editCompleteBlock(self.item);
                }
            }
        }

### <a name="add-segue"></a>Hinzufügen von Todo-Eintrags-View-Controller und Segue zum Storyboard

1. Kehren Sie mit dem Project Navigator zur Datei **MainStoryboard_iPhone.storyboard** zurück.

2. Fügen Sie einen neuen View Controller für den Todo-Eintrag zum Storyboard hinzu, und zwar rechts vom vorhandenen **Todo-Listen-View-Controller**. Legen Sie als benutzerdefinierte Klasse dieses neuen View Controller **QSItemViewController** fest. Weitere Informationen finden Sie unter [Adding a Scene to a Storyboard] (in englischer Sprache).

3. Fügen Sie einen **Show**-Segue aus dem **Todo-Listen-View-Controller** zum **Todo-Eintrags-View-Controller** hinzu. Legen Sie dann in Attributes Inspector den Segue-Bezeichner auf **detailSegue** fest.

    Erstellen Sie diesen Segue nicht über eine beliebige Zelle oder eine Schaltfläche im ursprünglichen View Controller. Drücken Sie stattdessen STRG, und ziehen Sie vom View-Controller-Symbol über dem **Todo-Listen-View-Controller** in der Storyboard-Oberfläche zum **Todo-Eintrags-View-Controller**:

    ![][todo-list-view-controller-add-segue]

    Wenn Sie den Segue unbeabsichtigt aus einer Zelle erstellen, wird er beim Ausführen einer App zweimal ausgelöst, was zu folgendem Fehler führt:

        Nested push animation can result in corrupted navigation bar

    Weitere Informationen über Segues finden Sie unter [Hinzufügen eines Segue zwischen Szenen in einem Storyboard].

4. Fügen Sie dem neuen **Todo-Eintrags-View-Controller** ein Textfeld für Eintragstext und ein segmentiertes Steuerelement für den Abschlussstatus sowie Label hinzu. Setzen Sie im segmentierten Steuerelement den Titel von **Segment 0** auf **Yes** (Ja) und den Titel für **Segment 1** auf **No** (Nein). Verbinden Sie diese neuen Felder mit Outlets in Code. Weitere Informationen finden Sie unter [Build a User Interface] und [Segmented Controls] (in englischer Sprache).

      ![][add-todo-item-view-controller-3]

5. Verbinden Sie diese neuen Felder mit den entsprechenden Outlets, die Sie bereits **QSItemViewController.m** hinzugefügt haben. Verbinden Sie das Eintragstextfeld mit dem Outlet **itemText** und das segmentierte Steuerelement des Abschlussstatus mit dem Outlet **itemComplete**. Weitere Informationen finden Sie unter [Creating an Outlet Connection] (in englischer Sprache).

6. Legen Sie den Delegat des Textfelds auf den View Controller fest. Drücken Sie STRG, und ziehen Sie vom View-Controller-Symbol unter dem **Todo-Eintrags-View-Controller** in der Storyboard-Oberfläche nach unten. Wählen Sie dann das Delegat-Outlet. Dadurch wird dem Storyboard angegeben, dass der Delegat dieses Textfelds dieser View Controller ist.

7. Überprüfen Sie, ob die App mit den bisher vorgenommenen Änderungen funktioniert. Führen Sie die App nun im Simulator aus. Fügen Sie der Todo-Liste Einträge hinzu, und klicken Sie auf diese. Der (derzeit leere) Eintrags-View-Controller wird angezeigt.

      ![][add-todo-item-view-controller-4] ![][add-todo-item-view-controller-5]

### <a name="add-item-details"></a>Hinzufügen von Eintragsdetails zum Todo-Eintrags-View-Controller

1. Wir verweisen von **QSTodoListViewController.m** auf **QSItemViewController**. Dafür fügen wir in **QSTodoListViewController.m** eine Zeile zum Importieren von **QSItemViewController.h** hinzu.

        #import "QSItemViewController.h"

2. Fügen Sie der Oberfläche **QSTodoListViewController** in **QSTodoListViewController.m** eine neue Eigenschaft zum Speichern des zu bearbeitenden Eintrags hinzu:

        @property (strong, nonatomic)   NSDictionary *editingItem;

3. Implementieren Sie **tableView:didSelectRowAtIndexPath:** in **QSTodoListViewController.m**, um den zu bearbeitenden Eintrag zu speichern, und rufen Sie dann den Segue auf, um die Detailansicht anzuzeigen.

        - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
            NSManagedObject *item = [self.fetchedResultsController objectAtIndexPath:indexPath];
            self.editingItem = [MSCoreDataStore tableItemFromManagedObject:item]; // map from managed object to dictionary
            
            [self performSegueWithIdentifier:@"detailSegue" sender:self];
        }

4. Implementieren Sie **PrepareForSegue:sender:** in **QSTodoListViewController.m**, um den Eintrag an den **Todo-Eintrags-View-Controller** zu übergeben, und geben Sie den Rückruf an, für den Fall, dass der Benutzer die Detailansicht beendet:

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSItemViewController *ivc = (QSItemViewController *) [segue destinationViewController];
                ivc.item = [self.editingItem mutableCopy];
                
                ivc.editCompleteBlock = ^(NSDictionary *editedValue) {
                    [self.todoService updateItem:editedValue completion:^(NSUInteger index) {
                        self.editingItem = nil;
                    }];
                };
            }
        }

5. Überprüfen Sie, ob die App mit den bisher vorgenommenen Änderungen funktioniert. Führen Sie die App nun im Simulator aus. Fügen Sie der Todo-Liste Einträge hinzu, und klicken Sie auf diese. Der View Controller ist nun nicht mehr leer, sondern zeigt die Details der Todo-Einträge an.

      ![][add-todo-item-view-controller-6]

### <a name="saving-edits"></a>Hinzufügen von Unterstützung für das Speichern von Bearbeitungen

1. Wenn Sie in der Navigationsansicht auf die Schaltfläche Zurück klicken, gehen die Bearbeitungen verloren. Wir haben Daten an die Detailansicht gesendet, sie werden jedoch nicht zurück an die Masteransicht gesendet. Da wir bereits einen Zeiger an eine Kopie des Eintrags übergeben haben, können wir anhand dieses Zeigers die Liste der an dem Eintrag vorgenommenen Aktualisierungen abrufen und ihn auf dem Server aktualisieren. Aktualisieren Sie zunächst die Server-Wrapper-Klasse von **QSTodoService** in **QSTodoService.m**, indem Sie den Vorgang **completeItem** entfernen und einen neuen Vorgang **updateItem** hinzufügen. Das ist notwendig, weil mit **completeItem** Einträge lediglich als abgeschlossen markiert werden. Mit **updateItem** hingegen werden die Einträge aktualisiert.

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            
            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:mutable completion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                
                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

2. Entfernen Sie die Deklaration für **completeItem** aus **QSTodoService.h**, und fügen Sie diese Deklaration für **updateItem** hinzu:

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion;

3. Testen wir nun die App. Überprüfen Sie, ob die App mit den bisher vorgenommenen Änderungen funktioniert. Führen Sie die App nun im Simulator aus. Fügen Sie der Todo-Liste Einträge hinzu, und klicken Sie auf diese. Versuchen Sie, einen Eintrag zu bearbeiten, und gehen Sie dann wieder zurück. Überprüfen Sie, ob die Beschreibung des Eintrags in der Masteransicht der App aktualisiert wird. Aktualisieren Sie die App, indem Sie nach unten ziehen, und überprüfen Sie, ob die Bearbeitung in den Remotedienst übernommen wurde.

### <a name="conflict-handling-problem"></a>Problem bei der Konfliktbehandlung

1. Untersuchen wir, was passiert, wenn zwei unterschiedliche Clients versuchen, gleichzeitig dieselben Daten zu bearbeiten. Im folgenden Beispiel gibt es einen Eintrag „Mobile Services is Cool!“ Ändern wir diesen auf einem Gerät in „I love Mobile Services!“ und auf einem anderen Gerät in „I love Azure!“.

      ![][conflict-handling-problem-1]

2. Starten Sie die App an zwei Orten: auf zwei iOS-Geräten oder im Simulator und auf einem iOS-Gerät. Sollten Sie kein physisches Gerät für den Test besitzen, starten Sie eine Instanz im Simulator, und senden Sie über einen REST-Client eine PATCH-Anforderung an den mobilen Dienst. Die URL der PATCH-Anforderung enthält dem Namen des Mobile Service, den Namen der Todo-Eintragstabelle und die ID der Todo-Eintragstabelle, die Sie bearbeiten. Der x-zumo-application-Header ist der Anwendungsschlüssel:

        PATCH https://donnam-tutorials.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: xuAdWVDcLuCNfkTvOfaqzCCSBVHqoy96

        {
            "id": "CBBF4464-E08A-47C9-B6FB-6DCB30ACCE7E",
            "text": "I love Azure!"
        }

3. Aktualisieren Sie nun die Einträge in den beiden Instanzen der App. Im Ausgabeprotokoll in Xcode wird ein Fehler angezeigt:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

  Das liegt daran, dass im Aufruf für **pullWithQuery:completion:** im completion-Block der Fehlerparameter „non-nil“ lautet, sodass der Fehler über **NSLog** ausgegeben wird.

### <a name="service-add-conflict-handling"></a>Aktualisieren von QSTodoService zur Unterstützung der Konfliktbehandlung

1. Lassen wir den Benutzer entscheiden, wie er den Konflikt im Client behandelt. Dafür implementieren wir das Protokoll **MSSyncContextDelegate**. Ändern Sie in **QSTodoService.h** und **QSTodoService.m** die Factorymethodendeklaration (**QSTodoService *)defaultService;** auf die unten stehende Anweisung, sodass der Synchronisierungskontextdelegat als Parameter ausgegeben wird:

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2. Ändern Sie in **QSTodoService.m** die Zeile **init** wie unten dargestellt, um wieder den Synchronisierungskontextdelegat als Parameter zu erhalten:

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3. Ändern Sie in **QSTodoService.m** den Aufruf **init** in **defaultServiceWithDelegate** stattdessen zu **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4. Ändern Sie in **QSTodoService.m** die Initialisierung von **self.client.syncContext**, um für den Delegat **syncDelegate** anstelle von **nil** anzugeben:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### <a name="add-alert-view"></a>Hinzufügen der UI Alert View-Hilfe zur Unterstützung der Konfliktbehandlung

1. Lassen wir den Benutzer im Falle eines Konflikts entscheiden, welche Version beibehalten werden soll:
  * die Client-Version behalten (überschreibt die Version auf dem Server)
  * die Serverversion behalten (aktualisiert die lokale Client-Tabelle)
  * keine der beiden Versionen behalten (bricht die Übertragung ab, der Vorgang steht aus)

  Da während der Anzeige der Aufforderung ein weiteres Update durchgeführt werden kann, zeigen wir weiterhin Optionen an, bis der Server eine Fehlerantwort zurückgibt. Verwenden wir im Code eine Hilfsklasse, die eine Warnungsansicht anzeigt und einen Delegat verwendet, der bei Anzeige der Warnungsansicht aufgerufen wird. Definieren wir zuerst die Hilfsklasse **QSUIAlertViewWithBlock**.

2. Fügen Sie diese neue Klasse, **QSUIAlertViewWithBlock**, unter Verwendung von Xcode hinzu, und überschreiben Sie **QSUIAlertViewWithBlock.h** mit den folgenden Inhalten:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

3. Überschreiben Sie als nächstes **QSUIAlertViewWithBlock.m** mit der folgenden Datei:

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

### <a name="add-conflict-handling"></a>Hinzufügen der Konfliktbehandlung zum Todo-Listen-View-Controller

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

### <a name="test-app"></a>Testen der App

Testen wir die Anwendung mit Konflikten. Bearbeiten Sie denselben Eintrag in zwei unterschiedlichen Instanzen der App, die gleichzeitig ausgeführt werden, oder verwenden Sie die App und einen REST-Client.

Führen Sie eine Aktualisierung in den App-Instanzen durch, indem Sie von oben nach unten ziehen. Ihnen wird eine Eingabeaufforderung zum Auflösen des Konflikts angezeigt:

![][conflict-ui]


### Zusammenfassung

Um das abgeschlossenen Projekt aus [Erste Schritte mit Offlinedaten] so einzustellen, dass Konflikte ermittelt werden, haben Sie zunächst die Funktion zum Bearbeiten und Aktualisieren der Todo-Einträge hinzugefügt.

Dafür haben Sie einen neuen Eintragsdetails-View-Controller hinzugefügt, den Haupt-View-Controller und den Detail-View-Controller verbunden und schließlich die Funktion zum Speichern von Bearbeitungen und zu ihrer Übertragung an die Cloud hinzugefügt.

Als nächstes haben Sie festgestellt, was im Falle eines Konflikts geschieht. Sie haben Unterstützung für eine Konfliktbehandlung hinzugefügt, indem Sie das Protokoll **MSSyncContextDelegate** implementiert haben. Sie haben zudem über die Serveroberflächenklasse von **QSTodoService**, **QSTodoListViewController** und unterstützenden Klassen Unterstützung für den Synchronisierungskontextdelegat aktiviert.

Nebenbei haben Sie eine Hilfsklasse **QSUIAlertViewWithBlock** hinzugefügt, damit Benutzern eine Warnung angezeigt wird, und zum Schluss Code zu **QSTodoListViewController** hinzugefügt, um den Benutzer zur Lösung des Konflikts auf eine von drei Arten aufzufordern.

<!-- URLs. -->

[Aktualisieren des App-Projekts zwecks Bearbeitung]: #update-app
[Aktualisieren des Todo-Listen-View-Controller]: #update-list-view
[Hinzufügen des Todo-Eintrags-View-Controller]: #add-view-controller
[Hinzufügen von Todo-Eintrags-View-Controller und Segue zum Storyboard]: #add-segue
[Hinzufügen von Eintragsdetails zum Todo-Eintrags-View-Controller]: #add-item-details
[Hinzufügen von Unterstützung für das Speichern von Bearbeitungen]: #saving-edits
[Problem bei der Konfliktbehandlung]: #conflict-handling-problem
[Aktualisieren von QSTodoService zur Unterstützung der Konfliktbehandlung]: #service-add-conflict-handling
[Hinzufügen der UI Alert View-Hilfe zur Unterstützung der Konfliktbehandlung]: #add-alert-view
[Hinzufügen der Konfliktbehandlung zum Todo-Listen-View-Controller]: #add-conflict-handling
[Testen der App]: #test-app


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
[Hinzufügen eines Segue zwischen Szenen in einem Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Erste Schritte mit Offlinedaten]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md

<!--HONumber=54-->