<properties title="Fehler w&auml;hrend der Authentifizierungserkennung" pageTitle="Fehler w&auml;hrend der Authentifizierungserkennung" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Fehler während der Authentifizierungserkennung

Beim Erkennen des vorherigen Authentifizierungscodes hat der Assistent einen nicht kompatiblen Authentifizierungstyp erkannt.

##### Was wird überprüft?

Der Assistent versucht, Versionen von Authentifizierungscode zu erkennen, die mit früheren Versionen von Visual Studio konfiguriert wurden. Wenn Sie diesen Fehler erhalten, ist ein nicht kompatibler Authentifizierungstyp in Ihrem Projekt vorhanden. Der Assistent erkennt die folgenden Authentifizierungstypen aus früheren Versionen von Visual Studio:

-   Windows-Authentifizierung
-   Einzelne Benutzerkonten
-   Organisationskonten

Zum Erkennen von Windows-Authentifizierung in einem MVC-Projekt sucht der Assistent nach dem Element `authentication` aus Ihrer Datei **web.config**.

<pre class="prettyprint">
    &lt;configuration&gt;
        &lt;system.web&gt;
            &lt;authentication mode=&quot;Windows&quot; /&gt;
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Zum Erkennen von Windows-Authentifizierung in einem Web-API-Projekt sucht der Assistent nach dem Element `IISExpressWindowsAuthentication` aus Ihrer Datei **CSPROJ** des Projekts:

<pre class="prettyprint">
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            &lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
        &lt;/PropertyGroup&gt;
    &lt;/Project&gt;
</pre>

Zum Erkennen der Authentifizierung einzelner Benutzerkonten sucht der Assistent nach dem Paketelement aus Ihrer Datei **Packages.config**.

<pre class="prettyprint">
    &lt;packages&gt;
        &lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
    &lt;/packages&gt;
</pre>

Zum Erkennen der alten Form von Organisationskontoauthentifizierung sucht der Assistent nach dem folgenden Element aus der Datei **web.config**:

<pre class="prettyprint">
    &lt;configuration*gt;
        &lt;appSettings&gt;
            &lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Wenn Sie den Authentifizierungstyp ändern möchten, entfernen Sie den inkompatiblen Authentifizierungstyp, und führen Sie den Assistenten dann erneut aus.

Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD][Authentifizierungsszenarien für Azure AD].

  [Authentifizierungsszenarien für Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx
