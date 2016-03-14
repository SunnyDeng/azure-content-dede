<properties
   pageTitle="Arbeiten mit anspruchsbasierten Identitäten in mehrmandantenfähigen Anwendungen | Microsoft Azure"
   description="Informationen zum Verwenden von Ansprüchen für die Ausstellerüberprüfung und Autorisierung"
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

# Arbeiten mit anspruchsbasierten Identitäten in mehrmandantenfähigen Anwendungen

Dieser Artikel ist [Teil einer Reihe]. Es gibt auch eine vollständige [Beispielanwendung], die diese Reihe begleitet.

## Ansprüche in Azure AD

Wenn sich ein Benutzer anmeldet, sendet Azure AD ein ID-Token, das einen Satz mit Ansprüchen des Benutzers enthält. Ein Anspruch ist eine einfache Information, die als Schlüssel-Wert-Paar ausgedrückt wird. Beispiel: `email`=`bob@contoso.com`. Ansprüche haben einen Aussteller (in diesem Fall Azure AD), bei dem es sich um die Entität handelt, die den Benutzer authentifiziert und die Ansprüche erstellt. Sie vertrauen den Ansprüchen, da Sie dem Aussteller vertrauen. (Wenn Sie umgekehrt dem Aussteller nicht trauen, vertrauen Sie nicht den Ansprüchen!)

Allgemeines:

1.	Der Benutzer wird authentifiziert.
2.	Der Identitätsanbieter sendet einen Satz von Ansprüchen.
3.	Die App normalisiert oder ergänzt die Ansprüche (optional).
4.	Die App verwendet die Ansprüche, um Autorisierungsentscheidungen zu treffen.

In OpenID Connect wird der Satz von Ansprüchen, den Sie erhalten, vom [scope]-Parameter der Authentifizierungsanforderung bestimmt. Azure AD stellt jedoch einen begrenzten Satz von Ansprüchen über OpenID Connect aus. Siehe [Unterstützte Token und Anspruchstypen]. Wenn Sie weitere Informationen zum Benutzer wünschen, müssen Sie die Azure AD Graph-API verwenden.

Hier sind einige der Ansprüche aus AAD, die eine App in der Regel interessieren:

Typ des Anspruchs im ID-Token |	Beschreibung
-----------------------|--------------
aud | Der Empfänger, für den das Token ausgestellt wurde. Dies ist die Client-ID der Anwendung. Im Allgemeinen müssen Sie sich nicht um diesen Anspruch kümmern, da er von der Middleware automatisch überprüft wird. Beispiel: `"91464657-d17a-4327-91f3-2ed99386406f"`
groups | Eine Liste mit AAD-Gruppen, deren Mitglied der Benutzer ist. Beispiel: `["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
iss | Der [Aussteller] des OIDC-Tokens. Beispiel: `https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
name | Anzeigename des Benutzers. Beispiel: `"Alice A."`
oid | Der Objektbezeichner des Benutzers im AAD. Dieser Wert ist der unveränderliche und nicht wiederverwendbare Bezeichner des Benutzers. Verwenden Sie diesen Wert, nicht „email“, als eindeutigen Bezeichner für Benutzer, denn E-Mail-Adressen können sich ändern. Wenn Sie die Azure AD Graph-API in Ihrer App verwenden, ist die Objekt-ID der Wert, der zum Abfragen von Profilinformationen verwendet wird. Beispiel: `"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
roles | Eine Liste der App-Rollen des Benutzers. Beispiel: `["SurveyCreator"]`
tid | Mandanten-ID. Dieser Wert ist ein eindeutiger Bezeichner für den Mandanten in Azure AD. Beispiel: `"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique\_name | Ein vom Menschen lesbarer Anzeigename des Benutzers. Beispiel: `"alice@contoso.com"`
upn | Benutzerprinzipalname. Beispiel: `"alice@contoso.com"`

Diese Tabelle enthält die Anspruchstypen, wie sie im ID-Token angezeigt werden. In ASP.NET Core 1.0 konvertiert die OpenID Connect-Middleware einige der Anspruchstypen, wenn die „Claims“-Auflistung für den Benutzerprinzipal aufgefüllt wird:

-	oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`
-	tid > `http://schemas.microsoft.com/identity/claims/tenantid`
-	unique\_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-	upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## Transformationen von Ansprüchen

Während des Authentifizierungsvorgangs möchten Sie ggf. die Ansprüche ändern, die Sie vom Identitätsanbieter erhalten. In ASP.NET Core 1.0 können Sie die Anspruchstransformation im **AuthenticationValidated**-Ereignis der OpenID Connect-Middleware durchführen. (Siehe [Authentifizierungsereignisse].)

Alle Ansprüche, die Sie während des **AuthenticationValidated**-Ereignisses hinzufügen, werden im Authentifizierungscookie der Sitzung gespeichert. Sie werden nicht per Push zurück an Azure AD übertragen.

Hier einige Beispiele für die Transformation von Ansprüchen:

