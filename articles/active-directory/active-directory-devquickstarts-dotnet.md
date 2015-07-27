<properties
	pageTitle="Erste Schritte in Azure AD .NET | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine .NET Windows Desktop-Anwendung erstellt wird, die sich für die Anmeldung in Azure AD integriert und über OAuth durch Azure AD geschützte APIs aufruft."
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


# Integrieren von Azure AD in einer Windows-Desktop-WPF-Anwendung

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Bei der Entwicklung einer Desktopanwendung ist es für Sie mit Azure AD einfach und problemlos möglich, Ihre Benutzer über deren Active Directory-Konten zu authentifizieren. Außerdem ermöglicht es in Ihren Anwendungen die sichere Nutzung jeder durch Azure AD geschützten Web-API wie der Azure-API oder Office 365-APIs.

Für systemeigene .NET- Clients, die auf geschützte Ressourcen zugreifen müssen, bietet Azure AD die Active Directory-Authentifizierungsbibliothek (ADAL). Die einzige Aufgabe von ADAL besteht darin, Ihrer Anwendung das Abrufen von Zugriffstoken zu erleichtern. Um Ihnen zu zeigen, wie einfach das geht, wollen wir nun eine .NET WPF-Anwendung mit einer Aufgabenliste entwickeln, die folgende Aktionen ausführt:

-	Abrufen der Zugriffstoken zum Aufrufen der Azure AD Graph-API mit dem [OAuth 2.0-Authentifizierungsprotokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Durchsuchen eines Verzeichnisses nach Benutzern mit einem bestimmten Aliasnamen
-	Abmelden von Benutzern

Zur Entwicklung der vollständigen Arbeitsanwendung müssen Sie folgende Schritte ausführen:

2. Registrieren Ihrer Anwendung bei Azure AD
3. Installieren und Konfigurieren von ADAL
5. Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

Beginnen Sie, indem Sie [das Anwendungsgerüst](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) oder [das vollständige Beispiel herunterladen](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Außerdem benötigen Sie einen Azure AD-Mandanten, unter dem Sie Benutzer erstellen und Ihre Anwendung registrieren können. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](active-directory-howto-tenant.md).

## *1. Registrieren der Anwendung DirectorySearcher*
Damit Ihre Anwendung Tokens abrufen kann, müssen Sie sie zunächst beim Azure AD-Mandanten registrieren und ihr die Berechtigung für den Zugriff auf die Azure AD Graph-API erteilen:

-	Melden Sie sich beim Azure-Verwaltungsportal an.
-	Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
-	Wählen Sie den Mandanten aus, unter dem die Anwendung registriert werden soll.
-	Klicken Sie auf die Registerkarte **Anwendungen** und dann in der unteren Schublade auf **Hinzufügen**.
-	Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue **systemeigene Clientanwendung**.
    -	Am **Namen** der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können.
    -	Die **Umleitungs-URI** ist eine Kombination aus einem Schema und einer Zeichenfolge, die Azure AD für die Rückgabe der Tokenantworten verwendet. Geben Sie einen für Ihre Anwendung spezifischen Wert ein, z. B. `http://DirectorySearcher`.
-	Nach Abschluss der Registrierung weist AAD Ihrer Anwendung eine eindeutige Client-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten, weswegen Sie ihn aus der Registerkarte **Konfigurieren** kopieren sollten.
- Suchen Sie ebenso auf der Registerkarte **Konfigurieren** den Abschnitt „Berechtigungen für andere Anwendungen“. Fügen Sie für die Anwendung „Azure Active Directory“ unter **Delegierte Berechtigungen** die Berechtigung **Zugriff auf das Verzeichnis Ihrer Organisation** hinzu. Mit dieser Berechtigung kann die Anwendung die Graph-API nach Benutzern abfragen.

## *2. Installieren und Konfigurieren von ADAL*
Nachdem Sie nun eine Anwendung in Azure AD erstellt haben, können Sie ADAL installieren und Ihren identitätsbezogenen Code schreiben. Damit ADAL mit Azure AD kommunizieren kann, müssen Sie ihm einige Informationen über die Registrierung Ihrer Anwendung bereitstellen. Fügen Sie ADAL dazu zunächst über die Paket-Manager-Konsole zum Projekt DirectorySearcher hinzu.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	Öffnen Sie `app.config` im Projekt DirectorySearcher. Ersetzen Sie die Werte der Elemente im Abschnitt `<appSettings>` durch die Werte, die Sie im Azure-Verwaltungsportal eingegeben haben. Wenn die ADAL in Ihrem Code verwendet wird, verweist er auf diese Werte.
    -	`ida:Tenant` ist die Domäne Ihres Azure AD-Mandanten, z. B. „contoso.onmicrosoft.com“.
    -	`ida:ClientId` ist die Client-ID Ihrer Anwendung, die Sie aus dem Portal kopiert haben.
    -	`ida:RedirectUri` ist die Umleitungs-URL, die Sie im Portal registriert haben.

## *3. Verwenden von ADAL zum Abrufen von Tokens aus AAD*
Das Grundprinzip von ADAL ist wie folgt: Wann immer Ihre Anwendung ein Zugriffstoken benötigt, ruft sie `authContext.AcquireToken(...)` auf, und ADAL erledigt alles Weitere.

-	Öffnen Sie im Projekt `DirectorySearcher` die Datei `MainWindow.xaml.cs`, und suchen Sie die Methode `MainWindow()`. Der erste Schritt ist die Initialisierung des `AuthenticationContext` Ihrer Anwendung – der primären Klasse von ADAL. Dort übergeben Sie ADAL die zur Kommunikation mit Azure AD notwendigen Koordinaten und weisen es an, wie Token zwischengespeichert werden sollen.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());
    ...
}
```

- Suchen Sie jetzt die Methode `Search(...)`, die aufgerufen wird, wenn der Benutzer auf die Suchschaltfläche in der Benutzeroberfläche der Anwendung klickt. Diese Methode übergibt der Azure AD Graph-API eine GET-Anforderung für die Suche nach Benutzern, deren UPNs mit dem angegebenen Suchbegriff beginnen. Für die Abfrage der Graph-API müssen Sie dem `Authorization`-Header der Anforderung jedoch ein Zugriffstoken hinzufügen – und hier kommt ADAL ins Spiel.

```C#
private void Search(object sender, RoutedEventArgs e)
{
    ...

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = authContext.AcquireToken(graphResourceId, clientId, redirectUri);
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Wenn Ihre Anwendung mit einem `AcquireToken(...)`-Aufruf ein Token anfordert, versucht ADAL ein Token zurückzugeben, ohne den Benutzer nach seinen Anmeldeinformationen zu fragen. Stellt ADAL fest, dass sich der Benutzer zum Abrufen eines Tokens anmelden muss, zeigt es einen Anmeldedialog an, erfasst die Anmeldeinformationen des Benutzers und gibt nach erfolgreicher Authentifizierung ein Token zurück. Wenn ADAL aus welchem Grund auch immer kein Token zurückgeben kann, löst es eine `AdalException` aus.
- Beachten Sie, dass das Objekt `AuthenticationResult` ein `UserInfo`-Objekt enthält, mit dem von Ihrer Anwendung benötigte Informationen erfasst werden können. In DirectorySearcher wird `UserInfo` zum Einstellen der Benutzer-ID in der Benutzeroberfläche der Anwendung verwendet.

- Nachdem der Benutzer auf die Abmeldeschaltfläche geklickt hat, soll sichergestellt sein, dass er beim nächsten Aufruf von `AcquireToken(...)` wieder zur Anmeldung aufgefordert wird. In ADAL muss dazu lediglich der Tokencache gelöscht werden:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- Betätigt der Benutzer die Abmeldeschaltfläche hingegen nicht, so sollte die Benutzersitzung für die nächste Ausführung von DirectorySearcher erhalten bleiben. Beim Starten der Anwendung kann der Tokencache von ADAL nach einem vorhandenen Token durchsucht und die Benutzeroberfläche entsprechend aktualisiert werden. Rufen Sie zurück in `MainWindow()` `AcquireToken(...)` ein weiteres Mal auf, dieses Mal mit dem Parameter `PromptBehavior.Never`. `PromptBehavior.Never` informiert ADAL, dass der Benutzer nicht zur Anmeldung aufgefordert werden soll, dass ADAL vielmehr eine Ausnahme auslösen soll, wenn es kein Token zurückgeben kann.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = authContext.AcquireToken(graphResourceId, clientId, redirectUri, PromptBehavior.Never);
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Glückwunsch! Sie haben nun eine funktionierende .NET WPF-Anwendung, die Benutzer authentifizieren, Web-APIs über OAuth 2.0 sicher aufrufen und grundlegende Benutzerinformationen abfragen kann. Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen. Führen Sie danach Ihre DirectorySearcher-Anwendung aus, und melden Sie sich unter einem dieser Benutzer an. Suchen Sie anhand des UPN nach anderen Benutzern. Schließen Sie die Anwendung, und führen Sie sie erneut aus. Wie Sie sehen, bleibt die Benutzersitzung erhalten. Melden Sie sich ab und unter einem anderen Benutzer wieder an.

ADAL erleichtert Ihnen die Integration all dieser allgemeinen Identitätsfunktionen in Ihrer Anwendung. Es übernimmt die unangenehmen Verwaltungsarbeiten für Sie – die Cacheverwaltung, die Unterstützung des OAuth-Protokolls, die Anzeige einer Anmeldeschnittstelle für den Benutzer, die Aktualisierung abgelaufener Tokens und vieles mehr. Das Einzige, womit Sie sich noch beschäftigen müssen, ist der API-Aufruf `authContext.AcquireToken(...)`.

Als Referenz stellen wir [hier](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip) das vollständige Beispiel (ohne Ihre Konfigurationswerte) bereit. Sie können sich nun weiteren Szenarien zuwenden. Wie wäre es zum Beispiel mit Folgendem:

[Schützen einer .NET Web-API mit Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

Sehen Sie sich auch die folgenden Ressourcen an: – [AzureAD-Beispiele auf GitHub >>](https://github.com/AzureAdSamples) – [CloudIdentity.com >>](https://cloudidentity.com) – Azure AD-Dokumentation auf [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=July15_HO3-->