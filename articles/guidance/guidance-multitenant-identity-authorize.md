<properties
   pageTitle="Autorisierung in mehrmandantenfähigen Anwendungen | Microsoft Azure"
   description="Informationen zum Durchführen der Autorisierung in einer mehrmandantenfähigen Anwendung"
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

# Autorisierung in mehrmandantenfähigen Anwendungen

Dieser Artikel ist [Teil einer Reihe]. Es gibt auch eine vollständige [Beispielanwendung], die diese Reihe begleitet.

In diesem Artikel betrachten wir zwei allgemeine Vorgehensweisen für die Autorisierung.

-	**Rollenbasierte Autorisierung**. Autorisieren einer Aktion basierend auf den Rollen, die einem Benutzer zugewiesen sind. Einige Aktionen erfordern z. B. eine Administratorrolle.
-	**Ressourcenbasierte Autorisierung**. Autorisieren eine Aktion basierend auf einer bestimmten Ressource. Jede Ressource hat beispielsweise einen Besitzer. Der Besitzer kann die Ressource löschen, andere Benutzer können das nicht.

In einer Standard-App wird eine Kombination beider Varianten verwendet. Um beispielsweise eine Ressource zu löschen, muss der Benutzer Besitzer _oder_ Administrator der Ressource sein.

## Rollenbasierte Autorisierung

In der [Tailspin-Anwendung „Surveys“][Tailspin] gibt es die folgenden Rollen:

- Administrator. Kann alle Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen auf alle Umfragen anwenden, die zu diesem Mandanten gehören.
- Creator (Ersteller). Kann neue Umfragen erstellen.
- Reader (Leser). Kann alle Umfragen lesen, die zu diesem Mandanten gehören.

Rollen gelten für _Benutzer_ der Anwendung. In der Anwendung „Surveys“ ist ein Benutzer entweder Administrator, Ersteller oder Leser.

Eine Erläuterung der Definition und Verwaltung von Rollen finden Sie unter [Anwendungsrollen].

Unabhängig davon, wie Sie die Rollen verwalten, ist Ihr Autorisierungscode ähnlich. In ASP.NET Core 1.0 wurde eine Abstraktion namens _Autorisierungsrichtlinien_ eingeführt. Mithilfe dieses Features definieren Sie Autorisierungsrichtlinien im Code und wenden anschließend diese Richtlinien auf Controlleraktionen an. Die Richtlinie ist vom Controller entkoppelt.

### Erstellen von Richtlinien

Um eine Richtlinie zu definieren, erstellen Sie zuerst eine Klasse, die `IAuthorizationRequirement` implementiert. Am einfachsten erfolgt diese über eine Ableitung von `AuthorizationHandler`. Untersuchen Sie in der `Handle`-Methode die relevanten Ansprüche.

Es folgt ein Beispiel aus der Tailspin-Anwendung „Surveys“:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Siehe [SurveyCreatorRequirement.cs].

Diese Klasse definiert die Anforderung für einen Benutzer, eine neue Umfrage zu erstellen. Der Benutzer muss die Rolle „SurveyAdmin“ oder „SurveyCreator“ haben.

Definieren Sie in der „startup“-Klasse eine benannte Richtlinie, die eine oder mehrere Anforderungen enthält. Wenn mehrere Anforderungen vorhanden sind, muss der Benutzer _alle_ Anforderungen erfüllen, um autorisiert zu werden. Der folgende Code definiert zwei Richtlinien:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Siehe [Startup.cs].

Dieser Code legt auch das Authentifizierungsschema fest, das ASP.NET anweist, welche Authentifizierungsmiddleware ausgeführt werden soll, wenn die Autorisierung misslingt. In diesem Fall geben wir die Middleware für die Cookieauthentifizierung an, da diese den Benutzer zu einer Seite des Typs „Verboten“ umleiten kann. Der Speicherort der Seite „Verboten“ wird in der „AccessDeniedPath“-Option für die Cookiemiddleware festgelegt. Siehe [Konfigurieren der Authentifizierungsmiddleware].

### Autorisieren von Controlleraktionen

Um eine Aktion in einem MVC-Controller zu autorisieren, legen Sie abschließend die Richtlinie im `Authorize`-Attribut fest:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

In früheren Versionen von ASP.NET wird die **Roles**-Eigenschaft für dieses Attribut festgelegt:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Dies wird in ASP.NET Core 1.0 noch immer unterstützt, doch gibt es verglichen mit Autorisierungsrichtlinien einige Nachteile:

