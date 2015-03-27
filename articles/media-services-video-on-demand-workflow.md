<properties 
	pageTitle="Media Services-VoD-Workflow" 
	description="In diesem Thema werden die Schritte eines typischen Media Services-VoD-Workflows beschrieben." 
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


# Media Services-VoD-Workflow

## Übersicht

In diesem Thema werden die Schritte eines typischen Azure Media Services-VoD-Workflows (Video on Demand) beschrieben. Jeder Schritt enthält Links zu relevanten Themen. Für Aufgaben, die mithilfe verschiedener Technologien erledigt werden können, stehen Schaltflächen zur Technologie Ihrer Wahl (z. B. .NET oder REST) zur Verfügung.   

Beachten Sie, dass Sie Media Services in Ihre vorhandenen Tools und Prozesse integrieren können. Codieren Sie Inhalte z. B. am Standort, und laden Sie sie danach zu Media Services hoch, um sie in mehreren Formaten zu transcodieren und über ein Azure-CDN oder ein Drittanbieter-CDN zu übermitteln. 

Das folgende Diagramm zeigt die Hauptbestandteile der Media Services-Plattform, die am Video on Demand-Workflow beteiligt sind.
![VoD workflow][vod-overview]

Weitere Informationen finden Sie unter [Media Services - Übersicht](../media-services-overview).

## Erstellen eines Media Services-Kontos

Verwenden Sie das **Azure-Verwaltungsportal** zum [Erstellen eines Azure Media Services-Kontos](../media-services-create-account/). 

## Konfigurieren von Streamingendpunkten

[In Bearbeitung]

## Einrichten der Entwicklungsumgebung  

Wählen Sie **.NET** oder **REST-API** für Ihre Entwicklungsumgebung.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## Programmgesteuerte Verbindung  

Wählen Sie **.NET** oder **REST-API**, um eine programmgesteuerte Verbindung mit Azure Media Services herzustellen.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

## Hochladen von Medien 

Laden Sie Ihre Dateien mithilfe von **Azure-Verwaltungsportal**, **.NET** oder **REST-API** hoch.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

## Medienverarbeitung: Codierung, Indizierung, Überwachen von Aufträgen

### Abrufen eines Medienprozessors

Rufen Sie mithilfe von **.NET** oder **REST-API** einen Medienprozessor ab.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

### Erstellen von Aufträgen 

Ein Auftrag ist eine Entität, die Metadaten zu einer Reihe von Aufgaben enthält (z. B. Codierung oder Indizierung). Jede Aufgabe führt einen unteilbaren Vorgang für das (oder die) Eingangsmedienobjekt(e) durch. Ein Beispiel zum Erstellen von Codierungsaufträgen finden Sie hier:

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

### Überwachen des Auftragsfortschritts

Überwachen Sie den Auftragsfortschritt mithilfe von **Azure-Verwaltungsportal**, **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

### Indizieren

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

### Codieren 

Siehe [Codierung mit Azure Media Services](../media-services-encode-asset).

## Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel 

Konfigurieren Sie Inhaltsschutz und Autorisierungsrichtlinien für Inhaltsschlüssel mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

## Konfigurieren von Übermittlungsrichtlinien für Medienobjekte

Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## Veröffentlichen von Medienobjekten

Veröffentlichen Sie Medienobjekte (durch Locator-Erstellung) mithilfe des **Azure-Verwaltungsportals** oder mit **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

## Skalieren eines Media Services-Kontos

Sie können **Media Services** skalieren, indem Sie die Anzahl **reservierter Einheiten für das Streaming ** und die Anzahl **reservierter Einheiten für die Codierung** angeben, die für Ihr Konto bereitgestellt werden soll. 

Außerdem können Sie Ihr Media Services-Konto skalieren, indem Sie Speicherkonten hinzufügen. Jedes Speicherkonto ist auf 500 TB beschränkt. Um den Speicher über die Standardbeschränkungen hinaus zu erweitern, können Sie mehrere Speicherkonten mit einem einzelnen Media Services-Konto verknüpfen.

[Dieses](../media-services-how-to-scale) Thema enthält Links zu relevanten Themen.


## Wiedergabe von Inhalten

Weitere Informationen finden Sie unter [Wiedergabe Ihrer Inhalte mit vorhandenen Playern](../media-services-playback-content).

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
