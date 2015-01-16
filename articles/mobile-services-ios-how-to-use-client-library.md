<properties urlDisplayName="iOS Client Library" pageTitle="Verwenden der iOS-Clientbibliothek - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service iOS client library, iOS client library" description="Erfahren Sie mehr über die Verwendung der iOS-Clientbibliothek für Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the iOS client library for Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />




# Verwenden der iOS-Clientbibliothek für Mobile Services
<div class="dev-center-tutorial-selector sublanding">
  <a href="/de-de/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/de-de/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/de-de/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/de-de/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/de-de/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des iOS-Clients für Azure Mobile Services. Die Beispiele sind in Objective-C geschrieben und erfordern das [Mobile Services SDK].  Dieses Lernprogramm erfordert außerdem das [iOS SDK]. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Wenn Sie keine Erfahrungen mit Mobile Services haben, sollten Sie eventuell zunächst den [Schnellstart für mobile Dienste][Erste Schritte mit Mobile Services] absolvieren. Im Schnellstart-Lernprogramm lernen Sie, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.

## Inhaltsverzeichnis

- [Windows Azure Mobile Services][]
- [Konzepte][]
- [Einrichtung und Voraussetzungen][]
- [Gewusst wie: Erstellen des Mobile Services-Clients][]
- [Gewusst wie: Erstellen eines Tabellenverweises][]
- [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst][]
	- [Zurückgegebene Daten filtern]
    - [Verwenden des MSQuery-Objekts][Gewusst wie: Verwenden von MSQuery]
	- [Bestimmte Spalten auswählen]
- [Gewusst wie: Einfügen von Daten in einen mobilen Dienst]
- [Gewusst wie: Ändern von Daten in einem mobilen Dienst]
- [Gewusst wie: Datenbindung in der Benutzeroberfläche]
- [Gewusst wie: Authentifizieren von Benutzern]
- [Gewusst wie: Fehlerbehandlung]

<!--- [How to: Design unit tests]
- [How to: Customize the client]
	- [Customize request headers]
	- [Customize data type serialization]
- [Next steps][]-->

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Einrichtung und Voraussetzungen

In diesem Handbuch wird davon ausgegangen, dass Sie einen mobilen Dienst mit einer Tabelle erstellt haben.  Weitere Informationen finden Sie unter [Tabelle erstellen], oder Sie verwenden die `ToDoItem-Tabelle`, die Sie im Lernprogramm [Erste Schritte mit Mobile Services] erstellt haben. Die Beispiele in diesem Lernprogramm verwenden eine Tabelle mit dem Namen `ToDoItem` und den folgenden Spalten:

+ `id`
+ `text`
+ `complete`
+ `duration`


Bei der ersten Erstellung Ihrer iOS-Anwendung müssen Sie darauf achten, dass Sie `WindowsAzureMobileServices.framework` in der Einstellung [**Binärdatei mit Bibliotheken verknüpfen**](https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html) für Ihre Anwendung hinzufügen. Klicken Sie bei diesem Schritt auf "Andere hinzufügen...", navigieren Sie zum Speicherort des Azure Mobile Services SDK und wählen Sie den Speicherort aus.

Außerdem müssen Sie den folgenden Verweis in den entsprechenden Dateien oder in der .pch-Datei Ihrer Anwendung hinzufügen.

	#import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

<h2><a name="create-client"></a>Gewusst wie: Erstellen des Mobile Services-Clients</h2>

Der folgende Code erstellt das Clientobjekt für den mobilen Dienst, das für den Zugriff auf Ihren mobilen Dienst verwendet wird.

	MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

Ersetzen Sie im obigen Code `MobileServiceUrl` und `AppKey` durch die URL und den Anwendungsschlüssel Ihres mobilen Dienstes. Um diese Einstellungen für Ihren mobilen Dienst herauszufinden, wählen Sie Ihren mobilen Dienst im Azure-Verwaltungsportal aus und klicken Sie auf **Dashboard**.

Sie können den Client auch über ein **NSURL**-Objekt mit der URL des Dienstes erstellen:

	MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];

<h2><a name="table-reference"></a>Gewusst wie: Erstellen eines Tabellenverweises</h2>

Bevor Sie auf die Daten in Ihrem mobilen Dienst zugreifen können, benötigen Sie einen Verweis auf die abzufragende Tabelle, um Elemente abfragen, ändern oder löschen zu können. Im folgenden Beispiel ist `ToDoItem` der Tabellenname:

	MSTable *table = [client tableWithName:@"ToDoItem"];


<h2><a name="querying"></a>Gewusst wie: Abfragen von Daten aus einem mobilen Dienst</h2>

Sobald Sie ein MSTable-Objekt haben, können Sie Ihre Abfrage erstellen.  Die folgende einfache Abfrage ruft alle Elemente in unserer ToDoItem-Tabelle ab.

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

