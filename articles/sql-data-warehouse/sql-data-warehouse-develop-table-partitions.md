<properties
   pageTitle="Tabellenpartitionen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Verwendung von Tabellenpartitionen in Azure SQL Data Warehouse zum Entwickeln von Lösungen."
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
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Tabellenpartitionen in SQL Data Warehouse

So migrieren Sie SQL Server-Partitionsdefinitionen nach SQL Data Warehouse

- Entfernen Sie SQL Server-Partitionsfunktionen und-Schemas, da dies beim Erstellen der Tabelle durchgeführt wird.
- Definieren Sie die Partitionen beim Erstellen der Tabelle. Legen Sie einfach die Partitionsgrenzpunkte fest, und geben Sie an, ob der Grenzpunkt für `RANGE RIGHT` oder `RANGE LEFT` gelten soll.

### Festlegen der Partitionsgröße
Bei SQL DW können DBAs zwischen verschiedenen Tabellentypen wählen: Heap, gruppierter Index (Clustered Index, CI) oder gruppierter ColumnStore-Index (Clustered ColumnStore Index, CCI). Bei jedem dieser Tabellentypen kann der DBA die Tabelle partitionieren (also zur Verbesserung der Leistung in mehrere Abschnitte unterteilen). In Tabellen mit zu vielen Partitionen kann es jedoch unter Umständen zu Leistungseinbußen oder Abfragefehlern kommen. Dies gilt insbesondere bei CCI-Tabellen. Der DBA muss wissen, wann sich der Einsatz der Partitionierung anbietet und wie viele Partitionen erstellt werden sollten, damit die Partitionierung hilfreich ist. Auf der Grundlage der vorliegenden Richtlinien können DBAs optimale Entscheidungen für ihre Szenarien treffen.

Tabellenpartitionen sind in der Regel doppelt hilfreich:

1. Bei Verwendung von Partitionswechseln zum schnellen Abtrennen eines Tabellenbereichs. Für eine Faktentabelle wird häufig ein Design verwendet, das nur Zeilen für einen bestimmten, begrenzten Zeitraum enthält. So kann beispielsweise eine Umsatzfaktentabelle erstellt werden, die nur Daten für die letzten 36 Monate enthält. Am Monatsende wird jeweils der älteste Verkaufsdatenmonat aus der Tabelle gelöscht. Dies kann zwar durch einfaches Löschen aller Zeilen für den ältesten Monat erreicht werden, das zeilenweise Löschen umfangreicher Datenmengen kann jedoch sehr zeitaufwendig sein. Zur Optimierung dieses Szenarios unterstützt SQL DW Partitionswechsel. Dadurch kann ein gesamter Zeilensatz einer Partition in einem schnellen Einzelvorgang gelöscht werden.   

2. Durch die Partitionierung kann bei Abfragen problemlos ein großer Zeilensatz (also eine Partition) von der Verarbeitung ausgenommen werden, wenn die Abfrage ein Prädikat auf die Partitionsspalte anwendet. Ein Beispiel: Wenn eine Umsatzfaktentabelle auf der Grundlage des Verkaufsdatumsfelds in 36 Monate partitioniert wird, kann bei Abfragen, die das Verkaufsdatum als Filter verwenden, die Verarbeitung von Partitionen übersprungen werden, die nicht dem Filter entsprechen. Bei einer auf diese Weise genutzten Partitionierung handelt es sich um einen undifferenzierten Index.

