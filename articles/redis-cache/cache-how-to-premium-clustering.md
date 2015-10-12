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
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Konfigurieren von Redis-Clustern für Azure Redis Cache vom Typ "Premium"
Für Azure Redis Cache stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -funktionen bieten, einschließlich des neuen Premium-Tarifs, der sich derzeit in der Vorschauphase befindet.

Der Premium-Tarif für Azure Redis Cache umfasst Clustering, Persistenz und Unterstützung des virtuellen Netzwerks. In diesem Artikel wird erläutert, wie Cluster in einer Azure Redis Cache-Instanz vom Typ "Premium" konfiguriert werden.

Informationen zu anderen Funktionen des Premium-Caches finden Sie unter [Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md) und [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md).

>[AZURE.NOTE]Der Premium-Tarif von Azure Redis Cache befindet sich derzeit in der Vorschauphase.

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

Nach der Erstellung des Caches können Sie eine Verbindung mit dem Cache herstellen und ihn genauso wie einen nicht gruppierten Cache verwenden. Redis verteilt die Daten auf alle Cache-Shards.

## Clustering – häufig gestellte Fragen

Die folgende Liste enthält Antworten auf häufig gestellte Fragen zum Clustering in Azure Redis Cache.

## Was ist die maximale Cachegröße, die ich erstellen kann?

Die maximale Größe bei Premium-Cache beträgt 53 GB. Sie können bis zu 10 Shards mit einer maximalen Größe von 530 GB erstellen. Wenn Sie eine höhere Cachegröße benötigen, können Sie sie [anfordern](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Weitere Informationen finden Sie unter [Azure Redis Cache Preise](https://azure.microsoft.com/pricing/details/cache/).

## Wird das Clustering auf allen Redis-Clients unterstützt?

Zum gegenwärtigen Zeitpunkt unterstützen nicht alle Clients das Redis-Clustering. StackExchange.Redis ist einer der Clients, der das Clustering unterstützt. Weitere Informationen zu anderen Clients finden Sie im [Redis cluster tutorial](http://redis.io/topics/cluster-tutorial) im Abschnitt [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (in englischer Sprache).

>[AZURE.NOTE]Wenn Sie StackExchange.Redis als Client verwenden, stellen Sie sicher, dass Sie die neueste Version [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 oder höher verwenden, damit das Clustering ordnungsgemäß ausgeführt wird.

## Wie stelle ich eine Verbindung mit dem Cache her, wenn das Clustering aktiviert ist?

Sie können eine Verbindung mit dem Cache mit den gleichen [Endpunkten, Ports und Schlüsseln](cache-configure.md#properties) herstellen, die Sie auch für einen Cache verwenden, bei dem das Clustering nicht aktiviert ist. Redis verwaltet das Clustering auf dem Back-End, sodass es nicht über Ihren Client verwaltet werden muss.

## Kann ich direkt eine Verbindung mit den einzelnen Shards des Caches herstellen?

Dies wird offiziell nicht unterstützt. Allerdings besteht jeder Shard aus einem Paar aus primärem Cache und Replikatcache, die zusammen als Cacheinstanz bezeichnet werden. Mit "redis-cli.exe" können Sie nach dem folgenden Muster eine Verbindung mit diesen Cacheinstanzen herstellen.

Ohne SSL verwenden Sie die folgenden Befehle.

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Ersetzen Sie bei SSL `1300N` durch `1500N`.

## Kann ich das Clustering für einen bereits erstellten Cache konfigurieren?

Während der Vorschauphase können Sie das Clustering nur bei der Erstellung eines Caches aktivieren und konfigurieren.

## Kann ich das Clustering für einen Basic- oder Standard-Cache konfigurieren?

Clustering ist nur für Premium-Caches verfügbar.

## Nächste Schritte

Informationen zur Verwendung weiterer Funktionen des Premium-Caches finden Sie in den folgenden Artikeln. - [Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md) - [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

<!---HONumber=Oct15_HO1-->