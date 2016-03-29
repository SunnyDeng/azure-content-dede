<properties
	pageTitle="Vorgehensweise beim Verwenden des iOS-SDKs für Azure Mobile Apps"
	description="Vorgehensweise beim Verwenden des iOS-SDKs für Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

# Verwenden der iOS-Clientbibliothek für Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des aktuellsten [Azure Mobile Apps-iOS-SDKs](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Wenn Sie mit Azure Mobile Apps noch nicht vertraut sind, führen Sie zunächst den [Schnellstart von Azure Mobile Apps] durch, um ein Back-End und eine Tabelle zu erstellen und ein vorgefertigtes iOS-Xcode-Projekt herunterzuladen. In diesem Handbuch konzentrieren wir uns auf das clientseitige iOS-SDK. Weitere Informationen zum serverseitigen .NET-SDK für das Back-End finden Sie unter [Arbeiten mit dem .NET Back-End](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## Referenzdokumentation

Das Referenzdokumentation für das iOS-Client-SDK finden Sie hier: [Azure Mobile Apps iOS-Client-Referenz](http://azure.github.io/azure-mobile-services/iOS/v3/).

##<a name="Setup"></a>Einrichtung und Voraussetzungen

Dieses Lernprogramm setzt voraus, dass Sie ein Back-End mit einer Tabelle erstellt haben. In dieser Anleitung wird davon ausgegangen, dass die Tabelle das gleiche Schema wie die Tabellen in diesen Lernprogrammen aufweist. Dieses Handbuch setzt außerdem voraus, dass Sie in Ihrem Code auf das `MicrosoftAzureMobile.framework` verweisen und `MicrosoftAzureMobile/MicrosoftAzureMobile.h` importieren.

##<a name="create-client"></a>Vorgehensweise beim Erstellen von Clients

Erstellen Sie für den Zugriff auf ein Azure Mobile Apps-Back-End in Ihrem Projekt einen `MSClient`. Ersetzen Sie `AppUrl` durch die URL der App. Sie können `gatewayURLString` und `applicationKey` leer lassen. Wenn Sie ein Gateway für die Authentifizierung einrichten, befüllen Sie `gatewayURLString` mit der Gateway-URL.

**Objective-C:**

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift:**

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Gewusst wie: Erstellen von Tabellenverweisen

Zum Zugreifen auf oder Aktualisieren von Daten erstellen Sie einen Verweis auf die Back-End-Tabelle. Ersetzen Sie `TodoItem` durch den Namen Ihrer Tabelle.

**Objective-C:**

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift:**

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Gewusst wie: Abfragen von Daten

Fragen Sie zum Erstellen einer Abfrage das `MSTable`-Objekt ab. Die folgende Abfrage ruft alle Elemente im `TodoItem` ab und protokolliert den Text von jedem Element.

**Objective-C:**

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

**Swift:**

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Gewusst wie: Filtern zurückgegebener Daten

Für das Filtern von Ergebnissen stehen zahlreiche Optionen zur Verfügung.

Um mithilfe eines Prädikats zu filtern, verwenden Sie `NSPredicate` und `readWithPredicate`. Mit den folgenden Filtern werden nur Daten zu unvollständigen Todo-Elemente zurückgegeben.

**Objective-C:**

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
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

**Swift:**

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Gewusst wie: Verwenden von MSQuery

Um eine komplexe Abfrage (einschließlich Sortierung und Paging) auszuführen, erstellen Sie ein `MSQuery`-Objekt entweder direkt oder durch Verwenden eines Prädikats:

**Objective-C:**

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift:**

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

Mit `MSQuery` können Sie verschiedene Abfrageeigenschaften steuern, darunter die Folgenden. Führen Sie eine `MSQuery`-Abfrage durch, indem Sie auf `readWithCompletion`, wie im folgenden Beispiel gezeigt, aufrufen.
* Festlegen der Reihenfolge der Ergebnisse
* Einschränkung der zurückzugebenden Felder
* Einschränkung der Anzahl der zurückzugebenden Datensätze
* Festlegen der Gesamtanzahl in der Antwort
* Festlegen benutzerdefinierter Abfrageparameter in der Anforderung
* Anwenden zusätzlicher Funktionen


## <a name="sorting"></a>Gewusst wie: Sortieren von Daten mit MSQuery

Um die Ergebnisse zu sortieren, sehen wir uns ein Beispiel an. Um zuerst aufsteigend nach Feld `text` und dann absteigend nach Feld `completion` zu sortieren, rufen Sie `MSQuery` wie folgt auf:

**Objective-C:**

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

**Swift:**

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Gewusst wie: Einschränken von Feldern und Erweitern von Abfragezeichenfolgen-Parametern mit MSQuery

Um die in einer Abfrage zurückzugebenden Felder einzuschränken, geben Sie die Namen der Felder in der **SelectFields**-Eigenschaft an. Hier werden nur der Text und abgeschlossene Felder angezeigt:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Um zusätzliche Abfragezeichenfolgen-Parameter in die Anforderung einzubinden (z. B. weil diese Parameter von einem benutzerdefinierten serverseitigen Skript verwendet werden), füllen Sie `query.parameters` wie folgt aus:

**Objective-C**:

```
query.parameters = @{
	@"myKey1" : @"value1",
	@"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

##<a name="inserting"></a>Gewusst wie: Einfügen von Daten

Um eine neue Tabellenzeile einzufügen, erstellen Sie ein neues `NSDictionary`, und rufen Sie `table insert` auf. Mobile Services generiert automatisch neue Spalten basierend auf `NSDictionary`, wenn das [dynamische Schema] nicht deaktiviert ist.

Wenn `id` nicht angegeben wird, generiert das Back-End automatisch eine neue eindeutige ID. Geben Sie eine eigene `id` ein, um E-Mail-Adressen, Benutzernamen oder einen eigenen benutzerdefinierten Wert als ID zu verwenden. Die Bereitstellung einer eigenen ID kann Joins und die geschäftsorientierte Datenbanklogik vereinfachen.

Das `result` enthält das neue eingefügte Element. Abhängig von Ihrer Serverlogik kann es im Vergleich zur ursprünglichen Übergabe an den Server zusätzliche oder geänderte Daten enthalten.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Gewusst wie: Ändern von Daten

Um eine vorhandene Zeile zu aktualisieren, ändern Sie ein Element, und rufen Sie `update` auf:

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Geben Sie alternativ die Zeilen-ID und das aktualisierte Feld ein:

**Objective-C:**

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift:**

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Für Änderungen muss zumindest das `id`-Attribut gesetzt sein.

##<a name="deleting"></a>Gewusst wie: Löschen von Daten

Um ein Element zu löschen, rufen Sie `delete` mit dem Element auf:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Geben Sie alternativ eine Zeilen-ID ein, um das Element zu löschen:

**Objective-C:**

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift:**

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Für Löschungen muss zumindest das `id`-Attribut gesetzt sein.

##<a name="customapi"></a>Gewusst wie: Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie beliebige Back-End-Funktionen verfügbar machen. Sie muss keinem Tabellenvorgang zuordnet sein. Sie erhalten nicht nur mehr Kontrolle über das Messaging, sondern können sogar Header lesen/festlegen und das Layout der Antwort ändern. Informationen zum Erstellen einer benutzerdefinierten API auf dem Back-End finden Sie unter [Benutzerdefinierte APIs](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis).

Rufen Sie zum Öffnen einer benutzerdefinierten APIs `MSClient.invokeAPI` wie unten dargestellt auf. Der Inhalt der Anforderung und der Antwort wird als JSON behandelt. Um weitere Medientypen zu nutzen, [verwenden Sie die andere Überladung von `invokeAPI`](http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:).

Um eine `GET`-Anforderung anstelle einer `POST`-Anforderung zu erstellen, setzen Sie den Parameter `HTTPMethod` auf `"GET"` und den Parameter `body` auf `nil` (da GET-Anforderungen keinen Nachrichtentext enthalten). Wenn Ihre benutzerdefinierte API anderen HTTP-Verben unterstützt, ändern Sie `HTTPMethod` entsprechend.

**Objective-C**:
```
    [self.client invokeAPI:@"sendEmail"
                      body:@{ @"contents": @"Hello world!" }
                HTTPMethod:@"POST"
                parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
                   headers:nil
                completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                    if(error) {
                        NSLog(@"ERROR %@", error);
                    } else {
                        // Do something with result
                    }
                }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Gewusst wie: Registrieren von Pushvorlagen zum Senden plattformübergreifender Benachrichtigungen

Um Vorlagen zu registrieren, übergeben Sie einfach die Vorlagen mit Ihrer **client.push registerDeviceToken**-Methode in Ihrer Client-App.

**Objective-C:**

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	}
}];
```

**Swift:**

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Ihre Vorlagen vom Typ „NSDictionary“ und können mehrere Vorlagen im folgenden Format enthalten:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Beachten Sie, dass aus Sicherheitsgründen alle Tags entfernt werden. Informationen zum Hinzufügen von Tags zu Installationen bzw. Vorlagen innerhalb von Installationen finden Sie unter [Verwenden des .NET-Back-End-Server SDK für Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

Zum Senden von Benachrichtigungen, die diese registrierten Vorlagen verwenden, arbeiten Sie mit den [Benachrichtigungs-Hub-APIs](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="errors"></a>Gewusst wie: Fehlerbehandlung

Beim Aufrufen eines mobilen Dienstes enthält der completion-Block einen `NSError`-Parameter. Wenn ein Fehler auftritt, ist der Wert dieses Parameters “non-nil“. Sie sollten diesen Parameter in Ihrem Code prüfen und Fehler entsprechend behandeln, wie in den oben aufgeführten Codeausschnitten veranschaulicht.

Die Datei [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) definiert die Konstanten `MSErrorResponseKey`, `MSErrorRequestKey` und `MSErrorServerItemKey`, um mehr Daten im Zusammenhang mit dem Fehler zu erhalten. Sie können wie folgt abgerufen werden:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Darüber hinaus definiert die Datei Konstanten für jeden Fehlercode. Sie können wie folgt verwendet werden:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Gewusst wie: Authentifizieren von Benutzern mit der Active Directory-Authentifizierungsbibliothek

Nutzen Sie die Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL), um Benutzer mithilfe von Azure Active Directory bei Ihrer Anwendung anzumelden. Dies wird aufgrund der besseren Bedienbarkeit und der Möglichkeit, zusätzliche Anpassungen vorzunehmen, den `loginAsync()`-Methoden häufig vorgezogen.

1. Konfigurieren Sie Ihr mobiles App-Back-End für die AAD-Anmeldung, indem Sie die im Tutorial [So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung](app-service-mobile-how-to-configure-active-directory-authentication.md) beschriebenen Schritte ausführen. Schließen Sie auch den optionalen Schritt zur Registrierung einer nativen Clientanwendung ab. Bei iOS ist es empfehlenswert (aber nicht erforderlich), den Umleitungs-URI im Format `<app-scheme>://<bundle-id>` anzugeben. Weitere Einzelheiten finden Sie in der [Schnellstartanleitung für die ADAL unter iOS](active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem).

