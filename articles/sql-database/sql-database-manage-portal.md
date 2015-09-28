<properties 
	pageTitle="Verwalten von Azure SQL-Datenbanken über das Azure-Verwaltungsportal" 
	description="Erfahren Sie, wie Sie das Azure-Verwaltungsportal verwenden, um eine relationale Datenbank mithilfe des Azure-Verwaltungsportals in der Cloud zu verwalten." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="09/11/2015" 
	ms.author="sstein"/>


# Verwalten von Azure SQL-Datenbank über das Azure-Verwaltungsportal


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

Das [Azure-Verwaltungsportal][Management Portal] ermöglicht das Erstellen, Überwachen und Verwalten von Azure SQL-Datenbanken und -Servern. In diesem Artikel werden die Datenbankvorgänge erläutert, die mit dem Verwaltungsportal ausgeführt werden können.

>[AZURE.NOTE]Wenn Ihnen das Azure-Verwaltungsportal nicht vertraut ist, erhalten Sie in diesem [Video][Azure Portal Tour] (in englischer Sprache) einen kurzen Überblick über die allgemeinen Funktionen und Konzepte.

![Datenbankübersicht](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\. Aktionen zur Datenbankverwaltung
![Datenbankverwaltungsaktionen](./media/sql-database-manage-portal/sqldatabase_actions.png)

Das Azure-Verwaltungsportal bietet eine Reihe von allgemeinen Datenbankaktionen, auf die oben auf dem Blatt "Datenbank" zugegriffen werden kann. Sie können eine Datenbank zu einem früheren Zeitpunkt wiederherstellen, eine Datenbank in Visual Studio öffnen, eine Datenbank auf einen neuen Server kopieren und die Datenbank in ein Azure-Speicherkonto exportieren.

- [Wiederherstellen einer SQL-­Datenbank](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Öffnen einer SQL-Datenbank in Visual Studio](sql-database-connect-query.md)
- [Exportieren einer SQL-­Datenbank](sql-database-export.md)

## 2\. Datenbanküberwachung
![Datenbanküberwachung](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Azure SQL-Datenbanken verfügen standardmäßig über Überwachungsdiagramme für die Datenbankdurchsatzeinheit (DTU), die Größe der Datenbank und die Verbindungsintegrität. Diese Überwachungsdiagramme können angepasst und erweitert werden, um außerdem die CPU-Nutzung, den Daten-E/A-Prozentsatz, Deadlocks, den Protokoll-E/A-Prozentsatz oder sogar den Prozentsatz der Anforderungen anzuzeigen, die von der Firewall blockiert werden. Weitere Informationen zum Anpassen der Überwachungsdiagrammen finden Sie [hier][Azure part monitoring].

Darüber hinaus können Regeln für Warnhinweise eingerichtet werden, um eine angegebene Metrik zu überwachen und einen bestimmten Administrator und Co-Administrator zu warnen, wenn vordefinierte Grenzwerte erreicht werden. Weitere Informationen zum Einrichten von Regeln für Warnhinweise im Azure-Verwaltungsportal finden Sie [hier][Azure part monitoring].

## 3\. Datenbanksicherheit und Überwachung
![Datenbanksicherheit](./media/sql-database-manage-portal/sqldatabase_security.png)

Azure SQL-Datenbanken können so konfiguriert werden, dass alle Datenbankereignisse verfolgt werden und in ein Überwachungsprotokoll in Ihrem Azure-Speicherkonto geschrieben werden. Diese Funktion kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.

- [SQL-Datenbanküberwachung](sql-database-auditing-get-started.md)

Azure SQL-Datenbanken können auch zum Maskieren vertraulicher Daten für nicht berechtigte Benutzer konfiguriert werden.

- [Dynamische Datenmaskierung](sql-database-dynamic-data-masking-get-started.md)


## 4\. Georeplikation
![Georeplikation](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Azure SQL-Datenbanken können konfiguriert werden, um bereits übergebene Transaktionen asynchron in eine sekundäre Datenbank zu replizieren. Mit der Georeplikation im Verwaltungsportal können Sie die Azure-Region wählen, in der die sekundäre Datenbank gespeichert werden soll.

- [Georeplikation](https://msdn.microsoft.com/library/azure/dn783447.aspx)





##Zusätzliche Ressourcen
* [SQL-Datenbank](sql-database-technical-overview.md)   
* [Überwachen der SQL-Datenbank mit dynamischen Verwaltungssichten][]   
* [Transact-SQL-Referenz (SQL-Datenbank)][]
  
  [Azure Portal Tour]: https://go.microsoft.com/fwlink/?LinkID=522341
  [Management Portal]: https://portal.azure.com
  [Azure part monitoring]: ../documentdb-monitor-accounts.md
  [AzureDb management overview]: http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/
  [Introducing SQL Database]: http://azure.microsoft.com/services/sql-database
  [Database geo-replication]: http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/
  [Managing Azure SQL Database using SQL Server Management Studio]: sql-database-manage-azure-ssms.md
  [Überwachen der SQL-Datenbank mit dynamischen Verwaltungssichten]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Transact-SQL-Referenz (SQL-Datenbank)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [AzureDb Auditing]: http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/
  [AzureDb datamasking]: http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/

 
 

<!---HONumber=Sept15_HO3-->