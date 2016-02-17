<properties 
	pageTitle="Sitzungsstatus mit Azure Redis Cache in Azure App Service" 
	description="Erfahren Sie, wie Sie den Azure Cache Service zur Unterstützung des ASP.NET-Sitzungszustand-Caching verwenden." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="riande"/>


# Sitzungsstatus mit Azure Redis Cache in Azure App Service


In diesem Thema wird erläutert, wie Sie den Azure Redis Cache-Dienst für den Sitzungszustand verwenden.

Falls Ihre ASP.NET-Web-App den Sitzungszustand verwendet, müssen Sie einen externen Sitzungszustandsanbieter (entweder den Redis Cache Service oder einen SQL Server-Sitzungszustandsanbieter) konfigurieren. Wird der Sitzungszustand ohne einen externen Anbieter verwendet, sind Sie auf eine Instanz Ihrer Web-App beschränkt. Der Redis Cache Service lässt sich am schnellsten und einfachsten aktivieren.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="createcache"></a>Erstellen des Caches
Befolgen Sie [diese Anweisungen](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) zum Erstellen des Caches.

##<a id="configureproject"></a>Hinzufügen des RedisSessionStateProvider-NuGet-Pakets zur Web-App
Installieren Sie das `RedisSessionStateProvider`-NuGet-Paket. Verwenden Sie den folgenden Befehl zur Installation über die Paket-Manager-Konsole (**Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Bei Installation über**Extras** > **NuGet-Paket-Manage** > **NuGet-Pakete für Projektmappe verwalten** suchen Sie nach `RedisSessionStateProvider`.

Weitere Informationen finden Sie auf der [NuGet-RedisSessionStateProvider-Seite](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) und unter [Konfigurieren der Cacheclients](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Ändern der Datei "web.config"
Das NuGet-Paket fügt nicht nur Assemblyverweise für Cache, sondern auch Stub-Einträge in die Datei *web.config* ein.

1. Öffnen Sie *web.config*, und suchen Sie das Element **sessionState**.

1. Geben Sie die Werte für `host`, `accessKey`, `port` (der SSL-Port sollte 6380 lauten) ein, und legen Sie `SSL` auf `true` fest. Diese Werte finden Sie im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) auf dem Blatt für Ihre Cacheinstanz. Weitere Informationen finden Sie unter [Verbinden mit dem Cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Beachten Sie, dass der Nicht-SSL-Port für neue Caches standardmäßig deaktiviert ist. Weitere Informationen zum Aktivieren des Nicht-SSL-Ports finden Sie im Abschnitt [Zugriffsports](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) zum Thema [Konfigurieren eines Caches in Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx). Das folgende Markup zeigt die Änderungen an der Datei *web.config*, insbesondere die Änderungen an *port*, *host*, accessKey* und *ssl*.

		  <system.web>;
		    <customErrors mode="Off" />;
		    <authentication mode="None" />;
		    <compilation debug="true" targetFramework="4.5" />;
		    <httpRuntime targetFramework="4.5" />;
		    <sessionState mode="Custom" customProvider="RedisSessionProvider">;
		      <providers>;  
		          <!--<add name="RedisSessionProvider" 
		            host = "127.0.0.1" [String]
		            port = "" [number]
		            accessKey = "" [String]
		            ssl = "false" [true|false]
		            throwOnError = "true" [true|false]
		            retryTimeoutInMilliseconds = "0" [number]
		            databaseId = "0" [number]
		            applicationName = "" [String]
		          />;-->;
		         <add name="RedisSessionProvider" 
		              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
		              port="6380"
		              host="movie2.redis.cache.windows.net" 
		              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
		              ssl="true" />;
		      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
		      </providers>;
		    </sessionState>;
		  </system.web>;


##<a id="usesessionobject"></a> Verwenden des Sitzungsobjekts im Code
Der letzte Schritt ist die Verwendung des Sitzungsobjekts im ASP.NET-Code. Fügen Sie Objekte mit der Methode **Session.Add** zum Sitzungsstatus hinzu. Diese Methode verwendet Schlüssel-Wert-Paare, um Elemente im Sitzungszustandscache zu speichern.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Der folgende Code ruft diesen Wert aus dem Sitzungszustand ab.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

Sie können den Redis Cache auch zum Zwischenspeichern von Objekten in Ihrer Web-App verwenden. Weitere Informationen finden Sie unter [MVC-Film-App mit Azure Redis Cache in 15 Minuten](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/). Weitere Informationen zur Verwendung des ASP.NET-Sitzungsstatus finden Sie unter [Überblick über den ASP.NET-Sitzungsstatus][].

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

  *Von [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net
  [Überblick über den ASP.NET-Sitzungsstatus]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 

<!---HONumber=AcomDC_0128_2016-->