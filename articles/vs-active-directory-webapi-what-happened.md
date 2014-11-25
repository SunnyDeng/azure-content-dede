<properties title="Erste Schritte mit Active Directory-Authentifizierung" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Erste Schritte][Erste Schritte]
> -   [Was ist passiert?][Was ist passiert?]

### <span id="whathappened">Was ist mit meinem Projekt passiert?</span>

Verweise wurden hinzugefügt.

##### NuGet-Paketverweise

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### .NET-Verweise

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Ihrem Projekt wurden Codedateien hinzugefügt

Eine Authentifizierungsstartklasse (**App\_Start/Startup.Auth.cs**) wurde Ihrem Projekt hinzugefügt. Sie enthält Startlogik für die Azure AD-Authentifizierung.

##### Ihrem Projekt wurde Startcode hinzugefügt

Wenn Sie bereits eine Startklasse in Ihrem Projekt verwendet haben, wurde die Methode **Configuration()** so aktualisiert, dass sie einen Aufruf von `ConfigureAuth(app)` enthält. Andernfalls wurde Ihrem Projekt eine Startklasse hinzugefügt.

##### Ihre Datei **app.config** oder **web.config** weist neue Konfigurationswerte auf.

Die folgenden Konfigurationseinträge wurden hinzugefügt.

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### Eine Azure AD-App wurde erstellt

Eine Azure AD-Anwendung wurde in dem Verzeichnis erstellt, das Sie im Assistenten ausgewählt haben.

[Weitere Informationen zu Azure Active Directory][Weitere Informationen zu Azure Active Directory]

  [Erste Schritte]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [Was ist passiert?]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Weitere Informationen zu Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
