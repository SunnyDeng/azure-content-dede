<properties 
	pageTitle="Verwenden von Azure Managed Cache Service" 
	description="Erfahren Sie, wie zur Verbesserung der Leistung Ihrer Azure-Anwendungen mit Azure Managed Cache Service beitragen." 
	services="cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="sdanie"/>

# Verwenden von Azure Managed Cache Service

Dieser Leitfaden beschreibt die ersten Schritte mit **Azure Managed Cache Service**. Die Beispiele sind in C#-Code geschrieben und verwenden die .NET API. Die behandelten Szenarien umfassen das **Erstellen und Konfigurieren eines Caches**, **Konfigurieren von Cacheclients**, **Hinzufügen und Entfernen von Objekten aus dem Cache, Speichern des ASP.NET-Sitzungsstatus im Cache** und **Aktivieren der Zwischenspeicherung von ASP.NET-Seitenausgaben mit dem Cache**. Weitere Informationen zum Verwenden von Azure Cache finden Sie im Abschnitt [Nächste Schritte][].

>Hilfestellung bei der Auswahl des für Ihre Anwendung geeigneten Azure Cache-Angebots finden Sie unter [Welches Azure Cache-Angebot ist für mich das richtige?][].

<a name="what-is"></a>
## Was ist Azure Managed Cache Service?

Azure Managed Cache Service ist eine verteilte, arbeitsspeicherinterne, skalierbare Lösung, mit der Sie hoch skalierbare und reaktionsschnelle Anwendungen erstellen können, indem Sie äußerst schnellen Zugriff auf Daten bieten.

Azure Managed Cache Service enthält die folgenden Features:

-   Vorbereitete ASP.NET-Anbieter für Sitzungszustand und Seitenausgabecaching für schnellere Webanwendungen, ohne Änderung des Anwendungscodes
-   Zwischenspeichern beliebiger serialisierbarer verwalteter Objekte, z. B. CLR-Objekte, Zeilen, XML und Binärdaten
-   Konsistentes Entwicklungsmodell für Azure und Windows Server AppFabric

Mit Managed Cache Service erhalten Sie Zugriff auf einen sicheren, dedizierten Cache, der von Microsoft verwaltet wird. Auf einen mit Managed Cache Service erstellten Cache kann auch von Anwendungen in Azure zugegriffen werden, die auf Azure-Websites, Web- und Workerrollen und Virtual Machines ausgeführt werden.

Managed Cache Service ist in drei Stufen verfügbar:

-	Basic: Cache mit einer Größe von 128 MB bis 1 GB
-	Standard: Cache mit einer Größe von 1 GB bis 10 GB
-	Premium: Cache mit einer Größe von 5 GB bis 150 GB

Diese Ausführungen unterscheiden sich hinsichtlich der Features und des Preises. Die Features werden weiter unten in diesem Leitfaden behandelt, weitere Informationen zu den Preisen finden Sie unter [Cache – Preisdetails][].

Dieser Leitfaden enthält eine Übersicht über die ersten Schritte mit Managed Cache Service. Detaillierte Informationen zu diesen Features, die über den Rahmen dieses Leitfadens hinausgehen, finden Sie unter [Azure Managed Cache Service][].

<a name="getting-started-cache-service"></a>
## Erste Schritte mit Azure Managed Cache Service

Die ersten Schritte mit Managed Cache Service sind unkompliziert. Zunächst stellen Sie einen Cache bereit und konfigurieren ihn. Als Nächstes konfigurieren Sie die Cacheclients, sodass von diesen auf den Cache zugegriffen werden kann. Nach der Konfiguration der Cacheclients können Sie mit der Verwendung beginnen.

-	[Erstellen des Caches][]
-	[Konfigurieren des Caches][]
-	[Konfigurieren der Cacheclients][]

<a name="create-cache"></a>
## Erstellen eines Caches

Cacheinstanzen werden in Managed Cache Service mit PowerShell-Cmdlets erstellt.

>Nach der Erstellung einer Managed Cache Service-Instanz mit PowerShell-Cmdlets können Sie die Instanz im [Azure-Verwaltungsportal][] anzeigen und konfigurieren.

