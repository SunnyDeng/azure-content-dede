<properties urlDisplayName="index" pageTitle="Gewusst wie: Verwalten von Urspr&uuml;ngen in einem Mediendienstekonto" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Verwalten von Streaming-Endpunkten in einem Media Services-Konto" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>Verwalten von Streaming-Endpunkten in einem Media Services-Konto

Mit Media Services können Sie mehrere Streaming-Endpunkte zu Ihrem Konto hinzufügen und die Streaming-Endpunkte konfigurieren. Jedes Media Services-Konto enthält mindestens einen Streaming-Endpunkt mit dem Namen **default**.

> [WACOM.NOTE] Streaming-Endpunkte wurden früher auch als Ursprung bezeichnet.

## Hinzufügen und Löschen von Streaming-Endpunkten

1.  Klicken Sie im [Management Portal][Management Portal] auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendienstes.
2.  Wählen Sie die Seite STREAMING-ENDPUNKTE aus.
3.  Klicken Sie unten auf der Seite auf ADD oder DELETE. Der Standard-Streaming-Endpunkt kann nicht gelöscht werden.
4.  Klicken Sie auf die Schaltfläche START, um den Streaming-Endpunkt zu starten.
5.  Klicken Sie auf den Namen des Streaming-Endpunkts, um diesen zu konfigurieren.

    ![Ursprungsseite][Ursprungsseite]

## Konfigurieren des Streaming-Endpunkts

In der Registerkarte CONFIGURE können Sie die folgenden Konfigurationen vornehmen:

1.  Einstellen der maximalen Zwischenspeicherungsdauer, die im cache control-Header der HTTP-Antworten angegeben wird. Dieser Wert überschreibt nicht den maximalen Zwischenspeicherungswert, der für den Blob-Inhalt ausdrücklich festgelegt wurde.

2.  Angabe von IP-Adressen, die sich mit dem veröffentlichten Streaming-Endpunkt verbinden dürfen. Falls keine IP-Adressen angegeben sind, dürfen sich alle IP-Adressen verbinden.

3.  Geben Sie die Konfiguration für die Header-Authentifizierung der Akamai-Signatur an.

    ![Ursprung konfigurieren][Ursprung konfigurieren]

    Die Konfiguration auf dieser Seite gilt nur für Streaming-Endpunkte mit mindestens einer reservierten Einheit. Wechseln Sie zur Reservieren SCALE, um die reservierten Einheiten für bedarfsgesteuertes Streaming zu reservieren. Detaillierte Informationen zu reservierten Einheiten finden Sie unter [Skalieren von Mediendiensten][Skalieren von Mediendiensten].

  [Management Portal]: https://manage.windowsazure.com/
  [Ursprungsseite]: ./media/media-services-manage-origins/media-services-origins-page.png
  [Ursprung konfigurieren]: ./media/media-services-manage-origins/media-services-origins-configure.png
  [Skalieren von Mediendiensten]: ../media-services-how-to-scale/
