<properties pageTitle="How to Use Azure Redis Cache" metaKeywords="" description="Learn how to create a use a cache in Azure Redis Cache" metaCanonical="" services="" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Verwenden des Azure Redis Cache

Dieser Leitfaden beschreibt die ersten Schritte mit dem
**Azure Redis Cache (Vorschau)**. Die Beispiele sind in C\#-Code geschrieben und
verwenden die .NET-API. Die behandelten Szenarien umfassen das **Erstellen und Konfigurieren eines Caches**, **Konfigurieren von Cacheclients**, **Hinzufügen und Entfernen von Objekten aus dem Cache** und **Speichern des ASP.NET-Sitzungsstatus im Cache**. Weitere Informationen
zum Verwenden von Azure Redis Cache finden Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

-   [Was ist Azure Redis Cache?][]
-   [Erste Schritte mit Azure Redis Cache][]

    -   [Erstellen des Caches][]
    -   [Konfigurieren der Cacheclients][]
-   [Arbeiten mit Caches][]

    -   [Verbindungsaufbau zum Cache][]
    -   [Hinzufügen zu und Abrufen von Objekten aus dem Cache][]
    -   [Angeben der Ablaufzeit eines Objekts im Cache][]
    -   [Speichern des ASP.NET-Sitzungsstatus im Cache][]
-   [Nächste Schritte][]

<a name="what-is"></a>

## Was ist Azure Redis Cache?

Microsoft Azure Redis Cache (momentan in der Vorschauphase) basiert auf dem beliebten Open Source Redis Cache. Sie erhalten Zugriff auf einen sicheren dedizierten Redis Cache, der von Microsoft verwaltet wird. Ein mit Azure Redis Cache erstellter Cache ist für beliebige Anwendungen in Microsoft Azure erreichbar.

Microsoft Azure Redis Cache ist in zwei Ebenen verfügbar:

-   **Einfach** – Einzelner Knoten. Mehrere Größen bis zu 26 GB.
-   **Standard** – Zwei Knoten Master/Slave. Mehrere Größen bis zu 26 GB.

Diese Ausführungen unterscheiden sich hinsichtlich der Features und des Preises. Die Features werden weiter unten in diesem Leitfaden behandelt, weitere Informationen zu den Preisen finden Sie unter [Cache – Preisdetails][].

Dieser Leitfaden bietet einen Überblick über die ersten Schritte mit Azure Redis Cache. Detaillierte Informationen zu diesen Features, die über den Rahmen dieses Leitfadens hinausgehen, finden Sie unter [Azure Redis Cache: Übersicht][].

<a name="getting-started-cache-service"></a>

## Erste Schritte mit Azure Redis Cache

Die ersten Schritte mit Azure Redis Cache sind einfach. Zunächst stellen Sie einen Cache bereit und konfigurieren ihn. Als Nächstes konfigurieren Sie die Cacheclients, sodass von diesen auf den Cache zugegriffen werden kann. Nach der Konfiguration der Cacheclients können Sie mit der Verwendung beginnen.

-   [Erstellen des Caches][]
-   [Konfigurieren des Caches][]
-   [Konfigurieren der Cacheclients][]

<a name="create-cache"></a>

## Erstellen eines Caches

Um einen Cache zu erstellen, melden Sie sich beim Azure-Vorschauportal an und klicken auf **Neu**, **Redis Cache (Vorschau)**.

![Neuer Cache][]

Geben Sie im Fenster **Neuer Redis Cache (Vorschau)** die gewünschte Konfiguration für den Cache an.

![Cache erstellen][]

Geben Sie unter **DNS-Name** einen Unterdomänennamen für den Cacheendpunkt ein. Der Endpunktname muss eine Zeichenfolge zwischen sechs und zwanzig Zeichen sein. Er darf nur Kleinbuchstaben und Ziffern enthalten und muss mit einem Buchstaben beginnen.

Wählen Sie unter **Preisebene** die gewünschte Größe und Merkmale für den Cache aus. Redis Cache ist in den folgenden beiden Ebenen verfügbar.