-	**Normalisierung von Ansprüchen** oder Vereinheitlichung benutzerbezogener Ansprüche. Dies ist besonders wichtig, wenn Sie Ansprüche von mehreren Identitätsanbietern erhalten, die ggf. unterschiedliche Anspruchstypen für ähnliche Informationen verwenden. Beispielsweise sendet Azure AD einen „upn“-Anspruch, der die E-Mail-Adresse des Benutzers enthält. Andere Identitätsanbieter senden ggf. einen „email“-Anspruch. Der folgende Code konvertiert den „upn“-Anspruch in einen „email“-Anspruch.

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Fügen Sie **Standardwertanspruchswerte** für Ansprüche hinzu, die nicht vorhanden sind, um z. B. einen Benutzer einer Standardrolle zuzuweisen. In einigen Fällen kann dies die Autorisierungslogik vereinfachen.
- Fügen Sie **benutzerdefinierte Anspruchstypen** mit anwendungsspezifischen Informationen zum Benutzer hinzu. Beispielsweise können Sie bestimmte Informationen zum Benutzer in einer Datenbank speichern. Sie können dem Authentifizierungsticket einen benutzerdefinierten Anspruch mit diesen Informationen hinzufügen. Der Anspruch wird in einem Cookie gespeichert. Daher müssen Sie es pro Anmeldesitzung nur einmal aus der Datenbank abrufen. Andererseits möchten Sie auch übermäßig große Cookies vermeiden, sodass Sie einen Kompromiss zwischen Cookiegröße und Datenbankabfragen finden müssen.   

Nach Abschluss des Authentifizierungsvorgangs stehen die Ansprüche in `HttpContext.User` zur Verfügung. An diesem Punkt sollten Sie sie als schreibgeschützte Auflistung behandeln, die Sie für Autorisierungsentscheidungen verwenden.

## Überprüfung des Ausstellers
In OpenID Connect (OIDC) identifiziert der Ausstelleranspruch (iss) den Identitätsanbieter, der das ID-Token ausgestellt hat. Im Rahmen des OIDC-Authentifizierungsvorgangs muss überprüft werden, ob der Ausstelleranspruch dem tatsächlichen Aussteller entspricht. Dies übernimmt die OIDC-Middleware für Sie.

In Azure AD ist der „issuer“-Wert pro AD-Mandant eindeutig (`https://sts.windows.net/<tenantID>`). Deshalb sollte eine Anwendung eine zusätzliche Prüfung vornehmen, um sicherzustellen, dass der Aussteller einen Mandanten darstellt, der sich bei der App anmelden darf.

Bei einer Anwendung mit nur einem Mandanten können Sie prüfen, ob der Aussteller Ihr eigener Mandant ist. Diesen Schritt führt die OIDC-Middleware standardmäßig automatisch aus. Bei einer App mit mehreren Mandanten müssen Sie entsprechend den verschiedenen Mandanten mehrere Aussteller zulassen. Es folgt eine empfohlene Vorgehensweise:

-	Legen Sie in den Optionen der OIDC-Middleware **ValidateIssuer** auf „false“ fest. Dadurch wird die automatische Prüfung deaktiviert.
-	Wenn sich ein Mandant anmeldet, speichern Sie den Mandanten und den Aussteller in der Benutzerdatenbank.
-	Wenn sich ein Benutzer anmeldet, schlagen Sie den Aussteller in der Datenbank nach. Wenn der Aussteller nicht gefunden wird, bedeutet dies, dass sich dieser Mandant nicht registriert hat. Sie können ihn zu einer Registrierungsseite umleiten.
-  Sie können auch bestimmte Mandanten auf eine Negativliste setzen, z. B. Kunden, die ihr Abonnement nicht bezahlt haben.

Eine ausführlichere Beschreibung finden Sie unter [Registrierung und Onboarding von Mandanten].

## Verwenden von Ansprüchen für die Autorisierung

Mit Ansprüchen ist die Identität eines Benutzers nicht länger eine monolithische Entität. Für einen Benutzer können beispielsweise E-Mail-Adresse, Telefonnummer, Geburtsdatum, Geschlecht usw. hinterlegt sein. Der Identitätsanbieter des Benutzers speichert u. U. alle diese Informationen. Doch wenn Sie den Benutzer authentifizieren, erhalten Sie in der Regel eine Teilmenge davon als Ansprüche. In diesem Modell ist die Identität des Benutzers schlicht ein Bündel von Ansprüchen. Wenn Sie Autorisierungsentscheidungen für einen Benutzer treffen, suchen Sie nach bestimmten Sätzen von Ansprüchen. Die Frage „Darf Benutzer X die Aktion Y ausführen?“ wird schlussendlich zu „Verfügt Benutzer X über Anspruch Z?“.

Es folgen einige grundlegende Muster für die Überprüfung von Ansprüchen.

-  So überprüfen Sie, ob der Benutzer einen bestimmten Anspruch mit einem bestimmten Wert hat:

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Dieser Code prüft, ob der Benutzer einen Rollenanspruch mit dem Wert „Admin“ hat. Er verarbeitet ordnungsgemäß den Fall, bei dem der Benutzer keine oder mehrere Rollenansprüche hat.

    Die **ClaimTypes**-Klasse definiert Konstanten für häufig verwendete Anspruchstypen. Dennoch können Sie einen beliebigen Zeichenfolgenwert für den Anspruchstyp verwenden.

-	So rufen Sie einen einzelnen Wert für einen Anspruchstyp ab, wenn Sie mindestens einen Wert erwarten:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-	So rufen Sie alle Werte für einen Anspruchstyp ab:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Weitere Informationen zur Verwendung von Ansprüchen bei der Autorisierung finden Sie unter [Autorisierung].

## Zusätzliche Ressourcen

- [Anspruchsbasierte Autorisierung]


<!-- Links -->
[Teil einer Reihe]: guidance-multitenant-identity.md
[scope]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Unterstützte Token und Anspruchstypen]: ../active-directory/active-directory-token-and-claims.md
[Aussteller]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Authentifizierungsereignisse]: guidance-multitenant-identity-authenticate.md#authentication-events
[Registrierung und Onboarding von Mandanten]: guidance-multitenant-identity-signup.md
[Autorisierung]: guidance-multitenant-identity-authorize.md
[Anspruchsbasierte Autorisierung]: https://docs.asp.net/en/latest/security/authorization/claims.html
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->