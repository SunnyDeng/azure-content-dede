<properties
   pageTitle="Optimieren von Transaktionen für SQL Data Warehouse | Microsoft Azure"
   description="Bewährte Methoden zum Schreiben von effizienten Transaktionsupdates in Azure SQL Data Warehouse"
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
   ms.date="02/27/2016"
   ms.author="jrj;barbkess"/>

# Optimieren von Transaktionen für SQL Data Warehouse

In diesem Artikel wird erläutert, wie Sie Transaktionscode so schreiben, dass Änderungen möglichst effizient vorgenommen werden.

## Grundlagen von Transaktionen und Protokollierung

Transaktionen sind eine wichtige Komponente eines relationalen Datenbankmoduls. Bei SQL Data Warehouse werden Transaktionen während der Änderung von Daten verwendet. Diese Transaktionen können expliziter oder impliziter Art sein. Einzelne `INSERT`-, `UPDATE`- und `DELETE`-Anweisungen sind Beispiele für implizite Transaktionen. Explizite Transaktionen werden explizit von einem Entwickler geschrieben, indem `BEGIN TRAN`, `COMMIT TRAN` oder `ROLLBACK TRAN` verwendet wird. Sie werden normalerweise eingesetzt, wenn mehrere Änderungsanweisungen zu einer einzelnen atomischen Einheit zusammengefasst werden müssen.

Azure SQL Data Warehouse speichert Änderungen in Form von Transaktionsprotokollen in der Datenbank. Jede Verteilung verfügt über ein eigenes Transaktionsprotokoll. Schreibvorgänge für Transaktionsprotokolle werden automatisch durchgeführt. Es ist keine Konfiguration erforderlich. Mit diesem Prozess wird der Schreibvorgang zwar sichergestellt, aber er bedeutet auch Mehraufwand für das System. Sie können diese negative Auswirkung reduzieren, indem Sie Code schreiben, der in Bezug auf Transaktionen effektiv ist. Code dieser Art lässt sich grob in zwei Kategorien einteilen.

- Nutzen von Konstrukten mit minimaler Protokollierung, wo dies möglich ist
- Verarbeiten von Daten mit bereichsbezogenen Batches, um einzelne Transaktionen mit langer Ausführungsdauer zu vermeiden
- Nutzen eines Partitionswechselmusters für große Änderungen einer bestimmten Partition

## Vergleich von minimaler und vollständiger Protokollierung
Im Gegensatz zu vollständig protokollierten Vorgängen, bei denen das Transaktionsprotokoll zum Nachverfolgen aller Zeilenänderungen verwendet wird, werden bei Vorgängen mit minimaler Protokollierung nur Umfangszuordnungen und Metadatenänderungen nachverfolgt. Die minimale Protokollierung umfasst daher nur die Informationen, die erforderlich sind, um für die Transaktion im Falle eines Ausfalls oder einer expliziten Anforderung (`ROLLBACK TRAN`) ein Rollback durchzuführen. Da im Transaktionsprotokoll deutlich weniger Informationen nachverfolgt werden, weist ein Vorgang mit minimaler Protokollierung eine höhere Leistung als ein Vorgang mit ähnlicher Größe und vollständiger Protokollierung auf. Da außerdem weniger Schreibvorgänge für das Transaktionsprotokoll anfallen, wird eine viel kleinere Menge von Protokolldaten generiert, und die E/A-Effizienz steigt.

>[AZURE.NOTE] Vorgänge mit minimaler Protokollierung können Teil von expliziten Transaktionen sein. Da alle Änderungen in Zuordnungsstrukturen nachverfolgt werden, ist es möglich, für Vorgänge mit minimaler Protokollierung ein Rollback durchzuführen. Es ist wichtig zu verstehen, dass für die Änderung eine „minimale“ Protokollierung erfolgt. Dies bedeutet nicht, dass keine Protokollierung durchgeführt wird.

## Vorgänge mit minimaler Protokollierung