Öffnen Sie zum Erstellen einer Managed Cache Service-Instanz ein Azure PowerShell-Befehlsfenster.

>Weitere Informationen zur Installation und Verwendung von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][].

Rufen Sie das Cmdlet [Add-AzureAccount][] auf, und geben Sie die Ihrem Konto zugeordnete E-Mail-Adresse und Ihr Kennwort ein. Ein Abonnement wird automatisch ausgewählt und nach dem Aufruf des Cmdlets [Add-AzureAccount][] angezeigt. Um das Abonnement zu ändern, rufen Sie das Cmdlet [Select-AzureSubscription][] auf.

>Falls Sie Azure PowerShell mit einem Zertifikat für Ihr Konto verknüpft haben, können Sie diesen Schritt überspringen. Weitere Informationen zum Verbinden von Azure PowerShell mit Ihrem Azure-Konto finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][].

Ein Abonnement wird standardmäßig ausgewählt und angezeigt. Um das Abonnement zu ändern, rufen Sie das Cmdlet [Select-AzureSubscription][] auf.

Rufen Sie das Cmdlet [New-AzureManagedCache][] auf, und geben Sie den Namen, die Region, das Cacheangebot und die Größe des Caches an.

Geben Sie unter **Name** einen Unterdomänennamen für den Cacheendpunkt ein. Der Endpunktname muss eine Zeichenfolge zwischen sechs und zwanzig Zeichen sein. Er darf nur Kleinbuchstaben und Ziffern enthalten und muss mit einem Buchstaben beginnen.

Geben Sie unter **Location** eine Region für den Cache an. Die beste Leistung wird erzielt, wenn der Cache in der Region erstellt wird, in der sich auch die Cacheclientanwendung befindet.

Die Optionen **Sku** und **Memory** werden gemeinsam verwendet, um die Größe des Caches zu bestimmen. Managed Cache Service ist in den folgenden drei Ausführungen verfügbar.

-	Basic: Cache mit einer Größe von 128 MB bis 1 GB, der in 128-MB-Schritten vergrößert werden kann, mit einem benannten Standardcache
-	Standard: Cache mit einer Größe von 1 GB bis 10 GB, der in 1-GB-Schritten vergrößert werden kann und Benachrichtigungen sowie bis zu zehn benannte Caches unterstützt
-	Premium: Cache mit einer Größe von 5 GB bis 150 GB, der in 5-GB-Schritten vergrößert werden kann und Benachrichtigungen, hohe Verfügbarkeit sowie bis zu zehn benannte Caches unterstützt

Wählen Sie für **Sku** und **Memory** die Werte aus, die den Anforderungen Ihrer Anwendung entsprechen. Beachten Sie, dass einige Cachefeatures (z. B. Benachrichtigungen und hohe Verfügbarkeit) nur bei bestimmten Cacheangeboten verfügbar sind. Weitere Informationen zum Auswählen des für Ihre Anwendung optimal geeigneten Cacheangebots und der geeigneten Cachegröße finden Sie unter [Cacheangebote][].

 Im folgenden Beispiel wird ein Cache mit 128 MB des Typs "Basic" mit dem Namen "contosocache" in der geografischen Region "South Central US" erstellt.

	New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

>Eine vollständige Liste der Parameter und Werte für die Erstellung von Caches finden Sie in der Dokumentation zum Cmdlet [New-AzureManagedCache][].

Nach dem Aufrufen des PowerShell-Cmdlets dauert das Erstellen des Caches ggf. einige Minuten. Nachdem der Cache erstellt wurde, weist der neue Cache den Status `Running` auf, ist mit den Standardeinstellungen zur Verwendung bereit und kann im [Azure-Verwaltungsportal][] angezeigt und konfiguriert werden. Informationen zum Anpassen der Konfiguration des Caches finden Sie im folgenden Abschnitt, [Konfigurieren des Caches][].

