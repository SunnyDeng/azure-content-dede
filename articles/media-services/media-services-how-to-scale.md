<properties 
	pageTitle="Skalieren eines Media Service" 
	description="Erfahren Sie, wie Sie Media Services durch Angabe der Anzahl von On-Demand Streaming Reserved Units und Encoding Reserved Units für Ihr Konto skalieren können." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2015"   
	ms.author="juliako"/>


#Skalieren eines Media Service  

##Übersicht

Sie können **Media Services** skalieren, indem Sie die Anzahl **reservierter Einheiten für das Streaming** und die Anzahl **reservierter Einheiten für die Codierung** angeben, die für Ihr Konto bereitgestellt werden soll.

Außerdem können Sie Ihr Media Services-Konto skalieren, indem Sie Speicherkonten hinzufügen. Jedes Speicherkonto ist auf 500 TB beschränkt. Um den Speicher über die Standardbeschränkungen hinaus zu erweitern, können Sie mehrere Speicherkonten mit einem einzelnen Media Service-Konto verknüpfen.

Dieses Thema enthält Links zu relevanten Themen.

##<a id="streaming_endpoins"></a>Reservierte Einheiten für das Streaming

Weitere Informationen finden Sie unter [Skalieren von Streamingeinheiten](media-services-manage-origins.md#scale_streaming_endpoints).

##<a id="encoding_reserved_units"></a>Reservierte Einheiten für die Codierung

Informationen zum Skalieren von Codierungseinheiten finden Sie nachfolgend unter den Themen **Portal** und **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

Beachten Sie, dass die reservierten Einheiten für Codierungs- und Indizierungsaufgaben identisch sind.

##<a id="storage"></a>Skalieren des Speichers

Weitere Informationen finden Sie unter [Verwalten von Media Services-Medienobjekte über mehrere Speicherkonten](https://msdn.microsoft.com/library/azure/dn271889.aspx) und [Arbeiten mit Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx).



##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1210_2015-->