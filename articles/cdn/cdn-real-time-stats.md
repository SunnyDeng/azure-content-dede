<properties 
	pageTitle="CDN – Echtzeitstatistiken" 
	description="Echtzeitstatistiken in Microsoft Azure CDN. Echtzeitstatistiken bieten Echtzeitdaten über die Leistung unseres CDN bei der Übermittlung von Inhalten an Ihre Clients." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Echtzeitstatistiken in Microsoft Azure CDN

## Übersicht

Dieses Dokument erläutert Echtzeitstatistiken in Microsoft Azure CDN. Diese Funktionalität bietet Echtzeitdaten über die Leistung unseres CDN bei der Übermittlung von Inhalten an Ihre Clients.

> [AZURE.NOTE]Echtzeitstatistiken sind ein Feature des CDN-Tarifs „Premium“. Einen Vergleich der Features der CDN-Tarife „Standard“ und „Premium“ finden Sie in der [Übersicht über das Azure CDN](cdn-overview.md).

Die folgenden Diagramme sind bei der Anzeige von Echtzeitstatistiken für die HTTP-basierten Plattformen verfügbar:

* [Bandbreite](#bandwidth)
* [Statuscodes](#status-codes)
* [Cachestatus](#cache-statuses)
* [Verbindungen](#connections)

> [AZURE.NOTE]Jedes der oben genannten Diagramme zeigt Echtzeitstatistiken für einen bestimmten Zeitraum an. Sobald der angegebene Zeitraum verstrichen ist, wird ein gleitendes Fenster der Daten angezeigt. Dies bedeutet, dass alte Daten aus dem Diagramm entfernt werden, um Platz für neue Daten zu schaffen. Die zeitliche Dauer dieses gleitenden Fensters kann über die Option „Zeitraum der Diagramme“ festgelegt werden.
 
## Zugreifen auf Echtzeitstatistiken

1. Klicken Sie auf dem Blatt „CDN-Profil“ auf die Schaltfläche **Verwalten**.

	![Schaltfläche „Verwalten“ auf dem Blatt „CDN-Profil“](./media/cdn-real-time-stats/cdn-manage-btn.png)
	
	Das CDN-Verwaltungsportal wird geöffnet.
	
2. Zeigen Sie auf die Registerkarte **Analysen** und dann auf das Flyout **Echtzeitstatistiken**. Klicken Sie auf **Große HTTP-Plattform**.
	
	Berichtsoptionen werden angezeigt.

## Bandbreite

Das Diagramm „Bandbreite“ zeigt die Menge der für die aktuelle Plattform verwendeten Bandbreite während eines angegebenen Zeitraums an. Der schattierte Teil des Diagramms zeigt die Bandbreitennutzung an. Die genaue Menge der Bandbreite, die gerade verwendet wird, wird direkt unter dem Liniendiagramm angezeigt.

> [AZURE.NOTE]Die Einheiten, in denen die Bandbreitennutzung gemeldet wird, sind die folgenden: Bit pro Sekunde (Bit/s), Kilobit pro Sekunde (Kbit/s), Megabit pro Sekunde (Mbit/s) oder Gigabit pro Sekunde (Gbit/s).

## Statuscodes

Das Diagramm „Statuscodes „ besteht aus farbcodierten Linien, die anzeigen, wie oft HTTP-Antwortcodes innerhalb eines bestimmten Zeitraums auftreten. Die linke Seite des Diagramms (y-Achse) gibt an, wie oft ein Statuscode für Anforderungen zurückgegeben wird, während der untere Teil des Diagramms (x-Achse) den Verlauf der Zeit angibt.

Eine Liste der Statuscodes wird direkt über dem Diagramm angezeigt. Diese Liste gibt jeden Statuscode an, der im Liniendiagramm enthalten sein kann, sowie die aktuelle Anzahl der Vorkommen pro Sekunde für den jeweiligen Statuscode. Standardmäßig wird für jeden Statuscode in dem Diagramm eine Linie angezeigt. Sie können jedoch auch nur die Statuscodes überwachen, die besonderen Bedeutung für Ihre CDN-Konfiguration haben. Dies erreichen Sie, indem Sie nur die gewünschten Statuscodeoptionen aktivieren und alle anderen Optionen deaktivieren. Nachdem Sie mit den Statuscodes zufrieden sind, die im Diagramm angezeigt werden, klicken Sie am besten auf „Diagramm aktualisieren“. Dies verhindert, dass die deaktivierten Statuscodes in das Diagramm übernommen werden.

> [AZURE.NOTE]Die Option **Diagramm aktualisieren** löscht das Diagramm. Danach werden nur die ausgewählten Statuscodes angezeigt.

Jede Statuscodeoption wird unten beschrieben.

Name | Beschreibung
-----|------------
Treffer gesamt pro Sekunde | Bestimmt, ob die Gesamtzahl der Anforderungen pro Sekunde für die aktuelle Plattform im Diagramm angezeigt wird. Sie können diese Option als Basisindikator verwenden, um den Prozentsatz der Treffergesamtzahl anzuzeigen, aus der ein bestimmter Statuscode besteht.
2xx pro Sekunde | Bestimmt, ob die Gesamtzahl der pro Sekunde für die aktuelle Plattform auftretenden 2xx-Statuscodes (z. B. 200, 201, 202 usw.) im Diagramm angezeigt wird. Dieser Typ von Statuscode gibt an, dass die Anforderung erfolgreich an den Client zugestellt wurde.
304 pro Sekunde | Bestimmt, ob die Gesamtzahl der pro Sekunde für die aktuelle Plattform auftretenden 304-Statuscodes im Diagramm angezeigt wird. Dieser Statuscode gibt an, dass das angeforderte Asset nicht geändert wurde, seit es das letzte Mal vom HTTP-Client abgerufen wurde.
3xx pro Sekunde | Bestimmt, ob die Gesamtzahl der pro Sekunde für die aktuelle Plattform auftretenden 3xx-Statuscodes (z. B. 300, 301, 302 usw.) im Diagramm angezeigt wird. Diese Option schließt Vorkommen von 304-Statuscodes aus. Dieser Typ von Statuscode gibt an, dass die Anforderung zu einer Umleitung geführt hat.
403 pro Sekunde | Bestimmt, ob die Gesamtzahl der pro Sekunde für die aktuelle Plattform auftretenden 403-Statuscodes im Diagramm angezeigt wird. Dieser Statuscode gibt an, dass die Anforderung als nicht autorisiert bewertet wurde. Eine mögliche Ursache für diesen Statuscode ist, wenn ein nicht autorisierter Benutzer ein Asset anfordert, das durch tokenbasierte Authentifizierung geschützt ist.
404 pro Sekunde | Bestimmt, ob die Gesamtzahl der pro Sekunde für die aktuelle Plattform auftretenden 404-Statuscodes im Diagramm angezeigt wird. Dieser Statuscode gibt an, dass das Asset nicht gefunden wurde.
4xx pro Sekunde | Bestimmt, ob die Gesamtzahl der pro Sekunde für die aktuelle Plattform auftretenden 4xx-Statuscodes (z. B. 400, 401, 402, 405 usw.) im Diagramm angezeigt wird. Diese Option schließt Vorkommen von 403- und 404-Statuscodes aus. Dieser Statuscode gibt an, dass das angeforderte Asset nicht an den Client zugestellt wurde.
5xx pro Sekunde | Bestimmt, ob die Gesamtzahl der pro Sekunde für die aktuelle Plattform auftretenden 5xx-Statuscodes (z. B. 500, 501, 502 usw.) im Diagramm angezeigt wird.
Sonstige pro Sekunde | Bestimmt, ob die Gesamtzahl der Vorkommen aller anderen Statuscodes im Diagramm angezeigt wird.

Sie können auch vorübergehend protokollierte Daten für einen bestimmten Statuscode ausblenden. Dies können Sie im Bereich direkt unterhalb des Diagramms, indem Sie die gewünschten Statuscodeoption deaktivieren. Der ausgewählte Statuscode wird sofort im Diagramm ausgeblendet. Durch das Aktivieren dieser Statuscodeoption wird die Option erneut angezeigt.

> [AZURE.NOTE]Die farbcodierten Optionen direkt unterhalb des Diagramms betreffen nur, was im Diagramm angezeigt wird. Sie wirken sich nicht darauf aus, ob das Diagramm diesen Statuscode verfolgt.

## Cachestatus

Das Diagramm „Cachestatus“ besteht aus farbcodierten Linien, die anzeigen, wie oft bestimmte Typen von Cachestatus innerhalb eines bestimmten Zeitraums auftreten. Die linke Seite des Diagramms (y-Achse) gibt an, wie oft ein Cachestatus für Anforderungen zurückgegeben wird, während der untere Teil des Diagramms (x-Achse) den Verlauf der Zeit angibt.

Eine Liste der Cachestatus wird direkt über dem Diagramm angezeigt. Diese Liste gibt jeden Cachestatus an, der im Liniendiagramm enthalten sein kann, sowie die aktuelle Anzahl der Vorkommen pro Sekunde für den jeweiligen Cachestatus. Standardmäßig wird für jeden Cachestatus in dem Diagramm eine Linie angezeigt. Sie können jedoch auch nur die Cachestatus überwachen, die besonderen Bedeutung für Ihre CDN-Konfiguration haben. Dies erreichen Sie, indem Sie nur die gewünschten Cachestatusoptionen aktivieren und alle anderen Optionen deaktivieren. Nachdem Sie mit den Cachestatus zufrieden sind, die im Diagramm angezeigt werden, klicken Sie am besten auf „Diagramm aktualisieren“. Dies verhindert, dass die deaktivierten Statuscodes in das Diagramm übernommen werden.

> [AZURE.NOTE]Die Option **Diagramm aktualisieren** löscht das Diagramm. Danach werden nur die ausgewählten Cachestatus angezeigt.

Sie können auch vorübergehend protokollierte Daten für einen bestimmten Cachestatus ausblenden. Dies können Sie im Bereich direkt unterhalb des Diagramms, indem Sie die gewünschten Antwortcodeoption deaktivieren. Der ausgewählte Antwortcode wird sofort im Diagramm ausgeblendet. Durch das Aktivieren dieser Antwortcodeoption wird die Option erneut angezeigt.

> [AZURE.NOTE]Die farbcodierten Optionen direkt unterhalb des Diagramms betreffen nur, was im Diagramm angezeigt wird. Sie wirken sich nicht darauf aus, ob das Diagramm diesen Statuscode verfolgt.

## Verbindungen

Diese grafische Darstellung der durchschnittlichen Anzahl von Verbindungen pro Minute ermöglicht Ihnen die Anzeige, wie viele Verbindungen mit Ihren Servern hergestellt wurden. Eine Verbindung besteht aus jeder Anforderung für ein Asset, die Ihr CDN passiert.

## Weitere Informationen
* [Übersicht über das Azure CDN](cdn-overview.md)
* [Überschreiben des HTTP-Standardverhaltens mithilfe des Regelmoduls](cdn-rules-engine.md)
* [Erweiterte HTTP-Berichte](cdn-advanced-http-reports.md)
* [Analysieren der Edgeleistung](cdn-edge-performance.md)

<!---HONumber=AcomDC_1203_2015-->