<properties
   pageTitle="Erstellen von SQL Data Warehouse mithilfe von Powershell | Microsoft Azure"
   description="Erstellen von SQL Data Warehouse mithilfe von Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/26/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Erstellen von SQL Data Warehouse mithilfe von Powershell

> [AZURE.SELECTOR]
- [Azure-Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Abrufen und Ausführen der Azure PowerShell-Cmdlets

> [AZURE.NOTE]  Zum Verwenden von Microsoft Azure PowerShell mit SQL Data Warehouse sollten Sie die aktuelle Version von Azure PowerShell mit ARM-Cmdlets herunterladen und installieren. Sie können Ihre Version überprüfen, indem Sie `Get-Module -ListAvailable -Name Azure` ausführen. Dieser Artikel basiert auf Microsoft Azure PowerShell Version 1.0.3 oder höher.

Wenn Sie PowerShell noch nicht eingerichtet haben, müssen Sie PowerShell herunterladen und konfigurieren.

1. Führen Sie zum Herunterladen des Azure PowerShell-Moduls den [Microsoft-Webplattform-Installer](http://aka.ms/webpi-azps) aus. Weitere Informationen zu diesem Installationsprogramm finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][].
2. Geben Sie zum Ausführen des Moduls auf der Startseite **Windows PowerShell** ein.
3. Führen Sie dieses Cmdlet aus, um sich am Azure-Ressourcen-Manager anzumelden.

	```Powershell
	Login-AzureRmAccount
	```

4. Wählen Sie das Abonnement aus, das Sie für Ihre aktuelle Sitzung verwenden möchten.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

## Erstellen einer SQL Data Warehouse-Datenbank
Verwenden Sie das New-AzureRmSQLDatabase-Cmdlet, um eine SQL Data Warehouse-Einheit bereitzustellen. Stellen Sie vor dem Ausführen des Befehls sicher, dass die unten angegebenen Voraussetzungen erfüllt sind.

### Voraussetzungen

- V12 Azure SQL Server zum Hosten der Datenbank
- Ressourcengruppenname für SQL Server liegt vor

### Bereitstellungsbefehl

Mit diesem Befehl wird eine neue Datenbank in SQL Data Warehouse bereitgestellt.

```Powershell
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

In diesem Beispiel wird eine neue Datenbank mit dem Namen „mynewsqldw1“ mit der Dienstzielebene „DW400“ auf dem Server „sqldwserver1“ bereitgestellt, der sich in der Ressourcengruppe „mywesteuroperesgp1“ befindet.

```Powershell
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

Die erforderlichen Parameter für dieses Cmdlet lauten wie folgt:

 + **RequestedServiceObjectiveName**: die angeforderte DWU-Menge in der Form "DWXXX". Zurzeit werden folgende Werte unterstützt: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 + **DatabaseName**: der Name des SQL Data Warehouse, das Sie erstellen.
 + **ServerName**: der Name des Servers, den Sie für die Erstellung verwenden (muss V12 sein).
 + **ResourceGroupName**: die Ressourcengruppe, die Sie verwenden. Verwenden Sie zum Abrufen der in Ihrem Abonnement verfügbaren Ressourcengruppen das Cmdlet "Get-AzureResourceGroup":
 + **Edition**: Sie müssen die Edition auf "Data Warehouse" festlegen, um ein SQL Data Warehouse zu erstellen.

Die Befehlsreferenz finden Sie unter [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx).

Die Parameteroptionen finden Sie unter [CREATE DATABASE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx).

## Nächste Schritte
Nach der SQL Data Warehouse-Bereitstellung können Sie [Beispieldaten laden][] oder die Schritte zum [Entwickeln][], [Laden][] oder [Migrieren][] erlernen.

Weitere Informationen zur programmgesteuerten Verwaltung von SQL Data Warehouse finden Sie in der Dokumentation zu [PowerShell][] und der [REST-API][].



<!--Image references-->

<!--Article references-->
[Migrieren]: ./sql-data-warehouse-overview-migrate.md
[Entwickeln]: ./sql-data-warehouse-overview-develop/.md
[Beispieldaten laden]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[REST-API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[Installieren und Konfigurieren von Azure PowerShell]: ./powershell-install-configure.md

<!---HONumber=AcomDC_0330_2016-->