<properties
   pageTitle="Verbund mit Active Directory-Verbunddiensten (AD FS) eines Kunden| Microsoft Azure"
   description="Wie ein Verbund mit den AD FS eines Kunden in einer mehrinstanzenfähigen Anwendung eingegangen wird"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Verbund mit Active Directory-Verbunddiensten (AD FS) eines Kunden

Dieser Artikel ist [Teil einer Serie]. Es gibt auch eine vollständige [Beispielanwendung], die diese Serie begleitet.

In diesem Artikel wird beschrieben, wie eine SaaS-Anwendung mit mehreren Mandanten eine Authentifizierung über Active Directory-Verbunddienste (AD FS) unterstützen kann, um einen Verbund mit den AD FS eines Kunden einzugehen.

## Übersicht

Azure Active Directory (Azure AD) vereinfacht das Anmelden von Benutzern von Azure AD-Mandanten sowie Kunden von Office 365 und Dynamics CRM Online. Doch wie sieht es mit Kunden aus, die ein lokales Active Directory in einem Unternehmensintranet nutzen?

Eine Möglichkeit für diese Kunden ist die Synchronisierung ihres lokalen AD mit Azure AD unter Verwendung von [Azure AD Connect]. Einigen Kunden ist es eventuell aufgrund IT-Unternehmensrichtlinien oder anderen Gründen nicht möglich, diese Methode zu nutzen. In diesem Fall wäre eine weitere Möglichkeit, einen Verbund über Active Directory-Verbunddienste (AD FS) einzurichten.

Aktivieren dieses Szenarios:

-	Der Kunde muss eine AD FS-Farm mit Internetanbindung besitzen.
-	Der SaaS-Anbieter stellt seine eigene AD FS-Farm bereit.
-	Der Kunde und der SaaS-Anbieter müssen eine [Verbundvertrauensstellung] einrichten. Dies ist ein manueller Prozess.

Die Vertrauensbeziehung umfasst drei wichtige Rollen:

-	Die AD FS des Kunden sind die [Kontopartner]. Diese sind verantwortlich für die Authentifizierung von Benutzern des Active Directory des Kunden, und für die Erstellung von Sicherheitstoken mit Benutzeransprüchen.
-	Der AD FS des SaaS-Anbieters sind die [Ressourcenpartner], die den Kontopartnern vertrauen und die Benutzeransprüche empfangen.
-	Die Anwendung wird als eine vertrauende Seite (RP) in den AD FS des SaaS-Anbieters konfiguriert.

	![Verbundvertrauensstellung](media/guidance-multitenant-identity/federation-trust.png)

> [AZURE.NOTE] In diesem Artikel wird davon ausgegangen, dass die Anwendung OpenID Connect als Authentifizierungsprotokoll verwendet. Eine andere Möglichkeit ist die Verwendung von WS-Verbund.

> Für OpenID Connect muss der SaaS-Anbieter AD FS 4.0 in Windows Server 2016 verwenden, was zurzeit in der Version Technical Preview läuft. OpenID Connect wird von AD FS 3.0 nicht unterstützt.

> ASP.NET Core 1.0 bietet ohne zusätzlichen Aufwand keine Unterstützung für WS-Verbund.

Ein Beispiel für die Verwendung von WS-Verbund mit ASP.NET 4 finden Sie unter https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation

## Authentifizierungsfluss

1.	Klickt der Benutzer auf „Anmelden“, leitet die Anwendung diesen an einen OpenID Connect-Endpunkt der AD FS des SaaS-Anbieters weiter.
2.	Der Benutzer/die Benutzerin gibt seinen/ihren Organisationsbenutzernamen ein („`alice@corp.contoso.com`“). AD FS verwenden Startbereichserkennung, um zu den AD FS des Kunden umzuleiten, wo Benutzer ihre Anmeldeinformationen eingeben.
3.	Die AD FS des Kunden senden mithilfe des WF-Verbunds (oder SAML) Benutzeransprüche an die AD FS des SaaS-Anbieters.
4.	Ansprüche werden von den AD FS mithilfe von OpenID Connect an die App übertragen. Dies erfordert einen Protokollübergang von WS-Verbund.

