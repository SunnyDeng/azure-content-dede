<properties
	pageTitle="Erste Schritte in Azure AD Windows Phone | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine Windows Phone-App erstellt wird, die sich für die Anmeldung in Azure AD integriert und über OAuth durch Azure AD geschützte APIs aufruft."
	services="active-directory"
	documentationCenter="windows"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="dastrock"/>



# Integrieren von Azure AD in einer Windows Phone-App

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Bei der Entwicklung einer Windows Phone-App können Sie Ihre Benutzer mit Azure AD einfach und problemlos über deren Active Directory-Konten authentifizieren. Außerdem ermöglicht es in Ihren Anwendungen die sichere Nutzung jeder durch Azure AD geschützten Web-API wie der Azure-API oder Office 365-APIs.

Für systemeigene .NET- Clients, die auf geschützte Ressourcen zugreifen müssen, bietet Azure AD die Active Directory-Authentifizierungsbibliothek (ADAL). Die einzige Aufgabe von ADAL besteht darin, Ihrer Anwendung das Abrufen von Zugriffstoken zu erleichtern. Wir wollen Ihnen nun zeigen, wie einfach das geht. Dazu erstellen wir die Windows Phone 8.1-App „Directory Searcher“ mit folgenden Funktionen:

-	Abrufen der Zugriffstoken zum Aufrufen der Azure AD Graph-API mit dem [OAuth 2.0-Authentifizierungsprotokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Durchsuchen eines Verzeichnisses nach Benutzern mit einem bestimmten UPN
-	Abmelden von Benutzern

Zur Entwicklung der vollständigen Arbeitsanwendung müssen Sie folgende Schritte ausführen:

2. Registrieren Ihrer Anwendung bei Azure AD
3. Installieren und Konfigurieren von ADAL
5. Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

Beginnen Sie, indem Sie [ein Projektgerüst](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) oder [das vollständige Beispiel herunterladen](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip). Bei beidem handelt es sich um eine Visual Studio 2013-Lösung. Außerdem benötigen Sie einen Azure AD-Mandanten, in dem Sie Benutzer erstellen und Ihre Anwendung registrieren können. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](active-directory-howto-tenant.md).

## *1. Registrieren der Anwendung DirectorySearcher*
Damit Ihre Anwendung Tokens abrufen kann, müssen Sie sie zunächst beim Azure AD-Mandanten registrieren und ihr die Berechtigung für den Zugriff auf die Azure AD Graph-API erteilen:

-	Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
-	Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
-	Wählen Sie den Mandanten aus, unter dem die Anwendung registriert werden soll.
-	Klicken Sie auf die Registerkarte **Anwendungen** und dann in der unteren Schublade auf **Hinzufügen**.
-	Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue **systemeigene Clientanwendung**.
    -	Am **Namen** der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können.
    -	Die **Umleitungs-URI** ist eine Kombination aus einem Schema und einer Zeichenfolge, die Azure AD für die Rückgabe der Tokenantworten verwendet. Geben Sie einen vorläufigen Platzhalterwert ein, zum Beispiel `http://DirectorySearcher`. Wir werden diesen Wert später ersetzen.
-	Nach Abschluss der Registrierung weist AAD Ihrer Anwendung eine eindeutige Client-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten, weswegen Sie ihn aus der Registerkarte **Konfigurieren** kopieren sollten.
- Suchen Sie ebenso auf der Registerkarte **Konfigurieren** den Abschnitt „Berechtigungen für andere Anwendungen“. Fügen Sie für die Anwendung „Azure Active Directory“ unter **Delegierte Berechtigungen** die Berechtigung **Zugriff auf das Verzeichnis Ihrer Organisation** hinzu. Mit dieser Berechtigung kann die Anwendung die Graph-API nach Benutzern abfragen.

## *2. Installieren und Konfigurieren von ADAL*
Nachdem Sie nun eine Anwendung in Azure AD erstellt haben, können Sie ADAL installieren und Ihren identitätsbezogenen Code schreiben. Damit ADAL mit Azure AD kommunizieren kann, müssen Sie ihm einige Informationen über die Registrierung Ihrer Anwendung bereitstellen. Fügen Sie ADAL dazu zunächst über die Paket-Manager-Konsole zum Projekt DirectorySearcher hinzu.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	Öffnen Sie `MainPage.xaml.cs` im Projekt DirectorySearcher. Ersetzen Sie die Werte im Abschnitt `Config Values` durch die Werte, die Sie im Azure-Portal eingegeben haben. Sobald Ihr Code ADAL verwendet, verweist er auf diese Werte.
    -	`tenant` ist die Domäne Ihres Azure AD-Mandanten, z. B. „contoso.onmicrosoft.com“.
    -	`clientId` ist die Client-ID Ihrer Anwendung, die Sie aus dem Portal kopiert haben.
