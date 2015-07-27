<properties
   pageTitle="Migrieren von SQL-Code nach SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Migration des SQL-Codes in Azure SQL Data Warehouse zum Entwickeln von Lösungen."
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
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Migrieren von SQL-Code nach SQL Data Warehouse

Um sicherzustellen, dass der Code mit SQL Data Warehouse kompatibel ist, müssen Sie sehr wahrscheinlich Änderungen an der Codebasis vornehmen. Mit einigen SQL Data Warehouse-Funktionen kann zudem die Leistung erheblich verbessert werden, da sie speziell für die direkte verteilte Ausführung entwickelt wurden. Um jedoch eine gleichbleibende Leistung und Skalierung zu gewährleisten, sind einige Funktionen auch nicht verfügbar.

## Transact-SQL-Codeänderungen

Die folgende Aufstellung enthält die wichtigsten Funktionen, die in Azure SQL Data Warehouse nicht unterstützt werden:

- ANSI-Joins bei Aktualisierungen
- ANSI-Joins bei Löschvorgängen
- MERGE-Anweisung
- Datenbankübergreifende Verknüpfungen
- [PIVOT- und UNPIVOT-Anweisungen][]
- [Cursor][]
- [SELECT..INTO][]
- INSERT..EXEC
- OUTPUT-Klausel
- Benutzerdefinierte Inlinefunktionen
- Funktionen mit mehreren Anweisungen
- Rekursive allgemeine Tabellenausdrücke (CTE)
- Aktualisierungen über allgemeine Tabellenausdrücke
- CLR-Funktionen und -Prozeduren
- $partition-Funktion
- Tabellenvariablen
- Parameter für Tabellenwerte
- Verteilte Transaktionen
- Commit- oder Rollback-Vorgänge
- SAVE TRANSACTION
- Ausführungskontexte (EXECUTE AS)
- [GROUP BY-Klausel mit ROLLUP-, CUBE- oder GROUPING SETS-Option][]
- [Über 8 Schachtelungsebenen][]
- [Aktualisieren über Sichten][]
- [Verwenden der SELECT-Anweisung zur Variablenzuweisung][]
- [Kein MAX-Datentyp für dynamische SQL-Zeichenfolgen][]

Glücklicherweise können die meisten dieser Einschränkungen umgangen werden. Die entsprechenden Erläuterungen finden Sie in den jeweiligen oben referenzierten Artikeln.

Es werden auch einige Systemfunktionen nicht unterstützt. Zu den wichtigsten Funktionen, die normalerweise in Data Warehousing verwendet, gehören u. a.:

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Wieder können viele dieser Probleme umgangen werden.

Der folgende Code ist beispielsweise eine Alternativlösung zum Abrufen von @@ROWCOUNT-Informationen:

```
SELECT  SUM(row_count) AS row_count 
FROM    sys.dm_pdw_sql_requests 
WHERE   row_count <> -1 
AND     request_id IN 
                    (   SELECT TOP 1    request_id 
                        FROM            sys.dm_pdw_exec_requests 
                        WHERE           session_id = SESSION_ID() 
                        ORDER BY end_time DESC
                    )
;
``` 

## Nächste Schritte
Hinweise zur Entwicklung des Codes finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[PIVOT- und UNPIVOT-Anweisungen]: sql-data-warehouse-develop-pivot-unpivot.md
[Cursor]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[GROUP BY-Klausel mit ROLLUP-, CUBE- oder GROUPING SETS-Option]: sql-data-warehouse-develop-group-by-options.md
[Über 8 Schachtelungsebenen]: sql-data-warehouse-develop-transactions.md
[Aktualisieren über Sichten]: sql-data-warehouse-develop-views.md
[Verwenden der SELECT-Anweisung zur Variablenzuweisung]: sql-data-warehouse-develop-variable-assignment.md
[Kein MAX-Datentyp für dynamische SQL-Zeichenfolgen]: sql-data-warehouse-develop-dynamic-sql.md
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->