Sie können den Erstellungsstatus im Azure PowerShell-Fenster überwachen. Sobald der Cache einsatzbereit ist, zeigt das Cmdlet [New-AzureManagedCache][] die Cacheinformationen wie im folgenden Beispiel gezeigt an.

	PS C:\> Add-AzureAccount
	VERBOSE: Account "user@domain.com" has been added.
	VERBOSE: Subscription "MySubscription" is selected as the default subscription.
	VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
	VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
	PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
	VERBOSE: Intializing parameters...
	VERBOSE: Creating prerequisites...
	VERBOSE: Verify cache service name...
	VERBOSE: Creating cache service...
	VERBOSE: Waiting for cache service to be in ready state...


	Name     : contosocache
	Location : South Central US
	State    : Active
	Sku      : Basic
	Memory   : 128MB



	PS C:\>




<a name="enable-caching"></a>
## Konfigurieren des Caches

Sie können die Optionen des Caches im Verwaltungsportal auf der Registerkarte **Konfigurieren** für Cache konfigurieren. Jeder Cache verfügt über einen benannten **Standardcache**. Die Standard- und Premium-Cacheangebote unterstützen darüber hinaus neun weitere benannte Caches, insgesamt also zehn. Jeder benannte Cache verfügt über eigene Optionen, über die Sie den Cache sehr flexibel konfigurieren können.

![NamedCaches][NamedCaches]

Geben Sie zum Erstellen eines benannten Caches den Namen des neuen Caches in das Feld **Name** ein, geben Sie die gewünschten Optionen an, und klicken Sie auf **Speichern** und dann zur Bestätigung auf **Ja**. Klicken Sie auf **Verwerfen**, um Änderungen zu verwerfen.

## Ablaufrichtlinie und Zeit (Minuten) ##

Mit der Option **Expiry Policy** und der Einstellung **Time (min)** wird festgelegt, wann zwischengespeicherte Elemente ablaufen. Es gibt drei Arten von Ablaufrichtlinien: **Absolut**, **Gleitend** und **Nie**.

Wenn **Absolut** angegeben ist, beginnt das unter **Time (min)** angegebene Ablaufintervall, sobald ein Element zum Cache hinzugefügt wird. Nachdem das unter **Time (min)** angegebene Intervall verstrichen ist, läuft das Element ab.

Wenn **Gleitend** angegeben ist, wird das unter **Time (min)** angegebene Ablaufintervall zurückgesetzt, sobald auf das Element im Cache zugegriffen wird. Das Element läuft erst dann ab, wenn das unter **Time (min)** angegebene Intervall nach dem letzten Zugriff auf das Element verstreicht.

Wenn **Never** angegeben ist, muss für **Time (min)** der Wert **0** festgelegt werden, und Elemente laufen nicht ab.

Die Standardablaufrichtlinie ist **Absolut**, und die Standardeinstellung für **Time (min)** beträgt 10 Minuten. Die Ablaufrichtlinie gilt fest für jedes Element in einem benannten Cache, **Time (min)** kann jedoch mithilfe der Überladungen **Add** und **Put**, die einen Zeitüberschreitungsparameter übernehmen, für jedes Element angepasst werden.

Weitere Informationen zu Entfernungs- und Ablaufrichtlinien finden Sie unter [Ablauf und Entfernung][].

## Benachrichtigungen ##

Cachebenachrichtigungen, über die Anwendungen asynchrone Benachrichtigungen empfangen können, wenn verschiedene Cachevorgänge im Cachecluster ausgeführt werden. Cachebenachrichtigungen ermöglichen auch die automatische Invalidierung von lokal zwischengespeicherten Objekten. Weitere Informationen finden Sie unter [Benachrichtigungen][].

>Benachrichtigungen sind nur im Standard- und Premium-Cacheangebot verfügbar, jedoch nicht im Basic-Cacheangebot. Weitere Informationen finden Sie unter [Cacheangebote][].

## Hohe Verfügbarkeit ##

Bei Aktivierung von hoher Verfügbarkeit wird von jedem Element, das zum Cache hinzugefügt wird, eine Sicherungskopie erstellt. Im Fall eines unerwarteten Fehlers bei der primären Kopie des Elements ist immer noch die Sicherungskopie verfügbar.

