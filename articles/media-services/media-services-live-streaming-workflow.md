<properties 
	pageTitle="Bereitstellen von Livestreaming mit Azure Media Services" 
	description="In diesem Thema werden die Schritte eines typischen Media Services-Livestreaming-Workflows beschrieben." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
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


#Bereitstellen von Livestreaming mit Azure Media Services

##Übersicht

In diesem Thema werden die Schritte eines typischen Azure Media Services (AMS)-Livestreaming-Workflows beschrieben. Jeder Schritt enthält Links zu relevanten Themen. Für Aufgaben, die mithilfe verschiedener Technologien erledigt werden können, stehen Schaltflächen zur Technologie Ihrer Wahl (z. B. .NET oder REST) zur Verfügung.   

Beachten Sie, dass Sie Media Services in Ihre vorhandenen Tools und Prozesse integrieren können. Codieren Sie Inhalte z. B. am Standort, und laden Sie sie danach zu Media Services hoch, um sie in mehreren Formaten zu transcodieren und über ein Azure-CDN oder ein Drittanbieter-CDN zu übermitteln. 

Das folgende Diagramm zeigt die Hauptbestandteile der Media Services-Plattform, die am Livestreaming-Workflow beteiligt sind.

![Live-Workflow][live-overview]

In diesem Thema werden die Konzepte im Zusammenhang mit Livestreaming beschrieben und Links zu Themen mit Anleitungen zur Durchführung von Livestreaming-Aufgaben bereitgestellt.

##Konzepte

Konzepte im Zusammenhang mit Livestreaming finden Sie unter [Media Services - Konzepte](media-services-concepts.md).

##Erstellen eines Media Services-Kontos

Verwenden Sie das **Azure-Verwaltungsportal** zum [Erstellen eines Azure Media Services-Kontos](media-services-create-account.md).

##Konfigurieren von Streamingendpunkten

Eine Übersicht über Streamingendpunkte sowie Informationen zu deren Verwaltung finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md)

##Einrichten der Entwicklungsumgebung  

Wählen Sie **.NET** oder **REST-API** für Ihre Entwicklungsumgebung.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

##Programmgesteuertes Verbinden  

Wählen Sie **.NET** oder **REST-API**, um eine programmgesteuerte Verbindung mit Azure Media Services herzustellen.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


##Verwenden lokaler Live-Encoder für die Ausgabe von Streams mit variabler Bitrate an einen Kanal

##Arbeiten mit Live-Transcodern von Drittanbietern

Weitere Informationen finden Sie unter [Verwenden von Drittanbieter-Liveencodern mit Azure Media Services](https://msdn.microsoft.com/library/azure/dn783464.aspx).

##Verwalten von Kanälen, Programmen und Medienobjekten

**Übersicht**: [Verwalten von Kanälen und Programmen - Übersicht](media-services-manage-channels-overview.md).

Wählen Sie **Portal**, **.NET** oder **REST-API**, um Beispiele anzuzeigen.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

##Konfigurieren von Übermittlungsrichtlinien für Medienobjekte

Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##Erstellen von Inhaltsschlüsseln

Erstellen Sie einen Inhaltsschlüssel für das Verschlüsseln Ihres Medienobjekts mit **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

##Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel 

Konfigurieren Sie Inhaltsschutz und Autorisierungsrichtlinien für Inhaltsschlüssel mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##Veröffentlichen und Bereitstellen von Medienobjekten

**Übersicht**: [Bereitstellen von Inhalten für Kunden - Übersicht](media-services-deliver-content-overview.md)

Veröffentlichen Sie Medienobjekte (durch Locator-Erstellung) mithilfe des **Azure-Verwaltungsportals** oder mit **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##Aktivieren von Azure CDN

Media Services unterstützt die Integration in Azure CDN. Informationen zum Aktivieren von Azure CDN finden Sie unter [Verwalten von Streamingendpunkten in Media Services-Konten](media-services-manage-origins.md#enable_cdn).

##Skalieren eines Media Services-Kontos

Sie können **Media Services** skalieren, indem Sie die Anzahl der **reservierten Einheiten für das Streaming** angeben, die für Ihr Konto bereitgestellt werden sollen. 

Informationen zum Skalieren von Streamingeinheiten finden Sie unter: [Skalieren von Streamingeinheiten](media-services-manage-origins.md#scale_streaming_endpoints.md).




[live-overview]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png


<!--HONumber=52--> 