## Einschränkungen

Zum Zeitpunkt der Erstellung erhält die Anwendung einen begrenzten Satz von Ansprüchen im OpenID-Id\_token. AD FS 4.0 befindet sich noch in der Vorschauversion, darum kann sich diese Liste noch ändern. Falls Ihre App zusätzliche Ansprüche benötigt, beachten Sie vor dem Fortfahren bitte, dass dies derzeit leider nicht möglich ist.

Derzeit werden folgende Ansprüche in den Id\_token gesendet:

Anspruch | Beschreibung
------|-------------
aud | Zielgruppe – die Anwendung, für die die Ansprüche ausgestellt wurden.
authenticationinstant | [Authentication-Instanz]
c\_hash | Codehashwert
exp | [Ablaufzeit]
iat | [Ausgestellt um]
iss | Aussteller Der Wert dieses Anspruchs sind immer die AD FS des Ressourcenpartners, nicht die AD FS des Kunden. (Anders ausgedrückt identifiziert dieser Anspruch den SaaS-Anbieter als den Aussteller, nicht den Kunden.)
name | Benutzername Beispiel: `john@corp.fabrikam.com`.
nameidentifier | [Namensbezeichner]
nonce | Sitzungsnonce
upn | Benutzerprinzipalname (UPN) Beispiel: john@corp.fabrikam.com

Beachten Sie insbesondere, dass der Anspruch „iss“ nicht die AD FS des Kunden angibt. Informationen zur Domäne des Benutzers finden Sie unter dem UPN. Im restlichen Artikel wird das Einrichten der Vertrauensstellung zwischen der Anwendung der RP (der App) und dem Kontopartner (dem Kunden) beschrieben.

## AD FS-Bereitstellung

Der SaaS-Anbieter kann AD FS lokal oder auf Azure-VMs bereitstellen. Für die Sicherheit und Verfügbarkeit sind die folgenden Richtlinien zu beachten:

-	Stellen Sie mindestens zwei AD FS-Server und zwei AD FS-Proxyserver bereit, um die beste Verfügbarkeit der Active Directory-Verbunddienste zu gewährleisten.
-	Domänencontroller und AD FS-Server sollten nie direkt mit dem Internet verbunden sein, und sie sollten sich in einem virtuellem Netzwerk mit direktem Zugriff auf diese befinden.
-	Webanwendungsproxys (zuvor AD FS-Proxys) müssen verwendet werden, um AD FS-Server im Internet zu veröffentlichen.

Das Einrichten einer ähnlichen Topologie in Azure erfordert die Verwendung von virtuellen Netzwerken, NSGs, Azure VMs und Verfügbarkeitsgruppen. Weitere Informationen finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Azure](MSDN).

## Konfigurieren der Anwendung zur Verwendung von OpenID Connect-Authentifizierung mit AD FS

Der SaaS-Anbieter muss OpenID Connect zwischen der Anwendung und den AD FS aktivieren. Fügen Sie hierzu eine Anwendungsgruppe in den AD FS hinzu. Ausführliche Informationen finden Sie in diesem [Blogeintrag] unter „Setting up a Web App for OpenId Connect sign in AD FS.“ (Konfigurieren einer Web-App für AD FS-Anmeldung via OpenID Connect) Konfigurieren Sie dann die OpenID Connect-Middleware. Der Metadatenendpunkt ist `https://domain/adfs/.well-known/openid-configuration`, wobei die Domäne der AD FS-Domäne des SaaS-Anbieters entspricht.

Sie können dies in der Regel mit anderen OpenID Connect-Endpunkten (z. B. AAD) kombinieren. Sie benötigen zwei verschiedene Anmeldeschaltflächen oder eine andere Möglichkeit, diese zu unterscheiden, damit der Benutzer an den richtigen Authentifizierungsendpunkt weitergeleitet wird.

## Konfigurieren des AD FS-Ressourcenpartners

Der SaaS-Anbieter muss für jeden Kunden folgende Schritte ausführen, wenn dieser über AD FS eine Verbindung herstellen möchte:

1.	Fügen Sie eine Vertrauensstellung für Anspruchsanbieter hinzu.
2.	Fügen Sie Anspruchsregeln hinzu.
3.	Aktivieren Sie eine Startbereichsermittlung.

Hier werden die Schritte im Detail veranschaulicht.

### Fügen Sie die Anspruchsanbieter-Vertrauensstellung hinzu.

1.	Klicken Sie im Server-Manager auf **Tools** und wählen Sie **AD FS-Verwaltung**.
2.	Führen Sie unter **AD FS** in der Konsolenstruktur einen Rechtsklick auf **Anspruchsanbieter-Vertrauensstellung** aus. Wählen Sie **Anspruchsanbieter-Vertrauensstellung hinzufügen**.
3.	Klicken Sie auf **Starten**, um den Assistenten zu starten.
4.	Wählen Sie die Option „Importieren von online oder in einem lokalen Netzwerk veröffentlichten Daten über den Anspruchsanbieter“. Geben Sie den URI des Verbundmetadaten-Endpunkts des Kunden ein. (Beispiel: `https://contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.) Sie erhalten diesen vom Kunden.
5.	Schließen Sie den Assistenten unter Verwendung der angegebenen Standardoptionen ab.

### Anspruchsregeln bearbeiten.

1.	Führen Sie einen Rechtsklick auf die neu hinzugefügte Anspruchsanbieter-Vertrauensstellung aus, und wählen Sie **Anspruchsregeln bearbeiten**.
2.	Klicken Sie auf **Regel hinzufügen**.
3.	Wählen Sie „Weiterleiten oder Filtern eines Eingehenden Anspruchs“, und klicken Sie auf **Weiter**. ![Assistenten zum Hinzufügen von Transformationsanspruchsregeln hinzufügen](media/guidance-multitenant-identity/edit-claims-rule.png)
4.	Geben Sie einen Namen für die Regel ein.
5.	Wählen Sie unter „Eingehender Anspruchstyp“ **UPN**.
6.	Wählen Sie „Durchlauf aller Anspruchswerte“. ![Assistenten zum Hinzufügen von Transformationsanspruchsregeln hinzufügen](media/guidance-multitenant-identity/edit-claims-rule2.png)
7.	Klicken Sie auf **Fertig stellen**.
8.	Wiederholen Sie die Schritte 2 bis 7, und geben Sie **Anker Anspruchstyp** für den eingehenden Anspruchstyp an.
9.	Klicken Sie auf **OK**, um den Assistenten abzuschließen.

### Aktivierung der Startbereichsermittlung
Führen Sie das folgende PowerShell-Skript aus:

```
Set-ADFSClaimsProviderTrust -TargetName "name" -OrganizationalAccountSuffix @("suffix")
```

Wobei „name“ der Anzeigename der Anspruchsanbieter-Vertrauensstellung ist, und „suffix“ das UPN-Suffix für das AD des Kunden (z. B. „corp.fabrikam.com“).

Mit dieser Konfiguration können Endbenutzer ihr Organisationskonto eingeben, und AD FS wählen automatisch den entsprechenden Anspruchsanbieter aus. Siehe unter [Anpassen der AD FS-Sign-in-Webseiten] den Abschnitt „Konfigurieren von Identitätsanbietern, um bestimmte E-Mail-Suffixe zu verwenden“.

## Konfigurieren der AD FS-Kontopartner

Kunden müssen wie folgt vorgehen:

1.	Eine Vertrauensstellung der vertrauenden Seite (RP) hinzufügen.
2.	Anspruchsregeln hinzufügen.

### Die RP-Vertrauensstellung hinzufügen.

1.	Klicken Sie im Server-Manager auf **Tools** und wählen Sie **AD FS-Verwaltung**.
2.	Führen Sie unter **AD FS** in der Konsolenstruktur einen Rechtsklick auf **Vertrauensstellung der vertrauenden Seite** aus. Wählen Sie **Hinzufügen der Vertrauensstellung der vertrauenden Seite**.
3.	Wählen Sie **Ansprüche unterstützend** und klicken Sie auf **Starten**.
4.	Wählen Sie auf der Seite **Auswählen von Datenquellen** die Option „Daten über den Anspruchsanbieter online oder über ein lokales Netzwerk importieren“. Geben Sie den URI des Verbundmetadaten-Endpunkts des SaaS-Anbieters an. ![Assistenten für Vertrauensstellung der vertrauenden Seite hinzufügen](media/guidance-multitenant-identity/add-rp-trust.png)
5.	Geben Sie auf der Seite **Anzeigename angeben** einen beliebigen Namen ein.
6.	Wählen Sie auf der Seite **Zugriffssteuerungsrichtlinien wählen** eine Richtlinie. Sie können jede Person im Unternehmen zulassen oder eine bestimmte Sicherheitsgruppe wählen. ![Assistenten für Vertrauensstellung der vertrauenden Seite hinzufügen](media/guidance-multitenant-identity/add-rp-trust2.png)
7.	Klicken Sie auf **Weiter**, um den Assistenten abzuschließen.

### Fügen Sie Anspruchsregeln hinzu

1.	Klicken Sie mit der rechten Maustaste auf die neu hinzugefügte Vertrauensstellung der vertrauenden Seite, und wählen Sie **Anspruchsausstellungsrichtlinie bearbeiten**.
2.	Klicken Sie auf **Regel hinzufügen**.
3.	Wählen Sie „Senden von LDAP-Attributen als Ansprüche“, und klicken Sie auf **Weiter**. ![Assistenten zum Hinzufügen von Transformationsanspruchsregeln hinzufügen](media/guidance-multitenant-identity/add-claims-rules.png)
4.	Geben Sie einen Namen für die Regel ein, z. B. „UPN“.
5.	Wählen Sie unter **Attributspeicher**, die Option **Active Directory**.
6.	Im Abschnitt **Zuordnung der LDAP-Attribute**:
  -	Wählen Sie unter **LDAP-Attribut**, die Option **Benutzerprinzipalname**.
  - Wählen Sie unter **Eingehender Anspruchstyp** die Option **UPN**. ![Assistenten zum Hinzufügen von Transformationsanspruchsregeln hinzufügen](media/guidance-multitenant-identity/add-claims-rules2.png)
7.	Klicken Sie auf **Fertigstellen**.
8.	Klicken Sie erneut auf **Regel hinzufügen**.
9.	Wählen Sie „Ansprüche per benutzerdefinierter Regel senden“, und klicken Sie auf **Weiter**.
10.	Geben Sie einen Namen für die Regel ein, z. B. „Ankeranspruch“.
11.	Geben Sie Folgendes unter **Benutzerdefinierte Regel** ein:

	```
    EXISTS([Type == "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype"])=>
      issue (Type = "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype",
             Value = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn");
	```

    Mit dieser Regel wird der UPN-Anspruch dem Ankeranspruch zugeordnet.

12.	Klicken Sie auf **Fertig stellen**.
13.	Klicken Sie auf **OK**, um den Assistenten abzuschließen.

<!-- Links -->
[Teil einer Serie]: guidance-multitenant-identity.md
[Azure AD Connect]: ../active-directory/active-directory-aadconnect.md
[Verbundvertrauensstellung]: https://technet.microsoft.com/library/cc738707.aspx
[Kontopartner]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[Ressourcenpartner]: https://technet.microsoft.com/library/cc758463(v=ws.10).aspx
[Authentication-Instanz]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.authenticationinstant%28v=vs.110%29.aspx
[Ablaufzeit]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.4
[Ausgestellt um]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.6
[Namensbezeichner]: https://technet.microsoft.com/library/ee913589.aspx
[Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[Blogeintrag]: http://www.cloudidentity.com/blog/2015/08/21/OPENID-CONNECT-WEB-SIGN-ON-WITH-ADFS-IN-WINDOWS-SERVER-2016-TP3/
[Anpassen der AD FS-Sign-in-Webseiten]: https://technet.microsoft.com/library/dn280950.aspx
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->