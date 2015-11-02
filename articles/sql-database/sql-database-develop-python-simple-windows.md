<properties 
	pageTitle="Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Python unter Windows" 
	description="Zeigt ein Python-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank von einem Windows-Client aus. Das Beispiel verwendet den pymssql-Treiber."
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
	ms.date="10/20/2015" 
	ms.author="meetb"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Python unter Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema zeigt ein in Python geschriebenes Codebeispiel. Das Beispiel wird auf einem Computer unter Windows ausgeführt. Das Beispiel stellt mithilfe des **pymssql**-Treibers eine Verbindung mit Azure SQL-Datenbank her.


## Anforderungen


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)


### Installieren der erforderlichen Module


Installieren Sie [pymssql](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql).

Stellen Sie sicher, dass Sie die richtige WHL-Datei auswählen.

Wählen Sie z. B. bei Verwendung von Python 2.7 auf einem 64-Bit-Computer die Datei "pymssql‑2.1.1‑cp27‑none‑win\_amd64.whl" aus. Speichern Sie die WHL-Datei nach dem Herunterladen im Ordner "C:/Python27".

Installieren Sie danach an der Befehlszeile mithilfe von pip den pymssql-Treiber. Wechseln Sie zum Laufwerk "C:/Python27", und führen Sie Folgendes aus:
	
	pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Anweisungen zum Aktivieren der Verwendung von pip erhalten Sie [hier](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).


## Erstellen einer Datenbank und Abrufen der Verbindungszeichenfolge


Im Thema [Erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank und zum Abrufen der Verbindungszeichenfolge. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.


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

## Nächste Schritte

Weitere Informationen finden Sie im [Python Developer Center](/develop/python/).

<!---HONumber=Oct15_HO4-->