Eine minimale Protokollierung ist für folgende Vorgänge möglich:

- CREATE TABLE AS SELECT (CTAS)
- INSERT..SELECT
- CREATE INDEX
- ALTER INDEX REBUILD
- DROP INDEX
- TRUNCATE TABLE
- DROP TABLE
- ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

## Bedingungen der minimalen Protokollierung für Massenladevorgänge

`CTAS` und `INSERT...SELECT` sind beides Massenladevorgänge. Beide werden aber durch die Zieltabellendefinition beeinflusst und sind vom Ladeszenario abhängig. In der Tabelle ist angegeben, ob der Massenvorgang über eine vollständige oder minimale Protokollierung verfügt:

| Primärer Index | Ladeszenario | Protokollierungsmodus |
| --------------------------- | -------------------------------------------------------- | ------------ |
| Heap | Beliebig | **Minimal** |
| Gruppierter Index | Leere Zieltabelle | **Minimal** |
| Gruppierter Index | Geladene Zeilen überlappen sich nicht mit vorhandenen Seiten im Ziel | **Minimal** |
| Gruppierter Index | Geladene Zeilen überlappen sich mit vorhandenen Seiten im Ziel | Vollständig |
| Gruppierter Columnstore-Index | Batchgröße >= 102.400 pro nach Partition ausgerichteter Verteilung | **Minimal** |
| Gruppierter Columnstore-Index | Batchgröße < 102.400 pro nach Partition ausgerichteter Verteilung | Vollständig |

Beachten Sie, dass es sich bei allen Schreibvorgängen zum Aktualisieren von sekundären oder nicht gruppierten Indizes immer um Vorgänge mit vollständiger Protokollierung handelt.

> [AZURE.IMPORTANT] SQL Data Warehouse verfügt über 60 Verteilungen. Wenn davon ausgegangen wird, dass alle Zeilen gleichmäßig verteilt und in einer einzelnen Partition angeordnet sind, muss Ihr Batch also mindestens 6.144.000 Zeilen enthalten, damit beim Schreiben in einen gruppierten Columnstore-Index die minimale Protokollierung verwendet wird. Falls die Tabelle partitioniert ist und die eingefügten Zeilen über Partitionsgrenzen hinweg reichen, benötigen Sie bei gleichmäßiger Datenverteilung 6.144.000 Zeilen pro Partitionsgrenze. Für jede Partition in jeder Verteilung muss unabhängig voneinander der Schwellenwert in Höhe von 102.400 Zeilen überschritten werden, damit für den Einfügevorgang in die Verteilung die minimale Protokollierung angewendet wird.

Das Laden von Daten in eine nicht leere Tabelle mit einem gruppierten Index kann häufig eine Mischung aus vollständig protokollierten und minimal protokollierten Zeilen umfassen. Bei einem gruppierten Index handelt es sich um eine ausbalancierte Struktur (B-Struktur) von Seiten. Falls die Seite, auf die geschrieben wird, bereits Zeilen aus einer anderen Transaktion enthält, werden diese Schreibvorgänge vollständig protokolliert. Aber wenn die Seite leer ist, wird für das Schreiben auf die Seite nur die minimale Protokollierung genutzt.

## Vorgänge mit vollständiger Protokollierung
Zusätzlich zu Aktualisierungen von sekundären Indizes gibt es noch andere Anweisungen, bei denen es sich um Vorgänge mit vollständiger Protokollierung handelt.
 
`UPDATE`- und `DELETE`-Anweisungen sind **immer** Vorgänge mit vollständiger Protokollierung.

Sie können diese Anweisungen aber optimieren, damit sie effizienter ausgeführt werden können.

Unten sind vier Beispiele angegeben, die veranschaulichen, wie Sie Code für Vorgänge mit vollständiger Protokollierung optimieren:

- `CTAS`
- Tabellenpartitionierung
- Batchvorgänge

