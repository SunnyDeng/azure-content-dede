<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Integrating Multi-Tenant Cloud Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# Integrieren von Cloud-Anwendungen für mehrere Mandanten in Azure Active Directory

## <a name="introduction"></a>Einführung

Azure Active Directory (Azure AD) ist ein moderner, REST-basierter Dienst, der Identitätsverwaltung und Funktionen zur Zugriffssteuerung für Cloudanwendungen bietet. Azure AD lasst sich ohne weiteres in Cloud-Dienste sowie Azure, Microsoft Office 365, Dynamics CRM Online und Windows Intune integrieren. Vorhandene lokale Active Directory-Bereitstellungen können Azure AD ebenfalls umfassend nutzen. Wenn Sie mehr erfahren möchten, rufen Sie die Seite [Azure: die Cloud-Plattform von Microsoft][Azure: die Cloud-Plattform von Microsoft] unter [windowsazure.com][windowsazure.com] auf.

Diese Anleitung ist für .NET-Entwickler bestimmt, die eine Anwendung für mehrere Mandanten in Azure AD integrieren möchten. Sie erhalten Informationen zu folgenden Themen:

-   Kunden die Möglichkeit geben, sich mit Azure AD für Ihre Anwendung anzumelden
-   Einmalige Anmeldung mit Azure AD ermöglichen
-   Verzeichnisdaten eines Kunden mit der Azure AD-Graph-API abfragen

Die zu dieser Anleitung gehörende Beispielanwendung können Sie [hier herunterladen][hier herunterladen]. Das Beispiel kann ohne Änderungen ausgeführt werden. Sie müssen jedoch möglicherweise die [Anschlusszuweisung in Visual Studio][Anschlusszuweisung in Visual Studio] ändern, um HTTPS verwenden zu können. Folgen Sie den Anweisungen im Link, legen Sie jedoch im Abschnitt "bindings" der Datei ApplicationHost.config "https" als Bindungsprotokoll fest. Alle Codeausschnitte in den folgenden Schritten stammen aus dem Beispiel.

> [WACOM.NOTE]
> Das Beispiel für eine Verzeichnis-App für mehrere Mandanten dient nur der Veranschaulichung. Dieses Beispiel (einschließlich der unterstützenden Bibliotheksklassen) sollte nicht in der Produktion verwendet werden.

### Voraussetzungen

Für diese Anleitung werden die folgenden Entwicklungsvoraussetzungen benötigt:

-   [Visual Studio 2012][Visual Studio 2012]
-   [WCF Data Services für OData][WCF Data Services für OData]

### Inhaltsverzeichnis

-   [Einführung][Einführung]
-   [Teil 1: Abrufen einer Client-ID für den Zugriff auf Azure AD][Teil 1: Abrufen einer Client-ID für den Zugriff auf Azure AD]
-   [Teil 2: Ermöglichen der Anmeldung mit Azure AD][Teil 2: Ermöglichen der Anmeldung mit Azure AD]
-   [Teil 3: Aktivieren der einmaligen Anmeldung][Teil 3: Aktivieren der einmaligen Anmeldung]
-   [Teil 4: Zugreifen auf Azure AD Graph][Teil 4: Zugreifen auf Azure AD Graph]
-   [Teil 5: Veröffentlichen der Anwendung][Teil 5: Veröffentlichen der Anwendung]
-   [Zusammenfassung][Zusammenfassung]

## <a name="getclientid"></a>Teil 1: Abrufen einer Client-ID für den Zugriff auf Azure AD

In diesem Abschnitt wird erläutert, wie Sie eine Client-ID und einen geheimen Clientschlüssel abrufen, nachdem Sie ein Konto für das Microsoft-Verkäuferdashboard erstellt haben. Eine Client-ID ist der eindeutige Bezeichner Ihrer Anwendung, und ein geheimer Clientschlüssel ist der zugeordnete Schlüssel, der zum Erstellen von Anforderungen mithilfe der Client-ID benötigt wird. Beides ist erforderlich, um Ihre Anwendung in Azure AD zu integrieren.

### Schritt 1: Erstellen eines Kontos mit dem Microsoft-Verkäuferdashboard

Um Anwendungen zu entwickeln und zu veröffentlichen, die sich in Azure AD integrieren lassen, müssen Sie ein Konto für [Microsoft-Verkäuferdashboard][Microsoft-Verkäuferdashboard] anmelden. Anschließend werden Sie aufgefordert, als Unternehmen oder als Privatperson [ein Kontoprofil zu erstellen][ein Kontoprofil zu erstellen]. Dieses Profil dient zum Veröffentlichen von Anwendungen auf dem Azure Marketplace oder auf anderen Marketplaces und wird zum Generieren von Client-ID und geheimem Clientschlüssel benötigt.

Neuen Konten wird der Status "Account Pending Approval" zugewiesen. Dieser Status hindert Sie nicht daran, mit der Entwicklung zu beginnen – Sie können weiterhin Client-IDs sowie Entwurfs-App-Einträge erstellen. Ein App-Eintrag kann jedoch erst nach der Genehmigung des Kontos selbst zur Genehmigung übermittelt werden. Kunden können den übermittelten App-Eintrag auf dem Azure Marketplace erst nach der Genehmigung sehen.

