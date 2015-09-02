<properties
	pageTitle="App-Modell v2.0 | Microsoft Azure"
	description="Vorgehensweise beim Erstellen einer systemeigenen .NET-App, bei der sich Benutzer sowohl mit ihrem persönlichen Microsoft-Konto als auch ihrem Geschäfts- oder Schulkonto anmelden können."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 \(Vorschauversion\): Hinzufügen der Anmeldung zu einer Windows-Desktop-App

Mit dem App-Modell v2.0 können Sie schnell eine Authentifizierung zu Ihren Desktop-Apps hinzufügen, die sowohl persönliche Microsoft-Konten als auch Geschäfts- oder Schulkonten unterstützt. Dadurch kann Ihre App außerdem sicher mit einer Back-End-Web-API sowie einigen [Office 365-Unified-APIs](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) kommunizieren.

> [AZURE.NOTE]
	Diese Informationen gelten für App-Modell v2.0 \(öffentliche Vorschauversion\). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

Für [systemeigene .NET- Apps, die auf einem Gerät ausgeführt werden](active-directory-v2-flows.md#mobile-and-native-apps), bietet Azure AD die Active Directory-Authentifizierungsbibliothek \(ADAL\). Die einzige Aufgabe von ADAL besteht darin, Ihrer Anwendung das Abrufen von Token für den Aufruf von Webdiensten zu erleichtern. Um Ihnen zu zeigen, wie einfach das geht, wollen wir nun eine .NET-WPF-App mit einer Aufgabenliste entwickeln, die folgende Aktionen ausführt:

-	Anmelden von Benutzern und Abrufen von Zugriffstoken mit dem [OAuth 2.0-Authentifizierungsprotokoll](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
-	Sicheres Aufrufen eines Back-End-ToDoList-Webdienstes, der ebenfalls durch OAuth 2.0 gesichert ist.
-	Abmelden von Benutzern

Zum Erstellen der vollständigen, funktionierenden App ist Folgendes erforderlich:

2. Registrieren Ihrer App
3. Installieren und Konfigurieren von ADAL
5. Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

Der Code für dieses Tutorial wird [auf GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet) verwaltet. Um folgen zu können, können Sie [das App-Gerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) oder das Gerüst klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

Die fertige App wird außerdem am Ende dieses Lernprogramms bereitgestellt.

## 1. Registrieren einer App
Erstellen Sie eine neue App unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com), oder führen Sie die [ausführlichen Schritte aus](active-directory-v2-app-registration.md).  Stellen Sie sicher, dass Sie:

- die der App zugewiesene **Anwendungs-ID** kopieren, da Sie sie demnächst benötigen.
- die **Web**-Plattform zur App hinzufügen.
- den richtigen **Umleitungs-URI** aus dem Portal eingeben. Sie müssen den Standardwert „urn:ietf:wg:oauth:2.0:oob“ verwenden.

## 2. Installieren und Konfigurieren von ADAL
Nachdem Sie eine App bei Microsoft registriert haben, können Sie ADAL installieren und Code im Zusammenhang mit Identitätsfunktionen schreiben. Damit ADAL mit dem v2.0-Endpunkt kommunizieren kann, müssen Sie einige Informationen zur App-Registrierung bereitstellen.

-	Fügen Sie dazu zunächst ADAL mithilfe der Paket-Manager-Konsole zum TodoListClient-Projekt hinzu.

```
PM \> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory ProjectName - TodoListClient - IncludePrerelease 
```

-	Öffnen Sie im Projekt „TodoListClient“ `app.config`. Ersetzen Sie die Werte der Elemente in Abschnitt `<appSettings>` durch die Werte, die Sie im App-Registrierungsportal eingegeben haben. Sobald Ihr Code ADAL verwendet, verweist er auf diese Werte.
    -	`ida:ClientId` ist die **Anwendungs-ID** Ihrer App, die Sie aus dem Portal kopiert haben.
    -	`ida:RedirectUri` ist der **Umleitungs-URI** aus dem Portal.
- Öffnen Sie im „TodoList“-Dienstprojekt `web.config` im Stammverzeichnis des Projekts.  
    - Ersetzen Sie den Wert `ida:Audience` mit der gleichen **Anwendungs-ID** aus dem Portal.

## 3\. Verwenden von ADAL zum Abrufen von Token
Das Grundprinzip von ADAL ist wie folgt: Wann immer Ihre Anwendung ein Zugriffstoken benötigt, rufen Sie einfach `authContext.AcquireToken(...)` auf, und ADAL erledigt alles Weitere.

-	Öffnen Sie im Projekt `TodoListClient` die Datei `MainWindow.xaml.cs`, und suchen Sie die Methode `OnInitialized(...)`. Der erste Schritt ist die Initialisierung des `AuthenticationContext` Ihrer Anwendung – der primären Klasse von ADAL. Dort übergeben Sie ADAL die zur Kommunikation mit Azure AD notwendigen Koordinaten und weisen es an, wie Token zwischengespeichert werden sollen.

```C#
protected override async void OnInitialized(EventArgs e)
{
		base.OnInitialized(e);

		authContext = new AuthenticationContext(authority, new FileCache());
		AuthenticationResult result = null;
		...
}
```

- Wenn die App gestartet wird, sollte geprüft werden, ob der Benutzer bereits in der App angemeldet ist. Es soll jedoch noch keine Benutzeroberfläche für die Anmeldung erzeugt werden – der Benutzer soll zu diesem Zweck selbst auf "Anmelden" klicken. Außerdem in der `OnInitialized(...)`-Methode:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
		result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

		// If we got here, a valid token is in the cache.  Proceed to
		// fetch the user's tasks from the TodoListService via the
		// GetTodoList() method.

		SignInButton.Content = "Clear Cache";
		GetTodoList();
}
catch (AdalException ex)
{
		if (ex.ErrorCode == "user_interaction_required")
		{
				// If user interaction is required, the app should take no action,
				// and simply show the user the sign in button.
		}
		else
		{
				// Here, we catch all other AdalExceptions

				string message = ex.Message;
				if (ex.InnerException != null)
				{
						message += "Inner Exception : " + ex.InnerException.Message;
				}
				MessageBox.Show(message);
		}
}
```

- Wenn der Benutzer nicht angemeldet ist und auf die Schaltfläche "Anmelden" klickt, soll eine Anmeldebenutzeroberfläche aufgerufen werden, und der Benutzer soll dort seine Anmeldeinformationen eingeben. Implementieren Sie den Schaltflächenhandler für die Anmeldung:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// TODO: Sign the user out if they clicked the "Clear Cache" button

		// If the user clicked the 'Sign-In' button, force
		// ADAL to prompt the user for credentials by specifying
		// PromptBehavior.Always.  ADAL will get a token for the
		// TodoListService and cache it for you.

		AuthenticationResult result = null;
		try
		{
				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
				SignInButton.Content = "Clear Cache";
				GetTodoList();
		}
		catch (AdalException ex)
		{
				// If ADAL cannot get a token, it will throw an exception.
				// If the user canceled the login, it will result in the
				// error code 'authentication_canceled'.

				if (ex.ErrorCode == "authentication_canceled")
				{
						MessageBox.Show("Sign in was canceled by the user");
				}
				else
				{
						// An unexpected error occurred.
						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}

						MessageBox.Show(message);
				}

				return;
		}

}
```

- Wenn der Benutzer sich erfolgreich angemeldet hat, empfängt ADAL ein Token und legt es für Sie in einem Zwischenspeicher ab, sodass Sie mit gutem Gewissen mit dem Aufrufen der `GetTodoList()`-Methode fortfahren können. Zum Abrufen der Aufgaben eines Benutzers ist nur noch die Implementierung der `GetTodoList()`-Methode erforderlich.

```C#
private async void GetTodoList()
{
		AuthenticationResult result = null;
		try
		{
				// Here, we try to get an access token to call the TodoListService
				// without invoking any UI prompt.  PromptBehavior.Never forces
				// ADAL to throw an exception if it cannot get a token silently.

				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
		}
		catch (AdalException ex)
		{
				// ADAL couldn't get a token silently, so show the user a message
				// and let them click the Sign-In button.

				if (ex.ErrorCode == "user_interaction_required")
				{
						MessageBox.Show("Please sign in first");
						SignInButton.Content = "Sign In";
				}
				else
				{
						// In any other case, an unexpected error occurred.

						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}
						MessageBox.Show(message);
				}

				return;
		}

		// Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

		...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn\(object sender = null, RoutedEventArgs args = null\) { // Wenn der Benutzer auf die Schaltfläche 'Zwischenspeicher leeren' geklickt hat, // leeren Sie den ADAL-TOken-Cache und zeigen Sie den Benutzer als abgemeldet an. // Außerdem müssen die Cookies aus dem Browsersteuerung gelöscht werden, // damit der nächste Benutzer die Möglichkeit hat, sich anzumelden.

		if (SignInButton.Content.ToString() == "Clear Cache")
		{
				TodoList.ItemsSource = string.Empty;
				authContext.TokenCache.Clear();
				ClearCookies();
				SignInButton.Content = "Sign In";
				return;
		}

		...
```

Glückwunsch! Sie verfügen nun über eine funktionierende .NET-WPF-App, mit der Sie Benutzer authentifizieren und Web-APIS mithilfe von OAuth 2.0 sicher aufrufen können. Führen Sie beide Projekte aus, und melden Sie sich entweder mit einem persönlichen Microsoft-Konto oder einem Geschäfts- oder Schulkonto an. Fügen Sie Aufgaben zur To-Do-Liste des Benutzers hinzu. Melden Sie sich ab, und anschließend wieder als anderer Benutzer an, um dessen To-Do-Liste anzuzeigen. Schließen Sie die App, und führen Sie sie erneut aus. Beobachten Sie dabei, wie die Sitzung des Benutzers intakt bleibt, weil die App Token in einer lokalen Datei zwischenspeichert.

ADAL vereinfacht das Übernehmen gemeinsamer Identitätsfeatures in die App, sowohl für persönliche als auch für Geschäftskonten. Es übernimmt den Großteil der Arbeit für Sie: Cacheverwaltung, Oauth-Protokollunterstützung, Bereitstellen einer Anmeldeoberfläche für den Benutzer, Aktualisieren abgelaufener Token, und Vieles mehr. Sie müssen nur einen einzigen API-Aufruf kennen, und zwar  „authContext.AcquireTokenAsync(...)“.

Das vollständige Beispiel finden Sie zur Referenz (ohne Ihre Konfigurationswerte) [als .ZIP-Datei hier](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), oder Sie können es aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Nächste Schritte

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

- [Sichern der TodoListService-Web-API mit dem App-Modell v2.0 \>\>](active-directory-v2-devquickstarts-dotnet-api.md)

Weitere Ressourcen:
- [Die App-Modell v2.0-Vorschauversion \>\>](active-directory-appmodel-v2-overview.md)
- [StackOverflow-"adal"-Tag \>\>](http://stackoverflow.com/questions/tagged/adal)

<!------HONumber=August15_HO7-->