-	Sie müssen nun die Callback-URI für Ihre Windows Phone-App ermitteln. Fügen Sie in dieser Zeile in der Methode `MainPage` einen Haltepunkt ein:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Führen Sie die Anwendung aus, und kopieren Sie bei Erreichen des Haltepunkts den Wert von `redirectUri`. Dies sollte ungefähr wie folgt aussehen:

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Ersetzen Sie zurück im Azure-Verwaltungsportal auf der Registerkarte **Konfigurieren** der Anwendung den Wert von **RedirectUri** durch diesen Wert.  

## *3. Verwenden von ADAL zum Abrufen von Tokens aus AAD*
Das Grundprinzip von ADAL ist wie folgt: Wann immer Ihre Anwendung ein Zugriffstoken benötigt, ruft sie `authContext.AcquireToken(…)` auf, und ADAL erledigt alles Weitere.

-	Der erste Schritt ist die Initialisierung des `AuthenticationContext` Ihrer Anwendung – der primären Klasse von ADAL. Dort übergeben Sie ADAL die zur Kommunikation mit Azure AD notwendigen Koordinaten und weisen es an, wie Token zwischengespeichert werden sollen.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Suchen Sie jetzt die Methode `Search(...)`, die aufgerufen wird, wenn der Benutzer auf die Suchschaltfläche in der Benutzeroberfläche der Anwendung klickt. Diese Methode übergibt der Azure AD Graph-API eine GET-Anforderung für die Suche nach Benutzern, deren UPNs mit dem angegebenen Suchbegriff beginnen. Für die Abfrage der Graph-API müssen Sie dem `Authorization`-Header der Anforderung jedoch ein Zugriffstoken hinzufügen – und hier kommt ADAL ins Spiel.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Wenn eine interaktive Authentifizierung erforderlich ist, zeigt ADAL über den Web Authentication Broker (WAB) und das [Fortsetzungsmodell](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) von Windows Phone die Azure AD-Anmeldeseite an. Bei der Anmeldung eines Benutzers muss Ihre Anwendung die Ergebnisse der WAB-Aktivität an ADAL übergeben. Dies ist so einfach wie die Implementierung der `ContinueWebAuthentication`-Schnittstelle:

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- An dieser Stelle wird das von ADAL an die Anwendung zurückgegebene `AuthenticationResult` benötigt. Hängen Sie im `QueryGraph(...)`-Callback an die GET-Anforderung im Autorisierungsheader das von Ihnen erworbene Zugriffstoken an:

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- Mit dem Objekt `AuthenticationResult` können Sie in Ihrer Anwendung auch Informationen zum Benutzer anzeigen. So zeigt das Ergebnis, in der `QueryGraph(...)`-Methode verwendet, die Benutzer-ID auf der Seite an:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Schließlich können Sie den Benutzer mit ADAL auch wieder von der Anwendung abmelden. Nachdem der Benutzer auf die Schaltfläche „Abmelden“ geklickt hat, soll der nächste Aufruf von `AcquireTokenSilentAsync(...)` fehlschlagen. In ADAL muss dazu lediglich der Tokencache gelöscht werden:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Glückwunsch! Sie haben nun eine funktionierende Windows Phone-App, die Benutzer authentifizieren, Web-APIs über OAuth 2.0 sicher aufrufen und grundlegende Benutzerinformationen abfragen kann. Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen. Führen Sie danach Ihre DirectorySearcher-Anwendung aus, und melden Sie sich unter einem dieser Benutzer an. Suchen Sie anhand des UPN nach anderen Benutzern. Schließen Sie die Anwendung, und führen Sie sie erneut aus. Wie Sie sehen, bleibt die Benutzersitzung erhalten. Melden Sie sich ab und unter einem anderen Benutzer wieder an.

ADAL erleichtert Ihnen die Integration all dieser allgemeinen Identitätsfunktionen in Ihrer Anwendung. Es übernimmt die unangenehmen Verwaltungsarbeiten für Sie – die Cacheverwaltung, die Unterstützung des OAuth-Protokolls, die Anzeige einer Anmeldeschnittstelle für den Benutzer, die Aktualisierung abgelaufener Tokens und vieles mehr. Das Einzige, womit Sie sich noch beschäftigen müssen, ist der API-Aufruf `authContext.AcquireToken*(…)`.

Als Referenz stellen wir [hier](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip) das vollständige Beispiel (ohne Ihre Konfigurationswerte) bereit. Sie können sich nun weiteren Identitätsszenarien zuwenden. Wie wäre es zum Beispiel mit Folgendem:

[Schützen einer .NET Web-API mit Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=Oct15_HO3-->