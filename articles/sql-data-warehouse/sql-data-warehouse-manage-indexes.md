<properties
   pageTitle="Verwalten von Indizes | Microsoft Azure"
   description="Anleitung zum Verwalten von Indizes für Benutzer"
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

# Verwalten von Indizes
In diesem Artikel werden wichtige Verfahren zum Verwalten von Indizes veranschaulicht.

## Optimieren von Indexneuerstellungen
Sie können die Indexneuerstellung auf zwei Arten optimieren:

1. Partitionieren der Tabelle und Ausführen von Indexneuerstellungen auf Partitionsebene
2. Verwenden von [CTAS][], um die Partition mit den Daten in einer neuen Tabelle neu zu erstellen und einen Partitionswechsel in die neue Tabelle durchzuführen

## Partitionierte Indexneuerstellungen

Unten ist ein Beispiel für die Neuerstellung einer einzelnen Partition angegeben:

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

ALTER INDEX..REBUILD eignet sich am besten für kleinere Datenvolumen, besonders für Columnstore-Indizes. Geöffnete, geschlossene und komprimierte Zeilengruppen werden alle in die Wiederherstellung eingeschlossen. Falls die Partition aber sehr groß ist, ist `CTAS` der effizientere Vorgang. Unten ist ein Beispiel für eine vollständige Indexneuerstellung angegeben.

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

Weitere Details zu dieser Syntax finden Sie im Artikel [ALTER INDEX][].

## Verwenden von CTAS zum Neuerstellen einer Partition

Unten ist ein Beispiel für die Neuerstellung einer Partition per CTAS angegeben:

```
-- Step 01. Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;

```

## Nächste Schritte
Weitere Anleitungen zur Erstellung und Größenauslegung von Partitionen finden Sie im Artikel zur [Tabellenpartitionierung][]. Der Artikel enthält außerdem ein Beispiel zur Identifizierung von Partitionsgrenzen.

Weitere Verwaltungstipps finden Sie in der Übersicht zur [Verwaltung][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Tabellenpartitionierung]: sql-data-warehouse-develop-table-partitions.md
[Verwaltung]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/de-DE/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->