<properties linkid="mobile-services-how-to-ios-client" urlDisplayName="iOS Client Library" pageTitle="How to use the iOS client library - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service iOS client library, iOS client library" description="Learn how to use the iOS client library for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the iOS client library for Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Verwenden der iOS-Clientbibliothek für Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/en-us/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/en-us/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/en-us/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des iOS-Clients für Azure Mobile Services. Die Beispiele sind in objective-C geschrieben, und Sie benötigen das [Mobile Services-SDK][Mobile Services-SDK]. Für dieses Lernprogramm benötigen Sie außerdem das [iOS SDK][iOS SDK]. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Wenn Sie keine Erfahrungen mit Mobile Services haben, sollten Sie eventuell zunächst den [Schnellstart für mobile Dienste][Schnellstart für mobile Dienste] absolvieren. Im Schnellstart-Lernprogramm lernen Sie, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.

## Inhaltsverzeichnis

-   [Windows Azure Mobile Services][Windows Azure Mobile Services]
-   [Konzepte][Konzepte]
-   [Einrichtung und Voraussetzungen][Einrichtung und Voraussetzungen]
-   [Gewusst wie: Erstellen des Mobile Services-Clients][Gewusst wie: Erstellen des Mobile Services-Clients]
-   [Gewusst wie: Erstellen eines Tabellenverweises][Gewusst wie: Erstellen eines Tabellenverweises]
-   [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst][Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]

    -   [Zurückgegebene Daten filtern][Zurückgegebene Daten filtern]
    -   [Verwenden des MSQuery-Objekts][Verwenden des MSQuery-Objekts]
    -   [Bestimmte Spalten auswählen][Bestimmte Spalten auswählen]
-   [Gewusst wie: Einfügen von Daten in einen mobilen Dienst][Gewusst wie: Einfügen von Daten in einen mobilen Dienst]
-   [Gewusst wie: Ändern von Daten in einem mobilen Dienst][Gewusst wie: Ändern von Daten in einem mobilen Dienst]
-   [Gewusst wie: Datenbindung in der Benutzeroberfläche][Gewusst wie: Datenbindung in der Benutzeroberfläche]
-   [Gewusst wie: Authentifizieren von Benutzern][Gewusst wie: Authentifizieren von Benutzern]
-   [Gewusst wie: Fehlerbehandlung][Gewusst wie: Fehlerbehandlung]

<!--- [How to: Design unit tests] - [How to: Customize the client]     - [Customize request headers]     - [Customize data type serialization] - [Next steps][Next steps]-->

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

## <a name="Setup"></a>Einrichtung und Voraussetzungen

Dieses Lernprogramm setzt voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle][Erstellen einer Tabelle]. Alternativ können Sie die `ToDoItem`-Tabelle verwenden, die Sie im Lernprogramm [Erste Schritte mit Mobile Services][Schnellstart für mobile Dienste] erstellt haben. Die Beispiele in diesem Lernprogramm verwenden eine Tabelle mit dem Namen `ToDoItem` und den folgenden Spalten:

-   `id`
-   `text`
-   `complete`
-   `duration`

Bei der ersten Erstellung Ihrer iOS-Anwendung müssen Sie darauf achten, dass Sie `WindowsAzureMobileServices.framework` in der Einstellung [**Binärdatei mit Bibliotheken verknüpfen**][**Binärdatei mit Bibliotheken verknüpfen**] für Ihre Anwendung hinzufügen. Klicken Sie bei diesem Schritt auf "Andere hinzufügen…", navigieren Sie zum Speicherort des Azure Mobile Services SDK und wählen Sie den Speicherort aus.

Außerdem müssen Sie den folgenden Verweis in den entsprechenden Dateien oder in der .pch-Datei Ihrer Anwendung hinzufügen.

    #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

## <a name="create-client"></a><span class="short-header">Erstellen des Clients</span>Gewusst wie: Erstellen des mobile Dienste-Clients

Der folgende Code erstellt das Clientobjekt für den mobilen Dienst, das für den Zugriff auf Ihren mobilen Dienst verwendet wird.

    MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

