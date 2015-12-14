<properties 
	pageTitle="CDN – Verbessern der Leistung durch Komprimieren von Dateien" 
	description="Sie können die Geschwindigkeit von Dateiübertragungen erhöhen und die Leistung beim Laden von Seiten verbessern, indem Sie Ihre Dateien komprimieren." 
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

# Verbessern der Leistung durch Komprimieren von Dateien

In diesem Thema wird erläutert, wie Sie die Geschwindigkeit von Dateiübertragungen erhöhen und die Leistung beim Laden von Seiten verbessern, indem Sie Ihre Dateien komprimieren.

CDN bietet zwei Möglichkeiten zur Unterstützung der Komprimierung:

- Sie können die Komprimierung auf Ihrem Ursprungsserver aktivieren. In diesem Fall unterstützt CDN die Komprimierung standardmäßig und sendet komprimierte Dateien an Clients. 
- Sie können die Komprimierung direkt auf CDN-Edgeservern aktivieren. In diesem Fall komprimiert CDN die Dateien und sendet sie an die Endbenutzer.

## Aktivieren der Komprimierung

> [AZURE.NOTE]Die CDN-Tarife „Standard“ und „Premium“ bieten die gleiche Komprimierungsmethode, jedoch mit einer anderen Benutzeroberfläche. Weitere Informationen zu den Unterschieden zwischen den CDN-Tarifen „Standard“ und „Premium“ finden Sie unter [Übersicht über Azure CDN](cdn-overview.md).

### Standard-Ebene

1. Klicken Sie auf dem Blatt „CDN-Profil“ auf den CDN-Endpunkt, den Sie verwalten möchten.
	
	![Blade-Endpunkte für CDN-Profile](./media/cdn-file-compression/cdn-endpoints.png)

	Das Blatt „CDN-Endpunkt“ öffnet sich.

2. Klicken Sie auf die Schaltfläche **Konfigurieren**.

	![Schaltfläche „Verwalten“ auf dem CDN-Profilblatt](./media/cdn-file-compression/cdn-config-btn.png)
	
	Es wird das Blatt „CDN-Konfiguration“ geöffnet.
	
3. Schalten Sie **Komprimierung** an.
	
	![CDN-Komprimierungsoptionen](./media/cdn-file-compression/cdn-compress-standard.png)
	
4. Verwenden Sie die Standardtypen, oder ändern Sie die Liste durch Entfernen oder Hinzufügen von Dateitypen.

5. Klicken Sie auf die Schaltfläche **Speichern**, wenn Sie die gewünschten Änderungen vorgenommen haben.

### Premium-Ebene

1. Klicken Sie auf dem Blatt „CDN-Profil“ auf die Schaltfläche **Verwalten**.

	![Schaltfläche „Verwalten“ auf dem CDN-Profilblatt](./media/cdn-file-compression/cdn-manage-btn.png)
	
	Das CDN-Verwaltungsportal wird geöffnet.
	
2. Zeigen Sie auf die Registerkarte **HTTP Groß** und dann auf das Flyout **Cacheeinstellungen**. Klicken Sie auf **Komprimierung**.
	
	Es werden die Kompressionsoptionen angezeigt.
	
	![Dateikomprimierung](./media/cdn-file-compression/cdn-compress-files.png)
	
3. Nach dem Ändern der Liste mit den Dateitypen, klicken Sie auf die Schaltfläche **Update**.


## Komprimierungsprozess und -regeln

1. Die anfordernde Person sendet eine Inhaltsanforderung.
2. Ein Edgeserver prüft, ob ein **Accept-Encoding**-Header vorhanden ist.
	1. Sofern vorhanden, gibt dieser Header die angeforderte Komprimierungsmethode an.
	1. Fehlt der Header, wird die Anforderung in einem nicht komprimierten Format verarbeitet.
3.	Der nächstgelegene Edge-POP überprüft den Cachestatus sowie die Komprimierungsmethode und prüft, ob noch eine entsprechende Gültigkeitsdauer vorliegt.
	1.	Cachefehler: Wenn die angeforderte Version nicht zwischengespeichert ist, wird die Anforderung an den Ursprungsserver weitergeleitet.
	2.	Cachetreffer mit derselben Komprimierungsmethode: Der Edgeserver sendet die komprimierten Inhalte sofort an den Client.
	3.	Cachetreffer mit anderer Komprimierungsmethode: Der Edgeserver transcodiert die Ressource in die angeforderte Komprimierungsmethode. 
	4.	Cachetreffer mit nicht komprimierten Inhalten: Wenn die anfängliche Anforderung dazu geführt hat, dass die Ressource in einem nicht komprimierten Format zwischengespeichert wurde, wird geprüft, ob für die Anforderung eine Edgeserverkomprimierung durchgeführt werden kann (die Prüfung erfolgt basierend auf den Kriterien im obigen Abschnitt zu Definition/Anforderungen).
		1.	Sofern möglich, komprimiert der Edgeserver die Datei und sendet sie an den Client.
		2.	Ist eine Komprimierung nicht möglich, sendet der Edgeserver den nicht komprimierten Inhalt sofort an den Client. 



## Überlegungen 

1. Für Media Services CDN-aktivierte Streamingendpunkte ist die Komprimierung für die folgenden Inhaltstypen standardmäßig aktiviert: application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Sie können die Komprimierung für die genannten Typen im Azure-Portal nicht aktivieren/deaktivieren.  
2. Auf dem Edgeserver wird nur eine Dateiversion (komprimiert oder nicht komprimiert) zwischengespeichert. Die Anforderung einer anderen Dateiversion führt dazu, dass der Inhalt vom Edgeserver transcodiert wird.  

<!---HONumber=AcomDC_1203_2015-->