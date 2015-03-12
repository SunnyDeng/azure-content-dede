<properties 
	pageTitle="Verwalten von Streamingendpunkten in einem Media Services-Konto" 
	description="Diese Thema zeigt, wie Sie Streamingendpunkte im Azure-Verwaltungsportal verwalten." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>Verwalten von Streamingendpunkten in einem Media Services-Konto

Dieser Artikel gehört zur Reihe [Media Services: Video-on- Demand-Workflow](../media-services-video-on-demand-workflow) und [Media Services: Livestreaming-Workflow](../media-services-live-streaming-workflow).  


In Media Services stellt ein Streamingendpunkt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Client-Player-Anwendung oder einem Content Delivery Network (CDN) bereitstellen kann. Derzeit bietet Microsoft Azure Media Services keine nahtlose CDN-Integration, Sie können jedoch einen der marktüblichen CDN-Anbieter (Azure CDN oder Akamai) nutzen. Der ausgehende Stream des Streamingendpunkt-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein. 

Darüber hinaus können Sie die Kapazität des Streamingendpunkt-Diensts für die Verarbeitung wachsender Bandbreitenanforderungen steuern, indem Sie Skalierungseinheiten (auch bekannt als Streamingeinheiten)anpassen. Es wird empfohlen, für Anwendungen in der Produktionsumgebung eine oder mehrere Skalierungseinheiten zu reservieren. Skalierungseinheiten stellen Ihnen dedizierte Ausgangskapazitäten bereit, die zu jeweils 200 Mbit/s erworben werden können, und zusätzliche Funktionen, die aktuell dynamische Paketfunktionen umfassen. 

Diese Thema zeigt, wie Sie Streamingendpunkte im Azure-Verwaltungsportal verwalten.


## Hinzufügen und Löschen von Streamingendpunkten 

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendiensts.
2. Wählen Sie die Seite **STREAMINGENDPUNKTE** aus. 
3. Klicken Sie unten auf der Seite auf HINZUFÜGEN oder LÖSCHEN. Der Standard-Streamingendpunkt kann nicht gelöscht werden. 
4. Klicken Sie auf die Schaltfläche START, um den Streamingendpunkt zu starten. 
5. Klicken Sie auf den Namen des Streamingendpunkts, um diesen zu konfigurieren.   

	![Origin page][origin-page]

## <a id="scale_streaming_endpoints"></a>Skalieren des Streamingendpunkts

Streamingeinheiten stellen Ihnen dedizierte Ausgangskapazitäten bereit, die zu jeweils 200 Mbit/s erworben werden können, und zusätzliche Funktionen, die aktuell [dynamische Paketfunktionen](http://go.microsoft.com/fwlink/?LinkId=276874) umfassen. Standardmäßig wird das Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen (z. B. Rechen- und Ausgangskapazität usw.) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Streamingdurchsatz zu erhöhen, sollten Sie Streamingeinheiten kaufen. 

Um die Anzahl der Streamingeinheiten zu ändern, gehen Sie folgendermaßen vor:

1. Um die Anzahl der Streamingeinheiten anzugeben, wählen Sie die Registerkarte SKALIEREN aus, und verschieben Sie anschließend den Schieberegler für die **reservierte Kapazität**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Die Zuordnung neuer Einheiten für das Streaming dauert ca. 20 Minuten. 

	 
	>[AZURE.NOTE] Aktuell kann das bedarfsgesteuerte Streaming für bis zu eine Stunde deaktiviert werden, wenn Sie einen positiven Wert für die Streaming-Einheiten zurück auf null setzen.

	>[AZURE.NOTE] Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet. Informationen zu den Preisen finden Sie unter den [Media Services-Preisangaben](http://go.microsoft.com/fwlink/?LinkId=275107).
	
## Konfigurieren des Streamingendpunkts

Mit der Registerkarte KONFIGURIEREN können Sie die Konfiguration wie in der Abbildung gezeigt vornehmen. Nachfolgend finden Sie die Beschreibung der Felder.

>[AZURE.NOTE] Die Konfiguration auf dieser Seite gilt nur für Streaming-Endpunkte mit mindestens einer reservierten Einheit. Reservierte Einheiten für bedarfsgesteuertes Streaming reservieren.

![Configure origin][configure-origin]
  

1. Einstellen der maximalen Zwischenspeicherungsdauer, die im cache control-Header der HTTP-Antworten angegeben wird. Dieser Wert überschreibt nicht den maximalen Zwischenspeicherungswert, der für den Blob-Inhalt ausdrücklich festgelegt wurde.

2. Angabe von IP-Adressen, die sich mit dem veröffentlichten Streaming-Endpunkt verbinden dürfen. Falls keine IP-Adressen angegeben sind, dürfen sich alle IP-Adressen verbinden.

3. Geben Sie die Konfiguration für die Header-Authentifizierung der Akamai-Signatur an.

4. Sie können eine domänenübergreifende Richtlinie für Adobe Flash-Clients angeben (weitere Informationen unter [Dateiangaben für domänenübergreifende Richtlinien](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)). Außerdem auch Clientzugriffsrichtlinien für Microsoft Silverlight-Clients (weitere Informationen unter [Domänenübergreifendes Bereitstellen von Services](https://msdn.microsoft.com/de-de/library/cc197955(v=vs.95).aspx).  

5. Sie können auch benutzerdefinierte Hostnamen konfigurieren, indem Sie auf die Schaltfläche **Konfigurieren** klicken. Weitere Informationen finden Sie unter der **CustomHostNames**-Eigenschaft im Thema [StreamingEndpoint](https://msdn.microsoft.com/de-de/library/dn783468.aspx).  



[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=45--> 
