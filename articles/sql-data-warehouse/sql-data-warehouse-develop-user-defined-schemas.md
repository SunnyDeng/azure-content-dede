<properties
   pageTitle="Benutzerdefinierte Schemas in SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Verwendung von Transact-SQL-Schemas in Azure SQL Data Warehouse zum Entwickeln von Lösungen."
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

# Benutzerdefinierte Schemas in SQL Data Warehouse

Herkömmliche Data Warehouses verwenden häufig separate Datenbanken, um Anwendungsgrenzen auf Basis von Workload, Domäne oder Sicherheit zu erstellen. Ein herkömmliches Data Warehouse in SQL Server kann z. B. eine Stagingdatenbank, eine Data Warehouse-Datenbank und einige Data Mart-Datenbanken enthalten. In dieser Topologie funktioniert jede Datenbank als Workload und Sicherheitsgrenze in der Architektur.

Im Gegensatz dazu werden mit SQL Data Warehouse die gesamten Data Warehouse-Workloads innerhalb einer Datenbank ausgeführt. Datenbankübergreifende Verknüpfungen sind nicht zulässig. SQL Data Warehouse erwartet daher, dass alle vom Warehouse verwendeten Tabellen innerhalb der einen Datenbank gespeichert werden.

> [AZURE.NOTE]SQL Data Warehouse unterstützt keine datenbankübergreifenden Abfragen. Data Warehouse-Implementierungen, die diese Funktion nutzen, müssen daher überarbeitet werden.

## Recommendations 

Hierbei handelt es sich um Empfehlungen für die Konsolidierung der Workload-, Sicherheits-, Domänen- und Funktionsgrenzen mithilfe von benutzerdefinierten Schemas.

1. Verwenden Sie eine SQL Data Warehouse-Datenbank zum Ausführen Ihrer gesamten Data Warehouse-Workloads.
2. Konsolidieren Sie Ihre vorhandene Data Warehouse-Umgebung zur Verwendung einer SQL Data Warehouse-Datenbank.
3. Nutzen Sie **benutzerdefinierte Schemas**, um die Grenze bereitzustellen, die zuvor mithilfe von Datenbanken implementiert wurde.

Wenn zuvor keine benutzerdefinierten Schemas verwendet wurden, können Sie von Grund auf neu beginnen. Verwenden Sie einfach den alten Datenbanknamen als Grundlage für Ihre benutzerdefinierten Schemas in der SQL Data Warehouse-Datenbank.

Wenn bereits Schemas verwendet wurden, stehen Ihnen einige Optionen zur Verfügung:

1. Entfernen Sie die älteren Schemanamen, und beginnen Sie von vorn.
2. Behalten Sie die älteren Schemanamen bei, indem Sie diese vorab an den Tabellennamen anhängen.
3. Behalten Sie die älteren Schemanamen bei, indem Sie Sichten auf die Tabelle in einem zusätzlichen Schema zum Neuerstellen der alten Schemastruktur implementieren.

> [AZURE.NOTE]Option 3 mag auf den ersten Blick die attraktivste Option sein. Bei genauerem Hinsehen treten jedoch Probleme zutage. Sichten werden nur in SQL Data Warehouse gelesen. Jede Änderung von Daten oder Tabellen muss für die Basistabelle ausgeführt werden. Option 3 führt außerdem eine Schicht von Sichten im System ein. Sie sollten dies besonders berücksichtigen, wenn Sie in Ihrer Architektur bereits Sichten verwenden.


### Beispiele:

1. Implementieren von benutzerdefinierten Schemas basierend auf Datenbanknamen

```
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

2. Behalten Sie die älteren Schemanamen bei, indem Sie diese vorab an den Tabellennamen anhängen. Verwenden Sie Schemas für die Workload-Grenze.

```
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

3. Beibehalten von älteren Schemanamen mit Sichten

```
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey	BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey	BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM	[edw].customer
;
```

> [AZURE.NOTE]Jede Änderung in der Schemastrategie erfordert eine Überprüfung des Sicherheitsmodells für die Datenbank. In vielen Fällen können Sie das Sicherheitsmodell vereinfachen, indem Sie Berechtigungen auf Schemaebene zuweisen. Wenn präzisere Berechtigungen erforderlich sind, können Sie Datenbankrollen verwenden.

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Oct15_HO1-->