### Optimieren umfangreicher Löschvorgänge per CTAS
Wenn Sie eine große Datenmenge in einer Tabelle oder Partition löschen müssen, ist es häufiger sinnvoller, stattdessen die Daten mit `SELECT` auszuwählen, die Sie behalten möchten. Sie können per [CTAS][] eine neue Tabelle erstellen. Verwenden Sie nach dem Erstellen ein [RENAME OBJECT][]-Befehlspaar, um die Namen der Tabellen auszutauschen.

```
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(	CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([ProductKey])
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20000101, 20010101, 20020101, 20030101, 20040101, 20050101
												,	20060101, 20070101, 20080101, 20090101, 20100101, 20110101
												,	20120101, 20130101, 20140101, 20150101, 20160101, 20170101
												,	20180101, 20190101, 20200101, 20210101, 20220101, 20230101
												,	20240101, 20250101, 20260101, 20270101, 20280101, 20290101
												)
)
AS
SELECT 	*
FROM 	[dbo].[FactInternetSales]
WHERE	[PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

### Optimieren von umfangreichen Aktualisierungen per CTAS
Wenn Sie eine große Zahl von Zeilen in einer Tabelle oder einer Partition aktualisieren müssen, ist es häufig viel effizienter, einen Vorgang mit minimaler Protokollierung zu verwenden, z.B. [CTAS][].

Im Beispiel unten wurde eine vollständige Tabellenaktualisierung in einen `CTAS`-Vorgang konvertiert, damit die minimale Protokollierung möglich ist.

In diesem Fall fügen wir dem Umsatz in der Tabelle nachträglich einen Rabattbetrag hinzu:

```
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(	CLUSTERED INDEX
,	DISTRIBUTION = HASH([ProductKey])
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20000101, 20010101, 20020101, 20030101, 20040101, 20050101
												,	20060101, 20070101, 20080101, 20090101, 20100101, 20110101
												,	20120101, 20130101, 20140101, 20150101, 20160101, 20170101
												,	20180101, 20190101, 20200101, 20210101, 20220101, 20230101
												,	20240101, 20250101, 20260101, 20270101, 20280101, 20290101
												)
				)
)
AS 
SELECT
	[ProductKey]  
,	[OrderDateKey] 
,	[DueDateKey]  
,	[ShipDateKey] 
,	[CustomerKey] 
,	[PromotionKey] 
,	[CurrencyKey] 
,	[SalesTerritoryKey]
,	[SalesOrderNumber]
,	[SalesOrderLineNumber]
,	[RevisionNumber]
,	[OrderQuantity]
,	[UnitPrice]
,	[ExtendedAmount]
,	[UnitPriceDiscountPct]
,	ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,	[ProductStandardCost]
,	[TotalProductCost]
,	ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
		 ELSE [SalesAmount] - 5
		 END AS MONEY),0) AS [SalesAmount]
,	[TaxAmt]
,	[Freight]
,	[CarrierTrackingNumber] 
,	[CustomerPONumber]
FROM	[dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] Bei der Neuerstellung großer Tabellen kann die Nutzung von SQL Data Warehouse-Features zur Workloadverwaltung vorteilhaft sein. Weitere Details finden Sie im Artikel zur [Parallelität][] im Abschnitt über die Workloadverwaltung.

### Wechseln der Partition zum Aktualisieren von Daten
Bei umfangreichen Änderungen innerhalb einer Partition ist ein Muster für Partitionswechsel sehr sinnvoll. Wenn der Datenänderungsaufwand groß ist und mehrere Partitionen umfasst, lässt sich mit dem einfachen Durchlaufen der Partitionen das gleiche Ergebnis erzielen.

Schritte zum Durchführen eines Partitionswechsels:
1. Erstellen einer leeren Bereinigungspartition
2. Durchführen der Aktualisierung als CTAS-Vorgang
3. Verschieben der vorhandenen Daten in die Tabelle „out“
4. Einfügen der neuen Daten
5. Bereinigen der Daten

