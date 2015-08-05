<properties 
	pageTitle="Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank | Microsoft Azure" 
	description="Bietet Codebeispiele und Treiber Node.js unter Linux, Python unter Mac OS, Java unter Windows u. v. m. für Azure SQL-Datenbank-Clients."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="genemi"/>


# Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank


Dieses Thema enthält Links zu Schnellstart-Codebeispielen, die Sie zum Herstellen einer Verbindung mit Azure SQL-Datenbank verwenden können.


- Kurze Beispiele für Verbindungen und Abfragen.
- Beispiele für die Wiederholung von Verbindung und Abfrage und die automatische Wiederholung, wenn ein Fehler als [*vorübergehender*](http://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors) Fehler eingestuft wird (z. B. Verbindungstimeout).


Die Beispiele decken Folgendes ab:


- Eine Vielzahl von Programmiersprachen.
- Windows, Linux und Mac OS als Betriebssystem, unter dem das Clientprogramm ausgeführt werden kann.
- Links zum Download aller notwendigen Verbindungstreiber.
- Kurze Schnellstart-Codebeispiele.
- Längere Beispiele mit Behandlung vorübergehender Fehler in Form einer automatisierten Wiederholungslogik.
- Codebeispiele, die relationale Resultsets in ein objektorientiertes Format konvertieren.


> [AZURE.NOTE]Codebeispiele für weitere Sprachen werden vorbereitet. Links zu diesen Beispielen werden in diesem Thema hinzugefügt.


## Clients unter Linux


Dieser Abschnitt enthält Links zu Themen mit Codebeispielen für Clientprogramme unter Linux.


| Sprache | Kurzes Beispiel | Beispiel für Wiederholung | Relational in objektorientiert |
| :-- | :-- | :-- | :-- |
| Node.js | [Tedious](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-unbutu-linux.md) | . | . |
| Ruby | [FreeTDS, TinyTDS](sql-database-develop-ruby-simple-linux.md) | . | . |


## Clients unter Mac OS


Dieser Abschnitt enthält Links zu Themen mit Codebeispielen für Clientprogramme unter Mac OS.


| Sprache | Kurzes Beispiel | Beispiel für Wiederholung | Relational in objektorientiert |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |
| Ruby | [Homebrew<br/>FreeTDS, TinyTDS](sql-database-develop-ruby-simple-mac-osx.md) | . | . |


## Clients unter Windows


Dieser Abschnitt enthält Links zu Themen mit Codebeispielen für Clientprogramme unter Windows.


| Sprache | Kurzes Beispiel | Beispiel für Wiederholung | Relational in objektorientiert |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)<br/><br/>[ADO.NET mit Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx) | [ADO.NET Entity Framework](http://msdn.microsoft.com/library/azure/ff951633.aspx) |
| C++ | [ODBC-Treiber](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. INSERT, TRANSACTION, SELECT.](sql-database-develop-java-simple-windows.md)<br/><br/>[Java. Eclipse](sql-data-java-how-to-use-sql-database.md)<br/><br/>[Java. JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [ODBC](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## Weitere Informationen


- [Downloads von SDKs und Tools für zahlreiche Sprachen und Plattformen](http://azure.microsoft.com/downloads/#cmd-line-tools)
- [Connection Libraries for SQL Database and SQL Server](sql-database-libraries.md) (Verbindungsbibliotheken für SQL-Datenbanken und SQL Server, in englischer Sprache)
- [Liste mit numerischem Code für vorübergehende Fehler](http://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors)<br/>&nbsp;
- [Azure SQL-Datenbankentwicklung: Themen zur Vorgehensweise](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Verbindungen mit SQL-Datenbanken: Wichtige Empfehlungen](sql-database-connect-central-recommendations.md)
- [Create your first Azure SQL Database](sql-database-get-started.md) (Erstellen einer ersten Azure SQL-Datenbank, in englischer Sprache)

<!---HONumber=July15_HO4-->