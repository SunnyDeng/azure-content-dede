<properties
	pageTitle="Azure AD B2C Preview | Microsoft Azure"
	description="Erstellen einer .NET-Web-API mithilfe von Azure Active Directory B2C, die mit OAuth 2.0-Zugriffstoken für die Authentifizierung gesichert ist."
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
	ms.topic="hero-article"
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Azure Active Directory B2C-Vorschau: Erstellen einer .NET-Web-API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Mit Azure Active Directory (Azure AD) B2C können Sie eine Web-API mithilfe von OAuth 2.0-Zugriffstoken sichern. Mit diesen Token können Ihre Client-Apps, die Azure AD B2C verwenden, sich bei der API authentifizieren. In diesem Artikel erfahren Sie, wie eine .NET MVC-App (Model-View-Controller) für eine Aufgabenliste erstellt wird, die Benutzerregistrierung, -anmeldung und die Verwaltung von Profilen umfasst. Die Aufgabenlisten der einzelnen Benutzer werden von einem Aufgabendienst gespeichert. Dabei handelt es sich um eine Web-API, mit der authentifizierte Benutzer Aufgaben in ihrer Aufgabenliste erstellen und lesen können.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für all Ihre Benutzer, Apps, Gruppen usw. Wenn Sie noch keines verwenden, [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie die weiteren Schritte in diesem Leitfaden ausführen.

## Erstellen einer Anwendung

Als Nächstes müssen Sie in Ihrem B2C-Verzeichnis eine App erstellen. Dadurch werden Azure AD die Informationen bereitgestellt, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Führen Sie folgende Schritte aus:

- Fügen Sie der Anwendung eine **Web-App** oder **Web-API** hinzu.
- Verwenden Sie den **Umleitungs-URI** (Uniform Resource Identifier) `https://localhost:44316/` für die Web-App. Dies ist der Standardspeicherort des Web-App-Clients für dieses Codebeispiel.
- Kopieren Sie die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie später.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [Richtlinie](active-directory-b2c-reference-policies.md) definiert. Der Client in diesem Codebeispiel enthält drei Benutzeroberflächen, für die Identitäten relevant sind: Registrierung, Anmeldung und Profilbearbeitung. Sie müssen für jeden Typ eine Richtlinie erstellen, wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Beachten Sie beim Erstellen der drei Richtlinien Folgendes:

- Wählen Sie auf dem Blatt für den Identitätsanbieter **Registrierung mit Benutzer-ID** oder **E-Mail-Registrierung** aus.
- Wählen Sie den **Anzeigenamen** und andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche für jede Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie benötigen diese Richtliniennamen später.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

## Herunterladen des Codes

Der Code für dieses Tutorial wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet) verwaltet. Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Sie können das Gerüst auch klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

Die fertige App ist ebenfalls [als ZIP-Datei verfügbar](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) oder unter der Verzweigung `complete` im gleichen Repository enthalten.

Nachdem Sie den Beispielcode heruntergeladen haben, öffnen Sie zum Einstieg die Visual Studio-SLN-Datei. Die Projektmappendatei enthält zwei Projekte: `TaskWebApp` und `TaskService`. `TaskWebApp` ist eine MVC-Webanwendung, mit der der Benutzer interagiert. `TaskService` ist die Back-End-Web-API der App, in der die Aufgabenlisten der einzelnen Benutzer gespeichert werden.

## Konfigurieren der Aufgaben-Web-App

