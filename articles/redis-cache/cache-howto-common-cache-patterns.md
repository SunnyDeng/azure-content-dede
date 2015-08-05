<properties 
   pageTitle="Allgemeine Cachemuster mit Azure Redis Cache" 
   description="Erfahren Sie mehr über Szenarien und Gründe für die Nutzung von Azure Redis Cache" 
   services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd" 
   ms.date="05/21/2015"
   ms.author="riande"/>

# Allgemeine Cachemuster mit Azure Redis Cache

Auf dieser Seite sind die gängigsten Vorteile bei Nutzung eines Caches aufgeführt.

## Optimieren des Datenzugriffs mit einem Cache

Mithilfe eines Caches kann der Datenzugriff im Vergleich zum Abruf aus einem Datenspeicher erheblich beschleunigt werden. Ein Cache ermöglicht einen hohen Durchsatz bei niedriger Latenz. Durch Abrufen aktiver Daten aus dem Cache können Sie nicht nur Ihre App beschleunigen, sondern auch die Datenzugriffslast verringern und ihre Reaktionsfähigkeit für andere Abfragen erhöhen. Das Speichern von Informationen in einem Cache hilft, Ressourcen einzusparen, und erhöht bei wachsenden Anforderungen an die Anwendung die Skalierbarkeit. Ihre Anwendung kann wesentlich schneller auf Bedarfsspitzen reagieren, wenn Daten effizient aus einem Cache abgerufen werden können.

## Verteilter Sitzungszustand
Wenngleich es als bewährte Methode gilt, die Verwendung des Sitzungszustands zu vermeiden, können einige Anwendungen bei Verwenden des Sitzungszustands tatsächlich mehr Leistung und weniger Komplexität profitieren. Bei anderen Anwendungen hingegen ist das Verwenden des Sitzungszustands unumgänglich. Der standardmäßige arbeitsspeicherinterne Anbieter des Sitzungszustands lässt kein horizontales Hochskalieren (d. h. die Ausführung mehrerer Instanzen der Website) zu. Der ASP.NET SQL Server-Sitzungszustandsanbieter lässt für mehrere Websites das Verwenden des Sitzungszustands zu, was aber im Vergleich zu einer arbeitsspeicherinternen Anbieter auf Kosten von Latenz geht. Der Redis--Sitzungszustands-Cacheanbieter ist Alternative mit niedriger Latenz, die sehr einfach zu konfigurieren und einzurichten ist. Wenn Ihre Anwendung nur eine begrenzte Menge des Sitzungszustands nutzt, können Sie den Großteil des Caches für die Zwischenspeicherung von Daten und einen kleinen Teil für den Sitzungszustand verwenden.

## Überstehen von Dienstausfällen (Cachefallback)
 Durch Speichern von Daten in einem Cache kann die Anwendung Systemfehler überstehen, z. B. Netzwerklatenz, Webdienstprobleme und Hardwareausfälle. Es ist oft besser, zwischengespeicherte Daten bereitzustellen, bis Ihr Webdienst oder Ihre Datenbank wiederhergestellt ist, als dass Ihre App vollständig ausfällt.

## Nächste Schritte
Weitere Informationen zu Azure Redis Cache:
 
- [Azure Redis Cache-Dokumentation](http://azure.microsoft.com/documentation/services/cache/): Diese Seite enthält viele nützliche Links zur Verwendung von Azure Redis Cache.
- [MVC movie app with Azure Redis Cache in 15 minutes](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/): Der Blogbeitrag bietet einen schnellen Einstieg in die Nutzung des Redis-Caches von Azure in einer ASP.NET MVC-App.
- [Verwenden des ASP.NET-Sitzungszustands mit Azure-Websites](../app-service-web/web-sites-dotnet-session-state-caching.md): In diesem Thema wird erläutert, wie Sie den Azure Redis Cache-Dienst für den Sitzungszustand verwenden.

<!---HONumber=July15_HO4-->