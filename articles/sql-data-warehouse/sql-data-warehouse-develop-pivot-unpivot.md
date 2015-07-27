<properties
   pageTitle="Pivotieren und Entpivotieren von Daten in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Pivotieren und Entpivotieren von Daten in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
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

# Pivotieren und Entpivotieren von Daten in SQL Data Warehouse

Sie können Daten in SQL Data Warehouse pivotieren, indem Sie eine CASE-Anweisung verwenden.

Dieser Artikel enthält zwei einfache Beispiele dazu, wie eine Tabelle pivotiert und entpivotiert wird, ohne die Syntax zum Pivotieren und Entpivotieren von SQL Server zu verwenden.

## Pivotieren

```
CREATE TABLE AnnualSales_pvt
WITH    (   DISTRIBUTION = ROUND_ROBIN
        )
AS
WITH SalesPVT 
AS
(   SELECT  [EnglishProductCategoryName]
    ,       CASE WHEN [CalendarYear] = 2001 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2001'
    ,       CASE WHEN [CalendarYear] = 2002 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2002'
    ,       CASE WHEN [CalendarYear] = 2003 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2003'
    ,       CASE WHEN [CalendarYear] = 2004 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2004'
    FROM    [dbo].[FactInternetSales] s
    JOIN    [dbo].[DimDate] d               ON s.[OrderDateKey]          = d.[DateKey]
    JOIN    [dbo].[DimProduct] p            ON s.[ProductKey]            = p.[ProductKey]
    JOIN    [dbo].[DimProductSubCategory] u ON p.[ProductSubcategoryKey] = u.[ProductSubcategoryKey]
    JOIN    [dbo].[DimProductCategory] c    ON u.[ProductCategoryKey]    = c.[ProductCategoryKey]
    GROUP BY 
            [EnglishProductCategoryName]
    ,       [CalendarYear]
)
SELECT  [EnglishProductCategoryName]
,       SUM([CY_2001])  AS 'CY_2001'
,       SUM([CY_2002])  AS 'CY_2002'
,       SUM([CY_2003])  AS 'CY_2003'
,       SUM([CY_2004])  AS 'CY_2004'
FROM    SalesPVT
GROUP BY 
        [EnglishProductCategoryName]
;
```

## Entpivotieren

Das Entpivotieren ist etwas komplexer. Es ist mit `CASE` aber trotzdem machbar. Außerdem müssen Sie hierfür zunächst ermitteln, wie viele Spalten entpivotiert werden sollen. Im vorherigen Beispiel wurden vier Spalten pivotiert. Dies behalten wir bei. Zum Durchführen der Entpivotierung müssen wir das Dataset vorübergehend um den Faktor 4 verstärken. Dies ergibt einen zweistufigen Vorgang:

Erstellen Sie zuerst eine temporäre Tabelle mit vier Zeilen. Wir verwenden diese Tabelle, um die Daten zu verstärken:

```
CREATE TABLE #Nmbr
WITH 
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION     = USER_DB
)
AS
SELECT 1 AS 'Number'
UNION ALL
SELECT 2
UNION ALL
SELECT 3
UNION ALL
SELECT 4
OPTION (LABEL = 'CTAS : #Nmbr : CREATE')
;
```

Im zweiten Schritt verwenden wir CASE, um die Daten bedingt zu entpivotieren, indem wir den Satz zurück in Zeilen konvertieren. Hierzu müssen wir ein kartesisches Produkt erstellen, indem wir eine Verknüpfung mit der temporären Tabelle #Nmbr einrichten, die wir im ersten Schritt erstellt haben:

```
SELECT  [EnglishProductCategoryName]
,       CASE    c.[Number]
                WHEN 1 THEN [CY_2001]
                WHEN 2 THEN [CY_2002]
                WHEN 3 THEN [CY_2003]
                WHEN 4 THEN [CY_2004]
        END as Sales
FROM AnnualSales_pvt
JOIN #Nmbr c ON 1=1
WHERE   CASE    c.[Number]
                WHEN 1 THEN CY_2001
                WHEN 2 THEN CY_2002
                WHEN 3 THEN CY_2003
                WHEN 4 THEN CY_2004
        END IS NOT NULL
OPTION (LABEL = 'Unpivot :  : SELECT')
;
```

Vergessen Sie am Ende nicht die Bereinigung, indem Sie die temporäre Tabelle verwerfen.

```
DROP TABLE #Nmbr
;
```

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->