2. Installieren Sie die ADAL mithilfe von Cocoapods. Bearbeiten Sie Ihr Podfile, um Folgendes zu integrieren, und ersetzen Sie dabei **YOUR-PROJECT** durch den Namen Ihres Xcode-Projekts:

		source 'https://github.com/CocoaPods/Specs.git'
		link_with ['YOUR-PROJECT']
		xcodeproj 'YOUR-PROJECT'
und der Pod:

		pod 'ADALiOS'

3. Führen Sie unter Verwendung des Terminals `pod install` im Verzeichnis mit Ihrem Projekt aus, und öffnen Sie den erstellten Xcode-Arbeitsbereich (nicht das Projekt).

4. Fügen Sie auf Grundlage der verwendeten Sprache den unten stehenden Code zu Ihrer Anwendung hinzu. Nehmen Sie dabei die folgenden Änderungen vor:

* Ersetzen Sie **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, unter dem Sie Ihre Anwendung bereitgestellt haben. Das Format sollte https://login.windows.net/contoso.onmicrosoft.com entsprechen. Sie können diesen Wert auf der Registerkarte „Domäne“ in Ihrer Azure Active Directory-Instanz im [klassischen Azure-Portal] kopieren.

* Ersetzen Sie **INSERT-RESOURCE-ID-HERE** durch die Client-ID für Ihr mobiles App-Back-End. Sie finden diese im Portal auf der Registerkarte **Erweitert** unter **Azure Active Directory-Einstellungen**.

