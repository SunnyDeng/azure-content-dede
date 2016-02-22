<properties 
	pageTitle="Verwalten von Streamingendpunkten in einem Media Services-Konto" 
	description="Diese Thema zeigt, wie Sie Streamingendpunkte im klassischen Azure-Portal verwalten." 
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
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>Verwalten von Streamingendpunkten in einem Media Services-Konto

> [AZURE.SELECTOR]
- [Portal](media-services-manage-origins.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)


In Microsoft Azure Media Services stellt ein **Streamingendpunkt** einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Client-Player-Anwendung oder einem Content Delivery Network (CDN) bereitstellen kann. Media Services bietet auch eine nahtlose Integration von Azure CDN. Der ausgehende Stream des StreamingEndpoint-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein.

Darüber hinaus können Sie die Kapazität des Streamingendpunkt-Diensts für die Verarbeitung wachsender Bandbreitenanforderungen steuern, indem Sie Skalierungseinheiten (auch bekannt als Streamingeinheiten)anpassen. Es wird empfohlen, für Anwendungen in der Produktionsumgebung eine oder mehrere Skalierungseinheiten zu reservieren. Skalierungseinheiten stellen dedizierte Ausgangskapazitäten bereit, die in Schritten zu jeweils 200 Mbit/s erworben werden können, sowie zusätzliche Funktionen, die Folgendes umfassen: [dynamische Paketerstellung](media-services-dynamic-packaging-overview.md), CDN-Integration und erweiterte Konfiguration.

Beachten Sie, dass Ihnen nur Kosten entstehen, wenn Ihr StreamingEndpoint sich im Ausführungsmodus befindet.

Dieses Thema bietet einen Überblick über die wichtigsten Funktionen, die von Streamingendpunkten bereitgestellt werden. Es wird zudem erläutert, wie Streamingendpunkte über das klassische Azure-Portal verwaltet werden.


##Hinzufügen und Löschen von Streamingendpunkten

Sie können Streamingendpunkte mit dem .NET SDK, der REST-API oder dem klassischen Azure-Portal hinzufügen oder entfernen.

Führen Sie folgende Schritte aus, um mit dem klassischen Azure-Portal Streamingendpunkte hinzuzufügen oder zu löschen:

1. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Media Service
2. Wählen Sie die Seite **STREAMING-ENDPUNKTE** aus.
3. Klicken Sie unten auf der Seite auf HINZUFÜGEN oder LÖSCHEN. Der Standard-Streaming-Endpunkt kann nicht gelöscht werden.
4. Klicken Sie auf die Schaltfläche START, um den Streaming-Endpunkt zu starten.
5. Klicken Sie auf den Namen des Streamingendpunkts, um diesen zu konfigurieren.

![Streamingendpunkt-Seite][streaming-endpoint]


In der Standardeinstellung können Sie bis zu zwei Streamingendpunkte festlegen. Wenn Sie weitere anfordern möchten, finden Sie entsprechende Informationen unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

##<a id="scale_streaming_endpoints"></a>Skalieren des Streamingendpunkts

Streamingeinheiten stellen Ihnen dedizierte Ausgangskapazitäten bereit, die zu jeweils 200 Mbit/s erworben werden können, und zusätzliche Funktionen, die aktuell [dynamische Paketfunktionen](media-services-dynamic-packaging-overview.md) umfassen. Standardmäßig wird das Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen (z. B. Rechen- und Ausgangskapazität usw.) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Streamingdurchsatz zu erhöhen, sollten Sie Streamingeinheiten kaufen.

Die Skalierung können Sie mit dem .NET SDK, der REST-API oder dem klassischen Azure-Portal durchführen.

Um die Anzahl der Streamingeinheiten über das Portal zu ändern, gehen Sie folgendermaßen vor:

1. Um die Anzahl der Streaming-Einheiten anzugeben, wählen Sie die Registerkarte SKALIERUNG aus und verschieben anschließend den Schieberegler für die **reservierte Kapazität**.

	![Skalierungsseite](./media/media-services-manage-origins/media-services-origin-scale.png)

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

Sie können diese Eigenschaften mit dem .NET SDK, der REST-API oder dem klassischen Azure-Portal konfigurieren.

Um die Anzahl der Streamingeinheiten über das Portal zu ändern, gehen Sie folgendermaßen vor:

