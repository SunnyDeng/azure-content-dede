<properties
	pageTitle="Verwenden von Azure Redis Cache mit Node.js | Microsoft Azure"
	description="Erste Schritte mit Azure Redis Cache mit Node.js und ";node_redis";."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="10/05/2015"
	ms.author="sdanie"/>

# Verwenden von Azure Redis Cache mit Node.js

Azure Redis Cache bietet Zugriff auf einen sicheren dedizierten Redis-Cache, der von Microsoft verwaltet wird. Auf Ihren Cache kann in jeder Anwendung in Microsoft Azure zugegriffen werden.

Dieses Thema beschreibt die ersten Schritte mit Azure Redis Cache und Node.js. Ein anderes Beispiel zum Verwenden von Azure Redis Cache mit Node.js finden Sie unter [Erstellen einer Node.js-Chatanwendung mit Socket.IO auf einer Azure-Website][].


## Voraussetzungen

Installieren Sie [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

In diesem Lernprogramm wird [node\_redis](https://github.com/mranney/node_redis) verwendet. Sie können jedoch alle unter [http://redis.io/clients](http://redis.io/clients) aufgeführten Node.js-Clients verwenden.

## Erstellen eines Redis-Caches in Azure

Klicken Sie im [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=398536) auf **Neu** und **Daten und Speicher**, und wählen Sie **Redis-Cache** aus.

  ![][1]

Geben Sie einen DNS-Hostnamen ein. Das Format hierfür ist `<name>.redis.cache.windows.net`. Klicken Sie auf **Erstellen**.

  ![][2]


Sobald Sie den Cache erstellt haben, klicken Sie im Vorschauportal darauf, um die Cache-Einstellungen anzuzeigen. Klicken Sie auf den Link unter **Schlüssel**, und kopieren Sie den Primärschlüssel. Sie benötigen ihn zum Authentifizieren von Anforderungen.

  ![][4]


## Aktivieren des Nicht-SSL-Endpunkts


Klicken Sie auf den Link unter **Ports**, und klicken Sie bei "Zugriff nur über SSL zulassen" auf **Nein**. Dadurch wird der Nicht-SSL-Port für den Cache aktiviert. Der "node\_redis"-Client unterstützt SSL derzeit nicht.

  ![][3]


## Fügen Sie dem Cache Daten hinzu, und rufen Sie sie ab.

	var redis = require("redis");

    // Add your cache name and access key.
	var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth_pass: '<key>' });

	client.set("foo", "bar", function(err, reply) {
	    console.log(reply);
	});

	client.get("foo",  function(err, reply) {
	    console.log(reply);
	});


Ausgabe:

	OK
	bar


## Nächste Schritte

- [Aktivieren Sie die Cachediagnose](cache-how-to-monitor.md#enable-cache-diagnostics), damit Sie die Integrität Ihres Caches [überwachen](cache-how-to-monitor.md) können.
- Lesen Sie die offizielle [Redis-Dokumentation](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Erstellen einer Node.js-Chatanwendung mit Socket.IO auf einer Azure-Website]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=Oct15_HO3-->