* Ersetzen Sie **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der nativen Clientanwendung kopiert haben.

* Ersetzen Sie **INSERT-REDIRECT-URI-HERE** durch den Endpunkt _/.auth/login/done_ Ihrer Website mithilfe des HTTPS-Schemas. Dieser Wert sollte etwa so aussehen: \__https://contoso.azurewebsites.net/.auth/login/done_.

**Objective-C**:

	#import <ADALiOS/ADAuthenticationContext.h>
	#import <ADALiOS/ADAuthenticationSettings.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*))completionBlock;
	{
	    NSString *authority = @"INSERT-AUTHORITY-HERE";
	    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
	    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
	    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
	    ADAuthenticationError *error;
	    ADAuthenticationContext authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
	    authContext.parentController = parent;
	    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
	    [authContext acquireTokenWithResource:resourceId
	                                 clientId:clientId
	                              redirectUri:redirectUri
	                          completionBlock:^(ADAuthenticationResult *result) {
	                              if (result.status != AD_SUCCEEDED)
	                              {
	                                  completionBlock(nil, result.error);;
	                              }
	                              else
	                              {
	                                  NSDictionary *payload = @{
	                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
	                                                            };
	                                  [client loginWithProvider:@"aad" token:payload completion:completionBlock];
	                              }
	                          }];
	}


**Swift**:

	// add the following imports to your bridging header:
	//     #import <ADALiOS/ADAuthenticationContext.h>
	//     #import <ADALiOS/ADAuthenticationSettings.h>

	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let authority = "INSERT-AUTHORITY-HERE"
		let resourceId = "INSERT-RESOURCE-ID-HERE"
		let clientId = "INSERT-CLIENT-ID-HERE"
		let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
		var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
		let authContext = ADAuthenticationContext(authority: authority, error: error)
		authContext.parentController = parent
		ADAuthenticationSettings.sharedInstance().enableFullScreen = true
		authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
		        if result.status != AD_SUCCEEDED {
		            completion(nil, result.error)
		        }
		        else {
		            let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
		            client.loginWithProvider("aad", token: payload, completion: completion)
		        }
    		}
	}


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
[Schnellstart von Azure Mobile Apps]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[dynamische Schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0316_2016-->