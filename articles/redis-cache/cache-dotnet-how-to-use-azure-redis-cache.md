<properties 
	pageTitle="Verwenden von Azure Redis Cache" 
	description="Erfahren Sie, wie zur Verbesserung der Leistung Ihrer Azure-Anwendungen mit Azure Redis Cache beitragen." 
	services="redis-cache,app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/30/2015" 
	ms.author="sdanie"/>

# Verwenden von Azure Redis Cache

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Dieser Leitfaden beschreibt die ersten Schritte mit **Azure Redis Cache**. Microsoft Azure Redis Cache basiert auf dem beliebten Open Source Redis Cache. Sie erhalten Zugriff auf einen sicheren dedizierten Redis Cache, der von Microsoft verwaltet wird. Ein mit Azure Redis Cache erstellter Cache ist für beliebige Anwendungen in Microsoft Azure erreichbar.

Microsoft Azure Redis Cache ist in den folgenden Ebenen verfügbar:

-	**Basic** – Einzelner Knoten. Mehrere Größen bis zu 53 GB.
-	**Standard** – Zwei Knoten: Primär/Replikat. Mehrere Größen bis zu 53 GB. 99,9 % SLA
-	**Premium** – Zwei Knoten (Primär/Replikat) mit bis zu 10 Shards. Mehrere Größen von 6 GB bis zu 530 GB (für mehr wenden Sie sich an uns). Alle Funktionen der Standard-Ebene und weitere umfassen die Unterstützung für [Redis-Cluster](cache-how-to-premium-clustering.md), [Redis-Persistenz](cache-how-to-premium-persistence.md), und [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9 % SLA

Diese Ausführungen unterscheiden sich hinsichtlich der Features und des Preises. Weitere Informationen zu Preisen finden Sie unter [Cache – Preisübersicht][].

Dieser Anleitung erfahren Sie, wie Sie den [StackExchange.Redis][]-Client mit von C#-Code verwenden. Die behandelten Szenarien umfassen das **Erstellen und Konfigurieren eines Caches**, das **Konfigurieren von Cacheclients** und das **Hinzufügen und Entfernen von Objekten aus dem Cache**. Weitere Informationen zum Verwenden von Azure Redis Cache finden Sie im Abschnitt [Nächste Schritte][].

<a name="getting-started-cache-service"></a>
## Erste Schritte mit Azure Redis Cache

Die ersten Schritte mit Azure Redis Cache sind einfach. Zunächst stellen Sie einen Cache bereit und konfigurieren ihn. Als Nächstes konfigurieren Sie die Cacheclients, sodass von diesen auf den Cache zugegriffen werden kann. Nach der Konfiguration der Cacheclients können Sie mit der Verwendung beginnen.

-	[Erstellen des Caches][]
-	[Konfigurieren der Cacheclients][]

<a name="create-cache"></a>
## Erstellen eines Caches

Um einen Cache zu erstellen, melden Sie sich beim [Azure-Vorschauportal][] an und klicken auf **Neu**, **Daten und Speicher**, **Redis Cache**.

![Neuer Cache][NewCacheMenu]

>[AZURE.NOTE]Wenn Sie noch kein Azure-Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion][].

Geben Sie auf dem Blatt **Neuer Redis Cache** die gewünschte Konfiguration für den Cache an.

![Cache erstellen][CacheCreate]

