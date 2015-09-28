<properties
	pageTitle="Azure AD B2C-Vorschau | Microsoft Azure"
	description="Erstellen einer Webanwendung, die eine Web-API mithilfe von Azure AD B2C aufruft."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="dastrock"/>

# Azure AD B2C-Vorschau: Aufrufen einer Web-API von einer .NET-Web-App

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Mit Azure AD B2C können Sie Ihren Web-Apps und Web-APIs in wenigen Schritten leistungsstarke Self-Service-Features zur Identitätsverwaltung hinzufügen. In diesem Artikel wird gezeigt, wie Sie eine .NET MVC-Web-App für Aufgabenlisten erstellen, die eine .NET-Web-API mithilfe von OAuth 2.0-Bearertoken aufruft. Sowohl die Web-App als auch die Web-API verwenden Azure AD B2C zum Verwalten von Benutzeridentitäten und zum Authentifizieren von Benutzern.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Dieser Artikel behandelt nicht das Implementieren der Anmeldung, Registrierung und Profilverwaltung mit Azure AD B2C. Er konzentriert sich auf das Aufrufen von Web-APIs aufrufen, nachdem der Benutzer bereits authentifiziert wurde. Wenn nicht bereits erfolgt, sollten Sie mit dem [Lernprogramm "Erste Schritte mit .NET-Web-App"](active-directory-b2c-devquickstarts-web-dotnet.md) beginnen, um Informationen zu den Grundlagen von Azure AD B2C zu erhalten.

## 1\. Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. Wenn Sie noch nicht über ein Verzeichnis verfügen, [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

## 2\. Erstellen einer Anwendung

Nun müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Die Web-App und die Web-API werden in diesem Fall durch eine einzelne **Anwendungs-ID** dargestellt, da sie zusammen eine logische App bilden. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Ersetzen Sie

- Fügen Sie der Anwendung eine **Web-App/Web-API** hinzu.
- Geben Sie `https://localhost:44316/` als **Antwort-URL** ein – dies ist die Standard-URL für dieses Codebeispiel.
- Erstellen Sie einen **geheimen Schlüssel für Ihre Anwendung**, und notieren Sie ihn sich. Sie benötigen ihn später.
- Notieren Sie sich die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie ebenfalls in Kürze.

## 3\. Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [**Richtlinie**](active-directory-b2c-reference-policies.md) definiert. Diese Web-App enthält drei Benutzeroberflächen, für die Identitäten relevant sind: Registrierung, Anmeldung und Profilbearbeitung. Sie müssen eine Richtlinie für jeden Typ erstellen, wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Wenn Sie die drei Richtlinien erstellen, beachten Sie Folgendes:

- Wählen Sie den **Anzeigenamen** und einige andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche in den Richtlinien aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen in Kürze. 

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

Beachten Sie, dass in diesem Artikel nicht behandelt wird, wie die gerade erstellten Richtlinien verwendet werden. Weitere Informationen zur Funktionsweise von Richtlinien in Azure AD B2C finden Sie im [Tutorial "Erste Schritte mit .NET-Web-Apps"](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Herunterladen des Codes

Der Code für dieses Tutorial wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet) verwaltet. Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP herunterladen](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip) oder das Gerüst klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

Die fertige App ist ebenfalls [als ZIP verfügbar](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) und unter `complete` im gleichen Repository enthalten.

Nachdem Sie den Beispielcode heruntergeladen haben, öffnen Sie die `.sln`-Datei in Visual Studio, um zu beginnen. Wie Sie sehen, sind in der Projektmappe zwei Projekte enthalten: ein `TaskWebApp`-Projekt und ein `TaskService`-Projekt. `TaskWebApp` ist der WPF-Web-App-Front-End, mit dem der Benutzer interagiert. `TaskService` ist die Back-End-Web-API der App, in der die Aufgabenliste des Benutzers gespeichert wird.

## 5\. Konfigurieren des Aufgabendiensts

Wenn `TaskService` Anforderungen von `TaskWebApp` erhält, sucht er nach einem gültigen Zugriffstoken zum Authentifizieren der Anforderung. Zum Überprüfen der Zugriffstoken müssen Sie `TaskService` einige Informationen zur App bereitstellen. Öffnen Sie im `TaskService`-Projekt die Datei `web.config` aus dem Stammverzeichnis des Projekts, und ersetzen Sie die Werte im Abschnitt `<appSettings>`.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
  ```

This article will not cover the details of securing the `TaskService`.  If you want to learn how a web API securely authenticates requests using Azure AD B2C, check out our
[Web API Getting Started article](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Configure the task web app

In order for the `TaskWebApp` to communicate with Azure AD B2C, there are a few common parameters that you will need to provide.  In the `TaskWebApp` project, open up the
`web.config` file in the root of the project and replace the values in the `<appSettings>` section.  These values will be used throughout the web app.

```
<appSettings> <add key="webpages:Version" value="3.0.0.0" /> <add key="webpages:Enabled" value="false" /> <add key="ClientValidationEnabled" value="true" /> <add key="UnobtrusiveJavaScriptEnabled" value="true" /> <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" /> <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" /> <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" /> <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" /> <add key="ida:RedirectUri" value="https://localhost:44316/" /> <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" /> <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" /> <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" /> <add key="api:TaskServiceUrl" value="https://localhost:44332/" /> </appSettings> ```

