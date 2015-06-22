<properties
	pageTitle="Erste Schritte in Azure AD Xamarin | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine Xamarin-Anwendung erstellt wird, die sich für die Anmeldung in Azure AD integriert und über OAuth durch Azure AD geschützte APIs aufruft."
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="dastrock"/>


# Integrieren von Azure AD in eine Xamarin-Anwendung

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Mit Xamarin können Sie Anwendungen in C# schreiben, die auf verschiedenen Plattformen, mobilen Geräten und PCs gleichermaßen, ausgeführt werden können. Bei der Entwicklung einer Anwendung mit Xamarin können Sie Ihre Benutzer mit Azure AD einfach und problemlos über deren Active Directory-Konten authentifizieren. Außerdem ermöglicht es in Ihren Anwendungen die sichere Nutzung jeder durch Azure AD geschützten Web-API wie der Azure-API oder Office 365-APIs.

Für Xamarin-Apps, die auf geschützte Ressourcen zugreifen müssen, bietet Azure AD die Active Directory-Authentifizierungsbibliothek (ADAL). Die einzige Aufgabe von ADAL besteht darin, Ihrer Anwendung das Abrufen von Zugriffstoken zu erleichtern. Wir wollen Ihnen nun zeigen, wie einfach das geht. Dazu erstellen wir die Anwendung „DirectorySearcher“ mit folgenden Funktionen:

-	Ausführung unter iOS, Android, Windows Desktop, Windows Phone und Windows Store
- Verwendung einer einzigen portablen Klassenbibliothek (PCL) zur Authentifizierung der Benutzer und zum Abrufen der Tokens für die Azure AD Graph-API
-	Durchsuchen eines Verzeichnisses nach Benutzern mit einem bestimmten UPN

Zur Entwicklung der vollständigen Arbeitsanwendung müssen Sie folgende Schritte ausführen:

2. Einrichten der Xamarin-Entwicklungsumgebung
2. Registrieren Ihrer Anwendung bei Azure AD
3. Installieren und Konfigurieren von ADAL
5. Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

Beginnen Sie, indem Sie [ein Projektgerüst](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) oder [das vollständige Beispiel herunterladen](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Bei beidem handelt es sich um eine Visual Studio 2013-Lösung. Außerdem benötigen Sie einen Azure AD-Mandanten, in dem Sie Benutzer erstellen und Ihre Anwendung registrieren können. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](active-directory-howto-tenant.md).

