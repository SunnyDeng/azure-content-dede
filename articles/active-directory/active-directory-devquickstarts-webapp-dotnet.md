<properties
	pageTitle="Erste Schritte in Azure AD .NET | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine .NET-MVC-Webanwendung erstellt wird, die sich für die Anmeldung in Azure AD integriert."
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
	ms.date="04/28/2015"
	ms.author="dastrock"/>

# An- und Abmeldung bei Webanwendungen mit Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD erleichtert die Auslagerung der Identitätsverwaltung für Webanwendungen, indem es eine einmalige An- und Abmeldung (Single Sign-on und Single Sign-out) mit nur wenigen Codezeilen ermöglicht. Für Asp.NET-Webanwendungen erreichen Sie das Gleiche durch die Microsoft-Implementierung der Community-gestützten und in .NET Framework 4.5 enthaltenen OWIN-Middleware. Hier verwenden wir OWIN für Folgendes: – Anmelden eines Benutzers bei der Anwendung mit Azure AD als Identitätsanbieter. – Anzeigen von Informationen zum Benutzer. – Abmelden des Benutzers von der Anwendung.

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren einer Anwendung in Azure AD
2. Konfigurieren Ihrer Anwendung für die Verwendung der OWIN-Authentifizierungspipeline
3. Ausgabe von An- und Abmeldeanforderungen an Azure AD mit OWIN
4. Ausdrucken von Informationen zum Benutzer

Beginnen Sie, indem Sie [das Anwendungsgerüst](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) oder [das vollständige Beispiel herunterladen](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip). Außerdem benötigen Sie einen Azure AD-Mandanten, bei dem Sie Ihre Anwendung registrieren. Wenn Sie noch keinen Mandanten haben, [erfahren Sie hier, wie Sie einen erhalten](active-directory-howto-tenant.md).

## *1. Registrieren einer Anwendung in Azure AD*
Damit Ihre Anwendung Benutzer authentifizieren kann, müssen Sie zunächst in Ihrem Mandanten eine neue Anwendung registrieren.

- Melden Sie sich beim Azure-Verwaltungsportal an.
- Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
- Wählen Sie den Mandanten aus, in dem die Beispielanwendung registriert werden soll.
- Klicken Sie auf die Registerkarte **Anwendungen** und dann in der unteren Schublade auf „Hinzufügen“.
- Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue **Webanwendung und/oder Web-API**.
    - Am **Namen** der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können.
    -	Die **Anmelde-URL** ist die Basis-URL Ihrer Anwendung. Der Standardwert des Gerüsts lautet `https://localhost:44320/`.
    - Die **App-ID-URI** ist eine eindeutige Kennung für die Anwendung. Üblicherweise wird `https://<tenant-domain>/<app-name>` verwendet, zum Beispiel: `https://contoso.onmicrosoft.com/my-first-aad-app`
- Nach Abschluss der Registrierung weist AAD Ihrer Anwendung eine eindeutige Client-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten, weswegen Sie ihn aus der Registerkarte „Konfigurieren“ kopieren sollten.

## *2. Konfigurieren Ihrer Anwendung für die Verwendung der OWIN-Authentifizierungspipeline*
Hier konfigurieren wir die OWIN-Middleware für die Verwendung des Authentifizierungsprotokolls OpenID Connect. OWIN wird unter anderem für die Ausgabe von Anmelde- und Abmeldeanforderungen, für die Verwaltung der Benutzerssitzungen und für das Abrufen der Benutzerinformationen verwendet.

