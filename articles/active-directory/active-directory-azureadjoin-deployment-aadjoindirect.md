<properties
	pageTitle="Verwendungsszenarios und Bereitstellungsaspekte für Azure AD Join| Microsoft Azure"
	description="Erläutert, wie Administratoren Azure AD Join für Ihre Endbenutzer (Mitarbeiter, Studenten und andere Benutzer) einrichten können. Zudem werden die unterschiedlichen Praxis-Szenarios, die bei der Verwendung von Azure AD Join möglich sind, erläutert."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="02/26/2016"

	ms.author="femila"/>

# Verwendungsszenarios und Bereitstellungsaspekte für Azure AD Join

## Verwendungsszenarios für Azure AD Join
### Szenario 1: Unternehmen, die größtenteils in der Cloud sind

Azure Active Directory Join (Azure AD Join) kann davon profitieren, wenn Sie aktuell die Identitäten für Ihr Unternehmen in der Cloud ausführen und verwalten, oder diese in Kürze in die Cloud umziehen. Sie können ein Konto verwenden, das Sie in Azure AD erstellt haben, um sich bei Windows 10 anzumelden. Ihre Benutzer können ihre Computer entweder über [den Prozess des Eindrucks beim ersten Ausführen (First Run Experience, FRX)](active-directory-azureadjoin-user-frx.md) oder über [das Einstellungenmenü](active-directory-azureadjoin-user-upgrade.md) mit Azure AD verknüpfen. Ihre Benutzer kommen auch in den Genuss eines SSO-Zugriffs (Single Sign-On, einmaliges Anmelden) auf Cloudressourcen wie Office 365, und zwar entweder über ihre Browser oder in Office-Anwendungen.

### Szenario 2: Bildungseinrichtungen

Bildungseinrichtungen weisen in der Regel zwei Benutzertypen auf: Lehrkräfte und Schüler. Lehrkräfte werden als längerfristige Mitglieder der Organisation betrachtet. Erstellen von lokalen Konten für sie ist wünschenswert. Aber Schüler und Studenten sind für kürzere Zeiten Mitglieder der Organisation, und ihre Konten können daher in Azure AD verwaltet werden. Dies bedeutet, dass die Verzeichnisse in der Cloud abgelegt werden können und nicht lokal gespeichert werden müssen. Es bedeutet auch, dass Studenten sich mit ihren Azure AD-Konten an Windows anmelden und Zugriff auf Office 365-Ressourcen in Office-Anwendungen erhalten können.

### Szenario 3: Einzelhandelsunternehmen

Einzelhandelsunternehmen haben sowohl saisonbedingte als auch feste Mitarbeiter. Für feste Mitarbeiter in Vollzeit erstellen Sie in der Regel lokale Konten und verwenden Computer, die in die Domäne eingebunden sind. Da Saisonkräfte nur für kürzere Zeit Teil der Organisation sind, werden ihre Konten meist an einem Ort verwaltet, an dem Benutzerlizenzen leichter verschoben werden können. Wenn Sie ihre Benutzerkonten in der Cloud mit Office 365-Lizenzen erstellen, profitieren diese Benutzer von der Anmeldung bei Windows- und Office-Anwendungen mit einem Azure AD-Konto. Gleichzeitig können Sie ihre Lizenzen flexibler handhaben, nachdem diese Benutzer das Unternehmen verlassen haben.

### Szenario 4: Zusätzliche Szenarien

Zusammen mit den oben beschriebenen Szenarien profitieren Sie davon, dass Ihre Benutzer ihre Geräte mit Azure AD verbinden, da dadurch eine vereinfachte Verknüpfung, effiziente Geräteverwaltung, Registrierung bei der automatischen mobilen Geräteverwaltung und SSO bei Azure AD und lokalen Ressourcen ermöglicht wird.


## Bereitstellungsaspekte für Azure AD Join

### Aktivieren Ihrer Benutzer für das direkte Verbinden eines Unternehmensgeräts mit Azure AD


Unternehmen können Nur-Cloud-Konten für Partnerunternehmen und Organisationen bereitstellen. Diese Partner können dann einfach auf Unternehmens-Apps und Ressourcen mit einmaligem Anmelden zugreifen. Dieses Szenario gilt für Benutzer, die auf Ressourcen in erster Linie in der Cloud zugreifen, wie z. B. Office 365 oder SaaS-Apps, die Azure AD für die Authentifizierung benötigen.

### Voraussetzungen
**Auf Unternehmensebene (Administrator)**

*	Azure-Abonnement mit Azure Active Directory  

**Auf Benutzerebene**

*	Windows 10 (Professional- und Enterprise-Editionen)

### Administratoraufgaben
* [Einrichten der Geräteregistrierung](active-directory-azureadjoin-setup.md)

### Benutzeraufgaben
* [Einrichten eines neuen Windows 10-Geräts mit Azure AD während des Setups](active-directory-azureadjoin-user-frx.md)
* [Einrichten eines Windows 10-Geräts mit Azure AD im Einstellungenmenü](active-directory-azureadjoin-user-upgrade.md)
* [Verknüpfen eines persönlichen Windows 10-Geräts mit Ihrer Organisation](active-directory-azureadjoin-personal-device.md)



## Aktivieren von BYOD (Bring Your Own Device, BYOD) in Ihrer Organisation für Windows 10
Sie können Ihre Benutzer und Mitarbeiter so einrichten, dass sie ihre persönlichen Windows-Geräte (BYOD) für den Zugriff auf Unternehmens-Apps und -Ressourcen verwenden. Ihre Benutzer können ihre Azure AD-Konten (Geschäfts- oder Schulkonten) einem persönlichen Windows-Gerät hinzufügen, um in einer sicheren und kompatiblen Art auf Arbeitsressourcen zuzugreifen.

### Voraussetzungen
**Auf Unternehmensebene (Administrator)**

*	Azure AD-Abonnement

**Auf Benutzerebene**

*	Windows 10 (Professional- und Enterprise-Editionen)


### Administratoraufgaben

* [Einrichten der Geräteregistrierung](active-directory-azureadjoin-setup.md)

### Benutzeraufgaben
* [Verknüpfen eines persönlichen Windows 10-Geräts mit Ihrer Organisation](active-directory-azureadjoin-personal-device.md)


## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->