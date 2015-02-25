<properties 
	pageTitle="Authentifizieren und Autorisieren von Branchenanwendungen auf Azure-Websites" 
	description="Lernen Sie die verschiedenen Optionen zur Authentifizierung und Autorisierung von Branchenanwendungen kennen, die auf Azure-Websites bereitgestellt werden." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="12/23/2014" 
	ms.author="cephalin"/>

# Authentifizieren und Autorisieren von Benutzern in Branchenanwendungen auf Azure-Websites #

[Azure-Websites](http://azure.microsoft.com/de-de/services/websites/) ermöglichen Unternehmensszenarien für Branchenanwendungen, indem unabhängig davon, ob die Benutzer über Ihre lokale Umgebung oder das öffentliche Internet zugreifen, eine einmalige Anmeldung (SSO) unterstützt wird. Für die Authentifizierung und ordnungsgemäße Autorisierung Ihrer internen Active Directory-Benutzer kann eine Integration in [Azure Active Directory](http://azure.microsoft.com/de-de/services/active-directory/) (AAD) oder lokale Dienste für sichere Token, wie beispielsweise Active Directory-Verbunddienste (AD FS), vorgenommen werden.

## Problemlose Authentifizierung und Autorisierung ##

Mit einigen wenigen Mausklicks können Sie die Authentifizierung und Autorisierung für Ihre Website aktivieren. Die Konfiguration jeder Azure-Website erfolgt über Kontrollkästchen und ermöglicht eine grundlegende Zugriffssteuerung Ihrer Branchenwebsite. Erreicht wird dies durch die Erzwingung von HTTPS und Authentifizierung gegenüber einem Azure AD-Mandanten Ihrer Wahl, bevor Benutzer Zugriff auf sämtliche Websiteinhalte erhalten. Weitere Informationen finden Sie unter [Authentifizierung / Autorisierung für Azure-Websites](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/).

>[AZURE.NOTE] Diese Funktion steht derzeit als Vorschau zur Verfügung.

## Manuelles Implementieren der Authentifizierung und Autorisierung ##

In vielen Szenarien muss das Anwendungsverhalten in Bezug auf Authentifizierung und Autorisierung angepasst werden. Beispiele hierfür sind die Seite zur An-/Abmeldung, eine benutzerdefinierte Autorisierungslogik, mehrinstanzenfähige Anwendungen usw. In diesen Fällen kann es besser sein, die Authentifizierung und Autorisierung manuell zu konfigurieren, um mehr Flexibilität in Bezug auf die Funktionen zu erzielen. Nachfolgend werden die zwei wichtigsten Optionen erläutert.  

-	[Azure AD](../web-sites-dotnet-lob-application-azure-ad/) - Sie können Authentifizierung und Autorisierung für Ihre Website mit Azure AD implementieren. Die Verwendung von Azure AD als Identitätsanbieter zeichnet sich durch folgende Eigenschaften aus:
	-	Unterstützung gängiger Authentifizierungsprotokolle, z. B. [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/) und [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Eine vollständige Liste der unterstützten Protokolle finden Sie unter [Azure Active Directory-Authentifizierungsprotokolle](http://msdn.microsoft.com/de-de/library/azure/dn151124.aspx).
	-	Möglichkeit zur Verwendung eines Identitätsanbieters nur für Azure ohne eine lokale Infrastruktur.
	-	Die Verzeichnissynchronisierung kann mit einem lokalen AD-Verzeichnis konfiguriert (und lokal verwaltet) werden.
	-	Azure AD mit Verzeichnissynchronisierung aus der lokalen AD-Domäne ermöglicht ein nahtloses SSO-Erlebnis für Ihre Website, wenn AD-Benutzer aus dem Intranet oder über das Internet zugreifen. Aus dem Intranet können AD-Benutzer über die integrierte Authentifizierung automatisch auf die Website zugreifen. Über das Internet können sich AD-Benutzer mit ihren Windows-Anmeldeinformationen an der Website anmelden.
	-	Bereitstellung von SSO für [alle von Azure AD unterstützten Anwendungen](http://azure.microsoft.com/de-de/marketplace/active-directory/), einschließlich Azure, Office 365, Dynamics CRM Online, Windows InTune sowie Tausende von Cloudanwendungen von Drittanbietern. 
	-	Azure AD delegiert die Verwaltung von Anwendungen der [vertrauenden Seite](http://en.wikipedia.org/wiki/Relying_party) an Rollen ohne Administratorrechte, während der Anwendungszugriff auf vertrauliche Verzeichnisdaten weiterhin durch globale Administratoren konfiguriert werden muss.
	-	Sendet einen allgemeinen Satz an Anspruchstypen für alle Anwendungen der vertrauenden Seite. Eine Liste der Anspruchstypen finden Sie unter [Unterstützte Token- und Anspruchstypen](http://msdn.microsoft.com/de-de/library/azure/dn195587.aspx). Ansprüche können nicht angepasst werden.
	-	[Azure AD Graph-API](http://msdn.microsoft.com/de-de/library/azure/hh974476.aspx) ermöglicht Anwendungen den Zugriff auf Verzeichnisdaten in Azure AD.
-	[Lokale Sicherheitstokendienste, wie z. B. AD FS](../web-sites-dotnet-lob-application-adfs/) - Sie können die Authentifizierung und Autorisierung für Ihre Website mit einem lokalen Sicherheitstokendienst wie beispielsweise AD FS konfigurieren. Die Verwendung lokaler Active Directory-Verbunddienste zeichnet sich durch folgende Eigenschaften aus:
	-	Die AD FS-Topologie muss lokal bereitgestellt werden, was mit Kosten und Verwaltungsaufwand verbunden ist.
	-	Dieser Ansatz ist am besten geeignet, wenn die Unternehmensrichtlinie die lokale Speicherung von Active Directory-Daten vorschreibt.
	-	Nur AD FS-Administratoren können [Vertrauensstellungen der vertrauenden Seite und Anspruchsregeln](http://technet.microsoft.com/de-de/library/dd807108.aspx) konfigurieren.
	-	Möglichkeit zur Verwaltung von [Ansprüchen](http://technet.microsoft.com/de-de/library/ee913571.aspx) auf Anwendungsbasis.
	-	Es ist eine separate Lösung für den Zugriff auf lokale Active Directory-Daten über die Unternehmensfirewall erforderlich.




<!--HONumber=42-->