Definitionsgemäß verdoppelt sich der erforderliche Arbeitsspeicher für jedes Element bei Verwendung der hohen Verfügbarkeit. Berücksichtigen Sie diese Auswirkungen auf den Arbeitsspeicher bei der Kapazitätsplanung. Weitere Informationen finden Sie unter [Hohe Verfügbarkeit][].

>Hohe Verfügbarkeit ist nur im Premium-Cacheangebot verfügbar, nicht im Basic- oder Standard-Cacheangebot. Weitere Informationen finden Sie unter [Cacheangebote][].

## Entfernung ##

Um die innerhalb eines Caches verfügbare Arbeitsspeicherkapazität aufrechtzuerhalten, wird die Entfernung selten verwendeter Objekte unterstützt. Wenn die Arbeitsspeicherbelegung den Schwellenwert für den Arbeitsspeicher überschreitet, werden Objekte aus dem Arbeitsspeicher entfernt, bis die Speicherauslastung sich normalisiert. Dies geschieht unabhängig davon, ob die Objekte tatsächlich abgelaufen sind. Die Entfernung ist standardmäßig aktiviert. Wenn die Entfernung deaktiviert ist, werden keine Elemente aus dem Cache entfernt, sobald die Kapazitätsgrenze erreicht ist. Stattdessen sind Put- und Add-Operationen nicht erfolgreich.

Weitere Informationen zu Entfernungs- und Ablaufrichtlinien finden Sie unter [Ablauf und Entfernung][].

Nach der Konfiguration des Caches können Sie die Cacheclients konfigurieren, um Zugriff auf den Cache zu ermöglichen.

<a name="NuGet"></a>
## Konfigurieren der Cacheclients

Auf einen mit Managed Cache Service erstellten Cache kann auch von Azure-Anwendungen zugegriffen werden, die auf Azure-Websites, Web- und Workerrollen und Virtual Machines ausgeführt werden. Es wird ein NuGet-Paket bereitgestellt, das die Konfiguration von Cacheclientanwendungen vereinfacht.

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und wählen Sie **Manage NuGet Packages** aus, um eine Clientanwendung mithilfe des Cache NuGet-Pakets zu konfigurieren.

![NuGetPackageMenu][NuGetPackageMenu]

Geben Sie **WindowsAzure.Caching** in das Textfeld **Onlinesuche** ein, und wählen Sie in den Ergebnissen **Microsoft** **Azure** **Cache** aus. Klicken Sie auf **Installieren** und dann auf **Akzeptieren**.

![NuGetPackage][NuGetPackage]

Funktionen des NuGet-Pakets: Die erforderliche Konfiguration wird in die Konfigurationsdatei der Anwendung eingefügt, und die erforderlichen Assemblyverweise werden hinzugefügt. Bei Cloud Services-Projekten wird außerdem eine Einstellung für die Cacheclient-Diagnoseebene zur Datei "ServiceConfiguration.cscfg" des Clouddiensts hinzugefügt.

>Für ASP.NET-Webprojekte fügt das Cache NuGet-Paket auch zwei auskommentierte Abschnitte zu "web.config" hinzu. Der erste Abschnitt ermöglicht das Speichern des Sitzungszustands im Cache und der zweite Abschnitt ermöglicht das Zwischenspeichern der ASP.NET-Seitenausgabe. Weitere Informationen finden Sie unter [Vorgehensweise: Speichern des ASP.NET-Sitzungszustands im Cache] und [Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache][].

Das NuGet-Paket fügt die folgenden Konfigurationselemente zur Datei "web.config" oder "app.config" der Anwendung hinzu. Die Abschnitte **dataCacheClients** und **cacheDiagnostics** werden unter dem **configSections**-Element hinzugefügt. Wenn kein **configSections**-Element vorhanden ist, wird es als untergeordnetes Element des **configuration**-Elements erstellt.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />
  
    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />


Diese neuen Abschnitte enthalten Verweise auf ein **dataCacheClients**-Element, das ebenfalls dem **configuration**-Element hinzugefügt wird.

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>


Ersetzen Sie die folgenden zwei Elemente, nachdem die Konfiguration hinzugefügt wurde.

1. Ersetzen Sie **[Cacherollenname oder Dienstendpunkt]** durch den Endpunkt, der im Dashboard des Verwaltungsportals angezeigt wird.

	![Endpunkt][Endpoint]

2. Heben Sie die Auskommentierung des securityProperties-Abschnitts auf, und ersetzen Sie **[Authentifizierungsschlüssel]** durch den Authentifizierungsschlüssel. Sie finden diesen im Verwaltungsportal, wenn Sie im Cachedashboard auf **Manage Keys** klicken.

	![AccessKeys][AccessKeys]

>Diese Einstellungen müssen ordnungsgemäß konfiguriert werden, ansonsten kann von den Clients nicht auf den Cache zugegriffen werden.

Für Clouddienstprojekte fügt das NuGet-Paket auch eine **ClientDiagnosticLevel**-Einstellung zu **ConfigurationSettings** der Cacheclientrolle in "ServiceConfiguration.cscfg" hinzu. Das folgende Beispiel ist der Abschnitt **WebRole1** einer Datei "ServiceConfiguration.cscfg" mit einem **ClientDiagnosticLevel** von 1, was der Standardeinstellung für **ClientDiagnosticLevel** entspricht.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>Mit der Clientdiagnoseebene wird die Ebene für das Zwischenspeichern von Diagnoseinformationen, die für Cacheclients erfasst werden, konfiguriert. Weitere Informationen finden Sie unter [Problembehandlung und Diagnose][].

Das NuGet-Paket fügt außerdem Referenzen zu den folgenden Assemblys hinzu:

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Wenn es sich bei dem Projekt um ein Webprojekt handelt, wird darüber hinaus der folgende Assemblyverweis hinzugefügt:

-	Microsoft.Web.DistributedCache.dll.

Nachdem die Zwischenspeicherung im Clientprojekt konfiguriert wurde, können Sie die in den folgenden Abschnitten beschriebenen Methoden verwenden, um mit dem Cache zu arbeiten.

<a name="working-with-caches"></a>
## Arbeiten mit Caches

In den Schritten in diesem Abschnitt wird die Ausführung allgemeiner Aufgaben mit dem Cache beschrieben.

-	[Vorgehensweise: Erstellen eines DataCache-Objekts][]
-   [Vorgehensweise: Hinzufügen zu und Abrufen eines Objekts aus dem Cache][]
-   [Vorgehensweise: Angeben des Ablaufs eines Objekts im Cache][]
-   [Vorgehensweise: Speichern des ASP.NET-Sitzungszustands im Cache][]
-   [Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache][]

<a name="create-cache-object"></a>
## Vorgehensweise: Erstellen eines DataCache-Objekts

Um programmseitig mit dem Cache arbeiten zu können, benötigen Sie eine Referenz auf den Cache. Fügen Sie die folgende Zeile oben in jeder Datei hinzu, über die Sie Azure Cache verwenden möchten:

    using Microsoft.ApplicationServer.Caching;

>Wenn die Typen in der using-Anweisung nach der Installation des Cache NuGet-Pakets, mit dem die erforderlichen Verweise hinzugefügt werden, nicht von Visual Studio erkannt werden, stellen Sie sicher, dass das Zielprofil für das Projekt .NET Framework 4 oder höher ist, und wählen Sie eines der Profile aus, in dem nicht **Client Profile** angegeben ist. Anweisungen zum Konfigurieren von Cacheclients finden Sie unter [Konfigurieren der Cacheclients][].

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

Standardmäßig laufen Elemente im Cache 10 Minuten nach der Ablage im Cache ab. Dies kann in der Einstellung **Time (min)** auf der Registerkarte "Konfigurieren" für den Cache im Verwaltungsportal konfiguriert werden.

![NamedCaches][NamedCaches]

Es gibt drei Arten von **Ablaufrichtlinien**: **Nie**, **Absolut** und **Gleitend**. Damit wird konfiguriert, wie durch **Time (min)** der Ablauf bestimmt wird. Der **Standardablauftyp** lautet **Absolut** und bedeutet, dass der Countdown für den Ablauf eines Elements beginnt, sobald dieses im Cache abgelegt wird. Sobald die für ein Element angegebene Zeitdauer verstrichen ist, läuft es ab. Wenn **Gleitend** angegeben ist, wird der Ablaufcountdown für ein Element bei jedem Zugriff darauf im Cache zurückgesetzt, und das Element läuft erst ab, wenn die angegebene Zeitdauer seit dem letzten Zugriff verstrichen ist. Bei Angabe von **Never** muss für **Time (min)** der Wert **0** festgelegt werden. Die Elemente laufen nicht ab und bleiben für den gesamten Verbleib im Cache gültig.

Wenn ein längeres oder kürzeres Zeitüberschreitungsintervall anstelle der im Cache konfigurierten Einstellung gewünscht wird, kann eine bestimmte Dauer angegeben werden, wenn ein Element dem Cache hinzugefügt oder darin aktualisiert wird. Dazu wird die Überladung von **Add** und **Put** verwendet, die einen **TimeSpan**-Parameter übernimmt. Im folgenden Beispiel wird die Zeichenfolge **value** mit dem Schlüssel **item** und einer Zeitüberschreitungseinstellung von 30 Minuten dem Cache hinzugefügt.

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

Der Sitzungszustandsanbieter für Azure Cache ist ein prozessunabhängiger Speichermechanismus für ASP.NET-Anwendungen. Mit diesem Anbieter können Sie den Sitzungszustand in einem Azure-Cache speichern und müssen dies nicht im Arbeitsspeicher oder ein einer SQL Server-Datenbank tun. Um den Zustandsanbieter für Cachesitzungen zu verwenden, konfigurieren Sie zuerst den Cache, und konfigurieren Sie danach die ASP.NET-Anwendung für Cache mithilfe des Cache NuGet-Pakets, wie unter [Erste Schritte mit Managed Cache Service][] beschrieben. Wenn das Cache NuGet-Paket installiert ist, wird ein auskommentierter Abschnitt in "web.config" hinzugefügt, der die erforderliche Konfiguration für die ASP.NET-Anwendung enthält, um den Sitzungszustandsanbieter für Azure Cache verwenden zu können.

    <!--Uncomment this section to use Azure Caching for session state caching
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

>Wenn die Datei "web.config" diesen auskommentierten Abschnitt nach der Installation des Cache NuGet-Pakets nicht enthält, stellen Sie sicher, dass der aktuelle NuGet Package Manager (verfügbar unter [NuGet Package Manager Installation][]) installiert ist. Deinstallieren Sie das Paket dann und installieren Sie es neu.

Heben Sie die Auskommentierung des angegebenen Abschnitts auf, um den Sitzungsstatusanbieter für Azure Cache zu aktivieren. Der Standardcache wird im bereitgestellten Codeausschnitt angegeben. Geben Sie den gewünschten Cache im **cacheName**-Attribut an, wenn ein anderer Cache verwendet werden soll.

Weitere Informationen zum Verwenden des Sitzungszustandsanbieters von Managed Cache Service finden Sie unter [Sitzungszustandsanbieter für Azure Cache][].

<a name="store-page"></a>
## Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache

Der Ausgabecacheanbieter für Azure Cache ist ein prozessunabhängiger Speichermechanismus für Ausgabecachedaten. Diese Daten sind für vollständige HTTP-Antworten bestimmt (Zwischenspeichern von Seitenausgaben). Der Anbieter wird zum neuen Erweiterungspunkt des Ausgabecacheanbieters hinzugefügt, der in ASP.NET 4 eingeführt wurde. Um den Ausgabecacheanbieter zu nutzen, konfigurieren Sie zuerst Ihren Cachecluster, und konfigurieren Sie anschließend mithilfe des Cache NuGet-Pakets Ihre ASP.NET-Anwendung, wie unter [Erste Schritte mit Managed Cache Service][] beschrieben. Bei der Installation des Cache NuGet-Pakets wird der folgende auskommentierte Abschnitt in "web.config" hinzugefügt, der die erforderliche Konfiguration für die ASP.NET-Anwendung enthält, um den Ausgabecacheanbieter für Azure Cache verwenden zu können.

    <!--Uncomment this section to use Azure Caching for output caching
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

