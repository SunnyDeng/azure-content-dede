<properties 
	pageTitle="Erste Schritte mit Active Directory-Authentifizierung - Was ist passiert?" 
	description="Beschreibt, was mit Ihrem Azure Active Directory-Projekt in Visual Studio passiert ist" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Was ist mit meinem Projekt passiert?

> [AZURE.SELECTOR]
> - [Erste Schritte](vs-active-directory-dotnet-getting-started.md)
> - [Was ist passiert?](vs-active-directory-dotnet-what-happened.md)

###<span id="whathappened">Was ist mit meinem Projekt passiert?</span>
 
Verweise wurden hinzugefügt.

#####NuGet-Paketverweise

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####.NET-Verweise

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System`
- `System.Data`
- `System.Drawing`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####Ihrem Projekt wurden Codedateien hinzugefügt 

Die Authentifizierungsstartklasse `App_Start/Startup.Auth.cs` wurde Ihrem Projekt hinzugefügt. Sie enthält Startlogik für die Azure AD-Authentifizierung. Außerdem wurde eine Controllerklasse ("Controllers/AccountController.cs") hinzugefügt, die die Methoden `SignIn()` und `SignOut()` enthält. Schließlich wurde die Teilansicht `Views/Shared/_LoginPartial.cshtml` hinzugefügt, die einen Aktionslink für "SignIn/SignOut" enthält. 

#####Ihrem Projekt wurde Startcode hinzugefügt
 
Wenn Sie bereits eine Startklasse in Ihrem Projekt verwendet haben, wurde die **Configuration**-Methode so aktualisiert, dass sie einen Aufruf von `ConfigureAuth(app)` enthält. Andernfalls wurde Ihrem Projekt eine Startklasse hinzugefügt. 

#####Ihre Datei "app.config" oder "web.config" weist neue Konfigurationswerte auf 

Die folgenden Konfigurationseinträge wurden hinzugefügt. 
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####Eine Azure Active Directory-App (AD) wurde erstellt 
Eine Azure AD-Anwendung wurde in dem Verzeichnis erstellt, das Sie im Assistenten ausgewählt haben. 

[Weitere Informationen zu Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=49-->