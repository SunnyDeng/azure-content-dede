<properties
   pageTitle="Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten"
   description="Auflisten einer Anwendung, die einmaliges Anmelden unterstützt, im Azure Active Directory-Katalog | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/19/2016"
   ms.author="mbaldwin"/>


# Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten

Eine Anwendung, die einmaliges Anmelden mit Azure Active Directory unterstützt, kann nur im [Azure AD-Katalog](https://azure.microsoft.com/marketplace/active-directory/all/) aufgelistet werden, wenn sie einen der folgenden Integrationsmodi implementiert:

* **OpenID Connect**: Direkte Integration in Azure AD unter Verwendung von OpenID Connect für die Authentifizierung und der Azure AD-Zustimmungs-API für die Konfiguration. Wenn Sie gerade erst mit der Integration beginnen und Ihre Anwendung SAML nicht unterstützt, ist dies der empfohlene Modus.

* **SAML**: In Ihrer Anwendung können bereits Identitätsanbieter von Drittanbietern mithilfe des SAML-Protokolls konfiguriert werden.

Für die Auflistung bestehen je nach Modus folgende Anforderungen:

##Integration mithilfe von OpenID Connect

Befolgen Sie zum Integrieren Ihrer Anwendung in Azure AD diese [Anweisungen für Entwickler](active-directory-authentication-scenarios.md). Stellen Sie die nachfolgend beschriebenen Informationen zusammen, und senden Sie sie an waadpartners@microsoft.com.

* Stellen Sie Anmeldeinformationen für einen Testmandanten oder ein Testkonto mit Ihrer Anwendung bereit, die vom Azure AD-Team zum Testen der Integration verwendet werden können.  

* Geben Sie uns Anweisungen, wie sich das Azure AD-Team anmelden und eine Instanz von Azure AD unter Verwendung des [Azure AD Consent Framework](active-directory-integrating-applications.md#overview-of-the-consent-framework) mit Ihrer Anwendung verbinden kann.

* Stellen Sie alle weiteren notwendigen Anweisungen bereit, damit das Azure AD-Team das einmalige Anmelden mit Ihrer Anwendung testen kann.

* Stellen Sie folgende Informationen bereit:

> Name des Unternehmens:
> 
> Website des Unternehmens:
> 
> Anwendungsname:
> 
> Anwendungsbeschreibung (max. 256 Zeichen):
> 
> Website der Anwendung (informativ):
> 
> Technische Support-Website für die Anwendung oder Kontaktinformationen:
> 
> Client-ID der Anwendung, wie in den Anwendungsdetails auf https://manage.windowsazure.com gezeigt:
> 
> Anmelde-URL, unter der sich die Kunden anmelden und/oder die Anwendung erwerben:
> 
> Wählen Sie bis zu drei Kategorien aus, unter denen Ihre Anwendung aufgelistet wird (verfügbare Kategorien finden Sie im Azure Active Directory Marketplace):
> 
> Kleines Anwendungssymbol einfügen (PNG-Datei, 45 px mal 45 px, Volltonfarbe für den Hintergrund):
> 
> Großes Anwendungssymbol einfügen (PNG-Datei, 215 px mal 215 px, Volltonfarbe für den Hintergrund):
> 
> Logo einfügen (PNG-Datei, 150 px mal 122 px, transparente Hintergrundfarbe):

##Integration mithilfe von SAML

Jede App, die SAML 2.0 unterstützt, kann direkt in einen Azure AD-Mandanten integriert werden. Dazu befolgen Sie [diese Anweisungen zum Hinzufügen einer benutzerdefinierten Anwendung](active-directory-saas-custom-apps.md). Wenn Sie die Anwendungsintegration in Azure AD erfolgreich getestet haben, senden Sie die folgenden Informationen an <waadpartners@microsoft.com>.

* Stellen Sie Anmeldeinformationen für einen Testmandanten oder ein Testkonto mit Ihrer Anwendung bereit, die vom Azure AD-Team zum Testen der Integration verwendet werden können.  

* Geben Sie die URL für einmaliges Anmelden mit SAML, die Aussteller-URL (Entitäts-ID) und die Antwort-URL (Assertion Consumer Service) für Ihre Anwendung an, wie [hier](active-directory-saas-custom-apps.md) beschrieben. Wenn Sie diese Werte in der Regel als Teil einer SAML-Metadatendatei zur Verfügung stellen, senden Sie uns diese ebenfalls.

* Beschreiben Sie kurz, wie Azure AD als Identitätsanbieter in ihrer Anwendung mithilfe von SAML 2.0 konfiguriert wird. Wenn Ihre Anwendung die Konfiguration von Azure AD als Identitätsanbieter über ein Self-Service-Verwaltungsportal unterstützt, stellen Sie sicher, dass dies mit den oben angegebenen Anmeldeinformation eingerichtet werden kann.

* Stellen Sie folgende Informationen bereit:

> Name des Unternehmens:
> 
> Website des Unternehmens:
> 
> Anwendungsname:
> 
> Anwendungsbeschreibung (max. 256 Zeichen):
> 
> Website der Anwendung (informativ):
> 
> Technische Support-Website für die Anwendung oder Kontaktinformationen:
> 
> Anmelde-URL, unter der sich die Kunden anmelden und/oder die Anwendung erwerben:
> 
> Wählen Sie bis zu drei Kategorien aus, unter denen Ihre Anwendung aufgelistet wird (verfügbare Kategorien finden Sie im [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)):
> 
> Kleines Anwendungssymbol einfügen (PNG-Datei, 45 px mal 45 px, Volltonfarbe für den Hintergrund):
> 
> Großes Anwendungssymbol einfügen (PNG-Datei, 215 px mal 215 px, Volltonfarbe für den Hintergrund):
> 
> Logo einfügen (PNG-Datei, 150 px mal 122 px, transparente Hintergrundfarbe):

<!---HONumber=AcomDC_0302_2016-->