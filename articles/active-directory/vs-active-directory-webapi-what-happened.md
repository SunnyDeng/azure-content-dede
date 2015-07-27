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
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Was ist mit meinem Projekt passiert?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

###<span id="whathappened">Was ist mit meinem Projekt passiert?</span>

Verweise wurden hinzugefügt.

#####NuGet-Paketverweise 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####.NET-Verweise 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt` 

#####Ihrem Projekt wurden Codedateien hinzugefügt 

Eine Authentifizierungsstartklasse (**App_Start/Startup.Auth.cs**) wurde Ihrem Projekt hinzugefügt. Sie enthält Startlogik für die Azure AD-Authentifizierung.

#####Ihrem Projekt wurde Startcode hinzugefügt 

Wenn Sie bereits eine Startklasse in Ihrem Projekt verwendet haben, wurde die **Configuration**-Methode so aktualisiert, dass sie einen Aufruf von `ConfigureAuth(app)` enthält. Andernfalls wurde Ihrem Projekt eine Startklasse hinzugefügt.


#####Die Datei "app.config" oder "web.config" weist neue Konfigurationswerte auf.

Die folgenden Konfigurationseinträge wurden hinzugefügt. <pre> `<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

###Eine Azure AD-App wurde erstellt 

Eine Azure AD-Anwendung wurde in dem Verzeichnis erstellt, das Sie im Assistenten ausgewählt haben.


[Weitere Informationen zu Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=July15_HO3-->