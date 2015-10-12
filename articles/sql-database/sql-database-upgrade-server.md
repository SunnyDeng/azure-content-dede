<properties 
	pageTitle="Upgrade eines Azure SQL-Servers auf V12 mit PowerShell" 
	description="Upgrade eines Azure SQL-Servers auf V12 mit PowerShell" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2015" 
	ms.author="sstein"/>

# Upgrade auf SQL-Datenbank V12 mithilfe von PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


In diesem Artikel wird das Aktualisieren auf eine SQL-Datenbank V12 mithilfe von PowerShell veranschaulicht.

Im Verlauf der Aktualisierung auf SQL-Datenbank V12 müssen Sie auch alle Web- und Business-Datenbanken auf eine neue Dienstebene aktualisieren. Die folgenden Schritte umfassen die Verwendung von Empfehlungen zu Tarifen und elastischen Pools, um das [Aktualisieren aller Web- und Business-Datenbanken](sql-database-upgrade-new-service-tiers.md) auf dem Server zu unterstützen.


## Voraussetzungen 

Für das Upgrade eines Servers auf V12 mit PowerShell, muss Azure PowerShell installiert sein und ausgeführt werden. Je nach Version müssen Sie eventuell in den Ressourcen-Manager-Modus wechseln, um auf die PowerShell-Cmdlets für Azure-Ressourcen-Manager zuzugreifen.

Sie können die Azure PowerShell-Module herunterladen und installieren, indem Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) ausführen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

Die Cmdlets zum Erstellen und Verwalten von Azure SQL-Datenbanken befinden sich im Azure-Ressourcen-Manager-Modul. Wenn Sie Azure PowerShell starten, werden die Cmdlets im Azure-Modul standardmäßig importiert. Um zum Azure-Ressourcen-Manager-Modul zu wechseln, verwenden Sie das Cmdlet **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

Falls die Warnung "The Switch-AzureMode cmdlet is deprecated and will be removed in a future release" angezeigt wird, ignorieren Sie diese Warnung, und fahren Sie mit dem nächsten Abschnitt fort.

Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](../powershell-azure-resource-manager.md).



## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Um PowerShell-Cmdlets für Ihr Azure-Abonnement ausführen zu können, müssen Sie zunächst den Zugriff auf Ihr Azure-Konto herstellen. Führen Sie Folgendes aus. Es wird eine Anmeldeseite angezeigt, auf der Sie Ihre Anmeldeinformationen eingeben müssen. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim Azure-Portal.

	Add-AzureAccount

Nach der erfolgreichen Anmeldung sollten einige Informationen auf dem Bildschirm angezeigt werden, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.

Zur Auswahl des Abonnements, mit dem Sie arbeiten möchten, benötigen Sie Ihre Abonnement-ID (**-SubscriptionId**) oder den Abonnementnamen (**-SubscriptionName**). Sie können diese Informationen aus dem vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureSubscription** abrufen und die gewünschten Abonnementinformationen aus dem ResultSet kopieren.

Führen Sie das folgende Cmdlet mit den Informationen zu Ihrem Abonnement aus, um Ihr aktuelles Abonnement festlegen:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Die folgenden Befehle werden für das soeben ausgewählte Abonnement ausgeführt.

## Empfehlungen erhalten

Zum Abrufen der Empfehlung für das Serverupgrade führen Sie das folgende Cmdlet aus:

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Weitere Informationen finden Sie unter [Erstellen und Verwalten eines elastischen Azure SQL-Datenbankpools](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) und [Tarifempfehlungen für Azure SQL-Datenbank](sql-database-service-tier-advisor.md).



## Starten des Upgrades

Zum Starten des Upgrades des Servers führen Sie das folgende Cmdlet aus:

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Mit dem Ausführen dieses Befehls beginnt der Upgradevorgang. Sie können die Ausgabe der Empfehlung anpassen und diesem Cmdlet die bearbeitete Empfehlung bereitstellen.


## Aktualisieren eines Servers


    # Adding the account
    #
    Add-AzureAccount
    
    # Switch mode
    #
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Benutzerdefinierte Upgradezuordnung

Wenn die Vorschläge für Ihren Server und das Geschäftsszenario nicht geeignet sind, können Sie auswählen, wie das Upgrade Ihrer Datenbanken durchgeführt wird und sie entweder einzelnen oder elastischen Datenbanken zuordnen.

Die Parameter "ElasticPoolCollection" und "DatabaseCollection" sind optional:
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping
    #
    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap.Name = "DBMain" 
    $databaseMap.TargetEdition = "Standard" 
    $databaseMap.TargetServiceLevelObjective = "S0" 
     
    # Starting the upgrade
    #
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool) 
    




- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)



## Verwandte Informationen

- [Azure SQL Database Cmdlets](https://msdn.microsoft.com/library/mt163521.aspx) (in englischer Sprache)
- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)

<!---HONumber=Oct15_HO1-->