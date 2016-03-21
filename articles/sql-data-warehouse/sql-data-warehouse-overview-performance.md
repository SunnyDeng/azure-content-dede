<properties
   pageTitle="Übersicht über Leistung und Skalierbarkeit | Microsoft Azure"
   description="Einführung in die Funktionen für Leistung und Skalierbarkeit von SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;barbkess;jrj;nicw;sonyama"/>

# Übersicht über Leistung und Skalierbarkeit
Durch das Verlagern des Data Warehouse in die Cloud müssen Sie nicht mehr mit Low-Level-Hardwareprobleme befassen. Vorbei sind die Zeiten, in denen Sie die Art der Prozessoren, die Menge des Arbeitsspeichers oder den Speichertyp ermitteln mussten, um eine hohe Leistung des Data Warehouse zu erreichen. Stattdessen stellt SQL Data Warehouse Ihnen diese Frage: Wie schnell möchten Sie Ihre Daten analysieren?

SQL Data Warehouse ist eine cloudbasierte, verteilte Datenbankplattform, die entworfen wurde, um eine hohe, skalierbare Leistung bereitzustellen, bei der Sie die vollständige Kontrolle über die Ressourcen haben, die zum Auflösen von Abfragen verwendet werden. Durch die Anpassung der Anzahl der Ihrem Data Warehouse zugeteilten Data Warehouse-Einheiten können Sie die Größe der Warehouse-Ressourcen innerhalb weniger Sekunden flexibel skalieren. Als verteilte, skalierbare Plattform ermöglicht SQL Data Warehouse Ihrem Data Warehouse die effiziente und effektive Verarbeitung erheblicher Datenmengen, während Sie die vollständige Kontrolle über die Kosten der Lösung haben.

*Die folgenden Informationen gelten für Azure SQL Data Warehouse in der Vorschauversion. Diese Informationen werden während der Vorschau fortlaufend aktualisiert, da der Dienst bis zur allgemeinen Verfügbarkeit (GA) erweitert wird.*

Unsere Ziele für SQL Data Warehouse:
-	Vorhersagbare Leistung und lineare Skalierbarkeit für Daten bis zum Petabytebereich
-	Hohe Zuverlässigkeit für alle Data Warehouse-Vorgänge, unterstützt durch SLA (Service Level Agreement)
-	Kurze Zeit vom Laden der Daten bis zu den Einblicken in relationale und nicht relationale Daten

Während der Vorschau werden wir kontinuierlich an diesen Zielen arbeiten, um sie vor der allgemeinen Verfügbarkeit (GA) umzusetzen.

>[AZURE.NOTE] In den folgenden Abschnitten wird der Azure SQL Data Warehouse-Dienst beim Start der öffentlichen Vorschau beschrieben. Diese Informationen werden während der Vorschau fortlaufend aktualisiert, da der Dienst bis zur allgemeinen Verfügbarkeit (GA) erweitert wird.

## Datenschutz
SQL Data Warehouse speichert alle Daten im Azure-Speicher mit georedundanten Blobs. Drei synchrone Kopien der Daten werden in der lokalen Azure-Region behalten, um transparenten Datenschutz bei lokalen Ausfällen (z. B. Speicherlaufwerksausfälle) sicherzustellen. Darüber hinaus werden drei weitere asynchrone Kopien in einer Azure-Remoteregion zum Schutz von Daten bei einem regionalen Ausfall (Notfallwiederherstellung) beibehalten. Lokale und Remoteregionen werden zusammen verwendet, um akzeptable Synchronisierungslatenzen (z. B. Osten der USA und Westen der USA) zu erreichen.

## Datenbankwiederherstellung
SQL Data Warehouse sichert sämtliche Daten alle 4 Stunden mit Azure-Speichermomentaufnahmen. Diese Momentaufnahmen werden sieben Tage lang kostenlos aufbewahrt. Dadurch können Daten an bis zu 42 Punkten innerhalb der letzten 7 Tage bis zu dem Zeitpunkt wiederhergestellt werden, an dem letzte Momentaufnahme erstellt wurde. Bei der Vorschau wird auch die Möglichkeit, verschiedene Werte für die Beibehaltungsdauer anzugeben, eingeführt. Daten können aus einer Momentaufnahme mithilfe von PowerShell oder REST-APIs wiederhergestellt werden. Momentaufnahmen werden asynchron in eine Azure-Remoteregion kopiert, um eine noch bessere Wiederherstellbarkeit im Falle eines regionalen Ausfalls (Notfallwiederherstellung) zu erreichen.

## Zuverlässigkeit
SQL Data Warehouse ist ein verteiltes System mit mehreren Komponenten, bei dem die Anzahl der Komponenten zusammen mit der Anzahl der Data Warehouse-Einheiten (DWUs) zunimmt. SQL Data Warehouse erkennt und verringert automatisch die Compute-Fehler, jedoch kann ein Vorgang (z. B. das Laden von Daten oder eine Abfrage) aufgrund von individuellen Compute-Fehlern fehlschlagen. Während der Vorschau nehmen wir fortlaufende Verbesserungen an der Zuverlässigkeit der Abfragen vor, damit die meisten Vorgänge auch mit Fehlern abgeschlossen werden können.

