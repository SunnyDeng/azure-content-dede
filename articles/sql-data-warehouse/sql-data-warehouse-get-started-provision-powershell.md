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
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2015"
   ms.author="lodipalm"/>

# Erstellen von SQL Data Warehouse mithilfe von Powershell

> [AZURE.SELECTOR]
- [Azure preview portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]Damit Sie Microsoft Azure Powershell mit SQL Data Warehouse verwenden können, benötigen Sie Version 0.9.4 oder höher. Sie können die Version überprüfen, indem Sie "(Get-Module Azure).Version" in PowerShell ausführen.

## Abrufen und Ausführen der Azure PowerShell-Cmdlets
Wenn Sie Powershell nicht bereits eingerichtet haben, gehen Sie wie folgt vor:

1. Führen Sie zum Herunterladen des Azure PowerShell-Moduls den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) aus.
2. Geben Sie zum Ausführen des Moduls auf der Startseite **Microsoft Azure PowerShell** ein.
3. Falls Sie Ihr Konto noch nicht dem Computer hinzugefügt haben, führen Sie das folgende Cmdlet aus. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][]\:

            Add-AzureAccount

4. Sie müssen PowerShell auch im ARM-Modus ausführen. Sie wechseln in diesen Modus, indem Sie den folgenden Befehl ausführen:

            switch-azuremode AzureResourceManager

## Erstellen von SQL Data Warehouse
Nachdem PowerShell ordnungsgemäß unter Ihrem Konto eingerichtet ist, führen Sie Folgendes aus, um ein neues SQL Data Warehouse bereitzustellen:

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

Die erforderlichen Parameter für dieses Cmdlet lauten wie folgt:

 + **RequestedServiceObjectiveName**: die angeforderte DWU-Menge in der Form "DWXXX". Zurzeit werden folgende Werte unterstützt: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 + **DatabaseName**: der Name des SQL Data Warehouse, das Sie erstellen.
 + **ServerName**: der Name des Servers, den Sie für die Erstellung verwenden (muss V12 sein).
 + **ResourceGroupName**: die Ressourcengruppe, die Sie verwenden. Verwenden Sie zum Abrufen der in Ihrem Abonnement verfügbaren Ressourcengruppen das Cmdlet "Get-AzureResourceGroup":
 + **Edition**: Sie müssen die Edition auf "Data Warehouse" festlegen, um ein SQL Data Warehouse zu erstellen. 

## Nächste Schritte
Nach der SQL Data Warehouse-Bereitstellung können Sie [Beispieldaten laden][] oder die Schritte zum [Entwickeln][], [Laden][] oder [Migrieren][] lernen.

Weitere Informationen zur programmgesteuerten Verwaltung von SQL Data Warehouse finden Sie in der Dokumentation zu [PowerShell][] und der [REST-API][].



<!--Image references-->

<!--Article references-->
[Migrieren]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-overview-migrate/
[Entwickeln]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-overview-develop/
[Laden]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-overview-load/
[Beispieldaten laden]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[REST-API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/en-us/library/azure/dn546722.aspx
[firewall rules]: https://azure.microsoft.com/en-us/documentation/articles/sql-database-configure-firewall-settings/
[Installieren und Konfigurieren von Azure PowerShell]: powershell-install-configure.md

<!---HONumber=Oct15_HO3-->