<properties
   pageTitle="Verwalten von Statistiken in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Verwalten von Statistiken in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
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
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Verwalten von Statistiken in SQL Data Warehouse
 In SQL Data Warehouse werden statistische Daten verwendet, um die Kosten für unterschiedliche Wege zum Durchführen einer verteilten Abfrage zu bewerten. Wenn die Statistiken präzise sind, können mit dem Abfrageoptimierer qualitativ hochwertige Abfragepläne generiert werden, um die Abfrageleistung zu verbessern.

Das Erstellen und Aktualisieren der Statistiken ist wichtig, um die Abfrageleistung zu erzielen, für die SQL Data Warehouse ausgelegt ist. Diese Anleitung enthält eine Übersicht über die Statistiken sowie folgende Vorgehensweisen:

- Erstellen von Statistiken im Rahmen des Datenbankentwurfs
- Aktualisieren von Statistiken im Rahmen der Datenbankwartung
- Anzeigen von Statistiken mit Systemsichten und -Funktionen

## Einführung in Statistiken

Statistiken für einzelne Spalten sind Objekte, die Informationen zum Wertebereich und zur Häufigkeit von Werten in einer Spalte enthalten. Der Abfrageoptimierer verwendet dieses Histogramm, um die Anzahl von Spalten im Abfrageergebnis zu schätzen. Dies wirkt sich direkt auf Entscheidungen aus, die in Bezug auf die Optimierung der Abfrage getroffen werden.

Statistiken für mehrere Spalten sind Statistiken, die für eine Liste mit Spalten erstellt werden. Sie enthalten Einspaltenstatistiken für die erste Spalte der Liste sowie einige spaltenübergreifende Korrelationsinformationen, die als „Densities“ (Dichten) bezeichnet werden. Mehrspaltenstatistiken können die Abfrageleistung für bestimmte Vorgänge verbessern, z. B. zusammengesetzte Verknüpfungen (Joins) und Group By-Vorgänge.

Weitere Informationen finden Sie unter [DBCC SHOW_STATISTICS][] auf der MSDN-Website.

## Warum sind Statistiken erforderlich?
Ohne geeignete Statistiken können Sie nicht die Leistung erzielen, für deren Bereitstellung SQL Data Warehouse ausgelegt ist. Für Tabellen und Spalten werden von SQL Data Warehouse nicht automatisch Statistiken generiert, sodass Sie diese selbst erstellen müssen. Es ist ratsam, sie während des Erstellvorgangs der Tabelle zu erstellen und dann nach dem Auffüllen zu aktualisieren.

> [AZURE.NOTE]Wenn Sie SQL Server verwenden, können Sie ggf. SQL Server das Erstellen und Aktualisieren von Einspaltenstatistiken je nach Bedarf überlassen. In diesem Punkt unterscheidet sich SQL Data Warehouse. Da die Daten verteilt sind, werden in SQL Data Warehouse nicht automatisch Statistiken über alle verteilten Daten hinweg zusammengefasst. Die zusammengefassten Statistiken werden nur generiert, wenn Sie Statistiken erstellen und aktualisieren.

## Gründe für das Erstellen von Statistiken
Ein kohärenter Satz aktueller Statistiken ist ein zentraler Bestandteil von SQL Data Warehouse. Daher ist es wichtig, beim Entwerfen von Tabellen Statistiken zu erstellen.

Das Erstellen von Einspaltenstatistiken für jede Spalte ist eine einfache Möglichkeit zum Einsteigen in Statistiken. Es gibt aber immer auch Vor- und Nachteile, was die Leistung einerseits und die Kosten für die Erstellung und Aktualisierung von Statistiken andererseits betrifft. Wenn Sie Einspaltenstatistiken für alle Spalten erstellen und dann herausfinden, dass das Aktualisieren aller Statistiken zu lange dauert, können Sie einige Statistiken verwerfen oder einige Statistiken häufiger als andere aktualisieren.

Mehrspaltenstatistiken werden vom Abfrageoptimierer nur verwendet, wenn sich die Spalten in zusammengesetzten Verknüpfungen oder Group By-Klauseln befinden. Zusammengesetzte Filter profitieren derzeit nicht von Mehrspaltenstatistiken.

