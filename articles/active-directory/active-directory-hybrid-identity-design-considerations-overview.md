<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Übersicht | Microsoft Azure"
	description="Übersicht und Inhalt des Leitfadens mit Überlegungen zum Entwurf der Hybrid-Identität"
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
	ms.date="11/24/2015"
	ms.author="yuridio"/>

# Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität

In der Unternehmenswelt werden immer mehr Verbrauchergeräte genutzt, und cloudbasierte Software-as-a-Service-Anwendungen (SaaS) sind leicht einsetzbar. Daher stellt es eine große Herausforderung dar, über interne Rechenzentren und Cloudplattformen hinweg die Kontrolle über den Anwendungszugriff durch Benutzer zu behalten. Die Identitätslösungen von Microsoft decken sowohl lokale als auch cloudbasierte Funktionen ab, und es wird eine einzelne Benutzeridentität für die standortunabhängige Authentifizierung und Autorisierung gegenüber allen Ressourcen erstellt. Wir bezeichnen dies als Hybrid-Identität. Es gibt bei der Verwendung von Microsoft-Lösungen unterschiedliche Entwurfs- und Konfigurationsoptionen für die Hybrid-Identität, und in einigen Fällen kann es schwierig zu ermitteln sein, welche Kombination die Anforderungen Ihres Unternehmens am besten erfüllt. In diesem Leitfaden zu den „Überlegungen zum Entwurf der Hybrid-Identität“ erfahren Sie, wie Sie eine Hybrid-Identitätslösung entwerfen, mit der die geschäftlichen und technologischen Anforderungen für Ihr Unternehmen am besten erfüllt werden. In diesem Leitfaden wird ausführlich auf einige Schritte und Aufgaben eingegangen, an die Sie sich beim Entwerfen einer Hybrid-Identitätslösung halten können, die die besonderen Anforderungen Ihres Unternehmens erfüllt. Im Verlauf der Schritte und Aufgaben werden im Leitfaden die relevanten Technologien und Featureoptionen vorgestellt, die Unternehmen nutzen können, um die Anforderungen in den Bereichen Funktions- und Dienstqualitätsebene (z. B. Verfügbarkeit, Skalierbarkeit, Leistung, Verwaltbarkeit und Sicherheit) zu erfüllen. Im Leitfaden zu den „Überlegungen zum Entwurf der Hybrid-Identität“ sollen vor allem die folgenden Fragen beantwortet werden:

- Welche Fragen muss ich stellen und beantworten, um ein auf die Hybrid-Identität ausgelegtes Design für eine Technologie oder einen Problembereich zu erhalten, mit dem meine Anforderungen am besten erfüllt werden?
- Welche Abfolge von Aktivitäten sollte ich einhalten, um eine Hybrid-Identitätslösung für die Technologie oder den Problembereich zu entwerfen? 
- Welche Technologie- und Konfigurationsoptionen für die Hybrid-Identität sind verfügbar, um meine Anforderungen zu erfüllen? Über welche Nachteile, die sich bei Verwendung dieser Optionen ergeben können, muss ich informiert sein, damit ich die beste Option für mein Unternehmen wählen kann?


## An wen richtet sich dieser Leitfaden?
 CIO, CITO, Chief Identity Architects, Enterprise Architects und IT Architects, die für das Entwerfen einer Hybrid-Identitätslösung für mittlere und große Unternehmen verantwortlich sind.

## Wie kann dieser Leitfaden als Hilfe dienen? 
Dank dieses Leitfadens können Sie verstehen lernen, wie Sie eine Hybrid-Identitätslösung entwerfen, mit der ein cloudbasiertes System für die Identitätsverwaltung in Ihre aktuelle lokale Identitätslösung integriert werden kann. Die folgende Grafik enthält ein Beispiel für eine Hybrid-Identitätslösung, mit der IT-Administratoren ihre aktuelle lokale Windows Server Active Directory-Lösung in Microsoft Azure Active Directory integrieren können. So kann es Benutzern ermöglicht werden, das einmalige Anmelden (Single Sign-On, SSO) über lokale und in der Cloud befindliche Anwendungen hinweg zu nutzen.

