<properties
	pageTitle=""
	description="Beschreibt, was in Ihrem Visual Studio-Projekt geändert wurde, nachdem Sie den Azure Active Directory-Assistenten ausgeführt haben"
	services="active-directory"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="patshea123"/>

# Was ist mit meinem Projekt passiert?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Verweise wurden hinzugefügt

###NuGet-Paketverweise

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###.NET-Verweise

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##Änderungen am Code

###Ihrem Projekt wurden Codedateien hinzugefügt

Eine Authentifizierungsstartklasse \(**App\_Start/Startup.Auth.cs**\) wurde Ihrem Projekt hinzugefügt. Sie enthält Startlogik für die Azure AD-Authentifizierung.

###Ihrem Projekt wurde Startcode hinzugefügt

Wenn Sie bereits eine Startklasse in Ihrem Projekt verwendet haben, wurde die **Configuration**-Methode so aktualisiert, dass sie einen Aufruf von `ConfigureAuth(app)` enthält. Andernfalls wurde Ihrem Projekt eine Startklasse hinzugefügt.


###Die Datei "app.config" oder "web.config" weist neue Konfigurationswerte auf.

Die folgenden Konfigurationseinträge wurden hinzugefügt. <pre> `<appSettings>
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" />
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" />
    		<add key="ida:Audience" value="The App ID Uri from the wizard" />
	</appSettings>` </pre>

###Eine Azure AD-App wurde erstellt

Eine Azure AD-Anwendung wurde in dem Verzeichnis erstellt, das Sie im Assistenten ausgewählt haben.

[Weitere Informationen zu Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

##Wenn ich *das Deaktivieren der Authentifizierung für einzelne Benutzerkonten* ausgewählt habe, welche zusätzlichen Änderungen wurden an meinem Projekt vorgenommen?
NuGet-Paketverweise wurden entfernt, und die Dateien wurden entfernt und gesichert. Abhängig vom Status des Projekts müssen Sie möglicherweise manuell zusätzliche Verweise oder Dateien entfernen oder Code entsprechend ändern.

###NuGet-Paketverweise entfernt \(die vorhanden waren\)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###Codedateien gesichert und entfernt \(die vorhanden waren\)

Jede der folgenden Dateien wurde gesichert und aus dem Projekt entfernt. Sicherungsdateien befinden sich in einem Ordner "Backup" im Stammverzeichnis des Projektverzeichnisses.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###Codedateien gesichert \(die vorhanden waren\)

Jede der folgenden Dateien wurde gesichert, bevor sie ersetzt wurde. Sicherungsdateien befinden sich in einem Ordner "Backup" im Stammverzeichnis des Projektverzeichnisses.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##Wenn ich *Verzeichnisdaten lesen* aktiviert habe, welche zusätzlichen Änderungen wurden an meinem Projekt vorgenommen?

###An "app.config" oder "web.config" wurden zusätzliche Änderungen vorgenommen

Die folgenden zusätzlichen Konfigurationseinträge wurden hinzugefügt. <pre> `<appSettings>
	    <add key="ida:Password" value="Your Azure AD App's new password" />
	</appSettings>` </pre>

###Ihre Azure Active Directory-App \(AD\) wurde aktualisiert
Ihre Azure Active Directory-App wurde aktualisiert und enthält nun die Berechtigung *Verzeichnis lesen*. Außerdem wurde ein zusätzlicher Schlüssel erstellt, der als *ida:Password* in der Datei `web.config` verwendet wird.

[Weitere Informationen zu Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=July15_HO5-->