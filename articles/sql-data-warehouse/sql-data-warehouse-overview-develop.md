<properties
   pageTitle="Entwurfsentscheidungen und Programmiertechniken für SQL Data Warehouse-Entwicklung | Microsoft Azure"
   description="Entwicklungskonzepte, Entwurfsentscheidungen, Empfehlungen und Programmiertechniken für SQL Data Warehouse."
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
   ms.date="03/03/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Entwurfsentscheidungen und Programmiertechniken für SQL Data Warehouse

Lesen Sie diese Entwicklungsartikel, um ein besseres Verständnis der wesentlichen Entwurfsentscheidungen, der Empfehlungen und der Programmiertechniken für SQL Data Warehouse zu erlangen.

## Wesentliche Entwurfsentscheidungen
In den folgenden Artikeln werden einige der zentralen Konzepte und Entwurfsentscheidungen hervorgehoben, die Sie für die Entwicklung Ihres verteilten Data Warehouses mit SQL Data Warehouse verstehen müssen:

- [Verbindungen][]
- [Parallelität][]
- [Transaktionen][]
- [Benutzerdefinierte Schemas][]
- [Tabellenentwurf][]
- [Hashverteilungsschlüssel][]
- [Tabellenpartitionen][]
- [CTAS][]
- [Statistiken][]

## Entwicklungsempfehlungen und Programmiertechniken
In diesen Artikeln werden bestimmte Programmiertechniken, Tipps und Empfehlungen für die Entwicklung eines SQL Data Warehouse behandelt:

- [Gespeicherte Prozeduren][]
- [Bezeichnungen][]
- [Ansichten][]
- [Temporäre Tabellen][]
- [Dynamisches SQL][]
- [Schleifen][]
- [Umbenennen von Objekten][]
- [Pivotieren von Daten][]
- [Gruppierungsoptionen][]
- [Variablenzuweisung][]

## Nächste Schritte
Wenn Sie die Entwicklungsartikel durchgearbeitet haben, finden Sie auf der Seite [Transact-SQL-Referenz][] weitere Informationen zur unterstützten Syntax für SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[Parallelität]: sql-data-warehouse-develop-concurrency.md
[Verbindungen]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[Dynamisches SQL]: sql-data-warehouse-develop-dynamic-sql.md
[Gruppierungsoptionen]: sql-data-warehouse-develop-group-by-options.md
[Hashverteilungsschlüssel]: sql-data-warehouse-develop-hash-distribution-key.md
[Bezeichnungen]: sql-data-warehouse-develop-label.md
[Schleifen]: sql-data-warehouse-develop-loops.md
[Pivotieren von Daten]: sql-data-warehouse-develop-pivot-unpivot.md
[Umbenennen von Objekten]: sql-data-warehouse-develop-rename.md
[Statistiken]: sql-data-warehouse-develop-statistics.md
[Gespeicherte Prozeduren]: sql-data-warehouse-develop-stored-procedures.md
[Tabellenentwurf]: sql-data-warehouse-develop-table-design.md
[Tabellenpartitionen]: sql-data-warehouse-develop-table-partitions.md
[Temporäre Tabellen]: sql-data-warehouse-develop-temporary-tables.md
[Transaktionen]: sql-data-warehouse-develop-transactions.md
[Benutzerdefinierte Schemas]: sql-data-warehouse-develop-user-defined-schemas.md
[Variablenzuweisung]: sql-data-warehouse-develop-variable-assignment.md
[Ansichten]: sql-data-warehouse-develop-views.md

[Transact-SQL-Referenz]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->