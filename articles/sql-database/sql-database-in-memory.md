<properties
	pageTitle="Erste Schritte mit SQL In-Memory | Microsoft Azure"
	description="SQL In-Memory-Technologien verbessern die Leistung von Transaktions- und Analyseworkloads erheblich. Erfahren Sie, wie Sie von diesen Technologien profitieren."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="jodebrui"/>


# Erste Schritte mit In-Memory (Vorschau) in SQL-Datenbank

In-Memory-Features verbessern die Leistung bei Transaktions- und Analyseworkloads in den richtigen Situationen deutlich.

Im Mittelpunkt dieses Themas stehen zwei Demos, eine für In-Memory OLTP und eine für In-Memory Analytics. Jede der beiden Demos ist vollständig und enthält alle Schritte und den Code, den Sie zum Ausführen benötigen. Sie haben folgende Möglichkeiten:

- Verwenden des Codes, um Variationen zu testen und Abweichungen in den Leistungen zu ermitteln
- Lesen des Codes, um das Szenario zu verstehen und zu lernen, wie Sie In-Memory-Objekte erstellen und nutzen

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [Quick Start 1: In-Memory OLTP Technologies for Faster T-SQL Performance](http://msdn.microsoft.com/library/mt694156.aspx) (Schnellstart 1: In-Memory-OLTP-Technologien für höhere T-SQL-Leistung): Dieser Artikel unterstützt Sie ebenfalls bei den ersten Schritten.

#### In-Memory OLTP

In-Memory [OLTP](#install_oltp_manuallink) (Online Transaction Processing, Onlinetransaktionsverarbeitung) bietet folgende Features:

- Speicheroptimierte Tabellen
- Systemintern kompilierte gespeicherte Prozeduren


Eine speicheroptimierte Tabelle verfügt neben ihrer Standarddarstellung auf einer Festplatte auch über eine Darstellung ihrer selbst im aktiven Arbeitsspeicher. Geschäftstransaktionen für die Tabelle werden schneller ausgeführt, weil sie nur mit der Darstellung direkt interagieren, die sich im aktiven Arbeitsspeicher befindet.

Je nach Workload können Sie mithilfe von In-Memory OLTP einen bis um das 30-fache höheren Transaktionsdurchsatz erreichen.


Systemintern kompilierte gespeicherte Prozeduren erfordern weniger Computeranweisungen während der Laufzeit als herkömmliche gespeicherte Prozeduren, die interpretiert werden müssen. Wir haben festgestellt, dass die systeminterne Kompilierung die Zeitdauer gegenüber einer Kompilierung mit Interpretation auf ein Hundertstel verkürzt.


#### In-Memory Analytics 

In-Memory [Analytics](#install_analytics_manuallink) enthält dieses Feature:

- Columnstore-Indizes


Ein Columnstore-Index verbessert die Leistung von Abfrageworkloads durch eine außergewöhnliche Komprimierung von Daten.

In anderen Diensten sind Columnstore-Indizes zwingend speicheroptimiert. In der Azure SQL-Datenbank kann ein Columnstore-Index jedoch auf der Festplatte neben den herkömmlichen Tabellen existieren, die er indiziert.


#### Echtzeitanalysen

Für [Echtzeitanalysen](http://msdn.microsoft.com/library/dn817827.aspx) werden In-Memory OLTP und In-Memory Analytics kombiniert, um Folgendes zu erhalten:

- Geschäftsinformationen in Echtzeit basierend auf Daten aus dem laufenden Betrieb


#### Verfügbarkeit


Allgemeine Verfügbarkeit:

- [Columnstore-Indizes](http://msdn.microsoft.com/library/dn817827.aspx) auf *Festplatte*.


Vorschau:

- In-Memory OLTP
- In-Memory Analytics mit speicheroptimierten Columnstore-Indizes
- Real-Time Operational Analytics


Solange sich die In-Memory-Features in der Vorschau befinden, müssen einige Aspekte beachtet werden. Informationen dazu finden Sie [weiter unten in diesem Thema](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Die in der Vorschau befindlichen Features stehen nur für Azure SQL-Datenbanken im Tarif [*Premium*](sql-database-service-tiers.md) zur Verfügung, nicht für Datenbanken im Tarif „Standard“ oder „Basic“.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## A: Installieren des Beispiels für In-Memory OLTP

Sie können im [Azure-Portal](https://portal.azure.com/) die Beispieldatenbank „AdventureWorksLT [V12]“ mit wenigen Klicks erstellen. Anhand der Schritte in diesem Abschnitt wird erläutert, wie Sie anschließend Ihre AdventureWorksLT-Datenbank wie folgt erweitern können:

- In-Memory-Tabellen
- Systemintern kompilierte gespeicherte Prozedur


#### Installationsschritte

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com/) auf einem V12-Server eine Premium-Datenbank. Legen Sie die AdventureWorksLT [V12]-Beispieldatenbank als **Quelle** fest.
 - Detaillierte Anweisungen finden Sie unter [Erstellen Ihrer ersten Azure SQL-Datenbank](sql-database-get-started.md).

2. Stellen Sie eine Verbindung mit der Datenbank mit SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx) her.

3. Kopieren Sie das [In-Memory OLTP Transact-SQL-Skript](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) in die Zwischenablage.
 - Das T-SQL-Skript erstellt die erforderlichen In-Memory-Objekte in der in Schritt 1 erstellten Beispieldatenbank „AdventureWorksLT“.

4. Fügen Sie das T-SQL-Skript in SSMS ein, und führen Sie es aus.
 - Unerlässlich ist die `MEMORY_OPTIMIZED = ON`-Klausel in den CREATE TABLE-Anweisungen wie im folgenden Beispiel:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### Fehler 40536


Wenn bei Ausführen des T-SQL-Skripts der Fehler 40536 angezeigt wird, führen Sie das folgende T-SQL-Skript aus, um zu prüfen, ob die Datenbank In-Memory unterstützt:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Das Ergebnis **0** bedeutet, dass In-Memory nicht unterstützt wird. 1 bedeutet Unterstützung. So analysieren Sie das Problem:

- Stellen Sie sicher, dass die Datenbank erstellt wurde, nachdem die In-Memory OLTP-Features für die Vorschau aktiviert wurden.
- Stellen Sie sicher, dass die Datenbank dem Premium-Tarif unterliegt.


#### Informationen zu speicheroptimierten Elementen

**Tabellen**: Das Beispiel enthält die folgenden speicheroptimierten Tabellen:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Sie können im **Objekt-Explorer** speicheroptimierte Tabellen in SSMS wie folgt untersuchen:

- Klicken Sie mit der rechten Maustaste auf **Tabellen** > **Filter** > **Filtereinstellungen** > **Speicheroptimiert** gleich 1.


Oder Sie können die Katalogsichten abfragen wie z. B.:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**Systemintern kompilierte gespeicherte Prozedur**: „SalesLT.usp\_InsertSalesOrder\_inmem“ kann über eine Katalogsichtabfrage untersucht werden:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## Ausführen der OLTP-Beispielworkload

Der einzige Unterschied zwischen den beiden folgenden *gespeicherten Prozeduren* ist, dass die erste Prozedur speicheroptimierte Versionen der Tabellen verwendet, während die zweite Prozedur die herkömmlichen Tabellen auf dem Datenträger nutzt:

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


In diesem Abschnitt wird veranschaulicht, wie Sie das praktische Hilfsprogramm **ostress.exe** zum Ausführen der beiden gespeicherten Prozeduren bei hohen Belastungsgraden verwenden. Sie können vergleichen, wie lange es dauert, bis die beiden Belastungstests abgeschlossen sind.


Für das Ausführen von „ostress.exe“ wird empfohlen, dass Sie Parameterwerte übergeben, die für beide ausgelegt sind:

- Führen Sie eine große Anzahl gleichzeitiger Verbindungen mit z. B. „-n100“ aus.
- Lassen Sie jede Verbindung eine Schleife Hunderte Male durchlaufen, indem Sie z. B „-r500“ angeben.


Allerdings möchten Sie möglicherweise mit wesentlich kleineren Werten wie „-n10“ und „-r50“ starten, um sicherzustellen, dass alles funktioniert.


### Skript für „ostress.exe“


Dieser Abschnitt zeigt das T-SQL-Skript, das in unsere „ostress.exe“-Befehlszeile eingebettet ist. Das Skript verwendet Elemente, die von dem zuvor von Ihnen installierten T-SQL-Skript erstellt wurden.


Das folgende Skript fügt einen Beispielauftrag mit fünf Positionen in die folgenden speicheroptimierten *Tabellen* ein:

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


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
	
WHILE (@i < 20)
begin;
	EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
		@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
	SET @i = @i + 1;
end
```


Um die „\_ondisk“-Version des vorherigen T-SQL-Skripts für „ostress.exe“ zu erstellen, müssen Sie beide Vorkommen der Teilzeichenfolge „*\_inmem*“ durch „*\_ondisk*“ ersetzen. Diese Austauschvorgänge wirken sich auf die Namen von Tabellen und gespeicherten Prozeduren aus.


### Installieren von RML-Hilfsprogrammen und ostress


Im Idealfall planen Sie die Ausführung von „ostress.exe“ auf einer Azure-VM. Dazu erstellen Sie eine [Azure-VM](https://azure.microsoft.com/documentation/services/virtual-machines/) in der gleichen Azure-Region, in der sich die AdventureWorksLT-Datenbank befindet. Sie können „ostress.exe“ aber auch auf Ihrem Laptop ausführen.


Installieren Sie auf der VM oder dem gewählten Host die RML-Hilfsprogramme (Replay Markup Language), zu denen „ostress.exe“ gehört.

- Siehe hierzu die Diskussion zu „ostress.exe“ unter [Beispieldatenbank für In-Memory OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Oder siehe [Beispieldatenbank für In-Memory OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Oder siehe den [Blog zur Installation von „ostress.exe“](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### Ausführen der „\_inmem“-Belastungsworkload


Sie können ein Fenster mit einer *RML-Eingabeaufforderung* zum Ausführen der „ostress.exe“-Befehlszeile verwenden. Die Befehlszeilenparameter weisen „ostress“ zu Folgendem an:

- Ausführen von 100 Verbindungen gleichzeitig (-n100).
- 50-maliges Ausführen des T-SQL-Skripts auf jeder Verbindung (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


So führen Sie die vorherige „ostress.exe“-Befehlszeile aus:


1. Setzen Sie den Dateninhalt der Datenbank durch Ausführen des folgenden Befehls in SSMS zurück, um alle Daten zu löschen, die bei vorherigen Ausführungen eingefügt wurden:
```
EXECUTE Demo.usp_DemoReset;
```

2. Kopieren Sie den Text der vorhergehenden „ostress.exe“-Befehlszeile in die Zwischenablage.

3. Ersetzen Sie <placeholders> für die Parameter „-S“, „-U“, „-P“ und „-d“ durch die ordnungsgemäßen tatsächlichen Werte.

4. Führen Sie die bearbeitete Befehlszeile in einem RML-Befehlsfenster aus.


#### Das Ergebnis ist eine Dauer


Wenn „ostress.exe“ abgeschlossen ist, wird die Testlaufdauer im RML-Befehlsfenster als letzte Zeile ausgegeben. Ein kürzerer Testlauf dauerte beispielsweise ca. 1,5 Minuten:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Zurücksetzen, für „\_ondisk“ bearbeiten und dann erneut ausführen


Sobald Sie über das Ergebnis der Ausführung von „\_inmem“ verfügen, führen Sie die folgenden Schritte für die „\_ondisk“-Ausführung durch:


1. Setzen Sie die Datenbank durch Ausführen des folgenden Befehls in SSMS zurück, um alle Daten zu löschen, die bei der vorherigen Ausführung eingefügt wurden:
```
EXECUTE Demo.usp_DemoReset;
```

2. Bearbeiten Sie die „ostress.exe“-Befehlszeile so, dass alle Vorkommen von „*\_inmem*“ durch „*\_ondisk*“ ersetzt werden.

3. Führen Sie „ostress.exe“ ein zweites Mal aus, und erfassen Sie die Dauer.

4. Setzen Sie die Datenbank erneut zurück, um auf verantwortliche Weise eine u. U. große Menge an Testdaten zu löschen.


#### Erwartete Vergleichsergebnisse

Unsere In-Memory-Tests haben für diese einfache Workload eine **9-mal** höhere Leistung ergeben, wenn ostress auf einem virtuellen Azure-Computer ausgeführt wird, der sich in derselben Azure-Region wie die Datenbank befindet.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## B. Installieren des In-Memory Analytics-Beispiels


In diesem Abschnitt vergleichen Sie die E/A- und Statistikergebnisse mit dem Verwenden eines Columnstore-Indexes im Vergleich mit einem herkömmlichen Index.


Columnstore-Indizes sind mit herkömmlichen Indizes logisch identisch, aber physisch unterschiedlich. Ein Columnstore-Index organisiert Daten so, dass sie erheblich komprimiert werden. Dies bietet wesentliche Leistungsverbesserungen.


Für Echtzeitanalysen einer OLTP-Workload empfiehlt es sich häufig, einen nicht gruppierten Columnstore-Index zu verwenden. Weitere Informationen finden Sie unter [Beschreibung von Columnstore-Indizes](http://msdn.microsoft.com/library/gg492088.aspx).



### Vorbereiten des Columnstore-Analysetests


1. Erstellen Sie im Azure-Portal anhand des Beispiels eine neue AdventureWorksLT-Datenbank.
 - Verwenden Sie denselben Namen.
 - Wählen Sie den Tarif „Premium“.

2. Kopieren Sie [sql\_in-memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) in die Zwischenablage.
 - Das T-SQL-Skript erstellt die erforderlichen In-Memory-Objekte in der in Schritt 1 erstellten Beispieldatenbank „AdventureWorksLT“.
 - Das Skript erstellt die Dimensionstabelle und zwei Faktentabellen. Beide Faktentabellen werden mit 3,5 Mio. Zeilen aufgefüllt.
 - Die Ausführung des Skripts kann 15 dauern.

3. Fügen Sie das T-SQL-Skript in SSMS ein, und führen Sie es aus.
 - Unerlässlich ist das Schlüsselwort **COLUMNSTORE** in einer **CREATE INDEX**-Anweisung wie in: <br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Legen Sie „AdventureWorksLT“ auf den Kompatibilitätsgrad 130 fest: <br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Der Kompatibilitätsgrad 130 steht nicht in direkter Beziehung mit den In-Memory-Features Aber der Grad 130 bietet im Allgemeinen eine höhere Abfrageleistung als 120.


#### Wichtige Tabellen und Columnstore-Indizes


- „dbo.FactResellerSalesXL\_CCI“ ist eine Tabelle mit einem gruppierten **Columnstore**-Index, die auf *Datenebene* eine erweiterte Komprimierung aufweist.

- „dbo.FactResellerSalesXL\_PageCompressed“ ist eine Tabelle mit einem vergleichbaren herkömmlichen gruppierten Index, der nur auf *Seitenebene* komprimiert ist.


#### Wichtige Abfragen zum Vergleichen des Columnstore-Indexes


[Hier](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) folgen mehrere T-SQL-Abfragetypen, die Sie ausführen können, um Leistungssteigerungen zu erkennen. Schritt 2 im T-SQL-Skript enthält verschiedene Abfragen von unmittelbarem Interesse. Die beiden Abfragen unterscheiden sich nur in einer Zeile:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Für die Tabelle „FactResellerSalesXL**\_CCI**“ ist ein gruppierter Columnstore-Index vorhanden.

Der folgende T-SQL-Skriptauszug gibt Statistiken für E/A und Dauer der Abfrage der einzelnen Tabellen zurück.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## Besondere Aspekte der Vorschauversion von In-Memory OLTP


Die In-Memory OLTP-Features in der Azure SQL-Datenbank [wurden am 28. Oktober 2015 als Vorschau aktiviert](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


In der Vorschauphase vor der allgemeinen Verfügbarkeit wird In-Memory OLTP nur für Folgendes unterstützt:

- Datenbanken im Tarif *Premium*.

- Datenbanken, die nach Aktivierung der In-Memory OLTP-Features erstellt wurden.
 - Eine neue Datenbank kann In-Memory OLTP nicht unterstützen, wenn sie aus einer Datenbank wiederhergestellt wurde, die vor der Aktivierung der In-Memory OLTP-Features erstellt wurde.


Im Zweifelsfall können Sie stets die folgende T-SQL SELECT-Anweisung ausführen, um festzustellen, ob die Datenbank In-Memory OLTP unterstützt. Das Ergebnis **1** bedeutet, dass die Datenbank In-Memory OLTP unterstützt:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Wenn die Abfrage **1** zurückgibt, wird In-Memory OLTP in dieser Datenbank sowie allen Datenbankkopien und -wiederherstellungen unterstützt, die auf Basis auf dieser Datenbank erstellt wurden.


#### Nur im Premium-Tarif zulässige Objekte


Wenn eine Datenbank eine der folgenden Arten von In-Memory OLTP-Objekten oder -Typen enthält, wird eine Herunterstufung des Tarifs von „Premium“ zu „Basic“ oder „Standard“ nicht unterstützt. Um die Datenbank herunterzustufen, müssen Sie zuerst diese Objekte löschen:

- Speicheroptimierte Tabellen
- Speicheroptimierte Tabellentypen
- Systemintern kompilierte Module


#### Andere Beziehungen


- Das Verwenden von In-Memory OLTP-Features mit Datenbanken in elastischen Pools wird in der Vorschauphase nicht unterstützt, jedoch ggf. künftig unterstützt werden:

- Die Verwendung von In-Memory OLTP mit SQL Data Warehouse wird nicht unterstützt.
 - Das Columnstore-Indexfeature von In-Memory Analytics wird in SQL Data Warehouse unterstützt.

- Der Abfragespeicher erfasst in der Vorschauphase keine Abfragen innerhalb systemintern kompilierter Module, was jedoch künftig der Fall sein kann.

- Einige Transact-SQL-Funktionen werden bei In-Memory OLTP nicht unterstützt. Dies gilt sowohl für Microsoft SQL Server als auch Azure SQL-Datenbank. Einzelheiten finden Sie hier:
 - [Transact-SQL-Unterstützung für OLTP im Arbeitsspeicher](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Von In-Memory OLTP nicht unterstützte Transact-SQL-Konstrukte.](http://msdn.microsoft.com/library/dn246937.aspx)


## Weitere Schritte


- Lesen Sie [Verwenden von In-Memory OLTP in einer vorhandenen Azure SQL-Anwendung](sql-database-in-memory-oltp-migration.md).


## Zusätzliche Ressourcen

#### Weiterführende Informationen

- [Erfahren Sie mehr über In-Memory OLTP für sowohl Microsoft SQL Server als auch Azure SQL-Datenbank](http://msdn.microsoft.com/library/dn133186.aspx)

- [Informieren Sie sich über Real-Time Operational Analytics auf MSDN.](http://msdn.microsoft.com/library/dn817827.aspx)

- Das [Whitepaper zu allgemeinen Workloadmustern und Überlegungen zur Migration](http://msdn.microsoft.com/library/dn673538.aspx) beschreibt Workloadmuster, bei denen In-Memory OLTP im Allgemeinen erhebliche Leistungssteigerungen bietet.

#### Anwendungsentwurf

- [In-Memory OLTP (In-Memory Optimization)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Verwenden Sie In-Memory OLTP in einer vorhandenen Azure SQL-Anwendung.](sql-database-in-memory-oltp-migration.md)

#### Tools

- [SQL Server Data Tools Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx) (für die neueste monatliche Version)

- [Beschreibung von RML-Hilfsprogrammen (Replay Markup Language) für SQL Server](http://support.microsoft.com/de-DE/kb/944837)

- [Überwachen von In-Memory-Speicher](sql-database-in-memory-oltp-monitoring.md) für In-Memory OLTP

<!---HONumber=AcomDC_0323_2016-->