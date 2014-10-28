<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Use ASP.NET session state with Azure Websites" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Verwenden des ASP.NET-Sitzungszustands mit Azure-Websites

*Von [Rick Anderson][Rick Anderson]. Aktualisiert am 1. Juli 2014.*

In diesem Thema wird erläutert, wie Sie den Azure Redis Cache Service (Vorschau) für den Sitzungszustand verwenden.

Falls Ihre ASP.NET-Web-App den Sitzungszustand verwendet, müssen Sie einen externen Sitzungszustandsanbieter (entweder den Redis Cache Service oder einen SQL Server-Sitzungszustandsanbieter) konfigurieren. Wird der Sitzungszustand ohne einen externen Anbieter verwendet, sind Sie auf eine Instanz Ihrer Web-App beschränkt. Der Redis Cache Service lässt sich am schnellsten und einfachsten aktivieren.

Die grundlegenden Schritte zur Verwendung des Cache Service (Vorschau) für das Zwischenspeichern des Sitzungszustands umfassen:

-   [Erstellen des Caches][Erstellen des Caches]
-   [Hinzufügen des RedisSessionStateProvider-NuGet-Pakets zur Web-App][Hinzufügen des RedisSessionStateProvider-NuGet-Pakets zur Web-App]
-   [Ändern der Datei "web.config"][Ändern der Datei "web.config"]
-   [Verwenden des Sitzungsobjekts zum Speichern und Abrufen zwischengespeicherter Elemente][Verwenden des Sitzungsobjekts zum Speichern und Abrufen zwischengespeicherter Elemente]

## <span id="createcache"></span></a>Erstellen des Caches

Befolgen Sie [diese Anweisungen][diese Anweisungen] zum Erstellen des Caches.

## <span id="configureproject"></span></a>Hinzufügen des RedisSessionStateProvider-NuGet-Pakets zur Web-App

Installieren Sie das `RedisSessionStateProvider`-NuGet-Paket. Verwenden Sie den folgenden Befehl zur Installation über die Paket-Manager-Konsole (**Extras** \> **NuGet-Paket-Manager** \> **Paket-Manager-Konsole**):

`PM> Install-Package RedisSessionStateProvider -IncludePrerelease`

Bei Installation über **Extras** \> **NuGet-Package-Manager** \> **Nuget-Pakete für Projektmappe verwalten** suchen Sie nach `RedisSessionStateProvider` und stellen sicher, dass **Vorabversion einschließen** angegeben ist.

Weitere Informationen finden Sie auf der [NuGet-RedisSessionStateProvider-Seite][NuGet-RedisSessionStateProvider-Seite] und unter [Konfigurieren der Cacheclients][Konfigurieren der Cacheclients].

## <span id="configurewebconfig"></span></a>Ändern der Datei "web.config"

Das NuGet-Paket fügt nicht nur Assemblyverweise für Cache, sondern auch Stub-Einträge in die Datei *web.config* ein.

1.  Öffnen Sie *web.config*, und suchen Sie das Element **sessionState**.

2.  Geben Sie die Werte für `host`, `accessKey`, `port` (der SSL-Port sollte 6380 lauten) ein, und legen Sie `SSL` auf `true` fest. Diese Werte finden Sie im Azure-Verwaltungsvorschauportal im Fenster für Ihre Cacheinstanz. Weitere Informationen finden Sie unter [Verbindungsaufbau zum Cache][Verbindungsaufbau zum Cache].
    Der folgende Markup zeigt die Änderungen an der Datei *web.config*.

<pre class="prettyprint">
 &lt;system.web&gt; &lt;customErrors mode=&quot;Off&quot; /&gt; &lt;authentication mode=&quot;None&quot; /&gt; &lt;compilation debug=&quot;true&quot; targetFramework=&quot;4.5&quot; /&gt; &lt;httpRuntime targetFramework=&quot;4.5&quot; /&gt; &lt;sessionState mode=&quot;Custom&quot; customProvider=&quot;RedisSessionProvider&quot;&gt; &lt;providers&gt; &lt;!--&lt;add name=&quot;RedisSessionProvider&quot; host = &quot;127.0.0.1&quot; [String] port = &quot;&quot; [number] accessKey = &quot;&quot; [String] ssl = &quot;false&quot; [true|false] throwOnError = &quot;true&quot; [true|false] retryTimeoutInMilliseconds = &quot;0&quot; [number] databaseId = &quot;0&quot; [number] applicationName = &quot;&quot; [String] /&gt;--&gt; &lt;add name=&quot;RedisSessionProvider&quot; type=&quot;Microsoft.Web.Redis.RedisSessionStateProvider&quot; port=&quot;6380&quot; host=&quot;movie2.redis.cache.windows.net&quot; accessKey=&quot;m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=&quot; ssl=&quot;true&quot; /&gt; &lt;!--&lt;add name=&quot;MySessionStateStore&quot; type=&quot;Microsoft.Web.Redis.RedisSessionStateProvider&quot; host=&quot;127.0.0.1&quot; accessKey=&quot;&quot; ssl=&quot;false&quot; /&gt;--&gt; &lt;/providers&gt; &lt;/sessionState&gt; &lt;/system.web&gt;
</pre>

</p>
## <span id="usesessionobject"></span></a>Verwenden des Sitzungsobjekts im Code

Der letzte Schritt ist die Verwendung des Sitzungsobjekts im ASP.NET-Code. Fügen Sie Objekte mit der Methode **Session.Add** dem Sitzungszustand hinzu. Diese Methode verwendet Schlüssel-Wert-Paare, um Elemente im Sitzungszustandscache zu speichern.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Der folgende Code ruft diesen Wert aus dem Sitzungszustand ab.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

Sie können den Redis Cache auch zum Zwischenspeichern von Objekten in Ihrer Web-App verwenden. Weitere Informationen finden Sie unter [MVC movie app with Azure Redis Cache in 15 minutes][MVC movie app with Azure Redis Cache in 15 minutes] (MVC-Film-App mit Azure Redis Cache in 15 Minuten, in englischer Sprache).
Weitere Einzelheiten zur Verwendung des ASP.NET-Sitzungszustands finden Sie unter [Übersicht über den ASP.NET-Sitzungszustand][Übersicht über den ASP.NET-Sitzungszustand].

  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [Erstellen des Caches]: #createcache
  [Hinzufügen des RedisSessionStateProvider-NuGet-Pakets zur Web-App]: #configureproject
  [Ändern der Datei "web.config"]: #configurewebconfig
  [Verwenden des Sitzungsobjekts zum Speichern und Abrufen zwischengespeicherter Elemente]: #usesessionobject
  [diese Anweisungen]: http://azure.microsoft.com/de-de/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache
  [NuGet-RedisSessionStateProvider-Seite]: http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/
  [Konfigurieren der Cacheclients]: http://azure.microsoft.com/de-de/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet
  [Verbindungsaufbau zum Cache]: http://azure.microsoft.com/de-de/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache
  [MVC movie app with Azure Redis Cache in 15 minutes]: http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
  [Übersicht über den ASP.NET-Sitzungszustand]: http://msdn.microsoft.com/de-de/library/ms178581.aspx
