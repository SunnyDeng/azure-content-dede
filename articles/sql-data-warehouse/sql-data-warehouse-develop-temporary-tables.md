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
   ms.date="03/03/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Temporäre Tabellen in SQL Data Warehouse
Temporäre Tabellen sind sehr nützlich bei der Verarbeitung von Daten – vor allem bei Transformationen, bei denen die Zwischenergebnisse vorübergehend sind. In SQL Data Warehouse befinden sich temporäre Tabellen auf Sitzungsebene. Sie sind jedoch als lokale temporäre Tabellen definiert, aber im Gegensatz zu SQL Server-Tabellen kann von überall innerhalb der Sitzung auf sie zugegriffen werden.

Dieser Artikel enthält einige wichtige Anleitungen zur Verwendung von temporären Tabellen. Zudem werden die Grundsätze von temporären Tabellen auf Sitzungsebene behandelt. Mithilfe dieser Informationen können Sie Ihren Code modularisieren. Codemodularität ist wichtig für eine einfache Wartung und die Wiederverwendung von Code.

## Erstellen von temporären Tabellen
Das Erstellen einer temporären Tabelle ist sehr unkompliziert. Sie müssen nur dem Tabellennamen das Zeichen # voranstellen, wie im folgenden Beispiel gezeigt:

```
CREATE TABLE #stats_ddl
(
	[schema_name]			NVARCHAR(128) NOT NULL
,	[table_name]            NVARCHAR(128) NOT NULL
,	[stats_name]            NVARCHAR(128) NOT NULL
,	[stats_is_filtered]     BIT           NOT NULL
,	[seq_nmbr]              BIGINT        NOT NULL
,	[two_part_name]         NVARCHAR(260) NOT NULL
,	[three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
```

Temporäre Tabellen können auch mit `CTAS` auf genau die gleiche Weise erstellt werden.

```
CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
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

>[AZURE.NOTE] `CTAS` ist ein sehr leistungsfähiger Befehl. Er bietet den zusätzlichen Vorteil, dass er bei der Verwendung des Speicherplatzes für das Transaktionsprotokoll sehr effizient ist.


## Löschen von temporären Tabellen

Damit Ihre `CREATE TABLE`-Anweisungen erfolgreich sind, müssen Sie sicherstellen, dass die Tabelle noch nicht in der Sitzung vorhanden ist. Dies kann mit einer einfachen Überprüfung auf das Vorhandensein nach dem folgenden Muster erledigt werden:

```
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END
```

> [AZURE.NOTE] Für Codekonsistenz wird empfohlen, dieses Muster für Tabellen und temporäre Tabellen zu verwenden.

Es ist auch eine gute Idee, mit `DROP TABLE` temporäre Tabellen zu entfernen, wenn Sie sie in Ihrem Code nicht mehr benötigen.

```
DROP TABLE #stats_ddl
```

Bei der Entwicklung gespeicherter Prozeduren ist es üblich, die Befehle zum Löschen am Ende einer Prozedur zu bündeln, um sicherzustellen, dass diese Objekte bereinigt werden.

## Modularisieren von Code

Die Tatsache, dass temporäre Tabellen in einer Benutzersitzung an einer beliebigen Stelle angezeigt werden können, kann zur Modularisierung des Anwendungscodes genutzt werden.

Im Folgenden erstellen wir ein Beispiel.

Mit der folgenden gespeicherten Prozedur werden die oben genannten Beispiele zusammengeführt. Mit dem Code können die DDL-Anweisungen generiert werden, die zum Aktualisieren der Statistiken für jede Spalte in der Datenbank erforderlich sind:

```
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
	,@sample_pct     tinyint
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

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
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
GO
```

An diesem Punkt wurde keine Aktion in der Tabelle ausgeführt. Die Prozedur hat einfach die DDL-Anweisungen generiert, die zum Aktualisieren der Statistiken erforderlich sind, und diesen Code in einer temporären Tabelle gespeichert.

Beachten Sie jedoch, dass die gespeicherte Prozedur am Ende keinen `DROP TABLE`-Befehl enthält. Wir haben jedoch eine Überprüfung auf das Vorhandensein in die gespeicherte Prozedur aufgenommen, um den Code robuster und wiederholbar zu machen. Dadurch wird sichergestellt, dass `CTAS` nicht fehlschlägt, weil ein doppeltes Objekt in der Sitzung vorhanden ist.

Und jetzt zum interessanten Teil!

In SQL Data Warehouse ist es möglich, diese temporäre Tabelle außerhalb der Prozedur zu verwenden, die sie erstellt hat. Dies unterscheidet sich bei SQL Server. Tatsächlich kann die temporäre Tabelle **überall** innerhalb der Sitzung verwendet werden.

Dies kann zu modularerem und besser verwaltbarem Code führen. Betrachten Sie das folgende Beispiel:

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

Der resultierende Code ist viel kompakter.

In einigen Fällen können Inlinefunktionen und Funktionen mit mehreren Anweisungen auch mithilfe dieses Verfahrens ersetzt werden.

> [AZURE.NOTE] Sie können diese Lösung auch erweitern. Wenn Sie beispielsweise nur eine einzelne Tabelle aktualisieren möchten, müssen Sie nur die #stats\_ddl-Tabelle filtern

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

<!---HONumber=AcomDC_0323_2016-->