Wenn DBAs gruppierte ColumnStore-Indizes in SQL DW erstellen, müssen sie einen weiteren Faktor berücksichtigen: die Zeilenanzahl. Mit CCI-Tabellen lassen sich eine höhere Komprimierung sowie eine bessere SQL DW-Abfrageleistung erzielen. Aufgrund der internen Funktionsweise der Komprimierung in SQL DW muss jede Partition in einer CCI-Tabelle eine relativ hohe Anzahl von Zeilen besitzen, damit die Daten komprimiert werden. Darüber hinaus werden die Daten von SQL DW breit gestreut, und jede Verteilung wird wiederum durch Partitionen unterteilt. Für eine optimale Komprimierung und Leistung sind pro Verteilung und Partition mindestens 100.000 Zeilen erforderlich. Für das obige Beispiel bedeutet das: Wenn die Umsatzfaktentabelle 36 Monatspartitionen enthält und SQL DW 60 Verteilungen umfasst, muss die Umsatzfaktentabelle mindestens sechs Millionen Zeilen pro Monat umfassen (oder 216 Millionen Zeilen, wenn alle Monate aufgefüllt sind). Enthält eine Tabelle deutlich weniger Zeilen als mindestens empfohlen, sollte der Datenbankadministrator die Tabelle ggf. mit weniger Partitionen erstellen, um die Zeilenanzahl pro Verteilung zu erhöhen.


Verwenden Sie eine Abfrage wie unten, um die Größe der aktuellen Datenbank auf Partitionsebene zu ändern:

```
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]        = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

Die Gesamtanzahl der Verteilungen entspricht der Anzahl der beim Erstellen einer Tabelle verwendeten Speicherorte. Das ist eine komplexe Art zu formulieren, dass jede Tabelle einmal pro Verteilung erstellt wird.

Außerdem können Sie ungefähr die Anzahl der Zeilen abschätzen und somit auch die Größe der einzelnen Partitionen. Die Partition im Quell-Data Warehouse wird in die einzelnen Verteilungen unterteilt.

Verwenden Sie die folgende Berechnung zum Bestimmen der Partitionsgröße:

MPP-Partitionsgröße = SMP-Partitionsgröße/Anzahl von Verteilungen

Mit der folgenden Abfrage können Sie feststellen, über wie viele Verteilungen Ihre SQL Data Warehouse-Datenbank verfügt:

```
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

Sie wissen jetzt, wie groß jede Partition im Quellsystem ist und welche Größe Sie für SQLDW planen können, sodass Sie die Partitionsgrenze festlegen können.

### Workloadverwaltung
Eine letzte Information, die Sie der Tabellenpartitionsentscheidung berücksichtigen müssen, ist die Workloadverwaltung. In SQL Data Warehouse wird die maximale Speichermenge für die einzelnen Verteilungspunkte während der Abfrageausführung durch dieses Feature geregelt. Im folgenden Artikel finden Sie weitere Informationen zur [Workloadverwaltung]. Im Idealfall wird die Partitionsgröße hinsichtlich speicherinterner Vorgänge angepasst, wie z. B Columnstore-Indexneuerstellungen. Die Neuerstellung eines Index ist ein speicherintensiver Vorgang. Aus diesem Grund sollten Sie sicherstellen, dass für die Neuerstellung des Partitionsindex genügend Arbeitsspeicher zur Verfügung steht. Sie können die Menge an Arbeitsspeicher für die Abfrage erhöhen, indem Sie von der Standardrolle zu einer der anderen verfügbaren Rollen wechseln.

Informationen über die Zuordnung von Arbeitsspeicher pro Verteilung erhalten Sie durch Abfragen der dynamischen Resource Governor-Verwaltungsansichten. In der Praxis ist die Arbeitsspeicherzuweisung kleiner als in den folgenden Abbildungen. Die Abbildung bietet jedoch eine Richtlinie, die Sie für die Größe der Partitionen für Verwaltungsvorgänge verwenden können.

