<properties
	pageTitle="App-Modell v2.0 .NET-Web-App | Microsoft Azure"
	description="Vorgehensweise beim Erstellen einer .NET MVC-Web-App, bei der sich Benutzer sowohl mit ihrem persönlichen Microsoft-Konto als auch ihrem Geschäfts- oder Schulkonto anmelden können."
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
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# App-Modell v2. 0 (Vorschauversion): Hinzufügen der Anmeldung bei einer .NET-MVC-Web-App

Mit dem App-Modell v2. 0 können Sie schnell eine Authentifizierung zu Ihren Web-Apps hinzufügen, die sowohl persönliche Microsoft-Konten als auch Geschäfts- oder Schulkonten unterstützt. Für ASP.NET-Webanwendungen erreichen Sie das Gleiche durch die in .NET Framework 4.5 enthaltenen OWIN-Middleware von Microsoft.

>[AZURE.NOTE]
Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

 Hier verwenden wir OWIN für Folgendes:
-	Anmelden eines Benutzers bei der Anwendung mit Azure AD und dem App-Modell v2.0.
-	Anzeigen von Informationen zum Benutzer.
-	Abmelden des Benutzers von der Anwendung.

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren einer App
2. Konfigurieren Ihrer Anwendung für die Verwendung der OWIN-Authentifizierungspipeline
3. Ausgabe von An- und Abmeldeanforderungen an Azure AD mit OWIN
4. Ausdrucken von Informationen zum Benutzer

Der Code für dieses Lernprogramm wird [auf GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet) verwaltet. Um folgen zu können, können Sie [das App-Gerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) oder das Gerüst klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

Die fertige App wird außerdem am Ende dieses Lernprogramms bereitgestellt.

## 1\. Registrieren einer App
Erstellen Sie eine neue App unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com), oder führen Sie die folgenden [ausführlichen Schritte](active-directory-v2-app-registration.md) aus. Stellen Sie sicher, dass Sie:

- die Ihrer App zugewiesene **Anwendungs-ID** kopieren. Sie benötigen Sie in Kürze.
- die **Web**-Plattform für Ihre App hinzufügen.
- den richtigen **Umleitungs-URI** eingeben. Der Umleitungs-URI verweist auf Azure AD, wohin Authentifizierungsantworten gesendet werden sollen – der Standardwert in diesem Lernprogramm lautet `https://localhost:44326/`.

## 2\. Konfigurieren Ihrer Anwendung für die Verwendung der OWIN-Authentifizierungspipeline
Hier konfigurieren wir die OWIN-Middleware für die Verwendung des Authentifizierungsprotokolls OpenID Connect. OWIN wird unter anderem für die Ausgabe von Anmelde- und Abmeldeanforderungen, für die Verwaltung der Benutzerssitzungen und für das Abrufen der Benutzerinformationen verwendet.

-	Öffnen Sie zunächst die Datei `web.config` aus dem Stammverzeichnis des Projekts, und geben Sie die Konfigurationswerte Ihrer App im Abschnitt `<appSettings>` ein.
    -	`ida:ClientId` ist die **Anwendungs-ID**, die Ihrer App im Registrierungsportal zugewiesen ist.
    -	`ida:RedirectUri` ist der **Umleitungs-URI**, den Sie im Portal eingegeben haben.

-	Fügen Sie dem Projekt als Nächstes über die Paket-Manager-Konsole die NuGet-Pakete der OWIN-Middleware hinzu.

```
PM > Install-Package Microsoft.Owin.Security.OpenIdConnect
PM > Install-Package Microsoft.Owin.Security.Cookies
PM > Install-Package Microsoft.Owin.Host.SystemWeb
```

