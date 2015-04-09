<properties 
   pageTitle="Verwenden von Azure-Redis-Cache mit Python" 
   description="Erste Schritte mit Azure-Redis-Cache und Python" 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/19/2015"
   ms.author="mwasson"/>

# Verwenden von Azure-Redis-Cache mit Python 

Dieser Leitfaden beschreibt die ersten Schritte mit dem Azure-Redis-Cache und Python.


## Voraussetzungen

Installieren Sie [redis-py](https://github.com/andymccurdy/redis-py).


## Erstellen eines Redis-Caches in Azure

Klicken Sie im [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=398536) auf **Neu** und dann auf **Redis-Cache**. 

  ![][1]

Geben Sie einen DNS-Hostnamen ein. Er muss das Format `<Name>.redis.cache.windows.net` haben. Klicken Sie auf **Erstellen**.

  ![][2]

Sobald der Cache erstellt wurde, klicken Sie im Portal darauf, um die Cache-Einstellungen anzuzeigen. Was Sie benötigen:

- **Hostname.** Dies entspricht der Eingabe beim Erstellen des Caches.
- **Port.** Klicken Sie auf den Link unter **Ports**, um die Ports anzuzeigen. Verwenden Sie den SSL-Port. 
- **Zugriffsschlüssel.** Klicken Sie auf den Link unter **Schlüssel** , und kopieren Sie den Primärschlüssel.

## Fügen Sie dem Cache Daten hinzu, und rufen Sie sie ab.

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'
    
Ersetzen Sie *&lt;name&gt;* durch den Namen Ihres Caches und *&lt;key&gt;* durch Ihren Zugriffsschlüssel. 


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png


<!--HONumber=49-->