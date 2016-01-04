<properties 
	pageTitle="CDN-Cachingrichtlinie in der Media Services-Erweiterung" 
	description="Dieses Thema bietet einen Überblick über CDN-Cachingrichtlinien in der Media Services-Erweiterung." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="juliako"/>

#CDN-Cachingrichtlinie in der Media Services-Erweiterung

Azure Media Services bietet HTTP-basiertes adaptives Streaming und progressive Downloads. HTTP-basiertes Streaming ist hochgradig skalierbar und bietet den Vorteil, dass Dateien auf Proxy- und CDN-Ebene sowie clientseitig zwischengespeichert werden können. Streamingendpunkte bieten allgemeine Streamingfunktionen sowie Konfigurationsoptionen für HTTP-Cacheheader. Streamingendpunkte legen die HTTP-Cachesteuerungsheader "max-age" und "Expires" fest. Weitere Informationen zu HTTP-Cacheheadern finden Sie unter [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##Standardcachingheader

Standardmäßig wenden Streamingendpunkte Cacheheader zum Zwischenspeichern für 3 Tage auf On-Demand-Streamingdaten (tatsächliche Medienfragmente/-segmente) und Manifeste (Wiedergabelisten) an. Beim Livestreaming wenden Streamingendpunkte Cacheheader zum Zwischenspeichern für 3 Tage auf Daten (tatsächliche Medienfragmente/-segmente) und für 2 Sekunden auf Manifeste (Wiedergabelisten) an. Wenn ein Liveprogramm in ein On-Demand-Programm umgewandelt wird (Livearchiv), werden die Cacheheader für On-Demand-Streaming angewendet.

##Azure CDN-Integration

Azure Media Services bietet ein [integriertes CDN](http://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) für Streamingendpunkte. Cachesteuerungsheader werden auf CDN-aktivierte Streamingendpunkte genauso angewendet wie Streamingendpunkte. Azure CDN verwendet für Streamingendpunkte konfigurierte Cachewerte, um die Lebensdauer der intern zwischengespeicherten Objekte zu definieren, und verwendet diesen Wert, um die Übermittlungscacheheader festzulegen. Bei der Verwendung von CDN-aktivierten Streamingendpunkten empfiehlt es sich nicht, kleine Cachewerte festzulegen. Das Festlegen von kleinen Werten senkt die Leistung und reduziert die Vorteile von CDN. Es ist nicht zulässig, Cacheheader für CDN-aktivierte Streamingendpunkte auf Werte unterhalb von 600 Sekunden festzulegen.

##Konfigurieren von Cacheheadern mit Azure Media Services

Sie können das Azure Verwaltungsportal oder Azure Media Services-APIs verwenden, um Cacheheaderwerte zu konfigurieren.

1. Informationen zum Konfigurieren von Cacheheadern über das Verwaltungsportal finden Sie unter [Verwalten von Streamingendpunkten](../media-services-manage-origins.md) im Abschnitt "Konfigurieren des Streamingendpunkts".
2. Azure Media Services-REST-API, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl-Eigenschaften](http://go.microsoft.com/fwlink/?LinkId=615302).

##Rangfolge der Cachekonfiguration

1. In Azure Media Services konfigurierte Cachewerte überschreiben Standardwerte.
2. Wenn keine manuelle Konfiguration vorhanden ist, werden die Standardwerte angewendet.
3. Cacheheader zum Zwischenspeichern für 2 Sekunden werden unabhängig von der Azure Media- oder Azure Storage-Konfiguration auf Livestreamingmanifeste (Wiedergabelisten) angewendet. Dieser Wert kann nicht überschrieben werden.
 

<!---HONumber=AcomDC_1203_2015-->