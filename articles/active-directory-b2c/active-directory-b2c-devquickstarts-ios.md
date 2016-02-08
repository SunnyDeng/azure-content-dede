<properties pageTitle="Azure AD B2C Preview: Aufrufen von Web-APIs aus einer iOS-Anwendung | Microsoft Azure" description="In diesem Artikel wird beschrieben, wie Sie eine iOS-App für Aufgabenlisten erstellen, die eine node.js-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die iOS-App als auch die Web-API verwenden Azure AD B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="brandwe"/>

# Azure AD B2C Preview: Aufrufen von Web-APIs aus einer iOS-Anwendung

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Mit Azure AD B2C können Sie Ihren iOS-Apps und Web-APIs in wenigen Schritten leistungsstarke Self-Service-Features zur Identitätsverwaltung hinzufügen. In diesem Artikel wird gezeigt, wie Sie eine iOS-App für Aufgabenlisten erstellen, die eine node.js-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die iOS-App als auch die Web-API verwenden Azure AD B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Für diesen Schnellstart benötigen Sie als Voraussetzung eine Web-API, die durch Azure AD mit B2C geschützt wird. Wir haben sowohl für .NET als auch für node.js eine für Sie vorbereitet. Bei dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass das node.js-Web-API-Beispiel konfiguriert wurde. Weitere Informationen dazu finden Sie unter [Azure Active Directory-Web-API-Beispiel für Node.js](active-directory-b2c-devquickstarts-api-node.md`).

> [AZURE.NOTE]
	Dieser Artikel behandelt nicht das Implementieren der Anmeldung, Registrierung und Profilverwaltung mit Azure AD B2C. Er konzentriert sich auf das Aufrufen von Web-APIs aufrufen, nachdem der Benutzer bereits authentifiziert wurde. Wenn nicht bereits erfolgt, sollten Sie mit dem [Lernprogramm „Erste Schritte mit .NET-Web-App“](active-directory-b2c-devquickstarts-web-dotnet.md) beginnen, um Informationen zu den Grundlagen von Azure AD B2C zu erhalten.

## 1\. Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. Wenn Sie noch nicht über ein Verzeichnis verfügen,[ erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

## 2\. Erstellen einer Anwendung

Nun müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Die App und die Web-API werden in diesem Fall durch eine einzelne **Anwendungs-ID** dargestellt, da sie zusammen eine logische App bilden. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Ersetzen Sie

- Fügen Sie der Anwendung eine **Web-App/Web-API** hinzu.
- Geben Sie `http://localhost:3000/auth/openid/return` als **Antwort-URL** ein – dies ist die Standard-URL für dieses Codebeispiel.
- Erstellen Sie einen **geheimen Schlüssel für Ihre Anwendung**, und notieren Sie ihn sich. Sie benötigen ihn später.
- Notieren Sie die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie ebenfalls in Kürze.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [**Richtlinie**](active-directory-b2c-reference-policies.md) definiert. Diese Anwendung enthält drei Oberflächen für die Identität: Registrierung, Anmeldung und Facebook-Anmeldung. Sie müssen eine Richtlinie jedes Typs erstellen, wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Wenn Sie die drei Richtlinien erstellen, beachten Sie Folgendes:

- Wählen Sie den **Anzeigenamen** und einige andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche in jeder Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen in Kürze.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

Beachten Sie, dass in diesem Artikel nicht behandelt wird, wie die gerade erstellten Richtlinien verwendet werden. Weitere Informationen zur Funktionsweise von Richtlinien in Azure AD B2C finden Sie im [Tutorial "Erste Schritte mit .NET-Web-Apps"](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Herunterladen des Codes

Der Code für dieses Lernprogramm wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS) verwaltet. Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip) oder das Gerüst klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Zur Durchführung dieses Lernprogramms ist das Herunterladen des Gerüsts erforderlich.** Aufgrund der Komplexität der Implementierung einer voll funktionsfähigen Anwendung unter iOS enthält das **Gerüst** UX-Code, der ausgeführt wird, nachdem Sie das unten angegebene Lernprogramm ausgeführt haben. Mit dieser Maßnahme sollen Entwickler Zeit sparen. Der UX-Code ist für das Hinzufügen von B2C zu einer iOS-Anwendung nicht von Belang.

