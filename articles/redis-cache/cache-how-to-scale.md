<properties 
	pageTitle="Skalieren von Azure Redis Cache" 
	description="Erfahren Sie, wie Sie Azure Redis Cache-Instanzen skalieren" 
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
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Skalieren von Azure Redis Cache

>[AZURE.NOTE]Die Skalierungsfunktion von Azure Redis Cache befindet sich derzeit in der Vorschau.

Für Azure Redis Cache stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -funktionen bieten. Wenn sich die Anforderungen Ihrer Anwendung ändern, nachdem der Cache erstellt wurde, können Sie die Größe des Caches auf dem Blatt **Ändern des Tarifs** im [Azure-Portal](https://portal.azure.com) skalieren.

>[AZURE.NOTE]Wenn Sie einen Azure Redis Cache skalieren, können Sie die Größe ändern, Sie können jedoch nicht zwischen den Stufen Standard und Basic wechseln.

## Zeitpunkt für die Skalierung

Sie können die Integrität und Leistung Ihrer Cacheanwendungen mithilfe der [Überwachungsfunktionen](cache-how-to-monitor.md) von Azure Redis Cache überwachen, um zu ermitteln, ob der Cache skaliert werden muss.

Überwachen Sie die folgenden Metriken überwachen, um herauszufinden, ob eine Skalierung notwendig ist.

-	Redis-Serverlast
-	Speicherauslastung
-	Netzwerkbandbreite
-	CPU-Auslastung

Wenn Sie feststellen, dass Ihr Cache die Anforderungen Ihrer Anwendung nicht mehr erfüllt, können Sie in einen anderen Tarif wechseln, der zu Ihrer Anwendung passt. Weitere Informationen dazu, wie Sie ermitteln, welcher Cachetarif geeignet ist, finden Sie unter [Welches Angebot und welche Größe eignet sich für meinen Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)?

## Skalieren eines Caches
Um Ihren Cache zu skalieren, navigieren Sie per [Durchsuchen](https://msdn.microsoft.com/library/azure/dn793612.aspx#RedisCacheConfiguration) im [Azure-Portal](https://portal.azure.com) zu Ihrem Cache, und klicken Sie auf das Detail **Tarif "Standard"** oder **Tarif "Basic"** auf dem Blatt **Redis Cache**.

![Tarif][redis-cache-pricing-tier-part]

Wählen Sie auf dem Blatt **Tarif** den gewünschten Tarif aus, und klicken Sie auf **Auswählen**.

![Tarif][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Sie können nicht vom Basic-Tarif zum Standard-Tarif oder umgekehrt wechseln, und Sie können keine größeren Caches auf 250 MB zentral herunterskalieren. Sie können einen 250-MB-Cache auf einen größeren Cache hochskalieren, können diesen Cache jedoch später nicht wieder in den 250-MB-Tarif herunterskalieren. Wenn Sie vom Basic- in den Standard-Tarif wechseln oder Ihren Cache auf 250 MB herunterskalieren möchten, müssen Sie einen neuen Cache erstellen.

Während der Cache in den neuen Tarif skaliert wird, wird auf dem Blatt **Redis Cache** der Status **Skalierung** angezeigt.

![Skalieren][redis-cache-scaling]

Wenn die Skalierung abgeschlossen ist, ändert sich der Status von **Wird skaliert** zu **Wird ausgeführt**.

>[AZURE.IMPORTANT]Während der Skalierungsvorgänge sind Basic-Caches offline, und alle Daten im Cache gehen verloren. Sobald der Skalierungsvorgang abgeschlossen ist, wird der Basic-Cache wieder online geschaltet, enthält jedoch keine Daten. Standard-Caches bleiben während eines Skalierungsvorgangs online, und üblicherweise gehen keine Daten verloren, wenn ein Standard-Cache auf eine größere Größe skaliert wird. Beim Skalieren eines Standard-Caches auf eine kleinere Größe können Daten verloren gehen, wenn der neue Cache kleiner ist als die Menge der im Cache enthaltenen Daten. Wenn Daten beim Herunterskalieren verloren gehen, werden die Schlüssel mithilfe der Entfernungsrichtlinie [allkeys-lru](http://redis.io/topics/lru-cache) entfernt. Hinweis: Für Caches des Tarifs "Standard" gilt zwar eine SLA (Service Level Agreement, Vereinbarung zum Servicelevel) von 99,9 % für die Verfügbarkeit, es gibt jedoch keine SLA für Datenverlust.

## Automatisieren eines Skalierungsvorgangs

Sie können Ihre Azure Redis Cache-Instanz nicht nur über das Azure-Portal skalieren, sondern auch mithilfe der [Microsoft Azure-Verwaltungsbibliotheken](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Um Ihren Cache zu skalieren, rufen Sie die `IRedisOperations.CreateOrUpdate`-Methode auf, und übergeben Sie die neue Größe für `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://msdn.microsoft.com/de-de/library/azure/dn790557.aspx#bk_portal
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Weitere Informationen finden Sie im Beispiel [Verwalten von Redis Cache mithilfe von Microsoft Azure-Verwaltungsbibliotheken](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML).

## Häufig gestellte Fragen zur Skalierung

Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Skalierung von Azure Redis Cache-Instanzen.

## Muss ich nach dem Skalieren den Namen oder die Zugriffsschlüssel für den Cache ändern?

Nein, Cachename und -schlüssel bleiben während eines Skalierungsvorgangs unverändert.

## Wie funktioniert die Skalierung?

Wenn ein **Basic**-Cache skaliert wird, wird er heruntergefahren, und es wird ein neuer Cache mit der neuen Größe bereitgestellt. Während dieser Zeit ist der Cache nicht verfügbar, und alle Daten im Cache gehen verloren.

Wenn ein **Standard**-Cache skaliert wird, wird eines der Replikate heruntergefahren und mit der neuen Größe bereitgestellt, und die Daten werden übertragen. Anschließend führt das andere Replikat ein Failover aus, bevor es erneut bereitgestellt wird. Dieser Prozess ähnelt dem Prozess, der beim Ausfall eines Cacheknoten durchgeführt wird.

## Gehen während der Skalierung Daten aus dem Cache verloren?

Wenn ein **Basic**-Cache skaliert wird, gehen alle Daten verloren, und der Cache ist während des Skalierungsvorgangs nicht verfügbar.

Wenn ein **Standard**-Cache auf eine größere Größe skaliert wird, bleiben in der Regel alle Daten erhalten. Wenn Sie einen **Standard**-Cache auf eine kleinere Größe herunterskalieren, können Daten verloren gehen, je nachdem, ob der neue Cache groß genug für alle im alten Cache enthaltenen Daten ist. Wenn Daten beim Herunterskalieren verloren gehen, werden die Schlüssel mithilfe der Entfernungsrichtlinie [allkeys-lru](http://redis.io/topics/lru-cache) entfernt. Hinweis: Für Caches des Tarifs "Standard" gilt zwar eine SLA (Service Level Agreement, Vereinbarung zum Servicelevel) von 99,9 % für die Verfügbarkeit, es gibt jedoch keine SLA für Datenverlust.

## Ist der Cache während der Skalierung verfügbar?

**Standard**-Caches bleiben während des Skalierungsvorgangs verfügbar.

**Basic**-Caches sind während des Skalierungsvorgangs offline.

## Nicht unterstützte Vorgänge

Sie können während eines Skalierungsvorgangs nicht von einem **Basic**- zu einem **Standard**-Cache oder umgekehrt wechseln.

Sie können von einem **C0**-Cache (250 MB) auf eine größere Größe hochskalieren, Sie können jedoch einen größeren Cache nicht auf **C0** herunterskalieren.

Wenn bei einem Skalierungsvorgang ein Fehler auftritt, versucht der Dienst, den Vorgang rückgängig zu machen, und der Cache wird auf die ursprüngliche Größe zurückgesetzt.

## Wie lange dauert die Skalierung?

Die Skalierung dauert ca. 20 Minuten, je nachdem, wie viele Daten sich im Cache befinden.

## Woher weiß ich, dass die Skalierung abgeschlossen ist?

Im Portal können Sie den Fortschritt der Skalierung anzeigen. Wenn die Skalierung abgeschlossen ist, ändert sich der Status des Caches zu **Wird ausgeführt**.

## Warum befindet sich diese Funktion in der Vorschau?

Wir haben die Funktion veröffentlicht, um Feedback zu erhalten. Basierend auf dem Feedback werden wir diese Funktion in Kürze in der allgemein verfügbaren Version veröffentlichen.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=August15_HO6-->