Ersetzen Sie im obigen Code `MobileServiceUrl` und `AppKey` durch die URL und den Anwendungsschlüssel Ihres mobilen Dienstes. Um diese Einstellungen für Ihren mobilen Dienst herauszufinden, wählen Sie Ihren mobilen Dienst im Azure-Verwaltungsportal aus und klicken Sie auf **Dashboard**.

Sie können den Client auch über ein **NSURL**-Objekt mit der URL des Dienstes erstellen:

    MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];      

## <a name="table-reference"></a><span class="short-header">Erstellen eines Tabellenverweises</span>Gewusst wie: Erstellen eines Tabellenverweises

Bevor Sie auf die Daten in Ihrem mobilen Dienst zugreifen können, benötigen Sie einen Verweis auf die abzufragende Tabelle, um Elemente abfragen, ändern oder löschen zu können. Im folgenden Beispiel ist `ToDoItem` der Tabellenname:

    MSTable *table = [client tableWithName:@"ToDoItem"]; 

## <a name="querying"></a><span class="short-header">Abfragen von Daten</span>Gewusst wie: Abfragen von Daten aus einem mobilen Dienst

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
-   *totalCount*: Die Gesamtzahl der Elemente für alle Seiten der Abfrage, und nicht nur für die Elemente der aktuellen Seite. Dieser Wert ist -1, es sei denn, Sie haben die Gesamtzahl in Ihrer Abfrage ausdrücklich angefordert. Weitere Informationen finden Sie unter [Daten seitenweise zurückgeben][Daten seitenweise zurückgeben].
-   *error*: Alle aufgetretenen Fehler; andernfalls `nil`.

### <a name="filtering"></a>Gewusst wie: Zurückgegebene Daten filtern

Für die Filterung Ihrer Ergebnisse haben Sie verschiedene Möglichkeiten zur Auswahl.

Die gängigste Methode zur Filterung der Ergebnisse ist die Verwendung von NSPredicate.

    [table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

Die folgende Abfrage gibt nur unvollständige Elemente in unserer ToDoItem-Tabelle zurück:

    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
    [table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
            for(NSDictionary *item in items) {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
            }
        }
    }];

Einzelne Einträge können anhand der Id abgerufen werden.

    [table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
        }
    }];

Beachten Sie, dass der Rückruf in diesem Fall andere Parameter hat. Anstelle eines Arrays mit Ergebnissen und einer optionalen Anzahl erhalten Sie nur einen Eintrag zurück.

### <a name="query-object"></a>Verwenden des MSQuery-Objekts

Verwenden Sie das **MSQuery**-Objekt für Abfragen, die komplexer sind als einfache Zeilenfilter, wie z. B. Änderungen der Sortierreihenfolge für Ihre Ergebnisse oder Einschränkungen der Anzahl zurückzugebender Datensätze. Das folgende Beispiel zeigt, wie Sie eine MSQuery-Objektinstanz erstellen:

    MSQuery *query = [table query]; 

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

Mit dem MSQuery-Objekt können Sie die folgenden Abfrageeigenschaften steuern:

-   Angabe der Reihenfolge für zurückgegebene Ergebnisse.
-   Einschränkung der zurückzugebenden Felder.
-   Einschränkung der Anzahl der zurückzugebenden Einträge.
-   Auswählen, ob die Gesamtzahl in der Antwort enthalten sein soll.
-   Angeben benutzerdefinierter Abfrageparameter für die Anfrage.

Sie können Ihre Abfragen weiter verfeinern, indem Sie eine oder mehrere Funktionen anwenden. Sobald Sie die Abfrage definiert haben, können Sie diese durch Aufrufen der **readWithCompletion**-Funktion ausführen.

#### <a name="sorting"></a>Sortieren zurückgegebener Daten

Mit den folgenden Funktionen können Sie die Felder für die Sortierung angeben:

    -(void) orderByAscending:(NSString *)field
    -(void) orderByDescending:(NSString *)field

