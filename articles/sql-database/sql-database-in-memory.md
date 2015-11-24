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
	ms.date="11/16/2015"
	ms.author="jodebrui"/>


# Erste Schritte mit In-Memory (Vorschau) in SQL-Datenbank

Herkömmliche SQL-Tabellen werden nur auf einer Festplatte gespeichert. Mithilfe der In-Memory-Features können Sie eine Tabelle so optimieren, dass sie sich im aktiven Arbeitsspeicher befindet, während der Server ausgeführt wird.


In-Memory-Technologien verbessern die Leistung von Transaktions- und Analyseworkloads erheblich.

- Je nach Workload können Sie mithilfe von In-Memory OLTP (Online Transaction Processing, Onlinetransaktionsverarbeitung) einen bis um das 30-fache höheren Transaktionsdurchsatz erreichen.
 - Speicheroptimierte Tabellen.
 - Systemeigen kompilierte gespeicherte Prozeduren.

- Mit In-Memory Analytics können Sie die Abfrageleistung bis um das 100-fache steigern.
 - Columnstore-Indizes.


Für [Real-Time Analytics](http://msdn.microsoft.com/library/dn817827.aspx) kombinieren Sie diese Technologien, um sich Folgendes zu verschaffen:

- Geschäftsinformationen in Echtzeit basierend auf Betriebsdaten
- Sehr schnelle OLTP


#### Verfügbarkeit


- *Allgemeine Verfügbarkeit:* In-Memory Analytics ist nun allgemein verfügbar.
- *Vorschau:* In-Memory OLTP und Real-Time Operational Analytics befinden sich in der Vorschauphase.
 - Die beiden Technologien sind nur für Azure SQL-Datenbanken im Tarif [Premium](sql-database-service-tiers.md) verfügbar.


#### OLTP und Analysen

Die zwei Hauptabschnitte in diesem Thema sind wie folgt:

- A: In-Memory [OLTP](#install_oltp_manuallink), was Lese- und Schreibvorgänge umfasst.
- B. In-Memory [Analytics](#install_analytics_manuallink), was Lesevorgänge umfasst.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## A: Installieren des Beispiels für In-Memory OLTP

Sie können im [Azure-Vorschauportal](http://portal.azure.com/) die Beispieldatenbank „AdventureWorksLT [V12]“ mit wenigen Klicks erstellen. Anhand der Schritte in diesem Abschnitt wird erläutert, wie Sie anschließend Ihre AdventureWorksLT-Datenbank wie folgt erweitern können:

- In-Memory-Tabellen
- Systemeigen kompilierte gespeicherte Prozedur


#### Installationsschritte

1. Erstellen Sie im [Azure-Vorschauportal](http://portal.azure.com/) auf einem V12-Server eine Premium-Datenbank. Legen Sie die AdventureWorksLT [V12]-Beispieldatenbank als **Quelle** fest.
 - Detaillierte Anleitungen finden Sie unter [Erstellen Ihrer ersten Azure SQL-Datenbank](sql-database-get-started.md).

2. Stellen Sie eine Verbindung mit [SQL Server Management Studio (SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx) her.

3. Kopieren Sie das [In-Memory OLTP Transact-SQL-Skript](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) in die Zwischenablage.
 - Das T-SQL-Skript erstellt die erforderlichen In-Memory-Objekte in der in Schritt 1 erstellten Beispieldatenbank „AdventureWorksLT“.

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


Das Ergebnis **0** bedeutet, dass In-Memory nicht unterstützt wird. 1 bedeutet Unterstützung.


#### Informationen zu speicheroptimierten Elementen

**Tabellen**: Das Beispiel enthält die folgenden speicheroptimierten Tabellen:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Sie können im **Objekt-Explorer** speicheroptimierte Tabellen in SSMS wie folgt untersuchen:

- Klicken Sie mit der rechten Maustaste auf **Tabellen** > **Filter** > **Filtereinstellungen** > **Speicheroptimiert** gleich 1.


Oder Sie können die Katalogsichten abfragen wie z. B.:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**Systemeigen kompilierte gespeicherte Prozedur**: „SalesLT.usp\_InsertSalesOrder\_inmem“ kann über eine Katalogsichtabfrage untersucht werden:


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

- Führen Sie eine große Anzahl gleichzeitiger Verbindungen mit z. B. „-n100“ aus.
- Lassen Sie jede Verbindung eine Schleife Hunderte Male durchlaufen, indem Sie z. B „-r500“ angeben.


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
	
EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
	@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


Um die „\_ondisk“-Version des vorherigen T-SQL-Skripts für „ostress.exe“ zu erstellen, müssen Sie beide Vorkommen der Teilzeichenfolge „*\_inmem*“ durch „*\_ondisk*“ ersetzen. Diese Austauschvorgänge wirken sich auf die Namen von Tabellen und gespeicherten Prozeduren aus.


### Installieren von RML-Hilfsprogrammen und ostress


Im Idealfall planen Sie die Ausführung von „ostress.exe“ auf einer Azure-VM. Dazu erstellen Sie eine [Azure-VM](http://azure.microsoft.com/documentation/services/virtual-machines/) in der gleichen Azure-Region, in der sich die „AdventureWorksLT“-Datenbank befindet. Sie können „ostress.exe“ aber auch auf Ihrem Laptop ausführen.


Installieren Sie auf der VM oder dem gewählten Host die RML-Hilfsprogramme (Replay Markup Language), zu denen „ostress.exe“ gehört.

- Siehe die Erläuterungen zu „ostress.exe“ unter [Erweiterungen von AdventureWorks zum Veranschaulichen von In-Memory OLTP](http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx).
 - Oder siehe [Beispieldatenbank für In-Memory OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Oder siehe den [Blog zur Installation von „ostress.exe“](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014, whereas mt465764.aspx is for SQL 2016.
-->



### Ausführen der „\_inmem“-Belastungsworkload


Sie können ein Fenster mit einer *RML-Eingabeaufforderung* zum Ausführen unserer „ostress.exe“-Befehlszeile verwenden.

Bei Ausführung an der RML-Eingabeaufforderung bewirkt der folgende „ostress.exe“-Befehl Folgendes:

- Eine Million Aufträge werden mit jeweils fünf Positionen in speicheroptimierte Tabellen eingefügt.
- 100 gleichzeitige Verbindungen (-n100) werden verwendet.


```
ostress.exe -n100 -r500 -S<servername>.database.windows.net
	 -U<login> -P<password> -d<database>
	 -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
	 @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
	 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*
	 10000;
	 INSERT INTO @od SELECT OrderQty, ProductID FROM
	 Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int);
	 WHILE (@i < 20) begin;
	 EXECUTE SalesLT.usp_InsertSalesOrder_inmem
	 @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID,
	 @ShipToAddressID, @od; set @i += 1; end"
```


So führen Sie die vorherige „ostress.exe“-Befehlszeile aus:


1. Setzen Sie die Datenbank durch Ausführen des folgenden Befehls in SSMS zurück, um alle Daten zu löschen, die bei vorherigen Ausführungen eingefügt wurden: ```
EXECUTE Demo.usp_DemoReset;
```

2. Kopieren Sie den Text in die Zwischenablage.

3. Ersetzen Sie alle Zeilenendezeichen (\\r\\n) und alle Tabulatoren (\\t) durch Leerzeichen.

4. Ersetzen Sie <placeholders> für die Parameter „-S“, „-U“, „-P“ und „-d“ durch die ordnungsgemäßen tatsächlichen Werte.


#### Das Ergebnis ist eine Dauer


Wenn „ostress.exe“ abgeschlossen ist, wird die Testlaufdauer im RML-Befehlsfenster als letzte Zeile ausgegeben. Ein kürzerer Testlauf dauerte beispielsweise ca. 1,5 Minuten:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Zurücksetzen, für „\_ondisk“ bearbeiten und dann erneut ausführen


Sobald Sie über das Ergebnis der Ausführung von „\_inmem“ verfügen, führen Sie die folgenden Schritte für die „\_indisk“-Ausführung durch:


1. Setzen Sie die Datenbank durch Ausführen des folgenden Befehls in SSMS zurück, um alle Daten zu löschen, die beim vorherigen Testlauf eingefügt wurden: ```
EXECUTE Demo.usp_DemoReset;
```

2. Bearbeiten Sie die „ostress.exe“-Befehlszeile so, dass alle Vorkommen von „*\_inmem*“ durch „*\_ondisk*“ ersetzt werden.

3. Führen Sie „ostress.exe“ erneut aus, und erfassen Sie die Dauer.

4. Setzen Sie die Datenbank erneut zurück, um für eine ordnungsgemäße Bereinigung zu sorgen.
 - Ein einziger Testlauf, bei dem eine Million Aufträge eingefügt werden, führt zu 500 MB in speicheroptimierten Tabellen.


#### Erwartete Vergleichsergebnisse

Tests haben für diese Workload und bei Ausführung von „ostress“ auf einem virtuellen Computer, der sich in der gleichen Azure-Region befindet wie die Datenbank, eine **9-mal** höhere Leistung bei speicheroptimierten Tabellen als bei datenträgerbasierten Tabellen ergeben.

Die Leistungssteigerung kann noch höher sein, wenn die Konvertierung in systemeigen kompilierte gespeicherte Prozeduren hinzugefügt wird.


## B. Installieren des In-Memory Analytics-Beispiels


In diesem Abschnitt vergleichen Sie die E/A- und Statistikergebnisse mit dem Verwenden eines Columnstore-Indexes im Vergleich mit einem herkömmlichen Index.


Columnstore-Indizes sind mit herkömmlichen Indizes logisch identisch, aber physisch unterschiedlich. Ein Columnstore-Index organisiert Daten so, dass sie erheblich komprimiert werden. Dies bietet wesentliche Leistungsverbesserungen.


Für Echtzeitanalysen einer OLTP-Workload empfiehlt es sich häufig, einen nicht gruppierten Columnstore-Index zu verwenden. Weitere Informationen finden Sie unter [Beschreibung von Columnstore-Indizes](http://msdn.microsoft.com/library/gg492088.aspx).



### Vorbereiten des Columnstore-Analysetests


1. Erstellen Sie im Azure-Portal anhand des Beispiels eine neue AdventureWorksLT-Datenbank.
 - Verwenden Sie denselben Namen.
 - Wählen Sie den Tarif „Premium“.

2. Kopieren Sie [sql\_in-memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) in die Zwischenablage.
 - Das T-SQL-Skript erstellt die erforderlichen In-Memory-Objekte in der in Schritt 1 erstellten Beispieldatenbank „AdventureWorksLT“.
 - Das Skript erstellt die Dimensionstabelle und zwei Faktentabellen. Beide Faktentabellen werden mit 3,5 Mio. Zeilen aufgefüllt.
 - Die Ausführung des Skripts kann 15 dauern.

3. Fügen Sie das T-SQL-Skript in SSMS ein, und führen Sie es aus.
 - Unerlässlich ist das Schlüsselwort **COLUMNSTORE** in einer **CREATE INDEX**-Anweisung wie in: <br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Legen Sie „AdventureWorksLT“ auf den Kompatibilitätsgrad 130 fest: <br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`


#### Wichtige Tabellen und Columnstore-Indizes


- „dbo.FactResellerSalesXL\_CCI“ ist eine Tabelle mit einem gruppierten **Columnstore**-Index, die auf *Datenebene* eine erweiterte Komprimierung aufweist.

- „dbo.FactResellerSalesXL\_PageCompressed“ ist eine Tabelle mit einem vergleichbaren herkömmlichen gruppierten Index, der nur auf *Seitenebene* komprimiert ist.


#### Wichtige Abfragen zum Vergleichen des Columnstore-Indexes


[Hier](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) folgen mehrere T-SQL-Abfragetypen, die Sie ausführen können, um Leistungssteigerungen zu erkennen. Schritt 2 im T-SQL-Skript enthält verschiedene Abfragen von unmittelbarem Interesse. Die beiden Abfragen unterscheiden sich nur in einer Zeile:


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


## Besondere Aspekte der Vorschauversion von In-Memory


Die Vorschauphase der In-Memory-Features in Azure SQL-Datenbank [wurden am 28. Oktober 2015 aktiviert](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


In der Vorschauphase vor der allgemeinen Verfügbarkeit wird In-Memory OLTP nur für Folgendes unterstützt:

- Datenbanken im Tarif *Premium*.

- Datenbanken, die nach Aktivierung der In-Memory-Features erstellt wurden.
 - Eine neue Datenbank, die aus einer vor dem Aktivierungsdatum von In-Memory erstellten Sicherung wiederhergestellt wird, kann In-Memory nicht unterstützen.
 - Angenommen, Sie sichern eine Datenbank, die In-Memory unterstützt. Anschließend stellen Sie die Sicherung in einer alten Premium-Datenbank wieder her. Die alte Datenbank unterstützt jetzt In-Memory.


Im Zweifelsfall können Sie stets die folgende T-SQL SELECT-Anweisung ausführen, um festzustellen, ob die Datenbank In-Memory OLTP unterstützt. Das Ergebnis **1** bedeutet, dass die Datenbank In-Memory unterstützt:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Wenn die Abfrage **1** zurückgibt, wird In-Memory OLTP in dieser Datenbank sowie allen Datenbankkopien und -wiederherstellungen unterstützt, die basierend auf dieser Datenbank erstellt wurden.


#### Nur im Premium-Tarif zulässige Objekte


Wenn eine Datenbank eine der folgenden Arten von In-Memory OLTP-Objekten oder -Typen enthält, wird eine Herunterstufung des Tarifs von „Premium“ zu „Basic“ oder „Standard“ nicht unterstützt. Um die Datenbank herunterzustufen, müssen Sie zuerst diese Objekte löschen:

- Speicheroptimierte Tabellen
- Speicheroptimierte Tabellentypen
- Systemeigen kompilierte Module


#### Andere Beziehungen


- Das Verwenden von In-Memory OLTP-Features mit Datenbanken in elastischen Pools wird in der Vorschauphase nicht unterstützt, jedoch ggf. künftig unterstützt werden:

- Die Verwendung von In-Memory OLTP mit SQL Data Warehouse wird nicht unterstützt.
 - Das Columnstore-Indexfeature von In-Memory Analytics wird in SQL Data Warehouse unterstützt.

- Der Abfragespeicher erfasst in der Vorschauphase keine Abfragen innerhalb systemeigen kompilierter Module, was jedoch künftig der Fall sein kann.

- Einige Transact-SQL-Funktionen werden bei In-Memory OLTP nicht unterstützt. Dies gilt sowohl für Microsoft SQL Server als auch Azure SQL-Datenbank. Einzelheiten finden Sie hier:
 - [Transact-SQL-Unterstützung für OLTP im Arbeitsspeicher](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Von In-Memory OLTP nicht unterstützte Transact-SQL-Konstrukte.](http://msdn.microsoft.com/library/dn246937.aspx)


## Weitere Schritte


- Lesen Sie [Verwenden von In-Memory OLTP in einer vorhandenen Azure SQL-Anwendung](sql-database-in-memory-oltp-migration.md).


## Zusätzliche Ressourcen

#### Weiterführende Informationen

- [Erfahren Sie mehr über In-Memory OLTP für sowohl Microsoft SQL Server als auch Azure SQL-Datenbank](http://msdn.microsoft.com/library/dn133186.aspx)

- [Informieren Sie sich über Real-Time Operational Analytics auf MSDN.](http://msdn.microsoft.com/library/dn817827.aspx)

- Das [Whitepaper zu allgemeinen Workloadmustern und Überlegungen zur Migration](http://msdn.microsoft.com/library/dn673538.aspx) beschreibt Workloadmuster, bei denen In-Memory OLTP im Allgemeinen erhebliche Leistungssteigerungen bietet.

#### Anwendungsentwurf

- [In-Memory OLTP (In-Memory Optimization)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Verwenden Sie In-Memory OLTP in einer vorhandenen Azure SQL-Anwendung.](sql-database-in-memory-oltp-migration.md)

#### Tools

- [SQL Server Data Tools Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx) (für die neueste monatliche Version)

- [Beschreibung von RML-Hilfsprogrammen (Replay Markup Language) für SQL Server](http://support.microsoft.com/de-DE/kb/944837)

- [Überwachen von In-Memory-Speicher](sql-database-in-memory-oltp-monitoring.md) für In-Memory OLTP

<!---HONumber=Nov15_HO4-->