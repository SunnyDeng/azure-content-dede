<properties 
	pageTitle="Verwenden des ASP.NET-Sitzungszustands mit Azure-Websites" 
	description="Erfahren Sie, wie Sie den Azure Cache Service zur Unterstützung des ASP.NET-Sitzungszustand-Caching verwenden." 
	services="cache" 
	documentationCenter=".net" 
	authors="riande" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="1/13/2015" 
	ms.author="Rick-Anderson"/>


# Verwenden des ASP.NET-Sitzungszustands mit Azure-Websites


In diesem Thema wird erläutert, wie Sie den Azure Redis Cache Service (Vorschau) für den Sitzungszustand verwenden.

Falls Ihre ASP.NET-Web-App den Sitzungszustand verwendet, müssen Sie einen externen Sitzungszustandsanbieter (entweder den Redis Cache Service oder einen SQL Server-Sitzungszustandsanbieter) konfigurieren. Wird der Sitzungszustand ohne einen externen Anbieter verwendet, sind Sie auf eine Instanz Ihrer Web-App beschränkt. Der Redis Cache Service lässt sich am schnellsten und einfachsten aktivieren.

Die grundlegenden Schritte zur Verwendung des Cache Service (Vorschau) für das Zwischenspeichern des Sitzungszustands umfassen:

* [Erstellen des Caches](#createcache)
* [Hinzufügen des RedisSessionStateProvider-NuGet-Pakets zur Web-App](#configureproject)
* [Ändern der Datei "web.config"](#configurewebconfig)
* [Verwenden des Sitzungsobjekts zum Speichern und Abrufen zwischengespeicherter Elemente](#usesessionobject)

<h2><a id="createcache"></a>Erstellen des Caches</h2>
Befolgen Sie [diese Anweisungen](http://azure.microsoft.com/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache) zum Erstellen des Caches.

<h2><a id="configureproject"></a>Hinzufügen des RedisSessionStateProvider-NuGet-Pakets zur Web-App</h2>
Installieren Sie das `RedisSessionStateProvider`-NuGet-Paket.  Verwenden Sie den folgenden Befehl zur Installation über die Paket-Manager-Konsole (**Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**):

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
Bei Installation über **Extras** > **NuGet-Paket-Manager** > **Nuget-Pakete für Projektmappe verwalten** suchen Sie nach `RedisSessionStateProvider`, und stellen Sie sicher, dass **Vorabversion einschließen** angegeben ist.

Weitere Informationen finden Sie auf der [NuGet-RedisSessionStateProvider-Seite](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) und unter [Konfigurieren des Cacheclients](http://azure.microsoft.com/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet).

<h2><a id="configurewebconfig"></a>Ändern der Datei "web.config"</h2>
Das NuGet-Paket fügt nicht nur Assemblyverweise für Cache, sondern auch Stub-Einträge in die Datei *web.config* ein. 

1. Öffnen Sie *web.config*, und suchen Sie das Element **sessionState**.

1. Geben Sie die Werte für `host`,  `accessKey` und `port` ein (der SSL-Port sollte 6380 lauten), und legen Sie `SSL` auf `true` fest. Diese Werte finden Sie im Azure-Verwaltungsvorschauportal im Fenster für Ihre Cacheinstanz. Weitere Informationen finden Sie unter [Verbinden mit dem Cache](http://azure.microsoft.com/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache).
Das folgende Markup zeigt die Änderungen an der Datei *web.config*.


  <pre class="prettyprint">  
    &lt;system.web&gt;
    &lt;customErrors mode="Off" /&gt;
    &lt;authentication mode="None" /&gt;
    &lt;compilation debug="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
  &lt;sessionState mode="Custom" customProvider="RedisSessionProvider"&gt;
      &lt;providers&gt;  
          &lt;!--&lt;add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          /&gt;--&gt;
         &lt;add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> /&gt;
      &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" /&gt;--&gt;
      &lt;/providers&gt;
    &lt;/sessionState&gt;
  &lt;/system.web&gt;</pre>


<h2><a id="usesessionobject"></a>Verwenden des Sitzungsobjekts im Code</h2>
Der letzte Schritt ist die Verwendung des Sitzungsobjekts im ASP.NET-Code. Fügen Sie Objekte mit der Methode **Session.Add** dem Sitzungszustand hinzu. Diese Methode verwendet Schlüssel-Wert-Paare, um Elemente im Sitzungszustandscache zu speichern.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Der folgende Code ruft diesen Wert aus dem Sitzungszustand ab.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

Sie können den Redis Cache auch zum Zwischenspeichern von Objekten in Ihrer Web-App verwenden. Weitere Informationen finden Sie unter [MVC-Film-App mit Azure Redis Cache in 15 Minuten](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Weitere Informationen zur Verwendung des ASP.NET-Sitzungszustands finden Sie unter [Überblick über den ASP.NET-Sitzungszustand][].

  *Von [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://azure.microsoft.com/downloads/?sdk=net  
  [Überblick über den ASP.NET-Sitzungszustand]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png




<!--HONumber=42-->
