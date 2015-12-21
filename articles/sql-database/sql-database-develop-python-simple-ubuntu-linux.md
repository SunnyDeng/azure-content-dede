<properties
	pageTitle="Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Python mit pymssql unter Ubuntu"
	description="Zeigt ein Python-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank. Das Beispiel wird auf einem Clientcomputer unter Ubuntu Linux ausgeführt."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="meetb"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Python unter Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema zeigt ein Python-Codebeispiel, das auf einem Ubuntu Linux-Clientcomputer ausgeführt wird, um eine Verbindung mit einer Azure SQL-Datenbank herzustellen.


## Voraussetzungen


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).


### Installieren der erforderlichen Module


Öffnen Sie das Terminal, und navigieren Sie zu einem Verzeichnis, in dem Sie Ihr Python-Skript erstellen möchten. Geben Sie die folgenden Befehle zum Installieren von **FreeTDS** und **pymssql** ein. In pymssql wird FreeTDS für die Verbindung mit SQL-Datenbanken verwendet.

	sudo apt-get --assume-yes update
	sudo apt-get --assume-yes install freetds-dev freetds-bin
	sudo apt-get --assume-yes install python-dev python-pip
	sudo pip install pymssql


### Eine SQL-Datenbank

Auf der [Seite für erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.

## Schritt 1: Abrufen der Verbindungsdetails

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Schritt 2: Verbinden

!!!!!sql-database-include-connection-string-details-20-portalshots.md

Die [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html)-Funktion dient zum Herstellen einer Verbindung mit der SQL-Datenbank.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Schritt 3: Ausführen einer Abfrage

Mit der [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute)-Funktion können Sie ein Resultset aus einer Abfrage einer SQL-Datenbank abrufen. Diese Funktion akzeptiert praktisch jede Abfrage und gibt ein Resultset zurück, das mithilfe von [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone) durchlaufen werden kann.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Schritt 4: Einfügen einer Zeile

In diesem Beispiel erfahren Sie, wie Sie eine [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)-Anweisung sicher ausführen, Parameter zum Schutz Ihrer Anwendung vor einer [Einschleusung von SQL-Befehlen](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) übergeben und den automatisch generierten [Primärschlüsselwert](https://msdn.microsoft.com/library/ms179610.aspx) abrufen.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Schritt 5: Durchführen eines Rollbacks für eine Transaktion


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

## Nächste Schritte

Weitere Informationen finden Sie im [Python Developer Center](/develop/python/).

<!---HONumber=AcomDC_1210_2015-->