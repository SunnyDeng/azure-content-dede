<properties
	pageTitle="Erstellen eines SQL Data Warehouse mit TSQL | Microsoft Azure"
	description="Erfahren Sie, wie Sie ein Azure SQL Data Warehouse mit TSQL erstellen."
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="lodipalm"/>

# Erstellen einer SQL Data Warehouse-Datenbank mithilfe von Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Azure preview portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

In diesem Artikel erfahren Sie, wie Sie eine SQL Data Warehouse-Datenbank mithilfe von Transact-SQL (TSQL) erstellen.

## Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Visual Studio. Eine kostenlose Version von Visual Studio finden Sie auf der Seite [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs).
- Einen logischen V12-Server. Sie benötigen einen V12-SQL-Server zum Erstellen des SQL Data Warehouse. Wenn Sie keinen logischen V12-SQL-Server haben, erfahren Sie im [Azure-Vorschauportal in einem Tutorial][], wie Sie einen erstellen.

## Erstellen einer Datenbank mit Visual Studio

In diesem Artikel wird nicht behandelt, wie eine Datenbank ordnungsgemäß eingerichtet und eine Verbindung mit Visual Studio hergestellt wird. Eine vollständige Beschreibung der Vorgehensweise finden Sie in der Dokumentation zu [Verbindungsherstellung und Abfragen][]. Zum Starten öffnen Sie den SQL Server-Objekt-Explorer in Visual Studio und stellen eine Verbindung mit dem Server her, mit dessen Hilfe Sie Ihre SQL Data Warehouse-Datenbank erstellen möchten. Nachdem dies erfolgt ist, können Sie ein SQL Data Warehouse erstellen, indem Sie den folgenden Befehl auf die Datenbank "master" anwenden:

        CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>);

## Erstellen Sie eine Datenbank mit SQLCMD

Sie können ein SQL Data Warehouse auch erstellen, indem Sie die Befehlszeile öffnen und Folgendes ausführen:

        sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>)"

Beachten Sie beim Ausführen der oben genannten TSQL-Anweisungen die Parameter MAXSIZE und SERVICE\_OBJECTIVE. Diese bestimmen die anfängliche Speichergröße und Datenverarbeitungskapazität, die Ihrer Data Warehouse-Instanz zugewiesen sind. MAXSIZE akzeptiert die folgenden Größen. Wir empfehlen eine hohe Größe, um Wachstum unterstützen zu können: 250 GB, 500 GB, 750 GB, 1024 GB, 5120 GB, 10.240 GB, 20.480 GB, 30.720 GB, 40.960 GB, 51.200 GB.

SERVICE\_OBJECTIVE gibt die Anzahl der DWUs an, mit der Ihre Instanz gestartet wird, und akzeptiert die folgenden Werte: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000. Informationen zur Auswirkung dieser Parameter auf die Abrechnung finden Sie auf unserer Seite zur [Preisgestaltung][].

## Nächste Schritte
Nach der SQL Data Warehouse-Bereitstellung können Sie [Beispieldaten laden][] oder die Schritte zum [Entwickeln][], [Laden][] oder [Migrieren][] erlernen.

[Azure-Vorschauportal in einem Tutorial]: ./sql-data-warehouse-get-started-provision.md
[Verbindungsherstellung und Abfragen]: ./sql-data-warehouse-get-started-connect.md
[Migrieren]: ./sql-data-warehouse-overview-migrate.md
[Entwickeln]: ./sql-data-warehouse-overview-develop.md
[Laden]: ./sql-data-warehouse-overview-load.md
[Beispieldaten laden]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Preisgestaltung]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/

<!---HONumber=Oct15_HO4-->