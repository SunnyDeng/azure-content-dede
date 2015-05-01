<properties 
	pageTitle="Verwalten von Streamingendpunkten in einem Media Services-Konto" 
	description="Diese Thema zeigt, wie Sie Streamingendpunkte im Azure-Verwaltungsportal verwalten." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2015" 
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>Verwalten von Streamingendpunkten in einem Media Services-Konto

Dieser Artikel gehört zur Reihe [Media Services: Video-on- Demand-Workflow](media-services-video-on-demand-workflow.md) und [Media Services: Livestreaming-Workflow](media-services-live-streaming-workflow.md).  


In Microsoft Azure Media Services stellt ein **Streamingendpunkt** einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Client-Player-Anwendung oder einem Netzwerk für die Inhaltsübermittlung (CDN) bereitstellen kann. Media Services bietet auch eine nahtlose Integration von Azure CDN. Der ausgehende Stream des StreamingEndpoint-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein.  

Darüber hinaus können Sie die Kapazität des Streamingendpunkt-Diensts für die Verarbeitung wachsender Bandbreitenanforderungen steuern, indem Sie Skalierungseinheiten (auch bekannt als Streamingeinheiten)anpassen. Es wird empfohlen, für Anwendungen in der Produktionsumgebung eine oder mehrere Skalierungseinheiten zu reservieren. Skalierungseinheiten stellen dedizierte Ausgangskapazitäten bereit, die in Schritten zu jeweils 200 Mbit/s erworben werden können, sowie zusätzliche Funktionen, die Folgendes umfassen: [dynamische Paketerstellung](https://msdn.microsoft.com/library/azure/jj889436.aspx), CDN-Integration und erweiterte Konfiguration.

Beachten Sie, dass Ihnen nur Kosten entstehen, wenn Ihr StreamingEndpoint sich im Ausführungsmodus befindet. 

Dieses Thema bietet einen Überblick über die wichtigsten Funktionen, die von Streamingendpunkten bereitgestellt werden. Es wird zudem erläutert, wie Streamingendpunkte über das Azure-Verwaltungsportal verwaltet werden.


##Hinzufügen und Löschen von Streamingendpunkten 

Sie können Streamingendpunkte mit dem .NET SDK, der REST-API oder dem Azure-Verwaltungsportal hinzufügen oder entfernen.

Führen Sie folgende Schritte aus, um Streamingendpunkte hinzuzufügen oder zu löschen:

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendiensts.
2. Wählen Sie die Seite **STREAMINGENDPUNKTE** aus. 
3. Klicken Sie unten auf der Seite auf HINZUFÜGEN oder LÖSCHEN. Der Standard-Streamingendpunkt kann nicht gelöscht werden. 
4. Klicken Sie auf die Schaltfläche START, um den Streamingendpunkt zu starten. 
5. Klicken Sie auf den Namen des Streamingendpunkts, um diesen zu konfigurieren.   

	![Seite "Streamingendpunkte"][streaming-endpoint]


In der Standardeinstellung können Sie bis zu zwei Streamingendpunkte festlegen. Wenn Sie weitere anfordern möchten, finden Sie entsprechende Informationen unter [Quotas and limitations](media-services-quotas-and-limitations/) (in englischer Sprache).

##<a id="scale_streaming_endpoints"></a>Skalieren des Streamingendpunkts

Streamingeinheiten stellen Ihnen dedizierte Ausgangskapazitäten bereit, die zu jeweils 200 Mbit/s erworben werden können, und zusätzliche Funktionen, die aktuell [dynamische Paketfunktionen](http://go.microsoft.com/fwlink/?LinkId=276874) umfassen. Standardmäßig wird das Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen (z. B. Rechen- und Ausgangskapazität usw.) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Streamingdurchsatz zu erhöhen, sollten Sie Streamingeinheiten kaufen. 

Die Skalierung können Sie mit dem .NET SDK, der REST-API oder dem Azure-Verwaltungsportal durchführen.

Um die Anzahl der Streamingeinheiten über das Portal zu ändern, gehen Sie folgendermaßen vor:

1. Um die Anzahl der Streamingeinheiten anzugeben, wählen Sie die Registerkarte "SKALIEREN" aus, und verschieben Sie anschließend den Schieberegler für die **reservierte Kapazität**.

	![Seite "Skalieren"](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Die Zuordnung neuer Einheiten für das Streaming dauert ca. 20 Minuten. 

	 
	>[AZURE.NOTE] Aktuell kann das bedarfsgesteuerte Streaming für bis zu eine Stunde deaktiviert werden, wenn Sie einen positiven Wert für die Streaming-Einheiten zurück auf null setzen.

	>[AZURE.NOTE] Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet. Informationen zu den Preisen finden Sie unter [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107).
	
##<a id="configure_streaming_endpoints"></a>Konfigurieren des Streamingendpunkts

Mit einem Streamingendpunkt können Sie die folgenden Eigenschaften konfigurieren, wenn Sie über mindestens eine Skalierungseinheit verfügen: 

- Zugriffssteuerung
- Benutzerdefinierte Hostnamen
- Cachesteuerelement
- Websiteübergreifende Zugriffsrichtlinien

Ausführliche Informationen zu diesen Eigenschaften finden Sie unter [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Sie können diese Eigenschaften mit dem .NET SDK, der REST-API oder dem Azure-Verwaltungsportal konfigurieren.

Um die Anzahl der Streamingeinheiten über das Portal zu ändern, gehen Sie folgendermaßen vor:

1. Wählen Sie den Streamingendpunkt aus, den Sie konfigurieren möchten.
1. Wählen Sie die Registerkarte "KONFIGURIEREN" aus.
  
Eine kurze Beschreibung der Felder folgt.

![Ursprung konfigurieren][configure-origin]
  

1. Einstellen der maximalen Zwischenspeicherungsdauer, die im cache control-Header der HTTP-Antworten angegeben wird. Dieser Wert überschreibt nicht den maximalen Zwischenspeicherungswert, der für den Blob-Inhalt ausdrücklich festgelegt wurde.

2. Angabe von IP-Adressen, die sich mit dem veröffentlichten Streaming-Endpunkt verbinden dürfen. Falls keine IP-Adressen angegeben sind, dürfen sich alle IP-Adressen verbinden.

3. Geben Sie die Konfiguration für die Header-Authentifizierung der Akamai-Signatur an.

4. Sie können eine domänenübergreifende Zugriffsrichtlinie für Adobe Flash-Clients angeben. (Weitere Informationen finden Sie unter [Dateiangaben für domänenübergreifende Richtlinien](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).) Außerdem auch Clientzugriffsrichtlinien für Microsoft Silverlight-Clients. (Weitere Informationen finden Sie unter [Domänenübergreifendes Bereitstellen von Services](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx.)  

5. Sie können auch benutzerdefinierte Hostnamen konfigurieren, indem Sie auf die Schaltfläche **Konfigurieren** klicken. Weitere Informationen finden Sie unter der **CustomHostNames**-Eigenschaft im Thema [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).  


##<a id="enable_cdn"></a>Aktivieren der Azure CDN-Integration

Sie können angeben, dass die Azure CDN-Integration für einen Streamingendpunkt aktiviert wird (in der Standardeinstellung ist sie deaktiviert).

So setzen Sie die Azure CDN-Integration auf "True":

- Der Streamingendpunkt muss über mindestens eine Streamingeinheit (Skalierungseinheit) verfügen. Wenn Sie später die Skalierungseinheiten auf "0" festlegen möchten, müssen Sie zuerst die CDN-Integration deaktivieren. 

- Der Streamingendpunkt muss sich im angehaltenen Status befinden. Nach der Aktivierung der CDN-Integration können Sie den Streamingendpunkt starten. 

Die Aktivierung der Azure CDN-Integration kann bis zu 90 Minuten dauern.   

Wenn sie aktiviert ist, werden die folgenden Konfigurationseinstellungen deaktiviert: **Benutzerdefinierte Hostnamen** und **Zugriffssteuerung**.

Nicht alle Data Center unterstützen die Azure CDN-Integration. Bei Verwendung des Azure-Verwaltungsportals wird die Option **ENABLE CDN** angezeigt, wenn sie in Ihrem Data Center verfügbar ist. 

![Streamingendpunkt, "Enable CDN"][streaming-endpoint-enable-cdn]

Wenn Sie das .NET SDK oder die REST-API verwenden, müssen Sie überprüfen, ob die Azure CDN-Integration in Ihrem Data Center verfügbar ist. 

Gehen Sie dazu wie folgt vor:

1. Versuchen Sie, "CdnEnabled" auf "True" zu setzen.
1. Überprüfen Sie das zurückgegebene Ergebnis auf den HTTP-Fehlercode 412 (PreconditionFailed) mit der Meldung "Streaming endpoint CdnEnabled property cannot be set to true as CDN capability is not available in the current region". 

	Wenn dieser Fehler angezeigt wird, unterstützt das Data Center die CDN-Integration nicht. Versuchen Sie es mit einem anderen Data Center.


[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png


<!--HONumber=52-->