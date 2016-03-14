<properties
   pageTitle="Authentifizierung in mehrinstanzenfähigen Anwendungen | Microsoft Azure"
   description="Wie eine mehrinstanzenfähige Anwendung Benutzer von Azure AD authentifizieren kann"
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

# Authentifizierung in mehrinstanzenfähigen Anwendungen

Dieser Artikel ist [Teil einer Serie](guidance-multitenant-identity.md). Es gibt auch eine vollständige [Beispielanwendung], die diese Serie begleitet.

In diesem Artikel wird beschrieben, wie eine mehrinstanzenfähige Anwendung Benutzer von Azure Active Directory (Azure AD) mithilfe von OpenID Connect (OIDC) authentifizieren kann.

## Übersicht

Unsere [Referenzimplementierung](guidance-multitenant-identity-tailspin.md) ist eine ASP.NET Core 1.0-Anwendung. Die Anwendung verwendet die integrierte OpenID Connect-Middleware, um den OIDC-Authentifizierungsablauf auszuführen. Im folgenden Diagramm wird dargestellt, was geschieht, wenn sich der Benutzer auf übergeordneter Ebene anmeldet.

![Authentifizierungsfluss](media/guidance-multitenant-identity/auth-flow.png)

1.	Der Benutzer klickt in der App auf die Schaltfläche „Anmelden“. Diese Aktion wird von einem MVC-Controller bearbeitet.
2.	Der MVC-Controller gibt eine **ChallengeResult**-Aktion zurück.
3.	Die Middleware fängt die **ChallengeResult**-Aktion ab, und erstellt eine 302-Antwort, die den Benutzer auf die Azure AD-Anmeldeseite umleitet.
4.	Der Benutzer authentifiziert sich mit Azure AD.
5.	Azure AD sendet ein ID-Token an die Anwendung.
6.	Die Middleware überprüft das ID-Token. Der Benutzer ist jetzt in der Anwendung authentifiziert.
7.	Die Middleware leitet den Benutzer zur Anwendung zurück.

## Registrieren der Anwendung in Azure AD

Um OpenID Connect zu aktivieren, registriert der SaaS-Anbieter die Anwendung in seinen eigenen Azure AD-Mandanten.

Um die Anwendung zu registrieren, befolgen Sie die Schritte unter [Integrieren von Anwendungen in Azure Active Directory](../active-directory/active-directory-integrating-applications.md) im Abschnitt „So registrieren Sie eine neue Anwendung im Azure-Verwaltungsportal“.

Auf der Seite **Konfigurieren**:

-	Beachten Sie die Client-ID.
-	Unter **Anwendung ist mehrinstanzenfähig** klicken Sie auf **Ja**.
-	Legen Sie die **Antwort-URL** auf eine URL fest, an die Azure AD die Authentifizierungsantwort sendet. Sie können dazu die Basis-URL Ihrer App verwenden.
  -	Hinweis: Der URL-Pfad ist beliebig, solange der Hostname Ihrer bereitgestellten App entspricht.
  -	Sie können mehrere Antwort-URLs festlegen. Während der Entwicklung können Sie eine `localhost`-Adresse verwenden, um die App lokal auszuführen.
-	Generieren eines geheimen Clientschlüssels: Klicken Sie unter **Schlüssel** auf die Dropdownliste mit dem Titel **Dauer auswählen**, und wählen Sie 1 oder 2 Jahre aus. Der Schlüssel wird angezeigt, wenn Sie auf **Speichern** klicken. Achten Sie darauf, den Wert zu kopieren. Dieser wird nicht erneut angezeigt, wenn Sie die Konfigurationsseite erneut laden.

## Konfigurieren der Authentifizierungsmiddleware

Dieser Abschnitt beschreibt, wie die Authentifizierungsmiddleware in ASP.NET Core 1.0 für mehrinstanzenfähige Authentifizierung mit OpenID Connect konfiguriert wird.

Fügen Sie in Ihrer Startup-Klasse die OpenID Connect-Middleware hinzu:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] Weitere Informationen finden Sie unter [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs).

