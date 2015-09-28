<properties
	pageTitle="Azure AD B2C-Vorschau | Microsoft Azure"
	description="Erstellen einer Webanwendung mit Registrierung, Anmeldung und Profilverwaltung mithilfe von Azure AD B2C."
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

# Azure AD B2C-Vorschau: Erstellen einer .NET-Web-App

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Mit Azure AD B2C können Sie Ihren Web-Apps in wenigen Schritten leistungsstarke Self-Service-Features zur Identitätsverwaltung hinzufügen. In diesem Artikel erfahren Sie, wie eine .NET MVC-Web-App erstellt wird, die Benutzerregistrierung, -anmeldung und die Verwaltung von Profilen umfasst. Sie umfasst auch die Unterstützung für die Registrierung und Anmeldung mit einem Benutzernamen oder einer E-Mail-Adresse sowie über Konten sozialer Netzwerke wie Facebook oder Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. Wenn Sie noch nicht über ein Verzeichnis verfügen, [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

## 2\. Erstellen einer Anwendung

Nun müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Ersetzen Sie

- Fügen Sie der Anwendung eine **Web-App/Web-API** hinzu.
- Geben Sie `https://localhost:44316/` als **Antwort-URL** ein – dies ist die Standard-URL für dieses Codebeispiel.
- Notieren Sie sich die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie später.

## 3\. Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [**Richtlinie**](active-directory-b2c-reference-policies.md) definiert. Dieses Codebeispiel enthält drei Benutzeroberflächen, für die Identitäten relevant sind: Registrierung, Anmeldung und Profilbearbeitung. Sie müssen eine Richtlinie für jeden Typ erstellen, wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Wenn Sie die drei Richtlinien erstellen, beachten Sie Folgendes:

- Wählen Sie **Registrierung mit Benutzer-ID** oder **E-Mail-Registrierung** auf dem Blatt für den Identitätsanbieter aus.
- Wählen Sie den **Anzeigenamen** und einige andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** als Anwendungsanspruch in den Richtlinien aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen in Kürze. 

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

## 4\. Herunterladen des Codes und Konfigurieren der Authentifizierung

Der Code für dieses Beispiel wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet) verwaltet. Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP herunterladen](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) oder das Gerüst klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

Das fertige Beispiel ist ebenfalls [als ZIP verfügbar](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) und unter `complete` im gleichen Repository enthalten.

Nachdem Sie den Beispielcode heruntergeladen haben, öffnen Sie die `.sln`-Datei in Visual Studio, um zu beginnen.

Ihre App kommuniziert mit Azure AD B2C durch Senden von HTTP-Authentifizierungsanforderungen, in denen die Richtlinie, die ausgeführt werden soll, als Teil der Anforderung angegeben wird. Für .NET-Webanwendungen können Sie die OWIN-Bibliothek von Microsoft verwenden, um OpenID Connect-Authentifizierungsanfragen zu senden, Richtlinien auszuführen, die Sitzung des Benutzers zu verwalten usw.

Fügen Sie dem Projekt zunächst über die Visual Studio-Paket-Manager-Konsole die NuGet-Pakete der OWIN-Middleware hinzu.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Öffnen Sie dann die Datei `web.config` aus dem Stammverzeichnis des Projekts, und geben Sie die Konfigurationswerte Ihrer App im Abschnitt `<appSettings>` ein.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="[Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com]" /> 
    <add key="ida:ClientId" value="[Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

Fügen Sie dem Projekt eine OWIN-Startklasse mit dem Namen `Startup.cs` hinzu. Klicken Sie mit der rechten Maustaste auf das Projekt, wählen Sie **Hinzufügen** --> **Neues Element** aus, und suchen Sie nach "OWIN". Ändern Sie die Klassendeklaration in `public partial class Startup` – einen Teil dieser Klasse haben wir bereits für Sie in einer anderen Datei implementiert. Die OWIN-Middleware ruft die Methode `Configuration(...)` auf, wenn Ihre App gestartet wird. Rufen Sie in dieser Methode "ConfigureAuth(...)" auf, wo die Authentifizierung für Ihre App eingerichtet wird.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Öffnen Sie die Datei `App_Start\Startup.Auth.cs`, und implementieren Sie die Methode `ConfigureAuth(...)`. Die Parameter, die Sie in `OpenIdConnectAuthenticationOptions` bereitstellen, dienen als Koordinaten für Ihre Anwendung zur Kommunikation mit Azure AD. Außerdem müssen Sie eine Cookieauthentifizierung einrichten, da die OpenID Connect-Middleware Cookies verwendet, um z. B. Benutzersitzungen zu verwalten.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // The ACR claim is used to indicate which policy was executed
    public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
    public const string PolicyKey = "b2cpolicy";
    public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

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
                AuthenticationFailed = AuthenticationFailed,
                RedirectToIdentityProvider = OnRedirectToIdentityProvider,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
            // endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);
            
    }
