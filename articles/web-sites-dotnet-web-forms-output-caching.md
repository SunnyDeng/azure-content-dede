<properties urlDisplayName="details" pageTitle="Informationen zum Videocenter" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="sdanie" />



# Verwenden der Zwischenspeicherung der ASP.NET-Webformularausgabe mit Azure-Websites

In diesem Thema wird erläutert, wie Sie den Azure Cache Service (Vorschau) zur Unterstützung der ASP.NET-Seitenausgabe-Zwischenspeicherung für ASP.NET-Webformulare verwenden. Die Seitenausgabe-Zwischenspeicherung ist eine Optimierung, die für eine bestimmte Zeitdauer eine zwischengespeicherte Ausgabeseite direkt zurückgibt. Dies ist sinnvoll in Situationen, in denen der Zugriff auf eine Seite häufiger erfolgt als deren normale Änderung. Es ist wichtig festzuhalten, dass die Seitenausgabe-Zwischenspeicherung bei ASP.NET-MVC-Anwendungen nicht unterstützt wird.

Der Cache Service (Vorschau) bietet einen verteilten Zwischenspeicherungsdienst außerhalb der Website. So können alle Instanzen der Website auf die gleiche zwischengespeicherte Ausgabe einer Seite zugreifen.

Die grundlegenden Schritte zur Verwendung des Cache Service (Vorschau) für das Zwischenspeichern der Seitenausgabe umfassen:

