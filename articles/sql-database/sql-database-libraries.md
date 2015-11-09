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
	ms.date="08/04/2015"
	ms.author="pehteh"/>


# Verbindungsbibliotheken für SQL-Datenbank und SQL Server


In diesem Thema ist die mindestens erforderliche Versionsnummer für jede Bibliothek/jeden Treiber aufgelistet, mit der Clientprogramme Verbindungen mit Azure SQL-Datenbank oder Microsoft SQL Server herstellen können.

## Tabelle der von Microsoft veröffentlichten Treiberbibliotheken


Die folgende Tabelle enthält die Bibliotheken, die von Microsoft veröffentlicht wurden. Die Spalte **Bibliotheken** enthält Links, über die Sie jede Bibliothek herunterladen können. Die Spalte **Version** listet die Mindestversion auf, die für die Interaktion mit Azure SQL-Datenbank und Microsoft SQL Server empfohlen wird.


| Plattform | Betriebssystem | Bibliotheken<br/>zum Herunterladen | Version<br/>des Treibers | Beschreibung<br/>des Treibers | Weitere<br/>Informationen |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | Plattformübergreifend (.NET) | [ADO.NET, System.Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4\.5+ | SQL Server-Anbieter für .NET Framework | . |
| PHP | Windows | [PHP für SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2\.0+ | PHP-Treiber für SQL Server | [Link](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC für SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2\.0+ | 4 JDBC-Treibertyp, der Datenbankverbindungen über die Standard-JDBC-API bereitstellt | [Link](http://msdn.microsoft.com/library/dn425070.aspx) |
| ODBC | Windows | [ODBC für SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11\.0+ | Microsoft ODBC-Treiber für SQL Server | [Link](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC für SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0+ | Microsoft ODBC-Treiber für SQL Server | . |
| ODBC | Redhat Linux | [ODBC für SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0+ | Microsoft ODBC-Treiber für SQL Server | . |

### ODBC-Unterstützung

Beim Verwenden des Assistenten für Datenquellnamen (data source name, DSN) zum Definieren einer Datenquelle für die Azure SQL-Datenbank, klicken Sie auf die Option **Mit SQL Server-Authentifizierung anhand des vom Benutzer eingegebenen Benutzernamens und Kennworts**, und wählen Sie **Zum SQL Server verbinden, um Standardeinstellungen für die zusätzlichen Konfigurationsoptionen zu erhalten** aus. Geben Sie Ihren Benutzernamen und das Kennwort zum Herstellen einer Verbindung mit dem Azure SQL-Datenbankservers als **Anmelde-ID** und **Kennwort** ein. Deaktivieren Sie das Kontrollkästchen **Zum SQL Server verbinden, um Standardeinstellungen...** . Klicken Sie auf **Die Standarddatenbank ändern auf:** und geben Sie den Namen der Azure SQL-Datenbank ein, auch wenn sie nicht in der Liste angezeigt wird. Beachten Sie, dass im Assistenten in der Liste **Sprache der SQL Server-Systemmeldungen ändern auf:** in mehrere Sprachen enthält.

In dieser Version unterstützt die Microsoft Azure SQL-Datenbank nur Englisch, wählen Sie daher Englisch als Sprache aus. Die Elemente **Spiegelserver** oder **Datenbank anfügen** werden von der Microsoft Azure SQL-Datenbank und bleiben daher leer. Klicken Sie auf **Test Connection**.

Bei Verwendung des SQL Server 2008 Native Client ODBC-Treibers führt die Schaltfläche **Testverbindung** unter Umständen zu einem Fehler, der angibt, dass **master.dbo.syscharsets** nicht unterstützt wird. Ignorieren Sie diesen Fehler, und speichern und verwenden Sie den DSN.


### OLE DB für DB2 und SQL Server für den Entwurf von DRDA


Mit dem Microsoft OLE DB-Anbieter für DB2, Version 5.0 (Datenanbieter) können Sie verteilte Anwendungen für IBM DB2-Datenbanken erstellen. Der Datenanbieter nutzt die Vorteile der Datenzugriffsarchitektur von Microsoft SQL Server in Verbindung mit einem Microsoft-Netzwerkclient für DB2, der als Anwendungsanforderung für eine verteilte relationale Datenbankarchitektur (Distributed Relational Database Architecture, DRDA) fungiert. Der Datenanbieter konvertiert OLE DB-Befehle und -Datentypen vom Microsoft Component Object Model (COM) in DRDA-Protokollcodepunkte und -Datenformate.


Weitere Informationen finden Sie unter:


- [Microsoft OLE DB-Anbieter für DB2, Version 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Microsoft OLE DB-Anbieter für DB2, Version 4. 0 für Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)


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

<!---HONumber=Nov15_HO1-->