<properties
   pageTitle="Dynamischer SQL-Code in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zur Verwendung von dynamischem SQL-Code in Azure SQL Data Warehouse für die Entwickelung von Lösungen"
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
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Dynamischer SQL-Code in SQL Data Warehouse
Beim Entwickeln von Anwendungscode für SQL Data Warehouse müssen Sie unter Umständen dynamischen SQL-Code verwenden, um flexible, generische und modulare Lösungen bereitstellen zu können. SQL Data Warehouse unterstützt derzeit keine BLOB-Datentypen. Dadurch kann die Größe Ihrer Zeichenfolgen verringert werden, weil Blob-Typen sowohl varchar(max)- als auch nvarchar(max)-Typen umfassen. Wenn Sie diese Typen im Anwendungscode verwendet haben und sehr große Zeichenfolgen erstellen, müssen Sie den Code in Segmente unterteilen und stattdessen die EXEC-Anweisung verwenden.

Ein einfaches Beispiel:

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Wenn die Zeichenfolge kurz ist, können Sie [sp\_executesql][] normal verwenden.

> [AZURE.NOTE]Anweisungen, die als dynamisches SQL ausgeführt werden, unterliegen weiterhin allen TSQL-Validierungsregeln.

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp\_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->