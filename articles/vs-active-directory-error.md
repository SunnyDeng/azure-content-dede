<properties title="Error During Authentication Detection" pageTitle="Fehler während der Authentifizierungserkennung" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />
  
<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

###Fehler während der Authentifizierungserkennung 
Beim Erkennen des vorherigen Authentifizierungscodes hat der Assistent einen nicht kompatiblen Authentifizierungstyp erkannt.   

###Was wird überprüft?

####Projekttypen

Der Assistent überprüft, welche Art von Projekt Sie entwickeln, um die richtige Authentifizierungslogik in das Projekt einzufügen.  Bei einem Domänencontroller, der im Projekt von "ApiController" abgeleitet wird, gilt es als WebAPI-Projekt.  Wenn nur Domänencontroller vorhanden sind, die im Projekt von "MVC.Controller" abgeleitet sind, wird es als MVC-Projekt betrachtet.  Alles andere wird vom Assistenten als nicht unterstützt betrachtet.  WebForms-Projekte werden zurzeit nicht unterstützt.

#####Kompatibler Authentifizierungscode

Der Assistent überprüft auch Authentifizierungseinstellungen, die zuvor mit dem Assistenten konfiguriert wurden oder mit dem Assistenten kompatibel sind.  Wenn alle Einstellungen vorhanden sind, gilt dies als eintrittsinvarianter Fall, und der Assistent wird geöffnet und zeigt die Einstellungen an.  Wenn nur einige der Einstellungen vorhanden sind, wird es als Fehlerfall betrachtet.

In einem MVC-Projekt überprüft der Assistent die folgenden Einstellungen, die aus der vorherigen Verwendung des Assistenten resultieren:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

Außerdem überprüft der Assistent die folgenden Einstellungen in einem Web-API-Projekt, die aus der vorherigen Verwendung des Assistenten resultieren:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

#####Nicht kompatibler Authentifizierungscode

Der Assistent versucht schließlich, Versionen von Authentifizierungscode zu erkennen, die mit früheren Versionen von Visual Studio konfiguriert wurden. Wenn Sie diesen Fehler erhalten, ist ein nicht kompatibler Authentifizierungstyp in Ihrem Projekt vorhanden. Der Assistent erkennt die folgenden Authentifizierungstypen aus früheren Versionen von Visual Studio:

* Windows-Authentifizierung 
* Einzelne Benutzerkonten 
* Organisationskonten 
 

Zum Erkennen von Windows-Authentifizierung in einem MVC-Projekt sucht der Assistent nach dem Element "authentication" aus Ihrer Datei **web.config**.

<PRE class="prettyprint">
	&lt;configuration&gt;
	    &lt;system.web&gt;
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
	    &lt;/system.web&gt;
	&lt;/configuration&gt;
</pre>

Zum Erkennen von Windows-Authentifizierung in einem Web-API-Projekt sucht der Assistent nach dem Element "IISExpressWindowsAuthentication" aus Ihrer Datei **.csproj** des Projekts:

<PRE class="prettyprint">
	&lt;Project&gt;
	    &lt;PropertyGroup&gt;
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
	    &lt;/PropertyGroup>
	&lt;/Project&gt;
</PRE>

Zum Erkennen der Authentifizierung einzelner Benutzerkonten sucht der Assistent nach dem Paketelement aus Ihrer Datei **Packages.config**.

<PRE class="prettyprint">
	&lt;packages&gt;
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
	&lt;/packages&gt;
</PRE>

Zum Erkennen der alten Form von Organisationskontoauthentifizierung sucht der Assistent nach dem folgenden Element aus der Datei **web.config**:

<PRE class="prettyprint">
	&lt;configuration*gt;
	    &lt;appSettings&gt;
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
</PRE>

Wenn Sie den Authentifizierungstyp ändern möchten, entfernen Sie den inkompatiblen Authentifizierungstyp, und führen Sie den Assistenten dann erneut aus.

Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD](http://msdn.microsoft.com/library/azure/dn499820.aspx).
