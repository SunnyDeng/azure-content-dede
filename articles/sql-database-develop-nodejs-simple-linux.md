<properties 
	pageTitle="Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Node.js mit Tedious unter Ubuntu Linux" 
	description="Zeigt ein Node.js-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank. Das Beispiel verwendet den Tedious-Treiber zum Herstellen der Verbindung."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="mebha"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Node.js mit Tedious unter Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema enthält ein Node.js-Codebeispiel, das unter Ubuntu Linux ausgeführt wird. Das Beispiel stellt mithilfe des Tedious-Treibers eine Verbindung mit Azure SQL-Datenbank her.


## Erforderliche Softwarekomponenten


Öffnen Sie das Terminal, und installieren Sie **node** und **npm**, sofern diese Komponenten noch nicht auf Ihrem Computer installiert sind.


	sudo apt-get install node
	sudo apt-get install npm


Nach der Konfiguration Ihres Computers mit **node** und **npm** navigieren Sie zu dem Verzeichnis, in dem Sie das Node.js-Projekt erstellen möchten, und geben die folgenden Befehle ein.


	sudo npm init
	sudp npm install tedious


**npm init** erstellt ein node-Projekt. Wenn Sie während der Projekterstellung die Standardeinstellungen beibehalten möchten, drücken Sie die EINGABETASTE, bis das Projekt erstellt wurde. Im Projektverzeichnis wird nun die Datei **package.json** angezeigt.


### Erstellen von AdventureWorks-Datenbanken


Für das Codebeispiel in diesem Thema wird eine **AdventureWorks**-Testdatenbank vorausgesetzt. Wenn Sie noch keine erstellt haben, lesen Sie unter [Erste Schritte mit SQL-Datenbank](sql-database-get-started.md) nach. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.


## Herstellen von Verbindungen mit der SQL-Datenbank

Die [new Connection](http://pekim.github.io/tedious/api-connection.html)-Funktion dient zum Herstellen einer Verbindung mit der SQL-Datenbank.

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## Ausführen von SQL-SELECT-Anweisungen


Alle SQL-Anweisungen werden mithilfe der [new Request()](http://pekim.github.io/tedious/api-request.html)-Funktion ausgeführt. Wenn die Anweisung Zeilen zurückgibt, z. B. eine select-Anweisung, können Sie diese mithilfe der [request.on()](http://pekim.github.io/tedious/api-request.html)-Funktion abrufen. Wenn keine Zeilen vorhanden sind, gibt die [request.on()](http://pekim.github.io/tedious/api-request.html)-Funktion leere Listen zurück.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});
	
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	
	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);} 
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});
	
		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## Einfügen von Zeilen, Anwenden von Parametern und Abrufen von generierten Primärschlüsseln


In SQL-Datenbanken können die [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx)-Eigenschaft und das [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx)-Objekt zum automatischen Generieren von Werten für [Primärschlüssel](https://msdn.microsoft.com/library/ms179610.aspx) verwendet werden. In diesem Beispiel erfahren Sie, wie Sie eine "insert"-Anweisung ausführen, Parameter zum Schutz vor einer Einschleusung von SQL-Befehlen sicher übergeben und den automatisch generierten Primärschlüsselwert abrufen.


Das Codebeispiel in diesem Abschnitt wendet Parameter auf eine SQL-INSERT-Anweisung an. Der generierte Primärschlüsselwert wird vom Programm abgerufen.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});
	
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	
	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);} 
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}

<!---HONumber=58-->