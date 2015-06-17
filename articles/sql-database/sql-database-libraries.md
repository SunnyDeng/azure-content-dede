<properties
	pageTitle="Verbindungsbibliotheken für SQL-Datenbank und SQL Server"
	description="Listet die mindestens erforderliche Versionsnummer für jeden Treiber auf, mit der Clientprogramme Verbindungen mit Azure SQL-Datenbank oder Microsoft SQL Server herstellen können. Es wird ein Link zu Versionsinformationen für die Treiber bereitgestellt, die nicht von Microsoft, sondern von der Community freigegeben werden."
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/01/2015"
	ms.author="pehteh"/>


# Verbindungsbibliotheken für SQL-Datenbank und SQL Server


In diesem Thema ist die mindestens erforderliche Versionsnummer für jede Bibliothek/jeden Treiber aufgelistet, mit der Clientprogramme Verbindungen mit Azure SQL-Datenbank oder Microsoft SQL Server herstellen können.


Dieses Thema ist in zwei Abschnitte unterteilt:


- *Tabelle der von Microsoft veröffentlichten Treiberbibliotheken*: umfasst Bibliotheken, die von Microsoft veröffentlicht wurden. Microsoft verwaltet die Informationen in diesem Abschnitt.
- *Bibliotheken von Drittanbietern*: listet Bibliotheken auf, die von Drittanbietern und nicht von Microsoft verwaltet und veröffentlicht werden. **Dieser Abschnitt wird nur von der öffentlichen Entwicklercommunity verwaltet. Microsoft wartet diesen Abschnitt nicht.**


## Tabelle der von Microsoft veröffentlichten Treiberbibliotheken


Die folgende Tabelle enthält die Bibliotheken, die von Microsoft veröffentlicht wurden. Die Spalte **Bibliotheken** enthält Links, über die Sie jede Bibliothek herunterladen können. Die Spalte **Version** listet die Mindestversion auf, die für die Interaktion mit Azure SQL-Datenbank und Microsoft SQL Server empfohlen wird.


| Plattform | Betriebssystem | Bibliotheken | Version | Beschreibung |
| :--- | :--- | :--- | :--- | :--- |
| .NET | Plattformübergreifend (.NET) | [ADO.NET, System.Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4.5+ | SQL Server-Anbieter für .NET Framework |
| PHP | Windows | [PHP für SQL Server](http://www.microsoft.com/de-de/download/details.aspx?id=20098) | 2.0+ | PHP-Treiber für SQL Server |
| Java | Windows | [JDBC für SQL Server](https://www.microsoft.com/de-de/download/details.aspx?id=11774) | 2.0+ | 4 JDBC-Treibertyp, der Datenbankverbindungen über die Standard-JDBC-API bereitstellt |
| ODBC | Windows | [ODBC für SQL Server](http://www.microsoft.com/de-de/download/details.aspx?id=36434) | 11.0+ | Microsoft ODBC-Treiber für SQL Server |
| ODBC | Suse Linux | [ODBC für SQL Server](http://www.microsoft.com/de-de/download/details.aspx?id=34687) | 11.0+ | Microsoft ODBC-Treiber für SQL Server |
| ODBC | Redhat Linux | [ODBC für SQL Server](http://www.microsoft.com/de-de/download/details.aspx?id=34687) | 11.0+ | Microsoft ODBC-Treiber für SQL Server |


## Bibliotheken von Drittanbietern


> [AZURE.IMPORTANT]Die folgende Tabelle enthält die Bibliotheken, die von Drittanbietern zu deren Lizenzbedingungen veröffentlicht werden. Sie sind selbst verantwortlich für die Überprüfung und die Einhaltung der entsprechenden Drittanbieter-Lizenzen zur Verwendung dieser Bibliotheken. Sie tragen das Risiko der Verwendung dieser Bibliotheken. Microsoft übernimmt keine Garantien, weder ausdrücklich noch konkludent, in Bezug auf die hier bereitgestellten Informationen und stellt diese Informationen lediglich aus Gründen der Vereinfachung für die Benutzer zur Verfügung. Kein Teil dieses Dokuments wird auf irgendeine Art durch Microsoft unterstützt. <br/><br/>Es ist Aufgabe der öffentlichen Entwicklercommunity, die Informationen in diesem Abschnitt "Bibliotheken von Drittanbietern" mithilfe des [azure-content](http://github.com/Azure/azure-content/)-Repositorys, das sich im Besitz von **Azure** auf GitHub.com befindet, zu aktualisieren und zu verwalten. Microsoft empfiehlt Entwicklern, diesen Abschnitt zu aktualisieren. Microsoft-Mitarbeiter beabsichtigen *nicht*, die Informationen in diesem Abschnitt zu aktualisieren, da andere Personen mehr Expertise bei den einzelnen Drittanbieterbibliotheken aufweisen. Vielen Dank.


Die folgende Tabelle enthält die Bibliotheken, die von Drittanbietern, wie anderen Unternehmen oder der Community, veröffentlicht werden. Bibliotheken, die von Microsoft veröffentlichten wurden, sind auf den Abschnitt weiter oben in diesem Thema beschränkt.


| Plattform | Bibliotheken |
| :-- | :-- |
| Python | [pymssql *(org, stable)*](http://pymssql.org/en/stable/)<br/><br/>[pymssql *(org)*](http://pymssql.org/) |
| Node.js | [Tedious *(npmjs)*](http://www.npmjs.com/package/tedious) |
| Node.js | [Node-MSSQL *(github, patriksimek)*](https://github.com/patriksimek/node-mssql)<br/><br/>[Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| Node.js | [Edge.js *(github com, tjanczuk)*](https://github.com/tjanczuk/edge)<br/><br/>[Edge.js *(tjanczuk, github io)*](http://tjanczuk.github.io/edge/) |
| . | [FreeTDS *(org)*](http://www.freetds.org/) |


<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=58--> 