Es ist zu Beginn der SQL Data Warehouse-Entwicklung daher eine gute Idee, das folgende Muster zu implementieren: - Erstellen von Einspaltenstatistiken für jede Spalte in jeder Tabelle - Erstellen von Mehrspaltenstatistiken für Spalten, die in Verknüpfungen und Group By-Klauseln verwendet werden

Wenn Sie später genauer wissen, wie Sie Ihre Daten abfragen möchten, können Sie dieses Modell verfeinern. Dies gilt vor allem, wenn die Tabellen breit sind. Einen erweiterten Methodenansatz finden Sie im Abschnitt [Implementieren der Statistikverwaltung](## Implementieren der Statistikverwaltung).

## Gründe für das Aktualisieren von Statistiken
Es ist wichtig, das Aktualisieren von Statistiken in den üblichen Ablauf Ihrer Datenbankverwaltung einzubeziehen. Wenn sich die Verteilung der Daten in der Datenbank ändert, müssen die Statistiken aktualisiert werden. Andernfalls kann es zu einer suboptimalen Abfrageleistung kommen, und es könnte sich nicht lohnen, die weitere Problembehebung für die Abfrage durchzuführen.

Deshalb sollte bei der Problembehebung für eine Abfrage eine der ersten Fragen wie folgt lauten: „Sind die Statistiken auf dem aktuellen Stand?“

Diese Frage kann nicht anhand des Alters beantwortet werden. Ein aktuelles Statistikobjekt kann sehr alt sein. Wenn sich die Anzahl von Zeilen ändert oder eine wesentliche Änderung bei der Verteilung der Werte für eine bestimmte Spalte vorgenommen wird, *ist der Zeitpunkt gekommen*, die Statistiken zu aktualisieren.

Für Datumsspalten in einem Data Warehouse sind normalerweise häufige Statistikaktualisierungen erforderlich. Bei jedem Laden von neuen Zeilen in das Data Warehouse werden neue Datumsangaben für Lade- oder Transaktionsvorgänge hinzugefügt. Dadurch wird die Datenverteilung geändert, und die Statistiken sind nicht mehr aktuell.

Im Gegensatz dazu müssen die Statistiken für die Spalte „Geschlecht“ in einer Kundentabelle unter Umständen nie aktualisiert werden. Wenn davon auszugehen ist, dass die Verteilung zwischen Kunden konstant ist, bewirkt das Hinzufügen neuer Zeilen zur Tabellenvariante keine Änderung der Datenverteilung. Wenn Ihr Data Warehouse aber nur ein Geschlecht enthält und eine neue Anforderung zu mehr als einem Geschlecht führt, müssen Sie die Statistiken für die Spalte „Geschlecht“ auf jeden Fall aktualisieren.

Weitere Informationen finden Sie unter [Statistiken][] auf der MSDN-Website.

## Implementieren der Statistikverwaltung

Häufig ist es ratsam, den Datenladeprozess zu erweitern, um sicherzustellen, dass die Statistiken am Ende des Ladevorgangs aktualisiert werden. Das Laden von Daten ist der Zeitpunkt, zu dem Tabellen am häufigsten ihre Größe oder die Verteilung der Werte ändern. Daher ist dies ein logischer Ansatzpunkt zum Implementieren einiger Verwaltungsprozesse.

Unten sind einige Richtlinien zur Aktualisierung von Statistiken während des Ladeprozesses angegeben:

- Stellen Sie sicher, dass jede geladene Tabelle mindestens über ein aktualisiertes Statistikobjekt verfügt. Im Rahmen der Statistikaktualisierung werden dann die Informationen zur Tabellengröße (Zeilen- und Seitenanzahl) aktualisiert.
- Konzentrieren Sie sich auf Spalten mit JOIN-, GROUP BY-, ORDER BY- und DISTINCT-Klauseln.
- Erwägen Sie, Spalten vom Typ „aufsteigender Schlüssel“, z. B. Transaktionsdaten, häufiger zu aktualisieren, da diese Werte nicht in das Statistikhistogramm einbezogen werden.
- Erwägen Sie, Spalten mit statischer Verteilung weniger häufig zu aktualisieren.
- Bedenken Sie, dass jedes statistische Objekt der Reihe nach aktualisiert wird. Es ist ggf. nicht ideal, einfach `UPDATE STATISTICS <TABLE_NAME>` zu implementieren. Dies gilt besonders für breite Tabellen mit vielen Statistikobjekten.

> [AZURE.NOTE]Weitere Informationen zum Thema [Aufsteigender Schlüssel] finden Sie im Whitepaper zum Kardinalitätsschätzungsmodell von SQL Server 2014.

Weitere Informationen finden Sie unter [Kardinalitätsschätzung][] auf der MSDN-Website.

## Beispiele: Erstellen von Statistiken

In diesen Beispielen wird veranschaulicht, wie Sie verschiedene Optionen zum Erstellen von Statistiken verwenden. Die Optionen, die Sie für die einzelnen Spalten verwenden, richten Sie nach den Merkmalen Ihrer Daten und nach der Verwendung der Spalten in Abfragen.

### A: Erstellen von Einspaltenstatistiken mit Standardoptionen

Zum Erstellen von Statistiken für eine Spalte geben Sie einfach einen Namen für das Statistikobjekt und den Namen der Spalte an.

Bei dieser Syntax werden alle Standardoptionen verwendet. Standardmäßig wird in SQL Data Warehouse beim Erstellen von Statistiken eine Stichprobe von 20 % der Tabelle verwendet.

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Beispiel:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### B. Erstellen von Einspaltenstatistiken per Untersuchung jeder Zeile

Die standardmäßige Stichprobenrate von 20 % ist in den meisten Fällen ausreichend. Sie können die Stichprobenrate aber auch anpassen.

Verwenden Sie die folgende Syntax, um die gesamte Tabelle zu verwenden:

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Beispiel:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### C. Erstellen von Einspaltenstatistiken durch Angeben der Stichprobengröße

Alternativ dazu können Sie die Stichprobengröße als Prozentwert angeben:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### D. Erstellen von Einspaltenstatistiken für einen Teil der Zeilen

Eine weitere Möglichkeit ist das Erstellen von Statistiken für einen Teil der Zeilen einer Tabelle. Dies wird als gefilterte Statistik bezeichnet.

Sie können gefilterte Statistiken beispielsweise verwenden, wenn Sie planen, eine bestimmte Partition einer großen partitionierten Tabelle abzufragen. Indem Sie nur für die Partitionswerte Statistiken erstellen, wird die Genauigkeit der Statistiken erhöht und die Abfrageleistung verbessert.

In diesem Beispiel werden Statistiken für einen Bereich von Werten erstellt. Die Werte können leicht so definiert werden, dass sie den Werten in einer Partition entsprechen.

```
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE]Damit der Abfrageoptimierer beim Auswählen des Plans für die verteilte Abfrage die Verwendung von gefilterten Statistiken berücksichtigt, muss die Abfrage in die Definition des Statistikobjekts passen. Im vorherigen Beispiel müssen für die Where-Klausel der Abfrage col1-Werte zwischen 2000101 und 20001231 angegeben werden.

### E. Erstellen von Einspaltenstatistiken mit allen Optionen

Sie können die Optionen natürlich kombinieren. Im folgenden Beispiel wird ein Objekt vom Typ „gefilterte Statistik“ mit einer benutzerdefinierten Stichprobengröße erstellt:

```
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Die gesamte Referenz finden Sie unter [CREATE STATISTICS][] auf der MSDN-Website.

### F. Erstellen von Mehrspaltenstatistiken

Verwenden Sie zum Erstellen einer Mehrspaltenstatistik einfach die vorherigen Beispiele, aber geben Sie mehr Spalten an.

> [AZURE.NOTE]Das Histogramm, das zum Schätzen der Zeilenanzahl im Abfrageergebnis verwendet wird, ist nur für die erste Spalte verfügbar, die in der Definition des Statistikobjekts aufgelistet ist.

In diesem Beispiel basiert das Histogramm auf *product_category*. Spaltenübergreifende Statistiken werden für *product_category* und *product_sub_c\ategory* berechnet:

```
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Da zwischen *product_category* und *product_sub_category* eine Korrelation besteht, kann eine Mehrspaltenstatistik nützlich sein, wenn gleichzeitig auf diese Spalten zugegriffen wird.

### G. Erstellen von Statistiken für alle Spalten einer Tabelle

Eine Möglichkeit zum Erstellen von Statistiken ist das Ausgeben von CREATE STATISTICS-Befehlen nach dem Erstellen der Tabelle.

```
CREATE TABLE dbo.table1 
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1;
CREATE STATISTICS stats_col2 on dbo.table2;
CREATE STATISTICS stats_col3 on dbo.table3;
```

### H. Verwenden einer gespeicherten Prozedur zum Erstellen von Statistiken für alle Spalten einer Datenbank

SQL Data Warehouse verfügt nicht über eine im System gespeicherte Prozedur, die [sp_create_stats][] in SQL Server entspricht. Mit dieser gespeicherten Prozedur wird ein Einzelspaltenstatistik-Objekt für jede Spalte der Datenbank erstellt, die nicht bereits über eine Statistik verfügt.

Dies ist eine nützliche Einstiegshilfe für den Datenbankentwurf. Sie können diesen Vorgang an Ihre Anforderungen anpassen.

```
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
WHERE       l.[object_id] IS NULL
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')'
        WHEN 2
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN'
        WHEN 3
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT'
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Rufen Sie einfach die Prozedur auf, um damit Statistiken für alle Spalten der Tabelle zu erstellen.

```
prc_sqldw_create_stats;
```

## Beispiele: Aktualisieren von Statistiken

Sie können wie folgt vorgehen, um Statistiken zu aktualisieren:

1. Aktualisieren Sie ein Statistikobjekt. Geben Sie den Namen des Statistikobjekts an, das Sie aktualisieren möchten.
2. Aktualisieren Sie alle Statistikobjekte einer Tabelle. Geben Sie anstelle eines bestimmten Statistikobjekts den Namen der Tabelle an.


### A. Aktualisieren eines bestimmten Statistikobjekts ###
Verwenden Sie die folgende Syntax, um ein bestimmtes Statistikobjekt zu aktualisieren:

```
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Beispiel:

```
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Indem Sie bestimmte Statistikobjekte aktualisieren, können Sie den Zeit- und Ressourcenaufwand reduzieren, der zum Verwalten von Statistiken erforderlich ist. Hierfür ist aber Überlegung gefragt, damit die besten Statistikobjekte für die Aktualisierung ausgewählt werden können.


### B. Aktualisieren aller Statistiken einer Tabelle ###
Hier wird eine einfache Methode zum Aktualisieren aller Statistikobjekte einer Tabelle gezeigt.

```
UPDATE STATISTICS [schema_name].[table_name];
```

Beispiel:

```
UPDATE STATISTICS dbo.table1;
```

Diese Anweisung ist einfach zu verwenden. Bedenken Sie, dass hiermit alle Statistiken der Tabelle aktualisiert werden, sodass unter Umständen mehr Arbeit als erforderlich erledigt wird. Wenn die Leistung kein Problem darstellt, ist dies auf jeden Fall die einfachste und umfassendste Möglichkeit sicherzustellen, dass die Statistiken aktuell sind.

> [AZURE.NOTE]Beim Aktualisieren aller Statistiken einer Tabelle führt SQL Data Warehouse einen Scan durch, um für jede Statistik Stichproben der Tabelle zu nehmen. Wenn die Tabelle groß ist und viele Spalten und Statistiken enthält, kann es effizienter sein, je nach Bedarf einzelne Spalten zu aktualisieren.

Eine Implementierung einer `UPDATE STATISTICS`-Prozedur wird im Artikel [Temporäre Tabellen] beschrieben. Die Implementierungsmethode unterscheidet sich etwas von der obigen `CREATE STATISTICS`-Prozedur, aber das Endergebnis ist identisch.

Die vollständige Syntax finden Sie unter [Aktualisieren von Statistiken][] auf der MSDN-Website.

## Statistikmetadaten
Es gibt mehrere Systemsichten und -funktionen, die Sie zum Suchen nach Informationen zu Statistiken verwenden können. Beispielsweise können Sie sehen, ob ein Statistikobjekt veraltet ist, indem Sie die stats-date-Funktion verwenden. Damit können Sie anzeigen, wann Statistiken zuletzt erstellt oder aktualisiert wurden.

### Katalogsichten für Statistiken
Diese Systemsichten enthalten Informationen zu Statistiken:

| Katalogsicht | Beschreibung |
| :----------- | :---------- |
| [sys.columns][] | Eine Zeile für jede Spalte. |
| [sys.objects][] | Eine Zeile für jedes Objekt in der Datenbank. | |
| [sys.schemas][] | Eine Zeile für jedes Schema in der Datenbank. | |
| [sys.stats][] | Eine Zeile für jedes Statistikobjekt. |
| [sys.stats_columns][] | Eine Zeile für jede Spalte im Statistikobjekt. Eine Verknüpfung zurück zu sys.columns. |
| [sys.tables][] | Eine Zeile für jede Tabelle (enthält externe Tabellen). |
| [sys.table_types][] | Eine Zeile für jeden Datentyp. |


### Systemfunktionen für Statistiken
Diese Systemfunktionen sind nützlich für die Arbeit mit Statistiken:

| Systemfunktion | Beschreibung |
| :-------------- | :---------- |
| [STATS_DATE][] | Datum, an dem das Statistikobjekt zuletzt aktualisiert wurde. |
| [DBCC SHOW_STATISTICS][] | Stellt Zusammenfassungsebenen und ausführliche Informationen zur Verteilung der Werte gemäß Statistikobjekt bereit. |

### Kombinieren von Statistikspalten und -funktionen zu einer Sicht

In dieser Sicht werden Spalten, die sich auf Statistiken beziehen, und Ergebnisse aus der [STATS_DATE()][]-Funktion zusammengefasst.

```
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1 
AND     sts.[user_created] = 1
;
```

## DBCC SHOW_STATISTICS()-Beispiele

Mit DBCC SHOW_STATISTICS() werden die Daten angezeigt, die in einem Statistikobjekt enthalten sind. Diese Daten bestehen aus drei Teilen.

1. Header
2. Dichtevektor
3. Histogramm

Dies sind die Headermetadaten zur Statistik. Im Histogramm wird die Verteilung der Werte in der ersten Schlüsselspalte des Statistikobjekts angezeigt. Der Dichtevektor misst die spaltenübergreifende Korrelation. SQLDW berechnet Kardinalitätsschätzungen anhand der Daten im Statistikobjekt.

### Anzeigen von Header, Dichte und Histogramm

In diesem einfachen Beispiel werden alle drei Teile eines Statistikobjekts angezeigt.

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Beispiel:

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### Anzeigen eines oder mehrerer Teile von DBCC SHOW_STATISTICS();

Wenn Sie nur bestimmte Teile anzeigen möchten, verwenden Sie die `WITH`-Klausel und geben an, welche Teile dies sein sollen:

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Beispiel:

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## DBCC SHOW_STATISTICS()-Unterschiede
DBCC SHOW_STATISTICS() ist im Vergleich zu SQL Server strenger in SQL Data Warehouse implementiert.

1. Nicht dokumentierte Funktionen werden nicht unterstützt.
- Verwendung von Stats_stream nicht möglich
- Ergebnisse für bestimmte Teilmengen von Statistikdaten können nicht verknüpft werden, z. B. (STAT_HEADER JOIN DENSITY_VECTOR)
2. NO_INFOMSGS kann für die Meldungsunterdrückung nicht festgelegt werden
3. Eckige Klammern um Namen von Statistiken können nicht verwendet werden
4. Spaltennamen können nicht zum Identifizieren von Statistikobjekten verwendet werden
5. Benutzerdefinierter Fehler 2767 wird nicht unterstützt


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [SQL Data Warehouse-Entwicklungsübersicht][].

<!--Image references-->

<!--Link references--In actual articles, you only need a single period before the slash.-->
[SQL Data Warehouse-Entwicklungsübersicht]: ./sql-data-warehouse-overview-develop/
[Temporäre Tabellen]: ./sql-data-warehouse-develop-temporary-tables/

<!-- External Links -->
[Kardinalitätsschätzung]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]: https://msdn.microsoft.com/library/ms174384.aspx
[Statistiken]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[Aktualisieren von Statistiken]: https://msdn.microsoft.com/library/ms187348.aspx

<!---HONumber=July15_HO4-->