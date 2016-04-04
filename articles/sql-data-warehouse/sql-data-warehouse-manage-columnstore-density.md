<properties
   pageTitle="Verwalten von schiefen Tabellenverteilungen | Microsoft Azure"
   description="Leitfaden für Benutzer zum Ermitteln von Verteilungsschiefe in ihren verteilten Tabellen"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Verwalten von Columnstore-Indizes
Columnstore-Indizes sind das Rückgrat von Azure SQL Data Warehouse. Indem Sie die Indizes in einem optimalen Zustand erhalten, maximieren Sie die Leistung Ihres Systems.

In diesem Artikel wird erläutert, wie die Metadaten für den Columnstore-Index für Ihre Tabellen abgefragt werden. So können Sie Probleme diagnostizieren und schnell beheben.

## Abfragen von Columnstore-Metadaten
Eine Abfrage der Systemmetadaten ist erforderlich, um die Dichte des Columnstore-Index zu verstehen. Im Folgenden finden Sie ein Beispiel für die Art der Informationen, die Sie finden können.

```
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

Nach dem Erstellen der Ansicht können die Columnstore-Metadaten leicht analysiert werden. Eine Beispielabfrage ist unten dargestellt.

```
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## Verbessern der Columnstore-Dichte
Nachdem Sie die Abfrage ausgeführt haben, können Sie die Daten anzeigen und die Ergebnisse analysieren.

Sie sollten mehrere Punkte untersuchen:

| Spalte | Verwendung dieser Daten |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Wenn die Tabelle partitioniert ist, könnten Sie einen höheren Wert für die Anzahl der offenen Zeilengruppen erwarten. Jeder Partition in der Verteilung könnte theoretisch eine offene Zeilengruppe zugeordnet sein. Berücksichtigen Sie dies in der Analyse. Eine kleine Tabelle, die partitioniert wurde, könnte durch das vollständige Entfernen der Partitionierung optimiert werden, da dies die Komprimierung verbessern würde. |
| [row\_count\_total] | Die Gesamtanzahl der Zeilen für die Tabelle. Mit diesem Wert können Sie z. B. den Prozentwert der Zeilen im komprimierten Zustand berechnen. |
| [row\_count\_per\_distribution\_MAX] | Wenn alle Zeilen gleichmäßig verteilt sind, wäre dieser Wert die Zielanzahl von Zeilen pro Verteilung. Vergleichen Sie diesen Wert mit „compressed\_rowgroup\_count“. |
| [COMPRESSED\_rowgroup\_rows] | Gesamtanzahl der Zeilen im Columnstore-Format für die Tabelle. |
| [COMPRESSED\_rowgroup\_rows\_AVG] | Wenn für eine Zeilengruppe die durchschnittliche Anzahl von Zeilen erheblich kleiner als die maximale Anzahl von Zeilen ist, sollten Sie CTAS oder ALTER INDEX REBUILD zum erneuten Komprimieren der Daten verwenden. |
| [COMPRESSED\_rowgroup\_count] | Die Anzahl von Zeilengruppen im Columnstore-Format. Wenn diese Anzahl in Relation zur Tabelle sehr hoch ist, ist dies ein Indikator dafür, dass die Columnstore-Dichte gering ist. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | Zeilen werden im Columnstore-Format logisch gelöscht. Wenn die Anzahl in Relation zur Tabellengröße hoch ist, sollten Sie die Partition oder den Index neu erstellen, da sie dadurch physisch entfernt werden. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | Verwenden Sie diese Spalte zusammen mit den Spalten AVG und MAX, um den Wertebereich für die Zeilengruppen im Columnstore zu verstehen. Eine geringe Anzahl über dem Schwellenwert für das Laden (102.400 pro nach Verteilung ausgerichteter Partition) zeigt an, dass Optimierungen beim Laden von Daten möglich sind. |
| [COMPRESSED\_rowgroup\_rows\_MAX] | Wie oben. |
| [OPEN\_rowgroup\_count] | Offene Zeilengruppen sind normal. Sie sollten sinnvollerweise eine OPEN-Zeilengruppe pro Tabellenverteilung (60) erwarten. Eine sehr große Anzahl deutet auf das partitionsübergreifende Laden von Daten hin. Überprüfen Sie die Partitionierungsstrategie, um sicherzustellen, dass sie fehlerfrei ist. |
| [OPEN\_rowgroup\_rows] | Jede Zeilengruppe kann maximal 1.048.576 Zeilen enthalten. Verwenden Sie diesen Wert, um festzustellen, wie voll die offenen Zeilengruppen derzeit sind. |
| [OPEN\_rowgroup\_rows\_MIN] | Offene Gruppen geben an, dass Daten langsam in die Tabelle geladen werden oder dass beim vorherigen Laden verbleibende Zeilen in diese Zeilengruppe aufgenommen wurden. Verwenden die Spalten MIN, MAX und AVG, um festzustellen, wie viele Daten sich in OPEN-Zeilengruppen befinden. Bei kleinen Tabellen können es 100 % aller Daten sein! Verwenden Sie in diesem Fall ALTER INDEX REBUILD, um die Datenaufnahme in einen Columnstore zu erzwingen. |
| [OPEN\_rowgroup\_rows\_MAX] | Wie oben. |
| [OPEN\_rowgroup\_rows\_AVG] | Wie oben. |
| [CLOSED\_rowgroup\_rows] | Betrachten Sie die Zeilen in geschlossenen Zeilengruppen als Integritätsprüfung. |
| [CLOSED\_rowgroup\_count] | Die Anzahl der geschlossenen Zeilengruppen sollte niedrig sein, wenn überhaupt welche vorhanden werden. Geschlossene Zeilengruppen können mit dem Befehl ALTER INDEX ... REORGANISE in komprimierte Zeilengruppen konvertiert werden. Dies ist jedoch normalerweise nicht erforderlich. Geschlossene Gruppen werden automatisch durch den Tupelverschiebungsvorgang im Hintergrund in Columnstore-Zeilengruppen konvertiert. |
| [CLOSED\_rowgroup\_rows\_MIN] | Geschlossene Zeilengruppen sollten eine sehr hohe Füllrate aufweisen. Wenn die Füllrate für eine geschlossene Zeilengruppe niedrig ist, ist eine weitere Analyse des Columnstore erforderlich. |
| [CLOSED\_rowgroup\_rows\_MAX] | Wie oben. |
| [CLOSED\_rowgroup\_rows\_AVG] | Wie oben. |

