<properties 
	pageTitle="Verwendungsszenarios und Bereitstellungsaspekte für Azure AD Join| Microsoft Azure" 
	description="Erläutert, wie Administratoren Azure AD Join für Ihre Endbenutzer (Mitarbeiter, Studenten und andere Benutzer) einrichten können. Zudem werden die unterschiedlichen Praxis-Szenarios, die bei der Verwendung von Azure AD Join möglich sind, erläutert." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="femila"/>

# Verwendungsszenarios und Bereitstellungsaspekte für Azure AD Join 

## Verwendungsszenarios für Azure AD Join
Szenario 1: Unternehmen, die größtenteils in der Cloud sind
--------------------------------------------------------
Azure AD Join kann davon profitieren, wenn Sie aktuell die Identitäten für Ihr Unternehmen in der Cloud ausführen und verwalten, oder diese in Kürze in die Cloud umziehen. Sie können ein Konto verwenden, das Sie in Azure AD erstellt haben, um sich bei Windows 10 anzumelden. Ihre Benutzer können ihre Computer entweder über [den Prozess des Eindrucks beim ersten Ausführen](active-directory-azureadjoin-user-frx.md) oder über [die Einstellungsumgebung](active-directory-azureadjoin-user-upgrade.md) mit Azure AD verknüpfen. Ihre Benutzer profitieren nun vom SSO-Zugriff auf ihre Cloudressourcen wie Office 365 entweder im Browser oder in den Office-Anwendungen.

Szenario 2: Bildungseinrichtungen ----------------------------------------------------------------------------------Bildungseinrichtungen weisen in der Regel zwei Benutzertypen auf: Lehrkräfte und Schüler. Lehrkräfte werden als längerfristige Mitglieder der Organisation betrachtet, sodass lokale Konten für diese Benutzer empfohlen werden. Studenten werden jedoch als kurzfristigere Mitglieder der Organisation angesehen und können somit in Azure AD verwaltet werden, sodass Verzeichnisse in der Cloud statt lokal abgelegt werden können. Studenten können sich jetzt mit ihrem Azure AD-Konto bei Windows anmelden und erhalten Zugriff auf die Office 365-Ressourcen in den Office-Anwendungen.

Szenario 3: Einzelhandelsunternehmen
---------------------------------------------------------------------------------------
Einzelhandelsunternehmen haben sowohl saisonbedingte als auch feste Mitarbeiter. Für feste Mitarbeiter in Vollzeit können lokale Konten erstellt werden. Sie verwenden in der Regel Computer, die in die Domäne eingebunden sind. Saisonbedingte Mitarbeiter sind jedoch kurzfristigere Mitglieder der Organisation, sodass sie dort verwaltet werden sollten, wo Benutzerlizenzen leichter verschoben werden können. Das Erstellen dieser Benutzer in der Cloud mit Office 365-Lizenzen ermöglicht diesen, von den Vorteilen bei der Anmeldung an Windows- und Office-Anwendungen mit einem Azure AD-Konto zu profitieren. Gleichzeitig sind die Lizenzen flexibler, nachdem diese Benutzer das Unternehmen verlassen haben.

Szenario 4: Zusätzliche Szenarien
------------------------------------------------------------------------------------------
Außer den oben beschriebenen Szenarien können Sie davon profitieren, dass Ihre Benutzer ihre Geräte mit Azure AD verbinden, da dadurch eine vereinfachte Verknüpfung, Geräteverwaltung in Azure AD, automatische MDM-Registrierung, und SSO bei Azure AD und lokalen Ressourcen ermöglicht wird.


##Bereitstellungsaspekte für Azure AD Join

Aktivieren Ihrer Benutzer für das direkte Verbinden eines Unternehmensgeräts mit Azure AD
-----------------------------------------------------------------------------------------

Unternehmen können Nur-Cloud-Konten für Partnerunternehmen und Organisationen bereitstellen. Diese Partner erhalten dann einfachen Zugriff und SSO für ihre Unternehmens-Apps und -Ressourcen. Dieses Szenario gilt für Benutzer, die ihre Geräte in erster Linie für den Zugriff auf Ressourcen in der Cloud verwenden, wie z. B. Office 365 oder SaaS-Apps, die Azure AD für die Authentifizierung benötigen.

### Voraussetzungen
**Auf Unternehmensebene (Administrator)**

*	Azure-Abonnement mit Azure Active Directory  

**Auf Benutzerebene**

*	Windows 10 (Professional- und Enterprise-SKUs)

### Administratoraufgaben
* [Einrichten der Geräteregistrierung](active-directory-azureadjoin-setup.md)

### Benutzeraufgaben
* [Einrichten eines neuen Windows 10-Geräts mit Azure AD während des Setups](active-directory-azureadjoin-user-frx.md)
* [Einrichten eines Windows 10-Geräts mit Azure AD in den Einstellungen](active-directory-azureadjoin-user-upgrade.md)
* [Verknüpfen eines persönlichen Windows 10-Geräts mit Ihrer Organisation](active-directory-azureadjoin-personal-device.md)
  


## Aktivieren von BYOD in Ihrer Organisation für Windows 10
Sie können Ihre Benutzer und Mitarbeiter so einrichten, dass sie ihre persönlichen Windows-Geräte für den Zugriff auf Unternehmens-Apps und -Ressourcen verwenden. Ihre Benutzer können ihre Azure AD-Konten (Geschäftskonten) zu einem persönlichen Windows-Gerät hinzufügen, um auf Arbeitsressourcen in einer sicheren und kompatiblen Art zuzugreifen.

### Voraussetzungen
**Auf Unternehmensebene (Administrator)**

*	Azure AD-Abonnement

**Auf Benutzerebene**

*	Windows 10 (Professional- und Enterprise-SKUs)


### Administratoraufgaben

* [Einrichten der Geräteregistrierung](active-directory-azureadjoin-setup.md)

### Benutzeraufgaben
* [Verknüpfen eines persönlichen Windows 10-Geräts mit Ihrer Organisation](active-directory-azureadjoin-personal-device.md)


## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0218_2016-->