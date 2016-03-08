<properties pageTitle="Azure Active Directory B2C-Vorschau: Aufrufen von Web-APIs aus einer iOS-Anwendung | Microsoft Azure" description="In diesem Artikel wird beschrieben, wie Sie eine iOS-App für Aufgabenlisten erstellen, die eine Node.js-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die iOS-App als auch die Web-API verwenden Azure Active Directory B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Azure AD B2C-Vorschau: Aufrufen von Web-APIs aus einer iOS-Anwendung

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Mit Azure Active Directory (Azure AD) B2C können Sie Ihren iOS-Apps und Web-APIs in wenigen Schritten leistungsstarke Self-Service-Features zur Identitätsverwaltung hinzufügen. In diesem Artikel wird gezeigt, wie Sie eine iOS-App für Aufgabenlisten erstellen, die eine Node.js-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die iOS-App als auch die Web-API verwenden Azure AD B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Für diesen Schnellstart benötigen Sie eine Web-API, die durch Azure AD B2C geschützt wird. Wir haben sowohl für .NET als auch für Node.js eine für Sie vorbereitet. Bei dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass das Node.js-Web-API-Beispiel konfiguriert wurde. Weitere Informationen dazu finden Sie im [Azure Active Directory-Web-API-Beispiel für Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]
	Dieser Artikel behandelt nicht das Implementieren der Anmeldung, Registrierung und Profilverwaltung mit Azure AD B2C. Er konzentriert sich auf das Aufrufen von Web-APIs nach der Authentifizierung des Benutzers. Wenn nicht bereits erfolgt, beginnen Sie mit dem [Tutorial zu den ersten Schritten mit .NET-Web-Apps](active-directory-b2c-devquickstarts-web-dotnet.md), um Informationen zu den Grundlagen von Azure AD B2C zu erhalten.

## Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Ihre Benutzer, Apps, Gruppen usw. Erstellen Sie, sofern noch nicht geschehen, [Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

## Erstellen einer Anwendung

Als Nächstes müssen Sie in Ihrem B2C-Verzeichnis eine App erstellen. Dadurch werden Azure AD die Informationen bereitgestellt, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Die App und die Web-API werden in diesem Fall durch eine einzelne **Anwendungs-ID** dargestellt, da sie zusammen eine logische App bilden. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Führen Sie folgende Schritte aus:

- Fügen Sie der Anwendung eine **Web-App/Web-API** hinzu.
- Geben Sie `http://localhost:3000/auth/openid/return` als **Antwort-URL** ein. Dies ist die Standard-URL für dieses Codebeispiel.
- Erstellen Sie einen **geheimen Schlüssel** für Ihre Anwendung, und kopieren Sie ihn. Sie benötigen sie später.
- Kopieren Sie die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Diese benötigen sie später ebenfalls.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [Richtlinie](active-directory-b2c-reference-policies.md) definiert. Diese Anwendung enthält drei Oberflächen für die Identität: Registrierung, Anmeldung und Facebook-Anmeldung. Sie müssen eine Richtlinie jedes Typs erstellen, wie es im [Artikel zu Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben ist. Beachten Sie beim Erstellen der drei Richtlinien Folgendes:

- Wählen Sie den **Anzeigenamen** und die Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche in jeder Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen später.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erstellt haben, können Sie Ihre App erstellen.

Beachten Sie, dass in diesem Artikel nicht behandelt wird, wie die gerade erstellten Richtlinien verwendet werden. Grundlegende Informationen zur Funktionsweise von Richtlinien in Azure AD B2C finden Sie im [Tutorial zu den ersten Schritten mit .NET-Web-Apps](active-directory-b2c-devquickstarts-web-dotnet.md).

## Herunterladen des Codes

Der Code für dieses Tutorial wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS) verwaltet. Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). Sie können das Gerüst auch klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Sie müssen das Gerüst für dieses Tutorial herunterladen.** Aufgrund der Komplexität der Implementierung einer voll funktionsfähigen Anwendung unter iOS enthält das **Gerüst** UX-Code, der ausgeführt wird, nachdem Sie das Tutorial abgeschlossen haben. Mit dieser Maßnahme sollen Entwickler Zeit sparen. Der UX-Code ist für das Hinzufügen von B2C zu einer iOS-Anwendung nicht von Belang.

