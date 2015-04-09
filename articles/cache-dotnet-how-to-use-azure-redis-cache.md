<properties 
	pageTitle="Verwenden des Azure-Redis-Caches" 
	description="Erfahren Sie, wie zur Verbesserung der Leistung Ihrer Azure-Anwendungen mit Azure-Redis-Cache beitragen." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="sdanie"/>

# Verwenden des Azure-Redis-Caches

Dieser Leitfaden beschreibt die ersten Schritte mit dem **Azure-Redis-Cache**. Die Beispiele sind in C\#-Code geschrieben und verwenden die .NET-API. Die behandelten Szenarien umfassen das **Erstellen und Konfigurieren eines Caches**, **Konfigurieren von Cacheclients**, **Hinzufügen und Entfernen von Objekten aus dem Cache** und **Speichern des ASP.NET-Sitzungszustands im Cache**. Weitere Informationen zum Verwenden von Azure-Redis-Cache finden Sie im Abschnitt [Nächste Schritte][].

<a name="video"></a>
## Einführung in Azure-Redis-Cache (Video)
In diesem Video stellen Saurabh Pant und Scott Hanselman den Azure-Redis-Cache vor. (Dauer: 07:45)  

> [AZURE.VIDEO azure-redis-cache-101-introduction-to-redis]

<a name="what-is"></a>
## Was ist Azure-Redis-Cache?

Microsoft Azure-Redis-Cache basiert auf dem beliebten Open Source Redis Cache. Sie erhalten Zugriff auf einen sicheren dedizierten Redis Cache, der von Microsoft verwaltet wird. Ein mit Azure Redis Cache erstellter Cache ist für beliebige Anwendungen in Microsoft Azure erreichbar.

Microsoft Azure Redis Cache ist in zwei Ebenen verfügbar:

-	**Einfach** - Einzelner Knoten. Mehrere Größen bis zu 53 GB.
-	**Standard** - Zwei Knoten: Primär/Replikat. Mehrere Größen bis zu 53 GB. 99,9 % SLA

Diese Ausführungen unterscheiden sich hinsichtlich der Features und des Preises. Die Features werden weiter unten in diesem Leitfaden behandelt, weitere Informationen zu den Preisen finden Sie unter [Cache - Preisdetails][].

Dieser Leitfaden bietet einen Überblick über die ersten Schritte mit Azure Redis Cache. Detaillierte Informationen zu diesen Features, die über den Rahmen dieses Leitfadens hinausgehen, finden Sie unter [Azure Redis Cache: Übersicht][].

<a name="getting-started-cache-service"></a>
## Erste Schritte mit Azure-Redis-Cache

Die ersten Schritte mit Azure Redis Cache sind einfach. Zunächst stellen Sie einen Cache bereit und konfigurieren ihn. Als Nächstes konfigurieren Sie die Cacheclients, sodass von diesen auf den Cache zugegriffen werden kann. Nach der Konfiguration der Cacheclients können Sie mit der Verwendung beginnen.

-	[Erstellen des Caches][]
-	[Konfigurieren der Cacheclients][]

<a name="create-cache"></a>
## Erstellen eines Caches

Melden Sie sich zum Erstellen eines Caches beim Azure-Vorschauportal an, und klicken Sie auf **Neu**, **Redis Cache**.

![New cache][NewCacheMenu]

Geben Sie im Fenster **Neuer Redis Cache** die gewünschte Konfiguration für den Cache an.

![Create cache][CacheCreate]

Geben Sie unter **DNS-Name** einen Unterdomänennamen für den Cacheendpunkt ein. Der Endpunktname muss eine Zeichenfolge zwischen sechs und zwanzig Zeichen sein. Er darf nur Kleinbuchstaben und Ziffern enthalten und muss mit einem Buchstaben beginnen.

Wählen Sie unter **Preisebene** die gewünschte Größe und Merkmale für den Cache aus. Redis Cache ist in den folgenden beiden Ebenen verfügbar.

-	**Einfach** - Einzelner Knoten, mehrere Größen bis zu 53 GB.
-	**Standard** - Zwei Knoten, Master/Slave, 99,9% SLA, mehrere Größen bis zu 53 GB.

Wählen Sie unter **Abonnement** das Azure-Abonnement für den Cache aus.

>[AZURE.NOTE] Wenn das Konto nur über ein Abonnement verfügt, wird dieses automatisch ausgewählt, und die Dropdownliste Abonnement wird nicht angezeigt.

Unter **Ressourcengruppe** können Sie eine Ressourcengruppe für Ihren Cache auswählen oder erstellen.

>[AZURE.NOTE] Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][]. 