+ _items_: Ein **NSArray** der Einträge, die Ihre Abfrage zurückgegeben hat.
+ _totalCount_: Die Gesamtzahl der Elemente für alle Seiten der Abfrage, und nicht nur für die Elemente der aktuellen Seite. Dieser Wert ist -1, es sei denn, Sie haben die Gesamtzahl in Ihrer Abfrage ausdrücklich angefordert. Weitere Informationen finden Sie unter [Daten seitenweise zurückgeben].
+ _error_: Alle aufgetretenen Fehler; `nil` falls kein Fehler aufgetreten ist.

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

Beachten Sie, dass in diesem Fall die Callback-Parameter geringfügig anders sind.  Anstelle eines Arrays mit Ergebnissen und einer optionalen Anzahl erhalten Sie nur einen Datensatz zurück.

### <a name="query-object"></a>Verwenden des MSQuery-Objekts

Verwenden Sie das **MSQuery**-Objekt für Abfragen, die komplexer sind als einfache Zeilenfilter, wie z. B. Änderungen der Sortierreihenfolge für Ihre Ergebnisse oder Einschränkungen der Anzahl zurückzugebender Datensätze. Das folgende Beispiel zeigt, wie Sie eine MSQuery-Objektinstanz erstellen:

    MSQuery *query = [table query];

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

Mit dem MSQuery-Objekt können Sie die folgenden Abfrageeigenschaften steuern:

* Angabe der Reihenfolge für zurückgegebene Ergebnisse.
* Einschränkung der zurückzugebenden Felder.
* Einschränkung der Anzahl der zurückzugebenden Einträge.
* Auswählen, ob die Gesamtzahl in der Antwort enthalten sein soll.
* Angeben benutzerdefinierter Abfrageparameter für die Anfrage.

Sie können Ihre Abfragen weiter verfeinern, indem Sie eine oder mehrere Funktionen anwenden. Sobald Sie die Abfrage definiert haben, können Sie diese durch Aufrufen der **readWithCompletion**-Funktion ausführen.

#### <a name="sorting"></a>Zurückgegebene Daten sortieren

Mit den folgenden Funktionen können Sie die Felder für die Sortierung angeben:

	-(void) orderByAscending:(NSString *)field
	-(void) orderByDescending:(NSString *)field

Diese Abfrage sortiert die Ergebnisse zunächst nach Dauer und anschließend nach Fertigstellungsstatus der Aufgabe:

	[query orderByAscending:@"duration"];
	[query orderByAscending:@"complete"];
	[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		//code to parse results here
	}];

#### <a name="paging"></a>Daten seitenweise zurückgeben

Mobile Services kann in einer einzelnen Antwort nur eine bestimmte Anzahl Datensätze zurückgeben. Um die Anzahl der Datensätze zu steuern, die den Benutzern angezeigt werden, müssen Sie ein Paging-System implementieren.  Paging wird durchgeführt, indem Sie die folgenden drei Eigenschaften des **MSQuery**-Objekts verwenden:

