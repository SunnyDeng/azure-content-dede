<properties
   pageTitle="Migrieren zur SQL-Datenbank per Transaktionsreplikation"
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Datenbank importieren, Transaktionsreplikation"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Migrieren der SQL Server-Datenbank zur SQL-Datenbank per Transaktionsreplikation

Wenn Sie es sich nicht leisten können, Ihre SQL Server-Datenbank während der Migration aus der Produktion herauszunehmen, können Sie die SQL Server-Transaktionsreplikation als Migrationslösung verwenden. Bei der Transaktionsreplikation werden alle Änderungen an Ihren Daten oder Schemas, die zwischen dem Beginn der Migration und dem Abschluss der Migration erfolgen, in Ihrer Azure SQL-Datenbank angezeigt. Nach Abschluss die Migration müssen Sie nur die Verbindungszeichenfolge Ihrer Anwendungen ändern, um dafür auf Ihre Azure SQL-Datenbank zu verweisen, anstatt auf Ihre lokale Datenbank. Sobald mit der Transaktionsreplikation alle verbleibenden Änderungen Ihrer lokalen Datenbank abgearbeitet wurden und alle Anwendungen auf Azure DB zeigen, können Sie die Replikation sicher deinstallieren und Azure SQL-Datenbank als Produktionssystem nutzen.

 ![SeedCloudTR-Diagramm](./media/sql-database-cloud-migrate/SeedCloudTR.png)


Die Transaktionsreplikation ist eine Technologie, die seit SQL Server 6.5 in SQL Server integriert ist. Es handelt sich hierbei um eine sehr ausgereifte und bewährte Technologie, für die die meisten Datenbankadministratoren über Erfahrung verfügen. Mit der [SQL Server 2016-Vorschau](http://www.microsoft.com/server-cloud/products/sql-server-2016/) kann die Azure SQL-Datenbank als [Transaktionsreplikationsabonnent](https://msdn.microsoft.com/library/mt589530.aspx) für die lokale Veröffentlichung konfiguriert werden. Wenn Sie die Einrichtung über Management Studio durchführen, erhalten Sie genau das gleiche Ergebnis wie beim Einrichten eines Abonnenten der Transaktionsreplikation auf einem lokalen Server. Der Support für dieses Szenario ist gegeben, wenn der Herausgeber und der Verteiler mindestens über eine der folgenden SQL Server-Versionen verfügt:

 - SQL Server 2016 CTP3 (Vorschau) und höher 
 - SQL Server 2014 SP1 CU3 und höher
 - SQL Server 2014 RTM CU10 und höher
 - SQL Server 2012 SP2 CU8 und höher
 - SQL Server 2012 SP3 

Sie können die Transaktionsreplikation auch zum Migrieren einer Teilmenge Ihrer lokalen Datenbank verwenden. Die Veröffentlichung, die Sie zu Azure SQL-Datenbank replizieren, kann auf eine Teilmenge der Tabellen in der replizierten Datenbank beschränkt werden. Für jede replizierte Tabelle können Sie die Daten außerdem auf eine Teilmenge der Zeilen bzw. eine Teilmenge der Spalten beschränken.

<!---HONumber=AcomDC_0302_2016-->