-   **Einfach** - Einzelner Knoten, mehrere Größen bis zu 26 GB.
-   **Standard** - Zwei Knoten Master/Slave, 99,9% SLA (nach Ende der Vorschau), mehrere Größen bis zu 26 GB.

Wählen Sie unter **Abonnement** das Azure-Abonnement für den Cache aus.

> Wenn das Konto nur über ein Abonnement verfügt, wird dieses automatisch ausgewählt, und die Dropdownliste Abonnement wird nicht angezeigt.

Unter **Ressourcengruppe** können Sie eine Ressourcengruppe für Ihren Cache auswählen oder erstellen.

> Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][].

Unter **Geolocation** können Sie einen geografischen Standort für Ihren Cache auswählen. Um optimale Leistungen zu erzielen, empfiehlt Microsoft, den Cache in der Region zu erstellen, in der sich auch die Cacheclientanwendung befindet.

Klicken Sie nach der Konfiguration der Optionen für den neuen Cache auf **Erstellen**. Die Erstellung des Caches kann einige Minuten dauern. Sie können die den Fortschritt im Dashboard überwachen, um den Status zu überprüfen. Nach der Erstellung des Caches hat der neue Cache den Status **Wird ausgeführt** und kann mit den Standardeinstellungen verwendet werden.

![Cache erstellt][]

Nach der Erstellung Ihres Caches ist dieser im Fenster **Durchsuchen** verfügbar.

![Fenster Durchsuchen][]

Klicken Sie auf **Caches**, um Ihre Caches anzuzeigen.

![Caches][]

<a name="NuGet"></a>

## Konfigurieren der Cacheclients

Ein mit Azure Redis Cache erstellter Cache ist für beliebige Azure-Anwendungen erreichbar. In Visual Studio entwickelte .NET-Anwendungen können den **StackExchange.Redis**-Cacheclient verwenden. Dieser kann mithilfe eines NuGet-Pakets konfiguriert werden, um die Konfiguration von Cacheclientanwendungen zu vereinfachen.

> Weitere Informationen finden Sie auf der [StackExchange.Redis][]-Webseite auf Github und in der [Dokumentation für StackExchange.Redis-Cacheclients][].

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen Sie **NuGet-Pakete verwalten** aus, um eine Clientanwendung in Visual Studio mithilfe des StackExchange.Redis-NuGet-Pakets zu konfigurieren.

![NuGet-Pakete verwalten][]

Geben Sie **StackExchange.Redis** oder **StackExchange.Redis.StrongName** in das Textfeld **Online-Suche** ein, wählen Sie die gewünschte Version aus den Ergebnissen aus und klicken Sie auf **Installieren**.

> Falls Sie eine Version der **StackExchange.Redis**-Clientbibliothek mit starkem Namen bevorzugen, wählen Sie **StackExchange.Redis.StrongName** aus; andernfalls wählen Sie **StackExchange.Redis** aus.

![StackExchange.Redis-NuGet-Paket][]

Das NuGet-Paket wird heruntergeladen und fügt die benötigten Assemblyverweise zu Ihrer Clientanwendung hinzu, um mithilfe des StackExchange.Redis-Cacheclients auf den Azure Redis Cache zuzugreifen.

Nachdem die Zwischenspeicherung im Clientprojekt konfiguriert wurde, können Sie die in den folgenden Abschnitten beschriebenen Methoden verwenden, um mit dem Cache zu arbeiten.

<a name="working-with-caches"></a>

## Arbeiten mit Caches

In den Schritten in diesem Abschnitt wird die Ausführung allgemeiner Aufgaben mit dem Cache beschrieben.

-   [Verbindungsaufbau zum Cache][]
-   [Hinzufügen zu und Abrufen von Objekten aus dem Cache][]
-   [Speichern des ASP.NET-Sitzungsstatus im Cache][]

<a name="connect-to-cache"></a>

## Verbindungsaufbau zum Cache