Um die zu wechselnden Partitionen besser ermitteln zu können, erstellen wir zuerst ein Hilfsverfahren wie im Beispiel unten.

```
CREATE PROCEDURE dbo.partition_data_get
	@schema_name		   NVARCHAR(128)
,	@table_name			   NVARCHAR(128)
,	@boundary_value		   INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
	DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH	(	DISTRIBUTION = ROUND_ROBIN
		,	HEAP
		)
AS
WITH CTE
AS
(
SELECT 	s.name							AS [schema_name]
,		t.name							AS [table_name]
, 		p.partition_number				AS [ptn_nmbr]
,		p.[rows]						AS [ptn_rows]
,		CAST(r.[value] AS INT)			AS [boundary_value]
FROM		sys.schemas					AS s
JOIN		sys.tables					AS t	ON  s.[schema_id]		= t.[schema_id]
JOIN		sys.indexes					AS i	ON 	t.[object_id]		= i.[object_id]
JOIN		sys.partitions				AS p	ON 	i.[object_id]		= p.[object_id] 
												AND i.[index_id]		= p.[index_id] 
JOIN		sys.partition_schemes		AS h	ON 	i.[data_space_id]	= h.[data_space_id]
JOIN		sys.partition_functions		AS f	ON 	h.[function_id]		= f.[function_id]
LEFT JOIN	sys.partition_range_values	AS r 	ON 	f.[function_id]		= r.[function_id] 
												AND r.[boundary_id]		= p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT	*
FROM	CTE
WHERE	[schema_name]		= @schema_name
AND		[table_name]		= @table_name
AND		[boundary_value]	= @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Mit diesem Verfahren wird die Wiederverwendung von Code erhöht, und das Partitionswechselbeispiel ist übersichtlicher.

Im Code unten sind die fünf oben erwähnten Schritte enthalten, mit denen eine vollständige Partitionswechselroutine erzielt werden kann.

```
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
	DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(	DISTRIBUTION = HASH([ProductKey])
,	CLUSTERED COLUMNSTORE INDEX
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20020101, 20030101
												)
				)
)
AS
SELECT *
FROM	[dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
	DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(	DISTRIBUTION = HASH([ProductKey])
,	CLUSTERED COLUMNSTORE INDEX
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20020101, 20030101
												)
				)
)
AS 
SELECT
	[ProductKey]  
,	[OrderDateKey] 
,	[DueDateKey]  
,	[ShipDateKey] 
,	[CustomerKey] 
,	[PromotionKey] 
,	[CurrencyKey] 
,	[SalesTerritoryKey]
,	[SalesOrderNumber]
,	[SalesOrderLineNumber]
,	[RevisionNumber]
,	[OrderQuantity]
,	[UnitPrice]
,	[ExtendedAmount]
,	[UnitPriceDiscountPct]
,	ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,	[ProductStandardCost]
,	[TotalProductCost]
,	ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
		 ELSE [SalesAmount] - 5
		 END AS MONEY),0) AS [SalesAmount]
,	[TaxAmt]
,	[Freight]
,	[CarrierTrackingNumber] 
,	[CustomerPONumber]
FROM	[dbo].[FactInternetSales]
WHERE	OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]	SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))	+' TO [dbo].[FactInternetSales_out] PARTITION '	+CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))	+' TO [dbo].[FactInternetSales] PARTITION '		+CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

### Aufteilen von Datenänderungsvorgängen in verwaltbare Teile
Bei großen Datenänderungsvorgängen kann es sinnvoll sein, den Vorgang in Teile oder Batches aufzuteilen, um kleinere Einheiten zu erhalten.

Unten ist ein funktionierendes Beispiel angegeben. Für die Batchgröße wurde ein Beispielwert gewählt, um die Vorgehensweise zu verdeutlichen. In Wirklichkeit wäre der Wert für die Batchgröße deutlich höher.