Die fertige App ist auch [als ZIP-Datei verfügbar](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) oder unter der Verzweigung `complete` im gleichen Repository enthalten.

Laden Sie anschließend `podfile` mithilfe von CocoaPods. Dadurch wird ein neuer XCode-Arbeitsbereich erstellt, den Sie nun laden. Wenn Sie über CocoaPods nicht verfügen, [richten Sie es über die entsprechende Website ein](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Konfigurieren der iOS-Aufgabenanwendung

Damit die iOS-Aufgaben-App mit Azure AD B2C kommunizieren kann, müssen Sie einige allgemeine Parameter angeben. Öffnen Sie im Ordner `Microsoft Tasks` die Datei `settings.plist` aus dem Stammverzeichnis des Projekts, und ersetzen Sie die Werte im Abschnitt `<dict>`. Diese Werte werden in der gesamten App verwendet.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Abrufen von Zugriffstoken und Aufrufen der Aufgaben-API

In diesem Abschnitt wird veranschaulicht, wie Sie einen OAuth 2.0-Tokenaustausch in einer Web-App mit den Bibliotheken und Frameworks von Microsoft durchführen. Falls Sie mit Autorisierungscodes und Zugriffstoken nicht vertraut sind, finden Sie in der [Referenz zum OAuth 2.0-Protokoll](active-directory-b2c-reference-protocols.md) weitere Informationen.

### Erstellen von Headerdateien mithilfe von Methoden

Sie benötigen Methoden, um ein Token mit Ihrer ausgewählten Richtlinie abzurufen und anschließend den Aufgabenserver aufzurufen. Richten Sie diese jetzt ein.

Erstellen Sie eine Datei mit dem Namen `samplesWebAPIConnector.h` unter `/Microsoft Tasks` in Ihrem Xcode-Projekt.

Fügen Sie den folgenden Code hinzu, um festzulegen, was zu tun ist:

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Dies sind einfache Vorgänge zum Erstellen, Lesen, Aktualisieren und Löschen (CRUD) für Ihre API sowie eine `doPolicy`-Methode. Mithilfe dieser Methode erhalten Sie ein Token mit der gewünschten Richtlinie.

Beachten Sie, dass noch zwei weitere Headerdateien definiert werden müssen. Diese enthalten Ihr Aufgabenelement und die Richtliniendaten. Erstellen Sie diese jetzt:

Erstellen Sie die Datei `samplesTaskItem.h` mit dem folgenden Code:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Erstellen Sie außerdem die Datei `samplesPolicyData.h` für die Richtliniendaten:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Hinzufügen einer Implementierung für Ihre Aufgaben- und Richtlinienelemente

Da die Headerdateien nun eingerichtet wurden, schreiben Sie Code zum Speichern der Daten, die Sie für Ihr Beispiel verwenden möchten.

Erstellen Sie die Datei `samplesPolicyData.m` mit dem folgenden Code:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Schreiben von Setupcode für den Aufruf an ADAL für iOS

Der kurze Code zum Speichern Ihrer Objekte für die Benutzeroberfläche ist nun fertig. Als Nächstes müssen Sie Code schreiben, um mithilfe der Parameter in `settings.plist` Zugriff auf die Active Directory Authentication Library (ADAL) für iOS zu erhalten. Dadurch wird ein Zugriffstoken für Ihren Aufgabenserver abgerufen.

Die gesamte Arbeit wird in `samplesWebAPIConnector.m` erledigt.

Erstellen Sie zunächst die `doPolicy()`-Implementierung, die Sie in Ihrer Headerdatei `samplesWebAPIConnector.h` geschrieben haben.

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

Die Methode ist einfach. Als Eingabe werden das erstellte Objekt `samplesPolicyData`, das übergeordnete Element `ViewController` und ein Rückruf verwendet. Der Rückruf ist interessant, weshalb wir ihn uns etwas näher ansehen.

- Beachten Sie, dass `completionBlock` über `ADProfileInfo` als Typ verfügt, der mithilfe eines `userInfo`-Objekts zurückgegeben wird. `ADProfileInfo` ist der Typ, der alle Antworten vom Server enthält, einschließlich der Ansprüche.
- Beachten Sie auch `readApplicationSettings`. Hiermit werden die Daten gelesen, die Sie in `settings.plist` angegeben haben.
- Anschließend erhalten Sie eine umfangreiche `getClaimsWithPolicyClearingCache`-Methode. Dies ist der eigentliche Aufruf von ADAL für iOS, den Sie schreiben müssen. Darauf kommen wir später zurück.

Schreiben Sie als Nächstes die umfangreiche `getClaimsWithPolicyClearingCache`-Methode. Aufgrund ihres Umfangs wird diese in einem separaten Abschnitt beschrieben.

### Erstellen Ihres Aufrufs von ADAL für iOS

Wenn Sie das Gerüst von GitHub heruntergeladen haben, werden Sie bemerken, dass zur Unterstützung der Beispielanwendung bereits einige dieser Aufrufe verwendet werden. Sie basieren alle auf dem Muster `get(Claims|Token)With<verb>ClearningCache`. Dabei werden Objective C-Konventionen verwendet, die fast wie normales Englisch gelesen werden können. Beispiel: „Get a Token with extra parameters I provide you and clear the cache“ (Token mit zusätzlichen von mir bereitgestellten Parametern abrufen und Cache löschen) ist `getTokenWithExtraParamsClearingCache()`.

Oder Sie schreiben „Get claims and a token with the policy that I provide to you and don't clear the cache“ (Ansprüche und ein Token mit der von mir bereitgestellten Richtlinie abrufen und Cache nicht löschen) bzw. `getClaimsWithPolicyClearingCache`. Sie erhalten von ADAL immer ein Token zurück. Daher ist es nicht erforderlich, in der Methode „claims and token“ (Ansprüche und Token) anzugeben. In einigen Fällen benötigen Sie aber nur das Token ohne den zusätzlichen Aufwand für das Analysieren der Ansprüche. Daher haben wir im Gerüst eine Methode ohne Ansprüche bereitgestellt, die den Namen `getTokenWithPolicyClearingCache` hat.

Schreiben Sie jetzt diesen neuen Code:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

Der erste Teil sollte Ihnen bekannt vorkommen.

- Laden Sie die Einstellungen, die in `settings.plist` bereitgestellt wurden, und weisen sie `data` zu.
- Richten Sie `ADAuthenticationError` ein. Dies nimmt alle Fehler an, die von ADAL für iOS stammen.
- Erstellen Sie `authContext`. Dies erstellt Ihren Aufruf für ADAL. Sie übergeben diesem Element Ihre Autorisierung, um zu beginnen.
- Versehen Sie `authContext` mit einem Verweis auf den übergeordneten Controller, damit Sie dorthin zurückkehren können.
- Konvertieren Sie `redirectURI`, wobei es sich um eine Zeichenfolge in `settings.plist` handelt, in den von ADAL erwarteten NSURL-Typ.
- Richten Sie `correlationId` ein. Dabei handelt es sich um eine UUID, die den Aufruf über den Client zum Server und zurück verfolgen kann. Dies ist hilfreich für das Debuggen.

Als Nächstes gelangen Sie zu dem eigentlichen Aufruf für ADAL. Hierbei verändert sich der Aufruf im Vergleich dazu, was Sie von der vorherigen Verwendung von ADAL für iOS erwarten:

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

Wie Sie sehen, ist der Aufruf relativ einfach aufgebaut.

`scopes`: Die Bereiche, die Sie dem Server übergeben und die Sie vom Server für den anzumeldenden Benutzer anfordern möchten. Übergeben Sie für die B2C-Vorschau `client_id`. Zukünftig wird sich dies für das Lesen von Bereichen jedoch ändern. Dieses Dokument wird dann aktualisiert. `additionalScopes`: Dies sind zusätzliche Bereiche, die Sie unter Umständen für Ihre Anwendung verwenden. Diese werden in Zukunft voraussichtlich verwendet. `clientId`: Die Anwendungs-ID, die Sie vom Portal abgerufen haben. `redirectURI`: Die Umleitung, die für das Postback des Tokens erwartet wird. `identifier`: Eine Möglichkeit zum Identifizieren eines Benutzers, um zu ermitteln, ob im Cache ein verwendbares Token enthalten ist. Dadurch wird verhindert, das den Server immer ein anderes Token anfordert. Hierfür wird ein Typ mit dem Namen `ADUserIdentifier` genutzt, und Sie können angeben, was als ID verwendet werden soll. Verwenden Sie `username`. `promptBehavior`: Dies ist veraltet. Verwenden Sie `AD_PROMPT_ALWAYS`. `extraQueryParameters`: Alle anderen Daten, die Sie im URL-codierten Format an den Server übergeben möchten. `policy`: Die aufzurufende Richtlinie. Dies ist der wichtigste Teil dieser exemplarischen Vorgehensweise.

Sie sehen in `completionBlock`, dass Sie `ADAuthenticationResult` übergeben. Darin sind Ihr Token und Ihre Profilinformationen enthalten (wenn der Aufruf erfolgreich war).

Mit dem obigen Code können Sie ein Token für die von Ihnen bereitgestellte Richtlinie erhalten. Verwenden Sie dieses Token zum Aufrufen der API.

### Erstellen Ihrer Aufgabenaufrufe an den Server

Da Sie jetzt über ein Token verfügen, müssen Sie es zur Autorisierung für die API bereitstellen.

Drei Methoden müssen implementiert werden:

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

Mit `getTasksList` wird ein Array bereitgestellt, das die Aufgaben auf unserem Server darstellt. Mit `addTask` und `deleteTask` werden die nachfolgenden Aktionen durchgeführt, und bei erfolgreicher Ausführung wird `true` oder `false` zurückgegeben.

Schreiben Sie zuerst `getTaskList`:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

Informationen zum Aufgabencode geht über dem Umfang dieser exemplarischen Vorgehensweise hinaus. Aber es gibt einen interessanten Aspekt, den Sie vielleicht bemerkt haben: eine `craftRequest`-Methode, die Ihre Aufgaben-URL verwendet. Diese Methode verwenden Sie zum Erstellen der Anforderung mit dem empfangenen Zugriffstoken für den Server. Schreiben Sie dies als Nächstes.

Fügen Sie der `samplesWebAPIConnector.m`-Datei folgenden Code hinzu:

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Dabei wird ein Web-URI (Uniform Resource Identifier) verwendet, dem das Token mit dem Header `Bearer` in HTTP hinzugefügt wird. Anschließend erfolgt die Rückgabe. Rufen Sie die `getTokenClearingCache`-API auf. Vielleicht erscheint dies auf den ersten Blick merkwürdig, Sie verwenden diesen Aufruf jedoch, um ein Token aus dem Cache abzurufen und sicherzustellen, dass es noch gültig ist. (Die Aufrufe `getToken` erledigen dies per Abfrage an ADAL). Sie verwenden diesen Code bei jedem Aufruf. Erstellen Sie als Nächstes zusätzliche Aufgabenmethoden.

Schreiben Sie `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Dies folgt dem gleichen Muster, führt aber auch die letzte Methode ein, die Sie implementieren müssen: `convertTaskToDictionary`. Hierbei wird Ihr Array in ein Wörterbuchobjekt verwandelt. Dieses Objekt kann einfacher in die Abfrageparameter mutiert werden, die an den Server übergeben werden. Der Code ist einfach:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Schreiben Sie als Nächstes `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Hinzufügen der Abmeldung zur Anwendung

Implementieren Sie als Letztes die Abmeldung für die Anwendung. Dies ist einfach. In der `sampleWebApiConnector.m`-Datei:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## Ausführen der Beispiel-App

Der letzte Schritt besteht im Erstellen und Ausführen der App in Xcode. Registrieren Sie sich bei der App, oder melden Sie sich bei der App an, und erstellen Sie Aufgaben für einen angemeldeten Benutzer. Melden Sie sich ab, melden Sie sich als ein anderer Benutzer wieder an, und erstellen Sie für diesen Benutzer Aufgaben.

Beachten Sie, wie die Aufgaben pro Benutzer in der API gespeichert werden, da die API die Benutzeridentität aus dem empfangenen Zugriffstoken extrahiert.

Das vollständige Beispiel [wird als ZIP-Datei bereitgestellt](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). Sie können sie auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Nächste Schritte

Sie können nun mit den B2C-Themen für fortgeschrittenere Benutzer fortfahren. Versuchen Sie Folgendes:

[Rufen Sie eine Node.js-Web-API aus einer Node.js-Web-App auf.]()

[Passen Sie die UX für eine B2C-Anwendung an.]()

<!---HONumber=AcomDC_0302_2016-->