Die fertige App ist ebenfalls [als ZIP-Datei verfügbar](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) oder unter der Verzweigung `complete` im gleichen Repository enthalten.


Laden Sie nun das Podfile mithilfe von Cocoapods. Dadurch wird ein neuer XCode-Arbeitsbereich erstellt, den Sie nun laden. Wenn Sie nicht über Cocoapods verfügen, besuchen Sie die [Website zum Einrichten von Cocoapods](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\. Konfigurieren der iOS-Aufgabenanwendung

Damit die iOS-Aufgaben-App mit Azure AD B2C kommunizieren kann, müssen Sie einige allgemeine Parameter angeben. Öffnen Sie im Ordner `Microsoft Tasks` die Datei "`settings.plist`" aus dem Stammverzeichnis des Projekts, und ersetzen Sie die Werte im Abschnitt `<dict>`. Diese Werte werden in der gesamten App verwendet.

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

## 6\. Abrufen von Zugriffstoken und Aufrufen der Aufgaben-API

In diesem Abschnitt wird veranschaulicht, wie Sie einen OAuth 2.0-Tokenaustausch in einer Web-App mit den Bibliotheken und Frameworks von Microsoft durchführen. Falls Sie mit **Autorisierungscodes** und **Zugriffstoken** nicht vertraut sein sollten, ist es ratsam, sich die [Referenz zum OAuth 2.0-Protokoll](active-directory-b2c-reference-protocols.md) anzusehen.

#### Erstellen von Headerdateien mit den von uns verwendeten Methoden.

Wir benötigen Methoden, um ein Token mit unserer ausgewählten Richtlinie abzurufen und anschließend unseren Aufgabenserver aufzurufen. Diese richten wir jetzt ein.

Erstellen Sie eine Datei mit dem Namen `samplesWebAPIConnector.h` unter "/Microsoft Tasks" in Ihrem XCode-Projekt.

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

Sie werden sehen, dass dies einfache CRUD-Vorgänge unserer API sowie eine `doPolicy`-Methode umfasst, mit der Sie ein Token mit der gewünschten Richtlinie abrufen können.

Außerdem werden Sie feststellen, dass zwei andere Headerdateien definiert werden müssen, in denen unser Aufgabenelement und unsere Richtliniendaten enthalten sind. Lassen Sie uns diese jetzt erstellen:

Erstellen Sie eine Datei namens `samplesTaskItem.h` mit dem folgenden Code:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Erstellen Sie außerdem die Datei `samplesPolicyData.h` für unsere Richtliniendaten:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### Hinzufügen einer Implementierung für unsere Aufgaben- und Richtlinienelemente

Nachdem unsere Headerdateien jetzt vorhanden sind, müssen wir Code zum Speichern der Daten schreiben, die wir für unser Beispiel verwenden.

Erstellen Sie eine Datei namens `samplesPolicyData.m` mit dem folgenden Code:

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

#### Schreiben von Setupcode für den Aufruf an ADAL für iOS

Der kurze Code zum Speichern unserer Objekte für die Benutzeroberfläche ist fertig. Jetzt müssen wir den Code zum Zugreifen auf ADAL für iOS mit den Parametern schreiben, die wir in die Datei `settings.plist` einfügen, um ein Zugriffstoken für unseren Aufgabenserver zu erhalten. Da dies unübersichtlich werden kann, sollten Sie sich genau konzentrieren.

Die gesamte Arbeit wird in `samplesWebAPIConnector.m` erledigt.

Zuerst erstellen wir die `doPolicy()`-Implementierung, die wir in unserer Headerdatei `samplesWebAPIConnector.h` geschrieben haben:

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

Sie sehen, dass diese Methode ziemlich einfach ist. Als Eingabe werden das weiter oben erstellte `samplesPolicyData`-Objekt, das übergeordnete ViewController-Element und dann ein Rückruf verwendet. Der Rückruf ist interessant, weshalb wir Schritt für Schritt vorgehen.

1. Sie sehen, dass `completionBlock` über ADProfileInfo als Typ verfügt, der mit einem `userInfo`-Objekt zurückgegeben wird. ADProfileInfo ist der Typ, der alle Antworten vom Server enthält, vor allem Ansprüche.
2. Sie sehen, dass wir `readApplicationSettings` verwenden. Hiermit werden die Daten gelesen, die wir in `settings.plist` angegeben haben.
3. Schließlich ist noch eine ziemlich umfangreiche `getClaimsWithPolicyClearingCache`-Methode vorhanden. Dies ist der eigentliche Aufruf von ADAL für iOS, den wir schreiben müssen. Dies werden wir später erledigen.

Jetzt schreiben wir unsere umfangreiche `getClaimsWithPolicyClearingCache`-Methode. Aufgrund ihres Umfangs wird sie in einem separaten Abschnitt beschrieben.

#### Erstellen unseres Aufrufs von ADAL für iOS

Wenn Sie das Gerüst von GitHub heruntergeladen haben, werden Sie bemerken, dass zur Unterstützung der Beispielanwendung bereits einige dieser Gerüste verwendet werden. Sie basieren alle auf dem Muster `get(Claims|Token)With<verb>ClearningCache`. Bei Verwendung von Objetive C-Konventionen liest sich dies fast wie normales Englisch. Beispiel: „Get a Token with extra parameters I provide you and clear the cache“ (Token mit zusätzlichen von mir bereitgestellten Parametern abrufen und Cache löschen). Dies sieht wie folgt aus: `getTokenWithExtraParamsClearingCache()`. Ziemlich einfach.

Wir schreiben "get Claims and a token With the policy I provide you and don't clear the cache" (Ansprüche und ein Token mit der von mir bereitgestellten Richtlinie abrufen und Cache nicht löschen) bzw. `getClaimsWithPolicyClearingCache`. Wir erhalten von ADAL immer ein Token zurück. Daher ist es nicht erforderlich, in der Methode „Claims and token“ (Ansprüche und Token) anzugeben. In einigen Fällen benötigen Sie aber nur das Token ohne den zusätzlichen Aufwand für das Analysieren der Ansprüche. Daher haben wir im Gerüst eine Methode ohne Ansprüche bereitgestellt, die den Namen `getTokenWithPolicyClearingCache` hat.

Diesen Code schreiben wir jetzt:

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

Der erste Teil sollte Ihnen bekannt vorkommen. Wir laden die Einstellungen, die in `Settings.plist` bereitgestellt wurden, und weisen sie `data` zu. Anschließend richten wir `ADAuthenticationError` ein, um alle Fehler zu erfassen, die von ADAL für iOS kommen. Außerdem erstellen wir ein `authContext`-Element, mit dem unser Aufruf von ADAL eingerichtet wird. Wir übergeben unsere *authority*, um zu beginnen. Außerdem versehen wir `authContext` mit einem Verweis auf unseren übergeordneten Controller, damit wir dorthin zurückkehren können. Wir konvertieren auch unseren `redirectURI`, wobei es sich um eine Zeichenfolge in unserer `settings.plist` handelt, in den von ADAL erwarteten NSURL-Typ. Zuletzt richten wir eine `correlationId` ein, wobei es sich lediglich um eine UUID handelt, die den Aufruf über den Client zum Server und zurück verfolgen kann. Dies ist hilfreich für das Debuggen.

Nun kommen wir zum eigentlichen Aufruf von ADAL. Hierbei verändert sich der Aufruf gegenüber der Form, die Sie von der vorherigen Verwendung von ADAL für iOS erwarten:

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

Hier sehen Sie, dass der Aufruf relativ einfach aufgebaut ist.

**scopes**: Die Bereiche, die wir an den Server übergeben und die wir vom Server für den anzumeldenden Benutzer anfordern möchten. Für B2C Preview übergeben wir die „client\_id“. Zukünftig wird sich dies für das Lesen von Bereichen jedoch ändern. Dieses Dokument wird dann aktualisiert. **additionalScopes**: Hierbei handelt es sich um zusätzliche Bereiche, die Sie für Ihre Anwendung verwenden können. Wird zukünftig verwendet. **clientId**: Die Anwendungs-ID, die Sie vom Portal erhalten haben. **redirectURI**: Die Umleitung, die für das Postback des Tokens erwartet wird. **identifier**: Dies ist eine Möglichkeit zum Identifizieren des Benutzers, um zu ermitteln, ob im Cache ein verwendbares Token enthalten ist (im Gegensatz zum ständigen Abfragen eines weiteren Tokens vom Server). Sie sehen, dass hierfür ein Typ mit dem Namen `ADUserIdentifier` genutzt wird und dass wir angeben können, was wir als ID verwenden möchten. Es ist ratsam, "username" zu verwenden. **promptBehavior**: Dies ist veraltet und sollte AD\_PROMPT\_ALWAYS lauten. **extraQueryParameters**: Zusätzliche Elemente, die Sie im URL-codierten Format an den Server übergeben möchten. **policy**: Die Richtlinie, die Sie aufrufen. Der wichtigste Teil dieser exemplarischen Vorgehensweise.

Sie sehen im completionBlock, dass wir das `ADAuthenticationResult`-Element übergeben, in dem unser Token und die Profilinformationen enthalten sind (falls der Aufruf erfolgreich war).

Mit dem obigen Code können Sie ein Token für die von Ihnen bereitgestellte Richtlinie erhalten. Wir verwenden dieses Token, um die API aufzurufen.

#### Erstellen unserer Aufgabenaufrufe an den Server

Da wir jetzt ein Token haben, müssen wir es zur Autorisierung für unsere API bereitstellen.

Vielleicht erinnern Sie sich, dass wir drei Methoden implementieren müssen:

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

Mit `getTasksList` wird ein Array bereitgestellt, das die Aufgaben auf unserem Server darstellt. Mit `addTask` und `deleteTask` wird die nachfolgende Aktion durchgeführt und bei erfolgreicher Ausführung TRUE oder FALSE zurückgegeben.

Zuerst schreiben wir unser `getTaskList`-Element:

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

Es würde den Rahmen dieser exemplarischen Vorgehensweise sprengen, den Aufgabencode zu erläutern. Aber es gibt einen interessanten Aspekt, den Sie vielleicht bemerkt haben: eine `craftRequest`-Methode, die unsere Aufgaben-URL verwendet. Diese Methode verwenden wir zum Erstellen der Anforderung mit dem empfangenen Zugriffstoken für den Server. Dies schreiben wir als Nächstes.

Wir fügen den folgenden Code der Datei „samplesWebAPIConnector.m“ hinzu:

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

Wie Sie sehen, wird ein Web-URI verwendet, dem das Token wird mit dem Header `Bearer` in HTTP hinzugefügt wird. Anschließend erfolgt die Rückgabe an uns. Wir rufen die `getTokenClearingCache`-API auf, was auf den ersten Blick vielleicht sonderbar erscheint. Wir verwenden diesen Aufruf aber einfach, um ein Token aus dem Cache abzurufen und sicherzustellen, dass es noch gültig ist (getToken*-Aufrufe erledigen dies für uns per Abfrage an ADAL). Wir verwenden diesen Code bei jedem Aufruf. Nun kommen wir zurück zur Erstellung unserer zusätzlichen Task-Methoden.

Wir schreiben `addTask`:

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

Hierbei wird das gleiche Muster verwendet, aber es wird eine weitere (endgültige!) Methode eingeführt, die wir implementieren müssen: `convertTaskToDictionary`. Hiermit wird unser Array in ein Wörterbuchobjekt verwandelt, das leichter in die Abfrageparameter mutiert werden kann, die wir an den Server übergeben müssen. Dieser Code ist sehr einfach:

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Schließlich schreiben wir `deleteTask`:

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

Als Letztes müssen wir die Abmeldung für unsere Anwendung implementieren. Dies ist recht einfach. Wir befinden uns wieder in unserer Datei `sampleWebApiConnector.m`:

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

## 9\. Ausführen der Beispiel-App

Abschließend erstellen Sie die App in xCode und führen sie aus. Registrieren Sie sich bei der App, oder melden Sie sich bei der App an, und erstellen Sie Aufgaben für den angemeldeten Benutzer. Melden Sie sich ab, melden Sie sich als ein anderer Benutzer wieder an, und erstellen Sie für diesen Benutzer Aufgaben.

Beachten Sie, wie die Aufgaben pro Benutzer in der API gespeichert werden, da die API die Benutzeridentität aus dem empfangenen Zugriffstoken extrahiert.

Als Referenz stellen wir das [vollständige Beispiel hier als ZIP-Datei](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) bereit. Sie können es auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Nächste Schritte

Sie können nun mit den B2C-Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

[Aufrufen einer node.js-Web-API aus einer node.js-Web-App >>]()

[Anpassen der Benutzeroberfläche einer B2C-App >>]()

<!---HONumber=AcomDC_0128_2016-->