* [Erstellen des Caches](#createcache)
* [Konfigurieren des ASP.NET-Projekts zur Verwendung von Azure Cache](#configureproject)
* [Ändern der Datei "web.config"](#configurewebconfig)
* [Verwenden der Ausgabezwischenspeicherung für die temporäre Rückgabe zwischengespeicherter Versionen einer Seite](#useoutputcaching)

<h2><a id="createcache"></a>Erstellen des Caches</h2>
Cacheinstanzen im Managed Cache Service werden mithilfe von PowerShell-Cmdlets erstellt. 

>Nach der Erstellung einer Managed Cache Service-Instanz mit PowerShell-Cmdlets können Sie die Instanz im [Azure-Verwaltungsportal][] anzeigen und konfigurieren.

Öffnen Sie zum Erstellen einer Managed Cache Service-Instanz ein Azure PowerShell-Befehlsfenster.

>Weitere Informationen zur Installation und Verwendung von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][].

Rufen Sie das [Add-AzureAccount][]-Cmdlet auf, und geben Sie die Ihrem Konto zugeordnete E-Mail-Adresse und Ihr Kennwort ein. Ein Abonnement wird automatisch ausgewählt und nach dem Aufruf des Cmdlets [Add-AzureAccount][] angezeigt. Um das Abonnement zu ändern, rufen Sie das [Select-AzureSubscription][]-Cmdlet auf.

>Falls Sie Azure PowerShell mit einem Zertifikat für Ihr Konto verknüpft haben, können Sie diesen Schritt überspringen. Weitere Informationen zum Verbinden von Azure PowerShell mit Ihrem Azure-Konto finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][].

Ein Abonnement wird standardmäßig ausgewählt und angezeigt. Um das Abonnement zu ändern, rufen Sie das [Select-AzureSubscription][]-Cmdlet auf.

Rufen Sie das [New-AzureManagedCache][]-Cmdlet auf, und geben Sie den Namen, die Region, das Cacheangebot und die Größe des Caches an.

Geben Sie unter **Name** einen Unterdomänennamen für den Cacheendpunkt ein. Der Endpunktname muss eine Zeichenfolge zwischen sechs und zwanzig Zeichen sein. Er darf nur Kleinbuchstaben und Ziffern enthalten und muss mit einem Buchstaben beginnen.

Geben Sie unter **Location** eine Region für den Cache an. Die beste Leistung wird erzielt, wenn der Cache in der Region erstellt wird, in der sich auch die Cacheclientanwendung befindet.

Die Optionen **Sku** und **Memory** werden gemeinsam verwendet, um die Größe des Caches zu bestimmen. Managed Cache Service ist in den folgenden drei Ausführungen verfügbar.

-	Basic: Cache mit einer Größe von 128 MB bis 1 GB, der in 128-MB-Schritten vergrößert werden kann, mit einem benannten Standardcache
-	Standard: Cache mit einer Größe von 1 GB bis 10 GB, der in 1-GB-Schritten vergrößert werden kann und Benachrichtigungen sowie bis zu zehn benannte Caches unterstützt
-	Premium: Cache mit einer Größe von 5 GB bis 150 GB, der in 5-GB-Schritten vergrößert werden kann und Benachrichtigungen, hohe Verfügbarkeit sowie bis zu zehn benannte Caches unterstützt

Wählen Sie die **Sku** und den **Arbeitsspeicher** aus, die bzw. der den Anforderungen der Anwendung entspricht. Beachten Sie, dass einige Cachefeatures (z. B. Benachrichtigungen und hohe Verfügbarkeit) nur bei bestimmten Cacheangeboten verfügbar sind. Weitere Informationen zum Auswählen des für Ihre Anwendung optimal geeigneten Cacheangebots und der geeigneten Cachegröße finden Sie unter [Cacheangebote][].

 Im folgenden Beispiel wird ein Cache mit 128 MB des Typs "Basic" mit dem Namen "contosocache" in der geografischen Region "South Central US" erstellt.

	New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

>Eine vollständige Liste der Parameter und Werte für die Erstellung von Caches finden Sie in der Dokumentation zum [New-AzureManagedCache][]-Cmdlet.

Nach dem Aufrufen des PowerShell-Cmdlets dauert das Erstellen des Caches ggf. einige Minuten. Nachdem der Cache erstellt wurde, weist der neue Cache den Status "Running" auf, ist mit den Standardeinstellungen verwendungsbereit und kann im [Azure-Verwaltungsportal][] angezeigt und konfiguriert werden.

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





In den folgenden Abschnitten werden die Einstellungen aus der Registerkarte **Dashboard** verwendet, um Zwischenspeicherung für ein ASP.NET-Projekt zu konfigurieren.

<h2><a id="configureproject"></a>Konfigurieren des ASP.NET-Projekts</h2>
1. Vergewissern Sie sich zuerst, dass Sie das neueste ][]Azure SDK für .NET** 1. installiert[ haben.

2. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf das ASP.NET-Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus. (Bei Verwendung von WebMatrix klicken Sie stattdessen in der Symbolleiste auf die Schaltfläche **NuGet**.)

3. Geben Sie **WindowsAzure.Caching** in das Bearbeitungsfeld für die **Onlinesuche** ein.

	![NuGetDialog][NuGetDialog]

4. Wählen Sie das Paket **Azure Caching** aus, und klicken Sie dann auf **Installieren**.

<h2><a id="configurewebconfig"></a>Ändern der Datei "web.config"</h2>
Das NuGet-Paket fügt nicht nur Assemblyverweise für Cache, sondern auch Stub-Einträge in die Datei "web.config" ein. Um Cache für die ASP.NET-Seitenausgabe-Zwischenspeicherung zu verwenden, müssen mehrere Änderungen an der Datei "web.config" vorgenommen werden.

1. Öffnen Sie die Datei **web.config** für das ASP.NET-Projekt.

2. Wenn die Datei die Elemente **caching** und **outputCache** enthält, kommentieren Sie sie aus (oder entfernen Sie sie).

3. Anschließend müssen Sie das Element **caching** auskommentieren, das vom Azure Caching NuGet-Paket hinzugefügt wurde. Das Endergebnis sollte in etwa wie folgender Screenshot aussehen:

	![OutputConfig][OutputConfig]

4. Suchen Sie nun den Abschnitt **dataCacheClients**. Kommentieren Sie das untergeordnete Element **securityProperties** aus.

	![CacheConfig][CacheConfig]

5. Legen Sie im Element **autoDiscover** das Attribut **identifier** auf die Cache-Endpunkt-URL fest. Die Endpunkt-URL erfahren Sie in den Cache-Eigenschaften im Azure-Verwaltungsportal. Kopieren Sie auf der Registerkarte **Dashboard** den Wert für **ENDPOINT URL** in den Abschnitt **Auf einen Blick**.

	![EndpointURL][EndpointURL]

6. Legen Sie im Element **messageSecurity** das Attribut **authorizationInfo** auf den Cachezugriffsschlüssel fest. Den Zugriffsschlüssel für den Cache erfahren Sie im Azure-Verwaltungsportal. Klicken Sie anschließend auf der unteren Leiste auf **Schlüssel verwalten**. Klicken Sie neben dem Textfeld **PRIMÄRER ZUGRIFFSSCHLÜSSEL** auf die Kopierschaltfläche.

	![ManageKeys][ManageKeys]

<h2><a id="useoutputcaching"></a>Verwenden der Ausgabezwischenspeicherung</h2>
Im letzten Schritt werden Seiten in der ASP.NET-Webformularanwendung für die Ausgabezwischenspeicherung konfiguriert. Dazu wird das Attribut **OutputCache** am Anfang der .ASPX-Quelle hinzugefügt. Beispiel:

	<%@ OutputCache Duration="45" VaryByParam="*" %>

Mit dem obigen Beispielcode wird die Seite 45 Sekunden lang zwischengespeichert. Da **VaryByParam** auf "*" festgelegt ist, ändert sich diese zwischengespeicherte Seitenausgabe nicht, auch wenn unterschiedliche Abfrageparameter übergeben werden. Mit dem folgenden Beispielcode wird jedoch bei jedem Wert für den Parameter "UserId" eine andere Version der Seite zwischengespeichert:

	<%@ OutputCache Duration="45" VaryByParam="UserId" %>	

  
  
  
[installed the latest]: http://www.windowsazure.com/en-us/downloads/?sdk=net
[NewIcon]: ./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG
[NewCacheDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG
[CacheIcon]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG
[NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
[OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
[CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
[EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
[ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
  
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Cmdlets für Azure Managed Cache]: http://go.microsoft.com/fwlink/?LinkID=398555
[Installieren und Konfigurieren von Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/en-us/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/en-us/library/dn495203.aspx
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  
  
  
