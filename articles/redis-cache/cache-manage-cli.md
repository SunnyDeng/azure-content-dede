<properties 
	pageTitle="Erstellen und Verwalten von Azure Redis Cache mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI)" 
	description="Erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle auf einer beliebigen Plattform installieren, eine Verbindung mit Ihrem Azure-Konto herstellen und über die Azure-Befehlszeilenschnittstelle einen Redis-Cache erstellen und verwalten." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="sdanie"/>

# Erstellen und Verwalten von Azure Redis Cache mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

Die Azure-Befehlszeilenschnittstelle ist eine hervorragende Methode, um Ihre Azure-Infrastruktur von einer beliebigen Plattform aus zu verwalten. In diesem Artikel wird das Erstellen und Verwalten Ihrer Azure Redis Cache-Instanzen mit der Azure-Befehlszeilenschnittstelle beschrieben.

## Voraussetzungen

Zum Erstellen und Verwalten von Azure Redis Cache-Instanzen mithilfe der Azure-Befehlszeilenschnittstelle müssen Sie die folgenden Schritte ausführen.

-	Sie benötigen ein Azure-Abonnement. Wenn Sie dies noch nicht haben, können Sie in nur wenigen Minuten ein [kostenloses Testkonto](http://azure.microsoft.com/pricing/free-trial/) erstellen.
-	[Installieren Sie die Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).
-	Verbinden Sie die Installation der Azure-Befehlszeilenschnittstelle mit einem persönlichen Azure-Konto oder einem Arbeits- oder Schulkonto für Azure, und melden Sie sich mit dem `azure login`-Befehl über die Azure-Befehlszeilenschnittstelle an. Um die Unterschiede zu verstehen und eine Wahl treffen zu können, lesen Sie sich [Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure Befehlszeilenschnittstelle (Azure-CLI)](../xplat-cli-connect.md) durch.
-	Wechseln Sie vor dem Ausführen eines der folgenden Befehle in der Azure-Befehlszeilenschnittstelle in den Ressourcen-Manager-Modus, indem Sie den Befehl `azure config mode arm` ausführen. Weitere Informationen finden Sie unter [Festlegen des Azure-Ressourcen-Manager-Modus](../virtual-machines/xplat-cli-azure-resource-manager.md#setting-the-azure-resource-manager-mode).

## Eigenschaften in Redis Cache

Die folgenden Eigenschaften werden beim Erstellen und Aktualisieren von Redis Cache-Instanzen verwendet.

| Eigenschaft | Switch | Beschreibung |
|------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| Name | -n, --name | Der Name des Redis-Cache. |
| Ressourcengruppe | -g, --resource-group | Der Name der Ressourcengruppe. |
| location | -l, --location | Speicherort zum Erstellen des Cache. |
| size | -z, --size | Die Größe des Redis-Cache. Gültige Werte: [C0, C1, C2, C3, C4, C5, C6] |
| sku | -x, --sku | Redis-SKU. Sollte einer der folgenden Werte sein: [Basic, Standard] |
| MaxMemoryPolicy | -m, --max-memory-policy | MaxMemoryPolicy-Eigenschaft des Redis-Cache. Gültige Werte: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL] |
| EnableNonSslPort | -e, --enable-non-ssl-port | EnableNonSslPort-Eigenschaft des Redis-Cache. Fügen Sie dieses Flag hinzu, wenn Sie den Nicht-SSL-Port für den Cache aktivieren möchten. |
| Abonnement | -s, --subscription | Die Abonnement-ID. |
| key type | -t, --key-type | Der Typ des zu erneuernden Schlüssels. Gültige Werte: [Primary, Secondary] |

## Übersicht über alle Redis Cache-Befehle

Wenn Sie alle Redis Cache-Befehle und deren Parameter anzeigen möchten, verwenden Sie den `azure rediscache -h`-Befehl.

	C:\>azure rediscache -h
	help:    Commands to manage your Azure Redis Cache(s)
	help:
	help:    Create a Redis Cache
	help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Delete an existing Redis Cache
	help:      rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    List all Redis Caches within your Subscription or Resource Group
	help:      rediscache list [options]
	help:
	help:    Show properties of an existing Redis Cache
	help:      rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Change settings of an existing Redis Cache
	help:      rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Renew the authentication key for an existing Redis Cache
	help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help  output usage information
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Erstellen eines Redis-Cache

Verwenden Sie zum Erstellen eines Redis-Cache den folgenden Befehl:

	azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache create -h`-Befehl aus.

	C:\>azure rediscache create -h
	help:    Create a Redis Cache
	help:
	help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -l, --location <location>                    Location to create cache.
	help:      -z, --size <size>                            Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6]
	help:      -x, --sku <sku>                              Redis SKU. Should be one of : [Basic, Standard]
	help:      -m, --max-memory-policy <max-memory-policy>  MaxMemoryPolicy property of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -e, --enable-non-ssl-port                    EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
	help:      -s, --subscription <id>                      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Löschen eines vorhandenen Redis-Cache

Verwenden Sie zum Löschen eines Redis-Cache den folgenden Befehl:

	azure rediscache delete [--name <name> --resource-group <resource-group> ]

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache delete -h`-Befehl aus.

	C:\>azure rediscache delete -h
	help:    Delete an existing Redis Cache
	help:
	help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Auflisten aller Redis-Caches in Ihrem Abonnement oder Ihrer Ressourcengruppe

Wenn Sie alle Redis-Caches in Ihrem Abonnement oder Ihrer Ressourcengruppe auflisten möchten, verwenden Sie den folgenden Befehl:

	azure rediscache list [options]

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache list -h`-Befehl aus.

	C:\>azure rediscache list -h
	help:    List all Redis Caches within your Subscription or Resource Group
	help:
	help:    Usage: rediscache list [options]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Anzeigen der Eigenschaften eines vorhandenen Redis-Cache

Verwenden Sie zum Anzeigen der Eigenschaften eines vorhandenen Redis-Cache den folgenden Befehl:

	azure rediscache show [--name <name> --resource-group <resource-group>]

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache show -h`-Befehl aus.

	C:\>azure rediscache show -h
	help:    Show properties of an existing Redis Cache
	help:
	help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Ändern der Einstellungen eines vorhandenen Redis-Cache

Verwenden Sie zum Ändern der Einstellungen eines vorhandenen Redis-Cache den folgenden Befehl:

	azure rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache set -h`-Befehl aus.

	C:\>azure rediscache set -h
	help:    Change settings of an existing Redis Cache
	help:
	help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -m, --max-memory-policy <max-memory-policy>  Max Memory Policy of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -s, --subscription <subscription>            the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Erneuern des Authentifizierungsschlüssels für einen vorhandenen Redis-Cache

Verwenden Sie zum Erneuern des Authentifizierungsschlüssels für einen vorhandenen Redis-Cache den folgenden Befehl:

	azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Geben Sie `Primary` oder `Secondary` für `key-type` an.

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache renew-key -h`-Befehl aus.

	C:\>azure rediscache renew-key -h
	help:    Renew the authentication key for an existing Redis Cache
	help:
	help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
	help:      -t, --key-type <key-type>              type of key to renew
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Auflisten der primären und sekundären Schlüssel eines vorhandenen Redis-Cache

Verwenden Sie zum Auflisten der primären und sekundären Schlüssel eines vorhandenen Redis-Cache den folgenden Befehl:

	azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache list-keys -h`-Befehl aus.

	C:\>azure rediscache list-keys -h
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:
	help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

<!---HONumber=AcomDC_1203_2015-->