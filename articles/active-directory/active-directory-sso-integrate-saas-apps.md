<properties
    pageTitle="Integrieren des einmaligen Anmeldens mit Azure Active Directory in SaaS-Apps | Microsoft Azure"
    description="Ermöglichen Sie die Authentifizierung für das einmalige Anmelden und die Benutzerbereitstellung der zentralisierten Zugriffsverwaltung von SaaS-Apps in Azure Active Directory. Eine Übersicht zum Integrieren von Azure Active Directory in SaaS-Apps."
    services="active-directory"
	  keywords="Integrieren von Azure AD in SaaS-Apps"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="curtand"/>

# Integrieren des einmaligen Anmeldens mit Azure Active Directory in SaaS-Apps  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Wenn Sie die einmalige Anmeldung für eine App einrichten, die Sie in Ihre Organisation einbringen möchten, beginnen Sie mit einem vorhandenen Verzeichnis in Azure Active Directory (Azure AD). Sie können ein Azure AD-Verzeichnis verwenden, das Sie über Microsoft Azure, Office 365 oder Windows Intune erhalten haben. Wenn Sie mehrere dieser Verzeichnisse haben, erfahren Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](active-directory-administer.md), welches Sie am besten verwenden.

## Authentifizierung

Für Anwendungen, die die Protokolle SAML 2.0, WS-Verbund oder OpenID Connect unterstützen, verwendet Azure Active Directory Signaturzertifikate zum Herstellen von Vertrauensstellungen. Weitere Informationen hierzu finden Sie unter [Verwalten von Zertifikaten für die einmalige Verbundanmeldung](active-directory-sso-certs.md).

Für Anwendungen, die nur Anmeldungen auf HTML-Formularbasis unterstützen, verwendet Azure Active Directory "Password Vaulting", d. h die sichere Speicherung von Kennwörtern, zum Herstellen von Vertrauensstellungen. Dies ermöglicht die automatische Anmeldung der Benutzer in Ihrer Organisation bei einer SaaS-Anwendung durch Azure AD mithilfe der Benutzerkontoinformationen aus der SaaS-Anwendung. Azure AD erfasst die Benutzerkontoinformationen und das zugehörige Kennwort und speichert diese sicher. Weitere Informationen finden Sie unter [Kennwortbasierte einmalige Anmeldung](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## Autorisierung

Ein bereitgestelltes Konto ermöglicht es, Benutzer zur Verwendung einer Anwendung zu autorisieren, nachdem sie sich durch einmaliges Anmelden authentifiziert haben. Die Benutzerbereitstellung kann manuell erfolgen, oder in einigen Fällen können Sie Benutzerinformationen aus der SaaS-App anhand von Änderungen in Azure Active Directory hinzufügen und entfernen. Weitere Informationen zur Verwendung vorhandener Azure AD-Connectors für die automatisierte Bereitstellung finden Sie unter [Automatisierte Bereitstellung und Aufheben der Bereitstellung für SaaS-Anwendungen](active-directory-saas-app-provisioning.md).

Andernfalls können Sie einer App Benutzerinformationen manuell hinzufügen oder andere Bereitstellungslösungen verwenden, die auf dem Markt verfügbar sind.

## Access

Azure AD bietet mehrere anpassbare Möglichkeiten zum Bereitstellen von Anwendungen für Endbenutzer in Ihrer Organisation. Sie sind nicht an eine bestimmte Bereitstellungs- oder Zugriffslösung gebunden. Sie können [die Lösung, die Ihren Bedürfnissen am besten entspricht](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users), verwenden.

## Zusätzliche Aspekte für bereits verwendete Anwendungen

Das Einrichten des einmaligen Anmeldens für eine Anwendung, die in Ihrem Unternehmen bereits verwendet wird, ist ein anderer Prozess als das Erstellen neuer Konten für eine neue Anwendung. Beispiele für vorbereitende Schritte sind: Zuordnen von Benutzeridentitäten in der Anwendung zu Azure AD-Identitäten und Ermitteln, wie Benutzer das Anmelden an einer Anmeldung nach der Integration erfahren.

> [AZURE.NOTE] Um SSO für eine vorhandene Anwendung einzurichten, benötigen Sie globale Administratorrechte in Azure AD und der SaaS-Anwendung.

### Zuordnen von Benutzerkonten

Die Identität eines Benutzers verfügt normalerweise über einen eindeutigen Bezeichner, wobei es sich um eine E-Mail-Adresse oder einen UPN (Universal Principal Name) handeln kann. Sie müssen die Anwendungsidentität jedes Benutzers mit der entsprechenden Azure AD-Identität verknüpfen (bzw. die Zuordnung durchführen). Hierfür gibt es einige Möglichkeiten, je nachdem, welche Anforderungen für Ihre Anwendungsauthentifizierung gelten.

Weitere Informationen zum Zuordnen von Anwendungsidentitäten mit Azure AD-Identitäten finden Sie unter [Customizing claims issued in the SAML token for pre-integrated apps](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) (in englischer Sprache) und [Anpassen von Attributzuordnungen](active-directory-saas-customizing-attribute-mappings.md).

### Grundlegendes zur Benutzeroberfläche für die Anmeldung

Wenn Sie SSO für eine Anwendung integrieren, die bereits verwendet wird, muss beachtet werden, dass die Benutzeroberfläche davon betroffen ist. Bei allen Anwendungen beginnen Benutzer damit, sich mit ihren Azure AD-Anmeldeinformationen anzumelden. Es kann auch sein, dass sie ein anderes Portal verwenden müssen, um auf die Anwendungen zuzugreifen.

SSO kann für einige Anwendungen auf der Anmeldeoberfläche der Anwendung durchgeführt werden, aber für andere Anwendungen müssen Benutzer ein zentrales Portal für die Anmeldung verwenden, z. B. [Meine Apps](http://myapps.microsoft.com) oder [Office 365](http://portal.office.com/myapps). Erfahren Sie mehr über die verschiedenen Typen von SSO und die jeweilige Verwendungsweise für Benutzer in [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory](active-directory-appssoaccess-whatis.md).

Eine weitere nützliche Ressource ist der Abschnitt *Unterdrücken der Benutzerzustimmung* im Artikel [Leitfaden für Entwickler](active-directory-applications-guiding-developers-for-lob-applications.md).

## Nächste Schritte


Für SaaS-Apps, die Sie im App-Katalog finden, bietet Azure AD eine Reihe von [Lernprogrammen zur Integration von SaaS-Apps](active-directory-saas-tutorial-list.md).

Wenn die App sich nicht im App-Katalog befindet, können Sie [sie der Azure AD-App-Galerie als benutzerdefinierte Anwendung hinzufügen](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

In der Azure.com-Bibliothek finden Sie weitaus umfassendere Informationen zu diesen Problemen, beginnend mit [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Weitere Informationen

- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->