Unter **Geolocation** können Sie einen geografischen Standort für Ihren Cache auswählen. Um optimale Leistungen zu erzielen, empfiehlt Microsoft, den Cache in der Region zu erstellen, in der sich auch die Cacheclientanwendung befindet.

Klicken Sie nach der Konfiguration der Optionen für den neuen Cache auf **Erstellen**. Die Erstellung des Caches kann einige Minuten dauern. Sie können den Fortschritt im Dashboard überwachen, um den Status zu überprüfen. Nach der Erstellung des Caches verfügt der neue Cache über den Status **Wird ausgeführt** und kann mit den Standardeinstellungen verwendet werden.

![Cache created][CacheCreated]

Nach der Erstellung Ihres Caches ist dieser im Fenster **Durchsuchen** verfügbar.

![Browse blade][BrowseCaches]

Klicken Sie auf **Caches**, um Ihre Caches anzuzeigen.

![Caches][Caches]

<a name="NuGet"></a>
## Konfigurieren der Cacheclients

Ein mit Azure Redis Cache erstellter Cache ist für beliebige Azure-Anwendungen erreichbar. In Visual Studio entwickelte .NET-Anwendungen können den **StackExchange.Redis**-Cacheclient verwenden. Dieser kann mithilfe eines NuGet-Pakets konfiguriert werden, um die Konfiguration von Cacheclientanwendungen zu vereinfachen. 

>[AZURE.NOTE] Weitere Informationen finden Sie auf der [StackExchange.Redis][]-Webseite auf Github und in der Dokumentation für [StackExchange.Redis][]-Cacheclients.

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen Sie **NuGet-Pakete verwalten** aus, um eine Clientanwendung in Visual Studio mithilfe des StackExchange.Redis-NuGet-Pakets zu konfigurieren. 

![Manage NuGet packages][NuGetMenu]

Geben Sie **StackExchange.Redis** oder **StackExchange.Redis.StrongName** in das Textfeld **Online-Suche** ein, wählen Sie die gewünschte Version aus den Ergebnissen aus, und klicken Sie auf **Installieren**.

>[AZURE.NOTE] Falls Sie eine Version der **StackExchange.Redis**-Clientbibliothek mit starkem Namen bevorzugen, wählen Sie **StackExchange.Redis.StrongName** aus; andernfalls wählen Sie **StackExchange.Redis** aus.

![StackExchange.Redis NuGet package][StackExchangeNuget]

Das NuGet-Paket wird heruntergeladen und fügt die benötigten Assemblyverweise zu Ihrer Clientanwendung hinzu, um mithilfe des StackExchange.Redis-Cacheclients auf den Azure Redis Cache zuzugreifen.

Nachdem die Zwischenspeicherung im Clientprojekt konfiguriert wurde, können Sie die in den folgenden Abschnitten beschriebenen Methoden verwenden, um mit dem Cache zu arbeiten.

<a name="working-with-caches"></a>
## Arbeiten mit Caches

In den Schritten in diesem Abschnitt wird die Ausführung allgemeiner Aufgaben mit dem Cache beschrieben.

-	[Herstellen einer Verbindung mit dem Cache][]
-   [Hinzufügen zu und Abrufen von Objekten aus dem Cache][]
-   [Speichern des ASP.NET-Sitzungszustands im Cache][]

<a name="connect-to-cache"></a>
## Herstellen einer Verbindung mit dem Cache

Um programmseitig mit dem Cache arbeiten zu können, benötigen Sie eine Referenz auf den Cache. Fügen Sie die folgende Zeile oben in jeder Datei hinzu, in der Sie mithilfe des StackExchange.Redis-Clients auf einen Azure Redis Cache zugreifen möchten:

    using StackExchange.Redis;

>[AZURE.NOTE] Der StackExchange.Redis-Client benötigt .NET Framework 4 oder höher.

Die Verbindung zum Azure-Redis-Cache wird von der  `ConnectionMultiplexer`-Klasse verwaltet. Diese Klasse wird in Ihrer gesamten Clientanwendung gemeinsam verwendet und wiederverwendet und muss nicht für jeden Vorgang neu erstellt werden. 

Um eine Verbindung zum Azure-Redis-Cache herzustellen und eine Instanz einer verbundenen  `ConnectionMultiplexer`-Klasse zu erhalten, rufen Sie die statische  `Connect`-Methode auf und übergeben den Cache-Endpunkt und den Schlüssel wie im folgenden Beispiel gezeigt. Übergeben Sie den im Dienstportal generierten Azure-Schlüssel im Kennwort-Parameter.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.NOTE] Warnung: Speichern Sie niemals Anmeldeinformationen im Quellcode. Ich zeige diese Daten hier im Code, um dieses Beispiel zu vereinfachen. Unter [Azure-Webseiten: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen][] finden Sie Informationen zum Speichern von Anmeldeinformationen.

