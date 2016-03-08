<properties 
	pageTitle="Deinstallieren des Tools für elastische Datenbankaufträge" 
	description="Deinstallieren des Tools für elastische Datenbankaufträge" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="ddove; sidneyh"/>

#Deinstallieren von Komponenten der Aufträge für die elastische Datenbank
Komponenten der **Aufträge für die elastische Datenbank** können wahlweise mithilfe des Portals oder von PowerShell deinstalliert werden.

##Deinstallieren von Komponenten der Aufträge für die elastische Datenbank mithilfe des Azure-Portals

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com/).
2. Navigieren Sie zu dem Abonnement, das die Komponenten der **Aufträge für die elastische Datenbank** enthält, also dem Abonnement, in dem die Komponenten der Aufträge für die elastische Datenbank installiert wurden.
3. Klicken Sie auf **Durchsuchen** und dann auf **Ressourcengruppen**.
4. Wählen Sie die Ressourcengruppe mit dem Namen "\_\_ElasticDatabaseJob".
5. Löschen Sie die Ressourcengruppe.

##Deinstallieren von Komponenten der Aufträge für die elastische Datenbank mithilfe von PowerShell

1.	Öffnen Sie ein Microsoft Azure PowerShell-Befehlsfenster, und navigieren Sie zum Unterverzeichnis „tools“ im Ordner „Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x“: Geben Sie **cd tools** ein.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2.	Führen Sie das PowerShell-Skript „.\\UninstallElasticDatabaseJobs.ps1“ aus.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

Oder führen Sie alternativ einfach das folgende Skript aus, unter der Voraussetzung, dass bei der Installation der Komponenten die Standardwerte übernommen wurden:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## Nächste Schritte

Informationen zur erneuten Installation von Aufträgen für die elastische Datenbank finden Sie unter [Installieren des Diensts für Aufträge für die elastische Datenbank](sql-database-elastic-jobs-service-installation.md).

Weitere Informationen zu Aufträgen für die elastische Datenbank finden Sie unter [Übersicht über Aufträge für die elastische Datenbank](sql-database-elastic-jobs-overview.md).

<!--Image references-->

<!---HONumber=AcomDC_0224_2016-->