## *0. Einrichten der Xamarin-Entwicklungsumgebung*
Xamarin kann je nach Zielplattform unterschiedlich eingerichtet werden. Da dieses Lernprogramm Projekte für iOS, Android und Windows enthält, haben wir uns als Entwicklungstools für Visual Studio 2013 und den [Xamarin.iOS Build Host](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/) entschieden. Diese setzen Folgendes voraus: - einen Windows-Computer für die Ausführung von Visual Studio und der Windows-Anwendungen – einen OS X-Computer (zur Ausführung der iOS-Anwendungen) – ein Xamarin Business-Abonnement (die [kostenlose Testversion](http://developer.xamarin.com/guides/cross-platform/getting_started/beginning_a_xamarin_trial/) ist ausreichend) – [Xamarin für Windows](https://xamarin.com/download), einschließlich Xamarin.iOS, Xamarin.Android und Visual Studio-Integration (für dieses Beispiel empfehlenswert) – [Xamarin für OS X](https://xamarin.com/download), einschließlich Xamarin.iOS (und den Xamarin.iOS Build Host), Xamarin.Android, Xamarin.Mac und Xamarin Studio.

Wir empfehlen, mit der [Xamarin-Downloadseite](https://xamarin.com/download) zu beginnen und Xamarin sowohl auf Ihrem Mac als auch auf Ihrem PC zu installieren. Auch wenn Ihnen nicht beide Computer zur Verfügung stehen, können Sie das Beispiel ausführen, müssen dabei aber bestimmte Projekte auslassen. Befolgen Sie die [detaillierten Installationsanleitungen](http://developer.xamarin.com/guides/cross-platform/getting_started/installation/) für iOS und Android. Wenn Sie darüber hinaus mehr über die verfügbaren Entwicklungsoptionen wissen möchten, empfehlen wir Ihnen zusätzlich das Handbuch [Building Cross Platform Applications](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/part_1_-_understanding_the_xamarin_mobile_platform/) (in englischer Sprache). Im Augenblick brauchen Sie noch kein Gerät für die Entwicklung einzurichten, ebenso benötigen Sie noch kein Abonnement für das Apple Developer Program (es sei denn, Sie möchten die iOS-App auf einem Gerät ausführen).

Nach Abschluss der erforderlichen Konfigurationsschritte öffnen Sie die Lösung in Visual Studio, um zu beginnen. Sie finden dort sechs Projekte: fünf plattformspezifische Projekte und die portable Klassenbibliothek `DirectorySearcher.cs`, die auf allen Plattformen verwendet werden kann.


## *1. Registrieren der Anwendung DirectorySearcher*
Damit Ihre Anwendung Tokens abrufen kann, müssen Sie sie zunächst beim Azure AD-Mandanten registrieren und ihr die Berechtigung für den Zugriff auf die Azure AD Graph-API erteilen:

-	Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
-	Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
-	Wählen Sie den Mandanten aus, unter dem die Anwendung registriert werden soll.
-	Klicken Sie auf die Registerkarte **Anwendungen** und dann in der unteren Schublade auf **Hinzufügen**.
-	Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue **systemeigene Clientanwendung**.
    -	Am **Namen** der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können.
    -	Die **Umleitungs-URI** ist eine Kombination aus einem Schema und einer Zeichenfolge, die Azure AD für die Rückgabe der Tokenantworten verwendet. Geben Sie einen Wert ein, zum Beispiel `http://DirectorySearcher`.
-	Nach Abschluss der Registrierung weist AAD Ihrer Anwendung eine eindeutige Client-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten, weswegen Sie ihn aus der Registerkarte **Konfigurieren** kopieren sollten.
- Suchen Sie ebenso auf der Registerkarte **Konfigurieren** den Abschnitt „Berechtigungen für andere Anwendungen“. Fügen Sie für die Anwendung „Azure Active Directory“ unter **Delegierte Berechtigungen** die Berechtigung **Zugriff auf das Verzeichnis Ihrer Organisation** hinzu. Mit dieser Berechtigung kann die Anwendung die Graph-API nach Benutzern abfragen.

## *2. Installieren und Konfigurieren von ADAL*
Nachdem Sie nun eine Anwendung in Azure AD erstellt haben, können Sie ADAL installieren und Ihren identitätsbezogenen Code schreiben. Damit ADAL mit Azure AD kommunizieren kann, müssen Sie ihm einige Informationen über die Registrierung Ihrer Anwendung bereitstellen. Fügen Sie ADAL dazu zunächst über die Paket-Manager-Konsole zu jedem Projekt der Lösung hinzu.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.Windows -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.WindowsPhone -IncludePrerelease
`

- Beachten Sie, dass jedem Projekt zwei Bibliotheksverweise hinzugefügt werden – der PCL-Teil von ADAL und ein plattformspezifischer Teil.

-	Öffnen Sie `DirectorySearcher.cs` im Projekt DirectorySearcherLib. Ersetzen Sie die Klassenmember-Werte durch die Werte, die Sie im Azure-Portal eingegeben haben. Sobald Ihr Code ADAL verwendet, verweist er auf diese Werte.
    -	`tenant` ist die Domäne Ihres Azure AD-Mandanten, z. B. „contoso.onmicrosoft.com“.
    -	`clientId` ist die Client-ID Ihrer Anwendung, die Sie aus dem Portal kopiert haben.
    - `returnUri` ist die Umleitungs-URI, die Sie im Portal eingegeben haben, z. B. `http://DirectorySearcher`.

## *3. Verwenden von ADAL zum Abrufen von Tokens aus AAD*
*Nahezu* die gesamte Authentifizierungslogik der Anwendung befindet sich in `DirectorySearcher.SearchByAlias(...)`. In den plattformspezifischen Projekten müssen Sie der PCL von `DirectorySearcher` daher nur noch einen kontextbezogenen Parameter übergeben.

- Öffnen Sie dazu zunächst die Datei `DirectorySearcher.cs`, und fügen Sie deren `SearchByAlias(...)`-Methode einen neuen Parameter hinzu. Der kontextbezogene Parameter, der alle von ADAL für die Authentifizierung benötigten plattformspezifischen Objekte enthält, lautet `IPlatformParameters`.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	Als Nächstes initialisieren Sie den `AuthenticationContext` – die primäre Klasse von ADAL. Dort übergeben Sie ADAL die zur Kommunikation mit Azure AD notwendigen Koordinaten. Rufen Sie danach `AcquireTokenAsync(...)` auf, das das Objekt `IPlatformParameters` akzeptiert und die Authentifizierungsroutine aufruft, die erforderlich ist, damit die Anwendung ein Token erhält.

```C#
...
AuthenticationResult authResult = null;

try
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
}
...
```
- `AcquireTokenAsync(...)` versucht zunächst, ein Token für die angeforderte Ressource (in diesem Fall die Graph-API) zurückzugeben, ohne den Benutzer zur Eingabe seiner Anmeldeinformationen aufzufordern (die Authentifizierung erfolgt dann über den Cache oder durch Aktualisierung alter Tokens). Die Anmeldeseite von Azure AD wird nur angezeigt, wenn sich dies zum Abrufen des angeforderten Tokens nicht vermeiden lässt.


- Sie können das Zugriffstoken dann der Graph-API-Anforderung im Autorisierungsheader anfügen:

```C#
...
request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Mehr ist für die PCL von `DirectorySearcher` und den identitätsbezogenen Code Ihrer Anwendung nicht zu tun. Sie brauchen nun in den Ansichten der einzelnen Plattformen nur noch die `SearchByAlias(...)`-Methode aufzurufen und gegebenenfalls Code für die korrekte Handhabung des UI-Lebenszyklus hinzuzufügen.

####Android:
- Fügen Sie in `MainActivity.cs` im unteren Klick-Handler einen `SearchByAlias(...)`-Aufruf hinzu:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Außerdem müssen Sie die Lebenszyklus-Methode für `OnActivityResult` überschreiben, wenn Authentifizierungsumleitungen zurück an die betreffende Methode geleitet werden sollen. In Android stellt ADAL hierfür eine Hilfsmethode bereit:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Windows Desktop:
- Stellen Sie in `MainWindow.xaml.cs` einfach einen Aufruf an `SearchByAlias(...)`, der im `PlatformParameters`-Objekt des Desktops ein `WindowInteropHelper` übergibt:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS:
- In `DirSearchClient_iOSViewController.cs` nimmt das `PlatformParameters`-Objekt von iOS lediglich einen Verweis auf den Ansichtscontroller an:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Windows Store
- Öffnen Sie in Windows Store die Datei `MainPage.xaml.cs`, und implementieren Sie die Methode `Search`, die in einem freigegebenen Projekt zur bedarfsgerechten Aktualisierung der Benutzeroberfläche eine Hilfsmethode verwendet.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters(PromptBehavior.Auto, false));
```

####Windows Phone
- Öffnen Sie in Windows Phone die Datei `MainPage.xaml.cs`, und implementieren Sie die Methode `Search`, die zur Aktualisierung der Benutzeroberfläche in einem freigegebenen Projekt die gleiche Hilfsmethode verwendet.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters());
```

Glückwunsch! Damit haben Sie eine funktionsfähige Xamarin-Anwendung entwickelt, die Benutzer auf fünf verschiedenen Plattformen authentifizieren und Web-APIs über OAuth 2.0 sicher aufrufen kann. Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen. Führen Sie danach Ihre DirectorySearcher-Anwendung aus, und melden Sie sich unter einem dieser Benutzer an. Suchen Sie anhand des UPN nach anderen Benutzern.

ADAL erleichtert Ihnen die Integration allgemeiner Identitätsfunktionen in Ihrer Anwendung. Es übernimmt die unangenehmen Verwaltungsarbeiten für Sie – die Cacheverwaltung, die Unterstützung des OAuth-Protokolls, die Anzeige einer Anmeldeschnittstelle für den Benutzer, die Aktualisierung abgelaufener Tokens und vieles mehr. Das Einzige, womit Sie sich noch beschäftigen müssen, ist der API-Aufruf `authContext.AcquireToken*(…)`.

Als Referenz stellen wir [hier](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) das vollständige Beispiel (ohne Ihre Konfigurationswerte) bereit. Sie können sich nun weiteren Identitätsszenarien zuwenden. Wie wäre es zum Beispiel mit Folgendem:

[Schützen einer .NET Web-API mit Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

Sehen Sie sich auch die folgenden Ressourcen an: – [AzureAD-Beispiele auf GitHub >>](https://github.com/AzureAdSamples) – [CloudIdentity.com >>](https://cloudidentity.com) – Azure AD-Dokumentation auf [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=58--> 