## Indexverwaltung
Das Komprimieren von Zeilengruppen kann erreicht werden, indem Sie Partitionen mit [CTAS][] erstellen oder den Index selbst mit [ALTER INDEX][] neu erstellen. [CTAS][] ist im Allgemeinen schneller als [ALTER INDEX][] – insbesondere bei großen Tabellen oder Partitionen. Für kleinere Tabellen oder Partitionen ist jedoch [ALTER INDEX][] häufig viel praktischer.

>[AZURE.NOTE] ALTER INDEX...REBUILD ist ein Offlinevorgang. ALTER INDEX...REORGANISE ist ein Onlinevorgang. REORGANISE verarbeitet jedoch keine offenen Zeilengruppen. Um offene Zeilengruppen einzubinden, ist ALTER INDEX...REBUILD erforderlich.

Für das Neuerstellen von Indizes – vor allem bei größeren Tabellen – sind häufig zusätzliche Ressourcen nützlich. Azure SQL Data Warehouse bietet eine Funktion für die Workloadverwaltung, die zum Zuordnen von mehr Speicherplatz zu einem Benutzer verwendet werden kann. Informationen zum Reservieren von mehr Speicherplatz für die Neuerstellung des Index finden Sie im Abschnitt zur Workloadverwaltung im Artikel [Parallelität][].

## Nächste Schritte
Weitere Details zur Neuerstellung von Partitionen mit `CTAS` finden Sie in den folgenden Artikeln:

* [Tabellenpartitionierung][]
* [Parallelität][]

Detaillierte Hinweise zur Indexverwaltung finden Sie im Artikel [Verwalten von Indizes][].

Weitere Verwaltungstipps finden Sie in der Übersicht über die [Verwaltung][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Tabellenpartitionierung]: sql-data-warehouse-develop-table-partitions.md
[Parallelität]: sql-data-warehouse-develop-concurrency.md
[Verwaltung]: sql-data-warehouse-manage-monitor.md
[Verwalten von Indizes]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/de-DE/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->