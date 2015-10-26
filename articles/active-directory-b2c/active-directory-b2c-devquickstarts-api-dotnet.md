<properties
	pageTitle="Azure AD B2C-Vorschau | Microsoft Azure"
	description="Erstellen einer .NET-Web-API mithilfe von Azure AD B2C, die mit OAuth 2.0-Zugriffstoken für die Authentifizierung gesichert ist."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>
	
# Azure AD B2C-Vorschau: Erstellen einer .NET-Web-API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Mit Azure AD B2C können Sie eine Web-API mit OAuth 2.0-Zugriffstoken sichern, sodass sich die Client-Apps, die Azure AD B2C verwenden, bei der API authentifizieren können. In diesem Artikel erfahren Sie, wie eine .NET MVC-App für eine Aufgabenliste erstellt wird, die Benutzerregistrierung, -anmeldung und die Verwaltung von Profilen umfasst. Die Aufgabenlisten der einzelnen Benutzer werden von einem Aufgabendienst gespeichert – einer Web-API, mit der authentifizierte Benutzer Aufgaben in ihrer Aufgabenliste erstellen und lesen können.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. Wenn Sie noch nicht über ein Verzeichnis verfügen, [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

## 2\. Erstellen einer Anwendung

Nun müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Ersetzen Sie

- Nehmen Sie eine **Web-App/Web-API** in die Anwendung auf.
- Verwenden Sie für die Web-App den **Umleitungs-URI** `https://localhost:44316/` – dies ist der Standardspeicherort des Web-App-Clients für dieses Codebeispiel.
- Notieren Sie sich die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie später.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [**Richtlinie**](active-directory-b2c-reference-policies.md) definiert. Der Client in diesem Codebeispiel enthält drei Benutzeroberflächen, für die Identitäten relevant sind: Registrierung, Anmeldung und Profilbearbeitung. Sie müssen eine Richtlinie für jeden Typ erstellen, wie im [Artikel für Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Wenn Sie die drei Richtlinien erstellen, beachten Sie Folgendes:

- Wählen Sie **Registrierung mit Benutzer-ID** oder **E-Mail-Registrierung** auf dem Blatt für den Identitätsanbieter aus.
- Wählen Sie den **Anzeigenamen** und einige andere Anmeldeattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsanspruch in jeder Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich den **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie benötigen diese Richtliniennamen in Kürze. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

## 4\. Herunterladen des Codes

Der Code für dieses Lernprogramm wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet) verwaltet. Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip) oder das Gerüst klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

Die fertige App ist ebenfalls [als ZIP-Datei verfügbar](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) und unter `complete` im gleichen Repository enthalten.

Nachdem Sie den Beispielcode heruntergeladen haben, öffnen Sie die `.sln`-Datei in Visual Studio, um zu beginnen. Wie Sie sehen, sind in der Projektmappe zwei Projekte enthalten: ein `TaskWebApp`-Projekt und ein `TaskService`-Projekt. `TaskWebApp` ist eine MVC-Webanwendung, mit der der Benutzer interagiert. `TaskService` ist die Back-End-Web-API der App, in der die Aufgabenliste des Benutzers gespeichert wird.

## 5\. Konfigurieren der Aufgaben-Web-App

Wenn der Benutzer mit `TaskWebApp` interagiert, sendet der Client Anfragen an Azure AD und erhält Token, die für den Aufruf der Web-API `TaskService` verwendet werden können. Damit sich die Benutzer anmelden und Token erhalten können, benötigt `TaskWebApp` einige Informationen über Ihre App. Öffnen Sie im `TaskWebApp`-Projekt die Datei `web.config` aus dem Stammverzeichnis des Projekts, und ersetzen Sie die Werte im Abschnitt `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

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

Wenn Sie wissen möchten, wie eine Web-App wie `TaskWebApp` Azure AD B2C verwendet, sehen Sie sich unseren [Artikel mit den ersten Schritten für die Web-App-Anmeldung](active-directory-b2c-devquickstarts-web-dotnet.md) an.

## 6\. Sichern der API

Da Sie jetzt über einen Client verfügen, der die API im Auftrag eines Benutzers aufruft, können Sie `TaskService` mit OAuth 2.0-Bearertoken sichern. Die API kann Token mit der OWIN-Bibliothek von Microsoft akzeptieren und überprüfen.

#### Installieren von OWIN
Installieren Sie zuerst die OWIN-OAuth-Authentifizierungspipeline:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

#### Eingeben der B2C-Details
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

#### Hinzufügen einer OWIN-Startklasse
Fügen Sie dem `TaskService`-Projekt eine OWIN-Startklasse mit dem Namen `Startup.cs` hinzu. Klicken Sie mit der rechten Maustaste auf das Projekt, wählen Sie **Hinzufügen** --> **Neues Element** aus, und suchen Sie nach „OWIN“.
  

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

#### Konfigurieren der OAuth 2.0-Authentifizierung
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
			// This is where you specify that your API only accepts tokens from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

#### Sichern des Aufgabencontrollers
Nachdem Sie die App für die OAuth 2.0-Authentifizierung konfiguriert haben, müssen Sie nur noch ein `[Authorize]`-Tag zum Aufgabencontroller hinzufügen, um Ihre Web-API zu sichern. Dies ist der Controller, in dem die gesamte Bearbeitung der Aufgabenliste durchgeführt wird. Daher wird der gesamte Controller auf Klassenebene gesichert. Sie könnten das `[Authorize]`-Tag auch einzelnen Aktionen für eine präzisere Kontrolle hinzufügen.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

#### Abrufen von Benutzerinformationen aus dem Token
`TaskController` speichert Aufgaben in einer Datenbank, wobei jede Aufgabe einen zugeordneten Benutzer aufweist, der die Aufgabe "besitzt". Der Besitzer wird durch die **Objekt-ID** des Benutzers identifiziert (deshalb musste die Objekt-ID als Anwendungsanspruch allen Richtlinien hinzugefügt werden):

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## 7\. Ausführen der Beispiel-App

Erstellen Sie abschließend `TaskWebApp` und `TaskService`, und führen Sie beide aus. Registrieren Sie sich für die App mit einer E-Mail-Adresse oder einem Benutzernamen. Erstellen Sie einige Aufgaben in der Aufgabenliste des Benutzers, und beachten Sie, wie sie in der API beibehalten werden, auch nachdem Sie den Client beendet und neu gestartet haben.

## 8\. Bearbeiten der Richtlinien

Nachdem Sie eine API mit Azure AD B2C gesichert haben, können Sie mit Richtlinien für Ihre App experimentieren und den Effekt (oder dessen Fehlen) auf die API sehen. Sie können <!--add **identity providers**
to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->die **Anwendungsansprüche** in den Richtlinien bearbeiten und die Benutzerinformationen ändern, die in der Web-API verfügbar sind. Zusätzliche Ansprüche, die Sie hinzufügen, stehen Ihrer .NET MVC-Web-API im `ClaimsPrincipal`-Objekt zur Verfügung, wie oben beschrieben.

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO3-->