Falls Sie kein SSL verwenden möchten, können Sie entweder  `ssl=false` festlegen oder einfach Endpunkt und Schlüssel übergeben.

>[AZURE.NOTE] Der Nicht-SSL-Port ist für neue Caches standardmäßig deaktiviert. Anweisungen zum Aktivieren des Nicht-SSL-Ports finden Sie im Abschnitt "Zugriffsports" zum Thema [Konfigurieren eines Caches in Azure Redis Cache][].

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

>[AZURE.NOTE] Weitere Informationen zu erweiterten Optionen für den Verbindungsaufbau finden Sie unter [StackExchange.Redis-Konfigurationsmodell][].

Cache-Endgerät und Schlüssel finden Sie im Azure-Vorschauportal im Fenster für Ihre Cache-Instanz.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Nach dem Verbindungsaufbau können Sie einen Verweis auf diese Redis-Cache-Datenbank zurückgeben, indem Sie die  `ConnectionMultiplexer.GetDatabase`-Methode aufrufen.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE] Das von der  `GetDatabase`-Methode zurückgegebene Objekt ist ein einfaches Durchgangsobjekt und muss nicht gespeichert werden.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

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

Sie können Elemente im Cache speichern und aus dem Cache abrufen, indem Sie die Methoden  `StringSet` und  `StringGet` verwenden.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>[AZURE.NOTE] Redis speichert die meisten Daten als Redis-Zeichenfolgen. Diese können jedoch unterschiedliche Datentypen enthalten, inklusive serialisierter Binärdaten, die zum Speichern von .NET-Objekten im Cache verwendet werden können.

Beim Aufruf von  `StringGet` wird das Objekt zurückgegeben, sofern es vorhanden ist. Andernfalls wird NULL zurückgegeben. In diesem Fall können Sie den Wert aus der gewünschten Datenquelle abrufen und für die zukünftige Verwendung im Cache speichern. Dies wird auch als Cache-Aside-Muster bezeichnet.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE] Azure Redis Cache kann .NET-Objekte und primitive Datentypen speichern. .NET-Objekte müssen jedoch zunächst serialisiert werden. Die Serialisierung ist Aufgabe des Anwendungsentwicklers und überlässt dem Entwickler die Freiheit bei der Wahl des Serialisierers. Weitere Informationen finden Sie unter [Arbeiten .NET-Objekten im Cache][].

<a name="specify-expiration"></a>
## Angeben der Ablaufzeit eines Elements im Cache

