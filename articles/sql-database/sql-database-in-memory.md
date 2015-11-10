<properties
	pageTitle="Erste Schritte mit SQL In-Memory | Microsoft Azure"
	description="SQL In-Memory-Technologien verbessern die Leistung von Transaktions- und Analyseworkloads erheblich. Erfahren Sie, wie Sie von diesen Technologien profitieren."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Erste Schritte mit In-Memory (Vorschau)


SQL In-Memory-Technologien verbessern die Leistung von Transaktions- und Analyseworkloads erheblich. Je nach Workload können Sie mit In-Memory OLTP einen bis zu 30-mal höheren Transaktionsdurchsatz und mit In-Memory Analytics eine bis zu 100-mal höhere Abfrageleistung erzielen. Mit Real-Time Analytics kombinieren Sie diese Technologien, um geschäftliche Erkenntnisse in Echtzeit anhand von Daten aus dem laufenden Betrieb zu gewinnen.

In-Memory Analytics ist in der Azure-Datenbank allgemein verfügbar. In-Memory OLTP und Real-Time Operational Analytics befinden sich für Premium Azure SQL-Datenbanken in der Vorschau.


## Erste Schritte

Testen Sie In-Memory OLTP für Transaktionsworkloads:


- [Installieren Sie das Beispiel für In-Memory OLTP](#install-the-in-memory-oltp-sample).
- [Verwenden Sie In-Memory OLTP in einer vorhandenen Azure SQL-Anwendung.](sql-database-in-memory-oltp-migration.md)
- [Überwachen Sie In-Memory-Speicher](sql-database-in-memory-oltp-monitoring.md).


Testen Sie In-Memory Analytics für Analyseworkloads:

- [Installieren Sie das Beispiel für In-Memory Analytics](#install-the-in-memory-analytics-sample).
- Informieren Sie sich in MSDN über [Columnstore-Indizes](https://msdn.microsoft.com/library/gg492088.aspx).


## Installieren des Beispiels für In-Memory OLTP

Sie können die Beispieldatenbank „AdventureWorksLT [V12]“ mit wenigen Klicks im Azure-Vorschauportal erstellen. Die folgenden Schritte erläutern, wie Sie Ihre AdventureWorksLT-Datenbank mit Tabellen und systemintern kompilierten gespeicherten Prozeduren füllen, um die In-Memory OLTP-Objekte zu veranschaulichen.


1. Erstellen Sie im [Azure-Vorschauportal](https://portal.azure.com/) eine Premium-Datenbank auf einem V12-Server. Legen Sie die AdventureWorksLT [V12]-Beispieldatenbank als Quelle fest.
 - Detaillierte Anleitungen zu diesem Schritt finden Sie in [diesem Artikel](sql-database-get-started.md).

2. Stellen Sie eine Verbindung mit [SQL Server Management Studio (SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) oder einem ähnlichen Hilfsprogramm her.

3. Kopieren Sie das [In-Memory OLTP-Skript](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) in die Zwischenablage.
 - Das Skript erstellt die erforderlichen In-Memory-Objekte in der in Schritt 1 erstellten Beispieldatenbank „AdventureWorksLT“.

4. Fügen Sie das Transact-SQL-Skript in „SSMS.exe“ ein, und führen Sie es aus.



&nbsp;

Das Beispiel enthält die folgenden speicheroptimierten Tabellen:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

Untersuchen Sie die speicheroptimierten Tabellen im Objekt-Explorer oder durch Abfragen der Katalogsichten.

Beispiel:


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


Genauso kann auch die systemintern kompilierte gespeicherte Prozedur „SalesLT.usp\_InsertSalesOrder\_inmem“ im Objekt-Explorer oder durch Abfragen der Katalogsichten untersucht werden.

Beispiel:


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## Ausführen der Beispielworkload

Verwenden Sie die gespeicherten Prozeduren „SalesLT.usp\_InsertSalesOrder\_inmem“ und „SalesLT.usp\_InsertSalesOrder\_ondisk“, um die Einfügeleistung für speicheroptimierte Tabellen mit der Leistung für datenträgerbasierte Tabellen zu vergleichen.

Es empfiehlt sich, die Workload unter Verwendung mehrerer gleichzeitiger Clientverbindungen über eine Anwendung auszuführen, die sich in der gleichen Azure-Region befindet wie die Beispieldatenbank.

### Einfügen eines Beispielverkaufsauftrags

Das folgende Skript fügt einen Beispielauftrag mit fünf Positionen in die speicheroptimierten Tabellen „SalesLT.SalesOrderHeader\_inmem“ und „SalesLT.SalesOrderDetail\_inmem“ ein:


```
		DECLARE
			@i int = 0,
			@od SalesLT.SalesOrderDetailType_inmem,
			@SalesOrderID int,
			@DueDate datetime2 = sysdatetime(),
			@CustomerID int = rand() * 8000,
			@BillToAddressID int = rand() * 10000,
			@ShipToAddressID int = rand() * 10000;

		INSERT INTO @od
		SELECT OrderQty, ProductID
		FROM Demo.DemoSalesOrderDetailSeed
		WHERE OrderID= cast((rand()*60) as int);

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### Ausführen einer Beispielworkload für die Belastung

Um eine Beispielworkload für die Belastung auszuführen, erstellen Sie in der gleichen Region, in der sich Ihre Beispieldatenbank befindet, einen [virtuellen Azure-Computer](https://azure.microsoft.com/documentation/services/virtual-machines/). Verwenden Sie das Befehlszeilentool „ostress“, um die Workload auszuführen. Anleitungen zum [Installieren und Ausführen von „ostress“](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx) finden Sie auf MSDN.

Der folgende Befehl fügt bei Ausführung über die RML-Eingabeaufforderung eine Million Verkaufsaufträge mit jeweils fünf Positionen in speicheroptimierte Tabellen ein und verwendet dabei 100 gleichzeitige Verbindungen:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


Stellen Sie sicher, dass Sie <servername> durch den Namen Ihres Servers, <database> durch den Namen Ihrer Datenbank und <login> und <password> durch Ihre Anmeldeinformationen ersetzen.

Um die Einfügeleistung von speicheroptimierten Tabellen mit der von herkömmlichen datenträgerbasierten Tabellen zu vergleichen, verwenden Sie den folgenden Befehl, der die gleiche Million Verkaufsaufträge in datenträgerbasierte Tabellen einfügt:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


Tests haben für diese Workload und bei Ausführung von „ostress“ auf einem virtuellen Computer, der sich in der gleichen Azure-Region befindet wie die Datenbank, eine 9-mal höhere Leistung bei speicheroptimierten Tabellen als bei datenträgerbasierten Tabellen ergeben.

Stellen Sie sicher, dass Sie das Beispiel nach jedem Test zurücksetzen, um zu verhindern, dass kein In-Memory-Speicher mehr zur Verfügung steht. Führen Sie die folgende T-SQL-Anweisung in Ihrer Datenbank aus. Ein einziger Testlauf, bei dem eine Million Verkaufsaufträge eingefügt werden, führt zu einem Datenvolumen von mehr als 500 MB in speicheroptimierten Tabellen.


```
EXECUTE Demo.usp_DemoReset;
```


## Installieren des In-Memory Analytics-Beispiels

Erstellen Sie **zuerst** eine neue Azure SQL-Datenbank.

- Wählen Sie eine beliebige Premium Edition aus (Premium ist für Columnstore erforderlich).
- Stellen Sie sicher, dass Sie die Datenbank des Beispiels erstellen.

- Nennen Sie die Datenbank der Einfachheit halber „AdventureworksLT“.



Stellen Sie **anschließend** über [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx), September 2015 Preview oder höher, eine Verbindung mit der Azure SQL-Datenbank her.


- Führen Sie das Setupskript [sql\_in-memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) aus.


- Erstellen Sie das für die Dimensions- und Faktentabellen erforderliche Schema, indem Sie das Skript ausführen. Das Skript erstellt zwei Faktentabellen mit jeweils ca. 3,5 Millionen Zeilen.


- „FactResellerSales\_CCI“ verfügt über eine Columnstore-Tabelle.


- „FactResellerSalesXL\_PageCompressed“ ist eine äquivalente B-Strukturtabelle mit Seitenkomprimierung. **Hinweis:** Die Ausführung des Skripts und Generierung der Daten kann bis zu 15 Minuten dauern.


**Zum Schluss** führen Sie die Demoabfragen in der Datei [clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) aus und untersuchen das Feature.

## Weitere Informationen zu In-Memory OLTP

[In-Memory OLTP (In-Memory Optimization)](https://msdn.microsoft.com/library/dn133186.aspx)

Das [Whitepaper zu allgemeinen Workloadmustern und Überlegungen zur Migration](https://msdn.microsoft.com/library/dn673538.aspx) beschreibt Workloadmuster, bei denen In-Memory OLTP im Allgemeinen erhebliche Leistungssteigerungen bietet.

## Überlegungen zur Vorschau

In-Memory OLTP wird **nur** für Datenbanken der Premium Edition unterstützt.

In-Memory OLTP wird nur in neu erstellten Datenbanken unterstützt. Es wird nicht in Datenbanken unterstützt, die mithilfe die Kopier- oder Wiederherstellungsfunktion basierend auf einer vorhandenen Datenbank erstellt wurden. Nachdem Sie die neue Datenbank erstellt haben, können Sie diese jedoch kopieren oder wiederherstellen – die vollständige Funktionalität von In-Memory OLTP bleibt dabei erhalten.

Um zu überprüfen, ob In-Memory OLTP in einer bestimmten Datenbank unterstützt wird, führen Sie die folgende Abfrage aus:


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


Wenn die Abfrage **1** zurückgibt, wird In-Memory OLTP in dieser Datenbank sowie in allen auf dieser Datenbank basierenden Datenbankkopien und Datenbankwiederherstellungen unterstützt.

Wenn eine Datenbank eine der folgenden Arten von Objekten oder Typen enthält, wird eine Änderung der Dienstebene der Datenbank zu „Basic“ oder „Standard“ nicht unterstützt. Um ein Downgrade der Datenbank auszuführen, müssen Sie die Objekte zuerst löschen.

- Speicheroptimierte Tabellen
- Speicheroptimierte Tabellentypen
- Systemintern kompilierte Module, die In-Memory OLTP mit Datenbanken in elastischen Pools verwenden, werden nicht unterstützt.

Die Verwendung von In-Memory OLTP mit SQL Data Warehouse wird nicht unterstützt.

Der Abfragespeicher erfasst keine Abfragen innerhalb von systemintern kompilierten Modulen.

Einige Transact-SQL-Funktionen werden bei In-Memory OLTP nicht unterstützt. Informationen hierzu finden Sie unter [Transact-SQL-Unterstützung für In-Memory OLTP](https://msdn.microsoft.com/library/dn133180.aspx)

## Unterstützte Tools

[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx), September 2015 Preview oder höher.

[SQL Server Data Tools Preview (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx), September 2015 Preview oder höher.

## Zusätzliche Ressourcen

[Informieren Sie sich über In-Memory OLTP auf MSDN.](https://msdn.microsoft.com/library/dn133186.aspx)

[Informieren Sie sich über In-Memory Analytics (Columnstore) auf MSDN.](https://msdn.microsoft.com/library/gg492088.aspx)

[Informieren Sie sich über Real-Time Operational Analytics auf MSDN.](https://msdn.microsoft.com/library/dn817827.aspx)

Das [Whitepaper zu allgemeinen Workloadmustern und Überlegungen zur Migration](https://msdn.microsoft.com/library/dn673538.aspx) beschreibt Workloadmuster, bei denen In-Memory OLTP im Allgemeinen erhebliche Leistungssteigerungen bietet.

## Nächste Schritte

[Verwenden Sie In-Memory OLTP in einer vorhandenen Azure SQL-Anwendung](sql-database-in-memory-oltp-migration.md).

[Überwachen Sie In-Memory-Speicher](sql-database-in-memory-oltp-monitoring.md) für In-Memory OLTP.

<!---HONumber=Nov15_HO2-->