### Schritt 2: Abrufen einer Client-ID für Ihre Anwendung

Zur Integration Ihrer Anwendung in Azure AD werden Client-ID und geheimer Clientschlüssel benötigt. Eine Client-ID ist der eindeutige Bezeichner der Anwendung und dient hauptsächlich dazu, eine Anwendung für die einmalige Anmeldung oder für Authentifizierungsanrufe von Azure AD Graph zu identifizieren. Weitere Informationen zum Beziehen von Client-ID und geheimem Clientschlüssel finden Sie unter [Erstellen von Client-IDs und geheimen Clientschlüsseln im Microsoft-Verkäuferdashboard][Erstellen von Client-IDs und geheimen Clientschlüsseln im Microsoft-Verkäuferdashboard].

> [WACOM.NOTE]
> Da Ihre Client-ID und Ihr geheimer Clientschlüssel in dieser Anwendung später benötigt werden, sollten Sie sich beides notieren.

Um eine Client-ID und einen geheimen Clientschlüssel zu generieren, müssen Sie im Microsoft-Verkäuferdashboard die folgenden Eigenschaften eingeben:

**App-Domäne**: Der Hostname Ihrer Anwendung, z. B. "contoso.com". Diese Eigenschaft darf keine Portnummer enthalten. Während der Entwicklung sollte diese Eigenschaft auf "localhost" festgelegt werden.

**App Redirect URL**: Die Umleitungs-URL, an die Azure AD nach der Benutzeranmeldung und nach der Freigabe Ihrer Anwendung durch eine Organisation eine Antwort sendet; z. B.: "https://contoso.com/." Während der Entwicklung sollte diese Eigenschaft auf "https://localhost:&#60;portnummer&#62;" festgelegt werden.

### Schritt 3: Konfigurieren der Anwendung zur Verwendung der Client-ID und des geheimen Clientschlüssels

Für diesen Schritt werden die Client-ID und der geheime Clientschlüssel benötigt, die während der Einrichtung im Verkäuferdashboard generiert wurden. Die Client-ID ist für die einmalige Anmeldung bestimmt, und Client-ID sowie der geheime Clientschlüssel werden später verwendet, um ein Zugriffstoken zum Aufrufen der Azure AD Graph-API zu erhalten.

Die Beispielanwendung wurde vorab auf die Verwendung von Azure AD eingestellt und lädt die Client-ID sowie den geheimen Clientschlüssel aus der Konfiguration. Nehmen Sie in der Datei **Web.config** in der Beispielanwendung die folgenden Änderungen vor:

1.  Ersetzen Sie im Knoten **appSettings** die Werte für "clientId" und "SymmetricKey" durch die Client-ID, den geheimen Clientschlüssel und die App-Domäne:

        <appSettings>
            <add key="clientId" value="(Your Client ID value)"/>
            <add key="SymmetricKey" value="(Your Client Secret value)"/>
            <add key="AppHostname" value="(Your App Domain)"/>
        </appSettings>

2.  Fügen Sie im Knoten **audienceUris** von **system.identityModel** Ihre Client-ID nach "spn:" ein:

        <system.identityModel>
            <audienceUris>
                <add value="spn:(Your Client ID value)" />
            </audienceUris>

## <a name="enablesignup"></a>Teil 2: Ermöglichen der Anmeldung mit Azure AD

In diesem Abschnitt wird erläutert, wie Sie Kunden die Möglichkeit geben, sich mithilfe von Azure AD für Ihre Anwendung anzumelden. Bevor ein Kunde eine in Azure AD integrierte Anwendung verwenden kann, muss ein Mandantenadministrator die Anwendung autorisieren. Dieser Autorisierungsprozess beginnt mit einer Zustimmungsanforderung Ihrer Anwendung an Azure, wobei eine Antwort generiert wird, die von Ihrer Anwendung verarbeitet werden muss. Im Folgenden wird erläutert, wie eine Zustimmungsanforderung generiert und die Antwort verarbeitet wird.

Bei den Schritten in diesem Abschnitt werden Hilfsprogrammklassen aus der Beispielanwendung verwendet. Diese Klassen sind in der Bibliothek *Microsoft.IdentityModel.WAAD.Preview* des Beispiels enthalten und erleichtern die Konzentration auf den Anwendungscode anstatt auf die Besonderheiten von Identität und Protokoll.

### Schritt 1: Anfordern der Zustimmung für Ihre Anwendung

Die folgende Beispielinteraktion veranschaulicht das Anfordern der Zustimmung für Ihre Anwendung:

