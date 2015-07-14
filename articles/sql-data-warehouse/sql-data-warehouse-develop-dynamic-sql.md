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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Dynamischer SQL-Code in SQL Data Warehouse
Beim Entwickeln von Anwendungscode für SQL Data Warehouse müssen Sie unter Umständen dynamischen SQL-Code verwenden, um flexible, generische und modulare Lösungen bereitstellen zu können. Allerdings unterstützt SQL Data Warehouse derzeit keine Blob-Datentypen. Dadurch kann die Größe Ihrer Zeichenfolgen verringert werden, weil Blob-Typen sowohl varchar(max)- als auch nvarchar(max)-Typen umfassen. Unter Umständen finden Sie heraus, dass Sie diese Typen im Anwendungscode verwendet haben, als Sie sehr große Zeichenfolgen mit dynamischem SQL-Code erstellt haben, den Sie ausführen müssen.

In diesen Fällen müssen Sie den Code in Segmente unterteilen und stattdessen die EXEC-Anweisung verwenden.

Hier ist ein vereinfachtes Beispiel angegeben:

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Wenn die Zeichenfolge nicht besonders lang ist, können Sie [sp_executesql][] normal verwenden.


## Nächste Schritte
Weitere Tipps zur Entwicklung finden Sie unter [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/de-de/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->