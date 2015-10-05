<properties
   pageTitle="Temporäre Tabellen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Verwendung von temporären Tabellen in Azure SQL Data Warehouse zum Entwickeln von Lösungen."
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
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Temporäre Tabellen in SQL Data Warehouse
Temporäre Tabellen befinden sich in SQL Data Warehouse auf Sitzungsebene. Sie sind als lokale temporäre Tabellen definiert, und im Gegensatz zu SQL Server-Tabellen kann von überall innerhalb der Sitzung auf sie zugegriffen werden.

Temporäre Tabellen sind sehr nützlich bei der Verarbeitung von Daten – vor allem bei Transformationen, bei denen die Zwischenergebnisse vorübergehend sind.

In diesem Artikel werden einige spezifische Methoden für die Nutzung von temporären Tabellen hervorgehoben, mit denen der Code modularisiert werden kann.

## Modularisieren von Code

Die Tatsache, dass temporäre Tabellen in einer Benutzersitzung an einer beliebigen Stelle angezeigt werden können, kann zur Modularisierung des Anwendungscodes genutzt werden.

Im Folgenden erstellen wir ein Beispiel.

Die folgende gespeicherte Prozedur generiert die DDL-Anweisungen, die zum Aktualisieren der Statistiken für jede Spalte in der Datenbank erforderlich sind:

```
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
,   @sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION = HASH([seq_nmbr])
        ,   LOCATION     = USER_DB
        )
WITH t1
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
```

Beachten Sie, dass mit diesen Daten bisher nichts vorgenommen wurde. Die Prozedur hat einfach die DDL-Anweisungen generiert und sie in einer temporären Tabelle gespeichert.

Es ist jedoch in SQL Data Warehouse möglich, diese temporäre Tabelle außerhalb der Prozedur zu verwenden, die sie erstellt hat. Dies unterscheidet sich bei SQL Server. Tatsächlich kann die temporäre Tabelle **überall** innerhalb der Sitzung verwendet werden.

Dies kann zu modularerem und besser verwaltbarem Code führen.

```
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

In einigen Fällen können Inline-Funktionen und Funktionen mit mehreren Anweisungen auch mithilfe dieses Verfahrens ersetzt werden.

> [AZURE.NOTE]Sie können diese Lösung auch erweitern. Wenn Sie beispielsweise nur eine einzelne Tabelle aktualisieren möchten, müssen Sie nur die #stats\_ddl-Tabelle filtern

## Einschränkungen der temporären Tabelle
SQL Data Warehouse weist eine Reihe von Einschränkungen bei der Implementierung von temporären Tabellen auf.

Die wichtigsten Einschränkungen sind:

- Globale temporäre Tabellen werden nicht unterstützt.
- Sichten können nicht in temporären Tabellen erstellt werden.


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Sept15_HO4-->