<properties
   pageTitle="Verwenden von Azure Redis Cache mit Java"
   description="Erste Schritte mit Azure Redis Cache mit Java"
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="04/30/2015"
   ms.author="mwasson"/>

# Verwenden von Azure Redis Cache mit Java

Azure Redis Cache bietet Zugriff auf einen sicheren dedizierten Redis-Cache, der von Microsoft verwaltet wird. Auf Ihren Cache kann in jeder Anwendung in Microsoft Azure zugegriffen werden.

Dieses Thema beschreibt die ersten Schritte mit Azure Redis Cache und Java.


## Voraussetzungen

[Jedis](https://github.com/xetorthio/jedis) – Java-Client für Redis

In diesem Lernprogramm wird Jedis verwendet. Sie können jedoch alle unter [http://redis.io/clients](http://redis.io/clients) aufgeführten Java-Clients nutzen.


## Erstellen eines Redis-Caches in Azure

Klicken Sie im [Azure-Verwaltungsportal (Vorschau)](http://go.microsoft.com/fwlink/?LinkId=398536) auf **Neu** und **Daten und Speicher**, und wählen Sie **Redis-Cache** aus.

  ![][1]

Geben Sie einen DNS-Hostnamen ein. Das Format hierfür ist `<name>.redis.cache.windows.net`. Klicken Sie auf **Erstellen**.

  ![][2]


Sobald der Cache erstellt wurde, klicken Sie im Portal darauf, um die Cache-Einstellungen anzuzeigen. Klicken Sie auf den Link unter **Schlüssel**, und kopieren Sie den Primärschlüssel. Sie benötigen ihn zum Authentifizieren von Anforderungen.

  ![][4]


## Aktivieren des Nicht-SSL-Endpunkts


Klicken Sie auf den Link unter **Ports**, und klicken Sie bei "Zugriff nur über SSL zulassen" auf **Nein**. Dadurch wird der Nicht-SSL-Port für den Cache aktiviert. Der Jedis-Client unterstützt SSL derzeit nicht.

  ![][3]


## Fügen Sie dem Cache Daten hinzu, und rufen Sie sie ab.

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure your turn on non SSL port in Azure Redis using the Configuration section in the Azure portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## Nächste Schritte

- [Aktivieren Sie die Cachediagnose](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), damit Sie die Integrität Ihres Caches [überwachen](https://msdn.microsoft.com/library/azure/dn763945.aspx) können.
- Lesen Sie die offizielle [Redis-Dokumentation](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

<!---HONumber=July15_HO1-->