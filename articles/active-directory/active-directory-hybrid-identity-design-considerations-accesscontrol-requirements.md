
<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Ermitteln der Anforderungen in Bezug auf die Zugriffssteuerung | Microsoft Azure"
	description="Es werden die Säulen der Identität erläutert, und es wird die Ermittlung der Zugriffsanforderungen für Ressourcen für Benutzer in einer Hybrid-Umgebung beschrieben."
	documentationCenter=""
	services="active-directory"
	authors="yuridio"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="yuridio"/>

# Ermitteln der Zugriffssteuerungsanforderungen für Ihre Hybrid-Identitätslösung
Wenn eine Organisation ihre Hybrid-Identitätslösung entwirft, kann sie diese Gelegenheit auch zum Überprüfen von Zugriffsanforderungen für die Ressourcen nutzen, für die eine Bereitstellung für die Benutzer geplant ist. Der Datenzugriff deckt alle vier Säulen der Identität ab, also:

- Verwaltung
- Authentifizierung
- Autorisierung
- Überwachung

Im folgenden Abschnitt wird näher auf die Authentifizierung und Autorisierung eingegangen. Die Verwaltung und die Überwachung sind Teil des Hybrid-Identitätslebenszyklus. Weitere Informationen zu diesen Funktionen finden Sie unter [Ermitteln der Aufgaben für die Hybrid-Identitätsverwaltung](active-directory-hybrid-identity-design-considerations-hybridId-management-tasks.md).

>[AZURE.NOTE]Weitere Informationen zu den einzelnen Säulen finden Sie unter [Die vier Säulen der Identität – Identitätsverwaltung im Zeitalter von Hybrid-IT](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) (in englischer Sprache).

## Authentifizierung und Autorisierung
Es gibt verschiedene Szenarien für die Authentifizierung und Autorisierung. Diese Szenarien haben spezielle Anforderungen, die mit der Hybrid-Identitätslösung erfüllt werden müssen, für deren Einsatz sich das Unternehmen entscheidet. Szenarien mit Business to Business (B2B)-Kommunikation können für IT-Administratoren eine zusätzliche Herausforderung darstellen. Es muss sichergestellt werden, dass für die vom Unternehmen verwendete Authentifizierungs- und Autorisierungsmethode die Kommunikation mit den Geschäftspartnern möglich ist. Stellen Sie während des Entwurfsprozesses für die Authentifizierungs- und Autorisierungsanforderungen sicher, dass die folgenden Fragen beantwortet werden:

- Sollen vom Unternehmen nur Benutzer authentifiziert und autorisiert werden, die im eigenen System für die Identitätsverwaltung enthalten sind?
 - Gibt es Pläne für B2B-Szenarien?
 - Wenn ja: Ist bereits bekannt, welche Protokolle (SAML, OAuth, Kerberos, Token oder Zertifikate) verwendet werden, um beide Unternehmen zu verbinden?
- Werden diese Protokolle von der Hybrid-Identitätslösung unterstützt, die Sie einsetzen möchten?

Als weiterer wichtiger Punkt ist zu beachten, wo sich das Authentifizierungsrepository, das von Benutzern und Partnern verwendet wird, und das Verwaltungsmodell befinden. Erwägen Sie die beiden folgenden Hauptoptionen: - Zentralisiert: Bei diesem Modell können die Anmeldeinformationen, Richtlinien und die Verwaltung des Benutzers lokal oder in der Cloud zentralisiert werden. - Hybrid: Bei diesem Modell werden die Anmeldeinformationen, Richtlinien und die Verwaltung des Benutzers lokal zentralisiert und in die Cloud repliziert.

Das Modell, das von Ihrem Unternehmen gewählt wird, richtet sich jeweils nach den geschäftlichen Anforderungen. Sie sollten die folgenden Fragen beantworten, um zu identifizieren, wo sich das System für die Identitätsverwaltung befindet und welcher Verwaltungsmodus verwendet wird:

- Verfügt Ihr Unternehmen derzeit über eine lokale Identitätsverwaltung?
 - Wenn ja: Soll diese beibehalten werden?
 - Gibt es Regulierungs- oder Compliance-Anforderungen, die von Ihrem Unternehmen erfüllt werden müssen und von denen vorgeschrieben wird, wo sich das System für die Identitätsverwaltung befinden muss?
- Wird in Ihrem Unternehmen das einmalige Anmelden für lokale Apps oder Apps in der Cloud verwendet?
 - Wenn ja: Wirkt sich die Einführung eines Hybrid-Identitätsmodells auf diesen Prozess aus?

## Zugriffssteuerung
Die Authentifizierung und Autorisierung sind wichtige Elemente, um über die Überprüfung von Benutzern den Zugriff auf Unternehmensdaten zu ermöglichen. Ebenso wichtig ist aber auch die Steuerung der Zugriffsebene, die diese Benutzer haben, sowie der Zugriffsebene von Administratoren für die von ihnen verwalteten Ressourcen. Ihre Hybrid-Identitätslösung muss den präzisen Zugriff auf Ressourcen, Delegierung und die rollenbasierte Zugriffssteuerung bereitstellen können. Stellen Sie sicher, dass in Bezug auf die Zugriffssteuerung die folgenden Fragen beantwortet werden:

- Verfügt Ihr Unternehmen über mehr als einen Benutzer mit erhöhten Rechten für die Verwaltung Ihres Identitätssystems?
 - Wenn ja: Ist für jeden Benutzer die gleiche Zugriffsebene erforderlich?
- Muss Ihr Unternehmen den Zugriff für Benutzer zum Verwalten bestimmter Ressourcen delegieren?
 - Wenn ja: Wie häufig geschieht dies? 
- Muss Ihr Unternehmen Zugriffssteuerungsfunktionen zwischen lokalen Ressourcen und Cloudressourcen integrieren?
- Muss Ihr Unternehmen den Zugriff auf Ressourcen unter bestimmten Bedingungen einschränken?
- Verfügt Ihr Unternehmen über eine Anwendung, die benutzerdefinierten Steuerungszugriff auf einige Ressourcen benötigt?
 - Wenn ja: Wo befinden sich diese Apps (lokal oder in der Cloud)?
 - Wenn ja: Wo befinden sich diese Zielressourcen (lokal oder in der Cloud)?
 
>[AZURE.NOTE]Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. Unter [Definieren der Strategie zum Schutz von Daten](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) sind die verfügbaren Optionen und die jeweiligen Vor- und Nachteile beschrieben. Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.

## Nächste Schritte

[Bestimmen der Anforderungen an Reaktionen auf Vorfälle](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## Siehe auch
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-directory-overview.md)

<!---HONumber=Nov15_HO3-->