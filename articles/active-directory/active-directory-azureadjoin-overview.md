<properties
	pageTitle="Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join| Microsoft Azure"
	description="Enthält eine ausführliche Übersicht darüber, wie Windows 10-Geräte mithilfe von Azure AD bei Azure Active Directory registriert werden können."
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
Azure Active Directory Join (Azure AD Join) ist die Funktionalität, mit der ein zum Unternehmen gehörendes Gerät in Azure Active Directory registriert wird, um die zentralisierte Verwaltung des Geräts zu ermöglichen. So können Benutzer, z. B. Mitarbeiter und Studenten, eine Verbindung mit der Cloud des Unternehmens über Azure Active Directory herstellen. Dies ermöglicht vereinfachte Windows-Bereitstellungen und den Zugriff auf Organisations-Apps und Ressourcen von einem beliebigen Windows-Gerät aus – sowohl mit unternehmenseigenen als auch mit persönlichen Geräten (BYOD).

Azure AD Join ist für Unternehmen bestimmt, die neu auf die Cloud umstellen oder ganz auf die Cloud setzen. Es handelt sich meist um kleine bis mittelgroße Unternehmen, die keine lokale Windows Server Active Directory-Infrastruktur haben. Azure AD Join kann aber auch von großen Unternehmen auf Geräten genutzt werden, mit denen kein herkömmlicher Domänenbeitritt möglich ist (z. B. mobile Geräte), oder für Benutzer, die vor allem auf Office 365 oder andere Azure AD-SaaS-Apps zugreifen müssen.

Obwohl der herkömmliche Domänenbeitritt weiterhin die beste lokale Option für Geräte darstellt, mit denen der Beitritt zu einer Domäne durchgeführt werden kann, eignet sich Azure AD Join auch für Geräte, die diese Option nicht bieten. Außerdem ist Azure AD Join zum Verwalten von Benutzern in der Cloud geeignet. Hierfür werden Funktionen für die Verwaltung mobiler Geräte eingesetzt, und keine herkömmlichen Tools für die Domänenverwaltung, z. B. Gruppenrichtlinie und System Center Configuration Manager (SCCM).

