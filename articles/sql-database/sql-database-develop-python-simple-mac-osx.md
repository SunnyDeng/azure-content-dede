<properties 
	pageTitle="Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Python unter Mac OS" 
	description="Python-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank auf einem Macintosh-Computer. Das Beispiel verwendet den pymssql-Treiber."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="mebha"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Python unter Mac OS


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema zeigt ein in Python geschriebenes Codebeispiel. Das Beispiel wird auf einem Macintosh-Computer ausgeführt. Das Beispiel stellt mithilfe des **pymssql**-Treibers eine Verbindung mit Azure SQL-Datenbank her.


## Anforderungen


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).
- [FreeTDS](https://github.com/brianb/FreeTDS)
- [Pymssql](https://github.com/pymssql/pymssql)

### Installieren der erforderlichen Module


Öffnen Sie das Terminal, und installieren Sie folgende Komponenten:

**1) Homebrew**: Führen Sie den folgenden Befehl über das Terminal aus. Damit wird der Homebrew-Paket-Manager auf Ihren Computer heruntergeladen.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS**: Führen Sie den folgenden Befehl über das Terminal aus. Damit wird FreeTDS auf Ihren Computer heruntergeladen. FreeTDS ist für die Ausführung von pymmsql erforderlich.

    brew install FreeTDS
  
**3) Pymmsql**: Führen Sie den folgenden Befehl über das Terminal aus. Damit wird pymmsql auf Ihrem Computer installiert.

    sudo -H pip install pymssql

### Erstellen einer Datenbank und Abrufen der Verbindungszeichenfolge


Auf der Seite [Erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank und zum Abrufen der Verbindungszeichenfolge. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.


## Herstellen von Verbindungen mit der SQL-Datenbank


Die [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html)-Funktion dient zum Herstellen einer Verbindung mit der SQL-Datenbank.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Ausführen von SQL-SELECT-Anweisungen

Mit der [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute)-Funktion können Sie ein Resultset aus einer Abfrage einer SQL-Datenbank abrufen. Diese Funktion akzeptiert praktisch jede Abfrage und gibt ein Resultset zurück, das mithilfe von [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone) durchlaufen werden kann.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Einfügen von Zeilen, Übergeben von Parametern und Abrufen von generierten Primärschlüsseln

In SQL-Datenbanken können die [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx)-Eigenschaft und das [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx)-Objekt zum automatischen Generieren von Werten für [Primärschlüssel](https://msdn.microsoft.com/library/ms179610.aspx) verwendet werden.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Transaktionen


Dieses Codebeispiel veranschaulicht die Verwendung von Transaktionen für folgende Aufgaben:


- Starten von Transaktionen

- Einfügen von Zeilen mit Daten

- Durchführen von Rollbacks für Transaktionen zum Rückgängigmachen von Einfügungen


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()

 

<!---HONumber=July15_HO4-->