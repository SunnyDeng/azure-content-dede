<properties
	pageTitle="Verwenden der iOS-Clientbibliothek für Azure Mobile Services"
	description="Verwenden der iOS-Clientbibliothek für Mobile Services"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# Verwenden der iOS-Clientbibliothek für Azure Mobile Services

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des Azure Mobile Services [iOS SDK]. Wenn Sie noch nicht mit Mobile Services vertraut sind, führen Sie zuerst das Lernprogramm[Mobile Services-Schnellstart] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] durch, um Ihr Konto zu konfigurieren, eine Tabelle zu erstellen und einen mobilen Dienst zu erstellen.

> [AZURE.NOTE]Dieses Handbuch verwendet das neueste [iOS-SDK für Mobile Services](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Wenn das Projekt eine ältere Version des SDK verwendet, aktualisieren Sie zuerst das Framework in Xcode.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Einrichtung und Voraussetzungen

Dieses Lernprogramm setzt voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle]. Alternativ können Sie die `TodoItem`-Tabelle verwenden, die Sie im Lernprogramm [Mobile Services-Schnellstart] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] erstellt haben. In dieser Anleitung wird davon ausgegangen, dass die Tabelle das gleiche Schema wie die Tabellen in diesen Lernprogrammen aufweist. Weiter wird davon ausgegangen, dass der Xcode auf `WindowsAzureMobileServices.framework` verweist und `WindowsAzureMobileServices/WindowsAzureMobileServices.h` importiert.

##<a name="create-client"></a>Gewusst wie: Erstellen des Mobile Services-Clients

Erstellen Sie für den Zugriff auf einen mobilen Azure-Dienst in Ihrem Projekt ein `MSClient`-Clientobjekt. Ersetzen Sie `AppUrl` und `AppKey` mit der URL des mobilen Dienstes bzw. dem Dashboard-Wert für den Anwendungsschlüssel.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

##<a name="table-reference"></a>Gewusst wie: Erstellen von Tabellenverweisen

Erstellen Sie für den Zugriff auf den mobilen Azure-Dienst und die Aktualisierung des Dienstes einen Verweis auf die Tabelle. Ersetzen Sie `TodoItem` durch den Namen Ihrer Tabelle.

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>Gewusst wie: Abfragen von Daten

Fragen Sie zum Erstellen einer Abfrage das `MSTable`-Objekt ab. Die folgende Abfrage ruft alle Elemente im `TodoItem` ab und protokolliert den Text von jedem Element.

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="filtering"></a>Gewusst wie: Filtern zurückgegebener Daten

Für das Filtern von Ergebnissen stehen zahlreiche Optionen zur Verfügung.

Um mithilfe eines Prädikats zu filtern, verwenden Sie `NSPredicate` und `readWithPredicate`. Mit den folgenden Filtern werden nur Daten zu unvollständigen Todo-Elemente zurückgegeben.

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="query-object"></a>Gewusst wie: Verwenden von MSQuery

Um eine komplexe Abfrage (einschließlich Sortierung und Paging) auszuführen, erstellen Sie ein `MSQuery`-Objekt entweder direkt oder durch Verwenden eines Prädikats:

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

Mit `MSQuery` können Sie verschiedene Abfrageeigenschaften steuern, darunter die Folgenden. Führen Sie eine `MSQuery`-Abfrage durch, indem Sie `readWithCompletion` für MSQuery aufrufen, wie im nächsten Beispiel gezeigt. * Reihenfolge der Ergebnisse angeben * Zurückzugebenden Felder einschränken * Zurückzugebenden Datensätze einschränken * Gesamtzahl in der Antwort angeben * Benutzerdefinierte Abfragezeichenfolgen-Parameter in der Anforderung angeben * Zusätzliche Funktionen anwenden


## <a name="sorting"></a>Gewusst wie: Sortieren von Daten mit MSQuery

Um die Ergebnisse zu sortieren, sehen wir uns ein Beispiel an. Um zuerst aufsteigend nach Feld `text` und dann absteigend nach Feld `completion` zu sortieren, rufen Sie `MSQuery` wie folgt auf:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

## <a name="paging"></a>Gewusst wie: Seitenweises Zurückgeben von Daten

Mobile Services kann in einer einzelnen Antwort nur eine bestimmte Anzahl Datensätze zurückgeben. Sie müssen daher eine Seitenverwaltung implementieren, um die Anzahl der Datensätze zu steuern, die Ihren Benutzern angezeigt werden. Für die Seitenverwaltung verwenden Sie die folgenden drei Eigenschaften des **MSQuery**-Objekts:

```
+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`
```

Im folgenden Beispiel fragt eine einfache Funktion 5 Einträge vom Server ab und fügt diese an eine lokale Sammlung mit bereits abgefragten Einträgen an:

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)   				BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}

```

## <a name="selecting"></a><a name="parameters"></a>Gewusst wie: Einschränken von Feldern und Erweitern von Abfragezeichenfolgen-Parametern mit MSQuery

Um die in einer Abfrage zurückzugebenden Felder einzuschränken, geben Sie die Namen der Felder in der **SelectFields**-Eigenschaft an. Hier werden nur der Text und abgeschlossene Felder angezeigt:

```
	query.selectFields = @[@"text", @"completed"];
```

Um zusätzliche Abfragezeichenfolgen-Parameter in die Anforderung einzubinden (z. B. weil diese Parameter von einem benutzerdefinierten serverseitigen Skript verwendet werden), füllen Sie `query.parameters` wie folgt aus:

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>Gewusst wie: Einfügen von Daten