1.  Der Kunde klickt auf einer Webseite in Ihrer Anwendung auf einen Link zum Anmelden mithilfe von Azure AD.
2.  Sie leiten den Kunden zur Azure AD-Autorisierungsseite um, wobei die Informationen über Ihre Anwendung an die Anforderung gehängt werden.
3.  Der Kunde erteilt Ihrer Anwendung Zustimmung oder lehnt diese ab.
4.  Azure AD leitet den Kunden zu der von Ihnen festgelegten App-Umleitungs-URL um. Sie haben diese URL beim Generieren einer Client-ID und eines geheimen Clientschlüssels im Microsoft-Verkäuferdashboard festgelegt. Die Umleitungsanforderung zeigt das Ergebnis der Zustimmungsanforderung einschließlich Informationen über den Mandanten an, wenn die Zustimmung erteilt wurde.

Um die Umleitungsanforderung in Schritt 2 oben zu erzeugen, müssen Sie der folgenden URL für die Azure AD-Autorisierungsseite querystring-Parameter anhängen: *<http://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx>*

Die querystring-Parameter werden nachfolgend beschrieben:

**ApplicationID**: (Erforderlich) Der **ClientID**-Wert, den Sie im Verkäuferdashboard erhalten haben.

**RequestedPermissions**: (Optional) Die Berechtigungen, die Ihrer Anwendung vom Mandanten erteilt werden müssen.
Während der Entwicklung dienen diese Berechtigungen zum Testen von nicht veröffentlichten Anwendungen. Bei veröffentlichten Anwendungen wird dieser Parameter ignoriert. Stattdessen werden die angeforderten Berechtigungen in Ihrem Anwendungseintrag verwendet. Weitere Informationen über diesen Eintrag finden Sie in Teil 5.
Für diesen Parameter sind drei Werte möglich:

**DirectoryReader**: Erteilt die Berechtigung, Verzeichnisdaten wie Benutzerkonten, Gruppen und Informationen über Ihre Organisation zu lesen. Aktiviert die einmalige Anmeldung.

**UserAccountAdministrator**: Erteilt die Berechtigung, Daten wie Benutzer, Gruppen und Informationen über Ihre Organisation zu lesen und zu schreiben. Aktiviert die einmalige Anmeldung.

**None**: Aktiviert die einmalige Anmeldung, deaktiviert jedoch den Zugriff auf Verzeichnisdaten.

Der Standardwert lautet "None", wenn der Parameter nicht oder fehlerhaft festgelegt wird.

Es folgt ein Beispiel für eine gültige Zustimmungsanforderungs-URL:
*<https://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx?ApplicationId=33E48BD5-1C3E-4862-BA79-1C0D2B51FB26&RequestedPermissions=DirectoryReader>*

In der Beispielanwendung enthält der Link "Register" eine ähnliche URL für die Zustimmungsanforderung, wie unten dargestellt:

![Anmeldung][Anmeldung]

> [WACOM.NOTE]
> Wenn Sie Ihre nicht veröffentlichte Anwendung testen, durchlaufen Sie eine ähnliche Zustimmungsprozedur wie Ihre Kunden. Die Autorisierungsseite einer nicht veröffentlichten Anwendung sieht jedoch anders aus als die Autorisierungsseite einer veröffentlichten Anwendung. Eine veröffentlichte Anwendung zeigt App-Namen, Logo und Details zum Herausgeber an, eine nicht veröffentlichte Anwendung hingegen nicht.

### Schritt 2: Verarbeiten der Zustimmungsantwort

Nachdem ein Kunde Ihrer Anwendung die Zustimmung erteilt oder verweigert hat, sendet Azure AD eine Antwort an die App-Umleitungs-URL Ihrer Anwendung. Diese Antwort enthält die folgenden querystring-Parameter:

**TenantId**: Die eindeutige GUID für den Azure AD-Mandanten, von dem Ihre Anwendung autorisiert wurde. Dieser Parameter wird nur angegeben, wenn der Kunde Ihre Anwendung autorisiert hat.

**Consent**: Der Wert wird auf "Granted" festgelegt, wenn die Anwendung autorisiert wurde, oder auf "Denied", wenn die Anforderung abgelehnt wurde.

Es folgt ein Beispiel für eine gültige Antwort auf die Zustimmungsanforderung, die angibt, dass die Anwendung autorisiert wurde:
*<https://app.litware.com/redirect.aspx&TenantId=7F3CE253-66DB-4AEF-980A-D8312D76FDC2&Consent=Granted>*

Ihre Anwendung muss den Kontext pflegen, sodass die an die Azure AD-Autorisierungsseite gesendete Anforderung mit der Antwort verknüpft wird (und Antworten ohne zugeordnete Anforderung abgelehnt werden).

<div class="dev-callout"><strong>Hinweis</strong><p>Nachdem die Zustimmung erteilt wurde, ben&ouml;tigt Azure AD f&uuml;r die Bereitstellung der einmaligen Anmeldung und den Zugriff auf Graph eine gewisse Zeit. Der erste Benutzer eine Organisation, der sich f&uuml;r Ihre Anwendung anmeldet, sieht u.&nbsp;U. Anmeldefehler, bis die Bereitstellung abgeschlossen ist.</p></div>

