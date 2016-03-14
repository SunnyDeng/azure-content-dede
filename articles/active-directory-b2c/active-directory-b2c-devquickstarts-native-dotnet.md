<properties
	pageTitle="Azure Active Directory B2C-Vorschau | Microsoft Azure"
	description="Erstellen einer Windows-Desktopanwendung mit Anmeldung, Registrierung und Profilverwaltung mithilfe von Azure Active Directory B2C."
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Azure AD B2C-Vorschau: Erstellen von Windows-Desktop-Apps


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Mit Azure Active Directory (Azure AD) B2C können Sie Ihren Desktop-Apps in wenigen Schritten leistungsstarke Self-Service-Features zur Identitätsverwaltung hinzufügen. In diesem Artikel erfahren Sie, wie eine .NET Windows Presentation Foundation-App (WPF) für eine Aufgabenliste erstellt wird, die Benutzerregistrierung, -anmeldung und die Verwaltung von Profilen umfasst. Die App verfügt über Unterstützung für die Registrierung und Anmeldung mit einem Benutzernamen oder einer E-Mail-Adresse. Sie bietet auch Unterstützung für die Registrierung und Anmeldung über Konten für soziale Netzwerke wie Facebook und Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für all Ihre Benutzer, Apps, Gruppen usw. Wenn Sie noch keines verwenden, [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie die weiteren Schritte in diesem Leitfaden ausführen.

## Erstellen einer Anwendung

Als Nächstes müssen Sie in Ihrem B2C-Verzeichnis eine App erstellen. Dadurch werden Azure AD die Informationen bereitgestellt, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Führen Sie folgende Schritte aus:

- Fügen Sie einen **nativen Client** in die Anwendung ein.
- Kopieren Sie den **Umleitungs-URI** `urn:ietf:wg:oauth:2.0:oob`. Dies ist die Standard-URL für dieses Codebeispiel.
- Kopieren Sie die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie später.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [Richtlinie](active-directory-b2c-reference-policies.md) definiert. Dieses Codebeispiel enthält drei Benutzeroberflächen, für die Identitäten relevant sind: Registrierung, Anmeldung und Profilbearbeitung. Sie müssen für jeden Typ eine Richtlinie erstellen, wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Beachten Sie beim Erstellen der drei Richtlinien Folgendes:

- Wählen Sie auf dem Blatt für den Identitätsanbieter **Registrierung mit Benutzer-ID** oder **E-Mail-Registrierung** aus.
- Wählen Sie den **Anzeigenamen** und andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche für jede Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen später.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

## Herunterladen des Codes

Der Code für dieses Tutorial [wird auf GitHub verwaltet](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Zum Erstellen des Beispiels können Sie [ein Projektgerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Sie können das Gerüst auch klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Die fertige App ist ebenfalls [als ZIP-Datei verfügbar](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) oder unter der Verzweigung `complete` im gleichen Repository enthalten.

Nachdem Sie den Beispielcode heruntergeladen haben, öffnen Sie zum Einstieg die Visual Studio-SLN-Datei. Die Projektmappendatei enthält zwei Projekte: ein `TaskClient`-Projekt und ein `TaskService`-Projekt. `TaskClient` ist die WPF-Desktopanwendung, mit der der Benutzer interagiert. `TaskService` ist die Back-End-Web-API der App, in der die Aufgabenlisten der einzelnen Benutzer gespeichert werden. In diesem Fall werden `TaskClient` und `TaskService` durch eine einzige Anwendungs-ID dargestellt, da sie zusammen eine logische Anwendung bilden.

## Konfigurieren des Aufgabendiensts

Wenn `TaskService` eine Anforderung von `TaskClient` erhält, sucht er nach einem gültigen Zugriffstoken zum Authentifizieren der Anforderung. Zum Überprüfen des Zugriffstokens müssen Sie `TaskService` Informationen zur App bereitstellen. Öffnen Sie im Projekt `TaskService` die Datei `web.config` im Stammverzeichnis des Projekts, und ersetzen Sie die Werte im Abschnitt `<appSettings>`:

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

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Um zu erfahren, wie eine Web-API sicher Anforderungen mithilfe von Azure AD B2C authentifiziert, sehen Sie sich unseren [Artikel mit den ersten Schritten für die Web-API](active-directory-b2c-devquickstarts-api-dotnet.md) an.

## Ausführen von Richtlinien
Wenn `TaskService` zum Authentifizieren von Anforderungen bereit ist, können Sie `TaskClient` implementieren. Ihre App kommuniziert mit Azure AD B2C, indem HTTP-Authentifizierungsanforderungen gesendet werden. Sie geben die Richtlinie an, die als Teil der Anforderung ausgeführt werden soll. Für .NET-Desktopanwendungen können Sie die Active Directory Authentication Library (ADAL) zum Senden von OAuth 2.0-Authentifizierungsnachrichten, zum Ausführen von Richtlinien und zum Abrufen von Token, die Web-APIs aufrufen, verwenden.

### Installieren von ADAL
Fügen Sie ADAL mithilfe der Paket-Manager-Konsole von Visual Studio zum `TaskClient`-Projekt hinzu.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

### Eingeben der B2C-Details
Öffnen Sie die Datei `Globals.cs`, und ersetzen Sie alle Eigenschaftswerte durch Ihre eigenen. Diese Klasse wird von `TaskClient` für Verweise auf häufig verwendete Werte verwendet.

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### Erstellen von „AuthenticationContext“
Die primäre Klasse von ADAL ist `AuthenticationContext`. Sie stellt die Verbindung Ihrer App mit Ihrem B2C-Verzeichnis dar. Wenn die App gestartet wird, erstellen Sie eine Instanz von `AuthenticationContext` in `MainWindow.xaml.cs`. Diese kann im gesamten Fenster verwendet werden.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default. Because we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

### Initiieren eines Registrierungsflusses
Wenn sich ein Benutzer registrieren möchte, sollte ein Registrierungsfluss initiiert werden, der die Registrierungsrichtlinie verwendet, die Sie erstellt haben. Bei Verwendung von ADAL rufen Sie einfach `authContext.AcquireTokenAsync(...)` auf. Die von Ihnen an `AcquireTokenAsync(...)` übergebenen Parameter legen fest, welches Token Sie erhalten, welche Richtlinie in der Authentifizierungsanforderung verwendet wird usw.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that you want a token to our own back-end API.
		// Use the PromptBehavior. Always flag to indicate to ADAL that it should show a sign-up UI, no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information from AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign-up successfully completes, display the user's to-do list
		GetTodoList();
	}

	// Handle any exemptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
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

### Initiieren eines Anmeldeflusses
Sie können einen Anmeldefluss auf die gleiche Weise wie einen Registrierungsfluss initiieren. Wenn sich ein Benutzer anmeldet, nehmen Sie den gleichen Aufruf von ADAL vor, dieses Mal mithilfe der Anmelderichtlinie:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...
```

### Initiieren eines Profilbearbeitungsflusses
Auch in diesem Fall können Sie Ihre Profilbearbeitungsrichtlinie auf dieselbe Weise ausführen:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

In all diesen Fällen gibt ADAL entweder in `AuthenticationResult` ein Token zurück oder löst eine Ausnahme aus. Jedes Mal, wenn Sie ein Token von der ADAL abrufen, können Sie mit dem `AuthenticationResult.UserInfo`-Objekt die Benutzerdaten in der App (z. B. die Benutzeroberfläche) aktualisieren. ADAL speichert das Token auch für die Verwendung in anderen Teilen der Anwendung zwischen.

## Aufrufen von APIs
Sie haben jetzt ADAL zum Ausführen von Richtlinien sowie zum Abrufen von Token verwendet. In vielen Fällen möchten Sie jedoch prüfen, ob ein zwischengespeichertes Token vorhanden ist, ohne eine Richtlinie auszuführen. Ein solcher Fall tritt z. B. ein, wenn die App versucht, die Aufgabenliste des Benutzers von `TaskService` abzurufen. Sie können dazu dieselbe `authContext.AcquireTokenAsync(...)`-Methode verwenden und erneut `clientId` als Bereichsparameter einsetzen, dieses Mal aber zusätzlich mit `PromptBehavior.Never`:

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token
		// could not be acquired from the cache, rather than automatically prompt the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

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
	...
```

Wenn der Aufruf von `AcquireTokenAsync(...)` erfolgreich ist und ein Token im Cache gefunden wird, können Sie das Token dem `Authorization`-Header der HTTP-Anforderung hinzufügen. Auf diese Weise kann `TaskService` die Anforderung zum Lesen der Aufgabenliste für den Benutzer authentifizieren:

```C#
	...
	// After the token has been returned by ADAL, add it to the HTTP authorization header before the call is made to TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the to-do-list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

Dieses Muster können Sie jederzeit anwenden, um den Tokencache auf Token zu überprüfen, ohne dass der Benutzer sich anmelden muss. Wenn die App gestartet wird, möchten Sie z. B. möglicherweise `FileCache` auf vorhandene Token überprüfen. Auf diese Weise bleibt die Anmeldesitzung des Benutzers immer erhalten, wenn die App ausgeführt wird. Sie finden den gleichen Code im `OnInitialized`-Ereignis von `MainWindow`. `OnInitialized` verarbeitet die erste Ausführung.

## Abmelden des Benutzers
Sie können mit ADAL die App-Sitzung des Benutzers beenden, wenn dieser **Abmelden** auswählt. Mithilfe von ADAL wird dies durch Löschen aller Token aus dem Tokencache erreicht:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses. It is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## Ausführen der Beispiel-App

Zum Schluss erstellen Sie `TaskClient` und `TaskService` und führen sie aus. Registrieren Sie sich für die App mit einer E-Mail-Adresse oder einem Benutzernamen. Melden Sie sich ab und unter demselben Benutzer wieder an. Bearbeiten Sie das Profil dieses Benutzers. Melden Sie sich ab, und registrieren Sie sich als ein anderer Benutzer.

## Soziale Netzwerke als IDPs hinzufügen

Derzeit unterstützt die App nur die Registrierung und Anmeldung von Benutzern über **lokale Konten**. Dies sind in Ihrem B2C-Verzeichnis gespeicherte Konten, die einen Benutzernamen und ein Kennwort verwenden. Mit Azure AD B2C können Sie auch Unterstützung für andere Identitätsanbieter (IDPs) hinzufügen, ohne Ihren Code ändern zu müssen.

Um Ihrer App soziale Netzwerke als IdPs hinzuzufügen, befolgen Sie zunächst die detaillierten Anweisungen in diesen Artikeln. Sie müssen für jeden IdP, den Sie unterstützen möchten, eine Anwendung in dessen System registrieren und eine Client-ID abrufen.

- [Einrichten von Facebook als IDP](active-directory-b2c-setup-fb-app.md)
- [Einrichten von Google als IDP](active-directory-b2c-setup-goog-app.md)
- [Einrichten von Amazon als IDP](active-directory-b2c-setup-amzn-app.md)
- [Einrichten von LinkedIn als IDP](active-directory-b2c-setup-li-app.md)

Nachdem Sie Ihrem B2C-Verzeichnis die Identitätsanbieter hinzugefügt haben, müssen Sie jede der drei Richtlinien bearbeiten, um die neuen IDPs wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md) beschrieben einzubeziehen. Nachdem Sie Ihre Richtlinien gespeichert haben, führen Sie die App erneut aus. Die neuen IdPs sollten als Registrierungs- und Anmeldeoptionen in allen Benutzeroberflächen für Identitäten angezeigt werden.

Sie können mit Ihren Richtlinien experimentieren und die Auswirkungen auf die Beispiel-App beobachten. Fügen Sie IdPs hinzu, oder entfernen Sie sie, bearbeiten Sie Anwendungsansprüche, oder ändern Sie Registrierungsattribute. Experimentieren Sie so lange, bis Sie die Zusammenhänge zwischen Richtlinien, Authentifizierungsanforderungen und ADAL nachvollziehen können.

Als Referenz wird das fertige Beispiel [als ZIP-Datei bereitgestellt](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Sie können sie auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->