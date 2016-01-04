<properties 
	pageTitle="Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join| Microsoft Azure" 
	description="Bietet eine ausführliche Übersicht darüber, wie Windows 10-Geräte mithilfe von Azure AD bei Azure Active Directory registriert werden können." 
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
	ms.date="12/07/2015" 
	ms.author="femila"/>

# Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join

## Was ist Azure Active Directory Join? 
Azure Active Directory (Azure AD) Join ist die Funktionalität, die ein zum Unternehmen gehörendes Gerät in Azure Active Directory registriert, um die zentralisierte Verwaltung zu ermöglichen. Dadurch können Benutzer (Mitarbeiter, Schüler) nun über Azure Active Directory mit der Unternehmenscloud verbunden werden. Dies ermöglicht vereinfachte Windows-Bereitstellungen, den Zugriff auf Organisations-Apps und Ressourcen von einem beliebigen Windows-Gerät aus, sowohl unternehmenseigene als auch persönliche Geräte (BYOD).

Azure AD Join ist für Unternehmen gedacht, die hauptsächlich oder ausschließlich in der Cloud arbeiten (in der Regel kleine und mittelständische Unternehmen, die über keine lokale Active Directory-Infrastruktur verfügen). Allerdings kann und wird Azure AD auch von großen Organisationen genutzt, die keine herkömmliche Domänenverknüpfung einrichten können (z. B. für mobile Geräte), oder von Benutzern, die primär Zugriff auf Office 365 oder andere Azure AD-Saas-Apps benötigen.

Während die herkömmliche Domänenverknüpfung die beste lokale Benutzererfahrung für Geräte bietet, die mit einer Domäne verknüpft werden können, ist Azure AD Join für solche Geräte geeignet, bei denen dies nicht möglich ist, sowie für Bereiche, in denen Sie Benutzer in der Cloud mit MDM-Funktionen anstelle der herkömmlichen Domänenverwaltung mit einer Gruppenrichtlinie und SCCM verwalten möchten.

