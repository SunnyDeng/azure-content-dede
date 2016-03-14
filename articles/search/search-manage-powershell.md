<properties 
	pageTitle="Verwalten von Azure Search mit PowerShell-Skripts | Microsoft Azure | Gehosteter Cloudsuchdienst" 
	description="Verwalten Ihres Azure Search-Diensts in Microsoft Azure mit PowerShell-Skripts. Erstellen oder Aktualisieren eines Azure Search-Diensts und Verwalten von Azure Search-Administratorschlüsseln"  
	services="search" 
	documentationCenter="" 
	authors="seansaleh" 
	manager="mblythe" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="powershell" 
	ms.date="02/25/2016" 
	ms.author="seasa"/>

# Verwalten Ihres Search-Diensts in Microsoft Azure mit PowerShell
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST-API](search-get-started-management-api.md)

In diesem Thema werden die PowerShell-Befehle zur Durchführung vieler Verwaltungsaufgaben für Azure Search-Dienste beschrieben. Wir führen Sie schrittweise durch das Erstellen und Skalieren eines Search-Diensts und die Verwaltung der zugehörigen API-Schlüssel. Diese Befehle stehen parallel zu den Verwaltungsoptionen der [Azure Search Management REST API](http://msdn.microsoft.com/library/dn832684.aspx) zur Verfügung.

## Voraussetzungen
 
- Azure PowerShell 1.0 oder höher muss installiert sein. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).
- Sie müssen in PowerShell wie im Folgenden beschrieben bei Ihrem Azure-Abonnement angemeldet sein.

Zunächst müssen Sie sich mit dem folgenden Befehl in Azure anmelden:

	Login-AzureRmAccount

Geben Sie die E-Mail-Adresse Ihres Azure-Kontos und das Kennwort in das Anmeldedialogfeld von Microsoft Azure ein.

Als Alternative steht die [Nicht interaktive Anmeldung mit einem Dienstprinzipal](../resource-group-authenticate-service-principal.md) zur Verfügung.

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie Ihr Azure-Abonnement festlegen. Führen Sie den folgenden Befehl aus, um eine Liste Ihrer aktuellen Abonnements anzuzeigen.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Führen Sie den folgenden Befehl aus, um das Abonnement anzugeben. Im folgenden Beispiel ist der Name des Abonnements `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## Befehle als Starthilfe

	$serviceName = "your-service-name-lowercase-with-dashes"
	$sku = "free" # or "standard" for a paid service
	$location = "West US"
	# You can get a list of potential locations with
	# (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
	$resourceGroupName = "YourResourceGroup" 
	# If you don't already have this resource group, you can create it with with 
	# New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

	# Register the arm provider idempotently. This must be done once per subscription
	Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

	# Create a new search service
	# This command will return once the service is fully created
	New-AzureRmResourceGroupDeployment `
		-ResourceGroupName $resourceGroupName `
		-TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
		-NameFromTemplate $serviceName `
		-Sku $sku `
		-Location $location `
		-PartitionCount 1 `
		-ReplicaCount 1
	
	# Get information about your new service and store it in $resource
	$resource = Get-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
	
	# View your resource
	$resource
	
	# Get the primary admin api key
	$primaryKey = (Invoke-AzureRmResourceAction `
		-Action listAdminKeys `
		-ResourceId ($resource.ResourceId) `
		-ApiVersion 2015-08-19).PrimaryKey

	# Regenerate the secondary admin api Key
	$secondaryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action secondary).SecondaryKey

	# Create a query key for read only access to your indexes
	$queryKeyDescription = "query-key-created-from-powershell"
	$queryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/createQueryKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action $queryKeyDescription).Key

	# Delete query key
	Remove-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
		
	# Scale your service up
	# Note that this will only work if you made a non "free" service
	# This command will not return until the operation is finished
	# It can take 15 minutes or more to provision the additional resources
	$resource.Properties.ReplicaCount = 2
	$resource | Set-AzureRmResource
	
	# Delete your service
	# Deleting your service will delete all indexes and data in the service
	$resource | Remove-AzureRmResource
	
## Nächste Schritte
	
Nachdem der Dienst erstellt wurde, können Sie die nächsten Schritte ausführen: Erstellen eines [Index](search-what-is-an-index.md), [Abfragen eines Index](search-query-overview.md) und schließlich Erstellen und Verwalten von eigenen Suchanwendungen, die Azure Search verwendet.

- [Erstellen eines Azure Search-Index im Azure-Portal](search-create-index-portal.md)

- [Abfragen eines Azure Search-Index per Suchexplorer im Azure-Portal](search-explorer.md)

- [Einrichten eines Indexers zum Laden von Daten anderer Dienste](search-indexer-overview.md)

- [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md)

- [Analysieren Ihres Azure Search-Datenverkehrs](search-traffic-analytics.md)

<!---HONumber=AcomDC_0302_2016-->