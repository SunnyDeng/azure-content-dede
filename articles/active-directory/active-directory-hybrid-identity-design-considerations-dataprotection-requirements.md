<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Ermitteln der Anforderungen in Bezug auf den Schutz von Daten | Microsoft Azure"
	description="Beim Planen der Hybrid-Identitätslösung müssen Sie die Anforderungen für den Schutz der Daten ermitteln, die für Ihr Unternehmen gelten. Außerdem müssen Sie wissen, welche Optionen verfügbar sind, um diese Anforderungen am besten zu erfüllen."
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

#Planen einer Erweiterung der Datensicherheit mit einer starken Identitätslösung

Der erste Schritt zum Schützen der Daten besteht in der Identifizierung, wer auf die Daten zugreifen kann. Im Rahmen dieses Prozesses benötigen Sie eine Identitätslösung, die in Ihr System integriert werden kann und mit der Funktionen für die Authentifizierung und Autorisierung bereitgestellt werden können. Authentifizierung und Autorisierung werden häufig miteinander verwechselt, und ihre jeweilige Rolle wird falsch verstanden. In Wirklichkeit besteht ein deutlicher Unterschied, wie in der folgenden Abbildung dargestellt:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Lebenszyklusphasen der Mobilgerätverwaltung**

Beim Planen Ihrer Hybrid-Identitätslösung müssen Sie die Anforderungen für den Schutz der Daten kennen, die für Ihr Unternehmen gelten. Außerdem müssen Sie wissen, welche Optionen verfügbar sind, um diese Anforderungen am besten zu erfüllen.
 
>[AZURE.NOTE]Nach Abschluss der Planung für den Schutz der Daten sollten Sie sich die Informationen unter [Ermitteln der Anforderungen für die Multi-Factor Authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) durchlesen. So können Sie sicherstellen, dass Ihre Auswahl in Bezug auf Multi-Factor Authentication-Anforderungen nicht negativ von den Entscheidungen beeinflusst wird, die Sie in diesem Abschnitt getroffen haben.

## Bestimmen der Datenschutzanforderungen
Im Zeitalter der Mobilität haben die meisten Unternehmen das gleiche Ziel: Benutzer sollen mit ihren mobilen Geräten lokal oder per Remoteverbindung an jedem Ort produktiv arbeiten können, um die Produktivität zu steigern. Dies ist zwar ein häufiges Ziel, aber Unternehmen mit dieser Anforderung sorgen sich auch um die zahlreichen Bedrohungen, denen begegnet werden muss, um die Daten des Unternehmens zu schützen und den Datenschutz für Benutzer zu wahren. Die jeweiligen Anforderungen unterscheiden sich hierbei von Unternehmen zu Unternehmen. Unterschiedliche Compliance-Regeln, die häufig von der Branche des Unternehmens abhängen, führen zu unterschiedlichen Entwurfsentscheidungen.

Es gibt aber einige Sicherheitsaspekte, die unabhängig von der Branche untersucht und überprüft werden sollten. Dies wird im nächsten Abschnitt erläutert.

## Wege zum Datenschutz

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Wege zum Datenschutz**

Im obigen Diagramm wird zuerst die Identitätskomponente überprüft, bevor auf die Daten zugegriffen wird. Diese Daten können sich im Zeitraum des Zugriffs in verschiedenen Zuständen befinden. Jede Zahl in diesem Diagramm steht für einen Pfad, unter dem sich Daten zu bestimmten Zeitpunkten befinden können. Diese Zahlen werden im Folgenden erläutert:

1. Schutz von Daten auf Geräteebene
2. Schutz von Daten während der Übertragung
3. Schutz von Daten im lokalen Ruhezustand
4. Schutz von Daten im Ruhezustand in der Cloud

