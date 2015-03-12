<properties 
	pageTitle="Verschlüsseln von Medienobjekten in Media Services - Azure" 
	description="Erfahren Sie, wie Sie Medieninhalte in Media Services mit dem Microsoft PlayReady-Schutz verschlüsseln können. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Vorgehensweise: Dynamisches Verschlüsseln eines Medienobjekts mit PlayReady oder AES-128

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](../media-services-video-on-demand-workflow) and [Media Services Live Streaming workflow](../media-services-live-streaming-workflow) .
  
## Übersicht

Mit Microsoft Azure Media Services können Sie Inhalte (dynamisch) verschlüsselt übermitteln, und zwar mit AES (Advanced Encryption Standard unter Verwendung eines 128-Bit-Verschlüsselungsschlüssels) und PlayReady-DRM. Media Services umfasst auch einen Dienst für die Übermittlung von Schlüsseln und PlayReady-Lizenzen an autorisierte Clients. Zum Übermitteln geschützten Inhalts müssen Sie die Autorisierungsrichtlinie für Inhaltsschlüssel und die  Übermittlungsrichtlinie für Medienobjekte konfigurieren.

## Konfigurieren

Informationen zum Konfigurieren von Autorisierungsrichtlinien für Inhaltsschlüssel 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Konfigurieren von Übermittlungsrichtlinien für Medienobjekte
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 
<!--HONumber=45--> 