-	Geben Sie unter **DNS-Name** einen Unterdomänennamen für den Cacheendpunkt ein. Der Endpunktname muss eine Zeichenfolge zwischen sechs und zwanzig Zeichen sein. Er darf nur Kleinbuchstaben und Ziffern enthalten und muss mit einem Buchstaben beginnen.
-	Wählen Sie unter **Abonnement** das Azure-Abonnement für den Cache aus. Wenn das Konto nur über ein Abonnement verfügt, wird dieses automatisch ausgewählt, und die Dropdownliste **Abonnement** wird nicht angezeigt.
-	Unter **Ressourcengruppe** können Sie eine Ressourcengruppe für Ihren Cache auswählen oder erstellen. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][]. 
-	Unter **Standort** können Sie einen geografischen Standort für Ihren Cache auswählen. Um optimale Leistungen zu erzielen, empfiehlt Microsoft, den Cache in der Region zu erstellen, in der sich auch die Cacheclientanwendung befindet.
-	Wählen Sie unter **Tarif** die gewünschte Größe und Merkmale für den Cache aus.
-	**Redis-Cluster** ermöglichen Ihnen das Erstellen von Caches größer als 53 GB und das Sharding von Daten auf mehrere Redis-Knoten. Weitere Informationen finden Sie unter [Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md).
-	Die **Redis-Persistenz** ermöglicht das dauerhafte Speichern Ihres Caches in einem Azure Storage-Konto. Informationen zum Konfigurieren von Persistenz finden Sie unter [Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md).
-	**Virtual Network** bietet erhöhte Sicherheit und Isolierung durch Einschränken des Zugriffs auf Ihren Cache auf ausschließlich Clients im angegebenen Azure Virtual Network. Sie können alle Features von VNet, z. B. Subnetze, Richtlinien für die Zugriffssteuerung und andere Features, verwenden, um den Zugriff auf Redis weiter einzuschränken. Weitere Informationen finden Sie unter [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md).
-	Verwenden Sie die **Diagnose**, um ein Speicherkonto für Cachemetriken anzugeben. Weitere Informationen zum Konfigurieren und Anzeigen von Cachemetriken finden Sie unter [Überwachen von Azure Redis Cache](cache-how-to-monitor.md).

Klicken Sie nach der Konfiguration der Optionen für den neuen Cache auf **Erstellen**. Die Erstellung des Caches kann einige Minuten dauern. Sie können den Fortschritt im Startmenü überwachen, um den Status zu überprüfen. Nach der Erstellung des Caches hat der neue Cache den Status **Wird ausgeführt** und kann mit den Standardeinstellungen verwendet werden.

![Cache erstellt][CacheCreated]

Nach der Erstellung Ihres Caches ist dieser im Blatt **Durchsuchen** verfügbar.

![Blatt "Durchsuchen"][BrowseCaches]

Klicken Sie auf **Redis Caches**, um Ihre Caches anzuzeigen.

![Caches][Caches]

<a name="NuGet"></a>
## Konfigurieren der Cacheclients

Ein mit Azure Redis Cache erstellter Cache ist für beliebige Azure-Anwendungen erreichbar. In Visual Studio entwickelte .NET-Anwendungen können den **StackExchange.Redis**-Cacheclient verwenden. Dieser kann mithilfe eines NuGet-Pakets konfiguriert werden, um die Konfiguration von Cacheclientanwendungen zu vereinfachen.

>[AZURE.NOTE]Weitere Informationen finden Sie auf der [StackExchange.Redis][]-Seite auf GitHub und in der [Dokumentation für den StackExchange.Redis-Cacheclient][].

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen Sie **NuGet-Pakete verwalten** aus, um eine Clientanwendung in Visual Studio mithilfe des StackExchange.Redis-NuGet-Pakets zu konfigurieren.

![NuGet-Pakete verwalten][NuGetMenu]

Geben Sie **StackExchange.Redis** oder **StackExchange.Redis.StrongName** in das Textfeld **Online-Suche** ein, wählen Sie die gewünschte Version aus den Ergebnissen aus und klicken Sie auf **Installieren**.

>[AZURE.NOTE]Falls Sie eine Version der **StackExchange.Redis**-Clientbibliothek mit starkem Namen bevorzugen, wählen Sie **StackExchange.Redis.StrongName** aus; andernfalls wählen Sie **StackExchange.Redis** aus.

![StackExchange.Redis-NuGet-Paket][StackExchangeNuget]

Das NuGet-Paket wird heruntergeladen und fügt die benötigten Assemblyverweise zu Ihrer Clientanwendung hinzu, um mithilfe des StackExchange.Redis-Cacheclients auf den Azure Redis Cache zuzugreifen.

Nachdem die Zwischenspeicherung im Clientprojekt konfiguriert wurde, können Sie die in den folgenden Abschnitten beschriebenen Methoden verwenden, um mit dem Cache zu arbeiten.

<a name="working-with-caches"></a>
## Arbeiten mit Caches

In den Schritten in diesem Abschnitt wird die Ausführung allgemeiner Aufgaben mit dem Cache beschrieben.

