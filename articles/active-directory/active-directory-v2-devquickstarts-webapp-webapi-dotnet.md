<properties
	pageTitle="App-Modell v2.0: .NET-Web-App | Microsoft Azure"
	description="Vorgehensweise beim Erstellen einer .NET-MVC-Web-App, die Webdienste mit persönlichen Microsoft-Konten oder Geschäfts- oder Schulkonten für die Anmeldung aufruft."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 Vorschau: Aufrufen einer Web-API von einer .NET-Web-App

> [AZURE.NOTE]
	Diese Informationen gelten für Endpunkt v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

Mit dem App-Modell v2.0 können Sie schnell eine Authentifizierung zu Ihren Web-Apps und Web-APIs hinzufügen, die sowohl persönliche Microsoft-Konten als auch Geschäfts- oder Schulkonten unterstützen. Wir erstellen hier eine MVC-Web-App, die:

- Benutzer unterstützt durch OWIN-Middleware von Microsoft mit OpenID Connect anmeldet.
- OAuth 2.0-Zugriffstoken für eine Web-API mit ADAL abruft.
- Elemente in der "Aufgabenliste" (To-Do List) eines Benutzers erstellt, liest und löscht , die auf der Web-API gehostet und durch OAuth 2.0 gesichert wird.

Dieses Lernprogramm konzentriert sich hauptsächlich auf das Abrufen und Verwenden von Zugriffstoken in einer Web-App. Eine vollständige Beschreibung finden Sie [hier](active-directory-v2-flows.md#web-apps). Als Vorbedingung möchten Sie vielleicht zuerst erfahren, wie [ein grundlegendes Anmelden in eine Web-App hingefügt wird](active-directory-v2-devquickstarts-dotnet-web.md) oder wie [eine Web-API ausreichend geschützt wird](active-directory-v2-devquickstarts-dotnet-api.md).

Die grundlegenden Schritte zum Aufrufen der To-Do List-Web-API vom Client lauten wie folgt:

1. Registrieren einer App
2. Anmelden eines Benutzers in der Web-App mithilfe von OpenID Connect
3. Verwenden von ADAL zum Aufrufen eines Zugriffstokens bei Benutzeranmeldung
4. Aufrufen der To-Do List-Web-API mit einem Zugriffstoken.

Der Code für dieses Lernprogramm wird [auf GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet) verwaltet.

Um folgen zu können, können Sie [das App-Gerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) oder das Gerüst klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Alternativ können Sie [die fertige App als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) oder die fertige App klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## 1. Registrieren einer App
Erstellen Sie eine neue App unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com), oder führen Sie die folgenden [ausführlichen Schritte](active-directory-v2-app-registration.md) aus. Stellen Sie sicher, dass Sie:

- die Ihrer App zugewiesene **Anwendungs-ID** kopieren. Sie benötigen Sie in Kürze.
- einen **geheimen App-Schlüssel** des Typs **Kennwort** erstellen, und den Wert für später kopieren.
- die **Web**-Plattform für Ihre App hinzufügen.
- den richtigen **Umleitungs-URI** eingeben. Der Umleitungs-URI verweist auf Azure AD, wohin Authentifizierungsantworten gesendet werden sollen – der Standardwert in diesem Lernprogramm lautet `https://localhost:44326/`.


## 2. Melden Sie den Benutzer mit OpenID Connect an
Hier konfigurieren wir die OWIN-Middleware für die Verwendung des [Authentifizierungsprotokolls OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). OWIN wird unter anderem für die Ausgabe von Anmelde- und Abmeldeanforderungen, für die Verwaltung der Benutzersitzungen und für das Abrufen der Benutzerinformationen verwendet.

-	Öffnen Sie zunächst die Datei `web.config` aus dem Stammverzeichnis des `TodoList-WebApp`-Projekts, und geben Sie die Konfigurationswerte Ihrer App im Abschnitt `<appSettings>` ein.
    -	`ida:ClientId` ist die **Anwendungs-ID**, die Ihrer App im Registrierungsportal zugewiesen ist.
	- `ida:ClientSecret` ist der **geheime App-Schlüssel**, den Sie im Registrierungsportal erstellt haben.
    -	`ida:RedirectUri` ist der **Umleitungs-URI**, den Sie im Portal eingegeben haben.
- Öffnen Sie die Datei `web.config` im Stammverzeichnis des `TodoList-Service`-Projekts, und ersetzen Sie `ida:Audience` durch die gleiche **Anwendungs-ID** wie oben.


-	Fügen Sie dem `TodoList-WebApp`-Projekt nun über die Paket-Manager-Konsole die NuGet-Pakete der OWIN-Middleware hinzu.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