-	Es wird von einem bestimmten Anspruchstyp ausgegangen. Mit Richtlinien können alle Anspruchstypen überprüft werden. Rollen sind lediglich ein Anspruchstyp.
-	Der Rollenname ist im Attribut hartcodiert. Bei Richtlinien befindet sich die Autorisierungslogik zentral an einem Ort, wodurch das Aktualisieren oder Laden von Daten aus Konfigurationseinstellungen erleichtert wird.
-	Richtlinien ermöglichen komplexere Autorisierungsentscheidungen (z. B. Alter > = 21), die mittels einer einfachen Rollenmitgliedschaft nicht ausgedrückt werden können.

## Ressourcenbasierte Autorisierung

_Ressourcenbasierte Autorisierung_ erfolgt, wenn die Autorisierung von einer bestimmten Ressource abhängig ist, die von einem Vorgang betroffen ist. In der Tailspin-Anwendung „Surveys“ hat jede Umfrage einen Besitzer und 0 bis n Teilnehmer.

-	Der Besitzer kann die Umfrage lesen, aktualisieren, löschen, veröffentlichen und ihre Veröffentlichung aufheben.
-	Der Besitzer kann der Umfrage Teilnehmer zuweisen.
-	Teilnehmer können die Umfrage lesen und aktualisieren.

Beachten Sie, dass „Besitzer“ und „Teilnehmer“ keine Anwendungsrollen sind, sondern umfragebezogen in der Anwendungsdatenbank gespeichert werden. Um z. B. zu überprüfen, ob ein Benutzer eine Umfrage löschen kann, überprüft die App, ob der Benutzer der Besitzer der Umfrage ist.

Implementieren Sie in ASP.NET Core 1.0 die ressourcenbasierte Autorisierung mittels Ableitung von **AuthorizationHandler** und Überschreibung der **Handle**-Methode.

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Beachten Sie, dass diese Klasse für Umfrageobjekte stark typisiert ist. Registrieren Sie die Klasse für DI beim Start:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Verwenden Sie zur Durchführung von Autorisierungsüberprüfungen die **IAuthorizationService**-Schnittstelle, die Sie in Ihre Controller einfügen können. Der folgende Code überprüft, ob ein Benutzer eine Umfrage lesen kann:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Da wir ein `Survey`-Objekt übergeben, wird durch diesen Aufruf der `SurveyAuthorizationHandler` aufgerufen.

Es empfiehlt sich, in Ihrem Autorisierungscode alle rollen- und ressourcenbasierten Berechtigungen des Benutzers zusammenzuführen und anschließend diesen Berechtigungssatz mit dem gewünschten Vorgang abzugleichen. Es folgt ein Beispiel aus der App „Surveys“. Die Anwendung definiert mehrere Typen von Berechtigungen:

- Admin
- Contributor (Teilnehmer)
- Creator (Ersteller)
- Owner (Besitzer)
- Reader (Leser)

Die Anwendung definiert außerdem eine Reihe möglicher Aktionen für Umfragen:

- Erstellen
- Lesen
- Aktualisieren
- Löschen
- Veröffentlichen
- Veröffentlichung aufheben

Der folgende Code erstellt eine Liste der Berechtigungen für einen bestimmten Benutzer und eine Umfrage. Beachten Sie, dass dieser Code sowohl die App-Rollen des Benutzers als auch die Felder für Besitzer/Teilnehmer in der Umfrage untersucht.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Siehe [SurveyAuthorizationHandler.cs].

Bei einer mehrmandantenfähigen Anwendung müssen Sie sicherstellen, dass Berechtigungen nicht in die Daten anderer Mandanten gelangen. In der App „Surveys“ ist die Berechtigung „Teilnehmer“ mandantenübergreifend erlaubt, d. h. Sie können jemand aus einem anderen Mandanten als Teilnehmer zuweisen. Die anderen Berechtigungstypen sind auf Ressourcen beschränkt, die zum Mandanten des jeweiligen Benutzers gehören. Deshalb überprüft der Code die Mandanten-ID, bevor diese Berechtigungstypen gewährt werden. (Das Feld `TenantId` wird zugewiesen, wenn die Umfrage erstellt wird.)

Im nächste Schritt wird der Vorgang (Lesen, Aktualisieren, Löschen usw.) mit den Berechtigungen verglichen. Die App „Surveys“ implementiert diesen Schritt mithilfe einer Nachschlagetabelle für Funktionen:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

## Zusätzliche Ressourcen

- [Ressourcenbasierte Autorisierung](https://docs.asp.net/en/latest/security/authorization/resourcebased.html)
- [Benutzerdefinierte richtlinienbasierte Autorisierung](https://docs.asp.net/en/latest/security/authorization/policies.html)

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[Teil einer Reihe]: guidance-multitenant-identity.md
[Anwendungsrollen]: guidance-multitenant-identity-app-roles.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Konfigurieren der Authentifizierungsmiddleware]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->