-	[Herstellen einer Verbindung mit dem Cache][]
-   [Hinzufügen zu und Abrufen von Objekten aus dem Cache][]
-   [Arbeiten mit .NET-Objekten im Cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Herstellen einer Verbindung mit dem Cache

Um programmseitig mit dem Cache arbeiten zu können, benötigen Sie eine Referenz auf den Cache. Fügen Sie die folgende Zeile oben in jeder Datei hinzu, in der Sie mithilfe des „StackExchange.Redis“-Clients auf einen Azure Redis Cache zugreifen möchten:

    using StackExchange.Redis;

>[AZURE.NOTE]Der StackExchange.Redis-Client benötigt .NET Framework 4 oder höher.

Die Verbindung zum Azure Redis Cache wird von der `ConnectionMultiplexer`-Klasse verwaltet. Diese Klasse wird in Ihrer gesamten Clientanwendung gemeinsam verwendet und wiederverwendet und muss nicht für jeden Vorgang neu erstellt werden.

Um eine Verbindung zu einem Azure Redis Cache herzustellen und eine Instanz einer verbundenen `ConnectionMultiplexer`-Klasse zu erhalten, rufen Sie die statische `Connect`-Methode auf und übergeben den Cacheendpunkt und den Schlüssel wie im folgenden Beispiel gezeigt. Übergeben Sie den im Vorschauportal generierten Azure-Schlüssel im Parameter für das Kennwort.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT]Warnung: Speichern Sie niemals Anmeldeinformationen im Quellcode. Ich zeige diese Daten hier im Code, um dieses Beispiel zu vereinfachen. Informationen zum Speichern von Anmeldeinformationen finden Sie unter [Funktionsweise von Anwendungs- und Verbindungszeichenfolgen][].

Wenn Sie SSL nicht verwenden möchten, legen Sie entweder `ssl=false` fest, oder lassen Sie den `ssl`-Parameter aus.

