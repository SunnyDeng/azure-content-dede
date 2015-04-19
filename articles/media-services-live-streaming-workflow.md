<properties 
	pageTitle="Media Services: Livestreaming-Workflow" 
	description="In diesem Thema werden die Schritte eines typischen Media Services-Livestreaming-Workflows beschrieben." 
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
	ms.date="02/18/2015" 
	ms.author="juliako"/>


# Media Services: Livestreaming-Workflow

## Übersicht

In diesem Thema werden die Schritte eines typischen Azure Media Services (AMS)-Livestreaming-Workflows beschrieben. Jeder Schritt enthält Links zu relevanten Themen. Für Aufgaben, die mithilfe verschiedener Technologien erledigt werden können, stehen Schaltflächen zur Technologie Ihrer Wahl (z. B. .NET oder REST) zur Verfügung.   

Beachten Sie, dass Sie Media Services in Ihre vorhandenen Tools und Prozesse integrieren können. Codieren Sie Inhalte z. B. am Standort, und laden Sie sie danach zu Media Services hoch, um sie in mehreren Formaten zu transcodieren und über ein Azure-CDN oder ein Drittanbieter-CDN zu übermitteln. 

Das folgende Diagramm zeigt die Hauptbestandteile der Media Services-Plattform, die am Video on Demand-Workflow beteiligt sind.
![Live workflow][live-overview]


Weitere Informationen finden Sie unter [Media Services - Übersicht](media-services-overview.md).

## Erstellen eines Media Services-Kontos

Verwenden Sie das **Azure-Verwaltungsportal** zum [Erstellen eines Azure Media Services-Kontos](media-services-create-account.md).

## Konfigurieren von Streamingendpunkten

[In Bearbeitung]

## Einrichten der Entwicklungsumgebung  

Wählen Sie **.NET** oder **REST-API** für Ihre Entwicklungsumgebung.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## Programmgesteuerte Verbindung  

Wählen Sie **.NET** oder **REST-API**, um eine programmgesteuerte Verbindung mit Azure Media Services herzustellen.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

## Arbeiten mit Live-Transcodern

Weitere Informationen finden Sie unter [Verwenden von Drittanbieter-Liveencodern mit Azure Media Services](https://msdn.microsoft.com/library/azure/dn783464.aspx).

## Verwalten von Kanälen, Programmen und Medienobjekten

Weitere Informationen finden Sie unter [Livestreaming](https://msdn.microsoft.com/library/azure/dn783466.aspx).

## Konfigurieren von Inhaltsschutz und Autorisierungsrichtlinien für Inhaltsschlüssel 

Konfigurieren Sie die Autorisierungsrichtlinie für Schlüssel mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

## Konfigurieren von Übermittlungsrichtlinien für Medienobjekte

Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## Veröffentlichen von Medienobjekten

Veröffentlichen Sie Medienobjekte (durch Locator-Erstellung) mithilfe des **Azure-Verwaltungsportals** oder mit **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


## Skalieren eines Media Services-Kontos

Sie können **Media Services** skalieren, indem Sie die Anzahl der **reservierten Einheiten für das Streaming** angeben, die für Ihr Konto bereitgestellt werden sollen. 

Informationen zum Skalieren von Streamingeinheiten finden Sie unter: [Skalieren von Streamingeinheiten](media-services-manage-origins#scale_streaming_endpoints.md).



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
<!--HONumber=47-->
