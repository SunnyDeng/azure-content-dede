<properties
	pageTitle="Regelmodul für Azure Content Delivery Network (CDN) – Übereinstimmungsbedingungen und Featuredetails"
	description="Dieses Thema bietet ausführliche Beschreibungen der verfügbaren Übereinstimmungsbedingungen und Features für das Regelmodul von Azure Content Delivery Network (CDN)."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>


# CDN-Regelmodul – Übereinstimmungsbedingungen und Featuredetails

Dieses Thema bietet ausführliche Beschreibungen der verfügbaren Übereinstimmungsbedingungen und Features für das [Regelmodul](cdn-rules-engine.md) von Azure Content Delivery Network (CDN).

> [AZURE.NOTE] Das Regelmodul erfordert den CDN-Tarif „Premium“. Weitere Informationen zu den Features der CDN-Tarife „Standard“ und „Premium“ finden Sie unter [Übersicht über das Azure Content Delivery Network](cdn-overview.md).

## Übereinstimmungsbedingungen

Eine Übereinstimmungsbedingung gibt bestimmte Typen von Anforderungen an, für die verschiedene sog. Features ausgeführt werden.

Sie kann beispielsweise verwendet werden, um Anforderungen von Inhalten an einem bestimmten Speicherort, von einer bestimmten IP-Adresse oder einem Land generierte Anforderungen oder anhand von Headerinformationen zu filtern.

### Always

Die Übereinstimmungsbedingung „Always“ dient zum Anwenden einer Standardmenge von Features auf alle Anforderungen.

### Device

Die Übereinstimmungsbedingung „Device“ bestimmt Anforderungen von einem Mobilgerät anhand seiner Eigenschaften.

### Location

Diese Übereinstimmungsbedingungen dienen zum Bestimmen von Anforderungen basierend auf dem Standort des Anfordernden.

Name | Zweck
-----|--------
AS Number | Bestimmt Anforderungen, die aus einem bestimmten Netzwerk stammen.
Country | Bestimmt Anforderungen, die aus den angegebenen Ländern stammen.


### Origin

Diese Übereinstimmungsbedingungen dienen zum Bestimmen von Anforderungen, die auf CDN-Speicher oder einen Kundenursprungsserver zeigen.

Name | Zweck
-----|--------
CDN Origin | Bestimmt Anforderungen von Inhalten, die in CDN-Speicher gespeichert sind.
Customer Origin | Bestimmt Anforderungen von Inhalten, die auf einem spezifischen Kundenursprungsserver gespeichert sind.


### Request

Diese Übereinstimmungsbedingungen dienen zum Bestimmen von Anforderungen basierend auf ihren Eigenschaften.

Name | Zweck
-----|--------
Client IP Address | Bestimmt Anforderungen, die von einer bestimmten IP-Adresse stammen.
Cookie Parameter | Überprüft die Cookies, die jeder Anforderung für den angegebenen Wert zugeordnet sind.
Cookie Parameter Regex | Überprüft die Cookies, die jeder Anforderung zugeordnet sind, auf den angegebenen regulären Ausdruck.
Edge Cname | Bestimmt Anforderungen, die auf einen bestimmten Edge-CNAME zeigen.
Referring Domain | Bestimmt Anforderungen, auf die die angegebenen Hostnamen verwiesen haben.
Request Header Literal | Bestimmt Anforderungen, die den angegebenen auf einen bestimmten Wert festgelegten Header enthalten.
Request Header Regex | Bestimmt Anforderungen, die den angegebenen Header enthalten, der auf einen Wert festgelegt ist, der dem angegebenen regulären Ausdruck entspricht.
Request Header Wildcard | Bestimmt Anforderungen, die den angegebenen Header enthalten, der auf einen Wert festgelegt ist, der dem angegebenen Muster entspricht.
Request Method | Bestimmt Anforderungen anhand ihrer HTTP-Methode.
Request Scheme | Bestimmt Anforderungen anhand ihres HTTP-Protokolls.

### URL

Diese Übereinstimmungsbedingungen dienen zum Bestimmen von Anforderungen basierend auf ihren URLs.

Name | Zweck
-----|--------
URL Path Directory | Bestimmt Anforderungen anhand ihres relativen Pfads.
URL Path Extension | Bestimmt Anforderungen anhand ihrer Dateinamenerweiterung.
URL Path Filename | Bestimmt Anforderungen anhand ihres Dateinamens.
URL Path Literal | Vergleicht den relativen Pfad einer Anforderung mit dem angegebenen Wert.
URL Path Regex | Vergleicht den relativen Pfad einer Anforderung mit dem angegebenen regulären Ausdruck.
URL Path Wildcard | Vergleicht den relativen Pfad einer Anforderung mit dem angegebenen Muster.
URL Query Literal | Vergleicht die Abfragezeichenfolge einer Anforderung mit dem angegebenen Wert.
URL Query Parameter | Bestimmt Anforderungen, die den angegebenen Abfragezeichenfolgen-Parameter enthalten, der auf einen Wert festgelegt ist, der einem angegebenen Muster entspricht.
URL Query Regex | Bestimmt Anforderungen, die den angegebenen Abfragezeichenfolgen-Parameter enthalten, der auf einen Wert festgelegt ist, der einem angegebenen regulären Ausdruck entspricht.
URL Query Wildcard | Vergleicht die angegebenen Werte mit der Abfragezeichenfolge der Anforderung.

