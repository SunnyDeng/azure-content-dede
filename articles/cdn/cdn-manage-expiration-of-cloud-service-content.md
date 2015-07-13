<properties 
 pageTitle="Verwalten des Ablaufs von Clouddienstinhalten in Azure CDN (Content Delivery Network)" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/25/2015" 
 ms.author="mazha"/>

#Verwalten des Ablaufs von Clouddienstinhalten in Azure CDN (Content Delivery Network)

Objekte, die am meisten vom CDN-Caching von Azure profitieren, sind solche, auf die häufig während ihrer Gültigkeitsdauer zugegriffen wird. Ein Objekt bleibt für den Zeitraum seiner Gültigkeit im Cache und wird dann nach Ablauf dieser Zeit vom Cloud-Dienst aktualisiert. Anschließend wird der Prozess wiederholt.

Wenn Sie keine Cachewerte festlegen, beträgt der Gültigkeitszeitraum eines Objekts 7 Tage.

Bei statischen Inhalten wie Bildern und Stylesheets können Sie die Häufigkeit von Updates steuern, indem Sie eine web.config-Datei im Ordner "CDN" für den Inhalt einschließen. Durch Bearbeiten der **clientCache**-Einstellungen können Sie den Cache-Control-Header steuern. Die web.config-Einstellungen wirken sich auf alle Inhalte des Ordners sowie der Unterordner aus, solange die Inhalte nicht in einem weiteren untergeordneten Ordner überschrieben werden. Beispielsweise können Sie auf der Stammebene eine standardmäßige Gültigkeitsdauer festlegen, damit alle statischen Inhalte 3 Tage zwischengespeichert werden, während in einem Unterordner mit stärker veränderlichem Inhalt eine Cacheeinstellung von 6 Stunden verwendet wird.

Die folgende XML zeigt ein Beispiel zum Festlegen von **clientCache** auf ein maximales Alter von 3 Tagen:

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

Durch Angeben von **UseMaxAge** wird der Header "Cache-Control: max-age=<nnn>" anhand des im **CacheControlMaxAge**-Attribut angegebenen Werts der Antwort hinzugefügt. Das Format des Zeitraums für das **cacheControlMaxAge**-Attribut lautet <days>.<hours>:<min>:<sec>. Weitere Informationen zum **clientCache**-Knoten finden Sie unter [Clientcache<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).

Bei Inhalten, die von Anwendungen wie ASPX-Seiten zurückgegeben werden, können Sie das Verhalten von CDN beim Zwischenspeichern mithilfe der **HttpResponse.Cache**-Eigenschaft programmgesteuert festlegen. Weitere Informationen zur **HttpResponse.Cache**-Eigenschaft finden Sie unter [HttpResponse.CacheEigenschaft](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) und [HttpCachePolicy-Klasse](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).

Wenn Sie Anwendungsinhalte programmgesteuert zwischenspeichern möchten, stellen Sie sicher, dass der Inhalt entsprechend markiert ist, indem Sie "HttpCacheability" auf *Public* festlegen. Stellen Sie außerdem sicher, dass eine Cachevalidierung durchgeführt werden kann. Zu diesem Zweck kann ein Zeitstempel der letzten Änderung festgelegt werden, indem "SetLastModified" aufgerufen wird. Sie können auch einen "etag"-Wert festlegen, indem Sie "SetETag" aufrufen. Optional können Sie eine Ablaufzeit für den Cache angeben, indem Sie "SetExpires" aufrufen, oder Sie können sich auf die standardmäßige Cacheheuristik verlassen, die weiter oben in diesem Dokument beschrieben wird.

Fügen Sie beispielsweise folgenden Code hinzu, um Inhalt eine Stunde lang zwischenzuspeichern:

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

##Siehe auch

[Verwalten des Ablaufs von Blobinhalten in Azure CDN (Content Delivery Network)](./cdn-manage-expiration-of-blob-content.md)

<!---HONumber=62-->