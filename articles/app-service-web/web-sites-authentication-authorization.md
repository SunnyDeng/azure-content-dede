<properties 
	pageTitle="Verwenden von Active Directory für die Authentifizierung in Azure App Service" 
	description="Lernen Sie die verschiedenen Optionen zur Authentifizierung und Autorisierung von Branchenanwendungen kennen, die in Azure App Service-Web-Apps bereitgestellt werden." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Verwenden von Active Directory für die Authentifizierung in Azure App Service #

[Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) ermöglichen Unternehmensszenarios für Branchenanwendungen, indem unabhängig davon, ob die Benutzer über Ihre lokale Umgebung oder das öffentliche Internet zugreifen, eine einmalige Anmeldung (SSO) unterstützt wird. Für die Authentifizierung und ordnungsgemäße Autorisierung Ihrer internen Active Directory-Benutzer kann eine Integration in [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) (AAD) oder lokale Dienste für sichere Token, wie beispielsweise Active Directory-Verbunddienste (AD FS), vorgenommen werden.

## Problemlose Authentifizierung und Autorisierung ##

Mit einigen wenigen Mausklicks können Sie die Authentifizierung und Autorisierung für Ihre Web-App aktivieren. Die Konfiguration jeder Azure-Web-App erfolgt über Kontrollkästchen und ermöglicht eine grundlegende Zugriffssteuerung Ihrer Branchen-Web-App. Erreicht wird dies durch die Erzwingung von HTTPS und Authentifizierung gegenüber einem Azure AD-Mandanten Ihrer Wahl, bevor Benutzer Zugriff auf sämtliche Web-App-Inhalte erhalten. Weitere Informationen finden Sie unter [Web Apps Authentication / Authorization](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/) (in englischer Sprache).

>[AZURE.NOTE]Diese Funktion steht derzeit als Vorschau zur Verfügung.

## Manuelles Implementieren der Authentifizierung und Autorisierung ##

In vielen Szenarien muss das Anwendungsverhalten in Bezug auf Authentifizierung und Autorisierung angepasst werden. Beispiele hierfür sind die Seite zur An-/Abmeldung, eine benutzerdefinierte Autorisierungslogik, mehrinstanzenfähige Anwendungen usw. In diesen Fällen kann es besser sein, die Authentifizierung und Autorisierung manuell zu konfigurieren, um mehr Flexibilität in Bezug auf die Funktionen zu erzielen. Nachfolgend werden die zwei wichtigsten Optionen erläutert.

-	[Azure AD](web-sites-dotnet-lob-application-azure-ad.md) - Sie können Authentifizierung und Autorisierung für Ihre Web-App mit Azure AD implementieren. Die Verwendung von Azure AD als Identitätsanbieter zeichnet sich durch folgende Eigenschaften aus:
	-	Unterstützung gängiger Authentifizierungsprotokolle, z. B. [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/) und [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Eine vollständige Liste der unterstützten Protokolle finden Sie unter [Azure Active Directory-Authentifizierungsprotokolle](http://msdn.microsoft.com/library/azure/dn151124.aspx).
	-	Möglichkeit zur Verwendung eines Identitätsanbieters nur für Azure ohne eine lokale Infrastruktur.
	-	Die Verzeichnissynchronisierung kann mit einem lokalen AD-Verzeichnis konfiguriert (und lokal verwaltet) werden.
	-	Azure AD mit Verzeichnissynchronisierung aus der lokalen AD-Domäne ermöglicht ein nahtloses SSO-Erlebnis für Ihre Web-App, wenn AD-Benutzer aus dem Intranet oder über das Internet zugreifen. Aus dem Intranet können AD-Benutzer über die integrierte Authentifizierung automatisch auf die Web-App zugreifen. Über das Internet können sich AD-Benutzer mit ihren Windows-Anmeldeinformationen an der Web-App anmelden.
	-	Bereitstellung von SSO für [alle von Azure AD unterstützten Anwendungen](/marketplace/active-directory/), einschließlich Azure, Office 365, Dynamics CRM Online, Windows Intune sowie Tausende von Cloudanwendungen von Drittanbietern. 
	-	Azure AD delegiert die Verwaltung von Anwendungen der [vertrauenden Seite](http://en.wikipedia.org/wiki/Relying_party) an Rollen ohne Administratorrechte, während der Anwendungszugriff auf vertrauliche Verzeichnisdaten weiterhin durch globale Administratoren konfiguriert werden muss.
	-	Sendet einen allgemeinen Satz an Anspruchstypen für alle Anwendungen der vertrauenden Seite. Eine Liste der Anspruchstypen finden Sie unter [Unterstützte Token- und Anspruchstypen](http://msdn.microsoft.com/library/azure/dn195587.aspx). Ansprüche können nicht angepasst werden.
	-	[Azure AD Graph-API](http://msdn.microsoft.com/library/azure/hh974476.aspx) ermöglicht Anwendungen den Zugriff auf Verzeichnisdaten in Azure AD.
-	[Lokale Sicherheitstokendienste wie AD FS](../web-sites-dotnet-lob-application-adfs/) - Sie können die Authentifizierung und Autorisierung für Ihre Web-App mit einem lokalen Sicherheitstokendienst wie beispielsweise AD FS konfigurieren. Die Verwendung lokaler Active Directory-Verbunddienste zeichnet sich durch folgende Eigenschaften aus:
	-	Die AD FS-Topologie muss lokal bereitgestellt werden, was mit Kosten und Verwaltungsaufwand verbunden ist.
	-	Dieser Ansatz ist am besten geeignet, wenn die Unternehmensrichtlinie die lokale Speicherung von Active Directory-Daten vorschreibt.
	-	Nur AD FS-Administratoren können [Vertrauensstellungen der vertrauenden Seite und Anspruchsregeln](http://technet.microsoft.com/library/dd807108.aspx) konfigurieren.
	-	Möglichkeit zur Verwaltung von [Ansprüchen](http://technet.microsoft.com/library/ee913571.aspx) auf Anwendungsbasis.
	-	Es ist eine separate Lösung für den Zugriff auf lokale Active Directory-Daten über die Unternehmensfirewall erforderlich.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->