Nachdem ein Kunde Ihrer Anwendung die Zustimmung erteilt hat, ist es wichtig, den neu erstellten Mandanten in Ihrer Anwendung mit der von der Zustimmungsantwort zurückgegebenen TenantId zuzuordnen und zu speichern. Die Beispielanwendung enthält ein *HttpModule* im Namespace *Microsoft.IdentityModel.WAAD.Preview.Consent*, das die TenantId bei allen erfolgreichen Zustimmungsantworten automatisch in einem Kunden-/TenantId-"Datenspeicher" aufzeichnet. Den Code dazu finden Sie unten; die Aufzeichnung der TenantId in einem Kunden-/TenantId-"Datenspeicher" erfolgt mithilfe der *TrustedIssuers.Add*-Methode:

    private void Application_BeginRequest(Object source,
             EventArgs e)
    {
        HttpApplication application = (HttpApplication)source;
        HttpRequest req = application.Context.Request;             

        if((!string.IsNullOrEmpty(req.QueryString["TenantId"]) && (!string.IsNullOrEmpty(req.QueryString["Consent"]))))
        { 
            if(req.QueryString["Consent"].Equals("Granted",StringComparison.InvariantCultureIgnoreCase))
            {
                // For this sample we store the consenting tenants in
                // an XML file. We strongly recommend that you change
                // this to use your DataStore
                TrustedIssuers.Add(req.QueryString["TenantId"]; 
            }
        }            
    }

Bevor Sie den Zustimmungsanforderungs-/Antwortcode für Ihre Anwendung testen können, müssen Sie einen Azure AD-Mandanten erhalten.

### Schritt 3: Erhalten eines Azure AD-Mandanten zum Testen Ihrer Anwendung

Um die Möglichkeit der Integration Ihrer Anwendung in Azure AD zu testen, benötigen Sie einen Azure AD-Mandanten. Wenn Sie bereits einen Mandanten haben, den Sie zum Testen einer anderen Anwendung verwenden, können Sie ihn wiederverwenden. Wir empfehlen, mindestens zwei Mandanten zu erhalten, damit Ihre App von mehreren Mandanten getestet und verwendet werden kann. Sie sollten für diesen Zweck einen Produktionsmandanten verwenden. [Erhalten eines Azure AD-Mandanten][Erhalten eines Azure AD-Mandanten].

Sobald Sie einen Azure AD-Mandanten erhalten haben, können Sie die Anwendung erstellen und ausführen, indem Sie **F5** drücken. Zusätzlich können Sie versuchen, sich mit dem neuen Mandanten bei Ihrer Anwendung anzumelden.

<div class="dev-callout"><strong>Hinweis</strong><p>Wenn sich Ihre Kunden f&uuml;r einen neuen Azure AD-Mandanten anmelden, kann es eine gewisse Zeit dauern, bis dieser Mandant vollst&auml;ndig bereitgestellt ist. Die Benutzer sehen m&ouml;glicherweise Fehlermeldungen auf der Zustimmungsseite, bis die Bereitstellung abgeschlossen ist.</p></div>

## <a name="enablesso"></a>Teil 3: Aktivieren der einmaligen Anmeldung

In diesem Abschnitt wird gezeigt wie Sie die einmalige Anmeldung aktivieren. Der Prozess beginnt mit der Erstellung einer Anmeldeanforderung an Azure AD, mit der ein Benutzer gegenüber Ihrer Anwendung authentifiziert wird. Anschließend wird die Anmeldeantwort verifiziert, dass der Kunde zu einem Mandanten gehört, der Ihre Anwendung autorisiert hat. Für die Anmeldeanforderung werden die Client-ID aus dem Verkäuferdashboard und die Mandanten-ID der Organisation des Kunden benötigt.

Die Anmeldeanforderung bezieht sich auf einen bestimmten Mandanten und muss eine Mandanten-ID enthalten. Eine Mandanten-ID kann anhand des Domänennamens eines Azure-AD-Mandanten bestimmt werden. Um diesen Domänennamen während der Anmeldung vom Endbenutzer zu erhalten, gibt es in der Regel zwei Möglichkeiten:

-   Wenn die URL der Anwendung *<https://contoso.myapp.com>* oder *<https://myapp.com/contoso.com>* lautet, stellen *contoso* und *contoso.com* den Azure AD-Domänennamen dar und *myapp.com* die URL Ihrer Anwendung.
-   Ihre Anwendung kann den Benutzer zur Angabe seiner E-Mail-Adresse oder des Azure AD-Domänennamens auffordern. Dieser Ansatz wird in der Beispielanwendung verwendet, in der der Benutzer den Azure AD-Domänenname eingeben muss, wie unten dargestellt ist:

![Anmeldung][Anmeldung]

### Schritt 1: Suchen der Mandanten-ID

Mit dem vom Kunden angegebenen Azure AD-Domänennamen können Sie seine Mandanten-ID durch Analysieren der Metadaten des Azure AD-Verbunds suchen. In der Beispielanwendung wird dieser Prozess von der *Domain2TenantId*-Methode der *Microsoft.IdentityModel.WAAD.Preview.TenantUtils.Globals*-Klasse durchgeführt.

Zur Darstellung dieses Prozesses wird in den folgenden Schritten der Domänenname contoso.com verwendet.

1.  Rufen Sie die Datei **FederationMetadata.xml** für den Azure AD-Mandanten ab. Beispiel:
    *<https://accounts.accesscontrol.windows.net/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml>*
2.  Suchen Sie in der Datei **FederationMetadata.xml** den Eintrag **EntityDescriptor**. Die Mandanten-ID ist in der Eigenschaft **entityID** nach "<https://sts.windows.net>" enthalten (siehe unten):

         <EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sts.windows.net/a7456b11-6fe2-4e5b-bc83-67508c201e4b/" ID="_cba45203-f8f4-4fc3-a3bb-0b136a2bafa5"> 

    In diesem Fall lautet die Mandanten-ID **a7456b11-6fe2-4e5b-bc83-67508c201e4b**.

3.  Im Kunden-/TenantId-"Datenspeicher" Ihrer Anwendung sollten Sie die Domäne und die zugeordnete Mandanten-ID speichern. Diese beiden Werte können zusammen für künftige Anmeldeanforderungen verwendet werden und machen es überflüssig, die Datei **FederationMetadata.xml** jedes Mal aufzurufen. Die Beispielanwendung verfügt nicht über diese Optimierung.

### Schritt 2: Generieren der Anmeldeanforderung

Wenn sich ein Kunde bei Ihrer Anwendung anmeldet, z. B. durch Klicken auf eine Anmeldeschaltfläche, muss die Anmeldeanforderung mithilfe der Mandanten-ID des Kunden und der Client-ID Ihrer Anwendung generiert werden. In der Beispielanwendung wird diese Anforderung von der *GenerateSignInMessage*-Methode der *Microsoft.IdentityModel.WAAD.Preview.WebSSO.URLUtils*-Klasse generiert. Diese Methode verifiziert, ob die Mandanten-ID des Kunden eine Organisation darstellt, von der Ihre Anwendung autorisiert wurde, und generiert die Ziel-URL für die Anmeldeschaltfläche, wie im Folgenden dargestellt:

![Anmeldung][Anmeldung]

Durch Klicken auf die Schaltfläche wird der Browser des Benutzers zu einer Anmeldeseite für Azure AD geleitet. Nach der Anmeldung sendet Azure AD eine Anmeldeantwort an die Anwendung zurück.

### Schritt 3: Validieren des Ausstellers des eingehenden Tokens in der Anmeldeantwort

Wenn sich ein Kunde bei Ihrer Anwendung anmeldet, müssen Sie validieren, ob sein Mandant Ihre Anwendung autorisiert hat. Seine Anmeldeantwort enthält ein Token, und das Token enthält Eigenschaften und Ansprüche, die von Ihrer Anwendung geprüft werden können.

Um diese Validierung durchzuführen, müssen Sie die Mandanten-ID aus der Aussteller-Eigenschaft im Token abrufen und sicherstellen, dass sie im Kunden-/TenantId-"Datenspeicher" vorhanden ist. In der Beispielanwendung erfolgt diese Validierung durch Erstellen einer benutzerdefinierten Tokenhandler-Klasse, die *Saml2SecurityTokenHandler* von Windows Identity Foundation erweitert. Der benutzerdefiniert Tokenhandler verifiziert das eingehende Sicherheitstoken und stellt dessen Ansprüche und Eigenschaften der Anwendung zur Verfügung, sodass die Mandanten-ID validiert werden kann. Der Codeausschnitt für diese Klasse ist unten dargestellt.

In der Beispielanwendung befindet sich der Originalcode im Namespace *Microsoft.IdentityModel.WAAD.Preview.WebSSO*. Der Tokenhandler verwendet außerdem die Contains-Methode der *Microsoft.IdentityModel.WAAD.Preview.WebSSO.TrustedIssuers*-Klasse, die überprüft, ob die Mandanten-ID im Kunden-/TenantId-"Datenspeicher" abgelegt ist.

    /// <summary>
    /// Extends the out of the box SAML2 token handler by ensuring
    /// that incoming tokens have been issued by registered tenants 
    /// </summary>
    public class ConfigurationBasedSaml2SecurityTokenHandler : Saml2SecurityTokenHandler
    {
        public override ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> ValidateToken(SecurityToken token)
        {
            ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> aa = base.ValidateToken(token);
            Saml2SecurityToken ss = token as Saml2SecurityToken;
            string tenant = ss.Assertion.Issuer.Value.Split('/')[3];
            if (!TrustedIssuers.Contains(tenant))
            {
                throw new SecurityTokenValidationException(string.Format("The tenant {0} is not registered with the application", tenant));
            }
            return aa;
        }
    }

Nach der Validierung des Tokens wird der Benutzer bei der Anwendung angemeldet. Führen Sie die Anwendung aus, und versuchen Sie, sich mit einem Azure AD-Konto im zugestimmten Mandanten anzumelden, den Sie zuvor erstellt haben.

## <a name="accessgraph"></a>Teil 4: Zugreifen auf Azure AD Graph

In diesem Abschnitt wird beschrieben, wie Sie ein Zugriffstoken erhalten und die Azure AD Graph-API aufrufen, um auf die Verzeichnisdaten eines Mandanten zuzugreifen. Während das während der Anmeldung erhaltene Token z. B. Benutzerinformationen wie Namen und E-Mail-Adresse enthält, benötigt Ihre Anwendung möglicherweise auch Informationen wie Gruppenmitgliedschaften oder den Namen des Vorgesetzten des Benutzers. Diese Informationen können mithilfe der Graph-API aus dem Verzeichnis des Mandanten abgerufen werden. Weitere Informationen über die Graph-API finden Sie in [diesem Thema][diesem Thema].

Bevor Ihre Anwendung Azure AD Graph aufrufen kann, muss sie sich selbst authentifizieren und ein Zugriffstoken beziehen. Zugriffstoken werden durch Authentifizierung Ihrer Anwendung mit der Client-ID und dem geheimen Clientschlüssel abgerufen. In den folgenden Schritten werden die folgenden Verfahren erläutert:

1.  Verwenden einer generierten Proxyklasse zum Aufrufen von Azure AD Graph
2.  Beziehen eines Zugriffstokens mithilfe der Azure-Authentifizierungsbibliothek
3.  Aufrufen von Azure AD Graph zum Abrufen einer Liste von Mandantenbenutzern

<div class="dev-callout"><strong>Hinweis</strong><p>Die unterst&uuml;tzende Bibliothek Microsoft.IdentityModel.WAAD.Preview der Beispielanwendung enth&auml;lt bereits eine automatisch generierte Proxyklasse (erstellt durch Hinzuf&uuml;gen eines Dienstverweises auf https://graph.windows.net/your-domain-name namens GraphService). Die Anwendung verwendet diese Proxyklasse, um den Dienst Azure AD Graph aufzurufen.</p></div>

### Schritt 1: Verwenden der Proxyklasse zum Aufrufen von Azure AD Graph

In diesem Schritt verwenden wir die Beispielanwendung, um folgende Verfahren zu demonstrieren:

1.  Erstellen eines mandantenspezifischen Azure AD Graph-Endpunkts
2.  Verwenden des Endpunkts zum Instanziieren des Proxys für den Graph-Aufruf
3.  Hinzufügen des Autorisierungsheaders zur Anforderung und Beziehen des Tokens

In der Beispielanwendung werden diese Aufrufe der API von der GraphInterface-Methode in der *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface*-Klasse verarbeitet, wie aus dem folgenden Code hervorgeht.

    public GraphInterface()
    {
        // 1a: When the customer was signed in, we get a security token 
        // that contains a tenant id. Extract that here
        TenantDomainName = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/ws/2012/10/identity/claims/tenantid").Value;

        // 1b: We generate a URL (https://graph.windows.net/<CustomerDomainName>)
        // to access the Azure AD Graph API endpoint for the tenant 
        connectionUri = new Uri(string.Format(@"https://{0}/{1}", TenantUtils.Globals.endpoint, TenantDomainName));

        // 2: create an instance of the AzureAD Service proxy with the connection URL
        dataService = new DirectoryDataService(connectionUri);

        // This flags ignores the resource not found exception
        // If AzureAD Service throws this exception, it returns null
        dataService.IgnoreResourceNotFoundException = true;
        dataService.MergeOption = MergeOption.OverwriteChanges;
        dataService.AddAndUpdateResponsePreference = DataServiceResponsePreference.IncludeContent;

        // 3: This adds the default required headers to each request
        AddHeaders(GetAuthorizationHeader());
    }

### Schritt 2: Beziehen eines Zugriffstokens mithilfe der Azure-Authentifizierungsbibliothek

Die Beispielanwendung verwendet die Azure-Authentifizierungsbibliothek, um Token für den Zugriff auf die Graph-API zu beziehen. Das Beziehen des Tokens wird von der *GetAuthorizationHeader*-Methode in der *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface*-Klasse verwaltet und ist unten dargestellt.

<div class="dev-callout"><strong>Hinweis</strong><p>Die Azure-Authentifizierungsbibliothek ist als NuGet-Paket verf&uuml;gbar und kann in Visual Studio installiert werden.</p></div>

    /// <summary>
    /// Method to get the Oauth2 Authorization header from ACS
    /// </summary>
    /// <returns>AOauth2 Authorization header</returns>
    private string GetAuthorizationHeader()
    {
        // AAL values
        string fullTenantName = TenantUtils.Globals.StsUrl + TenantDomainName;
        string serviceRealm = string.Format("{0}/{1}@{2}", TenantUtils.Globals.GraphServicePrincipalId, TenantUtils.Globals.GraphServiceHost, TenantDomainName);
        string issuingResource = string.Format("{0}@{1}", Globals.ClientId, TenantDomainName);
        string clientResource = string.Format("{0}/{1}@{2}", Globals.ClientId, Globals.AppHostname, TenantDomainName);

        string authzHeader = null;
        AuthenticationContext _authContext = new AuthenticationContext(fullTenantName);

        try
        {
            ClientCredential credential = new ClientCredential(issuingResource, clientResource, Globals.ServicePrincipalKey);
            AssertionCredential _assertionCredential = _authContext.AcquireToken(serviceRealm, credential);
            authzHeader = _assertionCredential.CreateAuthorizationHeader();
        }
        catch (Exception ex)
        {
            AALException aex = ex as AALException;
            string a = aex.InnerException.Message;
        }

        return authzHeader;
    }

Die folgenden Informationen dienen dazu, das Zugriffstoken zu beziehen, wie im obigen Code demonstriert wird:

1.  Die Informationen der Anwendung (ClientID, ServicePrincipalKey und AppHostname)
2.  Die Zielinformationen, wobei es sich um den Graph handelt und auf die oben als ServiceRealm verwiesen wird
3.  Den zuvor bereits bezogenen TenantDomainName

### Schritt 3: Aufrufen von Azure AD Graph zum Abrufen einer Liste von Benutzern

Die folgende Methode in der *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface*-Klasse dient dazu, eine Liste aller Benutzer für Ihren Mandanten mithilfe des zuvor generierten *DataService*-Proxys abzurufen.

    public List<User> GetUsers()
    {
        // Add the page size using top
        var users = dataService.Users.AddQueryOption("$top", 20);

        // Execute the Query
        var userQuery = users.Execute();

        // Get the return users list
        return userQuery.ToList();
    }

Diese Methode wird aus der Datei **HomeController.cs** aufgerufen, um die Benutzerliste auf der Registerkarte "Users" in der Webanwendung anzuzeigen.

## <a name="publish"></a>Teil 5: Veröffentlichen der Anwendung

Nachdem Sie Ihre Anwendung gründlich getestet haben, können Sie einen Anwendungseintrag erstellen und diesen auf dem Azure Marketplace veröffentlichen. Diese Schritte werden im Microsoft-Verkäuferdashboard ausgeführt.

<div class="dev-callout"><strong>Hinweis</strong><p>Ihre App ist f&uuml;r die Verwaltung von Fakturierungsbeziehungen mit Ihren Kunden verantwortlich. Der Azure Marketplace stellt nur Links zur Website Ihrer Anwendung und Informationen dar&uuml;ber zur Verf&uuml;gung.</p></div>

### Schritt 1: Erstellen eines Anwendungsmanifests und eines App-Eintrags

Bevor Sie einen App-Eintrag erstellen, müssen Sie eine neue Client-ID und einen neuen geheimen Clientschlüssel für die Produktionsversion Ihrer Anwendung erstellen. In Teil 1 dieser Anleitung haben Sie eine Client-ID und einen geheimen Clientschlüssel erstellt, die für eine Testversion Ihrer Anwendung bestimmt waren. Wiederholen Sie diese Schritte, und konfigurieren Sie Ihre Anwendung für die neuen Werte. Stellen Sie dabei sicher, dass Sie eine Anwendungsdomäne und eine App-Umleitungs-URL für die Produktionsversion festlegen.

Anschließend müssen Sie ein Anwendungsmanifest erstellen, das die Berechtigungen auflistet, die Ihre Anwendung für die Kundenzustimmung anfordert. Dieses Manifest ist in einem XML-Format geschrieben, das von einer XSD-Datei geregelt wird. Das Manifest muss im Rahmen des Anwendungseintrags hochgeladen werden, den Sie erstellen.

Wie in Teil 1 der Anleitung beschrieben gibt es drei unterschiedliche Berechtigungsebenen:

**DirectoryReader**: Erteilt die Berechtigung, Verzeichnisdaten wie Benutzerkonten, Gruppen und Informationen über Ihre Organisation zu lesen. Aktiviert die einmalige Anmeldung.

**UserAccountAdministrator**: Erteilt die Berechtigung, Daten wie Benutzer, Gruppen und Informationen über Ihre Organisation zu lesen und zu schreiben. Aktiviert die einmalige Anmeldung.

**None**: Aktiviert die einmalige Anmeldung, deaktiviert jedoch den Zugriff auf Verzeichnisdaten.

Nachfolgend finden Sie zwei Beispiele für Anwendungsmanifeste. Im ersten Beispiel werden Berechtigungen für eine Anwendung mit einmaliger Anmeldung demonstriert, im zweiten Berechtigungen für eine schreibgeschützte Anwendung:

    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="None" Scope="http://directory" />
      </AppPermissionRequests>
    </AppRequiredPermissions>


    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="Directory Reader" Scope="http://directory">
          <Reason culture="de-de" value="Needs to read the app"/>
        </AppPermissionRequest>
      </AppPermissionRequests>
    </AppRequiredPermissions>

Das Attribut *Policy* in den obigen Beispielen beschreibt den Typ der angeforderten Anwendungsberechtigung. Derzeit wird nur das Berechtigungsattribut "AppOnly" unterstützt. Dieser Berechtigungstyp gibt an, dass die Anwendung nur als sie selbst auf das Active Directory zugreift.

Mit dem optionalen Element *Reason* können Sie (in mehreren Kulturen) Ihre Begründung für die erforderliche Berechtigungsebene angeben. Dieser Text wird auf der Zustimmungsseite angezeigt, um dem Kunden bei der Genehmigung oder Ablehnung Ihrer Anwendung zu helfen.

Mit der neuen Client-ID und dem Anwendungsmanifest können Sie einen Anwendungseintrag erstellen, wenn Sie den Anweisungen unter [Hinzufügen von Apps im Microsoft-Verkäuferdashboard][Hinzufügen von Apps im Microsoft-Verkäuferdashboard] folgen. Wählen Sie beim Erstellen eines Anwendungseintrags unbedingt den Azure AD-Anwendungstyp aus. Klicken Sie nach dem Erstellen des Anwendungseintrags auf "submit", um die Anwendung auf dem Azure Marketplace zu veröffentlichen. Sie müssen warten, bis Ihre Anwendung genehmigt ist, erst danach ist die Veröffentlichung abgeschlossen.

<div class="dev-callout"><strong>Hinweis</strong><p>Wenn Sie aufgefordert werden, Steuer- und Auszahlungsinformationen anzugeben, k&ouml;nnen Sie diesen Schritt &uuml;berspringen, da Sie Ihre Anwendung direkt an Kunden und nicht &uuml;ber Microsoft verkaufen.</p></div>

### Schritt 2: Abschließen der Tests und Veröffentlichen der Anwendung

Nachdem Ihr Anwendungseintrag genehmigt wurde, sollten Sie Ihre Anwendung erneut durchgängig testen. Stellen Sie z. B. sicher, dass die Anwendung mit der Client-ID und dem geheimen Clientschlüssel für die Produktion aktualisiert wurde. Arbeiten Sie die Testcheckliste ein letztes Mal ab, und vergewissern Sie sich, dass auf der Zustimmungsseite jetzt die Informationen angezeigt werden, die Sie in Ihren Anwendungseintrag aufgenommen haben.

## <a name="summary"></a>Zusammenfassung

In dieser Anleitung haben Sie gelernt, Ihre Anwendung für mehrere Mandanten in Azure AD zu integrieren. Der Prozess umfasste drei Schritte:

-   Kunden die Möglichkeit geben, sich mit Azure AD für Ihre Anwendung anzumelden
-   Einmalige Anmeldung mit Azure AD ermöglichen
-   Verzeichnisdaten eines Kunden mit der Azure AD-Graph-API abfragen

Die Integration in Azure AD gibt Ihren Kunden die Möglichkeit, sich bei Ihrer Anwendung mit einem Identitätsverwaltungssystem zu registrieren und anzumelden, das sie bereits verwalten. Dadurch verringert sich oder entfällt die Notwendigkeit, spezielle Identitätsverwaltungsaufgaben mit Ihrer Anwendung auszuführen. Diese Funktion macht die Nutzung Ihrer Anwendung für Ihre Kunden komfortabler und setzt die zuvor für Verwaltungsaufgaben benötigte Zeit frei.

  [Azure: die Cloud-Plattform von Microsoft]: http://www.windowsazure.com/de-de/home/features/identity/
  [hier herunterladen]: http://go.microsoft.com/fwlink/?LinkId=271213
  [Anschlusszuweisung in Visual Studio]: http://msdn.microsoft.com/de-de/library/ms178109(v=vs.100).aspx
  [Visual Studio 2012]: http://www.microsoft.com/visualstudio/eng/downloads
  [WCF Data Services für OData]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Einführung]: #introduction
  [Teil 1: Abrufen einer Client-ID für den Zugriff auf Azure AD]: #getclientid
  [Teil 2: Ermöglichen der Anmeldung mit Azure AD]: #enablesignup
  [Teil 3: Aktivieren der einmaligen Anmeldung]: #enablesso
  [Teil 4: Zugreifen auf Azure AD Graph]: #accessgraph
  [Teil 5: Veröffentlichen der Anwendung]: #publish
  [Zusammenfassung]: #summary
  [Microsoft-Verkäuferdashboard]: https://sellerdashboard.microsoft.com/
  [ein Kontoprofil zu erstellen]: http://msdn.microsoft.com/de-de/library/jj552460.aspx
  [Erstellen von Client-IDs und geheimen Clientschlüsseln im Microsoft-Verkäuferdashboard]: http://msdn.microsoft.com/de-de/library/jj552461.aspx
  [Anmeldung]: ./media/active-directory-dotnet-integrate-multitent-cloud-applications/login.png
  [Erhalten eines Azure AD-Mandanten]: http://g.microsoftonline.com/0AX00en/5
  [contoso]: https://contoso.myapp.com
  [diesem Thema]: http://msdn.microsoft.com/de-de/library/windowsazure/hh974476.aspx
  [Hinzufügen von Apps im Microsoft-Verkäuferdashboard]: http://msdn.microsoft.com/de-de/library/jj552465.aspx
