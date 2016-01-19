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
   ms.date="01/11/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Erstellen von SQL Data Warehouse mithilfe von Powershell

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]Damit Sie Microsoft Azure Powershell mit SQL Data Warehouse verwenden können, benötigen Sie Version 1.0 oder höher. Sie können die Version überprüfen, indem Sie „(Get-Module Azure).Version“ in PowerShell ausführen.

## Abrufen und Ausführen der Azure PowerShell-Cmdlets
Wenn Sie PowerShell noch nicht eingerichtet haben, müssen Sie PowerShell herunterladen und konfigurieren.

1. Führen Sie zum Herunterladen des Azure PowerShell-Moduls den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) aus.
2. Geben Sie zum Ausführen des Moduls auf der Startseite **Microsoft Azure PowerShell** ein.
3. Falls Sie Ihr Konto noch nicht dem Computer hinzugefügt haben, führen Sie das folgende Cmdlet aus. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][]\:

```
Add-AzureAccount
```

4. Wählen Sie das Abonnement aus, das Sie verwenden möchten. In diesem Beispiel wird die Liste der Abonnementnamen abgerufen. Anschließend wird als Abonnementname „MySubscription“ festgelegt. 

```
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```
   
## Erstellen von SQL Data Warehouse
Nachdem Sie PowerShell für Ihr Konto konfiguriert haben, können Sie folgenden Code ausführen, um eine neue Datenbank in SQL Data Warehouse bereitzustellen.

```
New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

Die erforderlichen Parameter für dieses Cmdlet lauten wie folgt:

 + **RequestedServiceObjectiveName**: die angeforderte DWU-Menge in der Form "DWXXX". Zurzeit werden folgende Werte unterstützt: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 + **DatabaseName**: der Name des SQL Data Warehouse, das Sie erstellen.
 + **ServerName**: der Name des Servers, den Sie für die Erstellung verwenden (muss V12 sein).
 + **ResourceGroupName**: die Ressourcengruppe, die Sie verwenden. Verwenden Sie zum Abrufen der in Ihrem Abonnement verfügbaren Ressourcengruppen das Cmdlet "Get-AzureResourceGroup":
 + **Edition**: Sie müssen die Edition auf "Data Warehouse" festlegen, um ein SQL Data Warehouse zu erstellen. 

Die Befehlsreferenz finden Sie unter [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx).

Die Parameteroptionen finden Sie unter [Erstellen von Datenbanken (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx).

## Nächste Schritte
Nach der Bereitstellung von SQL Data Warehouse können Sie [Beispieldaten laden][] oder sich mit dem [Entwickeln][], [Laden][] oder [Migrieren][] befassen.

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

<!---HONumber=AcomDC_0114_2016-->