<properties
   pageTitle="Schützen einer Back-End-Web-API in einer mehrmandantenfähigen Anwendung | Microsoft Azure"
   description="Schützen einer Back-End-Web-API"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Schützen einer Back-End-Web-API in einer mehrmandantenfähigen Anwendung

Dieser Artikel ist [Teil einer Reihe]. Es gibt auch eine vollständige [Beispielanwendung], die diese Reihe begleitet.

Die [Tailspin-Anwendung „Surveys“] verwendet eine Back-End-Web-API, um Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen für Umfragen zu verwalten. Wenn z. B. ein Benutzer auf „My Surveys“ klickt, sendet die Webanwendung eine HTTP-Anforderung an die Web-API:

```
GET /users/{userId}/surveys
```

Die Web-API gibt ein JSON-Objekt zurück:

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

Die Web-API lässt keine anonymen Anforderungen zu, sodass sich die Web-App mithilfe von OAuth2-Bearertoken selbst authentifizieren muss.

> [AZURE.NOTE] Dies ist ein Szenario zwischen Servern. Die Anwendung richtet aus dem Browserclient keine AJAX-Aufrufe an die API.

Sie können zwischen zwei Ansätzen wählen:

- Delegierte Benutzeridentität. Die Webanwendung authentifiziert sich mit der Identität des Benutzers.
- Anwendungsidentität. Die Webanwendung authentifiziert sich mit Ihrer Client-ID bei Befolgen des OAuth2-Ablaufs für Clientanmeldeinformationen.

Die Tailspin-Anwendung implementiert die delegierte Benutzeridentität. Im Folgenden werden die Hauptunterschiede erläutert:

**Delegierte Benutzeridentität**

- Das an die Web-API gesendete Bearertoken enthält die Identität des Benutzers.
- Die Web-API trifft Autorisierungsentscheidungen basierend auf der Identität des Benutzers.
- Die Webanwendung muss von der Web-API eingehende Fehler des Typs „403 (Verboten)“ behandeln, wenn der Benutzer für das Ausführen einer Aktion nicht autorisiert ist.
- Zumeist trifft die Webanwendung weiterhin einige sich auf die Benutzeroberfläche beziehende Autorisierungsentscheidungen (wie das Ein- und Ausblenden von Benutzeroberflächenelementen).
- Bei diesem Ansatz kann die Web-API potenziell von nicht vertrauenswürdigen Clients verwendet werden, z. B. einer JavaScript-Anwendung oder nativen Clientanwendung.

**Anwendungsidentität**

- Die Web-API ruft keine Informationen zum Benutzer ab.
- Die Web-API kann keine Autorisierung anhand der Identität des Benutzers vornehmen. Alle Autorisierungsentscheidungen werden von der Webanwendung getroffen.  
- Die Web-API kann nicht von einem nicht vertrauenswürdigen Client (JavaScript oder native Clientanwendung) verwendet werden.
- Dieser Ansatz ist möglicherweise etwas einfacher zu implementieren, da keine Autorisierungslogik in der Web-API vorhanden ist.

Bei beiden Ansätzen muss die Webanwendung ein Zugriffstoken als Anmeldeinformation erhalten, die für das Aufrufen der Web-API erforderlich ist.

- Für die delegierte Benutzeridentität muss das Token vom Identitätsanbieter stammen, der ein Token für den Benutzer ausstellen kann.

- Für Clientanmeldeinformationen kann eine Anwendung das Token vom Identitätsanbieter beziehen oder einen eigenen Tokenserver hosten. (Schreiben Sie jedoch keinen Tokenserver von Grund auf neu, sondern verwenden Sie ein getestetes Framework wie [IdentityServer3].) Wenn die Authentifizierung mithilfe von Azure AD erfolgt, wird unbedingt empfohlen, das Zugriffstoken aus Azure AD abzurufen, selbst beim Vorgang mit Clientanmeldeinformationen.

Im Rest dieses Artikels wird davon ausgegangen, dass die Anwendung mithilfe von Azure AD authentifiziert wird.

![Abrufen des Zugriffstokens](media/guidance-multitenant-identity/access-token.png)

## Registrieren der Web-API in Azure AD

Damit Azure AD ein Bearertoken für die Web-API ausstellen kann, müssen in Azure AD verschiedene Einstellungen konfiguriert werden.

1. [Registrieren Sie die Web-API in Azure AD].

2. Fügen Sie dem Anwendungsmanifest der Web-API in der `knownClientApplications`-Eigenschaft die Client-ID der Web-App hinzu. Siehe [Aktualisieren des Anwendungsmanifests].

3. [Erteilen Sie der Webanwendung Berechtigungen zum Aufrufen der Web-API].

  Im Azure-Verwaltungsportal können Sie zwei Arten von Berechtigungen festlegen: „Anwendungsberechtigungen“ für die Anwendungsidentität (Vorgang mit Clientanmeldeinformationen) oder „Delegierte Berechtigungen“ für die delegierte Benutzeridentität.

  ![Delegierte Berechtigungen](media/guidance-multitenant-identity/delegated-permissions.png)

## Abrufen eines Zugriffstokens

Vor dem Aufruf der Web-API ruft die Webanwendung ein Zugriffstoken aus Azure AD ab. Verwenden Sie in einer .NET-Anwendung die [Azure AD Authentication Library (ADAL) für .NET][ADAL].

Beim Vorgang mit OAuth 2-Autorisierungscode tauscht die Anwendung einen Autorisierungscode gegen ein Zugriffstoken. Der folgende Code verwendet die ADAL, um das Zugriffstoken abzurufen. Dieser Code wird während des `AuthorizationCodeReceived`-Ereignisses abgerufen.

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

