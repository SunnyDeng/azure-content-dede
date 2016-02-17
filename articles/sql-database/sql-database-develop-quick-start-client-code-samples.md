<properties 
	pageTitle="Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank | Microsoft Azure" 
	description="Bietet Codebeispiele und Treiber für Node.js unter Linux, Python, unter Mac OS, Java und Windows, Enterprise Library u. v. m. für Azure SQL-Datenbank-Clients."
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
	ms.date="01/10/2016" 
	ms.author="genemi"/>


# Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank


Dieses Thema enthält Links zu Schnellstart-Codebeispielen, die Sie zum Herstellen einer Verbindung mit Azure SQL-Datenbank verwenden können.


- Kurze Beispiele für Verbindungen und Abfragen.
- Beispiele für die Wiederholung von Verbindung und Abfrage und die automatische Wiederholung, wenn ein Fehler als [*vorübergehender*](sql-database-develop-error-messages.md#bkmk_connection_errors) Fehler eingestuft wird (z. B. Verbindungstimeout).


Die Beispiele decken Folgendes ab:


- Eine Vielzahl von Programmiersprachen.
- Windows, Linux und Mac OS als Betriebssystem, unter dem das Clientprogramm ausgeführt werden kann.
- Links zum Download aller notwendigen Verbindungstreiber.
- Kurze Schnellstart-Codebeispiele.
- Längere Beispiele mit Behandlung vorübergehender Fehler in Form einer automatisierten Wiederholungslogik.
- Codebeispiele, die relationale Resultsets in ein objektorientiertes Format konvertieren.


> [AZURE.NOTE] Codebeispiele für weitere Sprachen werden vorbereitet. Links zu diesen Beispielen werden in diesem Thema hinzugefügt.


## Clients unter Linux


Dieser Abschnitt enthält Links zu Themen mit Codebeispielen für Clientprogramme unter Linux.


| Sprache | Kurzes Beispiel | Beispiel für Wiederholung | Relational in objektorientiert |
| :-- | :-- | :-- | :-- |
| Node.js | [Tedious](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-ubuntu-linux.md) | . | . |
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
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET benutzerdefiniert](sql-database-develop-csharp-retry-windows.md)<br/><br/>[Enterprise Library](sql-database-develop-entlib-csharp-retry-windows.md) | (Entity Framework) |
| C++ | [ODBC-Treiber](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. Insert, Transaction, Select.](sql-database-develop-java-simple-windows.md) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [ODBC (benutzerdefiniert)](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## Weitere Informationen


- [Downloads von SDKs und Tools für zahlreiche Sprachen und Plattformen](https://azure.microsoft.com/downloads/#cmd-line-tools)
- [Connection Libraries for SQL Database and SQL Server (Verbindungsbibliotheken für SQL-Datenbanken und SQL Server, in englischer Sprache)](sql-database-libraries.md)
- [Liste mit numerischem Code für vorübergehende Fehler](sql-database-develop-error-messages.md#bkmk_connection_errors)<br/>&nbsp;
- [Azure SQL-Datenbankentwicklung: Themen zur Vorgehensweise](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Verbindungen mit SQL-Datenbanken: Wichtige Empfehlungen](sql-database-connect-central-recommendations.md)
- [Create your first Azure SQL Database (Erstellen einer ersten Azure SQL-Datenbank, in englischer Sprache)](sql-database-get-started.md)
- [Entity Framework 6 hier, EF 7 auf GitHub](http://entityframework.codeplex.com/)

<!---HONumber=AcomDC_0128_2016-->