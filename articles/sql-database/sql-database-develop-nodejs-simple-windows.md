<properties 
	pageTitle="Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Node.js unter Windows" 
	description="Zeigt ein Node.js-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank. Das Beispiel wird auf einem Clientcomputer unter Windows ausgeführt."
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
	ms.date="07/30/2015" 
	ms.author="mebha"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Node.js unter Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema enthält ein Node.js-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank. Das Node.js-Programm wird auf einem Windows-Clientcomputer ausgeführt. Zum Verwalten der Verbindung wird der msnodesql-Treiber verwendet.


## Anforderungen


Die folgenden Softwarekomponenten müssen auf dem Cliententwicklungscomputer vorhanden sein.


-  Node.js – [Version 0.8.9 (32-Bit-Version)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/). Scrollen Sie zum Download für das Windows-Installationsprogramm für 32-Bit-x86 (nicht für das x64-Windows-Installationsprogramm für 64 Bit), und klicken Sie darauf.
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/) – das Installationsprogramm für x86 oder x64. 
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2) – die Express Edition ist kostenlos von Microsoft erhältlich.
- SQL Server Native Client 11.0 – erhältlich als Microsoft SQL Server 2012 Native Client im [SQL Server 2012 Feature Pack](http://www.microsoft.com/download/details.aspx?id=29065).


### Installieren der erforderlichen Module

Sobald Sie die Anforderungen erfüllen, stellen Sie sicher, dass Sie Version 0.8.9 von „Node.js“ verwenden. Sie können dies überprüfen, indem Sie den folgenden Befehl in der Befehlszeile eingeben: node -v. <br> Navigieren Sie in einem **cmd.exe**-Befehlszeilenfenster zu Ihrem Projektverzeichnis, z. B. C:\\NodeJSSQLProject. Geben Sie die folgenden Befehle in der gezeigten Reihenfolge ein.

	npm init
	npm install msnodesql

Als Nächstes navigieren Sie zum Ordner „Node\_modules\\msnodesql“ und führen die Datei **Msnodesql-0.2.1-v0. 8-ia32** aus. Führen Sie die Schritte im Installationsassistenten aus, und drücken Sie "Fertig stellen", wenn Sie fertig sind. An diesem Punkt sollten Sie den „Node.js“-SQL Server-Treiber installiert haben. Fahren Sie mit den nächsten Schritten fort, um die Verbindungszeichenfolge abzurufen. Anschließend sollten Sie in der Lage sein, von der „Node.js“-Anwendung eine Verbindung mit der Azure SQL-Datenbank herzustellen.

### Erstellen einer Datenbank und Abrufen der Verbindungszeichenfolge
 
Im Thema [Erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank und zum Abrufen der Verbindungszeichenfolge. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.


## Herstellen von Verbindungen mit der SQL-Datenbank


- Kopieren Sie den folgenden Code in eine JS-Datei in Ihrem Projektverzeichnis.


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});	


- Führen Sie nun Ihre JS-Datei mit dem folgenden Befehl aus.


		node index.js


## Ausführen von SQL-SELECT-Anweisungen


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## Einfügen von Zeilen, Übergeben von Parametern und Abrufen von generierten Primärschlüsseln


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## Transaktionen


Die **conn.beginTransactions**-Methode funktioniert nicht in Azure SQL-Datenbank. Führen Sie stattdessen das Codebeispiel zum Ausführen von Transaktionen in SQL-Datenbanken aus.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	    
	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## Gespeicherte Prozeduren


Damit dieses Codebeispiel funktioniert, müssen Sie zunächst eine gespeicherte Prozedur erstellen, die keine Parameter eingibt. Sie können gespeicherte Prozeduren mit einem Tool wie SQL Server Management Studio ("SSMS.exe") erstellen.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
		
	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});

 
## Nächste Schritte

Weitere Informationen finden Sie im [Node.js Developer Center](/develop/nodejs/).

<!---HONumber=Oct15_HO3-->