Diese Abfrage sortiert die Ergebnisse zunächst nach Dauer und anschließend nach Fertigstellungsstatus der Aufgabe:

    [query orderByAscending:@"duration"];
    [query orderByAscending:@"complete"];
    [query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        //code to parse results here
    }]; 

#### <a name="paging"></a>Seitenweises Zurückgeben von Daten

Mobile Services kann in einer einzelnen Antwort nur eine bestimmte Anzahl Datensätze zurückgeben. Sie müssen daher eine Seitenverwaltung implementieren, um die Anzahl der Datensätze zu steuern, die Ihren Benutzern angezeigt werden. Für die Seitenverwaltung verwenden Sie die folgenden drei Eigenschaften des **MSQuery**-Objekts:

-   `BOOL includeTotalCount`
-   `NSInteger fetchLimit`
-   `NSInteger fetchOffset`

Im folgenden Beispiel fragt eine einfache Funktion 20 Einträge vom Server ab und fügt diese an eine lokale Sammlung mit bereits abgefragten Einträgen an:

    - (bool) loadResults() {
        MSQuery *query = [table query];

        query.includeTotalCount = YES;
        query.fetchLimit = 20;
        query.fetchOffset = self.loadedItems.count;

        [query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
            if(!error) {
                // Add the items to our local copy
                [self.loadedItems addObjectsFromArray:items];       

                // Set a flag to keep track if there are any additional records we need to load
                self.moreResults = (self.loadedItems.count < totalCount);
            }
        }];
    }

#### <a name="selecting"></a>Einschränkung der zurückgegebenen Felder

Um die zurückgegebenen Felder zu bestimmen, können Sie einfach die Namen der gewünschten Felder in der **selectFields**-Eigenschaft angeben. Das folgende Beispiel gibt nur die Felder text und completed zurück:

    query.selectFields = @[@"text", @"completed"];

#### <a name="parameters"></a>Angabe zusätzlicher Abfrageparameter

Mit der Clientbibliothek können Sie zusätzliche Parameter für die Abfragezeichenfolge in der Anfrage an den Server angeben. Sie können diese Parameter anschließend z. B. in Ihren Serverskripts verwenden. Das folgende Beispiel fügt zwei Abfrageparameter zur Anfrage hinzu:

    query.parameters = @{
        @"myKey1" : @"value1",
        @"myKey2" : @"value2",
    };

Diese Parameter werden in der Form `myKey1=value1&myKey2=value2` an Abfrage-URIs angehängt.
Weitere Informationen finden Sie unter [Gewusst wie: Zugreifen auf benutzerdefinierte Parameter][Gewusst wie: Zugreifen auf benutzerdefinierte Parameter].

## <a name="inserting"></a><span class="short-header">Einfügen von Daten</span>Vorgehensweise: Einfügen von Daten in einen mobilen Dienst

Um eine neue Tabelle in eine Zeile einzufügen, erstellen Sie ein neues [NSDictionary-Objekt][NSDictionary-Objekt] und übergeben es an die insert-Funktion. Der folgende Code fügt einen neuen Eintrag in die Tabelle ein:

    NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified 
        // data compared to what was passed to the server.
    }]; 

Mobile Dienste unterstützen eindeutige benutzerdefinierte Zeichenfolgen als Tabellen-Id. Auf diese Weise können Anwendungen eigene Werte wie z. B. E-Mail-Adressen oder Benutzernamen in der Id-Spalte einer Tabelle in einem mobilen Dienst verwenden. Wenn die Zeile beispielsweise durch eine E-Mail-Adresse identifiziert werden soll, können Sie das folgende JSON-Objekt verwenden.

    NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified 
        // data compared to what was passed to the server.
    }]; 

Falls beim Einfügen eines Datensatzes in eine Tabelle kein Id-Wert angegeben wird, generiert der mobile Dienst einen eindeutigen Wert für die Id.

Die Möglichkeit zum Verwenden von Zeichenfolgen-Ids bietet Entwicklern die folgenden Vorzüge