![](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## Warum Unternehmen Azure AD Join übernehmen sollten 

* **Wenn Ihr Unternehmen sich hauptsächlich in der Cloud befindet**: Wenn Sie ein Modell besitzen oder auf eines wechseln, bei dem Sie Ihren lokalen Fußabdruck reduzieren und verstärkt in der Cloud arbeiten möchten, könnte Azure AD Join Ihnen Vorteile bringen. Sie haben Azure AD-Konten vielleicht manuell oder über die Synchronisierung des lokalen Active Directory erstellt. In jedem Fall besitzen Sie ein Konto in Azure AD und können es zur Anmeldung bei Windows 10 verwenden. Ihre Benutzer können ihre Computer mit Azure AD verknüpfen, entweder über den OOBE-Prozess oder über den Vorgang in den Einstellungen. Ihre Benutzer profitieren nun vom SSO-Zugriff auf ihre Cloudressourcen wie Office 365 entweder im Browser oder in den Office-Anwendungen. 
* **Bildungseinrichtungen**: Zu den großen Szenarien gehört, dass Bildungseinrichtungen zwei Benutzertypen haben, Lehrkräfte und Schüler. Lehrkräfte werden als längerfristige Mitglieder der Organisation betrachtet, sodass lokale Konten für diese Benutzer empfohlen werden. Schüler werden jedoch als kurzfristigere Mitglieder der Organisation angesehen und können somit in Azure AD verwaltet werden, sodass Verzeichnisse in der Cloud statt lokal abgelegt werden können. Schüler können sich jetzt mit ihrem Azure AD-Konto bei Windows anmelden und erhalten Zugriff auf die Office 365-Ressourcen in den Office-Anwendungen. 
* **Einzelhandelsunternehmen**: In diesem Bereich ist eine Anforderung, saisonale Arbeitnehmer einfacher verwalten zu können. Für feste Mitarbeiter in Vollzeit werden auch hier lokale Konten erstellt. Sie verwenden in der Regel Computer, die in die Domäne eingebunden sind. Saisonbedingte Mitarbeiter sind jedoch kurzfristigere Mitglieder der Organisation, sodass sie dort verwaltet werden sollten, wo Benutzerlizenzen leichter verschoben werden können. Das Erstellen dieser Benutzer in der Cloud mit Office 365-Lizenzen ermöglicht diesen, von den Vorteilen bei der Anmeldung an Windows- und Office-Anwendungen mit einem Azure AD-Konto zu profitieren. Gleichzeitig sind die Lizenzen flexibler, nachdem diese Benutzer das Unternehmen verlassen haben. 
* **Andere Unternehmen**: Über diese spezifischen Szenarien hinaus kann es sein, dass Sie Ihre Benutzer zwar lokal im AD-Verzeichnis verwalten, aber dennoch davon profitieren können, wenn diese Azure AD Join verwenden. Vorteile sind die vereinfachte Verknüpfung, die Geräteverwaltung in Azure AD, die automatische MDM-Registrierung und einmaliges Anmelden bei Azure AD und lokalen Ressourcen.  

## Azure AD Join-Funktionen
Azure AD Join bietet folgende Funktionen:

* **Selbstverwaltung von unternehmenseigenen Geräten**: Unter Windows 10 können Endbenutzer ein völlig neues, noch eingeschweißtes Gerät während des Eindrucks beim ersten Ausführen ohne Beteiligung der IT konfigurieren.


* **Unterstützung moderner Formfaktoren**: Azure AD Join funktioniert auf Geräten, die traditionell keine Funktionen zum Beitritt zu einer Domäne besitzen.


* **Nutzung vorhandener Organisationskonten**: Endbenutzer müssen nicht länger ein persönliches Microsoft-Konto erstellen und verwalten, um vom Unternehmen ausgegebene Geräte optimal nutzen zu können, wie dies unter Windows 8 der Fall war. Sie können stattdessen ihr vorhandenes Arbeitskonto in Azure AD verwenden. Für viele Organisationen bedeutet dies im Grunde das Einrichten und Anmelden bei Windows mit den gleichen Anmeldeinformationen wie bei Office 365.


* **Automatische MDM-Registrierung**: Geräte können automatisch in der Verwaltung registriert werden, wenn sie mit Azure AD verknüpft sind. Dies funktioniert mit Microsoft Intune und MDMs anderer Anbieter. Bei der Verwaltung durch Intune kann die IT mit Azure AD verbundene Geräte gemeinsam mit in Domänen verbundenen Geräte in der SCCM-Verwaltungskonsole überwachen und verwalten.


* **Einmaliges Anmelden auf Unternehmensressourcen**: Benutzer profitieren vom einmaligen Anmelden vom Windows-Desktop auf Apps und Ressourcen in der Cloud, wie z. B. Office 365 und tausenden von Geschäftsanwendungen, die zur Authentifizierung über [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md) auf Azure AD zurückgreifen. Unternehmenseigene Geräte, die mit Azure AD verknüpft sind, profitieren auch vom einmaligen Anmelden auf lokalen Ressourcen, wenn sich das Gerät im Unternehmensnetzwerk befindet, und von überall aus, wenn diese Ressourcen über den [Azure AD-Anwendungsproxy](https://msdn.microsoft.com/library/azure/Dn768219.aspx) verfügbar gemacht werden.


* **Betriebssystem-Zustandsroaming**: Dinge wie Zugriffseinstellungen, Websites und Wi-Fi-Kennwörter werden über unternehmenseigene Geräte hinweg synchronisiert, ohne dass ein persönliches Microsoft-Konto erforderlich ist.


* **Unternehmensfähiger Windows Store**: Windows Store unterstützt die Anschaffung von Apps und die Lizenzierung mit Azure AD-Konten. Organisationen können Volumenlizenz-Apps verwenden und den Benutzern in ihrer Organisation verfügbar machen.

## Funktionsweise von verschiedene Geräten mit Azure AD Join

| Unternehmensgeräte (mit lokaler Domäne verknüpft) | Unternehmensgeräte (mit der Cloud verknüpft) | Persönliche Geräte |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Benutzer melden Sie sich bei Windows mit den Anmeldeinformationen ihrer Arbeit an (wie dies aktuell der Fall ist). | Benutzer können sich bei Windows mit den Anmeldeinformationen Ihrer Arbeit anmelden, die in Azure AD verwaltet werden. Dies ist für unternehmenseigene Geräte in drei Fällen relevant: 1. Die Organisation verfügt über kein lokales AD (z. B. Kleinunternehmen). 2. Die Organisation erstellt nicht alle Benutzerkonten in AD (z. B. Schüler, Berater, saisonale Mitarbeiter). 3. Unternehmenseigene Geräte, die nicht mit einer (lokalen) Domäne verknüpft werden können, wie Telefone oder Tablets mit Mobile SKU. Beispielsweise funktioniert ein sekundäres Gerät auf Fabrik-/Einzelhandelsebene bei verwalteten und verbundenen Organisationen. | Benutzer melden Sie sich bei Windows mit ihren persönlichen MSA-Anmeldeinformationen an (keine Änderung). |
| Benutzer haben Zugriff auf das Roaming von Einstellungen und auf Windows Store. Diese Dienste funktionieren mit Arbeitskonten (eine persönliche MSA ist nicht erforderlich). Erfordert, dass Unternehmen ihre lokale AD mit Azure AD verknüpfen. | Self-Service-Einrichtung – Benutzer können den Eindruck beim ersten Ausführen über ihr Arbeitskonto durchgehen (als Alternative dazu, dass die IT die Geräte bereitstellt, wobei beide Methoden unterstützt werden). | Es ist spielend leicht, ein Arbeitskonto hinzuzufügen, dass in AD oder Azure AD verwaltet wird. |
| Einmaliges Anmelden vom Desktop, um mit Apps, Websites oder Ressourcen zu arbeiten. Dies funktioniert sowohl lokal als auch in der Cloud für Apps, die Azure AD für die Authentifizierung nutzen. | Automatische Registrierung im Unternehmensverzeichnis (Azure AD) und für das MDM. (Azure AD Premium-Funktion) | Liefert einmaliges Anmelden für Apps und für Websites oder Ressourcen mit diesem Arbeitskonto |
| Benutzer können ihre persönliche MSA für den Zugriff auf eigene Bilder oder Dateien hinzufügen, ohne Unternehmensdaten zu beeinträchtigen (die Roamingeinstellungen funktionieren weiterhin mit dem Konto). Das MSA-Konto ermöglicht einmaliges Anmelden und führt nicht länger das Roaming der Einstellungen aus. | Self-Service-Kennwortzurücksetzung für die Windows-Anmeldung (die Möglichkeit, ein vergessenes Kennwort zurückzusetzen). (Hierfür benötigen Sie Azure AD Premium.) | Ermöglicht den Zugriff auf den Enterprise Store, damit Benutzer Branchen-Apps erwerben und auf ihren persönlichen Geräten verwenden können. | |


## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von einer Domäne beigetretenen Geräten mit Azure AD für Windows 10-Benutzeroberflächen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1210_2015-->