>[AZURE.NOTE]Der Nicht-SSL-Port ist für neue Caches standardmäßig deaktiviert. Informationen zum Aktivieren des Nicht-SSL-Ports finden Sie unter [Zugriffsports](cache-configure.md#access-ports).

Ein Ansatz zur Freigabe einer `ConnectionMultiplexer`-Instanz in Ihrer Anwendung ist das Verwenden einer statischen Eigenschaft, die wie im folgenden Beispiel eine verbundene Instanz zurückgibt. Dies ist eine threadsichere Möglichkeit, nur eine einzige verbundene `ConnectionMultiplexer`-Instanz zu initialisieren. In diesen Beispielen ist `abortConnect` auf „false“ festgelegt, was bedeutet, dass der Aufruf erfolgreich ist, auch wenn eine Verbindung mit Azure Redis Cache nicht hergestellt wird. Eine wichtige Funktion von `ConnectionMultiplexer` ist, dass die Verbindung mit dem Cache automatisch wiederhergestellt wird, sobald das Netzwerkproblem oder andere Ursachen beseitigt wurden.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

Weitere Informationen zu erweiterten Optionen für den Verbindungsaufbau finden Sie unter [StackExchange.Redis-Konfigurationsmodell][].

Cacheendpunkt und Schlüssel finden Sie auf dem Blatt **Redis-Cache** Ihrer Cache-Instanz.

![Cacheeigenschaften][CacheProperties]

![Schlüssel verwalten][ManageKeys]

Nach dem Verbindungsaufbau können Sie einen Verweis auf diese Redis-Cachedatenbank zurückgeben, indem Sie die `ConnectionMultiplexer.GetDatabase`-Methode aufrufen. Das von der `GetDatabase`-Methode zurückgegebene Objekt ist ein einfaches Durchgangsobjekt und muss nicht gespeichert werden.

	// Connection refers to a property that returns a ConnectionMultiplexer
	// as shown in the previous example.
	IDatabase cache = Connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

Sie sind nun in der Lage, sich mit einer Azure Redis Cache-In zu verbinden und einen Verweis auf die Cache-Datenbank zurückzugeben und können nun beginnen, mit dem Cache zu arbeiten.

<a name="add-object"></a>
## Hinzufügen zu und Abrufen von Objekten aus dem Cache

Sie können Elemente im Cache speichern und aus dem Cache abrufen, indem Sie die Methoden `StringSet` und `StringGet` verwenden.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

Redis speichert die meisten Daten als Redis-Zeichenfolgen. Diese können jedoch unterschiedliche Datentypen enthalten, inklusive serialisierter Binärdaten, die zum Speichern von .NET-Objekten im Cache verwendet werden können.

Beim Aufruf von `StringGet` wird das Objekt zurückgegeben, sofern es vorhanden ist. Andernfalls wird `null` zurückgegeben. In diesem Fall können Sie den Wert aus der gewünschten Datenquelle abrufen und für die zukünftige Verwendung im Cache speichern. Dies wird auch als Cache-Aside-Muster bezeichnet.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Um die Ablaufzeit eines Elements im Cache anzugeben, verwenden Sie den `TimeSpan`-Parameter von `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Arbeiten mit .NET-Objekten im Cache

Azure Redis Cache kann .NET-Objekte und primitive Datentypen speichern. .NET-Objekte müssen jedoch zunächst serialisiert werden. Die Serialisierung ist Aufgabe des Anwendungsentwicklers und überlässt dem Entwickler die Freiheit bei der Wahl des Serialisierers.

Eine einfache Möglichkeit zum Serialisieren von Objekten stellen das Verwenden der `JsonConvert`-Serialisierungsmethoden in [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) und Serialisieren in und aus JSON dar. Das folgende Beispiel zeigt „get“ und „set“ mit einer `Employee`- Objektinstanz.


	class Employee
	{
	    public int Id { get; set; }
	    public string Name { get; set; }
	
	    public Employee(int EmployeeId, string Name)
	    {
	        this.Id = EmployeeId;
	        this.Name = Name;
	    }
	}

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen vertraut gemacht haben, lesen Sie die folgenden Artikel zu Azure Redis Cache.

-	Überprüfen Sie die ASP.NET-Anbieter für Azure Redis Cache.
	-	[Azure Redis-Sitzungszustandsanbieter](cache-asp.net-session-state-provider.md)
	-	[Azure Redis Cache ASP.NET-Ausgabecacheanbieter](cache-asp.net-output-cache-provider.md)
-	[Aktivieren Sie die Cachediagnose](cache-how-to-monitor.md#enable-cache-diagnostics), damit Sie die Integrität Ihres Caches [überwachen](cache-how-to-monitor.md) können. Sie können die Metriken im Vorschauportal anzeigen und sie anschließend mit einem Tool Ihrer Wahl [herunterladen und prüfen](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring).
-	Lesen Sie die [Dokumentation für den StackExchange.Redis-Cacheclient][].
	-	Auf Azure Redis Cache können viele Redis-Clients und Entwicklungssprachen zugreifen. Weitere Informationen finden Sie unter [http://redis.io/clients][] und [Entwickeln in anderen Sprachen für Azure Redis Cache][].
	-	Azure Redis Cache kann auch mit Diensten wie Redsmin verwendet werden. Weitere Informationen finden Sie unter [Abrufen einer Azure Redis-Verbindungszeichenfolge und ihre Verwendung mit Redsmin][].
-	Lesen Sie außerdem die [Redis][]-Dokumentation und die Artikel zu [Redis-Datentypen][] und die [15-minütige Einführung in Redis-Datentypen][].



<!-- INTRA-TOPIC LINKS -->
[Nächste Schritte]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Erstellen des Caches]: #create-cache
[Configure the cache]: #enable-caching
[Konfigurieren der Cacheclients]: #NuGet
[Working with Caches]: #working-with-caches
[Herstellen einer Verbindung mit dem Cache]: #connect-to-cache
[Hinzufügen zu und Abrufen von Objekten aus dem Cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Entwickeln in anderen Sprachen für Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Abrufen einer Azure Redis-Verbindungszeichenfolge und ihre Verwendung mit Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis-Konfigurationsmodell]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache – Preisübersicht]: http://www.windowsazure.com/pricing/details/cache/
[Azure-Vorschauportal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Dokumentation für den StackExchange.Redis-Cacheclient]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis-Datentypen]: http://redis.io/topics/data-types
[15-minütige Einführung in Redis-Datentypen]: http://redis.io/topics/data-types-intro

[Funktionsweise von Anwendungs- und Verbindungszeichenfolgen]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[Kostenlose Azure-Testversion]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=AcomDC_1203_2015-->