Um programmseitig mit dem Cache arbeiten zu können, benötigen Sie eine Referenz auf den Cache. Fügen Sie die folgende Zeile oben in jeder Datei hinzu, in der Sie mithilfe des StackExchange.Redis-Clients auf einen Azure Redis Cache zugreifen möchten:

    using StackExchange.Redis;

> Der StackExchange.Redis-Client benötigt .NET Framework 4 oder höher.

Die Verbindung zum Azure Redis Cache wird von der Klasse `ConnectionMultiplexer` verwaltet. Diese Klasse wird in Ihrer gesamten Clientanwendung gemeinsam verwendet und wiederverwendet und muss nicht für jede Operation neu erstellt werden.

Um eine Verbindung zum Azure Redis Cache herzustellen und eine Instanz einer verbundenen `ConnectionMultiplexer`-Klasse zu erhalten, rufen Sie die statische Methode `Connect` auf und übergeben Sie den Cache-Endpunkt und den Schlüssel wie im folgenden Beispiel gezeigt. Übergeben Sie den im Dienstportal generierten Azure-Schlüssel im Kennwort-Parameter.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

> Warnung: Speichern Sie niemals Anmeldeinformationen im Quellcode. Ich zeige diese Daten hier im Code, um dieses Beispiel zu vereinfachen. Unter [Azure-Webseiten: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen][] finden Sie Informationen zum Speichern von Anmeldeinformationen.

Falls Sie kein SSL verwenden möchten, können Sie entweder `ssl=false` setzen oder einfach Endpunkt und Schlüssel übergeben.

    connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

> Weitere Informationen zu erweiterten Optionen für den Verbindungsaufbau finden Sie unter [StackExchange.Redis-Konfigurationsmodell][].

Cache-Endgerät und Schlüssel finden Sie im Azure-Vorschauportal im Fenster für Ihre Cache-Instanz.

![Cacheeigenschaften][]

![Schlüssel verwalten][]

Nach dem Verbindungsaufbau können Sie einen Verweis auf diese Redis Cache-Datenbank zurückgeben, indem Sie die Methode `ConnectionMultiplexer.GetDatabase` aufrufen.

    // connection referes to a previously configured ConnectionMultiplexer
    IDatabase cache = connection.GetDatabase();

> Das von der Methode `GetDatabase` zurückgegebene Objekt ist ein einfaches Durchgangsobjekt und muss nicht gespeichert werden.

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

Sie können Elemente im Cache speichern und aus dem Cache abrufen, indem Sie die Methoden `StringSet` und `StringGet` verwenden.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

> Redis speichert die meisten Daten als Redis-Zeichenfolgen. Diese können jedoch unterschiedliche Datentypen enthalten, inklusive serialisierter Binärdaten, die zum Speichern von .NET-Objekten im Cache verwendet werden können.

Beim Aufruf von `StringGet` wird das Objekt zurückgegeben, wenn es existiert, andernfalls wird null zurückgegeben. In diesem Fall können Sie den Wert aus der gewünschten Datenquelle abrufen und für die zukünftige Verwendung im Cache speichern. Dies wird auch als Cache-Aside-Muster bezeichnet.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

> Azure Redis Cache kann .NET-Objekte und primitive Datentypen speichern. .NET-Objekte müssen jedoch zunächst serialisiert werden. Die Serialisierung ist Aufgabe des Anwendungsentwicklers und überlässt dem Entwickler die Freiheit bei der Wahl des Serialisierers. Weitere Informationen finden Sie unter [Arbeiten .NET-Objekten im Cache][].

<a name="specify-expiration"></a>

## Angeben der Ablaufzeit eines Elements im Cache

