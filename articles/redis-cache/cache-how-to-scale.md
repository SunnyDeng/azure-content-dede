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
	ms.date="09/10/2015" 
	ms.author="sdanie"/>

# Skalieren von Azure Redis Cache

>[AZURE.NOTE]Die Skalierungsfunktion von Azure Redis Cache befindet sich derzeit in der Vorschau.

Für Azure Redis Cache stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -funktionen bieten. Wenn sich die Anforderungen Ihrer Anwendung ändern, nachdem der Cache erstellt wurde, können Sie die Größe des Caches auf dem Blatt **Ändern des Tarifs** im [Azure-Vorschauportal](https://portal.azure.com) skalieren.

## Zeitpunkt für die Skalierung

Sie können die Integrität und Leistung Ihrer Cacheanwendungen mithilfe der [Überwachungsfunktionen](cache-how-to-monitor.md) von Azure Redis Cache überwachen, um zu ermitteln, ob der Cache skaliert werden muss.

Überwachen Sie die folgenden Metriken überwachen, um herauszufinden, ob eine Skalierung notwendig ist.

-	Redis-Serverlast
-	Speicherauslastung
-	Netzwerkbandbreite
-	CPU-Auslastung

Wenn Sie feststellen, dass Ihr Cache die Anforderungen Ihrer Anwendung nicht mehr erfüllt, können Sie in einen anderen Tarif wechseln, der zu Ihrer Anwendung passt. Weitere Informationen dazu, wie Sie ermitteln, welcher Cachetarif geeignet ist, finden Sie unter [Welches Angebot und welche Größe eignet sich für meinen Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)?

## Skalieren eines Caches
Zum Skalieren Ihres Cache [navigieren Sie zum Cache](cache-configure.md#configure-redis-cache-settings) im [Vorschauportal](https://portal.azure.com) und klicken dann auf **Einstellungen**, **Preisstufe**.

Sie können auch auf dem Abschnitt **Standard-Ebene** oder **Basic-Ebene** auf dem Blatt **Redis-Cache** klicken.

![Tarif][redis-cache-pricing-tier-part]

Wählen Sie auf dem Blatt **Tarif** den gewünschten Tarif aus, und klicken Sie auf **Auswählen**.

![Tarif][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Sie können mit den folgenden Einschränkungen auf eine andere Preisstufe skalieren.
>
>-	Ein **Standard**-Cache kann nicht auf einen **Basic**-Cache skaliert werden.
>-	Ein **Basic**-Cache kann auf einen **Standard**-Cache skaliert werden, die Größe kann jedoch nicht gleichzeitig geändert werden. Wenn Sie eine andere Größe benötigen, können Sie anschließend einen Skalierungsvorgang auf die gewünschte Größe durchführen.
>-	Von einer größeren Größe kann nicht auf **C0 (250 MB)** herunterskaliert werden.

Während der Cache in den neuen Tarif skaliert wird, wird auf dem Blatt **Redis Cache** der Status **Skalierung** angezeigt.

![Skalieren][redis-cache-scaling]

Wenn die Skalierung abgeschlossen ist, ändert sich der Status von **Wird skaliert** zu **Wird ausgeführt**.

## Automatisieren eines Skalierungsvorgangs

Sie können Ihre Azure Redis Cache-Instanz nicht nur über das Vorschauportal skalieren, sondern auch mithilfe der [Microsoft Azure-Verwaltungsbibliotheken (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Um Ihren Cache zu skalieren, rufen Sie die `IRedisOperations.CreateOrUpdate`-Methode auf, und übergeben Sie die neue Größe für `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
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

Wenn ein **Basic**-Cache auf eine andere Größe skaliert wird, wird er heruntergefahren, und es wird ein neuer Cache mit der neuen Größe bereitgestellt. Während dieser Zeit ist der Cache nicht verfügbar, und alle Daten im Cache gehen verloren.

Wenn ein **Basic**-Cache auf einen **Standard**-Cache skaliert wird, wird ein Replikatcache bereitgestellt, und die Daten werden aus dem primären Cache in den Replikatcache kopiert. Der Cache bleibt während des Skalierungsvorgangs verfügbar.

Wenn ein **Standard**-Cache auf eine andere Größe skaliert wird, wird eines der Replikate heruntergefahren und mit der neuen Größe bereitgestellt, und die Daten werden übertragen. Anschließend führt das andere Replikat ein Failover aus, bevor es erneut bereitgestellt wird. Dieser Prozess ähnelt dem Prozess, der beim Ausfall eines Cacheknoten durchgeführt wird.

## Gehen während der Skalierung Daten aus dem Cache verloren?

Wenn ein **Basic**-Cache auf eine neue Größe skaliert wird, gehen alle Daten verloren, und der Cache ist während des Skalierungsvorgangs nicht verfügbar.

Wenn ein **Basic**-Cache auf einen **Standard**-Cache skaliert wird, werden die Daten im Cache in der Regel beibehalten.

Wenn ein **Standard**-Cache auf eine größere Größe skaliert wird, bleiben in der Regel alle Daten erhalten. Wenn Sie einen **Standard**-Cache auf eine kleinere Größe herunterskalieren, können Daten verloren gehen, je nachdem, ob der neue Cache groß genug für alle im alten Cache enthaltenen Daten ist. Wenn Daten beim Herunterskalieren verloren gehen, werden die Schlüssel mithilfe der Entfernungsrichtlinie [allkeys-lru](http://redis.io/topics/lru-cache) entfernt.

Hinweis: Für Caches des Tarifs "Standard" gilt zwar eine SLA (Service Level Agreement, Vereinbarung zum Servicelevel) von 99,9 % für die Verfügbarkeit, es gibt jedoch keine SLA für Datenverlust.

## Ist der Cache während der Skalierung verfügbar?

**Standard**-Caches bleiben während des Skalierungsvorgangs verfügbar.

**Basic**-Caches sind während der Skalierung auf eine andere Größe offline, bleiben jedoch bei der Skalierung von **Basic** auf **Standard** verfügbar.

## Nicht unterstützte Vorgänge

Ein **Standard**-Cache kann nicht zu einem **Basic**-Cache geändert werden.

Ein **Basic**-Cache kann auf einen **Standard**-Cache skaliert werden, die Größe kann jedoch nicht gleichzeitig geändert werden. Wenn Sie eine andere Größe benötigen, können Sie anschließend einen Skalierungsvorgang auf die gewünschte Größe durchführen.

Sie können von einem **C0**-Cache (250 MB) auf eine größere Größe hochskalieren, Sie können jedoch einen größeren Cache nicht auf **C0** herunterskalieren.

Wenn bei einem Skalierungsvorgang ein Fehler auftritt, versucht der Dienst, den Vorgang rückgängig zu machen, und der Cache wird auf die ursprüngliche Größe zurückgesetzt.

## Wie lange dauert die Skalierung?

Die Skalierung dauert ca. 20 Minuten, je nachdem, wie viele Daten sich im Cache befinden.

## Woher weiß ich, dass die Skalierung abgeschlossen ist?

Im Vorschauportal können Sie den Fortschritt der Skalierung anzeigen. Wenn die Skalierung abgeschlossen ist, ändert sich der Status des Caches zu **Wird ausgeführt**.

## Warum befindet sich diese Funktion in der Vorschau?

Wir haben die Funktion veröffentlicht, um Feedback zu erhalten. Basierend auf dem Feedback werden wir diese Funktion in Kürze in der allgemein verfügbaren Version veröffentlichen.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=Sept15_HO2-->