<properties linkid="mobile-services-how-to-ios-client" urlDisplayName="iOS Client Library" pageTitle="How to use the iOS client library - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service iOS client library, iOS client library" description="Learn how to use the iOS client library for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the iOS client library for Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Verwenden der iOS-Clientbibliothek für Mobile Services
======================================================

[.NET Framework](/de-de/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework")[HTML/JavaScript](/de-de/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript")[iOS](/de-de/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS")[Android](/de-de/develop/mobile/how-to-guides/work-with-android-client-library/ "Android")[Xamarin](/de-de/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin")

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des iOS-Clients für Azure Mobile Services. Die Beispiele sind in objective-C geschrieben, und Sie benötigen das [Mobile Services-SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533). Für dieses Lernprogramm benötigen Sie außerdem das [iOS SDK](https://developer.apple.com/xcode). Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Wenn Sie keine Erfahrungen mit Mobile Services haben, sollten Sie eventuell zunächst den [Schnellstart für mobile Dienste](/de-de/develop/mobile/tutorials/get-started-ios) absolvieren. Im Schnellstart-Lernprogramm lernen Sie, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.

Inhaltsverzeichnis
------------------

-   [Was ist Mobile Services?](#what-is)
-   [Konzepte](#concepts)
-   [Einrichtung und Voraussetzungen](#Setup)
-   [Gewusst wie: Erstellen des Mobile Services-Clients](#create-client)
-   [Gewusst wie: Erstellen eines Tabellenverweises](#table-reference)
-   [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst](#querying)
    -   [Filtern zurückgegebener Daten](#filtering)
    -   [Verwenden des MSQuery-Objekts](#query-object)
    -   [Auswählen bestimmter Spalten](#selecting)
-   [Gewusst wie: Einfügen von Daten in einen mobilen Dienst](#inserting)
-   [Gewusst wie: Ändern von Daten in einem mobilen Dienst](#modifying)
-   [Gewusst wie: Datenbindung in der Benutzeroberfläche](#binding)
-   [Gewusst wie: Authentifizieren von Benutzern](#authentication)
-   [Gewusst wie: Fehlerbehandlung](#errors)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

Einrichtung und Voraussetzungen
-------------------------------

Dieses Lernprogramm setzt voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle](http://msdn.microsoft.com/de-de/library/windowsazure/jj193162.aspx). Die Beispiele in diesem Lernprogramm verwenden eine Tabelle mit dem Namen `ToDoItem` und den folgenden Spalten:

-   `id`
-   `text`
-   `complete`
-   `duration`

Bei der ersten Erstellung Ihrer iOS-Anwendung müssen Sie darauf achten, dass Sie `WindowsAzureMobileServices.framework` in der Einstellung **Binärdatei mit Bibliotheken verknüpfen** für Ihre Anwendung hinzufügen.

Außerdem müssen Sie den folgenden Verweis in den entsprechenden Dateien oder in der .pch-Datei Ihrer Anwendung hinzufügen.

     #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

Erstellen des ClientsGewusst wie: Erstellen des Mobile Services-Clients
-----------------------------------------------------------------------

Der folgende Code erstellt das Clientobjekt für den mobilen Dienst, das für den Zugriff auf Ihren mobilen Dienst verwendet wird.

    MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

Ersetzen Sie im obigen Code `MobileServiceUrl` und `AppKey` durch die URL und den Anwendungsschlüssel Ihres mobilen Dienstes. Um diese Einstellungen für Ihren mobilen Dienst herauszufinden, wählen Sie Ihren mobilen Dienst im Azure-Verwaltungsportal aus und klicken Sie auf **Dashboard**.

Sie können den Client auch über ein **NSURL**-Objekt mit der URL des Dienstes erstellen:

    MSClient *client = [MSClient clientWithApplicationURL:(NSURL *)url
                                 applicationKey:(NSString *)string];

Erstellen eines TabellenverweisesGewusst wie: Erstellen eines Tabellenverweises
-------------------------------------------------------------------------------

Bevor Sie auf die Daten in Ihrem mobilen Dienst zugreifen können, benötigen Sie einen Verweis auf die abzufragende Tabelle, um Elemente abfragen, ändern oder löschen zu können. Im folgenden Beispiel ist `ToDoItem` der Tabellenname:

    MSTable *table = [client tableWithName:@"ToDoItem"]; 

Abfragen von DatenGewusst wie: Abfragen von Daten aus einem mobilen Dienst
--------------------------------------------------------------------------

Sobald Sie ein MSTable-Objekt haben, können Sie Ihre Abfrage erstellen. Die folgende Abfrage gibt alle Elemente in unserer ToDoItem-Tabelle zurück.

    [table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
            for(NSDictionary *item in items) {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
        
            }
        }
    }];

In diesem Beispiel schreiben wir den Text der Aufgabe lediglich in das Protokoll.

Der Rückruf enthält die folgenden Parameter:

-   *items*: Ein **NSArray** der Einträge, die Ihre Abfrage zurückgegeben hat.
-   *totalCount*: Die Gesamtzahl der Elemente für alle Seiten der Abfrage, und nicht nur für die Elemente der aktuellen Seite. Dieser Wert ist -1, es sei denn, Sie haben die Gesamtzahl in Ihrer Abfrage ausdrücklich angefordert. Weitere Informationen finden Sie unter [Daten seitenweise zurückgeben](#paging).
-   *error*: Alle aufgetretenen Fehler; `nil` falls kein Fehler aufgetreten ist.

### Gewusst wie: Filtern zurückgegebener Daten

Für die Filterung Ihrer Ergebnisse haben Sie verschiedene Möglichkeiten zur Auswahl.

Die gängigste Methode zur Filterung der Ergebnisse ist die Verwendung von NSPredicate.

    [table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

Die folgende Abfrage gibt nur unvollständige Elemente in unserer ToDoItem-Tabelle zurück:

    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
    [table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        //Schleife durch die Ergebnisse
    }];

Einzelne Einträge können anhand der Id abgerufen werden.

    [table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
        //Ihr Code
    }];

Beachten Sie, dass der Rückruf in diesem Fall andere Parameter hat. Anstelle eines Arrays mit Ergebnissen und einer optionalen Anzahl erhalten Sie nur einen Eintrag zurück.

### Verwenden des MSQuery-Objekts

Verwenden Sie das **MSQuery**-Objekt für Abfragen, die komplexer sind als einfache Zeilenfilter, wie z. B. Änderungen der Sortierreihenfolge für Ihre Ergebnisse oder Einschränkungen der Anzahl zurückzugebender Datensätze. Das folgende Beispiel zeigt, wie Sie eine MSQuery-Objektinstanz erstellen:

-   `MSQuery *query = [table query];`
-   `MSQuery *query = [table queryWithPredicate:(NSPredicate *)predicate];`

Mit dem MSQuery-Objekt können Sie die folgenden Abfrageeigenschaften steuern:

-   Angabe der Reihenfolge für zurückgegebene Ergebnisse.
-   Einschränkung der zurückzugebenden Felder.
-   Einschränkung der Anzahl der zurückzugebenden Einträge.
-   Auswählen, ob die Gesamtzahl in der Antwort enthalten sein soll.
-   Angeben benutzerdefinierter Abfrageparameter für die Anfrage.

Sie können Ihre Abfragen weiter verfeinern, indem Sie eine oder mehrere Funktionen anwenden. Sobald Sie die Abfrage definiert haben, können Sie diese durch Aufrufen der **readWithCompletion**-Funktion ausführen.

#### Sortieren zurückgegebener Daten

Mit den folgenden Funktionen können Sie die Felder für die Sortierung angeben:

    -(void) orderByAscending:(NSString *)field
    -(void) orderByDescending:(NSString *)field

Diese Abfrage sortiert die Ergebnisse zunächst nach Dauer und anschließend nach Fertigstellungsstatus der Aufgabe:

    [query orderByAscending(@"duration")];
    [query orderByAscending(@"duration")];
    [query readWithCompletion:(NSArray *items, NSInteger totalCount, NSError *error) {
        //Code zum Analysieren der Ergebnisse
    }]; 

#### Seitenweises Zurückgeben von Daten

Mobile Services kann in einer einzelnen Antwort nur eine bestimmte Anzahl Datensätze zurückgeben. Sie müssen daher eine Seitenverwaltung implementieren, um die Anzahl der Datensätze zu steuern, die Ihren Benutzern angezeigt werden. Für die Seitenverwaltung verwenden Sie die folgenden drei Eigenschaften des **MSQuery**-Objekts:

-   `BOOL includeTotalCount`
-   `NSInteger fetchLimit`
-   `NSInteger fetchOffset`

Im folgenden Beispiel fragt eine einfache Funktion 20 Einträge vom Server ab und fügt diese an eine lokale Sammlung mit bereits abgefragten Einträgen an:

    - (bool) loadResults() {
        MSQuery *query = [self.table query];

        query.includeTotalCount = YES;
        query.fetchLimit = 20;
        query.fetchOffset = self.loadedItems.count;
        [query readWithCompletion:(NSArray *items, NSInteger totalCount, NSError *error) {
            if(!error){
                //Elemente zur lokalen Kopie hinzufügen
                [self.loadedItems addObjectsFromArray:items];       

                //Markierung setzen, um zu bestimmen, ob weitere Datensätze geladen werden müssen
                self.moreResults = (self.loadedItems.count < totalCount);
            }
        }];
    }

#### Einschränkung der zurückgegebenen Felder

Um die zurückgegebenen Felder zu bestimmen, können Sie einfach die Namen der gewünschten Felder in der **selectFields**-Eigenschaft angeben. Das folgende Beispiel gibt nur die Felder text und completed zurück:

    query.selectFields = @[@"text", @"completed"];

#### Angabe zusätzlicher Abfrageparameter

Mit der Clientbibliothek können Sie zusätzliche Parameter für die Abfragezeichenfolge in der Anfrage an den Server angeben. Sie können diese Parameter anschließend z. B. in Ihren Serverskripts verwenden. Das folgende Beispiel fügt zwei Abfrageparameter zur Anfrage hinzu:

    query.parameters = @{
        @"myKey1" : @"value1",
        @"myKey2" : @"value2",
    };

Diese Parameter werden in der Form `myKey1=value1&myKey2=value2` an die Abfrage-URI angehängt. Weitere Informationen finden Sie unter [Gewusst wie: Zugreifen auf benutzerdefinierte Parameter](/de-de/develop/mobile/how-to-guides/work-with-server-scripts#access-headers).

Einfügen von DatenGewusst wie: Einfügen von Daten in einen mobilen Dienst
-------------------------------------------------------------------------

Um eine neue Tabelle in eine Zeile einzufügen, erstellen Sie ein neues [NSDictionary-Objekt](http://go.microsoft.com/fwlink/p/?LinkId=301965) und übergeben es an die insert-Funktion. Der folgende Code fügt einen neuen Eintrag in die Tabelle ein:

    NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // Das Ergebnis enthält das neue eingefügte Element,
        // Je nach Ihren Serverskripts enthält dieses Objekt zusätzliche oder geänderte 
        // Daten gegenüber dem Objekt, das Sie an den Server übergeben haben.
    }]; 

Mobile Services unterstützt eindeutige benutzerdefinierte Zeichenfolgen als Tabellen-Id. Auf diese Weise können Anwendungen eigene Werte wie z. B. E-Mail-Adressen oder Benutzernamen in der Id-Spalte einer Mobile Services-Tabelle verwenden. Wenn die Zeile beispielsweise durch eine E-Mail-Adresse identifiziert werden soll, können Sie das folgende JSON-Objekt verwenden.

    NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // Das Ergebnis enthält das neue eingefügte Element,
        // Je nach Ihren Serverskripts enthält dieses Objekt zusätzliche oder geänderte 
        // Daten gegenüber dem Objekt, das Sie an den Server übergeben haben.
    }]; 

Falls beim Einfügen eines Datensatzes in eine Tabelle kein Id-Wert angegeben wird, generiert Mobile Services einen eindeutigen Wert für die Id.

Die Möglichkeit zum Verwenden von Zeichenfolgen-Ids bietet Entwicklern die folgenden Vorzüge

-   Ids können ohne Roundtrip zur Datenbank generiert werden.
-   Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
-   Id-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Sie können auch Serverskripts verwenden, um die Id-Werte zu setzen. Das folgende Skript generiert eine benutzerdefinierte GUID und verwendet diese als Id für einen neuen Eintrag. Dieser Wert ähnelt dem Id-Wert, den Mobile Services generieren würde, wenn in der Abfrage kein Id-Wert übergeben wird.

    //Beispiel für das Generieren einer Id. Dies ist nicht erforderlich, da Mobile Services
    //eine Id generiert, wenn keine übergeben wird.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

Wenn eine Anwendung einen Id-Wert übergibt, speichert Mobile Services diesen Wert unverändert. Dies beinhaltet auch vor- und nachstehende Leerzeichen. Leerzeichen werden aus dem Wert gekürzt.

Der `id`-Wert muss eindeutig sein und darf keine Zeichen aus den folgenden Sätzen enthalten:

-   Steuerzeichen: [0x0000-0x001F] und [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set).
-   Druckbare Zeichen: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
-   Die IDs "." und ".."

Alternativ können Sie auch ganzzahlige Ids für Ihre Tabellen verwenden. Um ganzzahlige Ids zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI to manage Mobile Services tables](http://www.windowsazure.com/de-de/manage/linux/other-resources/command-line-tools/#Mobile_Tables) (CLI für Mobile Services-Tabellen, in englischer Sprache).

Wenn das dynamische Schema aktiviert ist, generiert Mobile Services automatisch neue Spalten auf Grundlage der Felder des Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://go.microsoft.com/fwlink/p/?LinkId=296271).

Ändern von DatenGewusst wie: Ändern von Daten in einem mobilen Dienst
---------------------------------------------------------------------

Sie können existierende Objekte ändern, indem Sie Elemente aus vorherigen Abfragen aktualisieren und anschließend die **update**-Funktion aufrufen.

    NSMutableDictionary *item = [self.results.item objectAtIndex:0];
    [item setObject:@YES forKey:@"complete"];
    [table update:item completion:^(NSDictionary *item, NSError *error) {
        //Fehlerbehandlung oder zusätzliche Logik
    }];

Für Änderungen genügt es, die geänderten Felder zusammen mit der Zeilen-ID anzugeben:

    [table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @Yes} completion:^(NSDictionary *item, NSError *error) {
        //Fehlerbehandlung oder zusätzliche Logik
    }];

Um ein Element aus einer Tabelle zu löschen, können Sie einfach das Element an die delete-Methode übergeben:

    [table delete:item completion:^(id itemId, NSError *error) {
        //Fehlerbehandlung oder zusätzliche Logik
    }];

Sie können Einträge auch direkt über deren ID löschen, wie im folgenden Beispiel gezeigt:

    [table deleteWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(id itemId, NSError *error) {
        //Fehlerbehandlung oder zusätzliche Logik
    }]; 

Beachten Sie, dass für Änderungen und Löschungen zumindest das `id`-Attribut gesetzt sein muss.

AuthentifizierenGewusst wie: Authentifizieren von Benutzern
-----------------------------------------------------------

Mobile Services unterstützt die folgenden Identitätsanbieter für die Authentifizierung von Benutzern:

-   Facebook
-   Google
-   Microsoft-Konto
-   Twitter
-   Azure Active Directory

Weitere Informationen zur Konfiguration von Identitätsanbietern finden Sie unter [Get started with authentication](/de-de/develop/mobile/tutorials/get-started-with-users-ios) (Erste Schritte zur Authentifizierung, in englischer Sprache).

Mobile Services unterstützt die beiden folgenden Authentifizierungsabläufe:

-   Im Fall einer servergesteuerten Anmeldung verwaltet Mobile Services den Anmeldeprozess im Namen Ihrer App. Eine anbieterspezifische Anmeldeseite wird von der Clientbibliothek angezeigt, und Mobile Services übernimmt die Authentifizierung beim ausgewählten Anbieter.

-   Im Fall einer clientgesteuerten Anmeldung fordert die App ein Token vom Identitätsanbieter an und legt dieses Token anschließend Mobile Services zur Authentifizierung vor.

Bei erfolgreicher Authentifizierung wird ein Benutzerobjekt zurückgegeben, das die zugewiesene Benutzer-ID und das Authentifizierungstoken enthält. Sie können diese Benutzer-ID in Serverskripts zur Validierung oder Änderung von Anfragen verwenden. Weitere Informationen finden Sie unter [Verwenden von Skripts zum Autorisieren von Benutzern](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios). Das Token kann sicher gespeichert und für nachfolgende Anmeldungen wiederverwendet werden.

Sie können auch Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Weitere Informationen finden Sie unter [Berechtigungen](http://msdn.microsoft.com/de-de/library/windowsazure/jj193161.aspx).

### Servergesteuerte Anmeldung

Dieses Beispiel verwendet ein Microsoft-Konto für die Anmeldung. Sie können diesen Code im ViewDidLoad Ihre Controllers aufrufen oder manuell über einen UIButton. Dieser Code zeigt eine Standardoberfläche für die Anmeldung beim Identitätsanbieter an.

    [client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
        completion:^(MSUser *user, NSError *error) {
            NSString *msg;
            if(error) {
                msg = [@"An error occured: " stringByAppendingString:error.description];
            } else {
                msg = [@"You are logged in as " stringByAppendingString:user.userId];
            }
        
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                  message:msg 
                                  delegate:nil 
                                  cancelButtonTitle:@"OK" 
                                  otherButtonTitles: nil];
            [alert show];
    }]; 

Hinweis: Falls Sie einen anderen Identitätsanbieter als ein Microsoft-Konto verwenden, ändern Sie den an die login-Methode übergebenen Wert auf einen der folgenden Werte: `facebook`, `twitter`, `google` oder `windowsazureactivedirectory`.

Sie können mit dem folgenden Code auch einen Verweis auf MSLoginController abrufen und selbst anzeigen:

    -(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### Clientgesteuerte Anmeldung (Einmaliges Anmelden)

In manchen Fällen erfolgt der Anmeldeprozess außerhalb des Mobile Services-Clients. Sie können diese Methode für die einmalige Anmeldung verwenden, oder wenn Ihre App den Identitätsanbieter direkt kontaktieren muss, um Benutzerinformationen abzurufen. In diesen Fällen können Sie sich bei Mobile Services anmelden, indem Sie ein Token übergeben, das Sie unabhängig von einem unterstützten Identitätsanbieter erhalten haben.

Das folgende Beispiel verwendet das [Live Connect SDK](http://go.microsoft.com/fwlink/p/?LinkId=301960), um die einmalige Anmeldung für iOS-Apps zu implementieren.

    [client loginWithProvider:@"microsoftaccount" 
        token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
        completion:^(MSUser *user, NSError *error) {
            self.loggedInUser = user;
            NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                message:msg 
                delegate:nil 
                cancelButtonTitle:@"OK" 
                otherButtonTitles: nil];
            [alert show];
    }];

Dieser Code geht davon aus, dass Sie zuvor eine **LiveConnectClient**-Instanz mit dem Namen `liveClient` im Controller erstellt haben und der Benutzer angemeldet ist.

### Gewusst wie: Zwischenspeichern von Authentifizierungstokens

Um zu verhindern, dass sich Benutzer bei jeder Ausführung Ihrer Anwendung authentifizieren müssen, können Sie die aktuelle Benutzeridentität nach der Anmeldung zwischenspeichern. Anschließend können Sie den Benutzer mit diesen Informationen direkt erstellen und den Anmeldungsprozess umgehen. Hierzu müssen Sie die Benutzer-ID und das Authentifizierungstoken lokal speichern. Im folgenden Beispiel wird das Token direkt im [Schlüsselbund] gespeichert:

    - (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
    {
        NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
        [query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
        [query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];
        
        if(isSearch) {
            // Korrekte Suchkonstanten verwenden, nur die Attribute der ersten Übereinstimmung werden zurückgegeben.
            [query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
            [query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
            [query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
        }

        return query;
    }

    - (IBAction)loginUser:(id)sender {
        NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
        CFDictionaryRef cfresult;

        OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
        if (status == noErr) {
            NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;
            
            //MSUser-Objekt erstellen
            MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
            NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
            user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
            [self.todoService.client setCurrentUser:user];
            
        } else if (status == errSecItemNotFound) {
            //Benutzer muss angemeldet werden
            [self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
                completion:^(MSUser *user, NSError *error) {
                    NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
                    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                            message:msg delegate:nil 
                                            cancelButtonTitle:@"OK" 
                                            otherButtonTitles: nil];
                    [alert show];
                
                    //Benutzer-ID und Token in KeyChain speichern
                    NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client 
                                                            andIsSearch:NO];
                    [newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
                    [newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding] 
                                                    forKey:(__bridge id)kSecValueData];
                 
                    OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
                    if(status != errSecSuccess) {
                        //Fehlerbehandlung nach Bedarf
                        NSAssert(NO, @"Error caching password.");
                    }
            }];
        }   

**Hinweis**

Token sind vertrauliche Daten und müssen verschlüsselt gespeichert werden, falls das Gerät verloren geht oder gestohlen wird.

Mit einem zwischengespeicherten Token muss sich der Benutzer erst dann wieder anmelden, wenn das Token abläuft. Wenn sich ein Benutzer mit einem abgelaufenen Token anmeldet, wird die Antwort "401 unauthorized" zurückgegeben. Der Benutzer muss sich in diesem Fall neu anmelden und ein neues Token abrufen, das erneut zwischengespeichert werden kann. Sie können Filter verwenden, um Code zur Behandlung von abgelaufenen Token an jeder Stelle schreiben zu müssen, an denen Ihre App den mobilen Dienst aufruft. Mit Filtern können Sie Aufrufe an und Antworten von Ihrem mobilen Dienst abfangen. Der Filtercode prüft Antworten auf 401, löst den Anmeldeprozess aus, falls das Token abgelaufen ist, und setzt anschließend die Anfrage fort, die den 401-Fehler ausgelöst hatte. Details finden Sie unter [Handling Expired Tokens](http://go.microsoft.com/fwlink/p/?LinkId=301955) (Umgang mit abgelaufenen Token, in englischer Sprache).

FehlerbehandlungGewusst wie: Fehlerbehandlung
---------------------------------------------

Bei Aufrufen an mobile Dienste enthält der completion-Block einen `NSError *error`-Parameter. Wenn ein Fehler auftritt, enthält dieser Parameter einen Wert unterschiedlich von null. Sie sollten diesen Parameter in Ihrem Code prüfen und Fehler entsprechend behandeln.

Im Fehlerfall erhalten Sie weitere Informationen, indem Sie die Datei MSError.h in Ihrem Code einbinden. Diese Datei definiert die folgenden Konstanten, die Sie für den Zugriff auf zusätzliche Daten aus `[error userInfo]` verwenden können:

-   **MSErrorResponseKey**: die HTTP-Antwortdaten des Fehlers
-   **MSErrorRequestKey**: die HTTP-Anfragedaten des Fehlers

Außerdem ist für jeden Fehlercode eine Konstante definiert. Sie finden eine Erklärung dieser Codes in der Datei MSError.h.

Ein Beispiel für die Validierung und Behandlung von Fehlern finden Sie unter [Validate and modify data in Mobile Services by using server scripts](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios) (Validierung und Änderung von Daten in Mobile Services mithilfe von Serverskripts, in englischer Sprache). In diesem Artikel erfolgt die serverseitige Validierung in Form von Serverskripts. Wenn ungültige Daten übermittelt werden, wird eine Fehlermeldung zurückgegeben und vom Client verarbeitet.


<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing 
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/de-de/library/windowsazure/jj193161.aspx
[Use scripts to authorize users]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamic schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /de-de/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/de-de/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: http://www.windowsazure.com/de-de/manage/linux/other-resources/command-line-tools/#Mobile_Tables