Es gibt auch zwei `[PolicyAuthorize]`-Decorator-Elemente, in denen Sie den Namen der Anmelderichtlinie angeben müssen. Das `[PolicyAuthorize]`-Attribut wird verwendet, um eine bestimmte Richtlinie aufzurufen, wenn der Benutzer versucht, auf eine Seite in der App zuzugreifen, für die eine Authentifizierung erforderlich ist.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

## 7\. Abrufen von Zugriffstoken und Aufrufen der Aufgaben-API

In diesem Abschnitt wird veranschaulicht, wie Sie einen OAuth 2.0-Tokenaustausch in einer Web-App mit den Bibliotheken und Frameworks von Microsoft durchführen. Falls Sie mit **Autorisierungscodes** und **Zugriffstoken** nicht vertraut sein sollten, ist es ratsam, sich die [Referenz zum OpenID Connect-Protokoll](active-directory-b2c-reference-protocols.md) anzusehen.

#### Abrufen eines Autorisierungscodes

Der erste Schritt beim Aufrufen der `TaskService`-Web-API ist das Authentifizieren des Benutzers und das Empfangen eines **Autorisierungscodes** von Azure AD. Sie können einen Autorisierungscode von Azure AD erhalten, nachdem alle Richtlinien erfolgreich ausgeführt wurden, einschließlich der Richtlinien zur Registrierung, Anmeldung und Profilbearbeitung.

Installieren Sie zuerst die OWIN OpenID Connect-Middleware mithilfe der Visual Studio-Paket-Manager-Konsole. Wir verwenden OWIN, um die Authentifizierungsanforderung an Azure AD zu senden und die Antworten zu verarbeiten:

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

Öffnen Sie die Datei `App_Start\Startup.Auth.cs`. Hier konfigurieren wir die OWIN-Authentifizierungspipeline und geben Details zu Ihrem B2C-Verzeichnis und zur Anwendung an, die Sie erstellt haben:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
	public const string PolicyKey = "b2cpolicy";
	public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

	// App config settings
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

	// B2C policy identifiers
	public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
	public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
	public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

	public void ConfigureAuth(IAppBuilder app)
	{
		app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

		app.UseCookieAuthentication(new CookieAuthenticationOptions());

		OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
		{
			// These are standard OpenID Connect parameters, with values pulled from web.config
			ClientId = clientId,
			RedirectUri = redirectUri,
			PostLogoutRedirectUri = redirectUri,
			Notifications = new OpenIdConnectAuthenticationNotifications
			{
				AuthenticationFailed = OnAuthenticationFailed,
				RedirectToIdentityProvider = OnRedirectToIdentityProvider,
				AuthorizationCodeReceived = OnAuthorizationCodeReceived,
			},
			Scope = "openid offline_access",

			// The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
			// endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
			ConfigurationManager = new PolicyConfigurationManager(
				String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
				new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

			// This piece is optional - it is used for displaying the user's name in the navigation bar.
			TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
			{
				NameClaimType = "name",
			},
		};

		app.UseOpenIdConnectAuthentication(options);
	}
	...
}
```

#### Abrufen eines Zugriffstokens mithilfe des Autorisierungscodes

Die Web-App ist jetzt zum Authentifizieren des Benutzers mit Ihrem B2C-Verzeichnis und zum Empfangen eines Autorisierungscodes von Azure AD konfiguriert. Der nächste Schritt besteht darin, diesen Autorisierungscode durch ein Zugriffstoken von Azure AD auszutauschen.

Immer wenn Ihre .NET-Web-Apps Zugriffstoken von Azure AD abrufen müssen, können Sie die **Active Directory-Authentifizierungsbibliothek (ADAL)** verwenden. Sie müssen ADAL für diesen Prozess nicht verwenden, aber ADAL erleichtert Ihnen die Arbeit durch die Übernahme vieler Details, wie z. B. Senden von OAuth 2.0-Authentifizierungsmeldungen, Zwischenspeichern und Aktualisieren von Token.

Installieren Sie zunächst wieder mit der Paket-Manager-Konsole ADAL im `TaskWebApp`-Projekt:

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

Nun müssen Sie den Autorisierungscode an ADAL übergeben, damit das Token für Sie abgerufen werden kann. Die OWIN OpenID Connect-Middleware stellt Ihnen eine Benachrichtigung zur Verwendung dieses Autorisierungscodes bereit. Die Benachrichtigung wird jedes Mal ausgelöst, wenn Ihre App einen Autorisierungscode von Azure AD erhält. Implementieren Sie in `App_Start\Startup.Auth.cs` den Benachrichtigungshandler `OnAuthorizationCodeReceived` mithilfe von ADAL:

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default.  In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache, for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
``` 

