<properties
   pageTitle="Create Table As Select (CTAS) in SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Codierung mit der Anweisung „Create Table As Select“ (CTAS) in Azure SQL Data Warehouse zum Entwickeln von Lösungen"
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Create Table As Select (CTAS) in SQL Data Warehouse
„Create Table As Select“ (CTAS) ist eines der wichtigsten verfügbaren T-SQL-Features. Es handelt sich dabei um einen vollständig parallelisierten Vorgang, bei dem eine neue Tabelle anhand der Ausgabe einer Select-Anweisung erstellt wird. Sie können sich dies wie eine Turboversion von SELECT..INTO vorstellen.

Mit CTAS können auch einige nicht unterstützte Features umgangen werden, die oben aufgeführt sind. Dies kann häufig zu einer Win-Win-Situation führen, da Ihr Code nicht nur kompatibel ist, sondern unter SQL Data Warehouse häufig auch schneller ausgeführt wird. Der Grund hierfür ist das vollständig parallelisierte Design.

> [AZURE.NOTE]Denken Sie zuerst an „CTAS“. Wenn Sie ein Problem voraussichtlich mit CTAS lösen können, ist dies meist der beste Ansatz. Dies gilt auch, wenn Sie dabei mehr Daten schreiben.

Szenarien, die mit CTAS umgangen werden können, sind z. B.:

- SELECT..INTO
- ANSI JOINS bei UPDATEs 
- ANSI JOINs bei DELETEs
- MERGE-Anweisung

Dieses Dokument enthält auch einige bewährte Methoden für die Codierung mit CTAS.

## SELECT..INTO
Unter Umständen kommt SELECT..INTO an einigen Stellen Ihrer Lösung vor.

Hier ist ein Beispiel für SELECT..INTO angegeben:

```
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Die Konvertierung in CTAS ist relativ einfach:

```
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

Bei Verwendung von CTAS können Sie auch eine Präferenz für die Datenverteilung angeben und die Tabelle bei Bedarf indizieren.

## ANSI-Verknüpfungsersatz für update-Anweisungen

Unter Umständen verfügen Sie über ein komplexes Update, mit dem zwei oder mehr Tabellen verknüpft werden, indem ANSI-Verknüpfungssyntax zum Durchführen des UPDATE- oder DELETE-Vorgangs genutzt wird.

Stellen Sie sich vor, Sie müssen diese Tabelle aktualisieren:

```
CREATE TABLE [dbo].[AnnualCategorySales]
(	[EnglishProductCategoryName]	NVARCHAR(50)	NOT NULL
,	[CalendarYear]					SMALLINT		NOT NULL
,	[TotalSalesAmount]				MONEY			NOT NULL
)
WITH
(
	DISTRIBUTION = ROUND_ROBIN
)
;
```

Die ursprüngliche Abfrage könnte etwa wie folgt ausgesehen haben:

```
UPDATE	acs
SET		[TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM	[dbo].[AnnualCategorySales] 	AS acs
JOIN	(
		SELECT	[EnglishProductCategoryName]
		,		[CalendarYear]
		,		SUM([SalesAmount])				AS [TotalSalesAmount]
		FROM	[dbo].[FactInternetSales]		AS s
		JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
		JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
		JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
		JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
		WHERE 	[CalendarYear] = 2004
		GROUP BY
				[EnglishProductCategoryName]
		,		[CalendarYear]
		) AS fis
ON	[acs].[EnglishProductCategoryName]	= [fis].[EnglishProductCategoryName]
AND	[acs].[CalendarYear]				= [fis].[CalendarYear]
;
```

Da SQL Data Warehouse ANSI-Verknüpfungen nicht unterstützt, können Sie diesen Code nicht einfach kopieren, ohne ihn leicht zu ändern.

Sie können eine Kombination aus CTAS und einer impliziten Verknüpfung verwenden, um diesen Code zu ersetzen:

```
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT	ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)	AS [EnglishProductCategoryName]
,		ISNULL(CAST([CalendarYear] AS SMALLINT),0) 						AS [CalendarYear]
,		ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)						AS [TotalSalesAmount]
FROM	[dbo].[FactInternetSales]		AS s
JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
WHERE 	[CalendarYear] = 2004
GROUP BY
		[EnglishProductCategoryName]
,		[CalendarYear]
;

-- Use an implicit join to perform the update 
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## ANSI-Verknüpfungsersatz für delete-Anweisungen
Manchmal ist CTAS der beste Ansatz zum Löschen von Daten. Anstatt die Daten zu löschen, wählen Sie einfach die Daten aus, die Sie behalten möchten. Dies gilt besonders für DELETE-Anweisungen, in denen ANSI-Verknüpfungssyntax verwendet wird, da dies in SQL Data Warehouse nicht unterstützt wird.

Beispiel für eine konvertierte DELETE-Anweisung:

```
CREATE TABLE dbo.DimProduct_upsert
WITH 
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p 
RIGHT JOIN dbo.stg_DimProduct s 
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## Ersetzen von MERGE-Anweisungen
MERGE-Anweisungen können mit CTAS zumindest teilweise ersetzt werden. Sie können `INSERT` und `UPDATE` zu einer einzelnen Anweisung zusammenfassen. Alle gelöschten Datensätze müssen in einer zweiten Anweisung abgeschlossen werden.

