<properties
	pageTitle="Übersicht über das Azure CDN"
	description="Erfahren Sie, was das Azure Content Delivery Network (CDN, Netzwerk für die Inhaltsübermittlung) ist und wie es für die Übermittlung breitbandiger Inhalte eingesetzt wird, indem Blobs und statische Inhalte zwischengespeichert werden."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# Übersicht über das Azure Content Delivery Network (CDN)

Das Azure CDN (Content Delivery Network, Netzwerk für die Inhaltsübermittlung) speichert von Clouddiensten verwendete Azure-Blobs und statische Inhalte an strategisch platzierten Standorten zwischen, um für das Bereitstellen von Inhalten für Benutzer maximale Bandbreite zu bieten.

Wenn Sie bereits CDN-Kunde sind, können Sie jetzt Ihre CDN-Endpunkte über das [Microsoft Azure-Portal](https://portal.azure.com) verwalten.


Das CDN bietet Entwicklern eine globale Lösung für die Übermittlung von Inhalten mit hoher Bandbreite durch Zwischenspeichern der Inhalte auf physischen Knoten auf der ganzen Welt. Eine aktuelle Liste von CDN-Knotenstandorten finden Sie unter [POP-Standorte von Azure Content Delivery Network (CDN)](cdn-pop-locations.md).

Die Verwendung des CDN zum Zwischenspeichern von Azure-Daten bietet folgende Vorteile:

- Bessere Leistung und Benutzerfreundlichkeit für Endbenutzer, die von einer Inhaltsquelle weit entfernt sind und Anwendungen verwenden, die viele Verbindungen mit dem Internet benötigen, um Inhalte zu laden.
- Große verteilte Skalierung, um hohe Lasten etwa zu Beginn eines Ereignisses, z. B. bei einer Produkteinführung, besser verarbeiten zu können


>[AZURE.IMPORTANT] Wenn Sie einen CDN-Endpunkt erstellen oder aktivieren, dauert es bis zu 90 Minuten, bis dieser weltweit verfügbar ist.

Wenn eine Anforderung für ein Objekt zuerst an das CDN gerichtet wird, wird das Objekt direkt aus seinem ursprünglichen Speicherort abgerufen. Dieser Ursprung kann ein Azure-Speicherkonto, eine Web-App, ein Cloud-Dienst oder ein beliebiger benutzerdefinierter Ursprung, der öffentlichen Webanforderungen akzeptiert, sein. Bei einer Anforderung mit der CDN-Syntax wird die Anforderung zum CDN-Endpunkt umgeleitet, der dem Standort am nächsten ist, von dem die Anforderung gestellt wurde, um auf das Objekt zuzugreifen. Wenn das Objekt an diesem Endpunkt nicht gefunden wird, wird es aus dem Dienst abgerufen und am Endpunkt zwischengespeichert, wobei eine Einstellung für Gültigkeitsdauer für das zwischengespeicherte Objekt verwaltet wird.

## Standard-Features

Der CDN-Tarif „Standard“ enthält die folgenden Features:

- Einfache Integration mittels der Azure-Dienste wie z. B. [Speicher](cdn-create-a-storage-account-with-cdn.md), Web-Apps und Media Services
- [Zwischenspeicherung von Abfragezeichenfolgen](cdn-query-string.md)
- [Unterstützung benutzerdefinierter Domänennamen](cdn-map-content-to-custom-domain.md)
- [Länderfilter](cdn-restrict-access-by-country.md)
- [Wichtige Analysen](cdn-analyze-usage-patterns.md)
- [Benutzerdefinierter Inhaltsursprung](cdn-how-to-use-cdn.md#caching-content-from-custom-origins)
- [HTTPS-Unterstützung](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- Lastenausgleich
- DDoS-Schutz
- [Schnelles Löschen](cdn-purge-endpoint.md)
- [Vorabladen von Assets](cdn-preload-endpoint.md)
- [Verwaltung über die REST-API](https://msdn.microsoft.com/library/mt634456.aspx)


## Premium-Features

Der CDN-Tarif „Premium“ beinhaltet alle Funktionen des Standard-Tarifs und folgende zusätzliche Features:

- [Anpassbares, regelbasiertes Modul zur Inhaltsübermittlung](cdn-rules-engine.md)
- [Erweiterte HTTP-Berichte](cdn-advanced-http-reports.md)
- [Echtzeitstatistiken](cdn-real-time-stats.md)

<!---HONumber=AcomDC_0302_2016-->