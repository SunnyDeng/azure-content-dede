<properties
   pageTitle="Schleifen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zu Transact-SQL-Schleifen und zum Ersetzen von Cursorn in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
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

# Schleifen in SQL Data Warehouse
SQL Data Warehouse unterstützt die [WHILE][]-Schleife für die wiederholte Ausführung von Anweisungsblöcken. Die Schleife wird so lange ausgeführt, wie die angegebenen Bedingungen wahr sind oder bis die Schleife im Code mit dem Schlüsselwort `BREAK` gezielt beendet wird. Schleifen sind besonders nützlich, um im SQL-Code definierte Cursor zu ersetzen. Glücklicherweise sind fast alle Cursor, die per SQL-Code geschrieben werden, Nur-Lese-Cursor für den schnellen Vorlauf. Daher sind [WHILE]-Schleifen eine hervorragende Alternative, wenn Sie einen Cursor ersetzen müssen.

## Nutzen von Schleifen und Ersetzen von Cursorn in SQL Data Warehouse
Stellen Sie sich vorher aber unbedingt die folgende Frage: „Kann dieser Cursor so umgeschrieben werden, dass satzbasierte Vorgänge verwendet werden?“ In vielen Fällen lautet die Antwort „Ja“. Und häufig ist dies auch die beste Vorgehensweise. Ein satzbasierter Vorgang wird oft erheblich schneller als ein Durchlauf Zeile für Zeile durchgeführt.

Nur-Lese-Cursor für den schnellen Vorlauf können leicht durch ein Schleifenkonstrukt ersetzt werden. Unten ist ein einfaches Beispiel zur Veranschaulichung dieses Ansatzes angegeben. Im Codebeispiel wird die Statistik für jede Tabelle der Datenbank aktualisiert. Indem die Tabellen mit der Schleife durchlaufen werden, kann jeder Befehl der Reihe nach ausgeführt werden.

Erstellen Sie zuerst eine temporäre Tabelle mit einer eindeutigen Zeilenzahl zum Identifizieren der einzelnen Anweisungen:
  
```
CREATE TABLE #tbl 
WITH 
(   LOCATION     = USER_DB
,   DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) ) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Initialisieren Sie als Nächstes die Variablen, die zum Durchführen des Schleifenvorgangs erforderlich sind:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Jetzt können Sie die Anweisungen per Schleife durchlaufen und einzeln ausführen:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Abschließend können Sie die temporäre Tabelle verwerfen, die Sie im ersten Schritt erstellt haben.

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->

<!---HONumber=July15_HO4-->