>Wenn die Datei "web.config" diesen auskommentierten Abschnitt nach der Installation des Cache NuGet-Pakets nicht enthält, stellen Sie sicher, dass der aktuelle NuGet Package Manager (verfügbar unter [NuGet Package Manager Installation][]) installiert ist. Deinstallieren Sie das Paket dann und installieren Sie es neu.

Heben Sie die Auskommentierung des angegebenen Abschnitts auf, um den Ausgabecacheanbieter für Azure Cache zu aktivieren. Der Standardcache wird im bereitgestellten Codeausschnitt angegeben. Geben Sie den gewünschten Cache im **cacheName**-Attribut an, wenn ein anderer Cache verwendet werden soll.

Fügen Sie jeder Seite, für die Sie die Ausgabe zwischenspeichern möchten, eine **OutputCache**-Direktive hinzu.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

In diesem Beispiel verbleiben die zwischengespeicherten Daten 60 Sekunden lang im Cache. Für jede Parameterkombination wird eine andere Version der Seite zwischengespeichert. Weitere Informationen zu den verfügbaren Optionen finden Sie unter [OutputCache-Direktive][].

Weitere Informationen zum Verwenden des Ausgabecacheanbieters für Azure Cache finden Sie unter [Seitenausgabecacheanbieter für Azure Cache][].

<a name="next-steps"></a>
## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Managed Cache Service vertraut gemacht haben, finden Sie unter den folgenden Links weitere Informationen zu komplexeren Cachingaufgaben.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Managed Cache Service][]
-	Informationen zum Migrieren zu Managed Cache Service: [Migrieren zum Managed Cache Service][]
-   Sehen Sie sich die Beispiele an: [Managed Cache Service – Beispiele][]

<!-- INTRA-TOPIC LINKS -->
[Nächste Schritte]: #next-steps
[What is Azure Managed Cache Service?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Erste Schritte mit Managed Cache Service]: #getting-started-cache-service
[Erstellen des Caches]: #create-cache
[Konfigurieren des Caches]: #enable-caching
[Konfigurieren der Cacheclients]: #NuGet
[Working with Caches]: #working-with-caches
[Vorgehensweise: Erstellen eines DataCache-Objekts]: #create-cache-object
[Vorgehensweise: Hinzufügen zu und Abrufen eines Objekts aus dem Cache]: #add-object
[Vorgehensweise: Angeben des Ablaufs eines Objekts im Cache]: #specify-expiration
[Vorgehensweise: Speichern des ASP.NET-Sitzungszustands im Cache]: #store-session
[Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net
  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Endpoint]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg
  
   
<!-- LINKS -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Sitzungszustandsanbieter für Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Seitenausgabecacheanbieter für Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Azure Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[OutputCache-Direktive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Problembehandlung und Diagnose]: http://go.microsoft.com/fwlink/?LinkId=320839
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache – Preisdetails]: http://www.windowsazure.com/pricing/details/cache/
[Management Portal]: https://manage.windowsazure.com/
[Cacheangebote]: http://go.microsoft.com/fwlink/?LinkId=317277
[Capacity planning]: http://go.microsoft.com/fwlink/?LinkId=320167
[Ablauf und Entfernung]: http://go.microsoft.com/fwlink/?LinkId=317278
[Hohe Verfügbarkeit]: http://go.microsoft.com/fwlink/?LinkId=317329
[Benachrichtigungen]: http://go.microsoft.com/fwlink/?LinkId=317276
[Migrieren zum Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=317347
[Managed Cache Service – Beispiele]: http://go.microsoft.com/fwlink/?LinkId=320840
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Azure Managed Cache Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=398555
[Installieren und Konfigurieren von Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/library/dn495203.aspx

[Welches Azure Cache-Angebot ist für mich das richtige?]: http://msdn.microsoft.com/library/azure/dn766201.aspx
 

<!---HONumber=July15_HO4-->