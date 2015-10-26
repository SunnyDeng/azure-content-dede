<properties
	pageTitle="Vorschau von Azure Active Directory-Domänendiensten: Features | Microsoft Azure"
	description="Features von Azure Active Directory-Domänendiensten"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Azure AD-Domänendienste *(Vorschau)*

## Merkmale
Die folgenden Features sind in der Vorschauversion der Azure Active Directory-Domänendienste verfügbar.

- **Einfache Bereitstellung:** Sie können die Azure AD-Domänendienste für Ihren Azure AD-Mandanten mit nur wenigen Mausklicks aktivieren. Unabhängig davon, ob es sich bei Ihrem Azure AD-Mandanten um einen Cloudmandanten handelt oder ob er mit Ihrem lokalen Verzeichnis synchronisiert wird, kann Ihre verwaltete Domäne schnell bereitgestellt werden.

- **Unterstützung für den Domänenbeitritt:** In einem virtuellen Azure-Netzwerk, in dem Azure Active Directory-Domänendienste verfügbar sind, können Sie Computer ganz einfach in eine Domäne aufnehmen. Das Beitreten zur Domäne auf Windows-Client- und Server-Betriebssystemen funktioniert nahtlos mit Domänen, die von den Azure Active Directory-Domänendiensten bereitgestellt werden. Sie können bei diesen Domänen auch automatisierte Domänenbeitrittstools verwenden.

- **Eine Domäneninstanz pro Azure AD-Verzeichnis:** Sie können eine einzige Active Directory-Domäne für jedes Azure AD-Verzeichnis erstellen.

- **Domänen mit benutzerdefinierten Namen erstellen:** Erstellen Sie Domänen mit benutzerdefinierten Namen (z. B. contoso.local) mithilfe der Azure Active Directory-Domänendienste. Dies schließt sowohl überprüfte als auch nicht überprüfte Domänennamen ein. Optional können Sie eine Domäne mit dem integrierten Domänensuffix erstellen (d. h. *.onmicrosoft.com), die von Ihrem Azure AD-Verzeichnis bereitgestellt wird.

- **In Azure AD integriert:** Sie müssen die Replikation in Azure Active Directory-Domänendienste nicht konfigurieren oder verwalten. Benutzerkonten, Gruppenmitgliedschaften und Benutzeranmeldeinformationen (Kennwörter) aus dem Azure AD-Verzeichnis stehen in Azure Active Directory-Domänendiensten automatisch zur Verfügung. Neue Benutzer, Gruppen oder Änderungen von Attributen auf Ihrem Azure AD-Mandanten oder in Ihrem lokalen Verzeichnis werden mit Azure Active Directory-Domänendiensten automatisch synchronisiert.

- **NTLM- und Kerberos-Authentifizierung:** Durch die Unterstützung für die NTLM- und Kerberos-Authentifizierung können Sie Anwendungen bereitstellen, die auf der integrierten Windows-Authentifizierung beruhen.

- **Verwenden der Anmeldeinformationen/Kennwörter Ihres Unternehmens:** Kennwörter für Benutzer auf Ihrem Azure AD-Mandanten können für die Azure Active Directory-Domänendienste verwendet werden. Benutzer in Ihrer Organisation können daher ihre Unternehmensanmeldeinformationen in der Domäne einsetzen – zum Einbinden von Computern in die Domäne, für die interaktive Anmeldung bzw. die Anmeldung über Remotedesktop, für die Authentifizierung beim DC usw.

- **LDAP-Bindungs- und LDAP-Leseunterstützung:** Sie können auf LDAP-Bindungen beruhende Anwendungen verwenden, um Benutzer in Domänen zu authentifizieren, die von den Azure Active Directory-Domänendiensten bereitgestellt werden. Darüber hinaus können Anwendungen, die Benutzer-/Computerattribute anhand von LDAP-Lesevorgängen aus dem Verzeichnis abfragen, auch mit Azure Active Directory-Domänendiensten arbeiten.

- **Gruppenrichtlinie:** Sie können ein einzelnes integriertes GPO sowohl für die Benutzer- als auch für die Computercontainer nutzen, um Compliance mit den erforderlichen Sicherheitsrichtlinien für Benutzerkonten und in die Domäne eingebundene Computer durchzusetzen.

- **In mehreren Azure-Regionen verfügbar:** Auf der Seite [Unterstützte Azure-Regionen](active-directory-ds-regions.md) finden Sie eine Liste der Azure-Regionen, in denen Azure Active Directory-Domänendienste verfügbar sind.

- **Hohe Verfügbarkeit:** Azure AD-Domänendienste bieten eine hohe Verfügbarkeit für Ihre Domäne. Dadurch wird eine höhere Dienstverfügbarkeit und Stabilität bei Fehlern gewährleistet. Die integrierte Integritätsüberwachung bietet eine automatische Wiederherstellung nach Fehlern durch das Einrichten neuer Instanzen zum Ersetzen fehlerhafter Instanzen und zum Bereitstellen eines fortlaufenden Diensts für Ihre Domäne.

- **Verwendung vertrauter Verwaltungstools:** Sie können vertraute Windows Server Active Directory-Verwaltungstools wie das Active Directory-Verwaltungscenter oder Active Directory-PowerShell zur Verwaltung von Domänen einsetzen, die von Azure Active Directory-Domänendiensten bereitgestellt werden.

<!---HONumber=Oct15_HO3-->