## Features

Ein Feature definiert die Art der Aktion, die auf die Art von Anforderung angewendet wird, die mithilfe verschiedener Übereinstimmungsbedingungen bestimmt wurde.

### Access

Diese Features dienen zum Steuern des Zugriffs auf Inhalte.

Name | Zweck
-----|--------
Deny Access | Bestimmt, ob alle Anfragen mit der Antwort „403 – Verboten“ abgelehnt werden.
Token Auth | Bestimmt, ob die tokenbasierte Authentifizierung auf eine Anforderung angewendet wird.
Token Auth Denial Code | Bestimmt die Art der Antwort, die einem Benutzer zurückgegeben wird, wenn eine Anforderung aufgrund der tokenbasierten Authentifizierung verweigert wird.
Token Auth Ignore URL Case | Bestimmt, ob bei von der tokenbasierten Authentifizierung durchgeführten URL-Vergleichen Groß-/Kleinschreibung unterschieden wird.
Token Auth Parameter | Bestimmt, ob der Abfragezeichenfolgen-Parameter der tokenbasierten Authentifizierung umbenannt werden soll.

### Caching

Diese Features dienen zum Anpassen des Zeitpunkts und der Art der Zwischenspeicherung von Inhalten.

Name | Zweck
-----|--------
Bandwidth Parameters | Bestimmt, ob Parameter zur Bandbreitenbeschränkung (z. B. „ec\_rate“ und „ec\_prebuf“) aktiv sein werden.
Bandwidth Throttling | Schränkt die Bandbreite für die Antwort ein, die von unseren Edgeservern bereitgestellt wird.
Bypass Cache | Bestimmt, ob die Anforderung unsere Cachetechnologie nutzen kann.
Cache-Control Header Treatment | Steuert die Generierung von „Cache-Control“-Headern durch den Edgeserver, wenn das Feature „External Max-Age“ aktiv ist.
Cache-Key Query String | Bestimmt, ob der Cacheschlüssel Abfragezeichenfolgen-Parameter, die einer Anforderung zugeordnet sind, ein- oder ausschließt.
Cache-Key Rewrite | Schreibt den einer Anforderung zugeordneten Cacheschlüssel neu.
Complete Cache Fill | Bestimmt, was passiert, wenn eine Anforderung in einem teilweisen Cachefehler auf einem Edgeserver resultiert.
Compress File Types | Definiert die Dateiformate, die auf dem Server komprimiert werden.
Default Internal Max-Age | Bestimmt das Standardintervall für maximales Alter für die erneute Überprüfung des Caches von Edge- und Ursprungsserver.
Expires Header Treatment | Steuert die Generierung von „Expires“-Headern durch einen Edgeserver, wenn das Feature „External Max-Age“ aktiv ist.
External Max-Age | Bestimmt das „max-age“-Intervall für die erneute Überprüfung des Caches von Browser und Edgeserver.
Force Internal Max-Age | Bestimmt das „max-age“-Intervall für die erneute Überprüfung des Caches von Edge- und Ursprungsserver.
H.264 Support (HTTP Progressive Download) | Bestimmt die Typen von H.264-Dateiformaten, die zum Streamen von Inhalten verwendet werden können.
Honor No-Cache Request | Bestimmt, ob „No-Cache“-Anforderungen eines HTTP-Clients an den Ursprungsserver weitergeleitet werden.
Ignore Origin No-Cache | Bestimmt, ob unser CDN bestimmte Direktiven ignoriert, die von einem Ursprungsserver bereitgestellt werden.
Ignore Unsatisfiable Ranges | Bestimmt die Antwort, die an Clients zurückgegeben wird, wenn eine Anforderung den Statuscode „416 – Angeforderter Bereich nicht erfüllbar“ generiert.
Internal Max-Stale | Steuert, wie lange nach Überschreiten der normalen Ablaufzeit ein Cacheobjekt von einem Edgeserver bereitgestellt werden kann, wenn der Edgeserver das Cacheobjekt nicht im Abgleich mit dem Ursprungsserver erneut überprüfen kann.
Partial Cache Sharing | Bestimmt, ob eine Anforderung teilweise zwischengespeicherte Inhalte erstellen kann.
Prevalidate Cached Content | Bestimmt, ob zwischengespeicherte Inhalte für eine frühzeitige erneute Überprüfung in Frage kommen, ehe ihre Gültigkeitsdauer abläuft.
Refresh Zero-Byte Cache Files | Bestimmt, wie eine Anforderung eines HTTP-Clients eines Cacheobjekts mit 0 Byte von unseren Edgeservern verarbeitet wird.
Set Cacheable Status Codes | Definiert die Gruppe von Statuscodes, die in zwischengespeicherten Inhalten resultieren können.
Stale Content Delivery on Error | Bestimmt, ob abgelaufene Cache-Inhalte übermittelt werden, wenn während der erneuten Überprüfung des Caches ein Fehler auftritt oder der angeforderte Inhalt vom Kundenursprungsserver abgerufen wird.
Stale While Revalidate | Verbessert die Leistung, indem unseren Edgeservern erlaubt wird, dem Anfordernden einen veralteten Client bereitzustellen, während die erneute Überprüfung erfolgt.
Comment | Dieses Feature erlaubt das Hinzufügen eines Hinweises in einer Regel.

