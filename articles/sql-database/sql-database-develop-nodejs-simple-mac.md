<properties
	pageTitle="Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Node.js mit Tedious unter Mac OS X"
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
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Node.js mit Tedious unter Mac OS X


> [AZURE.SELECTOR]
- [Node.js](sql-database-develop-nodejs-simple-mac.md)
- [Python](sql-database-develop-python-simple-mac-osx.md)
- [Ruby](sql-database-develop-ruby-simple-mac-osx.md)


Dieses Thema enthält ein Node.js-Codebeispiel, das unter Mac OS X ausgeführt wird. Das Beispiel stellt mithilfe des Tedious-Treibers eine Verbindung mit Azure SQL-Datenbank her.


## Voraussetzungen


Installieren Sie **node**, sofern es nicht bereits auf Ihrem Computer installiert ist.


Zum Installieren von node.js unter OS X 10.10 Yosemite können Sie ein vorkompiliertes binäres Paket herunterladen, das die Installation vereinfacht. Klicken Sie unter [nodejs.org](http://nodejs.org/) auf die Schaltfläche "Install", um das aktuelle Paket herunterzuladen.

Installieren Sie das Paket aus der DMG-Datei mit dem Installations-Assistenten, der **node** und **npm** installiert. npm ist der Node Package Manager, der zusätzliche Pakete für node.js installiert.


Nach der Konfiguration Ihres Computers mit **node** und **npm** navigieren Sie zu dem Verzeichnis, in dem Sie das Node.js-Projekt erstellen möchten, und geben die folgenden Befehle ein.


	npm init
	npm install tedious


**npm init** erstellt ein node-Projekt. Wenn Sie während der Projekterstellung die Standardeinstellungen beibehalten möchten, drücken Sie die EINGABETASTE, bis das Projekt erstellt wurde. Im Projektverzeichnis wird nun die Datei **package.json** angezeigt.

### Eine SQL-Datenbank

Auf der [Seite für erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.

## Schritt 1: Abrufen der Verbindungsdetails

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Schritt 2: Verbinden

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


## Schritt 3: Ausführen einer Abfrage


Alle SQL-Anweisungen werden mithilfe der [new Request()](http://pekim.github.io/tedious/api-request.html)-Funktion ausgeführt. Wenn die Anweisung Zeilen zurückgibt, z. B. eine select-Anweisung, können Sie diese mithilfe der [request.on()](http://pekim.github.io/tedious/api-request.html)-Funktion abrufen. Wenn keine Zeilen vorhanden sind, gibt die [request.on()](http://pekim.github.io/tedious/api-request.html)-Funktion leere Listen zurück.


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
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


## Schritt 4: Einfügen einer Zeile

In diesem Beispiel erfahren Sie, wie Sie eine [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)-Anweisung sicher ausführen, Parameter zum Schutz Ihrer Anwendung vor einer [Einschleusung von SQL-Befehlen](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) übergeben und den automatisch generierten [Primärschlüsselwert](https://msdn.microsoft.com/library/ms179610.aspx) abrufen.


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
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


## Nächste Schritte

Weitere Informationen finden Sie im [Node.js Developer Center](/develop/nodejs/).

<!---HONumber=AcomDC_1223_2015-->