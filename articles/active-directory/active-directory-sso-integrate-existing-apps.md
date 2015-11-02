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
   ms.date="10/16/2015"
   ms.author="kgremban; liviodlc"/>

# Integrieren von Azure Active Directory SSO in vorhandene Apps

## Übersicht

Das Einrichten des einmaligen Anmeldens (Single Sign-On, SSO) für eine Anwendung, die in Ihrem Unternehmen bereits verwendet wird, ist ein anderer Prozess als das Erstellen neuer Konten für eine neue Anwendung. Beispiele für vorbereitende Schritte sind: Zuordnen von Benutzeridentitäten in der Anwendung zu Azure Active Directory (AD)-Identitäten und Ermitteln, wie Benutzer das Anmelden an einer Anmeldung nach der Integration erfahren.

> [AZURE.NOTE]Um SSO für eine vorhandene Anwendung einzurichten, benötigen Sie globale Administratorrechte in Azure AD und der SaaS-Anwendung.

## Zuordnen von Benutzerkonten

Die Identität eines Benutzers verfügt normalerweise über einen eindeutigen Bezeichner, wobei es sich um eine E-Mail-Adresse oder einen UPN (Universal Principal Name) handeln kann. Sie müssen die Anwendungsidentität jedes Benutzers mit der entsprechenden Azure AD-Identität verknüpfen (bzw. die Zuordnung durchführen). Hierfür gibt es einige Möglichkeiten, je nachdem, welche Anforderungen für Ihre Anwendungsauthentifizierung gelten.

Weitere Informationen zum Zuordnen von Anwendungsidentitäten mit Azure AD-Identitäten finden Sie unter [Customizing claims issued in the SAML token for pre-integrated apps](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) (in englischer Sprache) und [Anpassen von Attributzuordnungen](active-directory-saas-customizing-attribute-mappings.md).

## Grundlegendes zur Benutzeroberfläche für die Anmeldung

Wenn Sie SSO für eine Anwendung integrieren, die bereits verwendet wird, muss beachtet werden, dass die Benutzeroberfläche davon betroffen ist. Bei allen Anwendungen beginnen Benutzer damit, sich mit ihren Azure AD-Anmeldeinformationen anzumelden. Darüber hinaus müssen sie möglicherweise über ein anderes Portal auf die Anwendungen zugreifen.

SSO kann für einige Anwendungen auf der Anmeldeoberfläche der Anwendung durchgeführt werden, aber für anderen Anwendungen müssen Benutzer ein zentrales Portal für die Anmeldung verwenden, z. B. [Meine Apps](http://myapps.microsoft.com) oder [Office 365](http://portal.office.com/myapps). Erfahren Sie mehr über die verschiedenen Typen von SSO und die jeweilige Verwendungsweise für Benutzer in [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory](active-directory-appssoaccess-whatis.md).

Weitere Informationen finden Sie unter *Unterdrücken der Benutzerzustimmung* im Artikel [Leitfaden für Entwickler](active-directory-applications-guiding-developers-for-lob-applications.md).

## Verwandte Artikel
Links zum Unterdrücken der Benutzerzustimmung und zu anderen Unterartikeln mit Entwicklerleitfäden

<!---HONumber=Oct15_HO4-->