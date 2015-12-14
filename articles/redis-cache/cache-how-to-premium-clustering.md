<properties 
	pageTitle="Konfigurieren von Redis-Clustern für Azure Redis Cache vom Typ ";Premium";" 
	description="Erfahren Sie, wie Sie das Redis-Clustering für Azure Redis Cache-Instanzen im Premium-Tarif erstellen und verwalten." 
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

# Konfigurieren von Redis-Clustern für Azure Redis Cache vom Typ "Premium"
Für Azure Redis Cache stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -features bieten, einschließlich des neuen Premium-Tarifs.

Der Premium-Tarif für Azure Redis Cache umfasst Clustering, Persistenz und Unterstützung des virtuellen Netzwerks. In diesem Artikel wird erläutert, wie Cluster in einer Azure Redis Cache-Instanz vom Typ "Premium" konfiguriert werden.

Informationen zu anderen Funktionen des Premium-Caches finden Sie unter [Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md) und [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md).

## Was ist Redis Cluster?
Azure Redis Cache umfasst Redis Cluster entsprechend der [Implementierung in Redis](http://redis.io/topics/cluster-tutorial). Redis Cluster bringt Ihnen die folgenden Vorteile.

-	Die Möglichkeit, das DataSet automatisch zwischen mehreren Knoten aufzuteilen. 
-	Die Möglichkeit, Vorgänge fortzusetzen, wenn bei einer Teilmenge der Knoten Fehler auftreten oder nicht alle Knoten im Cluster miteinander kommunizieren können. 
-	Höherer Durchsatz: Der Durchsatz steigt linear, wenn Sie die Anzahl der Shards erhöht. 
-	Höhere Speichergröße: Die Speichergröße erhöht sich linear, wenn Sie die Anzahl der Shards erhöht.  

Ausführliche Informationen zu Größe, Durchsatz und Bandbreite von Premium-Caches finden Sie in den [häufig gestellten Fragen zu Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

In Azure wird Redis Cluster als Modell aus primärem Cache und Replikatcache angeboten, in dem jeder Shard über ein Paar aus primärem Cache und Replikatcache mit Replikation verfügt und die Replikation mit dem Azure Redis Cache-Dienst verwaltet wird.

## Clustering
Clustering wird während der Erstellung des Caches auf dem Blatt **Neuer Redis-Cache** konfiguriert. Um einen Cache zu erstellen, melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an, und klicken Sie auf **Neu** > **Daten und Speicher** > **Redis Cache**.

![Erstellen eines Redis-Cache][redis-cache-new-cache-menu]

Zum Konfigurieren des Clustering wählen Sie zunächst auf dem Blatt **Wählen Sie Ihren Tarif** eine der **Premium**-Optionen für den Cache aus.

![Tarif auswählen][redis-cache-premium-pricing-tier]

Das Clustering wird auf dem Blatt **Redis Cluster** konfiguriert.

![Clustering][redis-cache-clustering]

Sie können bis zu 10 Shards im Cluster festlegen. Klicken Sie auf **Aktiviert**, und schieben Sie den Schieberegler, oder geben Sie eine Zahl zwischen 1 und 10 für **Shard count** ein, und klicken Sie auf **OK**.

Jeder Shard ist ein Paar aus primärem Cache und Replikatcache, das in Azure verwaltet wird. Die Gesamtgröße des Caches wird durch Multiplikation der Anzahl der Shards mit der im Tarif ausgewählten Cachegröße berechnet.

![Clustering][redis-cache-clustering-selected]

Nach der Erstellung des Caches können Sie eine Verbindung mit dem Cache herstellen und ihn genauso wie einen nicht gruppierten Cache verwenden. Redis verteilt die Daten auf alle Cache-Shards. Wenn die Diagnosefunktion [aktiviert](cache-how-to-monitor.md#enable-cache-diagnostics) ist, werden Metriken für jeden Shard separat erfasst und können auf dem Redis Cache-Blatt [angezeigt](cache-how-to-monitor.md) werden.

>[AZURE.IMPORTANT]Wenn Sie mithilfe von StackExchange.Redis eine Verbindung zu einem Azure Redis Cache mit Clusterunterstützung herstellen, tritt möglicherweise ein Problem auf und Sie erhalten `MOVE`-Ausnahmen. Dazu kann es kommen, weil es kurze Zeit dauert, bis der StackExchange.Redis-Cacheclient die Informationen zu den Knoten im Cachecluster gesammelt hat. Diese Ausnahmen können auftreten, wenn Sie zum ersten Mal eine Verbindung mit dem Cache herstellen und direkt Aufrufe an ihn senden, bevor der Client mit dem Sammeln dieser Informationen fertig ist. Die einfachste Möglichkeit zur Lösung des Problems in Ihrer Anwendung ist, eine Verbindung mit dem Cache herzustellen und dann eine Sekunde zu warten, bevor Sie Aufrufe an den Cache senden. Dies erreichen Sie wie im folgenden Beispielcode gezeigt durch Hinzufügen von `Thread.Sleep(1000)`. Beachten Sie, dass `Thread.Sleep(1000)` nur während der ersten Verbindung mit dem Cache auftritt. Weitere Informationen finden Sie unter [StackExchange.Redis.RedisServerException – VERSCHOBEN #248](https://github.com/StackExchange/StackExchange.Redis/issues/248). Ein Update zur Behebung dieses Problems wird derzeit entwickelt. Alle Updates werden hier veröffentlicht.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
        // Connect to the Redis cache for the first time
	    var connection =  ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

		// Wait for 1 second
		Thread.Sleep(1000);

		// Return the connected ConnectionMultiplexer
		return connection;
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Clustering – häufig gestellte Fragen

Die folgende Liste enthält Antworten auf häufig gestellte Fragen zum Clustering in Azure Redis Cache.

## Muss ich Änderungen an meiner Clientanwendung vornehmen, um das Clustering verwenden zu können?

-	Wenn das Clustering aktiviert ist, ist nur Datenbank 0 verfügbar. Wenn die Clientanwendung mehrere Datenbanken verwendet und versucht, eine andere Datenbank als Datenbank 0 zu lesen oder Schreibvorgänge dafür auszuführen, wird die folgende Ausnahme ausgelöst: `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
-	Wenn Sie [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) nutzen, müssen Sie 1.0.481 oder höher verwenden. Sie stellen eine Verbindung mit dem Cache mit den gleichen [Endpunkten, Ports und Schlüsseln](cache-configure.md#properties) her, die Sie auch für einen Cache verwenden, bei dem das Clustering nicht aktiviert ist. Der einzige Unterschied besteht darin, dass alle Lese- und Schreibvorgänge für Datenbank 0 erfolgen müssen.
	-	Für andere Clients gelten möglicherweise andere Anforderungen. Weitere Informationen finden Sie unter [Wird das Clustering auf allen Redis-Clients unterstützt?](#do-all-redis-clients-support-clustering)
-	Wenn für Ihre Anwendung mehrere Schlüsselvorgänge zu einem einzelnen Befehl zusammengefasst sind, müssen sich alle Schlüssel in demselben Shard befinden. Informationen dazu, wie Sie dies erreichen, finden Sie unter [Wie sind Schlüssel in einem Cluster verteilt?](#how-are-keys-distributed-in-a-cluster)
-	Bei Verwendung des Redis ASP.NET-Sitzungszustandsanbieters müssen Sie Version 2.0.0 oder höher verwenden. Weitere Informationen finden Sie unter [Kann ich das Clustering mit den Redis ASP.NET-Sitzungszustands- und -Ausgabezwischenspeicherungsanbietern verwenden?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

## Wie sind Schlüssel in einem Cluster verteilt?

Laut Redis-Dokumentation zum [Schlüsselverteilungsmodell](http://redis.io/topics/cluster-spec#keys-distribution-model) wird der Schlüsselbereich in 16384 Slots aufgeteilt. Jeder Schlüssel wird gehasht und einem dieser Slots zugewiesen, die auf die Knoten des Clusters verteilt werden. Sie können konfigurieren, welcher Teil des Schlüssels gehasht ist, um sicherzustellen, dass mehrere Schlüssel in demselben Shard angeordnet sind. Verwenden Sie hierfür Hashtags.

-	Schlüssel mit einem Hashtag: Wenn ein Teil des Schlüssels in `{` und `}` gesetzt ist, wird nur dieser Teil des Schlüssels gehasht, um den Hashslot eines Schlüssels zu ermitteln. Die folgenden drei Schlüssel würden sich beispielsweise in demselben Shard befinden: `{key}1`, `{key}2` und `{key}3`, da nur der `key`-Teil des Namens gehasht ist. Eine vollständige Liste mit den Spezifikationen für Schlüssel-Hashtags finden Sie unter [Schlüssel-Hashtags](http://redis.io/topics/cluster-spec#keys-hash-tags).
-	Schlüssel ohne Hashtag: Der gesamte Schlüsselname wird für das Hashing verwendet. Dies ergibt eine statistisch gleichmäßige Verteilung über die Shards des Caches.

Zur Erzielung einer optimalen Leistung und eines hohen Durchsatzes empfehlen wir die gleichmäßige Verteilung von Schlüsseln. Wenn Sie Schlüssel mit Hashtag verwenden, muss von der Anwendung sichergestellt werden, dass die Schlüssel gleichmäßig verteilt werden.

Weitere Informationen finden Sie unter [Schlüsselverteilungsmodell](http://redis.io/topics/cluster-spec#keys-distribution-model), [Redis Cluster-Daten-Sharding](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) und [Schlüssel-Hashtags](http://redis.io/topics/cluster-spec#keys-hash-tags).

## Was ist die maximale Cachegröße, die ich erstellen kann?

Die maximale Größe bei Premium-Cache beträgt 53 GB. Sie können bis zu 10 Shards mit einer maximalen Größe von 530 GB erstellen. Wenn Sie eine höhere Cachegröße benötigen, können Sie sie [anfordern](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Weitere Informationen finden Sie unter [Azure Redis Cache – Preise](https://azure.microsoft.com/pricing/details/cache/).

## Wird das Clustering auf allen Redis-Clients unterstützt?

Zum gegenwärtigen Zeitpunkt unterstützen nicht alle Clients das Redis-Clustering. StackExchange.Redis ist einer der Clients, der das Clustering unterstützt. Weitere Informationen zu anderen Clients finden Sie im [Redis Cluster Tutorial](http://redis.io/topics/cluster-tutorial) im Abschnitt [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (in englischer Sprache).

>[AZURE.NOTE]Wenn Sie StackExchange.Redis als Client verwenden, müssen Sie sicherstellen, dass Sie die neueste Version [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 oder höher verwenden, damit das Clustering ordnungsgemäß ausgeführt wird.

## Wie stelle ich eine Verbindung mit dem Cache her, wenn das Clustering aktiviert ist?

Sie können eine Verbindung mit dem Cache mit den gleichen [Endpunkten, Ports und Schlüsseln](cache-configure.md#properties) herstellen, die Sie auch für einen Cache verwenden, bei dem das Clustering nicht aktiviert ist. Redis verwaltet das Clustering auf dem Back-End, sodass es nicht über Ihren Client verwaltet werden muss.

## Kann ich direkt eine Verbindung mit den einzelnen Shards des Caches herstellen?

Dies wird offiziell nicht unterstützt. Allerdings besteht jeder Shard aus einem Paar aus primärem Cache und Replikatcache, die zusammen als Cacheinstanz bezeichnet werden. Mit dem Hilfsprogramm redis-cli in der [unstable](http://redis.io/download)-Verzweigung des Redis-Repositorys auf GitHub können Sie eine Verbindung mit diesen Cacheinstanzen herstellen. Diese Version implementiert grundlegende Unterstützung, wenn sie mit dem Switch `-c` gestartet wird. Weitere Informationen finden Sie im [Redis Cluster Tutorial](http://redis.io/topics/cluster-tutorial) unter [Playing with the Cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (in englischer Sprache) auf [http://redis.io](http://redis.io).

Ohne SSL verwenden Sie die folgenden Befehle.

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Ersetzen Sie für SSL `1300N` durch `1500N`.

## Kann ich das Clustering für einen bereits erstellten Cache konfigurieren?

Zu diesem Zeitpunkt können Sie das Clustering nur bei der Erstellung eines Caches aktivieren und konfigurieren.

## Kann ich das Clustering für einen Basic- oder Standard-Cache konfigurieren?

Clustering ist nur für Premium-Caches verfügbar.

## Kann ich das Clustering mit den Redis ASP.NET-Sitzungszustands- und -Ausgabezwischenspeicherungsanbietern verwenden?

-	**Redis-Ausgabecacheanbieter:** Keine Änderungen erforderlich.
-	**Redis-Sitzungszustandsanbieter:** Zum Verwenden des Clusterings müssen Sie [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.0 oder höher verwenden. Andernfalls wird eine Ausnahme ausgelöst. Dies ist eine unterbrechende Änderung. Weitere Informationen finden Sie unter [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details) (in englischer Sprache).

## Nächste Schritte
Informationen zur Verwendung weiterer Funktionen des Premium-Caches finden Sie in den folgenden Artikeln.

-	[Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md)
-	[Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

<!---HONumber=AcomDC_1203_2015-->