```

## 5\. Senden von Authentifizierungsanforderungen an Azure AD
Ihre Anwendung ist nun ordnungsgemäß für die Kommunikation mit Azure AD B2C über das Authentifizierungsprotokoll OpenID Connect konfiguriert. OWIN kümmert sich um all die mühseligen Details der Erstellung von Authentifizierungsnachrichten, Überprüfung der Azure AD-Tokens und Verwaltung der Benutzersitzungen. Jetzt müssen nur noch die einzelnen Benutzervorgänge initiiert werden.

Wenn ein Benutzer auf die Schaltflächen zum Registrieren, Anmelden oder Bearbeiten des Profils in der Web-App klickt, wird die zugeordnete Aktion in `Controllers\AccountController.cs` aufgerufen. Mit integrierten OWIN-Methoden können Sie in jedem Fall die richtige Richtlinie auslösen:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties using the PolicyKey provided.
    
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties (
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.SignInPolicyId}
                })
            { 
                RedirectUri = "/", 
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.SignUpPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string> 
                { 
                    {Startup.PolicyKey, Startup.ProfilePolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

Sie können auch ein benutzerdefiniertes `PolicyAuthorize`-Tag in Ihren Controllern verwenden, um zu erfordern, dass eine bestimmte Richtlinie ausgeführt wird, wenn der Benutzer noch nicht angemeldet ist. Öffnen Sie `Controllers\HomeController.cs`, und fügen Sie dem Anspruchscontroller das `[PolicyAuthorize]`-Tag hinzu. Achten Sie darauf, die enthaltene Beispielrichtlinie mit Ihrer eigenen Anmelderichtlinie zu ersetzen.

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed into the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

Mit OWIN können Sie den Benutzer auch von der App abmelden. In `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string> 
            { 
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

Standardmäßig sendet OWIN die angegebenen Richtlinien nicht in `AuthenticationProperties` an Azure AD. Sie können jedoch die von OWIN in der `RedirectToIdentityProvider`-Benachrichtigung generierten Anforderungen bearbeiten. Verwenden Sie diese Benachrichtigung in `App_Start\Startup.Auth.cs`, um den richtigen Endpunkt für jede Richtlinie aus den Metadaten der Richtlinie abzurufen. Dadurch wird sichergestellt, dass für jede Richtlinie, die Ihre App ausführen möchte, die richtige Anforderung an Azure AD gesendet wird.

```C#
// App_Start\Startup.Auth.cs

private async Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    PolicyConfigurationManager mgr = notification.Options.ConfigurationManager as PolicyConfigurationManager;
    if (notification.ProtocolMessage.RequestType == OpenIdConnectRequestType.LogoutRequest)
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseRevoke.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.EndSessionEndpoint;
    }
    else
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseChallenge.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.AuthorizationEndpoint;
    }
}
``` 

## 6\. Anzeigen der Benutzerinformationen
Bei der Authentifizierung von Benutzern mit OpenID Connect gibt Azure AD ein ID-Token an die App zurück. Dieses enthält die **Ansprüche** bzw. Assertionen des Benutzers. Mit diesen Ansprüchen können Sie Ihre App personalisieren:

Öffnen Sie die Datei `Controllers\HomeController.cs`. Auf die Ansprüche des Benutzers können Sie in Ihren Controllern über das Sicherheitsprinzipalobjekt `ClaimsPrincipal.Current` zugreifen.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
	Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
	ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Sie können auf alle Ansprüche, die Ihre Anwendung erhält, auf die gleiche Weise zugreifen. Eine Liste aller Ansprüche, die die App erhält, können Sie sich auf der Seite mit Ansprüchen ansehen.

## 7\. Ausführen der Beispiel-App

Erstellen und führen Sie Ihre Anwendung zum Schluss aus! Registrieren Sie sich für die App mit einer E-Mail-Adresse oder einem Benutzernamen. Melden Sie sich ab und unter demselben Benutzer wieder an. Bearbeiten Sie das Profil dieses Benutzers. Melden Sie sich ab, und registrieren Sie sich als anderer Benutzer. Beachten Sie, dass die Informationen auf der Registerkarte **Ansprüche** den Informationen entsprechen, die Sie in Ihren Richtlinien konfiguriert haben.

## 8\. Soziale Netzwerke als IDPs hinzufügen

Die App unterstützt bisher nur die Registrierung und Anmeldung von Benutzern mit sogenannten **lokalen Konten** – Konten in Ihrem B2C-Verzeichnis mit einem Benutzernamen und einem Kennwort. Mit Azure AD B2C können Sie auch andere **Identitätsanbieter** (oder IDPs) hinzufügen, ohne Ihren Code ändern zu müssen.

Um Ihrer App soziale Netzwerke als IDPs hinzuzufügen, befolgen Sie zunächst die detaillierten Anweisungen in einem oder zwei der folgenden Artikel. Sie müssen für jeden IDP, den Sie unterstützen möchten, eine Anwendung in dessen System registrieren und eine Client-ID abrufen.

- [Einrichten von Facebook als IDP](active-directory-b2c-setup-fb-app.md)
- [Einrichten von Google als IDP](active-directory-b2c-setup-goog-app.md)
- [Einrichten von Amazon als IDP](active-directory-b2c-setup-amzn-app.md)
- [Einrichten von LinkedIn als IDP](active-directory-b2c-setup-li-app.md) 

Wenn Sie die Identitätsanbieter Ihrem B2C-Verzeichnis hinzugefügt haben, müssen Sie jede der drei Richtlinien bearbeiten, um die neuen IDPs wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md) beschrieben einzufügen. Nachdem Sie Ihre Richtlinien gespeichert haben, führen Sie die App einfach erneut aus. Die neuen IDPs sollten als eine Registrierungs- und Anmeldeoption auf allen Benutzeroberflächen für Identitäten angezeigt werden.

Sie können frei mit Ihren Richtlinien experimentieren und die Auswirkung auf die Beispiel-App beobachten – Sie können IDPs hinzufügen/entfernen oder die Anwendungsansprüche oder Registrierungsattribute ändern. Experimentieren Sie so lange, bis Sie die Verknüpfung von Richtlinien, Authentifizierungsanforderungen und OWIN verstanden haben.

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [hier als ZIP-Datei bereit](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Sie können es alternativ aus GitHub klonen:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO3-->