### Headers

Diese Features dienen zum Hinzufügen, Ändern oder Löschen von Headern in der Anforderung oder Antwort.

Name | Zweck
-----|--------
Age Response Header | Bestimmt, ob ein „Age Response Header“ in die Antwort eingeschlossen wird, die an den Anfordernden gesendet wird.
Debug Cache Response Headers | Bestimmt, ob eine Antwort den „X-EC-Debug Response Header“ enthalten kann, der Informationen zur Cacherichtlinie für das angeforderte Objekt enthält.
Modify Client Request Header | Dient zum Überschreiben, Anfügen oder Löschen eines Headers in einer Anforderung.
Modify Client Response Header | Dient zum Überschreiben, Anfügen oder Löschen eines Headers in einer Antwort.
Set Client IP Custom Header | Erlaubt das Hinzufügen der IP-Adresse des anfordernden Clients zur Anforderung als benutzerdefinierter Anforderungsheader.

### Logs

Diese Features dienen zum Anpassen der Daten, die in unformatierten Protokolldateien gespeichert sind.

Name | Zweck
-----|--------
Custom Log Field 1 | Bestimmt das Format und den Inhalt, das/der dem benutzerdefinierten Protokollfeld in einer unformatierten Protokolldatei zugewiesen wird.
Log Query String | Bestimmt, ob eine Abfragezeichenfolge zusammen mit der URL in Zugriffsprotokollen gespeichert wird.

### Optimize

Diese Features bestimmen, ob eine Anforderung den vom Edge Optimizer vorgeschlagenen Optimierungen unterzogen wird.

Name | Zweck
-----|--------
Edge Optimizer | Bestimmt, ob der Edge Optimizer auf eine Anforderung angewendet werden kann.
Edge Optimizer – Instantiate Configuration | Instanziiert oder aktiviert die Edge Optimizer-Konfiguration, die mit einem Standort verknüpft ist.


### Origin

Diese Funktionen dienen zum Steuern, wie das CDN mit einem Ursprungsserver kommuniziert.

Name | Zweck
-----|--------
Maximum Keep-Alive Requests | Definiert die maximale Anzahl von Anforderungen für eine „Keep Alive“-Verbindung, bevor diese geschlossen wird.
Proxy Special Headers | Definiert die CDN-spezifischen Anforderungsheader, die von einem Edgeserver an einen Ursprungsserver weitergeleitet werden.

### Specialty

Diese Features bieten erweiterte Funktionalität, die nur von erfahrenen Benutzern verwendet werden sollte.

Name | Zweck
-----|--------
Cacheable HTTP Methods | Bestimmt zusätzliche HTTP-Methoden, die in unserem Netzwerk zwischengespeichert werden können.
Cacheable Request Body Size | Definiert den Schwellenwert zum Bestimmen, ob eine POST-Antwort zwischengespeichert werden kann.


### URL

Diese Funktionen ermöglichen, dass eine Anforderung zu einer anderen URL umgeleitet bzw. in eine andere URL umgeschrieben wird.

Name | Zweck
-----|--------
Follow Redirects | Bestimmt, ob Anforderungen zum Hostnamen umgeleitet werden können, der im „Location“-Header definiert ist, der vom Kundenursprungsserver zurückgegeben wird.
URL Redirect | Leitet Anfragen über den „Location“-Header weiter.
URL Rewrite | Schreibt die Anforderungs-URL um.

### Web Application Firewall

Das Feature „Web Application Firewall“ bestimmt, ob eine Anforderung von der Web Application Firewall untersucht wird.

## Weitere Informationen
* [Übersicht über das Azure CDN](cdn-overview.md)
* [Überschreiben des HTTP-Standardverhaltens mithilfe des Regelmoduls](cdn-rules-engine.md)

<!---HONumber=AcomDC_0302_2016-->