Um die Ablaufzeit eines Elements im Cache anzugeben, verwenden Sie den `TimeSpan`-Parameter von `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

<a name="store-session"></a>

## Speichern des ASP.NET-Sitzungsstatus im Cache

Azure Redis Cache stellt einen Sitzungszustandsanbieter bereit, mit dem Sie den Sitzungsstatus in einem Cache anstatt im Arbeitsspeicher oder in einer SQL Server-Datenbank speichern können. Konfigurieren Sie zuerst den Cache
und danach Ihre ASP.NET-Anwendung mithilfe des Redis Cache Sitzungszustands-NuGet-Pakets, um den Sitzungszustandsanbieter zu verwenden.

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen Sie **NuGet-Pakete verwalten** aus, um eine Clientanwendung in Visual Studio mithilfe des Redis Cache Sitzungszustands-NuGet-Pakets zu konfigurieren.

![NuGet-Pakete verwalten][]

Geben Sie **RedisSessionStateProvider** in das Textfeld **Online-Suche** ein, wählen Sie das Paket aus und klicken Sie auf **Installieren**.

![Redis Cache Sitzungszustands-NuGet-Paket][]

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
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
        accessKey="..." ssl="false" />
      </providers>
    </sessionState>

> Beachten Sie, dass das Wiederholungs-Zeitlimit in Millisekunden angegeben wird.

Kommentieren Sie unbedingt den Standard-Sitzungszustandsanbieter **InProc** aus.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Weitere Informationen zum Konfigurieren und Verwenden des Azure Redis Sitzungszustandsanbieters finden Sie unter [Azure Redis Sitzungszustandsanbieter][].

<a name="next-steps"></a>

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Azure Redis Cache vertraut gemacht haben,
finden Sie unter den folgenden Links weitere Informationen zu komplexeren Cachingaufgaben.

-   Weitere Informationen zum StackExchange.Redis-Client: [Dokumentation für den StackExchange.Redis-Cacheclient][Dokumentation für StackExchange.Redis-Cacheclients]
-   Lesen Sie außerdem die [Redis][]-Dokumentation und die Artikel zu [Redis-Datentypen][] und die [15-minütige Einführung in Redis-Datentypen][].
-   Weitere Informationen finden Sie in der MSDN-Referenz: [Azure Redis Cache][]

<!-- INTRA-TOPIC LINKS --> <!-- IMAGES --> <!-- LINKS -->

  [Nächste Schritte]: #next-steps
  [Was ist Azure Redis Cache?]: #what-is
  [Erste Schritte mit Azure Redis Cache]: #getting-started-cache-service
  [Erstellen des Caches]: #create-cache
  [Konfigurieren der Cacheclients]: #NuGet
  [Arbeiten mit Caches]: #working-with-caches
  [Verbindungsaufbau zum Cache]: #connect-to-cache
  [Hinzufügen zu und Abrufen von Objekten aus dem Cache]: #add-object
  [Angeben der Ablaufzeit eines Objekts im Cache]: #specify-expiration
  [Speichern des ASP.NET-Sitzungsstatus im Cache]: #store-session
  [Cache – Preisdetails]: http://www.windowsazure.com/en-us/pricing/details/cache/
  [Azure Redis Cache: Übersicht]: http://go.microsoft.com/fwlink/?LinkId=320830
  [Konfigurieren des Caches]: #enable-caching
  [Neuer Cache]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png
  [Cache erstellen]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png
  [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: http://azure.microsoft.com/de-de/documentation/articles/azure-preview-portal-using-resource-groups/
  [Cache erstellt]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png
  [Fenster Durchsuchen]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png
  [Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png
  [StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
  [Dokumentation für StackExchange.Redis-Cacheclients]: http://github.com/StackExchange/StackExchange.Redis#documentation
  [NuGet-Pakete verwalten]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png
  [StackExchange.Redis-NuGet-Paket]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png
  [Azure-Webseiten: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [StackExchange.Redis-Konfigurationsmodell]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md
  [Cacheeigenschaften]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png
  [Schlüssel verwalten]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png
  [Arbeiten .NET-Objekten im Cache]: http://msdn.microsoft.com/en-us/library/dn690521.aspx#Objects
  [Redis Cache Sitzungszustands-NuGet-Paket]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png
  [Azure Redis Sitzungszustandsanbieter]: http://go.microsoft.com/fwlink/?LinkId=398249
  [Redis]: http://redis.io/documentation
  [Redis-Datentypen]: http://redis.io/topics/data-types
  [15-minütige Einführung in Redis-Datentypen]: http://redis.io/topics/data-types-intro
  [Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247