```
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

> [AZURE.NOTE]Versuchen Sie, zu vermeiden, Ihre Partitionen größer als die Arbeitsspeicherzuweisung zu erstellen, die durch die größte Ressourcenklasse bereitgestellt wird. Wenn die Partitionen die Größen in dieser Abbildung übersteigen, besteht das Risiko der Speicherauslastung, was wiederum zu weniger optimaler Komprimierung führt.

## Partitionswechsel
Für den Wechsel zweier Partitionen zwischen zwei Tabellen müssen Sie sicherstellen, dass die Partitionen an ihren jeweiligen Grenzen ausgerichtet sind und die Tabellendefinitionen übereinstimmen. Da keine Überprüfungseinschränkungen verfügbar sind, um den Bereich der Werte in einer Tabelle zu erzwingen, muss die Quelltabelle die gleichen Partitionsgrenzen enthalten wie die Zieltabelle. Ist dies nicht der Fall, schlägt der Partitionswechsel fehl, da die Partitionsmetadaten nicht synchronisiert werden.

### Aufteilen einer Partition mit Daten
Die effizienteste Methode, um eine Partition zu teilen, die bereits Daten enthält, ist die Verwendung einer `CTAS`-Anweisung. Wenn es sich bei der partitionierten Tabelle um einen gruppierten Columnstore handelt, muss die Tabellenpartition leer sein, bevor sie aufgeteilt werden kann.

Im Folgenden finden Sie ein Beispiel für eine partitionierte Columnstore-Tabelle, die eine Zeile in jeder Partition enthält:

```
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE]Durch das Erstellen des Statistikobjekts stellen wir sicher, dass die Tabellenmetadaten genauer ist. Wenn wir das Erstellen von Statistiken auslassen, verwendet SQL Data Warehouse Standardwerte. Ausführliche Informationen zu Statistiken finden Sie unter [Statistiken][].

Anschließend können wir mit der `sys.partitions`-Katalogsicht die Zeilenanzahl abfragen:

```
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Wenn wir versuchen, diese Tabelle aufzuteilen, erhalten wir einen Fehler:

```
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Level 15, State 1, Line 44 SPLIT clause of ALTER PARTITION statement failed because the partition is not empty. Nur leere Partitionen können aufgeteilt werden, wenn ein Columnstore-Index für die Tabelle vorhanden ist. Deaktivieren Sie ggf. den Columnstore-Index vor Ausgabe der ALTER PARTITION-Anweisung, und erstellen Sie dann nach Abschluss von ALTER PARTITION den Columnstore-Index neu.

Wir können jedoch auch `CTAS` zum Erstellen einer neuen Tabelle zum Speichern von Daten verwenden.

```
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Ein Wechsel ist zulässig, da die Partitionsgrenzen ausgerichtet sind. Dadurch verbleibt die Quelltabelle mit einer leeren Partition, die wir später aufteilen können.

```
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Wir müssen nun lediglich die Daten mit `CTAS` an die neuen Partitionsgrenzen anpassen und wieder in die Haupttabelle zurückführen.

```
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Nach Abschluss der Datenverschiebung empfiehlt es sich, die Statistiken für die Zieltabelle zu aktualisieren, um sicherzustellen, dass sie genau die neue Verteilung der Daten in den entsprechenden Partitionen wiedergeben:

```
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### Datenquellen-Steuerelement für die Tabellenpartitionierung
Um ein **Rosten** der Tabellendefinition in Ihrem Quellcodeverwaltungssystem zu vermeiden, sollten Sie die folgende Vorgehensweise befolgen:

1. Erstellen der Tabelle als partitionierte Tabelle ohne Partitionswerte

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. Führen Sie einen `SPLIT` für die Tabelle als Teil des Bereitstellungsprozesses durch:

```
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Bei diesem Ansatz bleibt der Code in der Quellcodeverwaltung statisch und die Partitionierungsgrenzwerte können sich mit der Zeit dynamisch mit dem Warehouse entwickeln.

>[AZURE.NOTE]Der Partitionswechsel unterscheidet sich im Vergleich zu SQL Server. Weitere Informationen zu diesem Thema finden Sie unter [Migrieren Ihres Codes][].


## Nächste Schritte
Nachdem Sie Ihr Datenbankschema erfolgreich in SQL Data Warehouse migriert haben, können Sie mit den folgenden Artikeln fortfahren:

- [Migrieren Ihrer Daten][]
- [Migrieren Ihres Codes][]

<!--Image references-->

<!-- Article references -->
[Migrieren Ihres Codes]: sql-data-warehouse-migrate-code.md
[Migrieren Ihrer Daten]: sql-data-warehouse-migrate-data.md
[Statistiken]: sql-data-warehouse-develop-statistics.md
[Workloadverwaltung]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=AcomDC_0107_2016-->