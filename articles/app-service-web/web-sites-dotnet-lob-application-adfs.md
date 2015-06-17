<properties 
	pageTitle="Erstellen einer .NET MVC-Web-App in Azure App Service mit Azure Active Directory-Authentifizierung" 
	description="Erfahren Sie, wie Sie eine ASP.NET MVC-Branchenanwendung in Azure App Service-Web-Apps erstellen, die mit lokalem STS authentifiziert wird. Für dieses Lernprogramm gilt AD FS als lokaler STS." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="04/09/2015" 
	ms.author="cephalin"/>

# Erstellen einer .NET MVC-Web-App in Azure App Service mit Azure Active Directory-Authentifizierung

In diesem Artikel erfahren Sie, wie Sie zum Erstellen einer ASP.NET MVC-Line-of-Business-Anwendung in [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) mit einem lokalen [Active Directory Federation Services](http://technet.microsoft.com/library/hh831502.aspx) als Identitätsanbieter. Dieses Szenario kann funktionieren, wenn Sie Branchenanwendungen in Azure App Service-Web-Apps erstellen möchten, Ihre Organisation jedoch verlangt, alle Daten vor Ort zu speichern.

>[AZURE.NOTE]Einen Überblick über die unterschiedlichen Enterprise Authentifizierung und Autorisierung Optionen für Webanwendungen in Azure-App-Dienst finden Sie unter [Verwenden Sie Active Directory für die Authentifizierung in Azure-Anwendungsdiensts](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Was Sie erstellen ##

Sie erstellen eine grundlegende ASP.NET-Anwendung in Azure App Service-Web-Apps mit den folgenden Features:

- Authentifizieren von Benutzern bei AD FS
- Verwendet `[Authorize]` zum Autorisieren von Benutzern für verschiedene Aktionen
- Statische Konfiguration für das Debuggen in Visual Studio und das Veröffentlichen in App Service-Web-Apps (einmal konfigurieren, jederzeit debuggen und veröffentlichen)  

<a name="bkmk_need"></a>
## Was Sie benötigen ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Sie benötigen Folgendes zum Bearbeiten dieses Lernprogramms:

- Eine lokale AD FS-Bereitstellung (eine End-to-End-Exemplarische Vorgehensweise der Testumgebung, die ich verwende, finden Sie unter [Testumgebung: eigenständige STS mit AD FS in Azure-VM (Test)](TODO))
- Berechtigungen zum Erstellen von Vertrauensstellungen der vertrauenden Seite in AD FS-Verwaltung
- Visual Studio 2013
- [Azure SDK 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) oder höher

<a name="bkmk_sample"></a>
## Verwenden Sie Beispielanwendung für Line-of-Business-Vorlage ##

Die Beispielanwendung in diesem Lernprogramm [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), wird durch das Azure Active Directory-Team erstellt. Da AD FS WS-Federation unterstützt, können Sie es ohne Weiteres als Vorlage zum Erstellen neuer Branchenanwendungen verwenden. Es bietet die folgenden Features:

- Verwendet [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) zur Authentifizierung mit einem lokalen AD FS-Bereitstellung
- Funktionen zum An- und Abmelden
- Verwendet [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (anstelle von Windows Identity Foundation, d. h. WIF), ist die Zukunft von ASP.NET und viel einfacher, die für die Authentifizierung und Autorisierung als WIF einrichten

<a name="bkmk_setup"></a>
## Einrichten der Beispielanwendung ##

2.	Klonen oder Herunterladen die Beispielprojektmappe am [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) in Ihr lokales Verzeichnis.

	> [AZURE.NOTE]Die Anweisungen unter ["Readme.MD"](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) zeigt, wie Sie die Anwendung in Azure Active Directory eingerichtet, aber in diesem Lernprogramm Sie es mit AD FS richten so gehen Sie hier stattdessen.

3.	Öffnen Sie die Projektmappe, und öffnen Sie controllers\\accountcontroller in die **Projektmappen-Explorer**.

	Sie sehen, dass der Code einfach eine Authentifizierungsaufforderung ausgibt, um den Benutzer mithilfe des WS-Verbunds zu authentifizieren. Die gesamte Authentifizierung wird in der Datei "App_Start\\Startup.Auth.cs" konfiguriert.

4.  Öffnen Sie die Datei "App_Start\\Startup.Auth.cs". In der `ConfigureAuth` -Methode, beachten Sie die Zeile:

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	Im OWIN-Zusammenhang beschränkt sich der Aufwand zur Konfiguration der WS-Verbund-Authentifizierung auf dieses absolute Minimum. Der Vorgang ist wesentlich einfacher und eleganter als bei WIF, wo in die Datei "Web.config" überall XML-Code eingefügt werden muss. Die einzige Information, die Sie benötigen, sind die ID der vertrauenden Seite (RP) und die URL der Metadatendatei Ihres AD FS-Diensts. Hier sehen Sie ein Beispiel:

	-	RP-Bezeichner: `https://contoso.com/MyLOBApp`
	-	Metadaten-Adresse: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	Ändern Sie in der Datei "App_Start\\Startup.Auth.cs" die Definitionen für statische Zeichenfolgen wie unten hervorgehoben:
	<pre class="prettyprint">
Private statische Zeichenfolge Realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
<mark><del>private static String AadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
<mark><del>Private statische Zeichenfolge Mandanten ConfigurationManager.AppSettings["ida:Tenant ="];</del></mark>
<mark><del>Metadaten für private statische Zeichenfolge = Zeichenfolge. Format ("{0} / \ {1\} /federationmetadata/2007-06/federationmetadata.xml", AadInstance, Mandanten);</del></mark>
<mark>Metadaten für private statische Zeichenfolge = Zeichenfolge. Format ("https:// {0} /federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>

<mark><del>Behörde String = String.Format (CultureInfo.InvariantCulture, AadInstance, Mandanten);</del></mark>
</pre>

6.	Nehmen Sie jetzt die entsprechenden Änderungen in der Datei "Web.config" vor. Öffnen Sie die Datei "Web.config", und ändern Sie die App-Einstellungen wie unten dargestellt:
	<pre class="prettyprint">
&lt;appSettings>
  &lt; fügen Sie Key = "Webpages:Version" Value = "3.0.0.0" / >
  &lt; fügen Sie Key = "Webseiten: aktiviert" Wert = "false" / >
  &lt; fügen Sie Key = "ClientValidationEnabled" Value = "true" / >
  &lt; fügen Sie Key = "UnobtrusiveJavaScriptEnabled" Value = "true" / >
  <mark><del>&lt; fügen Sie Key = "Ida: Wtrealm" Value = "[App-ID-URI der WebApp-WSFederation-DotNet-https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet eingeben]" / ></del></mark>
  <mark><del>&lt; fügen Sie Key = "Ida: AADInstance" Value = "https://login.windows.net" / ></del></mark>
  <mark><del>&lt; fügen Sie Key = "Ida: Mandant" Value = "[EINGABETASTE Mandantenname, z. B.:" contoso.onmicrosoft.com"]" / ></del></mark>
  <mark>&lt; fügen Sie Schlüssel = "Ida: RPIdentifier" Value = "[Geben Sie den relying Party Bezeichner entsprechend der Konfiguration in AD FS, z. B. Https://localhost:44320 /]" / ></mark>
  <mark>&lt; fügen Sie Key = "Ida: ADFS" Value = "[eingeben des FQDN des AD FS-Dienst, z. B. adfs.contoso.com]" / ></mark>

&lt;/appSettings>
</pre>Tragen Sie die Schlüsselwerte entsprechend Ihrer jeweiligen Umgebung ein.

7.	Erstellen Sie die Anwendung, um sicherzustellen, dass keine Fehler vorliegen.

Das ist alles. Jetzt kann die Beispielanwendung mit AD FS arbeiten. Später müssen Sie für diese Anwendung noch eine Vertrauensstellung der vertrauenden Seite in AD FS konfigurieren.

<a name="bkmk_deploy"></a>
## Bereitstellen der Beispielanwendung in Azure App Service-Web-Apps

Sie veröffentlichen hier eine Anwendung in einer Web-App in App Service-Web-Apps und behalten dabei die Debugumgebung bei. Beachten Sie, dass Sie die Anwendung veröffentlichen, bevor sie eine RP-Vertrauensstellung bei AD FS hat. Daher funktioniert die Authentifizierung noch nicht. Wenn Sie dies jedoch jetzt tun, erhalten Sie die URL der Web-App, die Sie später auch zum Konfigurieren der RP-Vertrauensstellung verwenden.

1. Mit der rechten Maustaste in des Projekts, und wählen Sie **Veröffentlichen**.

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. Wählen Sie **Microsoft Azure-Webanwendungen**.
3. Wenn Sie sich nicht bei Azure angemeldet haben, klicken Sie auf **Anmelden**, und verwenden Sie zum Anmelden das Microsoft-Konto für Ihr Azure-Abonnement.
4. Klicken Sie nach der Anmeldung **Neu** eine neue Webanwendung erstellen.
5. Füllen Sie alle erforderlichen Felder aus. Da Sie später eine Verbindung mit lokalen Daten herstellen, erstellen Sie keine Datenbank für diese Web-App.

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. Klicken Sie auf **Erstellen**. Nachdem die Web-App erstellt wurde, wird das Dialogfeld "Web veröffentlichen" geöffnet.
7. In **Ziel-URL**, ändern Sie **http** auf **Https**. Kopieren Sie die gesamte URL in einen Text-Editor. Sie werden sie später verwenden. Klicken Sie dann auf **Veröffentlichen**.

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. Öffnen Sie in Visual Studio **Web.Release.config** in Ihrem Projekt. Fügen Sie den folgenden XML-Code in den `<configuration>` tag, und Ersetzen Sie den Schlüsselwert mit der URL für Ihre Webanwendung veröffentlichen.
	<pre class="prettyprint">
&lt;appSettings>
   &lt; hinzufügen Key = "Ida: RPIdentifier" Value = "<mark>[z. B. https://mylobapp.azurewebsites.net/]</mark>" xdt: Transform = "SetAttributes" xdt:Locator="Match(key)" / >
&lt;/appSettings></pre>

Danach sind in Ihrem Projekt zwei RP-IDs konfiguriert, eine für Ihre Debugumgebung in Visual Studio und eine für die veröffentlichte Web-App in Azure. Für beide Umgebungen wird in AD FS eine RP-Vertrauensstellung eingerichtet. Während des Debuggens die Anwendungseinstellungen in der Datei Web.config verwendet, um Ihre **Debuggen** Konfigurationsarbeit mit AD FS und bei der Veröffentlichung (standardmäßig die **Release** Konfiguration veröffentlicht wird), eine transformierte Web.config-Datei hochgeladen wird, die die app Einstellungsänderungen in Web.Release.config enthält.

Wenn Sie die in Azure veröffentlichte Web-App dem Debugger anfügen möchten (d. h., Sie müssen Debugsymbole Ihres Codes in der veröffentlichten Web-App hochladen), können Sie einen Klon der Debug-Konfiguration für das Debuggen von Azure erstellen, jedoch mit einer eigenen benutzerdefinierten "Web.config"-Transformation (z. B. "Web.AzureDebug.config"), die die Anwendungseinstellungen der Datei "Web.Release.config" verwendet. Auf diese Weise können Sie eine statische Konfiguration in verschiedenen Umgebungen beibehalten.

<a name="bkmk_rptrusts"></a>
## Konfigurieren von Vertrauensstellungen der vertrauenden Seite in der AD FS-Verwaltung ##

Jetzt müssen Sie eine RP-Vertrauensstellung in der AD FS-Verwaltung konfigurieren, bevor sich Ihre Beispielanwendung tatsächlich mit AD FS authentifizieren kann. Sie müssen zwei separate RP-Vertrauensstellungen einrichten, eine für Ihre Debugumgebung und eine für die veröffentlichte Web-App.

> [AZURE.NOTE]Wiederholen Sie die folgenden Schritte für beide Umgebungen.

4.	Melden Sie sich mit Anmeldeinformationen, die Berechtigungen zur Verwaltung von AD FS umfassen, auf dem AD FS-Server an.
5.	Öffnen Sie die AD FS-Verwaltung. Mit der rechten Maustaste **AD FS\\Trusted Relationships\\Relying Vertrauensstellungen** und wählen Sie **Partei Vertrauensstellung der vertrauenden Seite hinzufügen**.

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	In der **Auswählen einer Datenquelle** Seite **Daten über die vertrauende Seite manuell eingeben**.

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	In der **Angeben des Anzeigenamens** Seite, geben Sie einen Anzeigenamen für die Anwendung aus, und klicken Sie auf **Weiter**.
7.	In der **Protokoll auswählen** auf **Weiter**.
8.	In der **Zertifikat konfigurieren** auf **Weiter**.

	> [AZURE.NOTE]Da Sie HTTPS bereits verwenden sollten, sind verschlüsselte Token optional. Wenn Sie Token von AD FS auf dieser Seite verschlüsseln möchten, müssen Sie Ihrem Code auch Token-Entschlüsselungslogik hinzufügen. Weitere Informationen finden Sie unter [manuell konfigurieren von owin-WS-Verbund-Middleware und akzeptiert verschlüsselte Token](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
  
5.	Bevor Sie zum nächsten Schritt übergehen, benötigen Sie eine bestimmte Information aus Ihrem Visual Studio-Projekt. Beachten Sie in den Projekteigenschaften, die **SSL-URL** der Anwendung.

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	Sichern in AD FS-Verwaltung der **URL konfigurieren** auf der Seite der **Relying Party vertrauen Assistenten zum Hinzufügen von**, wählen Sie **Aktivieren Sie die Unterstützung für das WS-Federation Passive-Protokoll** und geben Sie die SSL-URL des Visual Studio-Projekts, das Sie im vorherigen Schritt notiert haben. Klicken Sie auf **Weiter**.

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	> [AZURE.NOTE]Die URL gibt an, auf welche Seite der Client nach der erfolgreichen Authentifizierung geleitet werden soll. Für die Debug-Umgebung wird <code>https://localhost:&lt;port&gt;/</code>. Für die veröffentlichte Web-App muss dies die Web-App-URL sein.

7.	In der **Konfigurieren von Bezeichnern** Seite, stellen Sie sicher, dass Ihr Projekt SSL-URL bereits aufgeführt ist, und klicken Sie auf **Weiter**. Klicken Sie auf **Weiter** ganz nach dem Abschluss des Assistenten mit Standardeinstellungen.

	> [AZURE.NOTE]Dieser Bezeichner wird in App_Start\\Startup.Auth.cs des Visual Studio-Projekts, anhand des Werts verglichen <code>WsFederationAuthenticationOptions.Wtrealm</code> während der Verbundauthentifizierung. Standardmäßig wird die URL der Anwendung aus dem vorherigen Schritt als RP-ID hinzugefügt.

8.	Damit ist die Konfiguration der RP-Anwendung für Ihr Projekt in AD FS abgeschlossen. Anschließend konfigurieren Sie diese Anwendung zum Senden die Ansprüche, die Ihre Anwendung benötigt. Die **Anspruchsregeln bearbeiten** Dialogfeld wird standardmäßig geöffnet, für die Sie am Ende des Assistenten, damit Sie sofort beginnen können. Wir wollen mindestens die folgenden Ansprüche konfigurieren (Schemas sind in Klammern angegeben):

	-	Name (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) – wird von ASP.NET zum Unterbrechen verwendet `User.Identity.Name`.
	-	Benutzerprinzipalname (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) – wird verwendet, um Benutzer in der Organisation eindeutig zu identifizieren.
	-	Gruppenmitgliedschaften als Rollen (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) – kann verwendet werden, mit `[Authorize(Roles="role1, role2,...")]` Ergänzung zum Autorisieren von Controllern und Aktionen. Tatsächlich ist dies nicht unbedingt die leistungsfähigste Methode für die Rollenautorisierung, insbesondere dann nicht, wenn die Active Directory-Benutzer normalerweise Hunderten von Sicherheitsgruppen angehören, denn dies entspricht Hunderten von Rollenansprüche im SAML-Token. Eine alternative Methode ist das bedingte Senden eines einzelnen Rollenanspruchs je nach Mitgliedschaft des Benutzers in einer bestimmten Gruppe. Allerdings werden wir die Methode im Rahmen dieses Lernprogramms einfach halten.
	-	Benennen Sie die ID (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) – für den Schutz vor Fälschung Überprüfung verwendet werden kann. Weitere Informationen zum Arbeiten mit der Überprüfung der Schutz vor Fälschung zu erleichtern, finden Sie die **Hinzufügen von Line-of-Business-Funktionen für die Beispielanwendung** im Abschnitt [Erstellen Sie eine .NET MVC-Webanwendung in Azure-App-Dienst mit Azure Active Directory-Authentifizierung](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud).

	> [AZURE.NOTE]Die Anspruchstypen, die Sie für Ihre Anwendung konfigurieren müssen, richten sich nach den Anforderungen Ihrer Anwendung. Die Liste der Ansprüche, die von Azure Active Directory-Anwendungen (d. h. RP Vertrauensstellungen) unterstützt, z. B. finden Sie unter [Token unterstützt und Anspruchstypen](http://msdn.microsoft.com/library/azure/dn195587.aspx).

8.	Klicken Sie im Dialogfeld "Anspruchsregeln bearbeiten" auf **Regel hinzufügen**.
9.	Die Ansprüche Name, UPN und Rolle konfigurieren, wie unten gezeigt, und klicken Sie auf **Fertig stellen**.

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	Anschließend erstellen Sie einen vorübergehenden Namen ID Anspruch mithilfe der Schritte im demonstriert [Bezeichner im SAML-Assertionen](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

9.	Klicken Sie auf **Regel hinzufügen** erneut.
10.	Wählen Sie **Senden von Ansprüchen mit benutzerdefinierter Regel** und klicken Sie auf **Weiter**.
11.	Fügen Sie die folgende regelsprache in die **benutzerdefinierte Regel** Feld, benennen Sie die Regel **pro-Sitzungs-ID** und klicken Sie auf **Fertig stellen**.  
	<pre class="prettyprint">
C1: [Typ == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp; &amp;
C2: [Typ == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
	= > () hinzufügen
		Speichern von = "_OpaqueIdStore",
		Typen = ("<mark>http://contoso.com/internal/sessionid</mark>"),
		Abfrage = "{0} \ {1\} \ {2\}; \ {3\}; \ {4\}",
		Param = "UseEntropy",
		Param = c1. Wert.
		Param = c1. OriginalIssuer,
		Param = "",
		Param = c2. Wert);
</pre>Die benutzerdefinierte Regel sollte wie folgt aussehen:

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	Klicken Sie auf **Regel hinzufügen** erneut.
10.	Wählen Sie **Transformieren eines eingehenden Anspruchs** und klicken Sie auf **Weiter**.
11.	Konfigurieren Sie die Regel, siehe unten (mit den Anspruchstyp, die Sie in die benutzerdefinierte Regel erstellt haben), und klicken Sie auf **Fertig stellen**.

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	Ausführliche Informationen zu den Schritten für die vorübergehende namens-ID-Anspruch oben finden Sie unter [Bezeichner im SAML-Assertionen](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

12.	Klicken Sie auf **Übernehmen** in den **Anspruchsregeln bearbeiten** Dialogfeld. Das Dialogfeld sollte jetzt wie im folgenden Screenshot aussehen:

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	> [AZURE.NOTE]Wiederholen Sie diese Schritte erneut für die Debugumgebung und die veröffentlichte Web-App.

<a name="bkmk_test"></a>
## Testen Sie die Verbundauthentifizierung für Ihre Anwendung.

Jetzt sind Sie bereit, die Authentifizierungslogik Ihrer Anwendung für AD FS zu testen. In meiner AD FS-Testumgebung habe ich einen Testbenutzer, der einer Testgruppe in Active Directory (AD) angehört.

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Um die Authentifizierung im Debugger zu testen, müssen Sie lediglich jetzt ist `F5`. Wenn Sie die Authentifizierung in der veröffentlichten Web-App testen möchten, navigieren Sie zu der URL.

Klicken Sie nach dem Laden der Webanwendung **Anmelden**. Je nach der von AD FS gewählten Authentifizierungsmethode wird nun ein Dialogfeld zum Anmelden oder die von AD FS bereitgestellte Anmeldeseite angezeigt. So sieht das im Internet Explorer 11 aus.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

Nachdem Sie sich mit einem Benutzer in der AD-Domäne von der AD FS-Bereitstellung anmelden, sollte jetzt angezeigt werden die Homepage erneut mit **Hello, <User Name>!** in der Ecke. So sieht das bei mir aus.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Bisher haben Sie Folgendes erreicht:

- Die Anwendung hat AD FS erreicht, und eine entsprechende RP-ID wird in der AD FS-Datenbank gefunden.
- AD FS hat einen Active Directory-Benutzer authentifiziert und leitet Sie zur Startseite der Anwendung zurück.
- AD FS als erfolgreich den Anspruch gesendet (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) zu Ihrer Anwendung, wie durch die Tatsache, dass der Benutzername in der Ecke angezeigt wird. 

Fehlt der Namensanspruch, Sie würde schon **Hallo!**. Wenn Sie views\\shared_loginpartial betrachten, werden Sie feststellen, verwendet `User.Identity.Name` der Benutzername angezeigt. Wie bereits erwähnt, füllt ASP.NET diese Eigenschaft mit den Namensanspruch des authentifizierten Benutzers, wenn dieser im SAML-Token verfügbar ist. Um alle Ansprüche zu sehen, die von AD FS gesendet werden, setzen Sie in der Datei "Controllers\\HomeController.cs" einen Haltepunkt in der Index-Aktionsmethode. Nachdem der Benutzer authentifiziert wurde, überprüfen Sie die `System.Security.Claims.Current.Claims` Auflistung.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png)

<a name="bkmk_authorize"></a>
## Autorisieren von Benutzern für bestimmte Domänencontroller oder Aktionen

Da Sie Gruppenmitgliedschaften als Rollenansprüche in der RP-Vertrauensstellung Konfiguration aufgenommen haben, jetzt können Sie diese direkt in die `[Authorize(Roles="...")]` Dekoration für Controller und Aktionen. In einer Line-of-Business-Anwendung mit dem Muster Create-Read-Update-Delete (CRUD) können Sie bestimmten Rollen Zugriff auf die einzelnen Aktionen gewähren. Vorläufig werden Sie diese Funktion auf dem vorhandenen Home-Controller lediglich testen.

1. Öffnen Sie die Datei "Controllers\\HomeController.cs".
2. Ergänzen Sie die `About` und `Contact` Aktionsmethoden ähnelt unten mit Sicherheit Gruppenmitgliedschaften, die Ihr authentifizierten Benutzer.  
	<pre class="prettyprint">
<mark>[Autorisieren (Rollen = "Test Group")]</mark>
public ActionResult About()
{
    ViewBag.Message = "Ihre Anwendung Beschreibungsseite.";

    return View();
}

<mark>[Autorisieren (Rollen = "Domänen-Admins")]</mark>
public ActionResult Contact()
{
    ViewBag.Message = "Your contact page.";

    return View();
}
</pre>Da ich fügte **Benutzer testen** auf **Testgruppe** in meiner Testumgebung von AD FS verwende ich Testgruppe zum Testen der Autorisierung auf `About`. Für `Contact`, bei negativen testen **Domänenadministratoren**, um die **Benutzer testen** gehört nicht.

3. Starten Sie den Debugger durch Eingabe `F5` anmelden, und klicken Sie auf **zu**. Sie sollten jetzt anzeigen der `~/About/Index` Seite erfolgreich, wenn der authentifizierte Benutzer für diese Aktion berechtigt ist.
4. Klicken Sie nun auf **Kontakt**, in meinem Fall sollte keine Autorisierung **Testbenutzer** für die Aktion. Allerdings wird der Browser zu AD FS umgeleitet, und schließlich wird diese Meldung angezeigt:

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	Wenn Sie diesen Fehler in der Ereignisanzeige auf dem AD FS-Server untersuchen, sehen Sie diese Ausnahmemeldung: <pre class="prettyprint"> Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>gleichen Clientbrowsersitzung hat "6" Anforderungen in den letzten "11" Sekunden.</mark> Details erhalten Sie von Ihrem Administrator. bei Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie (WrappedHttpListenerContext-Kontext) an Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse (WSFederationContext-Kontext, MSISSignInResponse-Antwort) am Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest (ProtocolContext ProtocolContext, PassiveProtocolHandler ProtocolHandler) unter Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext (WrappedHttpListenerContext-Kontext) </pre>

	Der Grund hierfür ist, dass MVC standardmäßig die Fehlermeldung "401 - Nicht autorisiert" zurückgibt, wenn die Rollen des Benutzers nicht autorisiert sind. Dadurch wird eine Anforderung zur erneuten Authentifizierung an Ihren Identitätsanbieter (AD FS) ausgelöst. Da der Benutzer bereits authentifiziert ist, kehrt AD FS zur selben Seite zurück- Diese gibt dann wiederum eine 401-Fehlermeldung aus, sodass eine Umleitungsschleife entsteht. Überschreiben Sie den AuthorizeAttribute `HandleUnauthorizedRequest` Methode mit einfachen Logik etwas angezeigt, die sinnvoll ist und nicht die Umleitung Schleife fortgesetzt.

5. Erstellen Sie im Projekt die Datei "AuthorizeAttribute.cs", und fügen Sie den folgenden Code ein.

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	Der überschreibende Code sendet die Fehlermeldung "HTTP 403 (Verboten)" anstelle von "HTTP 401 (Nicht autorisiert)", wenn zwar eine Authentifizierung, jedoch keine Autorisierung vorliegt.

6. Führen Sie den Debugger erneut mit `F5`. Auf **Kontakt** zeigt nun eine informativere (obgleich uninteressant) Fehlermeldung angezeigt:

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. Veröffentlichen Sie die Anwendung erneut in Azure App Service-Web-Apps, und testen Sie das Verhalten der Live-Anwendung.

<a name="bkmk_data"></a>
## Herstellen einer Verbindung mit lokalen Daten

Ein Grund dafür, Ihre Line-of-Business-Anwendung mit AD FS statt mit Azure Active Directory zu implementieren, sind Kompatibilitätsprobleme beim externen Speichern von Organisationsdaten. Dies kann auch bedeuten, dass es sich bei Ihrer Anwendung in Azure von lokalen Datenbanken zugreifen muss, da Sie nicht verwenden dürfen [SQL-Datenbank](/services/sql-database/) als die Datenebene für Ihre Webanwendungen.

Azure-App Service Web-Apps unterstützt den Zugriff auf lokale Datenbanken mit zwei Ansätze: [Hybridverbindungen](../integration-hybrid-connection-overview.md) und [virtuellen Netzwerken](web-sites-integrate-with-vnet.md). Weitere Informationen finden Sie unter [mithilfe von VNET-Integration und Hybrid-Verbindungen mit Azure App Service Web Apps](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>
## Weitere Ressourcen

- [Schützen der Anwendung durch SSL und das Attribut "Authorize"](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Verwenden Sie Active Directory für die Authentifizierung in Azure-App-Dienst](web-sites-authentication-authorization.md)
- [Erstellen Sie eine .NET MVC-Webanwendung in Azure-App-Dienst mit Azure Active Directory-Authentifizierung](web-sites-dotnet-lob-application-azure-ad.md)
- [Verwenden Sie die lokale Organisationsauthentifizierung-Option (ADFS) von ASP.NET in Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Vittorio Bertocci blog](http://blogs.msdn.com/b/vbertocci/)
- [Migrieren von einem Webprojekt VS2013 von WIF zu Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Übersicht über Active Directory-Verbunddienste](http://technet.microsoft.com/library/hh831502.aspx)
- [WS-Verbund-1.1-Spezifikation](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->