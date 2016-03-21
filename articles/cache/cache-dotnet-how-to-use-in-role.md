<properties 
	pageTitle="Verwenden des In-Role Caches (.NET) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Azure In-Role Cache verwenden. Die Beispiele sind in C#-Code geschrieben und verwenden die .NET API." 
	services="cache" 
	documentationCenter=".net" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>






# Verwenden des In-Role Caches (Azure Cache)

Dieser Leitfaden zeigt Ihnen die ersten Schritte mit **In-Role Cache für Azure Cache**. Die Beispiele sind in C#-Code geschrieben und verwenden die .NET API. Es werden folgende Szenarien vorgestellt: **Konfigurieren eines Cacheclusters**, **Konfigurieren von Cacheclients**, **Hinzufügen und Entfernen von Objekten vom Cache, Speichern des ASP.NET-Sitzungszustands im Cache** und **Aktivieren des ASP.NET-Seitenausgabecaches mithilfe des Cache**. Weitere Informationen zur Verwendung des In-Role Cache erhalten Sie unter [Nächste Schritte][].

>[AZURE.IMPORTANT]Wir geben die Deaktivierung der Dienste Azure Managed Cache Service und Azure In-Role Cache zum 30. November 2016 bekannt. Es wird empfohlen, als Vorbereitung für diese Einstellung dieser Dienste zu Azure Redis Cache zu migrieren. Weitere Informationen zu Daten und Hinweise zur Migration finden Sie unter [Welches Azure Cache-Angebot ist für mich das richtige?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

<a name="what-is"></a>
## Was ist In-Role Cache?

In-Role Caches bieten eine Caching-Schicht für Azure-Anwendungen. Das Caching erhöht die Leistung, indem es Daten vorübergehend im Speicher anderer Backend-Quellen speichert. Außerdem kann es die Kosten in Zusammenhang mit Datenbanktransaktionen in der Cloud reduzieren. Der In-Role Cache umfasst folgende Features:

-   Vorbereitete ASP.NET-Anbieter für Sitzungszustand und Seitenausgabecaching für schnellere Webanwendungen, ohne Änderung des Anwendungscodes
-   Zwischenspeichern beliebiger serialisierbarer verwalteter Objekte, z. B. CLR-Objekte, Zeilen, XML und Binärdaten
-   Konsistentes Entwicklungsmodell für Azure und Windows Server AppFabric

In-Role Cache stellt eine Möglichkeit zum Durchführen von Cachingaktionen bereit, indem ein Teil des Arbeitsspeichers der virtuellen Maschinen verwendet wird, in denen die Rolleninstanzen Ihrer Azure-Clouddienste (auch als gehostete Dienste bekannt) gehostet werden. Dadurch erhalten Sie eine höhere Flexibilität bei der Bereitstellung, die Caches können sehr groß sein und es gibt keine Cache-spezifischen Beschränkungen.

>[AZURE.IMPORTANT] Ab Azure SDK 2.6 verwendet In-Role Cache das Microsoft Azure Storage SDK, Version 4.3. In früheren Versionen des Azure SDK verwendete In-Role Cache das Azure Storage SDK 1.7. Anwendungen, die In-Role Cache mit Azure SDK-Versionen vor 2.6 verwenden, sollten zu Azure SDK 2.6 migriert werden, bevor Azure Storage Version 2011-08-18 am 1. August 2016 außer Betrieb gesetzt wird. Weitere Informationen finden Sie unter [Versionshinweise zu Azure SDK 2.6 – In-Role Cache](../azure-sdk-dotnet-release-notes-2_6.md#in-role-cache-updates) und [Aktualisierung bezüglich der Entfernung der Version Microsoft Azure Storage: Verlängerung bis 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

Das Caching von Rolleninstanzen bietet folgende Vorteile:

-	Keine Zusatzkosten für das Caching. Sie zahlen lediglich für die Computeressourcen, die den Cache hosten.
-	Beschränkungen und Engpässe werden eliminiert.
-	Mehr Kontrolle und Isolation. 
-	Verbesserte Leistung.
-	Caches werden automatisch in der Größe angepasst, wenn Rollen vergrößert oder verkleinert werden. Effektives Skalieren des Speichers, der für das Caching zur Verfügung seht, wenn Rolleninstanzen hinzugefügt oder entfernt werden.
-	Genaues Entwicklungszeit-Debugging 
-	Unterstützung des Memcache-Protokolls

Darüber hinaus bietet das Caching von Rolleninstanzen die folgenden konfigurierbaren Optionen:

-	Konfigurieren einer dedizierten Rolle für das Caching oder Zusammenstellen des Cachings auf bereits bestehenden Rollen. 
-	Bereitstellen des Caches für verschiedene Clients in der gleichen Clouddienstbereitstellung
-	Erstellen unterschiedlich bezeichneter Caches mit verschiedenen Eigenschaften
-	Optionales Konfigurieren hoher Verfügbarkeit individueller Caches
-	Verwenden erweiterter Caching-Funktionen wie Regionen, Tagging und Benachrichtigungen

Dieser Leitfaden bietet einen Überblick über die ersten Schritte mit In-Role Cache. Detaillierte Informationen zu diesen Features, die über den Rahmen dieses Leitfadens hinausgehen, finden Sie unter [Überblick In-Role Cache][].

<a name="getting-started-cache-role-instance"></a>
## Erste Schritte mit dem In-Role Cache

In-Role Cache bietet eine Möglichkeit, um das Caching über den Speicher der virtuellen Maschine zu aktivieren, der Ihre Rolleninstanzen hostet. Die Rolleninstanzen, die Ihre Caches hosten, werden als **Cachecluster** bezeichnet. Es gibt zwei Bereitstellungstopologien für das Caching auf Rolleninstanzen:

-	**Dediziertes Rollencaching**: Die Rolleninstanzen werden ausschließlich für das Caching verwendet.
-	**Zusammengestelltes Rollencaching**: Der Cache teilt die VM-Ressourcen (Bandbreite, CPU und Speicher) mit der Anwendung.

Um das Caching auf Rolleninstanzen zu verwenden, müssen Sie einen Cachecluster und anschließend die Cacheclients konfigurieren, damit sie auf den Cachecluster zugreifen können.

-	[Konfigurieren eines Cacheclusters][]
-	[Konfigurieren der Cacheclients][]

<a name="enable-caching"></a>
## Konfigurieren eines Cacheclusters

Zum Konfigurieren eines **Zusammengestellten Rollencacheclusters** müssen Sie die Rolle auswählen, in der Sie den Cachecluster hosten möchten. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Rolleneigenschaften und wählen Sie **Eigenschaften**.

![RoleCache1][RoleCache1]

Gehen Sie zur Registerkarte **Caching**, markieren Sie das Kästchen **Caching aktivieren**, und geben Sie die gewünschten Cachingoptionen an. Wenn das Caching in einer **Workerrolle** oder einer **ASP.NET- Webrolle** aktiviert ist, lautet die Standardeinstellung wie folgt: **Zusammengestelltes Rollencaching**, wobei 30 % des Speichers für die Rolleninstanzen dem Caching zugewiesen sind. Es wird automatisch ein Standardcache konfiguriert. Wenn gewünscht können weitere Caches mit anderen Namen erstellt werden, die ebenfalls den zugewiesenen Speicher teilen.

![RoleCache2][RoleCache2]

Fügen Sie eine **Cache-Workerrolle** zu Ihrem Projekt hinzu, um einen **Dedizierten Rollencachecluster** zu konfigurieren.

![RoleCache7][RoleCache7]

Wenn eine **Cache-Workerrolle** zu einem Projekt hinzugefügt wird, lautet die Standardkonfiguration **Dediziertes Rollencaching**.

![RoleCache8][RoleCache8]

Sobald das Caching aktiviert ist, kann das Cachecluster-Speicherkonto konfiguriert werden. In-Role Cache erfordert ein Azure-Speicherkonto. Das Speicherkonto wird verwendet, um Konfigurationsdaten für den Cachecluster aufzubewahren, auf den von allen virtuellen Maschinen zugegriffen wird, die dem Cachecluster zugewiesen sind. Das Speicherkonto wird auf der Cachecluster-Eigenschaftenseite auf der Registerkarte **Caching** direkt oberhalb der **Benannten Cacheeinstellungen** festgelegt.

![RoleCache10][RoleCache10]

>Wird das Speicherkonto nicht konfiguriert, kann die Rolle nicht gestartet werden.

Die Größe des Cache ergibt sich aus einer Kombination der VM-Größe der Rolle, der Anzahl der Instanzen der Rolle und der Art des Clusters, d. h. sie hängt davon ab, ob der Cachecluster als dedizierter Rollen- oder als zusammengestellter Rollencachecluster konfiguriert wird.

>In diesem Abschnitt finden Sie eine vereinfachte Übersicht über die Konfiguration der Cachegröße. Weitere Informationen zur Cachegröße und zur Kapazitätenplanung erhalten Sie unter [In-Role Cache – Erwägungen bezüglich der Kapazitätsplanung][].

Klicken Sie zum Konfigurieren der Größe der virtuellen Maschine und der Anzahl der Instanzen mit der rechten Maustaste im **Projektmappen-Explorer** auf "Rolleneigenschaften" und wählen Sie **Eigenschaften**.

![RoleCache1][RoleCache1]

Gehen Sie zur Registerkarte **Konfiguration**. Die standardmäßig festgelegte **Anzahl der Instanzen** ist 1 und die **Größe der VM** ist standardmäßig als **Klein** festgelegt.

![RoleCache3][RoleCache3]

Gesamter verfügbarer Speicher für die verschiedenen VM-Größen:

-	**Klein**: 1,75 GB
-	**Mittel**: 3,5 GB
-	**Groß**: 7 GB
-	**Sehr groß**: 14 GB


> Die Speichergrößen beziehen sich auf den gesamten Speicherplatz, der der VM zur Verfügung steht und für das Betriebssystem, Cacheprozesse, Cachedaten und Anwendungen geteilt wird. Weitere Informationen zur Konfiguration der Größe virtueller Maschinen finden Sie unter [Konfiguration der Größe einer virtuellen Maschine][]. Beachten Sie, dass **besonders kleine** virtuelle Maschinen das Caching nicht unterstützen.

Wird **Zusammengestelltes Rollencaching** festgelegt, wird die Cachegröße gemäß des prozentualen Anteils des Speichers der virtuellen Maschine festgelegt. Wird **Dediziertes Rollencaching** festgelegt, wird der gesamte Speicher, der der virtuellen Maschine zur Verfügung steht, für das Caching verwendet. Wenn zwei Rolleninstanzen konfiguriert werden, wird der gemeinsame Speicher der virtuellen Maschinen verwendet. Dadurch entsteht ein Cachecluster, bei dem der verfügbare Cachingspeicher auf mehrere Rolleninstanzen verteilt wird, der den Cacheclients jedoch als einzelne Ressource zur Verfügung steht. Die Konfiguration zusätzlicher Rolleninstanzen erhöht die Cachegröße auf die gleiche Art und Weise. Die Einstellungen, die nötig sind, um diese Cachegröße bereitzustellen, finden Sie in der Tabelle für die Kapazitätsplanung unter [In-Role Cache – Erwägungen bezüglich der Kapazitätsplanung][].

Sobald der Cachecluster konfiguriert wurde, können Sie die Cacheclients konfigurieren, um Zugriff auf den Cache zu erlauben.

<a name="NuGet"></a>
## Konfigurieren der Cacheclients

Um auf einen In-Role Cache zugreifen zu können, müssen die Clients in der gleichen Bereitstellung konfiguriert werden. Handelt es sich bei dem Cachecluster um einen dedizierten Rollencachecluster, sind die Clients in der Bereitstellung andere Rollen. Handelt es sich bei dem Cachecluster um einen zusammengestellten Rollencachecluster, können die Clients entweder die anderen Rollen in der Bereitstellung oder die Rollen selbst sein, die den Cachecluster hosten. Es wird ein NuGet-Paket bereitgestellt, das für die Konfiguration der einzelnen Clientrollen, die auf den Cache zugreifen, verwendet werden kann. Klicken Sie zum Konfigurieren einer Rolle für den Zugriff auf einen Cachecluster mithilfe des Caching NuGet-Pakets mit der rechten Maustaste im**Projektmappen-Explorer** auf das Rollenprojekt, und wählen Sie **NuGet-Pakete verwalten** aus.

![RoleCache4][RoleCache4]

Wählen Sie **In-Role Cache**, klicken Sie auf **Installieren** und anschließend auf **Akzeptieren**.

>Wird der **In-Role Cache** nicht in der Liste angezeigt, geben Sie **WindowsAzure.Caching** in das Feld **Onlinesuche** ein, und wählen Sie ihn aus den Ergebnissen aus.

![RoleCache5][RoleCache5]

Funktionen des NuGet-Pakets: Es fügt die erforderliche Konfiguration zur Konfigurationsdatei der Rolle hinzu, es fügt eine Cacheclienteinstellung für die Diagnoseebene zur Datei "ServiceConfiguration.cscfg" der Azure-Anwendung hinzu, und es fügt die erforderlichen Assemblyverweise hinzu.

>Für ASP.NET-Webrollen fügt das Caching NuGet-Pakets auch zwei auskommentierte Abschnitte zu "web.config" hinzu. Der erste Abschnitt ermöglicht das Speichern des Sitzungszustands im Cache und der zweite Abschnitt ermöglicht das Zwischenspeichern der ASP.NET-Seitenausgabe. Weitere Informationen finden Sie unter [Vorgehensweise: Speichern des ASP.NET-Sitzungszustands im Cache] und [Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache][].

Das NuGet-Paket fügt die folgenden Konfigurationselemente zur Datei "web.config" oder "app.config" der Rolle hinzu. Die Abschnitte **dataCacheClients** und **cacheDiagnostics** werden unter dem **configSections**-Element hinzugefügt. Wenn kein **configSections**-Element vorhanden ist, wird es als untergeordnetes Element des **configuration**-Elements erstellt.

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    
      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

Diese neuen Abschnitte umfassen Referenzen auf ein **dataCacheClients** Element und ein **cacheDiagnostics** Element. Diese Elemente werden außerdem zum Element **Konfiguration** hinzugefügt.

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

Ersetzen Sie nach Hinzufügen der Konfiguration **[Cachecluster-Rollenname]** mit dem Namen der Rolle, die den Cachecluster hostet.

>Wird **[Cachecluster-Rollenname]** nicht mit dem Namen der Rolle, die den Cachecluster hostet, ersetzt, wird eine **TargetInvocationException** ausgelöst, wenn auf den Cache mit **DatacacheException** zugegriffen wird. Es wird folgende Meldung angezeigt: "Es ist keine derartige Rolle vorhanden".

Das NuGet-Paket fügt auch eine **ClientDiagnosticLevel**-Einstellung zu **ConfigurationSettings** der Cacheclientrolle in "ServiceConfiguration.cscfg" hinzu. Das folgende Beispiel ist der Abschnitt **WebRole1** einer Datei "ServiceConfiguration.cscfg" mit einem **ClientDiagnosticLevel** von 1, was der Standardeinstellung für **ClientDiagnosticLevel** entspricht.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>Der In-Role Cache bietet sowohl einen Cacheserver als auch eine Cacheclient-Diagnosestufe. Die Diagnosestufe ist eine einzelne Einstellung, die festlegt, welche Diagnosedaten für das Caching gesammelt werden. Weitere Informationen finden Sie unter [Problembehebung und Diagnose für In-Role Caches][]

Das NuGet-Paket fügt außerdem Referenzen zu den folgenden Assemblys hinzu:

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Handelt es sich bei Ihrer Rolle um eine ASP.NET-Webrolle, wird auch die folgende Assembly hinzugefügt:

-	Microsoft.Web.DistributedCache.dll.

Nachdem die Zwischenspeicherung im Clientprojekt konfiguriert wurde, können Sie die in den folgenden Abschnitten beschriebenen Methoden verwenden, um mit dem Cache zu arbeiten.

<a name="working-with-caches"></a>
## Arbeiten mit Caches

Die folgenden Schritte in diesem Abschnitt beschreiben, wie allgemeine Aufgaben in Bezug auf das Caching durchgeführt werden.

-	[Vorgehensweise: Erstellen eines DataCache-Objekts][]
-   [Vorgehensweise: Hinzufügen zu und Abrufen eines Objekts aus dem Cache][]
-   [Vorgehensweise: Angeben des Ablaufs eines Objekts im Cache][]
-   [Vorgehensweise: Speichern des ASP.NET-Sitzungszustands im Cache][]
-   [Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache][]

<a name="create-cache-object"></a>
## Vorgehensweise: Erstellen eines DataCache-Objekts

Um programmseitig mit dem Cache arbeiten zu können, benötigen Sie eine Referenz auf den Cache. Fügen Sie folgende Zeile am Anfang jeder Datei ein, von der aus Sie den In-Role Cache verwenden möchten:

    using Microsoft.ApplicationServer.Caching;

>Sollte Visual Studio die Typen in der Using-Anweisung selbst nach der Installation des Caching NuGet-Pakets, das die nötigen Referenzen hinzufügt, nicht erkennen, stellen Sie sicher, dass das Zielprofil für das Projekt .NET Framework 4.0 oder höher ist, und wählen Sie eines der Profile, das auf kein **Client Profile** verweist. Anweisungen zum Konfigurieren von Cacheclients finden Sie unter [Konfigurieren der Cacheclients][].

Es gibt zwei Möglichkeiten, um ein **DataCache**-Objekt zu erstellen. Sie können entweder einfach einen **DataCache** erstellen und den Namen des gewünschten Caches angeben.

    DataCache cache = new DataCache("default");

Sobald der **DataCache** instanziiert ist, können Sie ihn verwenden, um mit dem Cache wie in den nachfolgenden Abschnitten beschrieben, zu interagieren.

Die zweite Möglichkeit besteht darin, mithilfe des Standardkonstruktors ein neues **DataCacheFactory**-Objekt in der Anwendung zu erstellen. Dadurch verwendet der Cacheclient die Einstellungen in der Konfigurationsdatei. Rufen Sie entweder die **GetDefaultCache**-Methode der neuen **DataCacheFactory**-Instanz auf, damit ein **DataCache**-Objekt zurückgegeben wird, oder die **GetCache**-Methode, und übergeben Sie den Namen Ihres Caches. Bei diesen Methoden wird ein **DataCache**-Objekt zurückgegeben, das Sie anschließend verwenden können, um programmseitig auf den Cache zuzugreifen.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.	

<a name="add-object"></a>
## Vorgehensweise: Hinzufügen zu und Abrufen eines Objekts aus dem Cache

Mit der **Add**- oder **Put**-Methode können Sie ein Element zum Cache hinzufügen. Durch die **Add**-Methode wird das angegebene Objekt zum Cache hinzugefügt, der Schlüssel ist der Wert des Schlüsselparameters.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Wenn sich bereits ein Objekt mit demselben Schlüssel im Cache befindet, wird eine **DataCacheException** mit der folgenden Meldung ausgegeben:

> ErrorCode:SubStatus: Es wird versucht, ein Objekt zu erstellen, dessen Schlüssel bereits im Cache vorhanden ist. Der Cache akzeptiert nur eindeutige Schlüsselwerte für Objekte.

Zum Abrufen eines Objekts mit einem bestimmten Schlüssel kann die **Get**-Methode verwendet werden. Wenn das Objekt vorhanden ist, wird es zurückgegeben, falls nicht, wird Null zurückgegeben.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

Mit der **Put**-Methode wird das Objekt mit dem angegebenen Schlüssel dem Cache hinzugefügt, falls es nicht vorhanden ist, bzw. ersetzt, falls es vorhanden ist.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## Vorgehensweise: Angeben des Ablaufs eines Objekts im Cache

Standardmäßig laufen Elemente im Cache 10 Minuten nach der Ablage im Cache ab. Die Zeit kann in den Rolleneigenschaften der Rolle, die den Cachecluster hostet, unter **Lebensdauer (min)** konfiguriert werden.

![RoleCache6][RoleCache6]

Es gibt drei verschiedene **Ablauftypen**: **Keine**, **Absolut** und **Gleitendes Fenster**. Diese Typen bestimmen, wie **Lebensdauer (min)** eingesetzt wird, um das Ablaufdatum festzulegen. Der **Standardablauftyp** lautet **Absolut** und bedeutet, dass der Countdown für den Ablauf eines Elements beginnt, sobald dieses im Cache abgelegt wird. Sobald die für ein Element angegebene Zeitdauer verstrichen ist, läuft es ab. Wird **Gleitendes Fenster** angegeben, wird die Lebensdauer für ein Element jedes Mal zurückgesetzt, wenn im Cache auf das Element zugegriffen wird. Das Element läuft nicht ab, solange die angegebene Zeit nach dem letzten Zugriff nicht abläuft. Wird **Keine** angegeben, muss die **Lebensdauer (min)** auf **0** gesetzt werden. Das Element läuft in diesem Fall nicht ab und bleibt gültig, solange es sich im Cache befindet.

Wird eine längere oder kürzere Lebensdauer als die in den Rolleneigenschaften angegebene gewünscht, kann eine spezifische Lebensdauer angegeben werden, wenn ein Element im Cache hinzugefügt oder aktualisiert wird. Verwenden Sie dafür die Überladung von **Add** und **Put**, die einen **TimeSpan**-Parameter annehmen. Im folgenden Beispiel wird die Zeichenfolge **value** mit dem Schlüssel **item** und einer Zeitüberschreitungseinstellung von 30 Minuten dem Cache hinzugefügt.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Um das verbleibende Zeitüberschreitungsintervall eines Elements im Cache anzuzeigen, kann mit der **GetCacheItem**-Methode ein **DataCacheItem**-Objekt abgerufen werden, das Informationen zu dem Element im Cache (einschließlich dem verbleibenden Zeitüberschreitungsintervall) enthält.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## Vorgehensweise: Speichern des ASP.NET-Sitzungszustands im Cache

Der Session State Provider für In-Role Caches ist ein außerprozessmäßiger Speichermechanismus für ASP.NET-Anwendungen. Mit diesem Anbieter können Sie den Sitzungszustand in einem Azure-Cache speichern und müssen dies nicht im Arbeitsspeicher oder ein einer SQL Server-Datenbank tun. Konfigurieren Sie zuerst Ihren Cachecluster, um den Caching Session State Provider zu nutzen. Konfigurieren Sie anschließend mithilfe des Caching NuGet-Pakets wie unter [Erste Schritte mit dem In-Role Cache][] beschrieben Ihre ASP.NET-Anwendung. Sobald das Caching NuGet-Paket installiert ist, fügt es einen auskommentierten Abschnitt in web.config hinzu, der die nötige Konfiguration für die ASP.NET-Anwendung beinhaltet, um den Session State Provider für den In-Role Cache zu verwenden.

    <!--Uncomment this section to use In-Role Cache for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

>Sollte web.config diesen auskommentierten Abschnitt nach der Installation des Caching NuGet-Pakets nicht enthalten, stellen Sie sicher, dass Sie den aktuellen NuGet-Paketmanager über [NuGet-Paketmanager Installation][] installiert haben. Deinstallieren und reinstallieren Sie anschließend das Paket.

Entfernen Sie Kommentare aus dem angegebenen Abschnitt, um den Session State Provider für In-Role Cache zu aktivieren. Der Standardcache wird im bereitgestellten Codeausschnitt angegeben. Geben Sie den gewünschten Cache im **cacheName**-Attribut an, wenn ein anderer Cache verwendet werden soll.

Weitere Informationen zur Verwendung des Caching-Services Session State Provider finden Sie unter [Session State Provider für In-Role Cache][].

<a name="store-page"></a>
## Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache

Der Output Cache Provider für In-Role Caches ist ein außerprozessmäßiger Speichermechanismus für Ausgabecachedaten. Diese Daten sind für vollständige HTTP-Antworten bestimmt (Zwischenspeichern von Seitenausgaben). Der Anbieter wird zum neuen Erweiterungspunkt des Ausgabecacheanbieters hinzugefügt, der in ASP.NET 4 eingeführt wurde. Konfigurieren Sie zuerst Ihren Cachecluster, um den Ausgabecacheanbieter zu nutzen. Konfigurieren Sie anschließend Ihre ASP.NET-Anwendung mithilfe des Caching NuGet-Pakets, wie unter [Erste Schritte mit dem In-Role Cache][] beschrieben. Sobald das Caching NuGet-Paket installiert ist, fügt es einen auskommentierten Abschnitt in web.config hinzu, der die nötige Konfiguration für die ASP.NET-Anwendung beinhaltet, um den Output Cache Provider für den In-Role Cache zu verwenden.

    <!--Uncomment this section to use In-Role Cache for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

>Sollte web.config diesen auskommentierten Abschnitt nach der Installation des Caching NuGet-Pakets nicht enthalten, stellen Sie sicher, dass Sie den aktuellen NuGet-Paketmanager über [NuGet-Paketmanager Installation][] installiert haben. Deinstallieren und reinstallieren Sie anschließend das Paket.

Entfernen Sie Kommentare aus dem angegebenen Abschnitt, um den Output Cache Provider für In-Role Cache zu aktivieren. Der Standardcache wird im bereitgestellten Codeausschnitt angegeben. Geben Sie den gewünschten Cache im **cacheName**-Attribut an, wenn ein anderer Cache verwendet werden soll.

Fügen Sie jeder Seite, für die Sie die Ausgabe zwischenspeichern möchten, eine **OutputCache**-Direktive hinzu.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

In diesem Beispiel verbleiben die zwischengespeicherten Daten 60 Sekunden lang im Cache. Für jede Parameterkombination wird eine andere Version der Seite zwischengespeichert. Weitere Informationen zu den verfügbaren Optionen finden Sie unter [OutputCache-Direktive][].

Weitere Informationen zur Verwendung des Output Cache Providers für In-Role Cache finden Sie unter [Output Cache Provider für In-Role Cache][].

<a name="next-steps"></a>
## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von In-Role Cache vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Cachingaufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [In-Role Cache][]
-   Erfahren Sie, wie Sie die Migration zum In-Role Cache durchführen: [Migrieren zum In-Role Cache][].
-   Sehen Sie sich die Beispiele an: [In-Role Cache-Beispiele][].
-	Sehen Sie sich [Maximale Leistung: Beschleunigen von Clouddienstanwendungen mit Azure-Caching][] von TechEd 2013 zum In-Role Caching an.

<!-- INTRA-TOPIC LINKS -->
[Nächste Schritte]: #next-steps
[What is In-Role Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Getting Started with the In-Role Cache Service]: #getting-started-cache-service
[Prepare Your Visual Studio Project to Use In-Role Cache]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Erste Schritte mit dem In-Role Cache]: #getting-started-cache-role-instance
[Konfigurieren eines Cacheclusters]: #enable-caching
[Configure the desired cache size]: #cache-size
[Konfigurieren der Cacheclients]: #NuGet
[Working with Caches]: #working-with-caches
[Vorgehensweise: Erstellen eines DataCache-Objekts]: #create-cache-object
[Vorgehensweise: Hinzufügen zu und Abrufen eines Objekts aus dem Cache]: #add-object
[Vorgehensweise: Angeben des Ablaufs eines Objekts im Cache]: #specify-expiration
[Vorgehensweise: Speichern des ASP.NET-Sitzungszustands im Cache]: #store-session
[Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net
 
<!-- IMAGES --> 
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png
  
<!-- LINKS -->
[Konfiguration der Größe einer virtuellen Maschine]: http://go.microsoft.com/fwlink/?LinkId=164387
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[In-Role Cache – Erwägungen bezüglich der Kapazitätsplanung]: http://go.microsoft.com/fwlink/?LinkId=252651
[In-Role Cache-Beispiele]: http://msdn.microsoft.com/library/jj189876.aspx
[In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=252658
[In-Role Cache]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Maximale Leistung: Beschleunigen von Clouddienstanwendungen mit Azure-Caching]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[Migrieren zum In-Role Cache]: http://msdn.microsoft.com/library/hh914163.aspx
[NuGet-Paketmanager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Output Cache Provider für In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx
[OutputCache-Direktive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Überblick In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=254172
[Session State Provider für In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Problembehebung und Diagnose für In-Role Caches]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me
 

<!---HONumber=AcomDC_0309_2016-->