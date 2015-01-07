<properties urlDisplayName="index" pageTitle="Verwalten von Ursprüngen in einem Media Services-Konto" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Streaming Endpoints in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


<h1><a id="managemediaservicesorigins"></a>Verwalten von Streaming-Endpunkten in einem Media Services-Konto</h1>

Mit Media Services können Sie mehrere Streaming-Endpunkte zu Ihrem Konto hinzufügen und die Streaming-Endpunkte konfigurieren. Jedes Media Services-Konto enthält mindestens einen Streaming-Endpunkt mit dem Namen **default**.

>[WACOM.NOTE] Streaming-Endpunkte wurden früher auch als Ursprung bezeichnet. 


<h2>Hinzufügen und Löschen von Streaming-Endpunkten</h2> 

1. Im [Verwaltungsportal](https://manage.windowsazure.com/) klicken Sie auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendienstes.
2. Wählen Sie die Seite STREAMING-ENDPUNKTE aus. 
3. Klicken Sie unten auf der Seite auf ADD oder DELETE. Der Standard-Streaming-Endpunkt kann nicht gelöscht werden. 
4. Klicken Sie auf die Schaltfläche START, um den Streaming-Endpunkt zu starten. 
5. Klicken Sie auf den Namen des Streaming-Endpunkts, um diesen zu konfigurieren.   

	![Origin page][origin-page]

<h2>Konfigurieren des Streaming-Endpunkts</h2>

In der Registerkarte CONFIGURE können Sie die folgenden Konfigurationen vornehmen:

1. Einstellen der maximalen Zwischenspeicherungsdauer, die im cache control-Header der HTTP-Antworten angegeben wird. Dieser Wert überschreibt nicht den maximalen Zwischenspeicherungswert, der für den Blob-Inhalt ausdrücklich festgelegt wurde.

2. Angabe von IP-Adressen, die sich mit dem veröffentlichten Streaming-Endpunkt verbinden dürfen. Falls keine IP-Adressen angegeben sind, dürfen sich alle IP-Adressen verbinden.

3. Geben Sie die Konfiguration für die Header-Authentifizierung der Akamai-Signatur an.



	![Configure origin][configure-origin]

	Die Konfiguration auf dieser Seite gilt nur für Streaming-Endpunkte mit mindestens einer reservierten Einheit. Wechseln Sie zur Reservieren SCALE, um die reservierten Einheiten für bedarfsgesteuertes Streaming zu reservieren. Detaillierte Informationen zu reservierten Einheiten finden Sie unter [Skalieren von Media Services](../media-services-how-to-scale/).


[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png

<!--HONumber=35.1-->