-   Ids können ohne Roundtrip zur Datenbank generiert werden.
-   Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
-   Id-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Sie können auch Serverskripts verwenden, um die Id-Werte zu setzen. Das folgende Skript generiert eine benutzerdefinierte GUID und verwendet diese als Id für einen neuen Eintrag. Dieser Wert ähnelt dem Id-Wert, den der mobile Dienst generieren würde, wenn in der Abfrage kein Id-Wert übergeben wird.

    //Example of generating an id. This is not required since Mobile Services
    //will generate an id if one is not passed in.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

Wenn eine Anwendung einen Id-Wert übergibt, speichert der mobile Dienst diesen Wert unverändert. Dies beinhaltet auch vor- und nachstehende Leerzeichen. Leerzeichen werden aus dem Wert gekürzt.

Der `id`-Wert muss eindeutig sein und darf keine Zeichen aus den folgenden Sätzen enthalten:

-   Steuerzeichen: [0x0000-0x001F] und [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1][ASCII-Steuerzeichen C0 und C1].
-   Druckbare Zeichen: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Die IDs "." und ".."

Alternativ können Sie auch ganzzahlige Ids für Ihre Tabellen verwenden. Um ganzzahlige Ids zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI to manage Mobile Services tables][CLI to manage Mobile Services tables] (CLI für Tabellen in mobilen Diensten, in englischer Sprache).

Wenn das dynamische Schema aktiviert ist, generiert Mobile Services automatisch neue Spalten auf Grundlage der Felder des Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema][Dynamisches Schema].

## <a name="modifying"></a><span class="short-header">Ändern von Daten</span>Vorgehensweise: Ändern von Daten in einem mobilen Dienst

Sie können existierende Objekte ändern, indem Sie Elemente aus vorherigen Abfragen aktualisieren und anschließend die **update**-Funktion aufrufen.

    NSMutableDictionary *item = [self.results.item objectAtIndex:0];
    [item setObject:@YES forKey:@"complete"];
    [table update:item completion:^(NSDictionary *item, NSError *error) {
        //handle errors or any additional logic as needed
    }];

Für Änderungen genügt es, die geänderten Felder zusammen mit der Zeilen-ID anzugeben:

    [table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @YES} completion:^(NSDictionary *item, NSError *error) {
        //handle errors or any additional logic as needed
    }];

Um ein Element aus einer Tabelle zu löschen, können Sie einfach das Element an die delete-Methode übergeben:

    [table delete:item completion:^(id itemId, NSError *error) {
        //handle errors or any additional logic as needed
    }];

Sie können Einträge auch direkt über deren ID löschen, wie im folgenden Beispiel gezeigt:

    [table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
        //handle errors or any additional logic as needed
    }]; 

Beachten Sie, dass für Änderungen und Löschungen zumindest das `id`-Attribut gesetzt sein muss.

## <a name="authentication"></a><span class="short-header">Authentifizieren</span>Vorgehensweise: Authentifizieren von Benutzern

Mobile Services unterstützt die folgenden Identitätsanbieter für die Authentifizierung von Benutzern:

-   Facebook
-   Google
-   Microsoft Account
-   Twitter
-   Azure Active Directory

Weitere Informationen zur Konfiguration von Identitätsanbietern finden Sie unter [Get started with authentication][Get started with authentication] (Erste Schritte zur Authentifizierung, in englischer Sprache).

Mobile Services unterstützt die beiden folgenden Authentifizierungsabläufe:

-   Im Fall einer servergesteuerten Anmeldung verwaltet Mobile Services den Anmeldeprozess im Namen Ihrer App. Eine anbieterspezifische Anmeldeseite wird von der Clientbibliothek angezeigt, und Mobile Services übernimmt die Authentifizierung beim ausgewählten Anbieter.

-   Im Fall einer clientgesteuerten Anmeldung fordert die App ein Token vom Identitätsanbieter an und legt dieses Token anschließend Mobile Services zur Authentifizierung vor.

Bei erfolgreicher Authentifizierung wird ein Benutzerobjekt zurückgegeben, das die zugewiesene Benutzer-ID und das Authentifizierungstoken enthält. Sie können diese Benutzer-ID in Serverskripts zur Validierung oder Änderung von Anfragen verwenden. Weitere Informationen finden Sie unter [Verwenden von Skripts zum Autorisieren von Benutzern][Verwenden von Skripts zum Autorisieren von Benutzern]. Das Token kann sicher gespeichert und für nachfolgende Anmeldungen wiederverwendet werden.