#### Abrufen eines Zugriffstoken in den Controllern

Jetzt verfügen wir über ein Zugriffstoken für den `TaskService`-Back-End, und es ist im Tokencache von ADAL gespeichert. Nun müssen wir es auch verwenden. `TasksController` ist verantwortlich für die Kommunikation mit der `TaskService`-API und sendet HTTP-Anforderungen an die API, um Aufgaben zu lesen, zu erstellen und zu löschen. Rufen Sie vor dem Senden einer HTTP-Anforderung ein Token von ADAL ab:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	AuthenticationResult result = null;
	try
	{
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

		// We don't care which policy is used to access the TaskService, so let's use the most recent policy
		string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;
		
		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache, and throw an exception if it cannot do so.
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

		...
	}
	catch (AdalException ee)
	{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
	}
	...
}
``` 

ADAL übernimmt das Zwischenspeichern von Token, aktualisiert sie, wenn sie ablaufen, und informiert Sie, wenn sich der Benutzer erneut anmelden muss, indem Ausnahmen ausgelöst werden. Sie müssen lediglich `AuthenticationContext.AcquireTokenSilentAsync(...)` jedes Mal aufrufen, wenn Sie in Ihrer App ein Token benötigen.

#### Lesen von Aufgaben aus der Web-API

Da Sie jetzt ein Token haben, können Sie es mit der HTTP-GET-Anforderung im `Authorization`-Header verknüpfen, um `TaskService` sicher aufzurufen:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	... 
	
	try 
	{
		HttpClient client = new HttpClient();
		HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

		// Add the token acquired from ADAL to the request headers
		request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
		HttpResponseMessage response = await client.SendAsync(request);

		if (response.IsSuccessStatusCode)
		{
			String responseString = await response.Content.ReadAsStringAsync();
			JArray tasks = JArray.Parse(responseString);
			ViewBag.Tasks = tasks;
			return View();
		}
		else
		{
			// If the call failed with access denied, then drop the current access token from the cache, 
			// and show the user an error indicating they might need to sign-in again.
			if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
			{
				var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
				foreach (TokenCacheItem tci in todoTokens)
					authContext.TokenCache.DeleteItem(tci);

				return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
			}
		}

		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
	}
	catch (Exception ex)
	{
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
	}
}

```

#### Erstellen und Löschen von Aufgaben mit der Web-API

Sie können genau dem gleichen Muster beim Senden von POST- und DELETE-Anforderungen an `TaskService` folgen. Rufen Sie einfach `AuthenticationContext.AcquireTokenSilentAsync(...)` auf, und fügen Sie das resultierende Token der Anforderung im `Authorization`-Header hinzu. Wir haben die `Create`-Aktion für Sie implementiert. Versuchen Sie, die `Delete`-Aktion selbst in `TasksController.cs` zu beenden.

## 8\. Abmelden des Benutzers

Ein letztes Detail. Wenn sich der Benutzer von der Web-App abmeldet, sollten Sie den ADAL-Tokencache löschen, um alle Reste der authentifizierten Sitzung des Benutzers zu entfernen:

```C#
// Controllers/AccountController.cs

public void SignOut()
{
	if (Request.IsAuthenticated)
	{
		// When the user signs out, clear their token cache in the process
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		authContext.TokenCache.Clear();

		HttpContext.GetOwinContext().Authentication.SignOut(
		new AuthenticationProperties(
			new Dictionary<string, string> 
			{ 
				{Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
			}), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
	}
}
```

## 9\. Ausführen der Beispiel-App

Erstellen Sie abschließend `TaskClient` und `TaskService`, und führen Sie beide aus. Registrieren Sie sich bei der App, oder melden Sie sich bei der App an, und erstellen Sie Aufgaben für den angemeldeten Benutzer. Melden Sie sich ab, und melden Sie sich als ein anderer Benutzer wieder an, und erstellen Sie für diesen Benutzer Aufgaben. Beachten Sie, wie die Aufgaben pro Benutzer in der API gespeichert werden, da die API die Benutzeridentität aus dem empfangenen Zugriffstoken extrahiert.

Als Referenz stellen wir das vollständige Beispiel [hier als ZIP-Datei bereit](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Sie können es auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO3-->