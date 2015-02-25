<properties pageTitle="Verwalten von Ursprüngen in einem Media Services-Konto" description="" services="media-services" documentationCenter="" authors="juliako" writer="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/29/2015" ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>Verwalten von Streaming-Endpunkten in einem Media Services-Konto

Mit Media Services können Sie mehrere Streaming-Endpunkte zu Ihrem Konto hinzufügen und die Streaming-Endpunkte konfigurieren. Jedes Media Services-Konto enthält mindestens einen Streaming-Endpunkt mit dem Namen **default**.

>[AZURE.NOTE] Streaming-Endpunkte wurden früher auch als Ursprung bezeichnet. 


## Hinzufügen und Löschen von Streaming-Endpunkten 

1. Klicken Sie im [Management Portal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendienstes.
2. Wählen Sie die Seite STREAMING-ENDPUNKTE aus. 
3. Klicken Sie unten auf der Seite auf ADD oder DELETE. Der Standard-Streaming-Endpunkt kann nicht gelöscht werden. 
4. Klicken Sie auf die Schaltfläche START, um den Streaming-Endpunkt zu starten. 
5. Klicken Sie auf den Namen des Streaming-Endpunkts, um diesen zu konfigurieren.   

	![Origin page][origin-page]

## Skalieren des Streaming-Endpunkts

Streaming-Einheiten stellen Ihnen dedizierte Ausgangskapazitäten bereit, die zu jeweils 200 Mbit/s erworben werden können, und  zusätzliche Funktionen, die aktuell [dynamische Paketfunktionen](http://go.microsoft.com/fwlink/?LinkId=276874) umfassen. Standardmäßig wird das bedarfsgesteuerte Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen (z. B. Rechen- und Ausgangskapazität usw.) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Durchsatz des bedarfsgesteuerten Streamings zu erhöhen, sollten Sie die Einheiten für Streaming kaufen. 

Um die Anzahl der Streaming-Einheiten zu ändern, gehen Sie folgendermaßen vor:

1. Um die Anzahl der Streaming-Einheiten anzugeben, wählen Sie die Registerkarte SKALIERUNG aus und verschieben anschließend den Schieberegler für die **reservierte Kapazität**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Die Zuordnung neuer Einheiten für das Streaming dauert ca. 20 Minuten. 

	 
	>[AZURE.NOTE] Aktuell kann das bedarfsgesteuerte Streaming für bis zu eine Stunde deaktiviert werden, wenn Sie einen positiven Wert für die Streaming-Einheiten zurück auf null setzen.

	>[AZURE.NOTE] Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet. Informationen zu den Preisen finden Sie unter den [Media Services-Preisangaben](http://go.microsoft.com/fwlink/?LinkId=275107).
	
## Konfigurieren des Streaming-Endpunkts

Mit der Registerkarte KONFIGURIEREN können Sie die Konfiguration wie in der Abbildung gezeigt vornehmen. Nachfolgend finden Sie die Beschreibung der Felder.

>[AZURE.NOTE] Die Konfiguration auf dieser Seite gilt nur für Streaming-Endpunkte mit mindestens einer reservierten Einheit. Reservierte Einheiten für bedarfsgesteuertes Streaming reservieren.

![Configure origin][configure-origin]
  

1. Einstellen der maximalen Zwischenspeicherungsdauer, die im cache control-Header der HTTP-Antworten angegeben wird. Dieser Wert überschreibt nicht den maximalen Zwischenspeicherungswert, der für den Blob-Inhalt ausdrücklich festgelegt wurde.

2. Angabe von IP-Adressen, die sich mit dem veröffentlichten Streaming-Endpunkt verbinden dürfen. Falls keine IP-Adressen angegeben sind, dürfen sich alle IP-Adressen verbinden.

3. Geben Sie die Konfiguration für die Header-Authentifizierung der Akamai-Signatur an.

4. Sie können eine domänenübergreifende Richtlinie für Adobe Flash-Clients angeben (weitere Informationen unter [Dateiangaben für domänenübergreifende Richtlinien](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). Außerdem auch Clientzugriffsrichtlinien für Microsoft Silverlight-Clients (weitere Informationen unter [Domänenübergreifendes Bereitstellen von Services](https://msdn.microsoft.com/de-de/library/cc197955(v=vs.95).aspx).  

5. Sie können auch benutzerdefinierte Hostnamen konfigurieren, indem Sie auf die Schaltfläche **configure** klicken. Weitere Informationen finden Sie unter der **CustomHostNames**-Eigenschaft unter [StreamingEndpoint](https://msdn.microsoft.com/de-de/library/dn783468.aspx).  




[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=42-->