-	Fügen Sie dem Projekt zunächst über die Paket-Manager-Konsole die NuGet-Pakete der OWIN-Middleware hinzu.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	Fügen Sie dem Projekt eine OWIN-Startklasse mit dem Namen `Startup.cs` hinzu. Klicken Sie dazu mit der rechten Maustaste auf das Projekt, wählen Sie **Hinzufügen** --> **Neues Element** aus, und suchen Sie nach „OWIN“. Die OWIN-Middleware ruft beim Starten Ihrer Anwendung die Methode `Configuration(...)` auf.
-	Ändern Sie die Klassendeklaration in `public partial class Startup` – einen Teil dieser Klasse haben wir bereits für Sie in einer anderen Datei implementiert. Rufen Sie in der Methode `Configuration(...)` „ConfigureAuth(...)“ auf, um die Authentifizierung für Ihre Webanwendung einzurichten.  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Öffnen Sie die Datei `App_Start\Startup.Auth.cs`, und implementieren Sie die Methode `ConfigureAuth(...)`. Die Parameter, die Sie in `OpenIDConnectAuthenticationOptions` bereitstellen, dienen als Koordinaten für Ihre Anwendung zur Kommunikation mit Azure AD. Außerdem müssen Sie eine Cookie-Authentifizierung einrichten, da die Middleware OpenID Connect im Hintergrund Cookies verwendet.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-	Öffnen Sie abschließend die Datei `web.config` aus dem Stammverzeichnis des Projekts, und geben Sie Ihre Konfigurationswerte im Abschnitt `<appSettings>` ein.
    -	Die `ida:ClientId` der Anwendung ist die GUID, die Sie in Schritt 1 aus dem Azure-Portal kopiert haben.
    -	`ida:Tenant` ist der Name Ihres Azure AD-Mandanten, z. B. „contoso.onmicrosoft.com“.
    -	Ihre `ida:PostLogoutRedirectUri` weist Azure AD an, wohin ein Benutzer nach einer erfolgreich ausgeführten Abmeldeanforderung umgeleitet werden soll.

## *3. Ausgabe von An- und Abmeldeanforderungen an Azure AD mit OWIN*
Ihre Anwendung ist nun ordnungsgemäß für die Kommunikation mit Azure AD über das Authentifizierungsprotokoll OpenID Connect konfiguriert. OWIN kümmert sich um all die mühseligen Details der Erstellung von Authentifizierungsnachrichten, Überprüfung der Azure AD-Tokens und Verwaltung der Benutzersitzungen. Sie müssen lediglich dafür sorgen, dass sich Ihre Benutzer an- und abmelden können.

- Über Autorisierungstags in Ihren Controllern können Sie die Benutzer vor dem Zugriff auf eine bestimmte Seite zur Anmeldung zwingen. Öffnen Sie `Controllers\HomeController.cs`, und fügen Sie dem Info-Controller den Tag `[Authorize]` hinzu.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	Mit OWIN können Sie Authentifizierungsanforderungen auch direkt aus Ihrem Code ausgeben. Öffnen Sie `Controllers\AccountController.cs`. Geben Sie in den Aktionen „SignIn()“ und „SignOut()“ OpenID Connect-Challenge- und Abmeldeanforderungen ein.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-	Öffnen Sie dann `Views\Shared_LoginPartial.cshtml`. In dieser Ansicht werden dem Benutzer sein Benutzername sowie die An- und Abmeldelinks für Ihre Anwendung angezeigt.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## *4. Anzeigen der Benutzerinformationen*
Bei der Authentifizierung von Benutzern mit OpenID Connect gibt Azure AD ein ID-Token an die Anwendung zurück. Dieses enthält die „Ansprüche“ bzw. Assertionen des Benutzers. Mit diesen Ansprüchen können Sie Ihre Anwendung personalisieren:

- Öffnen Sie die Datei `Controllers\HomeController.cs`. Auf die Ansprüche des Benutzers können Sie in Ihren Controllern über das Sicherheitsprinzipalobjekt `ClaimsPrincipal.Current` zugreifen.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Erstellen und führen Sie Ihre Anwendung zum Schluss aus! Falls noch nicht geschehen, ist es nun Zeit, in Ihrem Mandanten einen neuen Benutzer mit einer *.onmicrosoft.com-Domäne zu erstellen. Melden Sie sich unter diesem Benutzer an, und achten Sie darauf, wie dessen Identität in der oberen Navigationsleiste angezeigt wird. Melden Sie sich ab, und melden Sie sich unter dem Namen eines neuen Benutzers wieder bei Ihrem Mandanten an. Wenn Sie besonders ambitioniert sind und noch mehr ausprobieren wollen, können Sie auch eine weitere Instanz dieser Anwendung (mit eigener Client-ID) registrieren und ausführen. Sie erleben dann die einmalige Anmeldung (SSO) in Aktion.

Als Referenz stellen wir [hier](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) das vollständige Beispiel (ohne Ihre Konfigurationswerte) bereit.

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

[Schützen einer Web-API mit Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

Sehen Sie sich auch die folgenden Ressourcen an: – [AzureAD-Beispiele auf GitHub >>](https://github.com/AzureAdSamples) – [CloudIdentity.com >>](https://cloudidentity.com) – Azure AD-Dokumentation auf [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=62-->