Basierend auf gesammelten Telemetriedaten wird die Zuverlässigkeit von SQL Data Warehouse auf 98 % für typische Data Warehouse-Workloads geschätzt. Dies bedeutet, dass im Durchschnitt, 2 von 100 Abfragen aufgrund von Systemfehlern fehlschlagen können. Dies ist keine SLA für die Vorschau, sondern eher ein Indikator für die erwartete Zuverlässigkeit von ausgeführten Abfragen. Beachten Sie, dass die Wahrscheinlichkeit einer fehlerhaften Abfrage mit der Ausführungszeit steigt (eine Abfrage, die länger als 2 Stunden dauert, schlägt z. B. mit einer viel höheren Wahrscheinlichkeit fehl als eine Abfrage, die weniger als 10 Minuten dauert). Während der Vorschau nehmen wir fortlaufende Verbesserungen vor, um die gleiche Zuverlässigkeit für Vorgänge unabhängig von deren Ausführungszeit zu gewährleisten. Wenn wir diese Erweiterungen mit dem Ziel der Bereitstellung einer vollständigen SLA bei der allgemeinen Verfügbarkeit veröffentlichen, werden wir die erwartete Zuverlässigkeit aktualisieren.

Während der Vorschau wird SQL Data Warehouse in regelmäßigen Abständen aktualisiert werden, um neue Funktionen und wichtige Updates zu installieren. Diese Upgrades können störend sein, und derzeit werden Upgrades nicht in einem vorhersehbaren Zeitplan durchgeführt. Wenn Sie feststellen, dass dieser Prozess zu störend ist, wir empfehlen Ihnen, [ein Supportticket zu erstellen][], damit wir Ihnen helfen können, diesen Prozess zu umgehen.

## Leistung und Skalierbarkeit
SQL Data Warehouse führt Data Warehouse-Einheiten (DWUs) als Abstraktion von Datenverarbeitungsressourcen (CPUs, Arbeitsspeicher, Speicher-E/A) ein, die auf mehreren Knoten aggregiert sind. Durch eine Erhöhung der Anzahl der DWUs werden auch die aggregierten Datenverarbeitungsressourcen einer SQL Data Warehouse-Instanz erhöht. SQL Data Warehouse verteilt Vorgänge (z. B. das Laden von Daten oder Abfragen) auf die gesamte Compute-Infrastruktur in der Instanz, um die Leistung von Ladevorgängen und Abfragen zu erhöhen oder zu verringern, wenn das System hoch- oder herunterskaliert wird.

Für jedes Data Warehouse gibt es zwei grundlegende Messwerte für die Leistung:
- **Laderate**: Die Anzahl der Datensätze, die pro Sekunde in das Data Warehouse geladen werden können. Wir messen speziell die Anzahl der Datensätze, die über PolyBase aus dem Azure-Blob-Speicher in eine Tabelle mit einem gruppierten Spaltenspeicherindex importiert werden können.
- **Scanrate**: Die Anzahl der Datensätze, die pro Sekunde sequenziell aus dem Data Warehouse abgerufen werden können. Mit dieser Metrik wird speziell die Anzahl der Datensätze gemessen, die von einer Abfrage aus einer Tabelle mit einem gruppierten Spaltenspeicherindex zurückgegeben werden.

Während der Vorschau nehmen wir fortlaufende Verbesserungen vor, um diese Raten zu erhöhen und sicherzustellen, dass sie planbar skalieren.

## Zentrale Leistungskonzepte

Informationen zu zusätzlichen zentralen Leistung- und Skalierbarkeitskonzepten finden Sie in den folgenden Artikeln:

- [Leistung und Skalierbarkeit][]
- [Parallelitätsmodell][]
- [Entwerfen von Tabellen][]
- [Auswählen eines Hashverteilungsschlüssels für die Tabelle][]
- [Statistiken zum Verbessern der Leistung][]

## Nächste Schritte
Anleitungen zum Erstellen Ihrer SQL Data Warehouse-Lösung finden Sie im Artikel [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->

[Leistung und Skalierbarkeit]: sql-data-warehouse-performance-scale.md
[Parallelitätsmodell]: sql-data-warehouse-develop-concurrency.md
[Entwerfen von Tabellen]: sql-data-warehouse-develop-table-design.md
[Auswählen eines Hashverteilungsschlüssels für die Tabelle]: sql-data-warehouse-develop-hash-distribution-key.md
[Statistiken zum Verbessern der Leistung]: sql-data-warehouse-develop-statistics.md
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md
[ein Supportticket zu erstellen]: sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0309_2016-->