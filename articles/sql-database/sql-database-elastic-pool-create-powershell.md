<properties 
    pageTitle="Erstellen eines Pools für elastische Datenbanken (PowerShell) | Microsoft Azure" 
    description="Erfahren Sie, wie Sie mithilfe von PowerShell Ressourcen für Azure SQL-Datenbanken horizontal skalieren können, indem Sie für die Verwaltung mehrerer Datenbanken einen Pool für elastische Datenbanken erstellen." 
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# Erstellen eines Pools für elastische Datenbanken (PowerShell) 

> [AZURE.SELECTOR]
- [Azure-Portal](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Hier erfahren Sie, wie Sie einen [Pool für elastische Datenbanken](sql-database-elastic-pool.md) mit PowerShell-Cmdlets erstellen.

> [AZURE.NOTE] Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar. Wenn Sie über einen SQL-Datenbank V11-Server verfügen, können Sie in einem Schritt [mithilfe von PowerShell auf V12 aktualisieren und einen Pool erstellen](sql-database-upgrade-server-portal.md).


Sie müssen Azure PowerShell 1.0 oder höher installiert haben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).



## Erstellen eines elastischen Datenbankpools

Das Cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) erstellt den Pool für elastische Datenbanken.

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Erstellen einer neuen elastischen Datenbank in einem Pool für elastische Datenbanken

Verwenden Sie zum direkten Erstellen einer neuen Datenbank in einem Pool das Cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx), und legen Sie den **ElasticPoolName**-Parameter fest.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Verschieben einer eigenständigen Datenbank in einen Pool für elastische Datenbanken

Verwenden Sie zum Verschieben einer vorhandenen Datenbank in einen Pool das Cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx), und legen Sie den **ElasticPoolName**-Parameter fest.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Erstellen eines Pools für elastische Datenbanken (Beispiel für PowerShell)

Dieses Skript erstellt einen neuen Server. Wenn Sie also zur Eingabe eines Benutzernamens und eines Kennworts aufgefordert werden, geben Sie den Administratorbenutzernamen und das Administratorkennwort für Ihren neuen Server ein (nicht Ihre Azure-Anmeldeinformationen).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## Nächste Schritte

- [Verwalten Ihres Pools](sql-database-elastic-pool-manage-powershell.md)
- [Erstellen elastischer Aufträge](sql-database-elastic-jobs-overview.md) Elastische Aufträge erleichtern die Ausführung von T-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool.


## Referenz für elastische Datenbanken

Weitere Informationen über elastische Datenbanken und elastische Datenbankpools finden Sie unter [SQL-Datenbank – Pool für elastische Datenbanken](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0316_2016-->