Sie können auch Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Weitere Informationen finden Sie unter [Berechtigungen][Berechtigungen].

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

Das folgende Beispiel verwendet das [Live Connect SDK][Live Connect SDK], um die einmalige Anmeldung für iOS-Apps zu implementieren.

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

### <a name="caching-tokens"></a>Gewusst wie: Zwischenspeichern von Authentifizierungstokens

Um zu verhindern, dass sich Benutzer bei jeder Ausführung Ihrer Anwendung authentifizieren müssen, können Sie die aktuelle Benutzeridentität nach der Anmeldung zwischenspeichern. Anschließend können Sie den Benutzer mit diesen Informationen direkt erstellen und den Anmeldungsprozess umgehen. Hierzu müssen Sie die Benutzer-ID und das Authentifizierungstoken lokal speichern. Im folgenden Beispiel wird das Token direkt im [Schlüsselbund] gespeichert:

    - (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
    {
        NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
        [query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
        [query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];
        
        if(isSearch) {
            // Use the proper search constants, return only the attributes of the first match.
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
            
            //create an MSUser object
            MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
            NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
            user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
            [self.todoService.client setCurrentUser:user];
            
        } else if (status == errSecItemNotFound) {
            //we need to log the user in
            [self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
                completion:^(MSUser *user, NSError *error) {
                    NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
                    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                            message:msg delegate:nil 
                                            cancelButtonTitle:@"OK" 
                                            otherButtonTitles: nil];
                    [alert show];
                
                    //save the user id and token to the KeyChain
                    NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client 
                                                            andIsSearch:NO];
                    [newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
                    [newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding] 
                                                    forKey:(__bridge id)kSecValueData];
                 
                    OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
                    if(status != errSecSuccess) {
                        //handle error as needed
                        NSAssert(NO, @"Error caching password.");
                    }
            }];
        }   

<div class="dev-callout"><strong>Hinweis</strong>
<p>Token sind vertrauliche Daten und m&uuml;ssen verschl&uuml;sselt gespeichert werden, falls das Ger&auml;t verloren geht oder gestohlen wird.</p>
</div>

Mit einem zwischengespeicherten Token muss sich der Benutzer erst dann wieder anmelden, wenn das Token abläuft. Wenn sich ein Benutzer mit einem abgelaufenen Token anmeldet, wird die Antwort "401 unauthorized" zurückgegeben. Der Benutzer muss sich in diesem Fall neu anmelden und ein neues Token abrufen, das erneut zwischengespeichert werden kann. Sie können Filter verwenden, um Code zur Behandlung von abgelaufenen Token an jeder Stelle schreiben zu müssen, an denen Ihre App den mobilen Dienst aufruft. Mit Filtern können Sie Aufrufe an und Antworten von Ihrem mobilen Dienst abfangen. Der Filtercode prüft Antworten auf 401, löst den Anmeldeprozess aus, falls das Token abgelaufen ist, und setzt anschließend die Anfrage fort, die den 401-Fehler ausgelöst hatte. Details finden Sie unter [Umgang mit abgelaufenen Token][Umgang mit abgelaufenen Token].

## <a name="errors"></a><span class="short-header">Fehlerbehandlung</span>Vorgehensweise: Fehlerbehandlung

Bei Aufrufen an mobile Dienste enthält der completion-Block einen `NSError *error`-Parameter. Wenn ein Fehler auftritt, enthält dieser Parameter einen Wert unterschiedlich von null. Sie sollten diesen Parameter in Ihrem Code prüfen und Fehler entsprechend behandeln.

Im Fehlerfall erhalten Sie weitere Informationen, indem Sie die Datei MSError.h in Ihrem Code einbinden:

    #import <WindowsAzureMobileServices/MSError.h>

Diese Datei definiert die folgenden Konstanten, mit denen Sie auf zusätzliche Daten aus `[error userInfo]` zugreifen können:

-   **MSErrorResponseKey**: die HTTP-Antwortdaten des Fehlers
-   **MSErrorRequestKey**: die HTTP-Anfragedaten des Fehlers

Außerdem ist für jeden Fehlercode eine Konstante definiert. Sie finden eine Erklärung dieser Codes in der Datei MSError.h.

Ein Beispiel für die Validierung und Behandlung von Fehlern finden Sie unter [Validate and modify data in Mobile Services by using server scripts][Validate and modify data in Mobile Services by using server scripts] (Validierung und Änderung von Daten in Mobile Services mithilfe von Serverskripts, in englischer Sprache). In diesem Artikel erfolgt die serverseitige Validierung in Form von Serverskripts. Wenn ungültige Daten übermittelt werden, wird eine Fehlermeldung zurückgegeben und vom Client verarbeitet.

<!-- <h2><a name="#unit-testing"></a><span class="short-header">Designing tests</span>How to: Design unit tests</h2>  _(Optional) This section shows how to write unit test when using the client library (info from Yavor)._  <h2><a name="#customizing"></a><span class="short-header">Customizing the client</span>How to: Customize the client</h2>  _(Optional) This section shows how to send customize client behaviors._  ###<a name="custom-headers"></a>How to: Customize request headers  _(Optional) This section shows how to send custom request headers._  For more information see, New topic about processing headers in the server-side.  ###<a name="custom-serialization"></a>How to: Customize serialization  _(Optional) This section shows how to use attributes to customize how data types are serialized._  For more information see, New topic about processing headers in the server-side.  ## <a name="next-steps"></a>Next steps --> 

<!-- Anchors. --> 

<!-- Images. --> 

<!-- URLs. -->

  [.NET Framework]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework"
  [HTML/JavaScript]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript"
  [iOS]: /en-us/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS"
  [Android]: /en-us/develop/mobile/how-to-guides/work-with-android-client-library/ "Android"
  [Xamarin]: /en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin"
  [Mobile Services-SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [iOS SDK]: https://developer.apple.com/xcode
  [Schnellstart für mobile Dienste]: /en-us/develop/mobile/tutorials/get-started-ios
  [Windows Azure Mobile Services]: #what-is
  [Konzepte]: #concepts
  [Einrichtung und Voraussetzungen]: #Setup
  [Gewusst wie: Erstellen des Mobile Services-Clients]: #create-client
  [Gewusst wie: Erstellen eines Tabellenverweises]: #table-reference
  [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]: #querying
  [Zurückgegebene Daten filtern]: #filtering
  [Verwenden des MSQuery-Objekts]: #query-object
  [Bestimmte Spalten auswählen]: #selecting
  [Gewusst wie: Einfügen von Daten in einen mobilen Dienst]: #inserting
  [Gewusst wie: Ändern von Daten in einem mobilen Dienst]: #modifying
  [Gewusst wie: Datenbindung in der Benutzeroberfläche]: #binding
  [Gewusst wie: Authentifizieren von Benutzern]: #authentication
  [Gewusst wie: Fehlerbehandlung]: #errors
  [mobile-services-concepts]: ../includes/mobile-services-concepts.md
  [Erstellen einer Tabelle]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193162.aspx
  [**Binärdatei mit Bibliotheken verknüpfen**]: https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html
  [Daten seitenweise zurückgeben]: #paging
  [Gewusst wie: Zugreifen auf benutzerdefinierte Parameter]: /en-us/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
  [NSDictionary-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=301965
  [ASCII-Steuerzeichen C0 und C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
  [CLI to manage Mobile Services tables]: http://www.windowsazure.com/en-us/manage/linux/other-resources/command-line-tools/#Mobile_Tables
  [Dynamisches Schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
  [Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
  [Verwenden von Skripts zum Autorisieren von Benutzern]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios
  [Berechtigungen]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193161.aspx
  [Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
  [Umgang mit abgelaufenen Token]: http://go.microsoft.com/fwlink/p/?LinkId=301955
  [Validate and modify data in Mobile Services by using server scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios
