<properties
	pageTitle="Azure AD B2C-Vorschau | Microsoft Azure"
	description="Erstellen einer Windows-Desktopanwendung mit Registrierung, Anmeldung und Profilbearbeitung mithilfe von Azure AD B2C."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Azure AD B2C-Vorschau: Erstellen von Windows-Desktop-Apps

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Mit Azure AD B2C können Sie Ihren Desktop-Apps in wenigen Schritten leistungsstarke Self-Service-Features zur Identitätsverwaltung hinzufügen.  In diesem Artikel 
erfahren Sie, wie eine .NET WPF-App für eine Aufgabenliste erstellt wird, die Benutzerregistrierung, -anmeldung und die Verwaltung von Profilen umfasst. Sie umfasst auch die Unterstützung für die Registrierung und Anmeldung mit einem Benutzernamen oder
einer E-Mail-Adresse sowie über Konten sozialer Netzwerke wie Facebook oder Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1. Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. Wenn Sie noch nicht über
ein Verzeichnis verfügen, [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

## 2. Erstellen einer Anwendung

Nun müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind.  Befolgen Sie zum
Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Ersetzen Sie

- Einfügen eines **systemeigenen Clients** in die Anwendung
- Notieren Sie sich den **Umleitungs-URI** `urn:ietf:wg:oauth:2.0:oob` – dies ist die Standard-URL für dieses Codebeispiel.
- Notieren Sie sich die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie später.

    > [AZURE.IMPORTANT]Sie können hierfür keine Anwendungen verwenden, die im [Azure-Portal](https://manage.windowsazure.com/) auf der Registerkarte **Anwendungen** registriert sind.

## 3. Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [**Richtlinie**](active-directory-b2c-reference-policies.md) definiert.  Dieses Codebeispiel enthält drei 
Benutzeroberflächen, für die Identitäten relevant sind: Registrierung, Anmeldung und Profilbearbeitung. Sie müssen eine Richtlinie für jeden Typ erstellen, wie im 
[Artikel für Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Beachten Sie beim Erstellen der drei Richtlinien Folgendes:

- Wählen Sie **Registrierung mit Benutzer-ID** oder **E-Mail-Registrierung** auf dem Blatt für den Identitätsanbieter aus.
- Wählen Sie den **Anzeigenamen** und einige andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsanspruch in den Richtlinien aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen in Kürze. 

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

## 4\. Herunterladen des Codes

Der Code für dieses Lernprogramm wird auf [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet) verwaltet. Zum Erstellen des Beispiels können Sie 
[ein Projektgerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip) oder das Gerüst klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Die fertige App ist ebenfalls [als ZIP-Datei verfügbar](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) und unter
`complete` im gleichen Repository enthalten.

Nachdem Sie den Beispielcode heruntergeladen haben, öffnen Sie die `.sln`-Datei in Visual Studio, um zu beginnen. Wie Sie sehen, sind in der Projektmappe zwei Projekte enthalten: ein `TaskClient`-Projekt und ein `TaskService`-Projekt. `TaskClient` ist eine WPF-Desktopanwendung,
mit der der Benutzer interagiert. `TaskService` ist die Back-End-Web-API der App, in der die Aufgabenlisten der Benutzer gespeichert werden. Der `TaskClient` und der `TaskService` werden in diesem Fall durch eine einzige **Anwendungs-ID**
dargestellt, da sie zusammen eine logische Anwendung bilden.

## 5\. Konfigurieren des Aufgabendiensts

Wenn `TaskService` Anforderungen von `TaskClient` erhält, sucht er nach einem gültigen Zugriffstoken zum Authentifizieren der Anforderung. Zum Überprüfen der Zugriffstoken müssen Sie `TaskService` einige Informationen zur App bereitstellen. Öffnen Sie im `TaskService`-Projekt die Datei `web.config` aus dem Stammverzeichnis des Projekts, und ersetzen Sie die Werte im Abschnitt `<appSettings>`:

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

If you want to learn how a web API securely authenticates requests using Azure AD B2C, check out our
[Web API Getting Started article](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Execute policies
Now that the `TaskService` is ready to authenticate requests, we can implement the `TaskClient`.  Your app communicates with Azure AD B2C by sending HTTP authentication requests,
specifying the policy it wishes to execute as part of the request.  For .NET desktop applications, you can use the **Active Directory Authentication Library (ADAL)**
to send OAuth 2.0 authentication messages, execute policies, and get tokens for calling web APIs.

#### Install ADAL
Begin by adding ADAL to the TaskClient project using the Visual Studio Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### Geben Sie Ihre B2C-Details ein.
Öffnen Sie die Datei `Globals.cs`, und ersetzen Sie alle Eigenschaftswerte durch Ihre eigenen. Diese Klasse wird vom `TaskClient` für Verweise auf häufig verwendete Werte verwendet.

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


#### Erstellen eines "AuthenticationContext"
Die primäre Klasse der ADAL ist der `AuthenticationContext` – er stellt die Verbindung zwischen der App und Ihrem B2C-Verzeichnis dar. Wenn die App gestartet wird, erstellen Sie eine `AuthenticationContext`-Instanz in `MainWindow.xaml.cs`, die im Fenster verwendet werden kann.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

#### Initiieren eines Registrierungsflusses
Klickt der Benutzer auf die Registrierungsschaltfläche, soll mithilfe der von Ihnen erstellten Registrierungsrichtlinie ein Registrierungsfluss initiiert werden. Bei ADAL müssen Sie nur `authContext.AcquireTokenAsync(...)` aufrufen. Die von Ihnen an `AcquireTokenAsync(...)` übergebenen Parameter legen fest, welche Token Sie erhalten, welche Richtlinie für die Authentifizierungsanfrage verwendet wird usw.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
		// Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;
		
		// When the request completes successfully, you can get user information form the AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign up successfully completes, display the user's To-Do List
		GetTodoList();
	}
	
	// Handle any exeptions that occurred during execution of the policy.
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

#### Initiieren eines Anmeldeflusses
Ein Anmeldedatenfluss kann auf dieselbe Weise wie der Registrierungsfluss initiiert werden. Klickt der Benutzer auf die Anmeldeschaltfläche, nehmen Sie denselben ADAL-Aufruf vor – dieses Mal jedoch mit Ihrer Anmelderichtlinie:

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

#### Initiieren eines Profilbearbeitungsflusses
Auch in diesem Fall führen Sie Ihre Profilbearbeitungs-Richtlinie auf dieselbe Weise aus:

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

In all diesen Fällen gibt die ADAL entweder im `AuthenticationResult` ein Token zurück oder löst eine Ausnahme aus. Jedes Mal, wenn Sie ein Token von der ADAL erhalten, können Sie mit dem `AuthenticationResult.UserInfo`-Objekt die Benutzerdaten in der App (z. B. die Benutzeroberfläche) aktualisieren. Die ADAL speichert auch das Token für die Verwendung in anderen Teilen der Anwendung zwischen.

## 7\. Aufrufen von APIs
Wir haben bereits die ADAL zum Ausführen von Richtlinien sowie zum Abrufen von Token verwendet. In vielen Fällen möchten Sie jedoch prüfen, ob ein zwischengespeichertes Token vorhanden ist, ohne eine Richtlinie auszuführen. Ein solcher Fall tritt z. B. ein, wenn die App versucht, die Aufgabenliste des Benutzers vom `TaskService` abzurufen. Sie können dieselbe `authContext.AcquireTokenAsync(...)`-Methode verwenden und erneut die `clientId` als Bereichsparameter einsetzen, dieses Mal aber mit `PromptBehavior.Never`:

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
		// could not be acquired from the cache, rather than automatically prompting the user to sign in. 
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);
	
	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign-in.
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

Wenn der Aufruf von `AcquireTokenAsync(...)` erfolgreich ist und ein Token im Cache gefunden wird, können Sie das Token dem `Authorization`-Header der HTTP-Anforderung hinzufügen, damit der `TaskService` die Anforderung zum Lesen der Aufgabenliste für den Benutzer authentifizieren kann:

```C#
	...
	// Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the To Do list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
``` 

Dasselbe Muster können Sie jederzeit anwenden, um den Tokencache für Token zu überprüfen, ohne dass der Benutzer sich anmelden muss. Wir möchten z. B. beim Starten der App den `FileCache` auf vorhandene Token überprüfen, damit die Anmeldesitzung des Benutzers bei jeder Ausführung der App beibehalten wird. Sie finden den gleichen Code im `OnInitialized`-Ereignis des `MainWindow`, der auch die erste Ausführung behandelt.

## 8\. Abmelden des Benutzers
Sie können mit der ADAL auch die App-Sitzung des Benutzers beenden, wenn dieser auf die Schaltfläche "Abmelden" klickt. Mit der ADAL erfolgt dies einfach durch Leeren aller Token aus dem Tokencache:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
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

## 9\. Ausführen der Beispiel-App

Erstellen Sie abschließend `TaskClient` und `TaskService`, und führen Sie beide aus. Registrieren Sie sich für die App mit einer E-Mail-Adresse oder einem Benutzernamen. Melden Sie sich ab und unter demselben Benutzer wieder an. Bearbeiten Sie das Profil dieses Benutzers. Melden Sie sich ab, und registrieren Sie sich als anderer Benutzer.

## 10\. Soziale Netzwerke als IDPs hinzufügen

Die App unterstützt bisher nur die Registrierung und Anmeldung von Benutzern mit sogenannten **lokalen Konten** – Konten in Ihrem B2C-Verzeichnis mit einem Benutzernamen und einem Kennwort. Mit Azure AD B2C können Sie auch andere **Identitätsanbieter** (oder IDPs) hinzufügen, ohne Ihren Code ändern zu müssen.

Um Ihrer App soziale Netzwerke als IDPs hinzuzufügen, befolgen Sie zunächst die detaillierten Anweisungen in einem oder zwei der folgenden Artikel. Sie müssen für jeden IDP, den Sie unterstützen möchten, eine Anwendung in dessen System registrieren und eine Client-ID abrufen.

- [Einrichten von Facebook als IDP](active-directory-b2c-setup-fb-app.md)
- [Einrichten von Google als IDP](active-directory-b2c-setup-goog-app.md)
- [Einrichten von Amazon als IDP](active-directory-b2c-setup-amzn-app.md)
- [Einrichten von LinkedIn als IDP](active-directory-b2c-setup-li-app.md) 

Wenn Sie die Identitätsanbieter Ihrem B2C-Verzeichnis hinzugefügt haben, müssen Sie jede der drei Richtlinien bearbeiten, um die neuen IDPs wie im [Artikel für Richtlinienreferenzen](active-directory-b2c-reference-policies.md) beschrieben einzufügen. Nachdem Sie Ihre Richtlinien gespeichert haben, führen Sie die App einfach erneut aus. Die neuen IDPs sollten als eine Registrierungs- und Anmeldeoption auf allen Benutzeroberflächen für Identitäten angezeigt werden.

Sie können frei mit Ihren Richtlinien experimentieren und die Auswirkung auf die Beispiel-App beobachten – Sie können IDPs hinzufügen/entfernen oder die Anwendungsansprüche oder Registrierungsattribute ändern. Experimentieren Sie so lange, bis die Zusammenarbeit von Richtlinien, Authentifizierungsanforderungen und ADAL verstanden haben.

Als Referenz stellen wir das [vollständige Beispiel hier als ZIP-Datei](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) bereit. Sie können es auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->