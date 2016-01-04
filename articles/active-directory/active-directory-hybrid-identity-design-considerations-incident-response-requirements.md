
<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrid-Identität – Bestimmen der Anforderungen an Reaktionen auf Vorfälle | Microsoft Azure-Anforderungen"
	description="Ermitteln Sie für die Hybrid-Identitätslösung die Funktionen für Überwachung und Meldung, die von der IT-Abteilung genutzt werden können, um Maßnahmen zur Identifizierung und Minimierung einer potenziellen Bedrohung zu ergreifen."
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
	ms.date="11/06/2015"
	ms.author="yuridio"/>

# Bestimmen der Anforderungen an Reaktionen auf Vorfälle für Ihre Hybrid-Identitätslösung

Große und mittelgroße Unternehmen verfügen meist über einen Plan für [Reaktionen auf Vorfälle](https://technet.microsoft.com/library/cc700825.aspx), damit das IT-Personal je nach Schweregrad des Vorfalls die passenden Maßnahmen ergreifen kann. Das System für die Identitätsverwaltung ist eine wichtige Komponente des Prozesses für Reaktionen auf Vorfälle, weil damit identifiziert werden kann, wer eine bestimmte Aktion für das Ziel durchgeführt hat. Die Hybrid-Identitätslösung muss Funktionen für Überwachung und Berichterstellung bereitstellen können, die von der IT-Abteilung genutzt werden können, um Maßnahmen zur Identifizierung und Minimierung einer potenziellen Bedrohung zu ergreifen. Ein typischer Plan für Reaktionen auf Vorfälle enthält die folgenden Phasen:

1.	Anfängliche Bewertung
2.	Kommunikation von Vorfällen
3.	Schadensbegrenzung und Risikominderung
4.	Identifizierung der betroffenen Bereiche und des Schweregrads
5.	Sicherung der Beweise
6.	Benachrichtigung der entsprechenden Parteien
7.	Wiederherstellung des Systems
8.	Dokumentation
9.	Bewertung des Schadens und der Kosten
10.	Überprüfung des Prozesses und des Plans

Während der Identifizierung der betroffenen Bereiche und des Schweregrads müssen die Systeme ermittelt werden, die betroffen sind, sowie die Dateien, auf die zugegriffen wurde. Außerdem muss der Vertraulichkeitsgrad dieser Dateien ermittelt werden. Ihr Hybrid-Identitätssystem sollte in der Lage sein, diese Anforderungen zu erfüllen, damit Sie den Benutzer identifizieren können, der diese Änderungen vorgenommen hat.

## Überwachung und Berichterstellung
Sehr oft kann das Identitätssystem auch während der anfänglichen Bewertung als Hilfe dienen, sofern es über integrierte Funktionen für die Überwachung und Berichterstellung verfügt. Während der anfänglichen Bewertung muss von IT-Administratoren eine verdächtige Aktivität identifiziert werden können, oder das System sollte dazu in der Lage sein, dies basierend auf einer vorkonfigurierten Aufgabe automatisch auszulösen. Viele Aktivitäten können auf einen möglichen Angriff hindeuten. Es kann bei einem schlecht konfigurierten System aber auch vorkommen, dass in einem Angriffserkennungssystem falsch positive Ergebnisse angezeigt werden.

Das System für die Identitätsverwaltung sollte IT-Administratoren bei der Identifizierung und Meldung dieser verdächtigen Aktivitäten unterstützen. Normalerweise können diese technischen Anforderungen erfüllt werden, indem alle Systeme überwacht werden und über eine Meldefunktion verfügen, mit der auf potenzielle Bedrohungen hingewiesen werden kann. Verwenden Sie die unten angegebenen Fragen als Unterstützung beim Entwerfen Ihrer Hybrid-Identitätslösung, und berücksichtigen Sie die Anforderungen an die Reaktion auf Vorfälle:

- Ist in Ihrem Unternehmen ein Sicherheitsplan für Reaktionen auf Vorfälle vorhanden?
 - Wenn ja: Wird das aktuelle System für die Identitätsverwaltung als Teil des Prozesses verwendet?
- Muss Ihr Unternehmen verdächtige Anmeldeversuche von Benutzern auf unterschiedlichen Geräten identifizieren können?
- Muss Ihr Unternehmen potenziell gekaperte Anmeldeinformationen von Benutzern erkennen können?
- Muss Ihr Unternehmen den Zugriff und die Aktionen von Benutzern überprüfen können?
- Muss Ihr Unternehmen wissen, wann ein Benutzer sein Kennwort zurücksetzt?

## Durchsetzung von Richtlinien

Während der Phase der Schadensbegrenzung und Risikominderung ist es wichtig, die tatsächlichen und potenziellen Auswirkungen eines Angriffs zu reduzieren. Die Maßnahme, die Sie an diesem Punkt ergreifen, kann den Unterschied zwischen einer kleinen und einer großen Auswirkung ausmachen. Die genaue Reaktion hängt von Ihrem Unternehmen und der Art des jeweiligen Angriffs ab. Wenn die anfängliche Bewertung ergeben hat, dass ein Konto übernommen wurde, müssen Sie die Richtlinie durchsetzen, mit der dieses Konto gesperrt wird. Dies ist nur ein Beispiel für die Verwendung des System für die Identitätsverwaltung. Verwenden Sie die folgenden Fragen als Unterstützung beim Entwerfen der Hybrid-Identitätslösung, und berücksichtigen Sie, wie Richtlinien durchgesetzt werden, um auf einen aktuellen Vorfall zu reagieren:

- Verfügt Ihr Unternehmen über Richtlinien, mit denen für Benutzer der Zugriff auf das Netzwerk bei Bedarf blockiert werden kann?
 - Wenn ja: Kann die aktuelle Lösung in das System für die Hybrid-Identitätsverwaltung integriert werden, das Sie einführen möchten?
- Muss Ihr Unternehmen den bedingten Zugriff für Benutzer durchsetzen, die sich in Quarantäne befinden? 
 
>[AZURE.NOTE]Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. Unter [Definieren der Strategie zum Schutz von Daten](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) sind die verfügbaren Optionen und die jeweiligen Vor- und Nachteile beschrieben. Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.

## Nächste Schritte
[Definieren der Strategie zum Schutz von Daten](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=Nov15_HO4-->