1. Wählen Sie den Streamingendpunkt aus, den Sie konfigurieren möchten.
1. Wählen Sie die Registerkarte "KONFIGURIEREN" aus.
  
Eine kurze Beschreibung der Felder folgt.

![Ursprung konfigurieren][configure-origin]
  

1. Einstellen der maximalen Zwischenspeicherungsdauer, die im cache control-Header der HTTP-Antworten angegeben wird. Dieser Wert überschreibt nicht den maximalen Zwischenspeicherungswert, der für den Blob-Inhalt ausdrücklich festgelegt wurde.

2. Angabe von IP-Adressen, die sich mit dem veröffentlichten Streaming-Endpunkt verbinden dürfen. Falls keine IP-Adressen angegeben sind, dürfen sich alle IP-Adressen verbinden.

3. Geben Sie die Konfiguration für die Header-Authentifizierung der Akamai-Signatur an.

4. Sie können eine domänenübergreifende Zugriffsrichtlinie für Adobe Flash-Clients angeben. Weitere Informationen finden Sie unter [Cross-domain policy file specification](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) ("Dateiangaben für domänenübergreifende Richtlinien", in englischer Sprache) Außerdem auch Clientzugriffsrichtlinien für Microsoft Silverlight-Clients. Weitere Informationen finden Sie unter [Domänenübergreifendes Bereitstellen von Services] (https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx)).

5. Sie können auch benutzerdefinierte Hostnamen konfigurieren, indem Sie auf die Schaltfläche **Konfigurieren** klicken. Weitere Informationen finden Sie unter der **CustomHostNames**-Eigenschaft im Thema [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).


##<a id="enable_cdn"></a>Aktivieren der Azure CDN-Integration

Sie können angeben, dass die Azure CDN-Integration für einen Streamingendpunkt aktiviert wird (in der Standardeinstellung ist sie deaktiviert).

So setzen Sie die Azure CDN-Integration auf "True":

- Der Streamingendpunkt muss über mindestens eine Streamingeinheit (Skalierungseinheit) verfügen. Wenn Sie später die Skalierungseinheiten auf "0" festlegen möchten, müssen Sie zuerst die CDN-Integration deaktivieren. Beim Erstellen eines neuen Streamingendpunkts wird standardmäßig eine Streamingeinheit automatisch festgelegt.

- Der Streamingendpunkt muss sich im angehaltenen Status befinden. Nach der Aktivierung der CDN-Integration können Sie den Streamingendpunkt starten.

Die Aktivierung der Azure CDN-Integration kann bis zu 90 Minuten dauern. Es dauert bis zu zwei Stunden, bis die Änderungen in allen CDN-POPs aktiv sind.


Die CDN-Integration ist in allen Azure-Rechenzentren aktiviert: USA, Westen; USA, Osten; Nordeuropa; Westeuropa; Japan West; Japan Ost; Südostasien und Ostasien.

Nach der Aktivierung werden die folgenden Konfigurationseinstellungen deaktiviert: **Benutzerdefinierte Hostnamen** und **Access Control**.

![Streamingendpunkt – CDN-Aktivierung][streaming-endpoint-enable-cdn]


###Zusätzliche Überlegungen

- Wenn CDN für einen Streamingendpunkt aktiviert ist, können die Clients keine Inhalte direkt vom Ursprung anfordern. Wenn Sie Ihre Inhalte mit oder ohne CDN testen können möchten, können Sie einen anderen Streamingendpunkt erstellen, für den kein CDN aktiviert ist.
- Der Hostname für den Streamingendpunkt bleibt nach dem Aktivieren von CDN gleich. Sie müssen keine Änderungen an Ihrem Media Services-Workflow vornehmen, nachdem CDN aktiviert wurde. Wenn der Hostname des Streamingendpunkts z. B. "strasbourg.streaming.mediaservices.windows.net" lautet, wird nach der Aktivierung von CDN genau der gleiche Hostname verwendet.
- Für neue Streamingendpunkte können Sie CDN einfach aktivieren, indem Sie einen neuen Endpunkt erstellen. Für vorhandene Streamingendpunkte müssen Sie zuerst den Endpunkt beenden und dann das CDN aktivieren.
 

Weitere Informationen finden Sie unter [Ankündigung der Azure Media Services-Integration in Azure CDN (Content Delivery Network)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png
 

<!---HONumber=AcomDC_0211_2016-->