![](./media/hybrid-id-design-considerations/hybridID-example.png)


Die obige Abbildung ist ein Beispiel für eine Hybrid-Identitätslösung, bei der Clouddienste in lokale Funktionen integriert werden. So soll Endbenutzern für den Authentifizierungsvorgang eine einheitliche Oberfläche bereitgestellt und der IT-Abteilung die Verwaltung dieser Ressourcen erleichtert werden. Obwohl dies ein sehr häufiges Szenario sein kann, unterscheidet sich der Entwurf der Hybrid-Identitätslösung eines Unternehmens aufgrund von anderen Anforderungen wahrscheinlich vom Beispiel in Abbildung 1. Dieser Leitfaden enthält einige Schritte und Aufgaben, an die Sie sich beim Entwerfen einer Hybrid-Identitätslösung halten können, die die besonderen Anforderungen Ihres Unternehmens erfüllt. Bei allen folgenden Schritten und Aufgaben werden im Leitfaden die relevanten Technologien und Featureoptionen vorgestellt, mit denen es Ihnen ermöglicht wird, die Anforderungen der Funktions- und Dienstqualitätsebenen für Ihr Unternehmen zu erfüllen.

**Annahmen**: Sie verfügen bereits über etwas Erfahrung mit Windows Server, Active Directory-Domänendiensten und Azure Active Directory. In diesem Dokument wird davon ausgegangen, dass Sie erfahren möchten, wie Sie Ihre geschäftlichen Anforderungen mit diesen Lösungen allein oder im Rahmen einer integrierten Lösung erfüllen können.

## Überlegungen zum Entwurf – Übersicht
Dieses Dokument enthält eine Reihe von Schritten und Aufgaben, an die Sie sich halten können, um eine Hybrid-Identitätslösung zu entwerfen, die Ihre Anforderungen am besten erfüllt. Die Schritte sind in einer bestimmten Reihenfolge angegeben. Für Entwurfsüberlegungen, die in späteren Schritten enthalten sind, kann es aber erforderlich sein, die in früheren Schritten getroffenen Entscheidungen zu ändern. Der Grund hierfür ist, dass bestimmte Entwurfsentscheidungen ggf. zu Konflikten führen. Im gesamten Dokument wird versucht, Sie auf potenzielle Entwurfskonflikte hinzuweisen.

Sie können den Entwurf, der Ihre Anforderungen am besten erfüllt, erst ermitteln, nachdem Sie die Schritte so oft wie nötig durchlaufen haben, um alle Überlegungen des Dokuments einzubeziehen.

| Hybrid-Identitätsphase | Themenliste |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ermitteln der Identitätsanforderungen | [Ermitteln der Geschäftsanforderungen](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Ermitteln der Anforderungen an die Verzeichnissynchronisierung](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Ermitteln der Anforderungen für die Multi-Factor Authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definieren einer Strategie zur Einführung der Hybrid-Identität](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planen einer Erweiterung der Datensicherheit mit einer starken Identitätslösung | [Bestimmen der Datenschutzanforderungen](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Bestimmen der Content Management-Anforderungen](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Bestimmen der Zugriffssteuerungsanforderungen](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Bestimmen der Anforderungen an Reaktionen auf Vorfälle](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definieren der Strategie zum Schutz von Daten](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planen des Hybrid-Identitätslebenszyklus | [Ermitteln der Aufgaben für die Hybrid-Identitätsverwaltung](active-directory-hybrid-identity-design-considerations-hybridId-management-tasks.md) <br> [Synchronisierungsverwaltung](active-directory-hybrid-identity-design-considerations-hybridId-management-tasks.md)<br> [Ermitteln der Strategie für die Einführung der Hybrid-Identitätsverwaltung](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##Dieses Handbuch herunterladen
Sie können eine PDF-Version des Azure Hybrid Identity Design Considerations Guide in englischer Sprache in der [Technet Gallery](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288) herunterladen.

                                                             

<!---HONumber=AcomDC_1125_2015-->