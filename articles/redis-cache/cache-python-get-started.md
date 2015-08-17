<properties
   pageTitle="Verwenden von Azure Redis Cache mit Python"
   description="Erste Schritte mit Azure Redis Cache und Python"
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="python"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="08/04/2015"
   ms.author="mwasson"/>

# Verwenden von Azure Redis Cache mit Python

Dieses Thema beschreibt die ersten Schritte mit Azure Redis Cache und Python.


## Voraussetzungen

Installieren Sie [redis-py](https://github.com/andymccurdy/redis-py).


## Erstellen eines Redis-Caches in Azure

Klicken Sie im [Azure-Verwaltungsportal (Vorschau)](http://go.microsoft.com/fwlink/?LinkId=398536) auf **Neu** und **Daten und Speicher**, und wählen Sie **Redis-Cache** aus.

  ![][1]

Geben Sie einen DNS-Hostnamen ein. Das Format hierfür ist `<name>.redis.cache.windows.net`. Klicken Sie auf **Erstellen**.

  ![][2]

Sobald der Cache erstellt wurde, klicken Sie im Portal darauf, um die Cache-Einstellungen anzuzeigen. Was Sie benötigen:

- **Hostname.** Diesen haben Sie beim Erstellen des Caches eingegeben.
- **Port.** Klicken Sie auf den Link unter **Ports**, um die Ports anzuzeigen. Verwenden Sie den SSL-Port.
- **Zugriffsschüssel.** Klicken Sie auf den Link unter **Schlüssel**, und kopieren Sie den Primärschlüssel.

## Fügen Sie dem Cache Daten hinzu, und rufen Sie sie ab.

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'

Ersetzen Sie *&lt;name&gt;* durch den Namen Ihres Caches und *&lt;name&gt;* durch Ihren Zugriffsschlüssel.


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=August15_HO6-->