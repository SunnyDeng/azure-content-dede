<properties
	pageTitle="Übersicht über das Durchführen von Vergleichstests für Azure SQL-Datenbank"
	description="Dieses Thema beschreibt den Azure SQL Database Benchmark-Vergleichstest zum Messen der Leistung von Azure SQL-Datenbank."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/15/2015"
	ms.author="jroth" />

# Übersicht über das Durchführen von Vergleichstests für Azure SQL-Datenbank

## Übersicht
Microsoft Azure SQL-Datenbank bietet drei [Tarife](sql-database-service-tiers.md) mit mehreren Leistungsstufen. Jede Leistungsstufe bietet einen zunehmenden Umfang an Ressourcen bzw. Leistungsfähigkeit, um einen zunehmend höheren Durchsatz zu bieten.

Wichtig ist die Möglichkeit der Quantifizierung, wie sich die zunehmende Leistungsfähigkeit der einzelnen Stufen in wachsender Datenbankleistung ausdrückt. Hierfür hat Microsoft den Vergleichstest Azure SQL Database Benchmark (ASDB) entwickelt. Für den Vergleichstest wird eine Kombination grundlegender Vorgänge verwendet, die in allen OLTP-Workloads zu finden sind. Wir messen den Durchsatz, der für Datenbanken auf den einzelnen Leistungsstufen erzielt wird.