![Übersicht über unternehmenseigene Geräte und persönliche Geräte mit lokalem Active Directory und Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## Warum sollten Unternehmen Azure AD Join einführen?

* **Unternehmen, die hauptsächlich in der Cloud arbeiten**: Wenn Sie ein Modell besitzen oder zu einem Modell wechseln, bei dem Sie Ihren lokalen Fußabdruck reduzieren und verstärkt in der Cloud arbeiten möchten, können Sie von Azure AD Join profitieren. Sie haben Azure AD-Konten vielleicht manuell oder über die Synchronisierung Ihrer lokalen Active Directory-Daten erstellt. In jedem Fall besitzen Sie ein Konto in Azure AD und können es zur Anmeldung bei Windows 10 verwenden. Ihre Benutzer können ihre Computer mit Azure AD verknüpfen, entweder über die Out-of-Box-Experience (OOBE) oder über das Menü „Einstellungen“. Nach dem Beitritt kommen Benutzer in den Genuss eines SSO-Zugriffs (Single Sign-On, einmaliges Anmelden) auf Cloudressourcen wie Office 365, und zwar entweder über ihre Browser oder in Office-Anwendungen.

* **Bildungseinrichtungen**: Zu den häufig genannten Szenarien gehört hierbei, dass Bildungseinrichtungen zwei Benutzertypen haben: Lehrkräfte und Schüler bzw. Studenten. Lehrkräfte werden als längerfristige Mitglieder der Organisation betrachtet, sodass für diese Benutzer lokale Konten empfohlen werden. Aber Schüler und Studenten sind nur für kürzere Zeit Mitglieder der Organisation und können daher in Azure AD verwaltet werden. Dies bedeutet, dass die Verzeichnisse in der Cloud abgelegt werden können und nicht lokal gespeichert werden müssen. Es bedeutet auch, dass Studenten sich mit ihren Azure AD-Konten an Windows anmelden und entweder über ihre Browser oder in Office-Anwendungen Zugriff auf Office 365-Ressourcen erhalten können.

* **Einzelhandelsunternehmen**: Ein weiteres Szenario, über das von unseren Kunden berichtet wird, ist der Wunsch, dass Saisonkräfte einfacher verwaltet werden können. Konten für längerfristig beschäftigte Vollzeitmitarbeiter werden normalerweise als lokale Konten auf Computern erstellt, die einer Domäne beigetreten sind. Da Saisonkräfte nur für kürzere Zeit Teil der Organisation sind, werden sie meist an einem Ort verwaltet, an dem Benutzerlizenzen leichter verschoben werden können. Durch das Erstellen dieser Benutzerkonten in der Cloud mit Office 365-Lizenzen haben die Benutzer den Vorteil, dass sie sich an Windows und Office-Anwendungen mit einem Azure AD-Konto anmelden können. Und Sie können ihre Lizenzen flexibler handhaben, wenn diese Personen das Unternehmen wieder verlassen.
* **Andere Unternehmen**: Sie verwalten Benutzer zwar in Ihrer lokalen Active Directory-Umgebung, aber Sie können trotzdem davon profitieren, dass Benutzer Azure AD beitreten. Dies hat folgenden Grund: Azure AD bietet eine vereinfachte Benutzeroberfläche für den Beitritt, effiziente Geräteverwaltung, automatische Registrierung bei der Verwaltung mobiler Geräte und einmaliges Anmelden für Azure AD und lokale Ressourcen.  

## Welche Funktionen bietet Azure AD Join?
Azure AD Join bietet folgende Funktionen:

* **Eigene Bereitstellung von unternehmenseigenen Geräten**: Unter Windows 10 können Benutzer ein völlig neues, noch eingeschweißtes Gerät während des Eindrucks beim ersten Ausführen ohne Beteiligung der IT-Abteilung konfigurieren.


* **Unterstützung moderner Formfaktoren**: Azure AD Join funktioniert auf Geräten, die nicht über die herkömmlichen Funktionen zum Beitritt zu einer Domäne verfügen.


* **Unterstützung vorhandener Organisationskonten**: Benutzer müssen nicht mehr ein persönliches Microsoft-Konto erstellen und verwalten, um vom Unternehmen ausgegebene Geräte optimal nutzen zu können, wie dies unter Windows 8 der Fall war. Sie können stattdessen ihre vorhandenen Geschäftskonten in Azure AD verwenden. Für viele Organisationen bedeutet dies im Grunde, dass Benutzer das Einrichten und Anmelden bei Windows mit den gleichen Anmeldeinformationen wie für Office 365 durchführen können.


* **Automatische Registrierung für die mobile Geräteverwaltung**: Geräte können automatisch für die Verwaltung mobiler Geräte registriert werden, wenn sie über eine Verbindung mit Azure AD verfügen. Dieser Prozess funktioniert mit Microsoft Intune und Partnerlösungen für die Verwaltung mobiler Geräte. Wenn die Geräteverwaltung mit Intune durchgeführt wird, können IT-Administratoren Azure AD beigetretene Geräte parallel zu in die Domäne eingebundenen Geräten in der SCCM-Verwaltungskonsole überwachen und verwalten.


* **Einmaliges Anmelden auf Unternehmensressourcen**: Benutzer profitieren vom einmaligen Anmelden vom Windows-Desktop an Apps und Ressourcen in der Cloud, z. B. Office 365 und Tausenden von Geschäftsanwendungen, die zur Authentifizierung über [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md) auf Azure AD zurückgreifen. Unternehmenseigene Geräte, die mit Azure AD verknüpft sind, profitieren auch vom einmaligen Anmelden an lokalen Ressourcen, wenn sich das Gerät im Unternehmensnetzwerk befindet, und von überall aus, wenn diese Ressourcen über den [Azure AD-Anwendungsproxy](https://msdn.microsoft.com/library/azure/Dn768219.aspx) verfügbar gemacht werden.


* **Betriebssystem-Zustandsroaming**: Zugriffseinstellungen, Websites, WLAN-Kennwörter und andere Einstellungen werden über unternehmenseigene Geräte hinweg synchronisiert, ohne dass ein persönliches Microsoft-Konto erforderlich ist.


* **Für größere Unternehmen geeigneter Windows Store**: Der Windows Store unterstützt die Anschaffung von Apps und die Lizenzierung mit Azure AD-Konten. Organisationen können Volumenlizenz-Apps verwenden und für die Benutzer in ihrer Organisation verfügbar machen.

## Wie funktionieren verschiedene Geräte mit Azure AD Join?

| Unternehmensgerät (mit lokaler Domäne verknüpft) | Unternehmensgerät (mit der Cloud verknüpft) | Persönliches Gerät |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Benutzer können sich bei Windows mit den geschäftlichen Anmeldeinformationen anmelden (wie dies aktuell der Fall ist). | Benutzer können sich bei Windows mit den Anmeldeinformationen Ihrer Arbeit anmelden, die in Azure AD verwaltet werden. Dies ist in drei Fällen für unternehmenseigene Geräte relevant: 1) Die Organisation verfügt nicht über eine lokale Active Directory-Umgebung (z. B. ein kleines Unternehmen). 2) Die Organisation erstellt nicht alle Benutzerkonten in Active Directory (Konten für Studenten, Berater oder Saisonkräfte werden beispielsweise nicht in Active Directory erstellt). 3) Die Organisation verfügt über unternehmenseigene Geräte, für die der Beitritt zu einer (lokalen) Domäne durchgeführt werden kann, z. B. Smartphones oder Tablets, auf denen eine Mobile SKU ausgeführt wird (beispielsweise ein Zweitgerät, das in einen anderen Bereich eines Werks oder eines Einzelhandelsgeschäfts mitgenommen wird). Azure AD Join unterstützt den Beitritt von unternehmenseigenen Geräten sowohl für verwaltete Unternehmen als auch für Unternehmen mit Partnerverbund. | Benutzer melden sich an Windows mit den Anmeldeinformationen ihres persönlichen Microsoft-Kontos an (keine Änderung). |
| Benutzer haben Zugriff auf die Roamingeinstellungen und den Windows Store des Unternehmens. Diese Dienste arbeiten mit Geschäftskonten, und ein persönliches Microsoft-Konto ist nicht erforderlich. Organisationen müssen ihre lokale Active Directory-Umgebung daher mit Azure AD verbinden. | Benutzer können die Einrichtung selbst durchführen. Sie können den Eindruck beim ersten Ausführen (First-Run Experience, FRX) über ihr Geschäftskonto durchführen und sind nicht davon abhängig, dass die Geräte von der IT-Abteilung bereitgestellt werden. Es werden aber beide Methoden unterstützt. | Benutzer können leicht ein Geschäftskonto hinzufügen, das in Active Directory oder Azure AD verwaltet wird. |
| Benutzer können SSO auf dem Desktop sowie für geschäftliche Apps, Websites und Ressourcen nutzen – einschließlich lokaler Ressourcen und Cloud-Apps, bei denen Azure AD für die Authentifizierung verwendet wird. | Geräte werden automatisch im Unternehmensverzeichnis (Azure AD) registriert und automatisch für die Verwaltung mobiler Geräte registriert. (Dies ist eine Azure AD Premium-Funktion.) | Mit diesem Geschäftskonto können Benutzer SSO für Apps und Websites/Ressourcen nutzen. |
| Benutzer können ihre persönlichen Microsoft-Konten hinzufügen, um auf ihre persönlichen Bilder und Dateien zuzugreifen, ohne dass Unternehmensdaten beeinträchtigt werden. (Roamingeinstellungen funktionieren für ihre Geschäftskonten weiterhin.) Das Microsoft-Konto ermöglicht die Nutzung des einmaligen Anmeldens und führt kein Roaming von Einstellungen mehr durch. | Benutzer können eine Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) für die Windows-Anmeldung durchführen. Es ist also möglich, ein vergessenes Kennwort zurückzusetzen. (Dies ist eine Azure AD Premium-Funktion.) | Benutzer haben Zugriff auf den Windows Store des Unternehmens, damit sie Branchen-Apps auf ihren persönlichen Geräten erhalten und nutzen können. | |


## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0224_2016-->