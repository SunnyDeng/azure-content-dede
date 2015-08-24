<properties 
	pageTitle="CDN – Verbessern der Leistung durch Komprimieren von Dateien" 
	description="Sie können die Geschwindigkeit von Dateiübertragungen erhöhen und die Leistung beim Laden von Seiten verbessern, indem Sie Ihre Dateien komprimieren." 
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
	ms.date="08/10/2015" 
	ms.author="juliako"/>

#Verbessern der Leistung durch Komprimieren von Dateien

In diesem Thema wird erläutert, wie Sie die Geschwindigkeit von Dateiübertragungen erhöhen und die Leistung beim Laden von Seiten verbessern, indem Sie Ihre Dateien komprimieren.

CDN bietet zwei Möglichkeiten zur Unterstützung der Komprimierung:

- Sie können die Komprimierung auf Ihrem Ursprungsserver aktivieren. In diesem Fall unterstützt CDN die Komprimierung standardmäßig und sendet komprimierte Dateien an Clients. 
- Sie können die Komprimierung direkt auf CDN-Edgeservern aktivieren. In diesem Fall komprimiert CDN die Dateien und sendet sie an die Endbenutzer.

##Definitionen

- **Accept-Encoding**-Anforderungsheader: Dieser Header gibt die Komprimierungsmethoden an, die von einem Benutzer-Agent unterstützt werden. Dieser muss im Anforderungsheader enthalten sein. Weitere Informationen finden Sie unter [Definitionen für Headerfelder](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- **Inhaltstyp**: Eine Liste der Inhaltstypen, die zur Komprimierung hinzugefügt werden. Es können nur Textdateien komprimiert werden. Mögliche Werte sind z. B. "text/plain", "text/html".
- **Komprimierungsmethode**: Zu den unterstützten Komprimierungsmethoden gehören z. B. gzip/deflate/bzip2. Im Accept-Encoding-Anforderungsheader muss eine unterstützte Methode angegeben werden. 
- **Cachestatus**: Der Cachestatus gibt an, ob der angeforderte Inhalt bereits auf dem POP zwischengespeichert wurde, der der anfordernden Person am nächsten ist.  
- **Dateigröße**: Standardmäßig wird die Komprimierung nur für Dateien mit einer Größe zwischen 1 Byte und 1 MB unterstützt.  

##Workflow

1. Die anfordernde Person sendet eine Inhaltsanforderung.
2. Ein Edgeserver prüft, ob ein **Accept-Encoding**-Header vorhanden ist.
	1. Sofern vorhanden, gibt dieser Header die angeforderte Komprimierungsmethode an.
	1. Fehlt der Header, wird die Anforderung in einem nicht komprimierten Format verarbeitet.
3.	Der nächstgelegene Edge-POP überprüft den Cachestatus sowie die Komprimierungsmethode und prüft, ob noch eine gültige TTL vorliegt.
	1.	Cachefehler: Wenn die angeforderte Version nicht zwischengespeichert ist, wird die Anforderung an den Ursprungsserver weitergeleitet.
	2.	Cachetreffer mit derselben Komprimierungsmethode: Der Edgeserver sendet die komprimierten Inhalte sofort an den Client.
	3.	Cachetreffer mit anderer Komprimierungsmethode: Der Edgeserver transcodiert die Ressource in die angeforderte Komprimierungsmethode. 
	4.	Cachetreffer mit nicht komprimierten Inhalten: Wenn die anfängliche Anforderung dazu geführt hat, dass die Ressource in einem nicht komprimierten Format zwischengespeichert wurde, wird geprüft, ob für die Anforderung eine Edgeserverkomprimierung durchgeführt werden kann (die Prüfung erfolgt basierend auf den Kriterien im obigen Abschnitt zu Definition/Anforderungen).
		1.	Sofern möglich, komprimiert der Edgeserver die Datei und sendet sie an den Client.
		2.	Ist eine Komprimierung nicht möglich, sendet der Edgeserver den nicht komprimierten Inhalt sofort an den Client. 

![Dateikomprimierung](./media/cdn-file-compression/cdn-compress-files.png)

##Überlegungen 

1. Für Media Services CDN-aktivierte Streamingendpunkte ist die Komprimierung für die folgenden Inhaltstypen standardmäßig aktiviert: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Sie können die Komprimierung für die genannten Typen im Azure-Portal nicht aktivieren/deaktivieren.  
2. Auf dem Edgeserver wird nur eine Dateiversion (komprimiert oder nicht komprimiert) zwischengespeichert. Die Anforderung einer anderen Dateiversion führt dazu, dass der Inhalt vom Edgeserver transcodiert wird.  

<!---HONumber=August15_HO7-->