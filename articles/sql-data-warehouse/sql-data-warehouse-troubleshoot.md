<properties
   pageTitle="Problembehandlung | Microsoft Azure"
   description="Problembehandlung bei SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="twounder"/>

# Problembehandlung
In diesem Thema sind einige der Probleme aufgeführt, die bei der Verwendung von Azure SQL Data Warehouse häufiger auftreten.

## Konnektivität
Die Herstellung einer Verbindung mit Azure SQL Data Warehouse kann aus mehreren Gründen fehlschlagen:

- Es sind keine Firewallregeln festgelegt.
- Es werden nicht unterstützte Tools oder Protokolle verwendet.

### Firewallregeln
Azure SQL-Datenbanken sind durch Firewalls auf Server- und Datenbankebene geschützt, um sicherzustellen, dass nur bekannte IP-Adressen Zugriff auf Datenbanken haben. Die Firewalls sind standardmäßig sicher. Das bedeutet, dass Sie den Zugriff für Ihre IP-Adresse konfigurieren müssen, damit Sie eine Verbindung herstellen können.

Um Ihre Firewall für den Zugriff zu konfigurieren, führen Sie die im Abschnitt [Konfigurieren des Serverfirewall-Zugriffs für Ihre Client-IP-Adresse](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip) auf der Seite [Erstellen eines SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) beschriebenen Schritte aus.

### Nicht unterstützte Tools oder Protokolle
SQL Data Warehouse unterstützt [Visual Studio 2013/2015](sql-data-warehouse-get-started-connect.md) als Entwicklungsumgebungen und [SQL Server Native Client 10/11 (ODBC)](https://msdn.microsoft.com/library/ms131415.aspx) für Clientverbindungen.

Weitere Informationen finden Sie auf der Seite [Verbinden](sql-data-warehouse-get-started-connect.md).

## Abfrageleistung
SQL Data Warehouse verwendet allgemeine SQL Server-Konstrukte zum Ausführen von Abfragen, einschließlich Statistiken. [Statistiken](sql-data-warehouse-develop-statistics.md) sind Objekte, die Informationen zum Wertebereich und zur Häufigkeit von Werten in einer Datenbankspalte enthalten. Das Abfragemodul verwendet diese Statistiken, um die Abfrageausführung zu optimieren und die Abfrageleistung zu verbessern. Sie können die folgende Abfrage verwenden, um den Zeitpunkt zu ermitteln, zu dem die Statistiken zuletzt aktualisiert wurden.

```
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1 
	AND st.[user_created] = 1;
```

Weitere Informationen finden Sie auf der Seite [Statistiken](sql-data-warehouse-develop-statistics.md).

## Zentrale Leistungskonzepte

Informationen zu zusätzlichen zentralen Leistung- und Skalierbarkeitskonzepten finden Sie in den folgenden Artikeln:

- [Leistung und Skalierbarkeit][]
- [Parallelitätsmodell][]
- [Entwerfen von Tabellen][]
- [Auswählen eines Hashverteilungsschlüssels für die Tabelle][]
- [Statistiken zum Verbessern der Leistung][]

## Nächste Schritte
Anleitungen zum Erstellen Ihrer SQL Data Warehouse-Lösung finden Sie im Artikel [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->

[Leistung und Skalierbarkeit]: sql-data-warehouse-performance-scale.md
[Parallelitätsmodell]: sql-data-warehouse-develop-concurrency.md
[Entwerfen von Tabellen]: sql-data-warehouse-develop-table-design.md
[Auswählen eines Hashverteilungsschlüssels für die Tabelle]: sql-data-warehouse-develop-hash-distribution-key
[Statistiken zum Verbessern der Leistung]: sql-data-warehouse-develop-statistics.md
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0107_2016-->