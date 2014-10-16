<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Was ist passiert?

Ihrem Projekt wurden Verweise hinzugefügt.

###### NuGet-Paketverweise

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System.IdentityModel.Tokens.Jwt

###### .NET-Verweise

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System, System.Data, System.Drawing, System.IdentityModel, System.IdentityModel.Tokens.Jwt, System.Runtime.Serialization

###### Ihrem Projekt wurden Codedateien hinzugefügt

Eine Authentifizierungsstartklasse (**App_Start/Startup.Auth.cs**) wurde Ihrem Projekt hinzugefügt. Sie enthält Startlogik für die Azure AD-Authentifizierung. Außerdem wurde eine Controllerklasse (**Controllers/AccountController.cs**) hinzugefügt, die **SignIn()**- und **SignOut()**-Methoden enthält. Schließlich wurde noch eine Teilansicht (**Views/Shared/_LoginPartial.cshtml**) hinzugefügt, die einen Aktionslink für **SignIn/SignOut** enthält.

###### Ihrem Projekt wurde Startcode hinzugefügt

Wenn Sie bereits eine Startklasse in Ihrem Projekt verwendet haben, wurde die Methode **Configuration()** so aktualisiert, dass sie einen Aufruf von **ConfigureAuth(app)** enthält. Andernfalls wurde Ihrem Projekt eine Startklasse hinzugefügt.

###### Ihre Datei **app.config** oder **web.config** weist neue Konfigurationswerte auf

Die folgenden Konfigurationseinträge wurden hinzugefügt.
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

###### Eine Azure Active Directory-App (AD) wurde erstellt
Eine Azure AD-Anwendung wurde in dem Verzeichnis erstellt, das Sie im Assistenten ausgewählt haben.

## Erste Schritte mit Azure Active Directory (AD)

Mit dem hinzugefügten Code können Sie die folgenden Aufgaben ausführen.

###### Erfordern von Authentifizierung für den Zugriff auf Controller

Alle Controller in Ihrem Projekt wurden mit dem Attribut **[Authorize]** versehen. Dieses Attribut erfordert, dass der Benutzer vor dem Zugriff auf diese Controller authentifiziert werden muss. Wenn Sie anonymen Zugriff auf diesen Controller erlauben möchten, entfernen Sie dieses Attribut vom Controller.

###### Hinzufügen von SignIn-/SignOut-Steuerelementen

Wenn Sie Ihrer Ansicht **SignIn**-/**SignOut**-Steuerelemente hinzufügen möchten, können Sie die Teilansicht **\_LoginPartial.cshtml** zum Hinzufügen der Funktion zu einer Ihrer Ansichten verwenden. Das folgende Beispiel zeigt die Funktion, die der Standardansicht **\_Layout.cshtml** hinzugefügt wurde:
	<pre> 
	`<!DOCTYPE html> 
	<html> 
	<head> 
	    <meta charset="utf-8" /> 
	    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
	    <title>@ViewBag.Title - My ASP.NET Application</title> 
	    @Styles.Render("~/Content/css") 
	    @Scripts.Render("~/bundles/modernizr") 
	</head> 
	<body> 
	    <div class="navbar navbar-inverse navbar-fixed-top"> 
	        <div class="container"> 
	            <div class="navbar-header"> 
	                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                </button> 
	                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
	            </div> 
	            <div class="navbar-collapse collapse"> 
	                <ul class="nav navbar-nav"> 
	                    <li>@Html.ActionLink("Home", "Index", "Home")</li> 
	                    <li>@Html.ActionLink("About", "About", "Home")</li> 
	                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li> 
	                </ul> 
	                @Html.Partial("_LoginPartial") 
	            </div> 
	        </div> 
	    </div> 
	    <div class="container body-content"> 
	        @RenderBody() 
	        <hr /> 
	        <footer> 
	            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p> 
	        </footer> 
	    </div> 
	    @Scripts.Render("~/bundles/jquery") 
	    @Scripts.Render("~/bundles/bootstrap") 
	    @RenderSection("scripts", required: false) 
	</body> 
	</html>` </pre>