<properties
	pageTitle="PHP unter Windows für SQL-Datenbank | Microsoft Azure"
	description="Enthält ein PHP-Beispielprogramm, das eine Verbindung von einem Windows-Client mit Azure SQL-Datenbank herstellt, sowie Links zu den auf dem Client erforderlichen Softwarekomponenten."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="mebha"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von PHP unter Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


In diesem Thema wird veranschaulicht, wie Sie von einer in PHP geschriebenen Clientanwendung, die unter Windows ausgeführt wird, eine Verbindung mit Azure SQL-Datenbank herstellen können.


## Voraussetzungen


Für die Ausführung des PHP-Codebeispiels in diesem Thema muss auf dem Clientcomputer folgende Software installiert sein:


- [Microsoft-Treiber für PHP für Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) ("SQLSRV32.EXE" enthält die aktuellsten Komponenten)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- [Microsoft ODBC-Treiber](https://www.microsoft.com/de-de/download/details.aspx?id=36434)
- IIS Express
- [PHP 5.6 für IIS Express](http://www.microsoft.com/web/downloads/platform.aspx): Das Herunterladen erfolgt über den Webplattform-Installer. Verwenden Sie unbedingt Internet Explorer, um den Webplattform-Installer herunterzuladen

In unserem [Teamblog](http://blogs.msdn.com/b/sqlphp/archive/2015/05/11/getting-started-with-php-and-microsoft-sql-server.aspx) und [Video](https://www.youtube.com/watch?v=0oCjiRK_tUk) erfahren Sie, wie Sie die oben genannten Voraussetzungen installieren und einrichten.


## Erstellen einer Datenbank und Abrufen der Verbindungszeichenfolge


Im Thema [Erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank und zum Abrufen der Verbindungszeichenfolge. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.


## Herstellen von Verbindungen mit der SQL-Datenbank


Diese **OpenConnection**-Funktion wird zu Beginn in allen im Folgenden aufgeführten Funktionen aufgerufen.


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";
			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");
			$conn = sqlsrv_connect($serverName, $connectionOptions);
			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Ausführen einer Abfrage und Abrufen des Resultsets

Mit der [sqlsrv_query()](http://php.net/manual/en/function.sqlsrv-query.php)-Funktion können Sie ein Resultset aus einer Abfrage einer SQL-Datenbank abrufen. Diese Funktion akzeptiert praktisch jede Abfrage und das Verbindungsobjekt und gibt ein Resultset zurück, das mithilfe von [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php) durchlaufen werden kann.

	function ReadData()
	{
		try
		{
			$conn = OpenConnection();
			$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
			$getProducts = sqlsrv_query($conn, $tsql);
			if ($getProducts == FALSE)
				die(FormatErrors(sqlsrv_errors()));
			$productCount = 0;
			while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
			{
				echo($row['CompanyName']);
				echo("<br/>");
				$productCount++;
			}
			sqlsrv_free_stmt($getProducts);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}
	

## Einfügen von Zeilen, Übergeben von Parametern und Abrufen von generierten Primärschlüsseln


In SQL-Datenbanken können die [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx)-Eigenschaft und das [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx)-Objekt zum automatischen Generieren von Werten für [Primärschlüssel](https://msdn.microsoft.com/library/ms179610.aspx) verwendet werden.


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT 			INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";
			//Insert query
			$insertReview = sqlsrv_query($conn, $tsql);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));
			echo "Product Key inserted is :";	
			while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))
			{   
				echo($row['ProductID']);
			}
			sqlsrv_free_stmt($insertReview);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}

## Transaktionen


Dieses Codebeispiel veranschaulicht die Verwendung von Transaktionen für folgende Aufgaben:

- Starten von Transaktionen

- Hinzufügen einer Zeile mit Daten und Aktualisieren einer anderen Datenzeile

- Durchführen eines Commits der Transaktion, wenn Einfügung und Aktualisierung erfolgreich waren, und eines Rollbacks der Transaktion, wenn ein Vorgang fehlgeschlagen ist


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID,OrderQty,ProductID,UnitPrice) 
			VALUES (71774, 22, 709, 33)";
			$stmt1 = sqlsrv_query($conn, $tsql1);
			
			/* Set up and execute the second query. */
			$tsql2 = "UPDATE SalesLT.SalesOrderDetail SET OrderQty = (OrderQty + 1) WHERE ProductID = 709";
			$stmt2 = sqlsrv_query( $conn, $tsql2);
			
			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
			       sqlsrv_commit($conn);
			       echo("Transaction was commited");
			}
			else
			{
			    sqlsrv_rollback($conn);
			    echo "Transaction was rolled back.\n";
			}
			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Weitere nützliche Informationen


Weitere Informationen über die Installation und Verwendung von PHP finden Sie unter [Accessing SQL Server Databases with PHP](http://technet.microsoft.com/library/cc793139.aspx) (Zugreifen auf SQL Server-Datenbanken mit PHP, in englischer Sprache).

 

<!---HONumber=58_postMigration-->