```
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
	DROP TABLE #t;
	PRINT '#t dropped';
END

CREATE TABLE #t
WITH	(	DISTRIBUTION = ROUND_ROBIN
		,	HEAP
		)
AS
SELECT	ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,		SalesOrderNumber
,		SalesOrderLineNumber
FROM	dbo.FactInternetSales
WHERE	[OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE	@seq_start		INT = 1
,		@batch_iterator	INT = 1
,		@batch_size		INT = 50
,		@max_seq_nmbr	INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE	@batch_count	INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,		@seq_end		INT = @batch_size
;

SELECT COUNT(*)
FROM	dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE	@batch_iterator <= @batch_count
BEGIN
	DELETE
	FROM	dbo.FactInternetSales
	WHERE EXISTS
	(
			SELECT	1
			FROM	#t t
			WHERE	seq_nmbr BETWEEN  @seq_start AND @seq_end
			AND		FactInternetSales.SalesOrderNumber		= t.SalesOrderNumber
			AND		FactInternetSales.SalesOrderLineNumber	= t.SalesOrderLineNumber
	)
	;

	SET @seq_start = @seq_end
	SET @seq_end = (@seq_start+@batch_size);
	SET @batch_iterator +=1;
END
```

## Zusätzliche Anleitung zu den Vorgängen vom Typ „Anhalten“ und „Skalieren“
Mit Azure SQL Data Warehouse können Sie den Vorgang anhalten und fortsetzen und das Data Warehouse bedarfsgesteuert skalieren. Wenn Sie SQL Data Warehouse anhalten oder skalieren, ist es wichtig zu verstehen, dass alle laufenden Transaktionen sofort beendet werden. Dies führt dazu, dass für alle geöffneten Transaktionen ein Rollback durchgeführt wird. Wenn für Ihre Workload vor dem Anhalte- oder Skaliervorgang eine längere und unvollständige Datenänderung ausgegeben wurde, müssen diese Schritte rückgängig gemacht werden. Dies kann sich auf den Zeitraum auswirken, der für das vollständige Anhalten der Azure SQL Data Warehouse-Datenbank erforderlich ist.

> [AZURE.IMPORTANT] Sowohl `UPDATE` als auch `DELETE` sind Vorgänge mit vollständiger Protokollierung. Diese Vorgänge zum Rückgängigmachen und Wiederholen können also deutlich länger als vergleichbare Vorgänge mit minimaler Protokollierung dauern.

Die beste Vorgehensweise ist, auf den Abschluss aktiver Datenänderungstransaktionen zu warten, bevor SQL Data Warehouse angehalten oder skaliert wird. Allerdings ist dies unter Umständen nicht immer praktikabel. Sie können folgende Optionen verwenden, um das Risiko eines langen Rollbackvorgangs zu verringern:

- Schreiben Sie Vorgänge mit langer Ausführungsdauer um, indem Sie [CTAS][] verwenden.
- Teilen Sie den Vorgang in Teile auf, und arbeiten Sie mit einer Teilmenge der Zeilen.

## Nächste Schritte
Weitere Entwicklungstipps und Inhalte zu den gezeigten Beispielen finden Sie in den folgenden Artikeln:

- [Entwicklung][]
- [Transaktionen][]
- [Tabellenpartitionierung][]
- [Parallelität][]
- [CTAS][]
- [RENAME OBJECT][]

<!--Image references-->

<!--ACOM references-->
[Entwicklung]: sql-data-warehouse-overview-develop.md
[Transaktionen]: sql-data-warehouse-develop-transactions.md
[Tabellenpartitionierung]: sql-data-warehouse-develop-table-partitions.md
[table partition]: sql-data-warehouse-develop-table-partitions.md
[Parallelität]: sql-data-warehouse-develop-concurrency.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[RENAME OBJECT]: sql-data-warehouse-develop-rename.md

<!--MSDN references-->
[alter index]: https://msdn.microsoft.com/de-DE/library/ms188388.aspx

<!---HONumber=AcomDC_0323_2016-->