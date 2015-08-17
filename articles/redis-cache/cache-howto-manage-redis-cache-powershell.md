<properties 
 pageTitle="Verwalten von Azure-Redis-Cache mit Azure PowerShell" 
 description="Erfahren Sie, wie Sie administrative Aufgaben für Azure-Redis-Cache mit Azure PowerShell ausführen." 
 services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson"  
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple" 
   ms.date="08/04/2015"
   ms.author="riande"/>

# Verwalten von Azure-Redis-Cache mit Azure PowerShell

Dieses Lernprogramm bietet eine Kurzanleitung zum Erstellen, Aktualisieren und Löschen eines Azure-Redis-Caches.

## Voraussetzungen ##

Bevor Sie Windows PowerShell mit dem Ressourcen-Manager verwenden können, benötigen Sie Folgendes:

- Windows PowerShell, Version 3.0 oder 4.0. Um die Version von Windows PowerShell zu ermitteln, geben Sie `$PSVersionTable` ein. Vergewissern Sie sich, dass der Wert von `PSVersion` 3.0 oder 4.0 ist. Informationen zum Installieren einer kompatiblen Version finden Sie unter [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).
	
- Azure PowerShell, Version 0.8.0 oder neuer. Um die neueste Version zu installieren und sie Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

Dieses Lernprogramm ist für Windows PowerShell-Einsteiger gedacht. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Um detaillierte Hilfe zu einem Cmdlet aus dem Lernprogramm zu erhalten, verwenden Sie das Get-Help-Cmdlet.

	Get-Help <cmdlet-name> -Detailed

Geben Sie beispielsweise Folgendes ein, um Hilfe zum Add-AzureAccount-Cmdlet zu erhalten:

	Get-Help Add-AzureAccount -Detailed

## Ein einfaches Azure PowerShell-Skript für den Redis-Cache  ##

Das folgende Skript veranschaulicht das Erstellen, Aktualisieren und Löschen eines Azure-Redis-Caches.

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## Nächste Schritte

Weitere Informationen zur Verwendung von Windows PowerShell mit Azure finden Sie in den folgenden Ressourcen:
 
- [Azure Resource Manager Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765) (in englischer Sprache): Verwenden der Cmdlets im AzureResourceManager-Modul.
- [Using Resource groups to manage your Azure resources](../azure-portal/resource-group-portal) (in englischer Sprache): Erstellen und Verwalten von Ressourcengruppe im Azure-Verwaltungsportal.
- [Azure-Blog](http://blogs.msdn.com/windowsazure): Informationen zu neuen Funktionen in Azure.
- [Windows PowerShell-Blog](http://blogs.msdn.com/powershell): Informationen zu neuen Funktionen in Windows PowerShell.
- ["Hey, Scripting Guy!"- Blog](http://blogs.technet.com/b/heyscriptingguy/): Praktische Tipps und Tricks aus der Windows PowerShell-Community.

<!---HONumber=August15_HO6-->