Die technischen Kontrollen, mit denen das IT-Personal die eigentlichen Daten in diesen einzelnen Phasen schützen kann, werden nicht direkt von der Hybrid-Identitätslösung bereitgestellt. Es ist aber erforderlich, dass die Hybrid-Identitätslösung die Ressourcen für die Identitätsverwaltung (sowohl lokal als auch in der Cloud) zum Identifizieren von Benutzern verwenden kann, bevor der Zugriff auf die Daten gewährt wird. Stellen Sie beim Planen Ihrer Hybrid-Identitätslösung sicher, dass die folgenden Fragen gemäß den Anforderungen Ihres Unternehmens beantwortet werden:

## Schutz von Daten im Ruhezustand
Unabhängig davon, an welchem Ort sich die Daten im Ruhezustand befinden (Gerät, Cloud oder lokal), ist die Durchführung einer Bewertung wichtig, um die jeweiligen Unternehmensanforderungen zu verstehen. Stellen Sie für diesen Bereich sicher, dass die folgenden Fragen gestellt werden:

- Müssen in Ihrem Unternehmen ruhende Daten geschützt werden?
 - Wenn ja: Kann die Hybrid-Identitätslösung in Ihre derzeitige lokale Infrastruktur integriert werden?
 - Wenn ja: Kann die Hybrid-Identitätslösung in die Workloads der Cloud integriert werden?
- Ist die Cloudidentitätsverwaltung in der Lage, die Anmeldeinformationen der Benutzer und andere Daten zu schützen, die in der Cloud gespeichert sind?

## Schutz von Daten während der Übertragung
Daten, die zwischen dem Gerät und dem Rechenzentrum oder dem Gerät und der Cloud übertragen werden, müssen geschützt werden. Mit der Übertragung ist aber nicht unbedingt immer ein Kommunikationsprozess mit einer Komponente außerhalb Ihres Clouddiensts gemeint. Es geht auch um interne Übertragungen, z. B. zwischen zwei virtuellen Netzwerken. Stellen Sie für diesen Bereich sicher, dass die folgenden Fragen gestellt werden:

- Müssen Daten während der Übertragung in Ihrem Unternehmen geschützt werden?
 - Wenn ja: Kann die Hybrid-Identitätslösung in sichere Kontrollmechanismen integriert werden, z. B. SSL/TLS?
- Sorgt die Cloud-Identitätsverwaltung dafür, dass der Datenverkehr an den Verzeichnisspeicher bzw. im Verzeichnisspeicher (innerhalb und zwischen Rechenzentren) immer signiert ist?


## Compliance
Die Regulierungen, Gesetze und Compliance-Anforderungen variieren je nach Branche des Unternehmens. Unternehmen aus Branchen mit starker Regulierung müssen sich um die Aspekte der Identitätsverwaltung kümmern, die mit Compliance-Problemen verbunden sind. Vorschriften wie Sarbanes-Oxley (SOX), Health Insurance Portability and Accountability Act (HIPAA), Gramm-Leach-Bliley Act (GLBA) und der Payment Card Industry Data Security Standard (PCI DSS) sind sehr streng, was die Identität und den Zugriff betrifft. Die Hybrid-Identitätslösung, die von Ihrem Unternehmen eingeführt wird, muss über die Kernfunktionen verfügen, mit denen eine oder mehrere dieser Vorschriften erfüllt werden. Stellen Sie für diesen Bereich sicher, dass die folgenden Fragen gestellt werden:

- Ist die Hybrid-Identitätslösung mit den gesetzlichen Vorschriften für Ihr Unternehmen kompatibel?
- Verfügt die Hybrid-Identitätslösung über integrierte Funktionen, die Ihr Unternehmen in die Lage versetzen, die gesetzlichen Vorschriften zu erfüllen? 
 
>[AZURE.NOTE]Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. Unter [Definieren der Strategie zum Schutz von Daten](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) sind die verfügbaren Optionen und die jeweiligen Vor- und Nachteile beschrieben. Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.

## Nächste Schritte
 [Bestimmen der Content Management-Anforderungen](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## Siehe auch
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-directory-overview.md)

<!---HONumber=Nov15_HO3-->