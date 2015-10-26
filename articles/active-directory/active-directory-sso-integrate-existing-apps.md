<properties
   pageTitle="Integrieren von Azure Active Directory SSO in vorhandene Apps | Microsoft Azure"
   description="Verwalten der bereits verwendeten SaaS-Apps durch Aktivieren von Authentifizierung mit einmaliger Anmeldung und Benutzerbereitstellung in Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# Integrieren von Azure Active Directory SSO in vorhandene Apps

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## Weitere Überlegungen

Das Einrichten von SSO für Apps, die Sie bereits verwenden, unterscheidet sich vom Erstellen neuer Konten in neuen Apps. Wenn die App in Azure AD integriert ist, tritt für Administratoren und Benutzer eine Änderung auf.

### Was müssen Administratoren wissen, um das einmalige Anmelden für Apps einzurichten, die bereits verwendet werden?

Um SSO für eine vorhandene App einzurichten, benötigen Sie globale Administratorrechte in Azure AD und der SaaS-Anwendung.

Da die Anwendung bereits verwendet wird, müssen Sie die bestehenden App-Identitäten des Benutzers mit den jeweiligen Azure AD-Identitäten verknüpfen. Es muss bekannt sein, was diese App als eindeutigen Bezeichner für die Anmeldung verwendet: ob es sich um eine E-Mail-Adresse, einen universellen persönlichen Namen (UPN) oder einen Benutzernamen handelt. Dies wird mit dem eindeutigen Bezeichner des Benutzers in Azure AD verknüpft. Weitere Informationen zum Verknüpfen von App-Identitäten mit Azure AD-Identitäten finden Sie unter [Customizing claims issued in the SAML token for pre-integrated apps](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) (in englischer Sprache) und [Anpassen von Attributzuordnungen](active-directory-saas-customizing-attribute-mappings.md).

### Welche Auswirkungen hat dies auf Endbenutzer?

Wenn Sie SSO für eine Anwendung integrieren, die bereits verwendet wird, muss beachtet werden, dass die Benutzeroberfläche davon betroffen ist. Für alle Apps beginnen Benutzer damit, sich mit ihren Azure AD-Anmeldeinformationen anzumelden. Darüber hinaus müssen sie möglicherweise über ein anderes Portal auf die Anwendungen zugreifen. SSO kann für einige Programme auf der eigenen Website der App ausgeführt werden, bei anderen Apps müssen Benutzer sich jedoch bei einem zentralen App-Portal ([My Apps](myapps.microsoft.com) oder [Office 365](portal.office.com/myapps)) anmelden. Erfahren Sie mehr über die verschiedenen Typen von SSO und die jeweilige Verwendungsweise für Benutzer in [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Nächste Schritte
- Erfahren Sie, wie Sie die [einmalige Anmeldung mit Azure Active Directory mit neu erworbenen Apps integrieren](active-directory-sso-newly-acquired-saas-apps.md).
- Erfahren Sie mehr über [Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory](active-directory-appssoaccess-whatis.md).
- Suchen Sie [Lernprogramme zur Integration von SaaS-Apps](active-directory-saas-tutorial-list.md).
-	Hinzufügen einer benutzerdefinierten App mit [Azure AD-Self-Service-SAML-Konfiguration](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)

<!---HONumber=Oct15_HO3-->