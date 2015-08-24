<properties 
	pageTitle="Verwenden von CDN | Microsoft Azure" 
	description="Erfahren Sie, wie das Azure Content Delivery Network (CDN) für die Übermittlung breitbandiger Inhalte eingesetzt wird, indem Blobs und statische Inhalte zwischengespeichert werden." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/19/2015" 
	ms.author="mazha"/>


# Verwenden von CDN für Azure

Azure Content Delivery Network (CDN) ist der grundlegende Baustein für die Skalierung beliebiger HTTP-Anwendungen in Azure. Es bietet Azure-Kunden eine globale Lösung für das Zwischenspeichern und Bereitstellen von Inhalten in geografischer Nähe zu den Endbenutzern. So müssen Benutzeranforderungen nicht jedes Mal an den ursprünglichen Inhaltsspeicherort gesendet werden, sondern werden intelligent an den CDN-Edge-POP mit der besten Leistung geroutet. Dies führt zu einer erheblichen Verbesserung von Leistung und Benutzerfreundlichkeit. Eine aktuelle Liste von CDN-Knotenstandorten finden Sie unter [Standorte der Azure CDN-Knoten].

Die Verwendung von CDN zum Zwischenspeichern von Azure-Daten bietet folgende Vorteile:

-   Bessere Leistung und Benutzerfreundlichkeit für Endbenutzer, die von einer Inhaltsquelle weit entfernt sind und Anwendungen verwenden, die viele Verbindungen im Internet benötigen, um Inhalte zu laden.
-   Große verteilte Skalierung, um hohe Lasten etwa zu Beginn eines Ereignisses, z. B. bei einer Produkteinführung, besser verarbeiten zu können
-   Durch das Verteilen der Benutzeranforderungen und die Verarbeitung von Inhalten durch globale Edge-POPs entfällt weniger Datenverkehr auf den ursprünglichen Inhaltsspeicherort, d. h. dieser wird entlastet.

Aktuelle CDN-Kunden können das Azure CDN jetzt im [Azure-Verwaltungsportal] verwenden. Das CDN ist ein Add-On-Feature Ihres Abonnements und hat einen eigenen [Abrechnungsplan].

##Schritt 1: Erstellen eines CDN-Ursprungs in Azure

Der CDN-Ursprung ist der Speicherort, von dem CDN Inhalte abruft, um sie auf den Edge-POPs zwischenzuspeichern. Der integrierte Azure-Ursprung umfasst Azure Apps, Cloud Services, Storage und Media Services.

## Schritt 2: Erstellen eines CDN-Endpunkts mit Verweis auf Ihren Azure-Ursprung

Nachdem der Ursprung eingerichtet ist, steht dieser in der Ursprungsliste zur Verfügung, wenn Sie [einen neuen CDN-Endpunkt erstellen](cdn-create-new-endpoint.md).

> [AZURE.NOTE]Die für den Endpunkt erstellte Konfiguration ist nicht sofort verfügbar. Die Verteilung der Registrierung über das CDN-Netzwerk kann bis zu 60 Minuten dauern. Benutzer, die den CDN-Domänennamen sofort zu verwenden versuchen, sehen u. U. den Statuscode 400 (Unzulässige Anforderung), bis die Inhalte über das CDN verfügbar sind.

## Schritt 3: Einrichten Ihrer CDN-Konfiguration 

Sie können verschiedene Features für Ihren CDN-Endpunkt aktivieren, darunter beispielsweise eine Richtlinie für die Zwischenspeicherung, das Zwischenspeichern von Abfragezeichenfolgen usw.

## Schritt 4: Zugriff auf CDN-Inhalte

Verwenden Sie für den Zugriff auf die im CDN zwischengespeicherten Inhalte den im Portal bereitgestellten CDN-URL. Beispielsweise ähnelt die Adresse für einen zwischengespeicherten Blob der folgenden: http://<*CDNNamespace*>.vo.msecnd.net/<*myPublicContainer*>/<*BlobName*>



## Weitere Informationen

[Übersicht über das Azure Content Delivery Network (CDN)](cdn-overview.md)
 

<!---HONumber=August15_HO7-->