-	Öffnen Sie die Datei `App_Start\Startup.Auth.cs`und fügen Sie `using`-Anweisungen für die oben genannten Bibliotheken hinzu.
- Implementieren Sie in der gleichen Datei die `ConfigureAuth(...)`-Methode. Die Parameter, die Sie in `OpenIDConnectAuthenticationOptions` bereitstellen, dienen als Koordinaten für Ihre Anwendung zur Kommunikation mit Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

					// The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
					// The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
					// In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

					ClientId = clientId,
					Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
					Scope = "openid email profile offline_access",
					RedirectUri = redirectUri,
					PostLogoutRedirectUri = redirectUri,
					TokenValidationParameters = new TokenValidationParameters
					{
						ValidateIssuer = false,
					},

					// The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

					Notifications = new OpenIdConnectAuthenticationNotifications
					{
						AuthenticationFailed = OnAuthenticationFailed,
						AuthorizationCodeReceived = OnAuthorizationCodeReceived,
					}

    	});
}
...
```

## 3. Verwenden von ADAL zum Aufrufen eines Zugriffstokens bei Benutzeranmeldung
In der `AuthorizationCodeReceived`-Benachrichtigung möchten wir [OAuth 2.0 zusammen mit OpenID Connect](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) zum Einlösen des Autorisierungscodes für ein Zugriffstoken auf den To-Do List-Dienst verwenden. ADAL kann diesen Prozess erleichtern:

- Installieren Sie zunächst die Vorschauversion von ADAL:

```PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoList-WebApp -IncludePrerelease``` 
– Und fügen Sie eine weitere `using`-Anweisung zu der `App_Start\Startup.Auth.cs`-Datei für ADAL hinzu. 
– Fügen Sie nun eine neue Methode hinzu, den `OnAuthorizationCodeReceived`-Ereignishandler. Dieser Handler nutzt ADAL zum Abrufen eines Zugriffstokens auf die To-Do List-API und speichert das Token im Tokencache von ADAL für spätere Zwecke:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
		string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
		string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
		ClientCredential cred = new ClientCredential(clientId, clientSecret);

		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		var authContext = new Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(authority, new NaiveSessionCache(userObjectId));
		var authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), cred, new string[] { clientId });
}
...
```

- In Web-Apps verfügt ADAL über ein erweiterbares Tokencache, das zum Speichern von Token verwendet werden kann. In diesem Beispiel wird der `NaiveSessionCache` implementiert, der die HTTP-Sitzungsspeicher zum Zwischenspeichern von Token verwendet.

<!-- TODO: Token Cache article -->


## 4. Aufrufen der To-Do List-Web-API
Jetzt wird das in Schritt 3 abgerufene Zugriffstoken benötigt. Öffnen Sie die Datei `Controllers\TodoListController.cs` der Web-App, die alle CRUD-Anfragen an die To-Do List-API durchführt.

- Sie können hier wieder ADAL verwenden, um Zugriffstoken aus dem ADAL-Cache abzurufen. Fügen Sie dieser Datei zunächst eine `using`-Anweisung für ADAL hinzu.

    `using Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory;`

- Verwenden Sie in der `Index`-Aktivität die `AcquireTokenSilentAsync`-Methode von ADAL, um ein Zugriffstoken abzurufen, das zum Lesen von Daten aus dem To-Do List-Dienst verwendet werden kann:

```C#
...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser);
...
```

- Im Beispiel wird dann das resultierende Token auf die HTTP GET-Anforderung als `Authorization`-Header hinzugefügt, das der To-Do List-Dienst zum Authentifizieren der Anforderung nimmt.
- Wenn der To-Do List-Dienst eine `401 Unauthorized`-Antwort zurückgibt, sind die Zugriffstoken in ADAL aus irgendeinem Grund ungültig geworden. In diesem Fall sollten Sie alle Zugriffstoken aus dem ADAL-Cache löschen und dem Benutzer eine Meldung anzeigen, dass er sich möglicherweise erneut anmelden muss, wodurch der Fluss für die Tokenerfassung neu gestartet wird.

```C#
...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
		var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
		foreach (TokenCacheItem tci in todoTokens)
				authContext.TokenCache.DeleteItem(tci);

		return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
...
```

- Wenn ADAL aus irgendeinem Grund keine Zugriffstoken zurückgeben kann, sollten Sie den Benutzer anweisen, sich erneut anzumelden. Dies ist so einfach wie das Abfangen jeder `AdalException`:

```C#
...
catch (AdalException ee)
{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
...
```

- Der genaue identische `AcquireTokenSilentAsync`-Aufruf ist in den Aktionen `Create` und `Delete` implementiert. In Web-Apps können Sie diese ADAL-Methode nutzen, um Zugriffstoken immer dann abrufen zu können, wenn Sie sie in der App benötigen. ADAL übernimmt für Sie das Erfassen, Zwischenspeichern und Aktualisieren von Token.

Erstellen und führen Sie Ihre Anwendung zum Schluss aus! Melden Sie sich entweder mit einem Microsoft-Konto oder einem Azure AD-Konto an, und beachten Sie, wie die Identität des Benutzers in der oberen Navigationsleiste dargestellt wird. Fügen Sie einige Elemente zur Aufgabenliste (To Do-List) des Benutzers hinzu und löschen Sie sie, um durch die OAuth 2.0-API gesicherte Aufrufe in Aktion zu sehen. Sie verfügen jetzt über eine mit branchenüblichen Protokollen gesicherte Web-App und Web-API, die Benutzer beide mit ihren persönlichen Konten oder ihren Geschäfts- oder Schulkonten authentifizieren können.

Als Referenz stellen wir [hier](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) das vollständige Beispiel (ohne Ihre Konfigurationswerte) bereit.

## Nächste Schritte

Weitere Ressourcen: 
- [Die App-Modell v2.0-Vorschauversion \>\>](active-directory-appmodel-v2-overview.md)
- [StackOverflow-"adal"-Tag \>\>](http://stackoverflow.com/questions/tagged/adal)

<!---HONumber=AcomDC_0128_2016-->