Um die Ablaufzeit eines Elements im Cache anzugeben, verwenden Sie den  `TimeSpan`-Parameter von  `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Speichern des ASP.NET-Sitzungszustands im Cache

Azure Redis Cache stellt einen Sitzungszustandsanbieter bereit, mit dem Sie den Sitzungszustand in einem Cache anstatt im Arbeitsspeicher oder in einer SQL Server-Datenbank speichern können. Um den Sitzungszustandsanbieter
zu verwenden, konfigurieren Sie zuerst den Cache und danach Ihre ASP.NET-Anwendung für den Cache über das Redis Cache-Sitzungszustands-NuGet-Paket.

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen Sie **NuGet-Pakete verwalten** aus, um eine Clientanwendung in Visual Studio mithilfe des Redis Cache Sitzungszustands-NuGet-Pakets zu konfigurieren. 

![Manage NuGet packages][NuGetMenu]

Geben Sie **RedisSessionStateProvider** in das Textfeld **Online-Suche** ein, wählen Sie das Paket aus, und klicken Sie auf **Installieren**.

![Redis Cache Session State NuGet Package][SessionStateNuGet]

Das NuGet-Paket wird heruntergeladen und fügt die benötigten Assemblyverweise sowie den folgenden Abschnitt zu Ihrer Web.config-Datei hinzu. Dieser Abschnitt enthält die Konfiguration, die Ihre ASP.NET-Anwendung für die Verwendung des Cache-Sitzungszustandsanbieters braucht.

  <sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>



Im kommentierten Bereich finden Sie Beispiele für die Attribute sowie Beispieleinstellungen.

Konfigurieren sie die Attribute mit den Werten aus dem Cachefenster im Vorschauportal, und konfigurieren Sie die restlichen Werte nach Ihren Wünschen.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

Kommentieren Sie unbedingt den Standard-Sitzungszustandsanbieter **InProc** aus.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Weitere Informationen zum Konfigurieren und Verwenden des Azure Redis Sitzungszustandsanbieters finden Sie unter [Azure Redis-Sitzungszustandsanbieter][].

<a name="next-steps"></a>
## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Azure-Redis-Caches vertraut gemacht haben, finden Sie unter den folgenden Links weitere Informationen zu komplexeren Cachingaufgaben.

-	[Aktivieren Sie die Cachediagnose](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), damit Sie die Integrität Ihres Caches [überwachen](https://msdn.microsoft.com/library/azure/dn763945.aspx) können. Sie können die Metriken im Verwaltungsportal anzeigen und sie auch (mit den Tools Ihrer Wahl) [herunterladen und prüfen](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring).
-	Weitere Informationen zum StackExchange.Redis-Client: [Dokumentation für den StackExchange.Redis-Cacheclient][]
	-	Auf Azure-Redis-Cache können viele Redis-Clients und Entwicklungssprachen zugreifen. Weitere Informationen finden Sie unter [http://redis.io/clients][] und [Entwickeln in anderen Sprachen für Azure-Redis-Cache][].
	-	Azure-Redis-Cache kann auch mit Diensten wie Redsmin verwendet werden. Weitere Informationen finden Sie unter [Abrufen einer Azure-Redis-Verbindungszeichenfolge und ihre Verwendung mit Redsmin][].
-	Lesen Sie außerdem die [Redis][]-Dokumentation und die Artikel zu [Redis-Datentypen][] und die [15-minütige Einführung in Redis-Datentypen][].
-   Weitere Informationen finden Sie in der MSDN-Referenz: [Azure-Redis-Cache][]


<!-- INTRA-TOPIC LINKS -->
[Nächste Schritte]: #next-steps
[Einführung in Azure-Redis-Cache (Video)]: #video
[Was ist Azure-Redis-Cache?]: #what-is
[Erstellen eines Azure-Caches]: #create-cache
[Welche Art von Cache ist richtig für mich?]: #choosing-cache
[Vorbereiten Ihres Visual Studio-Projekts zur Verwendung des Azure-Caches]: #prepare-vs
[Konfigurieren der Anwendung für die Verwendung von Caching]: #configure-app
[Erste Schritte mit Azure-Redis-Cache]: #getting-started-cache-service
[Erstellen des Caches]: #create-cache
[Konfigurieren des Caches]: #enable-caching
[Konfigurieren der Cacheclients]: #NuGet
[Arbeiten mit Caches]: #working-with-caches
[Herstellen einer Verbindung mit dem Cache]: #connect-to-cache
[Hinzufügen zu und Abrufen von Objekten aus dem Cache]: #add-object
[Angeben des Ablaufs eines Objekts im Cache]: #specify-expiration
[Speichern des ASP.NET-Sitzungszustands im Cache]: #store-session

  
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
[Entwickeln in anderen Sprachen für Azure-Redis-Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Abrufen einer Azure-Redis-Verbindungszeichenfolge und ihre Verwendung mit Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis-Sitzungszustandsanbieter]: http://go.microsoft.com/fwlink/?LinkId=398249
[Azure-Verwaltungsportal]: http://windows.azure.com/
[Vorgehensweise: Programmgesteuertes Konfigurieren eines Cacheclients]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Sitzungszustandsanbieter für Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Zwischenspeichern des Sitzungszustands]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Ausgabecacheanbieter für Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team-Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Konfigurieren der Größe von virtuellen Computern]: http://go.microsoft.com/fwlink/?LinkId=164387
[Überlegungen zur Planung der Azure Caching-Kapazität]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[Vorgehensweise: Deklaratives Festlegen der Cachefähigkeit einer ASP.NET-Seite]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[Vorgehensweise: Programmgesteuertes Festlegen der Cachefähigkeit einer Seite]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Konfigurieren eines Caches in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis-Konfigurationsmodell]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Arbeiten mit .NET-Objekten im Cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[Installation des NuGet-Paket-Managers]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache - Preisdetails]: http://www.windowsazure.com/pricing/details/cache/
[Verwaltungsportal]: https://manage.windowsazure.com/

[Übersicht über Azure-Redis-Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure-Redis-Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrieren zu Azure-Redis-Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure-Redis-Cache - Beispiele]: http://go.microsoft.com/fwlink/?LinkId=320840
[Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Dokumentation für den StackExchange.Redis-Cacheclient]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis-Datentypen]: http://redis.io/topics/data-types
[eine 15-minütige Einführung in Redis-Datentypen]: http://redis.io/topics/data-types-intro

[Microsoft Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

<!--HONumber=49-->