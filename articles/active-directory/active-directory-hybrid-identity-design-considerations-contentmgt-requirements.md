<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Ermitteln der Anforderungen in Bezug auf das Content Management | Microsoft Azure"
	description="Sie erhalten einen Einblick, wie Sie die Content Management-Anforderungen für Ihr Unternehmen ermitteln. Wenn Benutzer ein eigenes Gerät verwenden, verfügen sie meist auch über unterschiedliche Anmeldeinformationen, die je nach genutzter Anwendung eingesetzt werden. Es ist wichtig zu unterscheiden, welche Inhalte mit persönlichen Anmeldeinformationen und welche Inhalte mit den Anmeldeinformationen des Unternehmens erstellt wurden. Ihre Identitätslösung sollte in der Lage sein, mit Clouddiensten zu interagieren, um für Endbenutzer eine nahtlose Erfahrung zu schaffen, und dabei den Datenschutz sicherzustellen und den Schutz vor Datenlecks zu erhöhen."
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
	ms.date="02/12/2016"
	ms.author="yuridio"/>

# Ermitteln der Content Management-Anforderungen für Ihre Hybrid-Identitätslösung

Wenn Sie die Content Management-Anforderungen Ihres Unternehmens genau kennen, kann sich dies direkt auf die Entscheidung auswirken, welche Hybrid-Identitätslösung verwendet werden soll. Aufgrund der Nutzung mehrerer Geräte und der Mitnahme eigener Geräte durch Benutzer ([BYOD](http://aka.ms/byodcg)) muss das Unternehmen seine Daten schützen und gleichzeitig dafür sorgen, dass der Datenschutz für Benutzer gewahrt bleibt. Wenn Benutzer ein eigenes Gerät verwenden, verfügen sie meist auch über unterschiedliche Anmeldeinformationen, die je nach genutzter Anwendung eingesetzt werden. Es ist wichtig zu unterscheiden, welche Inhalte mit persönlichen Anmeldeinformationen und welche Inhalte mit den Anmeldeinformationen des Unternehmens erstellt wurden. Ihre Identitätslösung sollte in der Lage sein, mit Clouddiensten zu interagieren, um für Endbenutzer eine nahtlose Erfahrung zu schaffen, und dabei den Datenschutz sicherzustellen und den Schutz vor Datenlecks zu erhöhen.

Ihre Identitätslösung wird von unterschiedlichen technischen Steuerungen genutzt, um Content Management bereitzustellen. Dies ist in der folgenden Abbildung dargestellt:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Sicherheitskontrollen, für die Ihr Identitätsverwaltungssystem verwendet wird**

Im Allgemeinen wird Ihr Identitätsverwaltungssystem in den folgenden Bereichen für Content Management-Anforderungen genutzt:

- Datenschutz: Identifizieren des Benutzers, dem eine Ressource gehört, und Anwenden der richtigen Kontrollen, um die Integrität zu wahren.
- Klassifizierung von Daten: Identifizieren des Benutzers bzw. der Gruppe und der Zugriffsebene für ein Objekt nach seiner Klassifizierung. 
- Schutz vor Datenlecks: Sicherheitskontrollen, die als Schutz vor Datenlecks dienen, müssen mit dem Identitätssystem interagieren, um die Identität des Benutzers zu überprüfen. Dies ist auch für Überwachungspfadzwecke wichtig.

>[AZURE.NOTE]
Weitere Informationen zu den bewährten Methoden und Richtlinien für die Datenklassifizierung finden Sie unter [Datenklassifizierung für Cloud Readiness](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf).

Stellen Sie beim Planen Ihrer Hybrid-Identitätslösung sicher, dass die folgenden Fragen gemäß den Anforderungen Ihres Unternehmens beantwortet werden:

- Verfügt Ihr Unternehmen über Sicherheitskontrollen zur Durchsetzung des Datenschutzes?
 - Wenn ja: Können diese Sicherheitskontrollen in die Hybrid-Identitätslösung integriert werden, die Sie einführen möchten?
- Wird in Ihrem Unternehmen die Datenklassifizierung verwendet?
 - Wenn ja: Kann die aktuelle Lösung in die Hybrid-Identitätslösung integriert werden, die Sie einführen möchten?
- Verfügt Ihr Unternehmen derzeit über eine Lösung als Schutz vor Datenlecks? 
 - Wenn ja: Kann die aktuelle Lösung in die Hybrid-Identitätslösung integriert werden, die Sie einführen möchten?
- Muss Ihr Unternehmen den Zugriff auf Ressourcen überwachen?
 - Wenn ja: Für welche Art von Ressourcen gilt dies?
 - Wenn ja: Welche Informationsebene ist hierbei erforderlich?
 - Wenn ja: Wo muss sich das Überwachungsprotokoll befinden? Lokal oder in der Cloud?
- Muss Ihr Unternehmen E-Mails verschlüsseln, die vertrauliche Daten enthalten (Sozialversicherungsnummern, Kreditkartennummern usw.)?
- Muss Ihr Unternehmen alle Dokumente/Inhalte verschlüsseln, die mit externen Geschäftspartnern ausgetauscht werden?
- Muss Ihr Unternehmen für bestimmte Arten von E-Mails (Nicht beantworten, Nicht weiterleiten) Unternehmensrichtlinien durchsetzen?
 
>[AZURE.NOTE]
Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. Unter [Definieren der Strategie zum Schutz von Daten](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) sind die verfügbaren Optionen und die jeweiligen Vor- und Nachteile beschrieben. Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.


## Nächste Schritte
[Bestimmen der Zugriffssteuerungsanforderungen](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0218_2016-->