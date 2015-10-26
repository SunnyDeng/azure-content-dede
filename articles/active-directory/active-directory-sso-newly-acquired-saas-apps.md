<properties
   pageTitle="Integrieren der einmaligen Anmeldung mit Azure Active Directory in neu erworbene Apps | Microsoft Azure"
   description="Azure Active Directory unterstützt die einmalige Anmeldung für neu erworbene SaaS-Apps, um eine zentralisierte Zugriffsverwaltung im Azure-Portal zu ermöglichen."
   services="active-directory"
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
      ms.date="10/09/2015"
      ms.author="curtand"/>

# Integrieren der einmaligen Anmeldung mit Azure Active Directory mit neu erworbenen Apps  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Wenn Sie die einmalige Anmeldung für eine App einrichten, die Sie in Ihre Organisation einbringen möchten, beginnen Sie mit einem vorhandenen Verzeichnis in Azure Active Directory. Sie können ein Azure AD-Verzeichnis verwenden, das Sie über Microsoft Azure, Office 365 oder Windows Intune erhalten haben. Wenn Sie mehrere dieser Verzeichnisse haben, erfahren Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](active-directory-administer.md), welches Sie am besten verwenden.

## Weitere Überlegungen

### Authentifizierung

Für Anwendungen, die die Protokolle SAML 2.0, WS-Verbund oder OpenID Connect unterstützen, verwendet Azure Active Directory Signaturzertifikate zum Herstellen von Vertrauensstellungen. Weitere Informationen hierzu finden Sie unter [Verwalten von Zertifikaten für die einmalige Verbundanmeldung](active-directory-sso-certs.md).

Für Anwendungen, die nur Anmeldungen auf HTML-Formularbasis unterstützen, verwendet Azure Active Directory "Password Vaulting", d. h die sichere Speicherung von Kennwörtern, zum Herstellen von Vertrauensstellungen. Dies ermöglicht die automatische Anmeldung der Benutzer in Ihrer Organisation bei einer SaaS-Anwendung durch Azure AD mithilfe der Benutzerkontoinformationen aus der SaaS-Anwendung. Azure AD erfasst die Benutzerkontoinformationen und das zugehörige Kennwort und speichert diese sicher. Weitere Informationen finden Sie unter [Kennwortbasierte einmalige Anmeldung](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

### Autorisierung

Ein bereitgestelltes Konto ermöglicht es, Benutzer zur Verwendung einer Anwendung zu autorisieren, nachdem sie sich durch einmaliges Anmelden authentifiziert haben. Die Benutzerbereitstellung kann manuell erfolgen, oder in einigen Fällen können Sie Benutzerinformationen aus der SaaS-App anhand von Änderungen in Azure Active Directory hinzufügen und entfernen. Weitere Informationen zur Verwendung vorhandener Azure AD-Connectors für die automatisierte Bereitstellung finden Sie unter [Automatisierte Bereitstellung und Aufheben der Bereitstellung für SaaS-Anwendungen](active-directory-saas-app-provisioning.md).

Andernfalls können Sie einer App Benutzerinformationen manuell hinzufügen oder andere Bereitstellungslösungen verwenden, die auf dem Markt verfügbar sind.

### Access

Azure AD bietet mehrere anpassbare Möglichkeiten, um Anwendungen für Endbenutzer in Ihrer Organisation bereitzustellen: Sie sind nicht an eine bestimmte Bereitstellungs- oder Zugriffslösung gebunden. Sie können [die Lösung, die Ihren Bedürfnissen am besten entspricht](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users), verwenden.

## Nächste Schritte

Für bereits in Ihrer Organisation vorhandene SaaS-Apps, die Sie für die einmalige Anmeldung aktivieren möchten, finden Sie weitere Informationen unter [Integrieren von Azure Active Directory SSO in vorhandene Apps](active-directory-sso-integrate-existing-apps.md)

Für SaaS-Apps, die Sie im App-Katalog finden, bietet Azure Active Directory eine Reihe von [Lernprogrammen zur Integration von SaaS-Apps](active-directory-saas-tutorial-list.md).

Wenn die App sich nicht im App-Katalog befindet, können Sie [sie der Azure Active Directory-App-Galerie als benutzerdefinierte Anwendung hinzufügen](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

In der Azure.com-Bibliothek finden Sie weitaus umfassendere Informationen zu diesen Problemen, beginnend mit [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!---HONumber=Oct15_HO3-->