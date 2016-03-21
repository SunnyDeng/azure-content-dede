<properties
	pageTitle="Verwalten von Azure-Redis-Cache mit Azure PowerShell | Microsoft Azure"
	description="Erfahren Sie, wie Sie administrative Aufgaben für Azure-Redis-Cache mit Azure PowerShell ausführen."
	services="redis-cache"
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="sdanie"/>

# Verwalten von Azure-Redis-Cache mit Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure-Befehlszeilenschnittstelle](cache-manage-cli.md)

Dieses Thema zeigt, wie Sie gängige Aufgaben wie Erstellen, Aktualisieren und Skalieren Ihrer Azure Redis Cache-Instanzen ausführen, Zugriffsschlüssel neu generieren und Ihre Caches anzeigen. Eine vollständige Liste der Azure Redis Cache-PowerShell-Cmdlets finden Sie unter [Azure Redis Cache-Cmdlets](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](#classic) werden weiter unten in diesem Artikel beschrieben.

## Voraussetzungen

Wenn Sie bereits Azure PowerShell installiert haben, benötigen Sie Version 1.0.0 oder höher. Sie können die von Ihnen installierte Azure Power Shell-Version mit diesem Befehl über die Azure PowerShell-Eingabeaufforderung prüfen.

	Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Zunächst müssen Sie sich mit dem folgenden Befehl in Azure anmelden.

	Login-AzureRmAccount

Geben Sie die E-Mail-Adresse Ihres Azure-Kontos und das Kennwort in das Anmeldedialogfeld von Microsoft Azure ein.

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie als Nächstes Ihr Azure-Abonnement festlegen. Führen Sie den folgenden Befehl aus, um eine Liste Ihrer aktuellen Abonnements anzuzeigen.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Führen Sie den folgenden Befehl aus, um das Abonnement anzugeben. Im folgenden Beispiel ist der Name des Abonnements `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Bevor Sie Windows PowerShell mit dem Azure-Ressourcen-Manager verwenden können, benötigen Sie Folgendes:

- Windows PowerShell, Version 3.0 oder 4.0. Um die Version von Windows PowerShell zu ermitteln, geben Sie `$PSVersionTable` ein. Vergewissern Sie sich, dass der Wert von `PSVersion` 3.0 oder 4.0 ist. Informationen zum Installieren einer kompatiblen Version finden Sie unter [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Um detaillierte Hilfe zu einem Cmdlet aus dem Lernprogramm zu erhalten, verwenden Sie das „Get-Help“-Cmdlet.

	Get-Help <cmdlet-name> -Detailed

Geben Sie beispielsweise Folgendes ein, um Hilfe zum `New-AzureRmRedisCache`-Cmdlet zu erhalten:

	Get-Help New-AzureRmRedisCache -Detailed

## Herstellen einer Verbindung mit der Azure Government-Cloud oder Azure China

Standardmäßig ist die Azure-Umgebung `AzureCloud`, was die globale Azure-Cloudinstanz darstellt. Verwenden Sie zum Verbinden mit einer anderen Instanz den `Add-AzureRmAccount`-Befehl mit dem Befehlszeilenschalter `-Environment` oder -`EnvironmentName` mit der gewünschten Umgebung bzw. dem gewünschten Umgebungsnamen.

Führen Sie zum Anzeigen der Liste der verfügbaren Umgebungen das Cmdlet `Get-AzureRmEnvironment` aus.

### Verbinden mit der Azure Government-Cloud

Verwenden Sie zum Verbinden mit der Azure Government-Cloud einen der folgenden Befehle.

	Add-AzureRMAccount -EnvironmentName AzureUSGovernment

oder

	Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Verwenden Sie zum Erstellen eines Caches in der Azure Government-Cloud einen der folgenden Standorte.

-	US Government, Virginia
-	US Government, Iowa

Weitere Informationen zur Azure Government-Cloud finden Sie unter [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) und im [Microsoft Azure Government-Entwicklerhandbuch](../azure-government-developer-guide.md).

### Verbinden mit der Azure China-Cloud

Verwenden Sie zum Verbinden mit der Azure China-Cloud einen der folgenden Befehle.

	Add-AzureRMAccount -EnvironmentName AzureChinaCloud

oder

	Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Verwenden Sie zum Erstellen eines Caches in der Azure China-Cloud einen der folgenden Standorte.

-	China, Osten
-	China, Norden

Weitere Informationen zur Azure China-Cloud finden Sie unter [AzureChinaCloud für Azure wird in China von 21Vianet betrieben](http://www.windowsazure.cn/).

## Eigenschaften, die für Azure Redis Cache-PowerShell verwendet werden

Die folgende Tabelle enthält Eigenschaften und Beschreibungen für Parameter, die beim Erstellen und Verwalten Ihrer Azure Redis Cache-Instanzen mithilfe von Azure PowerShell häufig verwendet werden.

| Parameter | Beschreibung | Standard |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Name | Name des Caches | |
| Standort | Ort des Caches | |
| ResourceGroupName | Name der Ressourcengruppe, in der der Cache erstellt werden soll | |
| Größe | Die Größe des Caches. Gültige Werte sind: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 MB, 1 GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB | 1 GB |
| ShardCount | Die Anzahl der zu erstellenden Shards beim Erstellen eines Premium-Caches mit Clusterunterstützung. Gültige Werte sind: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU | Gibt die SKU des Caches an. Gültige Werte sind: Basic, Standard, Premium | Standard |
| RedisConfiguration | Gibt die Redis-Konfigurationseinstellungen für „maxmemory-delta“, „maxmemory-policy“ und „notify-keyspace-events“ an. Beachten Sie, dass „maxmemory-delta“ und „notify-keyspace-events“ nur für Standard- und Premium-Caches verfügbar sind. | |
| EnableNonSslPort | Gibt an, ob der Nicht-SSL-Port aktiviert ist. | False |
| MaxMemoryPolicy | Dieser Parameter ist veraltet – verwenden Sie stattdessen RedisConfiguration. | |
| StaticIP | Wenn Sie den Cache in einem VNET hosten, geben Sie hiermit eine eindeutige IP-Adresse im Subnetz für den Cache an. Wird keine IP-Adresse angegeben, wird eine für Sie aus dem Subnetz ausgewählt. | |
| Subnetz | Wenn Sie den Cache in einem VNET hosten, geben Sie hiermit den Namen des Subnetzes an, in dem der Cache bereitgestellt wird. | |
| VirtualNetwork | Wenn Sie den Cache in einem VNET hosten, geben Sie hiermit die Ressourcen-ID des VNET an, in dem der Cache bereitgestellt wird. | |
| KeyType | Gibt an, welcher Zugriffsschlüssel beim Erneuern der Zugriffsschlüssel neu generiert werden muss. Gültige Werte sind: Primary, Secondary | | | |


## So erstellen Sie einen Redis-Cache

Neue Azure Redis Cache-Instanzen werden mit dem [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx)-Cmdlet erstellt.

>[AZURE.IMPORTANT] Beim ersten Erstellen eines Redis-Caches in einem Abonnement mithilfe des Azure-Portals registriert das Portal den `Microsoft.Cache`-Namespace für dieses Abonnement. Wenn Sie versuchen, den ersten Redis-Cache in einem Abonnement mithilfe von PowerShell zu erstellen, müssen Sie zunächst diesen Namespace mit dem folgenden Befehl registrieren; andernfalls tritt bei Ausführung von Cmdlets wie `New-AzureRmRedisCache` und `Get-AzureRmRedisCache` ein Fehler auf.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Um eine Liste der verfügbaren Parameter und ihrer Beschreibungen für `New-AzureRmRedisCache` anzuzeigen, führen Sie den folgenden Befehl aus.

	PS C:\> Get-Help New-AzureRmRedisCache -detailed
	
	NAME
	    New-AzureRmRedisCache
	
	SYNOPSIS
	    Creates a new redis cache.
	
	SYNTAX
	    New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
	    [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
	    <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
	    [<CommonParameters>]
	
	DESCRIPTION
	    The New-AzureRmRedisCache cmdlet creates a new redis cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to create.
	
	    -ResourceGroupName <String>
	        Name of resource group in which to create the redis cache.
	
	    -Location <String>
	        Location in which to create the redis cache.
	
	    -RedisVersion <String>
	        RedisVersion is deprecated and will be removed in future release.
	
	    -Size <String>
	        Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
	        C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
	
	    -Sku <String>
	        Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
	
	    -MaxMemoryPolicy <String>
	        The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
	        MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
	
	    -RedisConfiguration <Hashtable>
	        All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
	        rdb-backup-frequency, maxmemory-delta, maxmemory-policy, notify-keyspace-events, maxmemory-samples,
	        slowlog-log-slower-than, slowlog-max-len, list-max-ziplist-entries, list-max-ziplist-value,
	        hash-max-ziplist-entries, hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries,
	        zset-max-ziplist-value etc.
	
	    -EnableNonSslPort <Boolean>
	        EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
	        non-SSL port will be disabled. Possible values are true and false.

	    -ShardCount <Integer>
	        The number of shards to create on a Premium Cluster Cache.
	
	    -VirtualNetwork <String>
	        The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format:
	        /subscriptions/{subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
	
	    -Subnet <String>
	        Required when deploying a redis cache inside an existing Azure Virtual Network.
	
	    -StaticIP <String>
	        Required when deploying a redis cache inside an existing Azure Virtual Network.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Um einen Cache mit Standardparametern zu erstellen, führen Sie den folgenden Befehl aus.

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name` und `Location` sind erforderliche Parameter, aber die übrigen sind optional und haben Standardwerte. Mit dem vorherigen Befehl erstellen Sie eine Standard-SKU-Azure Redis Cache-Instanz mit Name, Speicherort und Ressourcengruppe gemäß der entsprechenden Angaben und einer Größe von 1 GB, für die der Nicht-SSL-Port deaktiviert ist.

Um einen Premium-Cache zu erstellen, geben Sie eine Größe von P1 (6 bis 60 GB), P2 (13 bis 130 GB), P3 (26 bis 260 GB), oder P4 (53 bis 530 GB) ein. Um Clustering zu aktivieren, geben Sie eine Shardanzahl mit dem Parameter `ShardCount` ein. Das folgende Beispiel erstellt einen P1-Premium-Cache mit 3 Shards. Ein P1-Premium-Cache ist 6 GB groß, und da wir drei Shards angegeben haben, beträgt die Gesamtgröße 18 GB (3 x 6 GB).

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Um Werte für die `RedisConfiuration`-Parameter anzugeben , schließen Sie die Werte als Schlüssel-/Wertpaare in `{}` ein, wie z. B. `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. Im folgenden Beispiel wird ein standardmäßiger 1 GB-Cache mit `allkeys-random`-maxmemory-Richtlinie und mit `KEA` konfigurierten Keyspace-Benachrichtigungen erstellt. Weitere Informationen finden Sie unter [Keyspace-Benachrichtigungen (Erweiterte Einstellungen)](cache-configure.md#keyspace-notifications-advanced-settings) und [maxmemory-Policy und maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

## So aktualisieren Sie einen Redis-Cache

Azure Redis Cache-Instanzen werden mit dem [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx)-Cmdlet aktualisiert.

Um eine Liste der verfügbaren Parameter und ihrer Beschreibungen für `Set-AzureRmRedisCache` anzuzeigen, führen Sie den folgenden Befehl aus.

	PS C:\> Get-Help Set-AzureRmRedisCache -detailed
	
	NAME
	    Set-AzureRmRedisCache
	
	SYNOPSIS
	    Set redis cache updatable parameters.
	
	SYNTAX
	    Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
	    [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
	    <Integer>] [<CommonParameters>]
	
	DESCRIPTION
	    The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to update.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    -Size <String>
	        Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
	        C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
	
	    -Sku <String>
	        Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
	
	    -MaxMemoryPolicy <String>
	        The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
	        MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
	
	    -RedisConfiguration <Hashtable>
	        All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
	        rdb-backup-frequency, maxmemory-delta, maxmemory-policy, notify-keyspace-events, maxmemory-samples,
	        slowlog-log-slower-than, slowlog-max-len, list-max-ziplist-entries, list-max-ziplist-value,
	        hash-max-ziplist-entries, hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries,
	        zset-max-ziplist-value etc.
	
	    -EnableNonSslPort <Boolean>
	        EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
	        currently configured value. Possible values are true and false.
	
	    -ShardCount <Integer>
	        The number of shards to create on a Premium Cluster Cache.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

`Set-AzureRmRedisCache` kann verwendet werden, um Eigenschaften wie `Size`, `Sku`, `EnableNonSslPort` und die `RedisConfiguration`-Werte zu aktualisieren.

Der folgende Befehl aktualisiert die maxmemory-Richtlinie für den Redis-Cache mit dem Namen „myCache“.

	Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## Skalieren eines Redis-Cache

`Set-AzureRmRedisCache` kann verwendet werden, um eine Azure Redis Cache-Instanz zu skalieren, wenn die Eigenschaften `Size`, `Sku` oder `ShardCount` geändert werden.

>[AZURE.NOTE]Skalieren eines Caches mithilfe von PowerShell unterliegt den gleichen Einschränkungen und Richtlinien wie die Skalierung eines Caches vom Azure-Portal aus. Sie können mit den folgenden Einschränkungen auf eine andere Preisstufe skalieren.
>
>-	Sie können keine Skalierung auf oder aus einem **Premium**-Cache vornehmen.
>-	Ein **Standard**-Cache kann nicht auf einen **Basic**-Cache skaliert werden.
>-	Ein **Basic**-Cache kann auf einen **Standard**-Cache skaliert werden, die Größe kann jedoch nicht gleichzeitig geändert werden. Wenn Sie eine andere Größe benötigen, können Sie anschließend einen Skalierungsvorgang auf die gewünschte Größe durchführen.
>-	Von einer größeren Größe kann nicht auf **C0 (250 MB)** herunterskaliert werden.
>
>Weitere Informationen finden Sie unter [Skalieren von Azure Redis Cache](cache-how-to-scale.md).

Das folgende Beispiel veranschaulicht, wie ein Cache mit Namen `myCache` zu einem 2,5-GB-Cache skaliert wird. Beachten Sie, dass dieser Befehl sowohl für einen Basic- als auch Standard-Cache gilt.

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Nach Eingabe dieses Befehls wird der Status des Caches zurückgegeben (ähnlich wie beim Aufruf von `Get-AzureRmRedisCache`). Beachten Sie, dass der `ProvisioningState` `Scaling` ist.

	PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
	
	
	Name               : mycache
	Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
	                     crosoft.Cache/Redis/mycache
	Location           : South Central US
	Type               : Microsoft.Cache/Redis
	HostName           : mycache.redis.cache.windows.net
	Port               : 6379
	ProvisioningState  : Scaling
	SslPort            : 6380
	RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
	                     [maxmemory-delta, 150]...}
	EnableNonSslPort   : False
	RedisVersion       : 3.0
	Size               : 1GB
	Sku                : Standard
	ResourceGroupName  : mygroup
	PrimaryKey         : ....
	SecondaryKey       : ....
	VirtualNetwork     :
	Subnet             :
	StaticIP           :
	TenantSettings     : {}
	ShardCount         :

Wenn der Skalierungsvorgang abgeschlossen ist, ändert sich `ProvisioningState` in `Succeeded`. Wenn Sie weitere Skalierungen vornehmen möchten, z. B. Wechsel von Basic zu Standard und Änderung der Größe, müssen Sie warten, bis der vorherige Vorgang abgeschlossen ist, oder Sie erhalten eine Fehlermeldung ähnlich der folgenden.

	Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## Abrufen von Informationen zu einem Redis-Cache

Sie können Informationen zu einem Cache mithilfe des [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx)-Cmdlets abrufen.

Um eine Liste der verfügbaren Parameter und ihrer Beschreibungen für `Get-AzureRmRedisCache` anzuzeigen, führen Sie den folgenden Befehl aus.

	PS C:\> Get-Help Get-AzureRmRedisCache -detailed
	
	NAME
	    Get-AzureRmRedisCache
	
	SYNOPSIS
	    Gets details about a single cache or all caches in the specified resource group or all caches in the current
	    subscription.
	
	SYNTAX
	    Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
	
	DESCRIPTION
	    The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
	    ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
	    specific cache name provided.
	
	    If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
	
	    If no parameters are given than it will return details about all caches the current subscription.
	
	PARAMETERS
	    -Name <String>
	        The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
	        returns the details for the cache.
	
	    -ResourceGroupName <String>
	        The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
	        then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
	        parameter is provided, then details for all caches in the resource group are returned.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Um Informationen über alle Caches im aktuellen Abonnement zurückzugeben, führen Sie `Get-AzureRmRedisCache` ohne Parameter aus.

	Get-AzureRmRedisCache

Um Informationen über alle Caches in einer bestimmten Ressourcengruppe zurückzugeben, führen Sie `Get-AzureRmRedisCache` mit dem `ResourceGroupName`-Parameter aus.

	Get-AzureRmRedisCache -ResourceGroupName myGroup

Um Informationen zu einem bestimmten Cache zurückzugeben, führen Sie `Get-AzureRmRedisCache` mit dem `Name`-Parameter aus, der den Namen des Caches enthält, und den `ResourceGroupName`-Parameter mit der Ressourcengruppe, die diesen Cache enthält.

	PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
	
	Name               : mycache
	Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
	                     crosoft.Cache/Redis/mycache
	Location           : South Central US
	Type               : Microsoft.Cache/Redis
	HostName           : mycache.redis.cache.windows.net
	Port               : 6379
	ProvisioningState  : Succeeded
	SslPort            : 6380
	RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
	                     [maxclients, 1000]...}
	EnableNonSslPort   : False
	RedisVersion       : 3.0
	Size               : 1GB
	Sku                : Standard
	ResourceGroupName  : myGroup
	VirtualNetwork     :
	Subnet             :
	StaticIP           :
	TenantSettings     : {}
	ShardCount         :

## Abrufen der Zugriffsschlüssel für einen Redis-Cache

Um die Zugriffsschlüssel für Ihren Cache abzurufen, können Sie das [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx)-Cmdlet verwenden.

Um eine Liste der verfügbaren Parameter und ihrer Beschreibungen für `Get-AzureRmRedisCacheKey` anzuzeigen, führen Sie den folgenden Befehl aus.

	PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
	
	NAME
	    Get-AzureRmRedisCacheKey
	
	SYNOPSIS
	    Gets the accesskeys for the specified redis cache.
	
	
	SYNTAX
	    Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
	
	DESCRIPTION
	    The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Um die Schlüssel für Ihren Cache abzurufen, rufen Sie das `Get-AzureRmRedisCacheKey`-Cmdlet auf und übergeben Sie im Namen Ihres Caches den Namen der Ressourcengruppe, die den Cache enthält.

	PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
	
	PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
	SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## Abrufen der Zugriffsschlüssel für Ihren Redis-Cache

Um die Zugriffsschlüssel für Ihren Cache abzurufen, können Sie das [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx)-Cmdlet verwenden.

Um eine Liste der verfügbaren Parameter und ihrer Beschreibungen für `New-AzureRmRedisCacheKey` anzuzeigen, führen Sie den folgenden Befehl aus.

	PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
	
	NAME
	    New-AzureRmRedisCacheKey
	
	SYNOPSIS
	    Regenerates the access key of a redis cache.
	
	SYNTAX
	    New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
	
	DESCRIPTION
	    The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    -KeyType <String>
	        Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
	
	    -Force
	        When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
	
Um den primären oder sekundären Schlüssel für Ihren Cache erneut zu generieren, rufen Sie das `New-AzureRmRedisCacheKey`-Cmdlet auf und übergeben Sie den Namen und die Ressourcengruppe. Geben Sie entweder `Primary` oder `Secondary` für den `KeyType`-Parameter an. Im folgenden Beispiel wird der sekundäre Zugriffsschlüssel für einen Cache erneut generiert.

	PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
	
	Confirm
	Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
	
	
	PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
	SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## Löschen eines Redis-Caches

Um einen Redis-Cache zu löschen, verwenden Sie das [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx)-Cmdlet.

Um eine Liste der verfügbaren Parameter und ihrer Beschreibungen für `Remove-AzureRmRedisCache` anzuzeigen, führen Sie den folgenden Befehl aus.

	PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
	
	NAME
	    Remove-AzureRmRedisCache
	
	SYNOPSIS
	    Remove redis cache if exists.
	
	SYNTAX
	    Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
	
	DESCRIPTION
	    The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to remove.
	
	    -ResourceGroupName <String>
	        Name of the resource group of the cache to remove.
	
	    -Force
	        When the Force parameter is provided, the cache is removed without any confirmation prompts.
	
	    -PassThru
	        By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
	        is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Im folgenden Beispiel wird der Cache mit dem Namen `myCache` entfernt.

	PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
	
	Confirm
	Are you sure you want to remove redis cache 'myCache'?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

<a name="classic"></a>
## Verwalten von Azure Redis Cache-Instanzen mit dem klassischen Bereitstellungsmodell von PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](cache-howto-manage-redis-cache-powershell.md) wird am Anfang dieses Artikels beschrieben.

Das folgende Skript veranschaulicht das Erstellen, Aktualisieren und Löschen eines Azure Redis Caches mit dem klassischen Bereitstellungsmodell.
		
		$VerbosePreference = "Continue"

    	# Create a new cache with date string to make name unique.
		$cacheName = "MovieCache" + $(Get-Date -Format ('ddhhmm'))
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache service is provisioned.
		
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
		
		# Update the access keys.
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -Name $cacheName -ResourceGroupName $resourceGroupName -RedisConfiguration @{"maxmemory-policy" = "AllKeys-LRU"}
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force

## Nächste Schritte

Weitere Informationen zur Verwendung von Windows PowerShell mit Azure finden Sie in den folgenden Ressourcen:

- [Dokumentation zu den Azure Redis Cache-Cmdlets auf MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Azure Resource Manager Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765) (in englischer Sprache): Verwenden der Cmdlets im AzureResourceManager-Modul.
- [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-portal/resource-group-portal.md): Erfahren Sie, wie Sie Ressourcengruppen im Azure-Portal erstellen und verwalten.
- [Azure-Blog](http://blogs.msdn.com/windowsazure): Informationen zu neuen Funktionen in Azure.
- [Windows PowerShell-Blog](http://blogs.msdn.com/powershell): Informationen zu neuen Funktionen in Windows PowerShell.
- ["Hey, Scripting Guy!"- Blog](http://blogs.technet.com/b/heyscriptingguy/): Praktische Tipps und Tricks aus der Windows PowerShell-Community.

<!---HONumber=AcomDC_0309_2016-->