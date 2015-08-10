<properties
	pageTitle="Azure AD-Terminologie | Microsoft Azure"
	description="Begriffe und Definitionen im Zusammenhang mit Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="TerryLan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="curtand"/>

# Azure AD-Terminologie

Microsoft Azure Active Directory \(Azure AD\) verfügt für Cloud-, Hybrid und lokale Szenarien über eigene Terminologie. In der folgende Tabelle werden diese Begriffe samt Definition vorgestellt.

 Begriff | Definition
------------- | -------------
Zusätzliche Sicherheitsüberprüfung | Eine Sicherheitseinstellung, die ein globaler Administrator für ein Benutzerkonto im Verzeichnis einer Organisation festlegen kann, um anzufordern, dass sowohl das Kennwort eines Benutzers als auch eine Antwort von seinem Telefon verwendet werden muss, um diese Identität im Azure Active Directory-Authentifizierungssystem zu verifizieren.
Azure Active Directory | Der Identitätsdienst in Azure, der die Identitätsverwaltung und die Zugriffssteuerungsfunktionen über eine auf REST basierende API bereitstellt.
Microsoft Azure Access Control Service | Der Azure-Dienst, der Verbundauthentifizierung und eine durch Regeln gesteuerte und auf Ansprüchen basierende Autorisierung für REST-Webdienste bereitstellt.
Azure Active Directory-Authentifizierungssystem | Der Identitätsdienst von Microsoft in der Cloud, der zum Authentifizieren und Autorisieren von Geschäfts- oder Schulkonten verwendet wird.
Azure Active Directory Graph | Eine Funktion von Azure Active Directory, die Benutzer-, Gruppen- und Rollenobjekte in einem sozialen Unternehmensdiagramm darstellt, um Benutzerinformationen und Beziehungen aufzuzeigen.
Azure Active Directory-Modul für Windows PowerShell | Eine Gruppe von Cmdlets, die zum Verwalten von Azure Active Directory verwendet werden. Sie können diese Cmdlets zum Verwalten von Benutzern, Gruppen, Domänen, Clouddienstabonnements, Lizenzen, der Verzeichnissynchronisierung, des einmaligen Anmeldens usw. verwenden.
Azure Active Directory Connect | Der Azure Active Directory Connect-Assistent stellt das einzige Tool samt Anleitungen zum Verbinden Ihrer lokalen Verzeichnisse mit Azure Active Directory dar. Der Assistent implementiert und konfiguriert alle Komponenten, die erforderlich sind, um Ihre Verzeichnisintegration einzurichten und auszuführen, einschließlich Synchronisierungsdiensten, Kennwortsynchronisierung oder Active Directory-Verbunddiensten \(AD FS\) sowie Voraussetzungen wie dem Azure AD PowerShell-Modul.
Azure Active Directory-Synchronisierungstool | Die Anwendung, die eine unidirektionale Synchronisierung von Verzeichnisobjekten aus einem lokalen Active Directory-Dienst eines Unternehmens in Azure Active Directory bereitstellt.
Verzeichnisintegration | Ein Merkmal von Azure Active Directory, das eingerichtet werden kann, um die Verwaltungsbenutzeroberfläche zu verbessern, die für das Verwalten von Identitäten in Ihrem lokalen und Ihrem Cloudverzeichnis verwendet wird. Verzeichnisintegrationsszenarien umfassen die Verzeichnissynchronisierung und die Verzeichnissynchronisierung mit einmaligem Anmelden.
Verzeichnissynchronisierung | Wird zum Synchronisieren lokaler Verzeichnisobjekte \(Benutzer, Gruppen, Kontakte\) mit der Cloud verwendet, um administrativen Aufwand zu verringern. Verzeichnissynchronisierung wird im Azure AD-Portal und Azure-Verwaltungsportal ggf. auch als "DirSync" bezeichnet. Nachdem die Verzeichnissynchronisierung eingerichtet wurde, können Administratoren Verzeichnisobjekte in einem lokalen Active Directory-Verzeichnis für eine Azure AD-Instanz bereitstellen.
Microsoft Online Services-Anmelde-Assistent | Der Anmelde-Assistent ist eine auf einem Clientcomputer installierte Anwendung, die es einem Benutzer ermöglicht, sich einmal an diesem Computer anzumelden und dann beliebig oft während der Anmeldesitzung auf Dienste zuzugreifen. Ohne den Anmelde-Assistenten müssen Endbenutzer bei jedem Zugriffsversuch auf einen Dienst einen Namen und ein Kennwort angeben. Der Anmelde-Assistent darf nicht mit einmaligem Anmelden \(SSO\) verwechselt werden. Letzteres ist eine Verzeichnisintegrationsfunktion von Azure Active Directory, die bereitgestellt werden kann, um die vorhandenen lokalen Unternehmensanmeldeinformationen eines Benutzers für den nahtlosen Zugriff auf Clouddienste von Microsoft zu nutzen.
Multi-Factor Authentication \(auch bekannt als zweistufige Authentifizierung oder 2FA\) | Die Multi-Factor Authentication fügt Benutzeranmeldungen und Transaktionen eine wichtige zweite Sicherheitsebene hinzu. Wenn Sie die Multi-Factor Authentication für ein Benutzerkonto in Azure AD aktivieren, muss der betreffende Benutzer bei jeder Anmeldung zusätzlich zu den standardmäßigen Kennwortanmeldeinformationen sein Telefon als zusätzliche Sicherheitsüberprüfungsmethode verwenden, um einen der Clouddienste von Microsoft verwenden zu können, die Ihre Organisation abonniert hat.
Einmaliges Anmelden | Dient dazu, Benutzern beim Zugriff auf Clouddienste von Microsoft eine optimierte Authentifizierungserfahrung zu bieten, während diese beim Unternehmensnetzwerk angemeldet sind. Damit einmaliges Anmelden eingerichtet werden kann, müssen Organisationen einen Sicherheitstokendienst lokal bereitstellen. Sobald einmaliges Anmelden eingerichtet wurde, können Benutzer ihre Active Directory-Unternehmensanmeldeinformationen \(Benutzername und Kennwort\) verwenden, um auf Dienste in der Cloud und ihre vorhandenen lokalen Ressourcen zuzugreifen.
Benutzer-ID | Eine Benutzer-ID ist ein eindeutiger Bezeichner, den ein Benutzer auf der Seite "Anmelden" für den Zugriff auf die Clouddienste von Microsoft angibt, die Ihre Organisation abonniert hat.
Geschäfts- oder Schulkonto | Ein Benutzerkonto, das von einer Organisation \(Unternehmen, Schule, gemeinnützige Organisation\) einem ihrer Mitglieder \(Mitarbeiter, Schüler, Kunde\) zugewiesen wird und Anmeldezugriff auf mindestens eines der Microsoft Cloud Service-Abonnements der Organisation \(z. B. Office 365 oder Azure\) bereitstellt. Diese Konten werden im Azure AD-Verzeichnis einer Organisation gespeichert und in der Regel gelöscht, wenn der Benutzer die Organisation verlässt. Geschäfts- oder Schulkonten unterscheiden sich von Microsoft-Konten insofern, als sie von Administratoren in der Organisation \(nicht vom Benutzer\) erstellt und verwaltet werden.

## Nächste Schritte
- [Als Organisation für Azure registrieren](sign-up-organization.md)
- [Verknüpfung von Azure-Abonnements mit Azure AD](active-directory-how-subscriptions-associated-directory.md)
- [Dienst- und andere Einschränkungen für Azure Active Directory](active-directory-service-limits-restrictions.md)

<!---HONumber=July15_HO5-->