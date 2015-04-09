<properties 
	pageTitle="Azure-Redis-Cache - Beispiele" 
	description="Erfahren Sie, wie Sie den Azure-Redis-Cache verwenden." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="sdanie"/>

# Azure-Redis-Cache - Beispiele 

Dieses Thema enthält eine Liste von Azure-Redis-Cache-Beispielen für Szenarien wie z. B. das Herstellen einer Verbindung mit einem Cache, das Lesen und Schreiben von Daten in und aus einem Cache und die Verwendung des ASP.NET-Redis-Cacheanbieters. Einige der Beispiele sind herunterladbare Projekte und einige bieten eine schrittweise Anleitung und Codeausschnitte, ohne mit einem herunterladbaren Projekt verknüpft zu sein.

## Hello World-Beispiele

Die Beispiele in diesem Abschnitt veranschaulichen die Grundlagen des Herstellens einer Verbindung mit einer Azure-Redis-Cache-Instanz und zum Lesen und Schreiben von Daten in dem Cache mithilfe einer Vielzahl von Sprachen und Redis-Clients.

Das [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)-Beispiel zeigt, wie das Lesen und Schreiben von Cache-Elementen mithilfe des [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-.NET-Clients funktioniert.

[Verwenden von Azure-Redis-Cache mit Node.js](cache-nodejs-get-started.md) zeigt die ersten Schritte mit Azure-Redis-Cache mit Node.js und dem [node_redis](https://github.com/mranney/node_redis)-Client.

[Verwenden von Azure-Redis-Cache mit Java](cache-java-get-started.md) zeigt die ersten Schritte mit Azure-Redis-Cache mit Java und dem [Jedis](https://github.com/xetorthio/jedis)-Client.

[Verwenden von Azure-Redis-Cache mit Python](cache-python-get-started.md) zeigt die ersten Schritte mit Azure-Redis-Cache mit Python und dem [redis-py](https://github.com/andymccurdy/redis-py)-Client.

Das [PHP-Beispiel](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample) veranschaulicht die ersten Schritte mit Azure-Redis-Cache mit PHP und dem [predis](https://github.com/nrk/predis)-Client.

[Arbeiten mit .NET-Objekten im Cache](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects) zeigt Ihnen eine Möglichkeit zum Serialisieren von .NET-Objekten, um sie aus einer Instanz von Azure-Redis-Cache zu lesen und zu schreiben. 

## Verwenden von Redis-Cache als horizontal hochskalierbare Rückwandplatine für ASP.NET SignalR

Das Beispiel [Verwenden von Redis-Cache als horizontal hochskalierbare Rückwandplatine für ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) veranschaulicht das Verwenden von Azure-Redis-Cache als SignalR-Rückwandplatine. Weitere Informationen zur Rückwandplatine finden Sie unter [Horizontales Hochskalieren von SignalR Scaleout mit Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## Redis-Cache-Beispiel für Kundenabfrage

Dieses Beispiel veranschaulicht Leistungsvergleiche zwischen den Zugriff auf Daten aus einem Cache und dem Zugriff auf Daten in persistentem Speicher. Dieses Beispiel enthält zwei Projekte.

-	[Demo, wie Redis-Cache die Leistung durch das Zwischenspeichern von Daten verbessern kann](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[Seeding der Datenbank und des Caches für die Demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## ASP.NET-Sitzungszustand und Ausgabezwischenspeicherung

Das Beispiel[Verwenden von Azure-Redis-Cache zum Speichern von ASP.NET SessionState und OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) veranschaulicht die Nutzung von Azure-Redis-Cache zum Speichern des ASP.NET-Sitzungs- und Ausgabecaches mit den "SessionState"- und "OutputCache"-Anbietern für Redis.

## Verwalten von Azure-Redis-Cache mit MAML

Das Beispiel [Verwalten von Azure-Redis-Cache mithilfe von Azure-Verwaltungsbibliotheken](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) veranschaulicht, wie Sie Azure-Verwaltungsbibliotheken zum Verwalten (Erstellen/Aktualisieren/Löschen) Ihres Caches nutzen können. 

## Beispiel einer benutzerdefinierten Überwachung

Das Beispiel [Zugreifen auf Redis-Cache-Überwachungsdaten](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstriert, wie Sie auf Überwachungsdaten für Ihren Azure-Redis-Cache außerhalb des Azure-Portals zugreifen können.



<!--HONumber=49-->