+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`


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

Diese Parameter werden in der Form `myKey1=value1&myKey2=value2` an die Abfrage-URI angehängt.
 Weitere Informationen finden Sie unter [Gewusst wie: Zugreifen auf benutzerdefinierte Parameter].

<h2><a name="inserting"></a>Gewusst wie: Einfügen von Daten in einen mobilen Dienst</h2>

Um eine neue Tabelle in eine Zeile einzufügen, erstellen Sie ein neues [NSDictionary-Objekt] und übergeben es an die insert-Funktion. Der folgende Code fügt einen neuen Eintrag in die Tabelle ein:

	NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Mobile Services unterstützen eindeutige benutzerdefinierte Zeichenfolgenwerte als Tabellen-ID. Auf diese Weise können Anwendungen benutzerdefinierte Werte wie z. B. E-Mail-Adressen oder Benutzernamen in der ID-Spalte einer Mobile Services-Tabelle verwenden. Wenn die Zeile beispielsweise durch eine E-Mail-Adresse identifiziert werden soll, können Sie das folgende JSON-Objekt verwenden.

	NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Falls beim Einfügen neuer Datensätze in eine Tabelle kein Wert für die Zeichenfolgen-ID angegeben wird, wird ein eindeutiger ID-Wert von Mobile Services generiert.

Die Möglichkeit zum Verwenden von Zeichenfolgen-Ids bietet Entwicklern die folgenden Vorzüge

+ Ids können ohne Roundtrip zur Datenbank generiert werden.
+ Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
+ Id-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Sie können auch Serverskripts verwenden, um die Id-Werte zu setzen. Das folgende Skript generiert eine benutzerdefinierte GUID und verwendet diese als ID für einen neuen Eintrag. Dieser Wert ähnelt dem ID-Wert, den der mobile Dienst generieren würde, wenn in der Abfrage kein ID-Wert übergeben wird.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Wenn eine Anwendung einen ID-Wert übergibt, speichert der mobile Dienst diesen Wert unverändert. Dies beinhaltet auch vor- und nachstehende Leerzeichen. Leerzeichen werden aus dem Wert gekürzt.

Der `ID`-Wert muss eindeutig sein und darf keine Zeichen aus den folgenden Sätzen enthalten:

+ Steuerzeichen: [0x0000-0x001F] and [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1].
+  Druckbare Zeichen: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Die IDs "." und ".." 

Alternativ können Sie auch ganzzahlige IDs für Ihre Tabellen verwenden. Um ganzzahlige Ids zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI zum Verwalten von Mobile Services-Tabellen].

Wenn das dynamische Schema aktiviert ist, generiert Mobile Services automatisch neue Spalten auf Grundlage der Felder des Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema].

<h2><a name="modifying"></a>Gewusst wie: Ändern von Daten in einem mobilen Dienst</h2>

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

<h2><a name="authentication"></a>Gewusst wie: Authentifizieren von Benutzern</h2>

Mobile Services unterstützt die folgenden Identitätsanbieter für die Authentifizierung von Benutzern:

- Facebook
- Google
- Microsoft Account
- Twitter
- Azure Active Directory

Weitere Informationen zur Konfiguration von Identitätsanbietern finden Sie unter [Erste Schritte mit der Authentifizierung].

Mobile Services unterstützt die beiden folgenden Authentifizierungsabläufe:

- Im Fall einer servergesteuerten Anmeldung verwaltet Mobile Services den Anmeldeprozess im Namen Ihrer App. Eine anbieterspezifische Anmeldeseite wird von der Clientbibliothek angezeigt, und Mobile Services übernimmt die Authentifizierung beim ausgewählten Anbieter.

- Im Fall einer clientgesteuerten Anmeldung fordert die App ein Token vom Identitätsanbieter an und legt dieses Token anschließend Mobile Services zur Authentifizierung vor.

Bei erfolgreicher Authentifizierung wird ein Benutzerobjekt zurückgegeben, das die zugewiesene Benutzer-ID und das Authentifizierungstoken enthält. Sie können diese Benutzer-ID in Serverskripts zur Validierung oder Änderung von Anfragen verwenden. Weitere Informationen finden Sie unter [Verwenden von Skripts zum Autorisieren von Benutzern]. Das Token kann sicher gespeichert und für nachfolgende Anmeldungen wiederverwendet werden.

Sie können auch Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Weitere Informationen finden Sie unter [Berechtigungen].

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

Das folgende Beispiel verwendet das [Live Connect SDK], um die einmalige Anmeldung für iOS-Apps zu implementieren.

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

This code assumes that you have previously created a **LiveConnectClient**-Instanz mit dem Namen `liveClient` im Controller und der Benutzer ist angemeldet.

###<a name="caching-tokens"></a>Gewusst wie: Zwischenspeichern von Authentifizierungstokens

Um zu verhindern, dass sich Benutzer bei jeder Ausführung Ihrer Anwendung authentifizieren müssen, können Sie die aktuelle Benutzeridentität nach der Anmeldung zwischenspeichern. Anschließend können Sie den Benutzer mit diesen Informationen direkt erstellen und den Anmeldungsprozess umgehen.  Hierzu müssen Sie die Benutzer-ID und den Authentifizierungstoken lokal speichern. Im folgenden Beispiel wird das Token direkt in [KeyChain] gespeichert:

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
<p>Token sind vertrauliche Daten und müssen verschlüsselt gespeichert werden, falls das Gerät verloren geht oder gestohlen wird.</p>
</div>

Mit einem zwischengespeicherten Token muss sich der Benutzer erst dann wieder anmelden, wenn das Token abläuft. Wenn sich ein Benutzer mit einem abgelaufenen Token anmeldet, wird die Antwort "401 unauthorized" zurückgegeben. Der Benutzer muss sich in diesem Fall neu anmelden und ein neues Token abrufen, das erneut zwischengespeichert werden kann. Sie können Filter verwenden, um zu vermeiden, dass Sie Code schreiben, der abgelaufene Token verarbeitet, wenn Ihre App den mobilen Dienst aufruft.  Mit Filtern können Sie Aufrufe an und Antworten von Ihrem mobilen Dienst abfangen. Der Filtercode prüft Antworten auf 401, löst den Anmeldeprozess aus, falls das Token abgelaufen ist, und setzt anschließend die Anfrage fort, die den 401-Fehler ausgelöst hatte. Details finden Sie unter [Umgang mit abgelaufenen Token].

<h2><a name="errors"></a>Gewusst wie: Fehlerbehandlung</h2>

Bei Aufrufen an mobile Dienste enthält der completion-Block einen `NSError *error`-Parameter. Wenn ein Fehler auftritt, enthält dieser Parameter einen Wert unterschiedlich von null. Sie sollten diesen Parameter in Ihrem Code prüfen und Fehler entsprechend behandeln.

Im Fehlerfall erhalten Sie weitere Informationen, indem Sie die Datei MSError.h in Ihrem Code einbinden:

    #import <WindowsAzureMobileServices/MSError.h>

Diese Datei definiert die folgenden Konstanten, die Sie für den Zugriff auf zusätzliche Daten aus `[error userInfo]` verwenden können:

+ **MSErrorResponseKey**: die HTTP-Antwortdaten des Fehlers
* **MSErrorRequestKey**: die HTTP-Anfragedaten des Fehlers

Außerdem ist für jeden Fehlercode eine Konstante definiert. Sie finden eine Erklärung dieser Codes in der Datei MSError.h.

Ein Beispiel für die Validierung und Behandlung von Fehlern finden Sie unter [Validierung und Änderung von Daten in Mobile Services mithilfe von Serverskripts]. In diesem Artikel erfolgt die serverseitige Validierung in Form von Serverskripts. Wenn ungültige Daten übermittelt werden, wird eine Fehlermeldung zurückgegeben und vom Client verarbeitet.

<!--
<h2><a name="#unit-testing"></a>Gewusst wie: Design von Komponententests</h2>

_(Optional) In diesem Abschnitt wird gezeigt, wie Sie bei Verwendung der Clientbibliothek einen Komponententest schreiben (Info über Yavor). _

<h2><a name="#customizing"></a>Gewusst wie: Anpassen des Clients</h2>

_(Optional) In diesem Abschnitt wird gezeigt, wie Sie angepasstes Clientverhalten senden. _

###<a name="custom-headers"></a>Gewusst wie: Anpassen der Anforderungsheader

_(Optional) In diesem Abschnitt wird gezeigt, wie Sie benutzerdefinierte Anforderungsheader senden. _

Weitere Informationen finden Sie unter dem neuen Thema zur Verarbeitung von Kopfzeilen auf der Serverseite.

###<a name="custom-serialization"></a>Gewusst wie: Anpassen der Serialisierung

_(Optional) In diesem Abschnitt wird gezeigt, wie Sie Attribute verwenden, um anzupassen, wie Datentypen serialisiert werden._

Weitere Informationen finden Sie unter dem neuen Thema zur Verarbeitung von Kopfzeilen auf der Serverseite.

## <a name="next-steps"></a>Nächste Schritte
-->

<!-- Anchors. -->

[Windows Azure Mobile Services]: #what-is
[Konzepte]: #concepts
[Einrichtung und Voraussetzungen]: #Setup
[Gewusst wie: Erstellen des Mobile Services-Clients]: #create-client
[Gewusst wie: Erstellen eines Tabellenverweises]: #table-reference
[Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]: #querying
[Zurückgegebene Daten filtern]: #filtering
[Zurückgegebene Daten sortieren]: #sorting
[Daten seitenweise zurückgeben]: #paging
[Bestimmte Spalten auswählen]: #selecting
[Gewusst wie: Datenbindung in der Benutzeroberfläche]: #binding
[Gewusst wie: Einfügen von Daten in einen mobilen Dienst]: #inserting
[Gewusst wie: Ändern von Daten in einem mobilen Dienst]: #modifying
[Gewusst wie: Authentifizieren von Benutzern]: #authentication
[Zwischenspeichern von Authentifizierungstokens]: #caching-tokens
[Gewusst wie: Hochladen von Bildern und großen Dateien]: #blobs
[Gewusst wie: Fehlerbehandlung]: #errors
[Gewusst wie: Design von Komponententests]: #unit-testing
[Gewusst wie: Anpassen des Clients]: #customizing
[Anpassen der Anforderungsheader]: #custom-headers
[Anpassen der Datentyp-Serialisierung]: #custom-serialization
[Nächste Schritte]: #next-steps
[Gewusst wie: Verwenden von MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Umgang mit abgelaufenen Token]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Berechtigungen]: http://msdn.microsoft.com/de-de/library/windowsazure/jj193161.aspx
[Verwenden von Skripts zum Autorisieren von Benutzern]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamisches Schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[Gewusst wie: Zugreifen auf benutzerdefinierte Parameter]: /de-de/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Erstellen einer Tabelle]: http://msdn.microsoft.com/de-de/library/windowsazure/jj193162.aspx
[NSDictionary-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII-Steuerungscodes C0 und C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI zum Verwalten von Mobile Services-Tabellen]: http://www.windowsazure.com/de-de/manage/linux/other-resources/command-line-tools/#Mobile_Tables