Beispiel für `UPSERT`:

```
CREATE TABLE dbo.[DimProduct_upsert]
WITH 
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS 
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## CTAS-Empfehlung: Explizites Angeben des Datentyps und der NULL-Zulässigkeit der Ausgabe

Beim Migrieren von Code stoßen Sie unter Umständen auf diese Art von Codiermuster:

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f 
;
```

Instinktiv denken Sie vielleicht, dass Sie diesen Code zu CTAS migrieren sollten, und damit hätten Sie auch recht. Hierbei liegt aber ein verstecktes Problem vor.

Der folgende Code führt NICHT zum gleichen Ergebnis:

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Beachten Sie, dass für die Spalte „result“ der Datentyp und die NULL-Zulässigkeitswerte des Ausdrucks übernommen werden. Dies kann zu geringfügigen Abweichungen bei den Werten führen, wenn nicht darauf geachtet wird.

Probieren Sie folgende Eingabe aus:

```
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Der für „result“ gespeicherte Wert ist anders. Da der in der Spalte „result“ beibehaltene Wert in anderen Ausdrücken verwendet wird, wird der Fehler noch signifikanter.

![][1]

Dies ist besonders für Datenmigrationen wichtig. Auch wenn die zweite Abfrage natürlich genauer ist, liegt ein Problem vor. Die Daten würden sich gegenüber dem Quellsystem unterscheiden, und dies stellt die Integrität der Migration infrage. Dies ist einer der seltenen Fälle, in denen die „falsche“ Antwort eigentlich die richtige Antwort ist!

Der Grund für diese Ungleichheit zwischen den beiden Ergebnissen ist die implizite Typumwandlung. Im ersten Beispiel wird für die Tabelle die Spaltendefinition festgelegt. Beim Einfügen der Zeile tritt eine implizite Typumwandlung auf. Im zweiten Beispiel ist keine implizite Typumwandlung vorhanden, da der Ausdruck den Datentyp der Spalte definiert. Beachten Sie auch, dass die Spalte im zweiten Beispiel so konfiguriert wurde, dass NULL-Werte zulässig sind. Im ersten Beispiel ist dies nicht der Fall. Als die Tabelle im ersten Beispiel erstellt wurde, wurde die NULL-Zulässigkeit der Spalte explizit definiert. Im zweiten Beispiel wurde dies dem Ausdruck überlassen, was standardmäßig zu einer NULL-Definition führen würde.

Zum Lösen dieser Probleme müssen Sie die Typumwandlung und NULL-Zulässigkeit im SELECT-Teil der CTAS-Anweisung explizit festlegen. Sie können diese Eigenschaften nicht im Create Table-Teil festlegen.

Im folgenden Beispiel wird veranschaulicht, wie Sie dies im Code beheben:

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Beachten Sie Folgendes: - CAST oder CONVERT hätten verwendet werden können - ISNULL wird zum Erzwingen der NULL-Zulässigkeit verwendet, nicht COALESCE - ISNULL ist die äußerste Funktion - Der zweite Teil von ISNULL ist eine Konstante, also 0

> [AZURE.NOTE]Damit die NULL-Zulässigkeit richtig festgelegt wird, muss unbedingt ISNULL verwendet werden, und nicht COALESCE. COALESCE ist keine deterministische Funktion, sodass für das Ergebnis des Ausdrucks immer NULL-Werte zulässig wären. Dies ist bei ISNULL anders. Die Funktion ist deterministisch. Wenn der zweite Teil der ISNULL-Funktion eine Konstante oder ein Literal ist, ist der sich ergebende Wert NICHT NULL.

Dieser Tipp ist nicht nur nützlich, um die Integrität der Berechnungen sicherzustellen. Er ist auch für das Wechseln der Tabellenpartition wichtig. Stellen Sie sich vor, Sie haben die folgende Tabelle als Faktentabelle definiert:

```
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH 
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
) 
;
```

Das Wertfeld ist aber ein berechneter Ausdruck und nicht Teil der Quelldaten.

Zum Erstellen des partitionierten Datasets können Sie beispielsweise wie folgt vorgehen:

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Die Abfrage würde problemlos ausgeführt werden. Das Problem entsteht, wenn Sie versuchen, den Partitionswechsel durchzuführen. Die Tabellendefinitionen stimmen nicht überein. CTAS muss geändert werden, damit die Tabellendefinitionen übereinstimmen.

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Sie sehen also, dass die Typkonsistenz und die Pflege der Eigenschaften für die NULL-Zulässigkeit für CTAS eine bewährte Methode für das Engineering sind. Es ist hilfreich, die Integrität in Ihren Berechnungen zu wahren. Außerdem wird so sichergestellt, dass der Partitionswechsel möglich ist.

Weitere Informationen zur Verwendung von [CTAS][] finden Sie auf der MSDN-Website. Dabei handelt es sich um eine der wichtigsten Anweisungen in Azure SQL Data Warehouse. Machen Sie sich damit eingehend vertraut.

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CTAS]: https://msdnstage.redmond.corp.microsoft.com/de-de/library/mt204041.aspx

<!--Other Web references-->

<!---HONumber=August15_HO6-->