<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Azure Cache Service (Preview)" authors="" solutions="" manager="" editor="" />

Verwenden des Azure Cache Service (Vorschau)
============================================

In diesem Leitfaden werden die ersten Schritte bei der Verwendung von **Azure Cache Service (Vorschau)** beschrieben. Die Beispiele sind in C\#-Code geschrieben und es wird die .NET-API verwendet. Die behandelten Szenarien umfassen das **Erstellen und Konfigurieren eines Caches**, **Konfigurieren von Cacheclients**, **Hinzufügen und Entfernen von Objekten aus dem Cache, Speichern des ASP.NET-Sitzungsstatus im Cache** und **Aktivieren der Zwischenspeicherung von ASP.NET-Seitenausgaben mit dem Cache**. Weitere Informationen zum Verwenden von Azure Cache finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist Azure Cache?](#what-is)
-   [Erste Schritte mit Cache Service (Vorschau)](#getting-started-cache-service)
    -   [Erstellen des Caches](#create-cache)
    -   [Konfigurieren des Caches](#enable-caching)
    -   [Konfigurieren der Cacheclients](#NuGet)
-   [Arbeiten mit Caches](#working-with-caches)
    -   [Vorgehensweise: Erstellen eines DataCache-Objekts](#create-cache-object)
    -   [Vorgehensweise: Hinzufügen zu und Abrufen eines Objekts aus dem Cache](#add-object)
    -   [Vorgehensweise: Angeben des Ablaufs eines Objekts im Cache](#specify-expiration)
    -   [Vorgehensweise: Speichern des ASP.NET-Sitzungsstatus im Cache](#store-session)
    -   [Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache](#store-page)
-   [Nächste Schritte](#next-steps)

## Was ist Azure Cache?

Azure Cache Service (Vorschau) ist eine verteilte und skalierbare In-Memory-Lösung, mit der Sie hochgradig skalierbare und dynamische Anwendungen erstellen können, indem Sie einen extrem schnellen Datenzugriff ermöglichen.

Azure Cache Service (Vorschau) enthält die folgenden Features:

-   Vorbereitete ASP.NET-Anbieter für Sitzungsstatus und Seitenausgabecaching für schnellere Webanwendungen, ohne Änderung des Anwendungscodes
-   Zwischenspeichern beliebiger serialisierbarer verwalteter Objekte, z. B.: CLR-Objekte, Zeilen, XML und Binärdaten
-   Konsistentes Entwicklungsmodell für Azure und Windows Server AppFabric

Cache Service (Vorschau) ermöglicht den Zugriff auf einen sicheren dedizierten Cache, der von Microsoft verwaltet wird. Auf einen mit Cache Service (Vorschau) erstellten Cache kann über Anwendungen in Azure zugegriffen werden, die in Azure-Websites, Web- und Workerrollen und Virtual Machines ausgeführt werden.

Cache Service (Vorschau) ist in drei Ausführungen verfügbar:

-   Basic: Cache mit einer Größe von 128 MB bis 1 GB
-   Standard: Cache mit einer Größe von 1 GB bis 10 GB
-   Premium: Cache mit einer Größe von 5 GB bis 150 GB

Diese Ausführungen unterscheiden sich hinsichtlich der Features und des Preises. Die Features werden weiter unten in diesem Leitfaden behandelt, weitere Informationen zu den Preisen finden Sie unter [Cache – Preisdetails](http://www.windowsazure.com/en-us/pricing/details/cache/).

Dieser Leitfaden enthält eine Übersicht über die ersten Schritte mit Cache Service (Vorschau). Ausführliche Informationen zu diesen Features über den Umfang dieses Leitfadens für die ersten Schritte hinaus finden Sie in der [Übersicht über Azure Cache Service (Vorschau)](http://go.microsoft.com/fwlink/?LinkId=320830).

## Erste Schritte mit Cache Service (Vorschau)

Die ersten Schritte mit Cache Service (Vorschau) sind unkompliziert. Zunächst stellen Sie einen Cache bereit und konfigurieren ihn. Als Nächstes konfigurieren Sie die Cacheclients, sodass von diesen auf den Cache zugegriffen werden kann. Nach der Konfiguration der Cacheclients können Sie mit der Verwendung beginnen.

-   [Erstellen des Caches](#create-cache)
-   [Konfigurieren des Caches](#enable-caching)
-   [Konfigurieren der Cacheclients](#NuGet)

## Erstellen eines Caches

Melden Sie sich zunächst beim [Verwaltungsportal](https://manage.windowsazure.com/) an, um einen Cache zu erstellen.

> Wenn Sie zum ersten Mal mit Cache Service (Vorschau) arbeiten, müssen Sie Zugriff auf das Cache Service-Vorschauprogramm anfordern. Klicken Sie auf **Neu** \> **Data Services** \> **Cache Preview** \> **Preview Program**, um sich für das Vorschauprogramm zu registrieren. Befolgen Sie die Anweisungen zum Anfordern des Zugriffs auf das Cache Service-Vorschauprogramm und fahren Sie mit den nächsten Schritten fort, wenn der Zugriff gewährt wurde.

Klicken Sie auf **Neu** \> **Data Services** \> **Cache Preview** \> **Schnellerfassung**.

![NewCacheMenu](./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png)

![Schnellerfassung](./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png)

Geben Sie unter **Endpunkt** einen Unterdomänennamen für den Cacheendpunkt ein. Der Endpunktname muss eine Zeichenfolge zwischen sechs und zwanzig Zeichen sein. Er darf nur Kleinbuchstaben und Ziffern enthalten und muss mit einem Buchstaben beginnen.

Wählen Sie unter **Region** eine Region für den Cache aus. Die beste Leistung wird erzielt, wenn der Cache in der Region erstellt wird, in der sich auch die Cacheclientanwendung befindet.

Wählen Sie unter **Abonnement** das Azure-Abonnement für den Cache aus.

> Wenn das Konto nur über ein Abonnement verfügt, wird dieses automatisch ausgewählt, und die Dropdownliste **Abonnement** wird nicht angezeigt.

Die Optionen **Cache Offering** und **Cache Memory** werden gemeinsam verwendet, um die Größe des Caches zu bestimmen. Cache Service (Vorschau) ist in den folgenden drei Ausführungen verfügbar.

-   Basic: Cache mit einer Größe von 128 MB bis 1 GB, der in 128-MB-Schritten vergrößert werden kann, mit einem benannten Standardcache
-   Standard: Cache mit einer Größe von 1 GB bis 10 GB, der in 1-GB-Schritten vergrößert werden kann und Benachrichtigungen sowie bis zu zehn benannte Caches unterstützt

-   Premium: Cache mit einer Größe von 5 GB bis 150 GB, der in 5-GB-Schritten vergrößert werden kann und Benachrichtigungen, hohe Verfügbarkeit sowie bis zu zehn benannte Caches unterstützt

Wählen Sie für **Cache Offering** und **Cache Memory** die Werte aus, die den Anforderungen Ihrer Anwendung entsprechen. Beachten Sie, dass einige Cachefeatures (z. B. Benachrichtigungen und hohe Verfügbarkeit) nur bei bestimmten Cacheangeboten verfügbar sind. Weitere Informationen zum Auswählen des Cacheangebots und der am besten für Ihre Anwendung geeigneten Größe finden Sie unter [Cacheangebote](http://go.microsoft.com/fwlink/?LinkId=317277) und [Kapazitätsplanung](http://go.microsoft.com/fwlink/?LinkId=320167).

Klicken Sie nach der Konfiguration der Option des neuen Caches auf **Create a New Cache**. Die Erstellung des Caches kann einige Minuten dauern. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen. Nach der Erstellung des Caches verfügt der neue Cache über den Status **Wird ausgeführt** und kann mit den Standardeinstellungen verwendet werden. Informationen zum Anpassen der Konfiguration des Caches finden Sie im folgenden Abschnitt, [Konfigurieren des Caches](#enable-caching).

## Konfigurieren des Caches

Sie können die Optionen des Caches im Verwaltungsportal auf der Registerkarte **Konfigurieren** für Cache konfigurieren. Jeder Cache verfügt über einen benannten **Standardcache**. Die Standard- und Premium-Cacheangebote unterstützen darüber hinaus neun weitere benannte Caches, insgesamt also zehn. Jeder benannte Cache verfügt über eigene Optionen, über die Sie den Cache sehr flexibel konfigurieren können.

![NamedCaches](./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg)

Geben Sie zum Erstellen eines benannten Caches den Namen des neuen Caches in das Feld **Name** ein, geben Sie die gewünschten Optionen an, und klicken Sie auf **Speichern** und dann zur Bestätigung auf **Ja**. Klicken Sie auf **Verwerfen**, um Änderungen zu verwerfen.

Ablaufrichtlinie und Zeit (Minuten)
-----------------------------------

Mit der Option **Expiry Policy** und der Einstellung **Time (min)** wird festgelegt, wann zwischengespeicherte Elemente ablaufen. Es gibt drei Arten von Ablaufrichtlinien: **Absolut**, **Gleitend** und **Never**.

Wenn **Absolut** angegeben ist, beginnt das unter **Time (min)** angegebene Ablaufintervall, sobald ein Element zum Cache hinzugefügt wird. Nachdem das unter **Time (min)** angegebene Intervall verstrichen ist, läuft das Element ab.

Wenn **Gleitend** angegeben ist, wird das unter **Time (min)** angegebene Ablaufintervall zurückgesetzt, sobald auf das Element im Cache zugegriffen wird. Das Element läuft erst dann ab, wenn das unter **Time (min)** angegebene Intervall nach dem letzten Zugriff auf das Element verstreicht.

Wenn **Never** angegeben ist, muss für **Time (min)** der Wert **0** festgelegt werden, und Elemente laufen nicht ab.

Die Standardablaufrichtlinie ist **Absolut**, und die Standardeinstellung für **Time (min)** beträgt 10 Minuten. Die Ablaufrichtlinie gilt fest für jedes Element in einem benannten Cache, **Time (min)** kann jedoch mithilfe der Überladungen **Add** und **Put**, die einen Zeitüberschreitungsparameter übernehmen, für jedes Element angepasst werden.

Weitere Informationen zu Entfernungs- und Ablaufrichtlinien finden Sie unter [Ablauf und Entfernung](http://go.microsoft.com/fwlink/?LinkId=317278).

Benachrichtigungen
------------------

Cachebenachrichtigungen, über die Anwendungen asynchrone Benachrichtigungen empfangen können, wenn verschiedene Cachevorgänge im Cachecluster ausgeführt werden. Cachebenachrichtigungen ermöglichen auch die automatische Invalidierung von lokal zwischengespeicherten Objekten. Weitere Informationen finden Sie unter [Benachrichtigungen](http://go.microsoft.com/fwlink/?LinkId=317276).

> Benachrichtigungen sind nur im Standard- und Premium-Cacheangebot verfügbar, jedoch nicht im Basic-Cacheangebot. Weitere Informationen finden Sie unter [Cacheangebote](http://go.microsoft.com/fwlink/?LinkId=317277).

Hohe Verfügbarkeit
------------------

Bei Aktivierung von hoher Verfügbarkeit wird von jedem Element, das zum Cache hinzugefügt wird, eine Sicherungskopie erstellt. Im Fall eines unerwarteten Fehlers bei der primären Kopie des Elements ist immer noch die Sicherungskopie verfügbar.

Definitionsgemäß verdoppelt sich der erforderliche Arbeitsspeicher für jedes Element bei Verwendung der hohen Verfügbarkeit. Berücksichtigen Sie diese Auswirkungen auf den Arbeitsspeicher bei der Kapazitätsplanung. Weitere Informationen finden Sie unter [Hohe Verfügbarkeit](http://go.microsoft.com/fwlink/?LinkId=317329).

> Hohe Verfügbarkeit ist nur im Premium-Cacheangebot verfügbar, nicht im Basic- oder Standard-Cacheangebot. Weitere Informationen finden Sie unter [Cacheangebote](http://go.microsoft.com/fwlink/?LinkId=317277).

Entfernung
----------

Um die innerhalb eines Caches verfügbare Arbeitsspeicherkapazität aufrechtzuerhalten, wird die Entfernung selten verwendeter Objekte unterstützt. Wenn die Arbeitsspeicherbelegung den Schwellenwert für den Arbeitsspeicher überschreitet, werden Objekte aus dem Arbeitsspeicher entfernt, bis die Speicherauslastung sich normalisiert. Dies geschieht unabhängig davon, ob die Objekte tatsächlich abgelaufen sind. Die Entfernung ist standardmäßig aktiviert. Wenn die Entfernung deaktiviert ist, werden keine Elemente aus dem Cache entfernt, sobald die Kapazitätsgrenze erreicht ist. Stattdessen sind **Put**- und **Add**-Operationen nicht erfolgreich.

Weitere Informationen zu Entfernungs- und Ablaufrichtlinien finden Sie unter [Ablauf und Entfernung](http://go.microsoft.com/fwlink/?LinkId=317278).

Nach der Konfiguration des Caches können Sie die Cacheclients konfigurieren, um Zugriff auf den Cache zu ermöglichen.

## Konfigurieren der Cacheclients

Auf einen mit Cache Service (Vorschau) erstellten Cache kann über Azure-Anwendungen zugegriffen werden, die in Azure-Websites, Web- und Workerrollen und Virtual Machines ausgeführt werden. Es wird ein NuGet-Paket bereitgestellt, das die Konfiguration von Cacheclientanwendungen vereinfacht.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt und wählen Sie **Manage NuGet Packages** aus, um eine Clientanwendung mithilfe des Cache NuGet-Pakets zu konfigurieren.

![NuGetPackageMenu](./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png)

Wählen Sie **Azure Caching** aus, und klicken Sie auf **Installieren**. Klicken Sie dann auf **Akzeptieren**.

> Wenn **Azure Caching** nicht in der Liste angezeigt wird, geben Sie **WindowsAzure.Caching** in das Textfeld **Online suchen** ein, und wählen Sie die Option aus den Ergebnissen aus.

![NuGetPackage](./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png)

Funktionen des NuGet-Pakets: Die erforderliche Konfiguration wird in die Konfigurationsdatei der Anwendung eingefügt, und die erforderlichen Assemblyverweise werden hinzugefügt. Bei Cloud Services-Projekten wird außerdem eine Einstellung für die Cacheclient-Diagnoseebene zur Datei **ServiceConfiguration.cscfg** des Clouddiensts hinzugefügt.

> Bei ASP.NET-Webprojekten werden mit dem Cache NuGet-Paket auch zwei auskommentierte Abschnitte zur Datei **web.config** hinzugefügt. Der erste Abschnitt ermöglicht die Speicherung des Sitzungsstatus im Cache, der zweite die Zwischenspeicherung der ASP.NET-Seitenausgabe. Weitere Informationen finden Sie unter [Vorgehensweise: Speichern des ASP.NET-Sitzungsstatus im Cache](#store-session) und [Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache](#store-page).

Mit dem NuGet-Paket werden die folgenden Konfigurationselemente in die Datei **web.config** oder **app.config** der Anwendung aufgenommen. Unter dem Element **configSections** werden die Abschnitte **dataCacheClients** und **cacheDiagnostics** hinzugefügt. Wenn kein **configSections**-Element vorhanden ist, wird es als untergeordnetes Element des **configuration**-Elements erstellt.

    <configSections>
      <!-- Vorhandene Abschnitte aus Gründen der Übersichtlichkeit weggelassen. -->
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
        <!--Um den In-Role Cache im Azure Cache-Dienst zu verwenden, 
            legen Sie als Bezeichner den Cachecluster-Rollennamen fest -->
        <!--Um den Azure Cache-Dienst zu verwenden,
            legen Sie als Bezeichner den Endpunkt des Cacheclusters fest -->
        <autoDiscover isEnabled="true" identifier="[Cacherollenname oder Dienstendpunkt]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Geben Sie in diesem Abschnitt die Sicherheitseinstellungen für die Verbindung mit dem Cache an. 
            Dieser Abschnitt wird nicht benötigt, wenn der Cache auf einer Rolle gehostet wird,
            die Teil des Clouddiensts ist. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentifizierungsschlüssel]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Ersetzen Sie die folgenden zwei Elemente, nachdem die Konfiguration hinzugefügt wurde.

1.  Ersetzen Sie **[Cacherollenname oder Dienstendpunkt]** durch den Endpunkt, der im Dashboard des Verwaltungsportals angezeigt wird.

    ![Endpunkt](./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png)

2.  Heben Sie die Auskommentierung des **securityProperties**-Abschnitts auf, und ersetzen Sie **[Authentifizierungsschlüssel]** durch den Authentifizierungsschlüssel. Sie finden diesen im Verwaltungsportal, wenn Sie im Cachedashboard auf **Manage Keys** klicken.

    ![AccessKeys](./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png)

> Diese Einstellungen müssen ordnungsgemäß konfiguriert werden, ansonsten kann von den Clients nicht auf den Cache zugegriffen werden.

Bei Cloud Services-Projekten wird durch das NuGet-Paket auch eine **ClientDiagnosticLevel**-Einstellung zu den **ConfigurationSettings** der Cacheclientrolle in **ServiceConfiguration.cscfg** hinzugefügt. Das folgende Beispiel stellt den Abschnitt **WebRole1** aus der Datei **ServiceConfiguration.cscfg** mit der Ebene 1 für **ClientDiagnosticLevel** dar. Dies ist die Standardeinstellung für **ClientDiagnosticLevel**.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Vorhandene Einstellungen aus Gründen der Übersichtlichkeit weggelassen. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> Mit der Clientdiagnoseebene wird die Ebene für das Zwischenspeichern von Diagnoseinformationen, die für Cacheclients erfasst werden, konfiguriert. Weitere Informationen finden Sie unter [Problembehandlung und Diagnose](http://go.microsoft.com/fwlink/?LinkId=320839).

Das NuGet-Paket fügt außerdem Referenzen zu den folgenden Assemblys hinzu:

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Wenn es sich bei dem Projekt um ein Webprojekt handelt, wird darüber hinaus der folgende Assemblyverweis hinzugefügt:

-   Microsoft.Web.DistributedCache.dll.

> Diese Assemblys befinden sich im Ordner **C:\\Programme\\Microsoft SDKs\\Windows Azure\\.NET SDK\\[SDK-Version]\\ref\\Caching\\**.

Nachdem die Zwischenspeicherung im Clientprojekt konfiguriert wurde, können Sie die in den folgenden Abschnitten beschriebenen Methoden verwenden, um mit dem Cache zu arbeiten.

## Arbeiten mit Caches

In den Schritten in diesem Abschnitt wird die Ausführung allgemeiner Aufgaben mit dem Cache beschrieben.

-   [Vorgehensweise: Erstellen eines DataCache-Objekts](#create-cache-object)
-   [Vorgehensweise: Hinzufügen zu und Abrufen eines Objekts aus dem Cache](#add-object)
-   [Vorgehensweise: Angeben des Ablaufs eines Objekts im Cache](#specify-expiration)
-   [Vorgehensweise: Speichern des ASP.NET-Sitzungsstatus im Cache](#store-session)
-   [Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache](#store-page)

## Vorgehensweise: Erstellen eines DataCache-Objekts

Um programmseitig mit dem Cache arbeiten zu können, benötigen Sie eine Referenz auf den Cache. Fügen Sie die folgende Zeile oben in jeder Datei hinzu, über die Sie Azure Cache verwenden möchten:

    using Microsoft.ApplicationServer.Caching;

> Wenn die Typen in der **using**-Anwendung nach der Installation des Cache NuGet-Pakets, mit dem die erforderlichen Verweise hinzugefügt werden, nicht von Visual Studio erkannt werden, stellen Sie sicher, dass das Zielprofil für das Projekt .NET Framework 4 oder höher ist, und wählen Sie eines der Profile aus, in dem nicht **Client Profile** angegeben ist. Anweisungen zum Konfigurieren von Cacheclients finden Sie unter [Konfigurieren der Cacheclients](#NuGet).

Es gibt zwei Möglichkeiten, um ein **DataCache**-Objekt zu erstellen. Sie können entweder einfach einen **DataCache** erstellen und den Namen des gewünschten Caches angeben.

    DataCache cache = new DataCache("default");

Sobald der **DataCache** instanziiert ist, können Sie ihn verwenden, um mit dem Cache wie in den nachfolgenden Abschnitten beschrieben, zu interagieren.

Die zweite Möglichkeit besteht darin, mithilfe des Standardkonstruktors ein neues **DataCacheFactory**-Objekt in der Anwendung zu erstellen. Dadurch verwendet der Cacheclient die Einstellungen in der Konfigurationsdatei. Rufen Sie entweder die **GetDefaultCache**-Methode der neuen **DataCacheFactory**-Instanz auf, damit ein **DataCache**-Objekt zurückgegeben wird, oder die **GetCache**-Methode, und übergeben Sie den Namen Ihres Caches. Bei diesen Methoden wird ein **DataCache**-Objekt zurückgegeben, das Sie anschließend verwenden können, um programmseitig auf den Cache zuzugreifen.

    // Cacheclient wurde gemäß der Einstellungen in der Anwendungskonfigurationsdatei konfiguriert.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Oder DataCache cache = cacheFactory.GetCache("MyCache");
    // Der Cache kann jetzt verwendet werden, um Elemente hinzuzufügen und abzurufen.  

## Vorgehensweise: Hinzufügen zu und Abrufen eines Objekts aus dem Cache

Mit der **Add**- oder **Put**-Methode können Sie ein Element zum Cache hinzufügen. Durch die **Add**-Methode wird das angegebene Objekt zum Cache hinzugefügt, der Schlüssel ist der Wert des Schlüsselparameters.

    // Zeichenfolge "value" zum Cache hinzufügen, Schlüssel lautet "item"
    cache.Add("item", "value");

Wenn sich bereits ein Objekt mit demselben Schlüssel im Cache befindet, wird eine **DataCacheException** mit der folgenden Meldung ausgegeben:

> ErrorCode:SubStatus: Es wird versucht, ein Objekt zu erstellen, dessen Schlüssel bereits im Cache vorhanden ist. Der Cache akzeptiert nur eindeutige Schlüsselwerte für Objekte.

Zum Abrufen eines Objekts mit einem bestimmten Schlüssel kann die **Get**-Methode verwendet werden. Wenn das Objekt vorhanden ist, wird es zurückgegeben, falls nicht, wird Null zurückgegeben.

    // Zeichenfolge "value" zum Cache hinzufügen, Schlüssel lautet "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" nicht im Cache. Aus angegebener Datenquelle abrufen
        // und hinzufügen.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" ist im Cache, Ergebnis in richtigen Typ umwandeln.
    }

Mit der **Put**-Methode wird das Objekt mit dem angegebenen Schlüssel dem Cache hinzugefügt, falls es nicht vorhanden ist, bzw. ersetzt, falls es vorhanden ist.

    // Zeichenfolge "value" zum Cache hinzufügen, Schlüssel lautet "item". Wenn vorhanden,
    // ersetzen.
    cache.Put("item", "value");

## Vorgehensweise: Angeben des Ablaufs eines Objekts im Cache

Standardmäßig laufen Elemente im Cache 10 Minuten nach der Ablage im Cache ab. Dies kann in der Einstellung **Time (min)** auf der Registerkarte **Konfigurieren** für Cache im Verwaltungsportal konfiguriert werden.

![NamedCaches](./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg)

Es gibt drei Einstellungen für **Expiry Policy**: **Never**, **Absolut** und **Gleitend**. Damit wird konfiguriert, wie durch **Time (min)** der Ablauf bestimmt wird. Der Standardablauftyp lautet **Absolut** und bedeutet, dass der Countdown für den Ablauf eines Elements beginnt, sobald dieses im Cache abgelegt wird. Sobald die für ein Element angegebene Zeitdauer verstrichen ist, läuft es ab. Wenn **Gleitend** angegeben ist, wird der Ablaufcountdown für ein Element bei jedem Zugriff darauf im Cache zurückgesetzt, und das Element läuft erst ab, wenn die angegebene Zeitdauer seit dem letzten Zugriff verstrichen ist. Bei Angabe von **Never** muss für **Time (min)** der Wert **0** festgelegt werden. Die Elemente laufen nicht ab und bleiben für den gesamten Verbleib im Cache gültig.

Wenn ein längeres oder kürzeres Zeitüberschreitungsintervall anstelle der im Cache konfigurierten Einstellung gewünscht wird, kann eine bestimmte Dauer angegeben werden, wenn ein Element dem Cache hinzugefügt oder darin aktualisiert wird. Dazu wird die Überladung von **Add** und **Put** verwendet, die einen **TimeSpan**-Parameter übernimmt. Im folgenden Beispiel wird die Zeichenfolge **value** mit dem Schlüssel **item** und einer Zeitüberschreitungseinstellung von 30 Minuten dem Cache hinzugefügt.

    // Zeichenfolge "value" zum Cache hinzufügen, Schlüssel lautet "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Um das verbleibende Zeitüberschreitungsintervall eines Elements im Cache anzuzeigen, kann mit der **GetCacheItem**-Methode ein **DataCacheItem**-Objekt abgerufen werden, das Informationen zu dem Element im Cache (einschließlich dem verbleibenden Zeitüberschreitungsintervall) enthält.

    // DataCacheItem-Objekt abrufen, das Informationen zu
    // "item" im Cache enthält. Wenn es kein Objekt mit dem Schlüssel "item" gibt, wird
    // Null zurückgegeben. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

## Vorgehensweise: Speichern des ASP.NET-Sitzungsstatus im Cache

Der Sitzungsstatusanbieter für Azure Cache ist ein prozessunabhängiger Speichermechanismus für ASP.NET-Anwendungen. Mit diesem Anbieter können Sie den Sitzungsstatus in einem Azure-Cache speichern und müssen dies nicht im Arbeitsspeicher oder ein einer SQL Server-Datenbank tun. Konfigurieren Sie zuerst den Cache und danach die ASP.NET-Anwendung für Cache mithilfe des Cache NuGet-Pakets, wie unter [Erste Schritte mit Cache Service (Vorschau)](#getting-started-cache-service) beschrieben, um den Anbieter des Cachesitzungsstatus zu verwenden. Wenn das Cache NuGet-Paket installiert ist, wird ein auskommentierter Abschnitt in **web.config** hinzugefügt, der die erforderliche Konfiguration für die ASP.NET-Anwendung enthält, um den Sitzungsstatusanbieter für Azure Cache verwenden zu können.

    <!--Auskommentierung dieses Abschnitt zur Verwendung von Azure Cache für das Zwischenspeichern des Sitzungsstatus aufheben
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

> Wenn die Datei **web.config** diesen auskommentierten Abschnitt nach der Installation des Cache NuGet-Pakets nicht enthält, stellen Sie sicher, dass der aktuelle NuGet Package Manager (verfügbar unter [NuGet Package Manager](http://go.microsoft.com/fwlink/?LinkId=240311)) installiert ist. Deinstallieren Sie das Paket dann und installieren Sie es neu.

Heben Sie die Auskommentierung des angegebenen Abschnitts auf, um den Sitzungsstatusanbieter für Azure Cache zu aktivieren. Der Standardcache wird im bereitgestellten Codeausschnitt angegeben. Geben Sie den gewünschten Cache im **cacheName**-Attribut an, wenn ein anderer Cache verwendet werden soll.

Weitere Informationen zum Verwenden des Sitzungsstatusanbieters des Cache-Diensts finden Sie unter [Sitzungsstatusanbieter für Azure Cache](http://go.microsoft.com/fwlink/?LinkId=320835).

## Vorgehensweise: Speichern der ASP.NET-Seitenausgabe im Cache

Der Ausgabecacheanbieter für Azure Cache ist ein prozessunabhängiger Speichermechanismus für Ausgabecachedaten. Diese Daten sind für vollständige HTTP-Antworten bestimmt (Zwischenspeichern von Seitenausgaben). Der Anbieter wird über den neuen Erweiterungspunkt für Ausgabecacheanbieter integriert, der in ASP.NET 4 eingeführt wurde. Konfigurieren Sie zuerst das Cachecluster und dann die ASP.NET-Anwendung für die Zwischenspeicherung mithilfe des Cache NuGet-Pakets, wie unter [Erste Schritte mit Cache Service (Vorschau)](#getting-started-cache-service) beschrieben, um den Ausgabecacheanbieter zu verwenden. Bei der Installation des Cache NuGet-Pakets wird der folgende auskommentierte Abschnitt in **web.config** hinzugefügt, der die erforderliche Konfiguration für die ASP.NET-Anwendung enthält, um den Ausgabecacheanbieter für Azure Cache verwenden zu können.

    <!--Auskommentierung dieses Abschnitt zur Verwendung von Azure Cache für das Zwischenspeichern der Ausgabe aufheben
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </outputCache>
    </caching>-->

> Wenn die Datei **web.config** diesen auskommentierten Abschnitt nach der Installation des Cache NuGet-Pakets nicht enthält, stellen Sie sicher, dass der aktuelle NuGet Package Manager (verfügbar unter [NuGet Package Manager](http://go.microsoft.com/fwlink/?LinkId=240311)) installiert ist. Deinstallieren Sie das Paket dann und installieren Sie es neu.

Heben Sie die Auskommentierung des angegebenen Abschnitts auf, um den Ausgabecacheanbieter für Azure Cache zu aktivieren. Der Standardcache wird im bereitgestellten Codeausschnitt angegeben. Geben Sie den gewünschten Cache im **cacheName**-Attribut an, wenn ein anderer Cache verwendet werden soll.

Fügen Sie jeder Seite, für die Sie die Ausgabe zwischenspeichern möchten, eine **OutputCache**-Direktive hinzu.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

In diesem Beispiel verbleiben die zwischengespeicherten Daten 60 Sekunden lang im Cache. Für jede Parameterkombination wird eine andere Version der Seite zwischengespeichert. Weitere Informationen zu den verfügbaren Optionen finden Sie unter [OutputCache-Direktive](http://go.microsoft.com/fwlink/?LinkId=251979).

Weitere Informationen zum Verwenden des Ausgabecacheanbieters für Azure Cache finden Sie unter [Seitenausgabecacheanbieter für Azure Cache](http://go.microsoft.com/fwlink/?LinkId=320837).

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Cache Service (Vorschau) vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Zwischenspeicherungsaufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Cache Service (Vorschau)](http://go.microsoft.com/fwlink/?LinkId=320830)
-   Informationen zum Migrieren zu Cache Service (Vorschau): [Migration in Cache Service (Vorschau)](http://go.microsoft.com/fwlink/?LinkId=317347)
-   Sehen Sie sich die Beispiele an: [Beispiele für Cache Service (Vorschau)](http://go.microsoft.com/fwlink/?LinkId=320840)