Wenn ein Benutzer mit `TaskWebApp` interagiert, sendet der Client Anforderungen an Azure AD und erhält dafür Token, die für den Aufruf der Web-API `TaskService` verwendet werden können. Zum Anmelden des Benutzers und zum Abrufen von Token benötigt `TaskWebApp` einige Informationen über Ihre App. Öffnen Sie im Projekt `TaskWebApp` die Datei `web.config` im Stammverzeichnis des Projekts, und ersetzen Sie die Werte im Abschnitt `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Es gibt auch zwei `[PolicyAuthorize]`-Decorator-Elemente, für die Sie den Namen der Anmelderichtlinie angeben müssen. Das `[PolicyAuthorize]`-Attribut wird verwendet, um eine bestimmte Richtlinie aufzurufen, wenn ein Benutzer versucht, auf eine Seite in der App zuzugreifen, für die eine Authentifizierung erforderlich ist.

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

Um zu erfahren, wie eine Web-App wie `TaskWebApp` Azure AD B2C verwendet, finden Sie unter [Erstellen einer .NET-Web-App](active-directory-b2c-devquickstarts-web-dotnet.md).

## Sichern der API

Wenn Sie über einen Client verfügen, der die API im Auftrag eines Benutzers aufruft, können Sie `TaskService` mit OAuth 2.0-Bearertoken sichern. Die API kann Token mit der OWIN-Bibliothek von Microsoft (Open Web Interface for .NET) akzeptieren und überprüfen.

### Installieren von OWIN
Installieren Sie zuerst die OWIN-OAuth-Authentifizierungspipeline:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### Eingeben der B2C-Details
Öffnen Sie die Datei `web.config` aus dem Stammverzeichnis des `TaskService`-Projekts, und ersetzen Sie die Werte im Abschnitt `<appSettings>`. Diese Werte werden in der API und der OWIN-Bibliothek verwendet.

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

### Hinzufügen einer OWIN-Startklasse
Fügen Sie dem `TaskService`-Projekt eine OWIN-Startklasse mit dem Namen `Startup.cs` hinzu. Klicken Sie mit der rechten Maustaste auf das Projekt, wählen Sie **Hinzufügen** und **Neues Element**, und suchen Sie dann nach „OWIN“.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### Konfigurieren der OAuth 2.0-Authentifizierung
Öffnen Sie die Datei `App_Start\Startup.Auth.cs`, und implementieren Sie die Methode `ConfigureAuth(...)`:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	// These values are pulled from web.config
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
	private const string discoverySuffix = ".well-known/openid-configuration";

	public void ConfigureAuth(IAppBuilder app)
	{   
		TokenValidationParameters tvps = new TokenValidationParameters
		{
			// This is where you specify that your API accepts tokens only from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

### Sichern des Aufgabencontrollers
Nachdem Sie die App für die OAuth 2.0-Authentifizierung konfiguriert haben, können Sie Ihre Web-API sichern, indem Sie dem Aufgabencontroller ein `[Authorize]`-Tag hinzufügen. Dies ist der Controller, in dem die gesamte Bearbeitung der Aufgabenliste durchgeführt wird. Daher wird der gesamte Controller auf Klassenebene gesichert. Zur präziseren Steuerung können Sie das `[Authorize]`-Tag auch einzelnen Aktionen hinzufügen.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

### Abrufen von Benutzerinformationen aus dem Token
`TasksController` speichert Aufgaben in einer Datenbank, wobei jeder Aufgabe ein Benutzer zugeordnet ist, der die Aufgabe „besitzt“. Der Besitzer wird durch die **Objekt-ID** des Benutzers identifiziert. (Daher mussten Sie die Objekt-ID als Anwendungsanspruch in all Ihren Richtlinien hinzufügen.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## Ausführen der Beispiel-App

Zum Schluss erstellen Sie `TaskWebApp` und `TaskService` und führen sie aus. Registrieren Sie sich für die App mit einer E-Mail-Adresse oder einem Benutzernamen. Erstellen Sie einige Aufgaben in der Aufgabenliste des Benutzers, und beachten Sie, wie sie in der API beibehalten werden, auch nachdem Sie den Client beendet und neu gestartet haben.

## Bearbeiten der Richtlinien

Nachdem Sie eine API mit Azure AD B2C gesichert haben, können Sie mit den Richtlinien Ihrer App experimentieren und die Auswirkungen (sofern vorhanden) auf die API beobachten. Sie können <!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->die Anwendungsansprüche in den Richtlinien bearbeiten und die Benutzerinformationen ändern, die in der Web-API verfügbar sind. Ansprüche, die Sie hinzufügen, stehen Ihrer .NET MVC-Web-API im `ClaimsPrincipal`-Objekt zur Verfügung, wie oben in diesem Artikel beschrieben.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0330_2016-->