Die Ressourcen und Leistungsfähigkeit jedes Tarifs und jeder Leistungsstufe werden als [Datenbanktransaktionseinheiten (Database Transaction Units, DTUs)](sql-database-technical-overview.md#understand-dtus) ausgedrückt. DTUs bieten eine Möglichkeit der Beschreibung der relativen Kapazität einer Leistungsstufe anhand eines kombinierten Maßes von CPU-, Arbeitsspeicher- und Lese-/Schreibraten, die von jeder Leistungsstufe geboten werden. Eine Verdoppelung der DTU-Rate einer Datenbank entspricht der Verdoppelung der Datenbankleistung. Der Vergleichstest erlaubt uns das Bewerten der Auswirkung der zunehmenden Leistungsfähigkeit auf die Datenbankleistung. Hierfür werden tatsächliche Datenbankvorgänge ausgeführt, während die Datenbankgröße, Benutzeranzahl und Transaktionsraten proportional zu den von der Datenbank bereitgestellten Ressourcen skaliert werden.

Indem der Durchsatz des Tarifs "Basic" als Transaktionen pro Stunde, des Tarifs "Standard" als Transaktionen pro Minute und des Tarifs "Premium" als Transaktionen pro Sekunde ausgedrückt wird, kann das Leistungspotenzial jedes Tarifs einfach in Bezug zu den Anforderungen einer Anwendung gesetzt werden.

## Korrelieren von Vergleichstestergebnissen mit realer Datenbankleistung
Wichtig ist der Hinweis, dass ASDB wie alle Vergleichstests nur repräsentativen und informativen Charakter hat. Die mit der Vergleichstestanwendung erzielten Transaktionsraten entsprechen nicht denjenigen, die mit anderen Anwendungen erzielt werden. Der Vergleichstest umfasst eine Zusammenstellung verschiedener Typen von Transaktionen, die auf ein Schema angewendet werden, das einen Bereich von Tabellen und Datentypen enthält. Während für den Vergleichstest dieselben grundlegenden Vorgänge ausgeführt werden, die für alle OLTP-Workloads üblich sind, wird keine spezifische Klasse von Datenbank oder Anwendung abgebildet. Ziel des Vergleichstests ist das Bereitstellen eines vernünftigen Richtwerts für die relative Leistung einer Datenbank, der bei einer Skalierung nach oben und unten zwischen Leistungsstufen erwartet werden kann. In der Praxis haben Datenbanken eine unterschiedliche Größe und Komplexität, müssen verschiedene Kombinationen von Workloads bewältigen und reagieren auf unterschiedliche Weise. Beispielsweise kann eine E/A-intensive Anwendung E/A-Schwellenwerte früher erreichen, oder eine CPU-intensive Anwendung kann CPU-Grenzwerte früher erreichen. Es gibt keine Garantie, dass eine bestimmte Datenbank bei steigender Last auf die gleiche Weise wie im Vergleichstest skaliert wird.

Der Vergleichstest und seine Methoden werden nachstehend ausführlich beschrieben.

## Übersicht über den Vergleichstest
ASDB misst die Leistung einer Kombination grundlegender Datenbankvorgänge, die bei OLTP-Workloads (Online Transaction Processing, Onlinetransaktionsverarbeitung) am häufigsten vorkommen. Obwohl der Vergleichstest speziell für das Cloud Computing konzipiert ist, wurden das Datenbankschema, der Datenbestand und Transaktionen so gestaltet, dass sie für die grundlegenden Elemente der am häufigsten vorkommenden OLTP-Workloads repräsentativ sind.

## Schema
Das Schema zeichnet sich durch ausreichende Vielfalt und Komplexität aus, um eine Vielzahl von Vorgängen zu unterstützen. Der Vergleichstest wird auf eine Datenbank angewendet, die aus sechs Tabellen besteht. Die Tabellen gehören zu drei Kategorien: feste Größe, Skalierung und anwachsend. Es gibt zwei Tabellen mit fester Größe, drei Skalierungstabellen und eine anwachsende Tabelle. Die Tabellen mit fester Größe haben eine konstante Anzahl von Zeilen. Die Skalierungstabellen haben eine Kardinalität, die proportional zur Datenbankleistung ist, sich jedoch während des Vergleichstests nicht ändert. Die anwachsende Tabelle hat anfänglich dieselbe Größe wie eine Skalierungstabelle, doch ihre Kardinalität ändert sich im Verlauf des Vergleichstests, da Zeilen eingefügt und gelöscht werden.

Das Schema enthält eine Kombination von Datentypen, wie z. B. ganze Zahl, numerisch, Zeichen und Datum/Uhrzeit. Das Schema enthält primäre und sekundäre Schlüssel, aber keine Fremdschlüssel, was bedeutet, dass es keine Einschränkungen der referenziellen Integrität zwischen Tabellen gibt.

Ein Datengenerierungsprogramm erzeugt die Daten für die ursprüngliche Datenbank. Ganzzahl- und numerische Daten werden anhand verschiedener Strategien generiert. Mitunter werden Werte zufällig über einen Bereich verteilt. In anderen Fällen wird eine Gruppe von Werten nach dem Zufallsprinzip permutiert, um sicherzustellen, dass eine bestimmte Verteilung beibehalten wird. Textfelder werden anhand einer gewichteten Liste von Wörtern generiert, um realistisch wirkende Daten zu erstellen.

Die Datenbankgröße basiert auf einem "Skalierungsfaktor". Der Skalierungsfaktor (abgekürzt SF) bestimmt die Kardinalität der Skalierungs- und anwachsenden Tabellen. Wie im folgenden Abschnitt "Benutzer und Geschwindigkeit" beschrieben, werden die Datenbankgröße, Anzahl der Benutzer und maximale Leistung allesamt proportional zueinander skaliert.

## Transaktionen
Der Workload besteht aus neun Transaktionstypen, wie in der folgenden Tabelle dargestellt. Jede Transaktion ist darauf ausgelegt, eine bestimmte Gruppe von Systemmerkmalen im Datenbankmodul und in der Systemhardware in starker Abgrenzung zu den anderen Transaktionen hervorzuheben. Dieser Ansatz erleichtert das Bewerten der Auswirkung verschiedener Komponenten auf die Gesamtleistung. Beispielsweise erzeugt die Transaktion "Leseintensiv" eine hohe Anzahl von Lesevorgängen auf dem Datenträger.

| Transaktionstyp | Beschreibung |
|---|---|
| Nicht leseintensiv | SELECT, speicherintern, schreibgeschützt |
| Mittel leseintensiv | SELECT, zumeist speicherintern, schreibgeschützt |
| Leseintensiv | SELECT, zumeist nicht speicherintern, schreibgeschützt |
| Nicht aktualisierungsintensiv | UPDATE; speicherintern, lesen/schreiben |
| Aktualisierungsintensiv | UPDATE, zumeist nicht speicherintern, lesen/schreiben |
| Nicht einfügungsintensiv | INSERT, speicherintern; lesen/schreiben |
| Einfügungsintensiv | INSERT, zumeist nicht speicherintern, lesen/schreiben |
| Löschen | DELETE, Kombination aus speicherintern und nicht speicherintern, lesen/schreiben |
| CPU-intensiv | SELECT, speicherintern, relativ hohe CPU-Last, schreibgeschützt |

## Kombination von Workloads
Transaktionen werden zufällig aus einer gewichteten Verteilung in der folgenden Kombination ausgewählt. Die Kombination weist ein Verhältnis von Lese- zu Schreibvorgängen von ca. 2:1 auf.

| Transaktionstyp | % der Kombination |
|---|---|
| Nicht leseintensiv | 35 |
| Mittel leseintensiv | 20 |
| Leseintensiv | 5 |
| Nicht aktualisierungsintensiv | 20 |
| Aktualisierungsintensiv | 3 |
| Nicht einfügungsintensiv | 3 |
| Einfügungsintensiv | 2 |
| Löschen | 2 |
| CPU-intensiv | 10 |

## Benutzer und Geschwindigkeit
Der Workload des Vergleichstests stammt von einem Tool, das Transaktionen über verschiedene Verbindungen übermittelt, um das Verhalten vieler gleichzeitiger Benutzer zu simulieren. Obwohl alle Verbindungen und Transaktionen computergeneriert sind, bezeichnen wir diese Verbindungen der Einfachheit halber als "Benutzer". Obwohl jeder Benutzer unabhängig von allen anderen Benutzern operiert, führen alle Benutzer denselben Zyklus der unten aufgeführten Schritte aus:

1. Herstellen einer Datenbankverbindung.
2. Wiederholen bis zur Aufforderung zum Beenden:
	- Auswählen einer Transaktion nach dem Zufallsprinzip (aus gewichteter Verteilung)
	- Ausführen der ausgewählten Transaktion und Messen der Antwortzeit
	- Warten auf eine Geschwindigkeitsverzögerung
3. Schließen der Datenbankverbindung
4. Beenden

Die Geschwindigkeitsverzögerung (in Schritt 2c) wird nach dem Zufallsprinzip ausgewählt, hat aber eine Verteilung mit durchschnittlich 1,0 Sekunden. Daher kann jeder Benutzer durchschnittlich höchstens eine Transaktion pro Sekunde generieren.

## Skalierungsregeln
Die Anzahl der Benutzer wird von der Größe der Datenbank (in Skalierungsfaktoreinheiten) bestimmt. Für alle fünf Skalierungsfaktoreinheiten gibt es einen Benutzer. Aufgrund der Geschwindigkeitsverzögerung kann ein Benutzer durchschnittlich höchstens eine Transaktion pro Sekunde generieren.

Beim Skalierungsfaktor 500 (SF=500) hat die Datenbank 100 Benutzer und kann eine maximale Rate von 100 Transaktionen pro Sekunde (TPS) erreichen. Zum Erreichen einer höheren TPS-Rate sind mehr Benutzer und eine größere Datenbank erforderlich.

Die folgenden Tabelle enthält die tatsächliche Anzahl von Benutzern für alle Tarife und Leistungsstufen.

| Tarif (Leistungsstufe) | Benutzer | Datenbankgröße |
|---|---|---|
| Basic | 5 | 720 MB |
| Standard (S0) | 10 | 1 GB |
| Standard (S1) | 20 | 2,1 GB |
| Standard (S2) | 50 | 7,1 GB |
| Premium (P1) | 100 | 14 GB |
| Premium (P2) | 200 | 28 GB |
| Premium (P6/P3) | 800 | 114 GB |

## Messdauer
Ein gültiger Vergleichstestlauf erfordert eine stabile Messdauer von mindestens einer Stunde.

## Metriken
Die Hauptmetriken im Vergleichstest sind Durchsatz und Antwortzeit.

- Der Durchsatz ist die wesentliche Messgröße im Vergleichstest. Der Durchsatz wird als Transaktionen pro Zeiteinheit gemeldet, wobei alle Transaktionstypen gezählt werden.
- Die Antwortzeit ist ein Maß für die Vorhersagbarkeit von Leistung. Die Einschränkung der Antwortzeit variiert je nach Dienstklasse, wobei höhere Dienstklassen strengere Anforderungen an die Antwortzeit haben (siehe unten).

| Dienstklasse | Durchsatzmaß | Anforderung an die Antwortzeit |
|---|---|---|
| Premium | Transaktionen pro Sekunde | 95\. Perzentil bei 0,5 Sekunden |
| Standard | Transaktionen pro Minute | 90\. Perzentil bei 1,0 Sekunden |
| Basic | Transaktionen pro Stunde | 80\. Perzentil bei 2,0 Sekunden |

## Zusammenfassung
Der Azure SQL-Datenbankvergleichstest misst die relative Leistung von Azure SQL-Datenbank bei Ausführung im Bereich der verfügbaren Tarife und Leistungsstufen. Für den Vergleichstest wird eine Kombination grundlegender Datenbankvorgänge ausgeführt, die bei OLTP-Workloads (Online Transaction Processing, Onlinetransaktionsverarbeitung) am häufigsten vorkommen. Durch Messen der tatsächlichen Leistung bietet der Vergleichstest eine aussagekräftigere Bewertung der Auswirkungen auf den Durchsatz durch Ändern der Leistungsstufe als dies möglich ist, indem bloß die Ressourcen aufgelistet werden, die jede Stufe zu bieten hat, wie z. B. CPU-Geschwindigkeit, Arbeitsspeichergröße und IOPS (E/A-Vorgänge pro Sekunde). Wir werden den Vergleichstest künftig weiterentwickeln, um seinen Umfang und die bereitgestellten Daten zu vergrößern.

## Ressourcen
[Einführung in SQL-Datenbank](sql-database-technical-overview.md)

[Tarife und Leistungsebenen](sql-database-service-tiers.md)

[Leitfaden zur Leistung für Einzeldatenbanken](sql-database-performance-guidance.md)

<!---HONumber=Oct15_HO3-->