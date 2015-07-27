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
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# Deinstallieren der Komponenten für elastische Datenbankaufträge

Tritt beim Installieren des Auftragsdiensts für elastische Datenbanken ein Fehler auf, löschen Sie die Ressourcengruppe für den Dienst.

## So deinstallieren Sie die Dienstkomponenten

1. Öffnen Sie das [Azure-Vorschauportal](https://ms.portal.azure.com/).
2. Navigieren Sie zum Abonnement, das den elastischen Auftrag enthält.
3. Klicken Sie auf **Durchsuchen** und dann auf **Ressourcengruppen**.
4. Wählen Sie die Ressourcengruppe mit dem Namen "__ElasticDatabaseJob".
5. Löschen Sie die Ressourcengruppe.

Wahlweise können Sie folgendes PowerShell-Skript verwenden:

1. Öffnen Sie ein [Microsoft Azure PowerShell-Fenster](../powershell-install-configure.md). 
2. Stellen Sie sicher, dass Sie das PowerShell SDK in der Version 0.8.10 oder höher verwenden.
3. Führen Sie das Skript aus:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## Nächste Schritte

Informationen zur erneuten Installation von elastischen Datenbankaufträgen finden Sie unter [Installieren des Diensts für elastische Datenbankaufträge](sql-database-elastic-jobs-service-installation.md).

Eine Übersicht über den Dienst für elastische Datenbankaufträge finden Sie unter [Übersicht über elastische Aufträge](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/
 

<!---HONumber=July15_HO3-->