Um eine neue Tabellenzeile einzufügen, erstellen Sie ein neues `NSDictionary`, und rufen Sie `table insert` auf. Mobile Services generiert automatisch neue Spalten basierend auf `NSDictionary`, wenn das [dynamische Schema] nicht deaktiviert ist.

Wenn `id` nicht angegeben wird, generiert das Back-End automatisch eine neue eindeutige ID. Geben Sie eine eigene `id` ein, um E-Mail-Adressen, Benutzernamen oder einen eigenen benutzerdefinierten Wert als ID zu verwenden. Die Bereitstellung einer eigenen ID kann Joins und die geschäftsorientierte Datenbanklogik vereinfachen.

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>Gewusst wie: Ändern von Daten

Um eine vorhandene Zeile zu aktualisieren, ändern Sie ein Element, und rufen Sie `update` auf:

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Geben Sie alternativ die Zeilen-ID und das aktualisierte Feld ein:

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Für Änderungen muss zumindest das `id`-Attribut gesetzt sein.

##<a name="deleting"></a>Gewusst wie: Löschen von Daten

Um ein Element zu löschen, rufen Sie `delete` mit dem Element auf:

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Geben Sie alternativ eine Zeilen-ID ein, um das Element zu löschen:

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Für Löschungen muss zumindest das `id`-Attribut gesetzt sein.

##<a name="#custom-api"></a>Gewusst wie: Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats. Ein Beispiel für das Erstellen einer benutzerdefinierten API in Ihrem mobilen Dienst finden Sie unter [Gewusst wie: Definieren eines benutzerdefinierten API-Endpunkts](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]


##<a name="authentication"></a>Gewusst wie: Authentifizieren von Benutzern

Azure Mobile Services unterstützt verschiedene Identitätsanbieter. Ein grundlegendes Lernprogramm finden Sie unter [Authentifizierung].

Azure Mobile Services unterstützt zwei Authentifizierungsabläufe:

- **Servergesteuerte Anmeldung**: Azure Mobile Services verwaltet den Anmeldeprozess im Namen Ihrer App. Es wird eine anbieterspezifische Anmeldeseite angezeigt, und der Benutzer mit dem ausgewählten Anbieter authentifiziert.

- **Clientgesteuerte Anwendung**: Die _App_ fordert einen Token vom Identitätsanbieter an und legt dieses Token Azure Mobile Services zur Authentifizierung vor.

Wenn die Authentifizierung erfolgreich ist, erhalten Sie ein Benutzerobjekt mit einem Benutzer-ID-Wert und dem Authentifizierungstoken zurück. Informationen zum Verwenden dieser Benutzer-ID zum Autorisieren von Benutzern finden Sie unter [Dienstseitige Autorisierung]. Informationen zum Beschränken des Zugriffs auf authentifizierte Benutzer finden Sie unter [Berechtigungen].

### Servergesteuerte Anmeldung

Hier erfahren Sie, wie Sie die servergesteuerte Anmeldung zum Projekt aus [Mobile Services-Schnellstart] hinzufügen. Verwenden Sie ähnlichen Code für Ihre anderen Projekte. Weitere Informationen und ein End-to-End-Beispiel in Aktion finden Sie unter [Authentifizierung].

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### Clientgesteuerte Anmeldung (Einmaliges Anmelden)

Sie können den Anmeldeprozess außerhalb des Mobile Services-Clients durchführen, wenn Sie einmaliges Anmelden aktivieren möchten oder wenn Ihre App den Identitätsanbieter direkt kontaktiert. In diesen Fällen können Sie sich bei Mobile Services anmelden, indem Sie ein Token übergeben, das Sie unabhängig von einem unterstützten Identitätsanbieter erhalten haben.

Das folgende Beispiel verwendet das [Live Connect SDK], um die einmalige Anmeldung für iOS-Apps zu implementieren. Dieses Beispiel geht davon aus, dass Sie eine **LiveConnectClient**-Instanz mit dem Namen `liveClient` im Controller erstellt haben und der Benutzer angemeldet ist.

```
	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
				// Handle success and errors
	}];
```

##<a name="caching-tokens"></a>Gewusst wie: Zwischenspeichern von Authentifizierungstoken

Im Folgenden wird gezeigt, wie Sie Token im Projekt aus [Mobile Services-Schnellstart] zwischenspeichern. Sie können ähnliche Schritte für jedes andere Projekt verwenden.[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="errors"></a>Gewusst wie: Fehlerbehandlung

Beim Aufrufen eines mobilen Dienstes enthält der completion-Block einen `NSError *error`-Parameter. Wenn ein Fehler auftritt, ist der Wert dieses Parameters “non-nil“. Sie sollten diesen Parameter in Ihrem Code prüfen und Fehler entsprechend behandeln.

Die Datei [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) definiert die Konstanten `MSErrorResponseKey`, `MSErrorRequestKey`, und `MSErrorServerItemKey`, um mehr Daten im Zusammenhang mit dem Fehler zu erhalten. Darüber hinaus definiert die Datei Konstanten für jeden Fehlercode. Ein Beispiel für die Verwendung dieser Konstanten finden Sie unter [Konflikthandler] und die Verwendung von `MSErrorServerItemKey` und `MSErrorPreconditionFailed`.

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
[Hinzufügen von Mobile Services zu einer vorhandenen App]: /develop/mobile/tutorials/get-started-data
[Mobile Services-Schnellstart]: /develop/mobile/tutorials/get-started-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Berechtigungen]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Dienstseitige Autorisierung]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[dynamische Schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Erstellen einer Tabelle]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Konflikthandler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=Sept15_HO3-->