Hier die verschiedenen erforderlichen Parameter:

- `authority`. Von der Mandanten-ID des angemeldeten Benutzers abgeleitet. (Nicht die Mandanten-ID des SaaS-Anbieters)  
- `authorizationCode`. Der Autorisierungscode, den Sie vom Identitätsanbieter erhalten haben.
- `clientId`. Die Client-ID der Webanwendung.
- `clientSecret`. Der geheime Clientschlüssel der Webanwendung.
- `redirectUri`. Der Umleitung-URI, den Sie für OpenID Connect festgelegt haben. Hier folgt der Rückruf des Identitätsanbieters mit dem Token.
- `resourceID`. Die App-ID-URI der Web-API, die Sie bei der Registrierung der Web-API in Azure AD erstellt haben.
- `tokenCache`. Ein Objekt, das die Zugriffstoken zwischengespeichert. Siehe [Tokencaching].

Wenn `AcquireTokenByAuthorizationCodeAsync` erfolgreich ist, speichert die ADAL das Token zwischen. Später können Sie das Token durch Aufrufen von „AcquireTokenSilentAsync“ aus dem Cache abrufen:

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

`userId` ist die Objekt-ID des Benutzers, die sich im `http://schemas.microsoft.com/identity/claims/objectidentifier`-Anspruch befindet.

## Aufrufen der Web-API mithilfe des Zugriffstokens

Sobald Sie das Token haben, senden Sie es im „Authorization“-Header der HTTP-Anforderungen an die Web-API.

```
Authorization: Bearer xxxxxxxxxx
```

Die folgende Erweiterungsmethode aus der Anwendung „Surveys“ legt den „Authorization“-Header unter Verwendung der **HttpClient**-Klasse auf eine HTTP-Anforderung fest.

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] Siehe [HttpClientExtensions.cs].

## Authentifizierung in der Web-API

Die Web-API muss das Bearertoken authentifizieren. In ASP.NET Core 1.0 können Sie das [Microsoft.AspNet.Authentication.JwtBearer][JwtBearer]-Paket verwenden. Dieses Paket enthält Middleware, die der Anwendung das Empfangen von OpenID Connect-Bearertoken ermöglicht.

Registrieren Sie die Middleware in Ihrer Web-API-Klasse `Startup`.

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] Siehe [Startup.cs].

- **Audience**. Legen Sie diese Einstellung auf die App-ID-URI der Web-API fest, die Sie bei der Registrierung der Web-API in Azure AD erstellt haben.
- **Authority**. Legen Sie bei einer mehrmandantenfähigen Anwendung diesen Eintrag auf https://login.microsoftonline.com/common/`` fest.
- **TokenValidationParameters**. Legen Sie bei einer mehrmandantenfähigen Anwendung **ValidateIssuer** auf „false“ fest. Dies bedeutet, dass die Anwendung den Aussteller überprüft.
- **Events** ist eine Klasse, die von **JwtBearerEvents** abgeleitet ist.

### Überprüfung des Ausstellers

Überprüfen Sie den Aussteller des Tokens im **JwtBearerEvents.ValidatedToken**-Ereignis. Der Aussteller wird im Anspruch „iss“ gesendet.

In der Anwendung „Surveys“ wird die [Mandantenanmeldung] nicht von der Web-API verarbeitet. Daher wird nur überprüft, ob der Aussteller bereits in der Anwendungsdatenbank vorhanden ist. Falls nicht, wird eine Ausnahme ausgelöst, die einen Authentifizierungsfehler verursacht.

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] Siehe [SurveysJwtBearerEvents.cs].

Sie können auch das **ValidatedToken**-Ereignis zur [Transformation von Ansprüchen] verwenden. Beachten Sie, dass die Ansprüche direkt aus Azure AD stammen. Wenn also die Webanwendung Transformationen von Ansprüchen vorgenommen hat, sind diese nicht im Bearertoken enthalten, das die Web-API empfängt.

## Autorisierung

Eine allgemeine Erörterung der Autorisierung finden Sie unter [Autorisierung]. Bei einer Web-API besteht der Hauptunterschied darin, dass die JwtBearer-Middleware die Autorisierungsantworten verarbeitet.

So beschränken Sie beispielsweise eine Controlleraktion auf authentifizierte Benutzer:

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```


Dies gibt den Statuscode 401 zurück, wenn der Benutzer nicht authentifiziert ist.

So beschränken Sie eine Controlleraktion mithilfe einer Autorisierungsrichtlinie:

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

Dies gibt den Statuscode 401 zurück, wenn der Benutzer nicht authentifiziert ist. Der Statuscode 403 wird zurückgegeben, wenn der Benutzer zwar authentifiziert, jedoch nicht autorisiert ist. Registrieren Sie die Richtlinie beim Start:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```


## Zusätzliche Ressourcen

- [Authentifizierungsszenarien für Azure AD][auth-scenarios]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[auth-scenarios]: ../active-directory/active-directory-authentication-scenarios.md/#web-application-to-web-api
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[Teil einer Reihe]: guidance-multitenant-identity.md
[Tailspin-Anwendung „Surveys“]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Registrieren Sie die Web-API in Azure AD]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[Aktualisieren des Anwendungsmanifests]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Erteilen Sie der Webanwendung Berechtigungen zum Aufrufen der Web-API]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[Tokencaching]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[Mandantenanmeldung]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[Transformation von Ansprüchen]: guidance-multitenant-identity-claims.md#claims-transformations
[Autorisierung]: guidance-multitenant-identity-authorize.md
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->