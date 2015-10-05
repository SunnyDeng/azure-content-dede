<properties
   pageTitle="Zuweisen von Variablen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Zuweisen von Transact-SQL-Variablen in Azure SQL Data Warehouse für die Entwicklung von Lösungen."
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

# Zuweisen von Variablen in SQL Data Warehouse
Variablen werden in SQL Data Warehouse mit der `DECLARE`-Anweisung oder der `SET`-Anweisung festgelegt.

Im Folgenden sind Möglichkeiten zum Festlegen eines Variablenwerts aufgeführt:

## Festlegen von Variablen mit DECLARE

Das Initialisieren von Variablen mit DECLARE ist eine der flexibelsten Möglichkeiten zum Festlegen eines Variablenwerts in SQL Data Warehouse.

```
DECLARE @v  int = 0
;
```

Sie können mit DECLARE auch mehrere Variablen gleichzeitig festlegen. Sie können `SELECT` und `UPDATE` nicht für Folgendes verwenden:

```
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Eine Variable kann nicht in der gleichen DECLARE-Anweisung initialisiert und verwendet werden. Im folgenden Beispiel, das **nicht** zulässig ist, wird @p1 in der gleichen DECLARE-Anweisung sowohl initialisiert als auch verwendet. Dies führt zu einem Fehler.

```
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## Festlegen von Werten mit SET
SET ist eine sehr allgemeine Methode zum Festlegen einer einzelnen Variable.

Alle folgenden Beispiele sind gültige Möglichkeiten zum Festlegen einer Variable mit SET:

```
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Sie können mit SET nur jeweils eine einzige Variable festlegen. Wie oben zu sehen ist, sind zusammengesetzte Operatoren jedoch zulässig.

## Einschränkungen
Sie können nicht SELECT oder UPDATE für die Zuweisung von Variablen verwenden.


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Sept15_HO4-->