-	Fügen Sie dem Projekt eine OWIN-Startklasse mit dem Namen `Startup.cs` hinzu. Klicken Sie dazu mit der rechten Maustaste auf das Projekt, wählen Sie **Hinzufügen** --> **Neues Element** aus, und suchen Sie nach „OWIN“. Die OWIN-Middleware ruft beim Starten Ihrer Anwendung die Methode `Configuration(...)` auf.
-	Ändern Sie die Klassendeklaration in `public partial class Startup` – einen Teil dieser Klasse haben wir bereits für Sie in einer anderen Datei implementiert. Rufen Sie in der Methode `Configuration(...)` „ConfigureAuth(...)“ auf, um die Authentifizierung für Ihre Web-App einzurichten.  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
	public partial class Startup
	{
		public void Configuration(IAppBuilder app)
		{
			ConfigureAuth(app);
		}
	}
}```

-	Open the file `App_Start\Startup.Auth.cs` and implement the `ConfigureAuth(...)` method.  The parameters you provide in `OpenIdConnectAuthenticationOptions` will serve as coordinates for your app to communicate with Azure AD.  You'll also need to set up Cookie Authentication - the OpenID Connect middleware uses cookies underneath the covers.

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
									 RedirectUri = redirectUri,
									 Scope = "openid",
									 ResponseType = "id_token",
									 PostLogoutRedirectUri = redirectUri,
									 TokenValidationParameters = new TokenValidationParameters
									 {
											 ValidateIssuer = false,
									 },
									 Notifications = new OpenIdConnectAuthenticationNotifications
									 {
											 AuthenticationFailed = OnAuthenticationFailed,
									 }
							 });
			 }
```

## 3. Ausgabe von An- und Abmeldeanforderungen an Azure AD mit OWIN
Ihre Anwendung ist nun ordnungsgemäß für die Kommunikation mit dem v2.0-Endpunkt über das Authentifizierungsprotokoll OpenID Connect konfiguriert.  OWIN kümmert sich um all die mühseligen Details der Erstellung von Authentifizierungsnachrichten, Überprüfung der Azure AD-Tokens und Verwaltung der Benutzersitzungen.  Sie müssen lediglich dafür sorgen, dass sich Ihre Benutzer an- und abmelden können.

- Über Autorisierungstags in Ihren Controllern können Sie die Benutzer vor dem Zugriff auf eine bestimmte Seite zur Anmeldung zwingen.  Öffnen Sie `Controllers\HomeController.cs`, und fügen Sie dem Info-Controller den Tag `[Authorize]` hinzu.

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

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-	Öffnen Sie dann `Views\Shared\_LoginPartial.cshtml`. In dieser Ansicht werden dem Benutzer sein Benutzername sowie die An- und Abmeldelinks für Ihre Anwendung angezeigt.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## 4\. Anzeigen der Benutzerinformationen
Bei der Authentifizierung von Benutzern mit OpenID Connect gibt der v2.0-Endpunkt ein ID-Token an die App zurück. Dieses enthält die [Ansprüche](active-directory-v2-tokens.md#id_tokens) bzw. Assertionen des Benutzers. Mit diesen Ansprüchen können Sie Ihre Anwendung personalisieren:

- Öffnen Sie die Datei `Controllers\HomeController.cs`. Auf die Ansprüche des Benutzers können Sie in Ihren Controllern über das Sicherheitsprinzipalobjekt `ClaimsPrincipal.Current` zugreifen.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

Erstellen und führen Sie Ihre Anwendung zum Schluss aus! Melden Sie sich entweder mit einem persönlichen Microsoft-Konto oder einem Geschäfts- oder Schulkonto an, und beachten Sie, wie die Identität des Benutzers in der oberen Navigationsleiste dargestellt wird. Sie verfügen jetzt über eine mit branchenüblichen Protokollen gesicherte Web-App, die Benutzer mit ihren persönlichen Konten oder ihren Geschäfts- oder Schulkonten authentifizieren kann.

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [hier als ZIP-Datei bereit](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Sie können es alternativ aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## Nächste Schritte

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

[Sichern einer Web-API mit dem App-Modell v2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Weitere Ressourcen:
- [App-Modell v2.0 (Vorschauversion) >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow-Tag "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=AcomDC_1203_2015-->