Weitere Informationen über die Startup-Klasse finden Sie unter [Anwendungsstart](https://docs.asp.net/en/latest/fundamentals/startup.html) in der Dokumentation zu ASP.NET Core 1.0.

Legen Sie folgende Middleware-Optionen fest:

- **ClientId**. Dies ist die Client-ID der Anwendung, die Sie erhalten haben, als Sie die Anwendung in Azure AD registriert haben.
- **Authority**. Für eine mehrinstanzenfähige Anwendung legen Sie diesen Eintrag auf `https://login.microsoftonline.com/common/` fest. Dies ist die URL für den allgemeinen Endpunkt von Azure AD. Sie erlaubt Benutzern aller Azure AD-Mandanten, sich anzumelden. Weitere Informationen zum allgemeinen Endpunkt finden Sie in [diesem Blogbeitrag](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/).
- Legen Sie in **TokenValidationParameters** **ValidateIssuer** auf „false“ fest. Das bedeutet, dass die App für die Überprüfung des Ausstellerwerts im ID-Token zuständig ist. (Die Middleware überprüft weiterhin das Token.) Weitere Informationen zum Überprüfen des Ausstellers finden Sie unter [Überprüfung des Ausstellers](guidance-multitenant-identity-claims.md#issuer-validation).
- **CallbackPath**. Setzen Sie diesen mit dem Pfad in der Antwort-URL gleich, die Sie in Azure AD registriert haben. Wenn die Antwort-URL z. B. `http://contoso.com/aadsignin` ist, sollte **CallbackPath** `aadsignin` sein. Wenn Sie diese Option nicht festlegen, ist der Standardwert `signin-oidc`.
- **PostLogoutRedirectUri**. Geben Sie eine URL an, um Benutzer nach dem Abmelden umzuleiten. Dies sollte eine Seite sein, die anonyme Anfragen ermöglicht. In der Regel ist dies die Startseite.
- **SignInScheme**. Legen Sie diesen Eintrag auf `CookieAuthenticationDefaults.AuthenticationScheme` fest. Diese Einstellung bedeutet, dass die Benutzeransprüche nach der Authentifizierung des Benutzers lokal in einem Cookie gespeichert werden. Dieses Cookie legt fest, wie der Benutzer während der Browsersitzung angemeldet bleibt.
- **Ereignisse.** Ereignisrückrufe: siehe [Authentifizierungsereignisse](#authentication-events).

Fügen Sie außerdem die Middleware für die Cookie-Authentifizierung zur Pipeline hinzu. Diese Middleware ist dafür verantwortlich, die Benutzeransprüche in ein Cookie zu schreiben, und es im Anschluss aus einem Cookie auszulesen, während Seiten geladen werden.

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## Initiierung des Authentifizierungsablaufs

Zum Starten des Authentifizierungsablaufs in ASP.NET MVC geben Sie ein **ChallengeResult** aus dem Controller zurück:

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

Dies bewirkt, dass die Middleware eine 302 (Found)-Antwort zurückgibt, mit der zum Authentifizierungsendpunkt weitergeleitet wird.

## Benutzeranmeldesitzung

Wie bereits erwähnt, schreibt die Middleware für die Cookie-Authentifizierung die Benutzeransprüche in ein Cookie, wenn sich der Benutzer zum ersten Mal anmeldet. Danach werden HTTP-Anforderungen authentifiziert, indem das Cookie gelesen wird.

Standardmäßig schreibt die Cookie-Middleware ein [Sitzungscookie][session-cookie], das gelöscht wird, sobald der Browser geschlossen wird. Wenn der Benutzer das nächste Mal die Seite besucht, ist eine erneute Anmeldung erforderlich. Wenn Sie jedoch **IsPersistent** auf „true“ im **ChallengeResult** festlegen, schreibt die Middleware ein permanentes Cookie, sodass der Benutzer nach dem Schließen des Browsers angemeldet bleibt. Unter [Cookieoptionen festlegen][cookie-options] können Sie den Ablauf der Cookies konfigurieren. Beständige Cookies sind angenehmer für den Benutzer, aber möglicherweise ungeeignet für einige Anwendungen (z. B. Onlinebanking-Anwendung), bei denen sich der Benutzer jedes Mal erneut anmelden soll.

## Informationen über die OpenID Connect-Middleware

Die OpenID Connect-Middleware in ASP.NET verbirgt die meisten Protokolldetails. Dieser Abschnitt enthält einige Hinweise zur Implementierung, die für das Verständnis des Protokollflusses nützlich sein können.

Zunächst sehen wir uns den Authentifizierungsablauf in Bezug auf ASP.NET an (dabei ignorieren wir die Details des Protokollflusses von OIDC zwischen der App und Azure AD). Im folgenden Diagramm wird der Prozess veranschaulicht.

![Anmeldefluss](media/guidance-multitenant-identity/sign-in-flow.png)

In diesem Diagramm gibt es zwei MVC-Controller. Der Kontocontroller verarbeitet Anmeldeanforderungen, und der Home-Controller verarbeitet die Homepage.

So verläuft der Authentifizierungsprozess:

1. Der Benutzer klickt auf die Schaltfläche „Anmelden“, und der Browser sendet eine GET-Anforderung. Beispiel: `GET /Account/SignIn/`.
2. Der Kontocontroller gibt ein `ChallengeResult` zurück
3. Die OIDC-Middleware gibt eine HTTP 302-Antwort zurück, die zu Azure AD umleitet.
4. Der Browser sendet die Authentifizierungsanforderung an Azure AD.
5. Der Benutzer meldet sich bei Azure AD an, und Azure AD sendet eine Authentifizierungsantwort zurück.
6. Die OIDC-Middleware erstellt einen Anforderungsprinzipal und übergibt diesen der Middleware zur Cookie-Authentifizierung.
7. Die Cookie-Middleware serialisiert den Anforderungsprinzipal und setzt ein Cookie.
8. Die OIDC-Middleware leitet an die Rückruf-URL der Anwendung zurück.
10. Der Browser folgt der Umleitung, und sendet das Cookie in der Anforderung.
11. Die Cookie-Middleware deserialisiert das Cookie in einen Anforderungsprinzipal und setzt `HttpContext.User` mit dem Anforderungsprinzipal gleich. Die Anforderung wird an einen MVC-Controller weitergeleitet.

### Authentifizierungsticket

Wenn die Authentifizierung erfolgreich war, erstellt die OIDC-Middleware ein Authentifizierungsticket. Dieses enthält einen Anforderungsprinzipal, der die Ansprüche des Benutzers enthält. Sie können innerhalb der Ereignisse **AuthenticationValidated** oder **TicketReceived** auf das Ticket zugreifen.

> [AZURE.NOTE] Bis zum Abschluss des gesamten Authentifizierungsablaufs enthält `HttpContext.User` immer noch einen anonymen Prinzipal, _nicht_ den authentifizierten Benutzer. Der anonyme Prinzipal hat eine leere Auflistung von Ansprüchen. Wenn die Authentifizierung abgeschlossen ist, und die App weiterleitet, deserialisiert die Cookie-Middleware das Authentifizierungscookie und legt `HttpContext.User` auf einen Anspruchsprinzipal fest, der den authentifizierten Benutzer darstellt.

### Authentifizierungsereignisse

Während des Authentifizierungsvorgangs löst die OpenID Connect-Middleware eine Reihe von Ereignissen aus:

- **RedirectToAuthenticationEndpoint**. Wird aufgerufen, unmittelbar bevor die Middleware an den Authentifizierungsendpunkt umleitet. Sie können dieses Ereignis verwenden, um die Umleitungs-URL zu ändern, z. B. um Anforderungsparameter hinzuzufügen. Ein Beispiel finden Sie unter [Aufforderung zur Administratorzustimmung hinzufügen](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt).

- **AuthorizationResponseReceived** Wird aufgerufen, nachdem die Middleware die Authentifizierungsantwort vom Identitätsanbieter (IDP) empfangen hat, jedoch noch vor der Überprüfung der Antwort durch die Middleware.

- **AuthorizationCodeReceived** Wird mit dem Autorisierungscode aufgerufen.

- **TokenResponseReceived** Wird aufgerufen, nachdem die Middleware ein Zugriffstoken vom Identitätsanbieter erhalten hat. Gilt nur für die Autorisierung des Codeflusses.

- **AuthenticationValidated**. Wird aufgerufen, nachdem die Middleware das ID-Token überprüft hat. Zu diesem Zeitpunkt besitzt die Anwendung bereits eine Sammlung überprüfter Ansprüche über den Benutzer. Sie können dieses Ereignis verwenden, um zusätzliche Überprüfungen der Ansprüche durchzuführen oder diese zu transformieren. Siehe [Arbeiten mit Ansprüchen](guidance-multitenant-identity-claims.md).

- **UserInformationReceived**. Wird aufgerufen, wenn die Middleware ein Benutzerprofil vom Userinfo-Endpoint erhält. Gilt nur für die Autorisierung eines Codeflusses, und auch nur dann, wenn die Middleware-Optionen `GetClaimsFromUserInfoEndpoint = true` entsprechen.

- **TicketReceived**. Wird aufgerufen, wenn die Authentifizierung abgeschlossen ist. Dies ist das letzte Ereignis, vorausgesetzt, die Authentifizierung wurde erfolgreich ausgeführt. Nachdem dieses Ereignis ausgeführt wurde, wird der Benutzer in der App angemeldet.

- **AuthenticationFailed** Wird aufgerufen, wenn die Authentifizierung fehlschlägt. Verwenden Sie dieses Ereignis, um Authentifizierungsfehler zu behandeln, z. B. durch Umleiten auf eine Fehlerseite.

Um Rückrufe für diese Ereignisse zu erhalten, richten Sie die Option **Ereignisse** auf der Middleware ein. Es gibt zwei Möglichkeiten, die Ereignishandler zu bestimmen: Inline mit Lambda-Ausdrücken oder in einer Klasse, die von **OpenIdConnectEvents** abgeleitet ist.

Inline mit Lambda-Ausdrücken:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

Ableiten von **OpenIdConnectEvents**:

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

Der zweite Ansatz empfiehlt sich, wenn Ihre Ereignisrückrufe wesentliche Logik enthalten, und diese damit Ihre Startklasse nicht überlasten. Unsere Referenzimplementierung verwendet diesen Ansatz; siehe [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs).

### OpenID Connect-Endpunkte

Azure AD unterstützt [OpenID Connect Discovery](https://openid.net/specs/openid-connect-discovery-1_0.html), wo der Identitätsanbieter (IDP) ein JSON-Metadatendokument aus einem [bekannten Endpunkt](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig) zurückgibt. Das Metadatendokument enthält Informationen wie z. B.:

-	Die Authentifizierungsendpunkt-URL An diesem Punkt leitet die App um, damit der Benutzer authentifiziert werden kann.
-	Die URL des Endpunkts „Sitzung beenden“, an dem die App den Benutzers abmeldet.
-	Die URL für die Signaturschlüssel. Der Client verwendet diese, um die OIDC-Token zu überprüfen, die er vom Indentitätsanbieter erhält.

Die OIDC-Middleware weiß automatisch, wie diese Metadaten abzurufen sind. Richten Sie die Option **Authority** in der Middleware ein, und die Middleware erstellt die URL für die Metadaten. (Sie können die Metadaten-URL überschreiben, indem Sie die Option **MetadataAddress** einrichten.)

### OpenID Connect-Abläufe

Standardmäßig verwendet die OIDC-Middleware Hybrid flow mit POST-Formular-Antwortmodus.

-	_Hybrid flow_ bedeutet, dass der Client ein ID-Token und einen Autorisierungscode im gleichen Roundtrip zum Autorisierungsserver leitet.
-	_Formular POST-Reaktionsmodus_ bedeutet, dass der Autorisierungsserver eine HTTP POST-Anforderung verwendet, um das ID-Token und den Autorisierungscode an die App zu senden. Die Werte sind Formular-URL-codiert (content type = „application/X-www-form-urlencoded“).

Wenn die OIDC-Middleware an den Autorisierungsendpunkt umleitet, enthält die Umleitungs-URL alle Abfragezeichenfolgen-Parameter, die OIDC benötigt. Für den Hybriddatenfluss:

-	client\_id Dieser Wert wird in der Option **ClientId** festgelegt
-	scope = „Openid-Profil“, d. h., es handelt sich um eine OIDC-Anforderung, und gewünscht ist das Profil des Benutzers
-	response\_type = „code id\_token“, spezifiziert einen Hybriddatenfluss
-	response\_mode = „form\_post“, spezifiziert die POST-Formular-Antwort

Um einen anderen Datenfluss anzugeben, bestimmen Sie die Eigenschaft **ResponseType** in den Optionen. Zum Beispiel:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->