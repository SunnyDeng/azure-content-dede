<properties 
	pageTitle="Fehler während der Authentifizierungserkennung" 
	description="Der Verbindungs-Assistent für Active Directory hat einen inkompatiblen Authentifizierungstyp erkannt." 
	services="active-directory" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="tarcher"/>

# Fehler während der Authentifizierungserkennung

Beim Erkennen des vorherigen Authentifizierungscodes hat der Assistent einen nicht kompatiblen Authentifizierungstyp erkannt.

##Was wird überprüft?

**Hinweis:** Um vorherigen Authentifizierungscode in einem Projekt ordnungsgemäß erkennen zu können, muss das Projekt erstellt werden. Wenn dieser Fehler auftritt und in Ihrem Projekt kein vorheriger Authentifizierungscode enthalten ist, erstellen Sie Ihr Projekt neu, und versuchen Sie es nochmals.

###Projekttypen

Der Assistent überprüft, welche Art von Projekt Sie entwickeln, um die richtige Authentifizierungslogik in das Projekt einzufügen. Bei einem Domänencontroller, der im Projekt von `ApiController` abgeleitet wird, gilt es als WebAPI-Projekt. Wenn nur Domänencontroller vorhanden sind, die im Projekt von `MVC.Controller` abgeleitet sind, wird es als MVC-Projekt betrachtet. Alles andere wird vom Assistenten als nicht unterstützt betrachtet. WebForms-Projekte werden derzeit nicht unterstützt.

###Kompatibler Authentifizierungscode

Der Assistent überprüft auch Authentifizierungseinstellungen, die zuvor mit dem Assistenten konfiguriert wurden oder mit dem Assistenten kompatibel sind. Wenn alle Einstellungen vorhanden sind, gilt dies als eintrittsinvarianter Fall, und der Assistent wird geöffnet und zeigt die Einstellungen an. Wenn nur einige der Einstellungen vorhanden sind, wird es als Fehlerfall betrachtet.

In einem MVC-Projekt überprüft der Assistent die folgenden Einstellungen, die aus der vorherigen Verwendung des Assistenten resultieren:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

Außerdem überprüft der Assistent die folgenden Einstellungen in einem Web-API-Projekt, die aus der vorherigen Verwendung des Assistenten resultieren:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

###Nicht kompatibler Authentifizierungscode

Der Assistent versucht schließlich, Versionen von Authentifizierungscode zu erkennen, die mit früheren Versionen von Visual Studio konfiguriert wurden. Wenn Sie diesen Fehler erhalten, ist ein nicht kompatibler Authentifizierungstyp in Ihrem Projekt vorhanden. Der Assistent erkennt die folgenden Authentifizierungstypen aus früheren Versionen von Visual Studio:

* Windows-Authentifizierung 
* Einzelne Benutzerkonten 
* Organisationskonten 
 

Zum Erkennen der Windows-Authentifizierung in einem MVC-Projekt sucht der Assistent nach dem `authentication`-Element aus Ihrer Datei **web.config**.

<pre>
	&lt;configuration>
	    &lt;system.web>
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /></span>
	    &lt;/system.web>
	&lt;/configuration>
</pre>

Zum Erkennen der Windows-Authentifizierung in einem Web-API-Projekt sucht der Assistent nach dem `IISExpressWindowsAuthentication`-Element aus der **CSPROJ**-Datei Ihres Projekts:

<pre>
	&lt;Project>
	    &lt;PropertyGroup>
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication>enabled&lt;/IISExpressWindowsAuthentication></span>
	    &lt;/PropertyGroup>
	&lt;/Project>
</pre>

Zum Erkennen der Authentifizierung einzelner Benutzerkonten sucht der Assistent nach dem Paketelement aus Ihrer Datei **Packages.config**.

<pre>
	&lt;packages>
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
	&lt;/packages>
</pre>

Zum Erkennen der alten Form von Organisationskontoauthentifizierung sucht der Assistent nach dem folgenden Element aus der Datei **web.config**:

<pre>
	&lt;configuration>
	    &lt;appSettings>
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /></span>
	    &lt;/appSettings>
	&lt;/configuration>
</pre>

Wenn Sie den Authentifizierungstyp ändern möchten, entfernen Sie den inkompatiblen Authentifizierungstyp, und führen Sie den Assistenten dann erneut aus.

Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD](active-directory-authentication-scenarios.md).
 

<!---HONumber=Nov15_HO3-->