<properties 
	pageTitle="Erstellen von erweiterten Codierungs-Workflows mit Workflow-Designer" 
	description="Erfahren Sie, wie Sie mit dem Workflow-Designer erweiterte Codierungs-Workflows erstellen." 
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
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Erstellen von erweiterten Codierungs-Workflows mit Workflow-Designer

##Übersicht
Der **Workflow-Designer** ist ein Windows-Desktoptool, das zum Entwerfen und Erstellen benutzerdefinierter Workflows für die Codierung mit dem **Medienencoder-Premium-Workflow** verwendet wird.

Dieses Tool kann auch zum Ändern [vorhandener Workflows](media-services-workflow-designer.md#existing_workflows) eingesetzt werden

>[AZURE.NOTE]Um eine Kopie des Workflow-Designer-Tools zu erhalten, wenden Sie sich an mepd@microsoft.com.


Sobald eine Workflowdatei erstellt wurde, kann sie als Medienobjekt hochgeladen werden und anschließend zum Codieren von Mediendateien verwendet werden. Informationen zum Codieren mit dem **Medienencoder-Premium-Workflow** unter Verwendung von **.NET** finden Sie im Thema [Erweiterte Codierung mit dem Medienencoder-Premium-Workflow](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Ändern vorhandener Workflows

Die standardmäßigen Workflowdateien können mit dem Designer-Tool geändert werden. Sie können die standardmäßigen Workflowdateien [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows) abrufen. Der Ordner enthält auch Beschreibungen dieser Dateien.

In den folgenden Videos wird die Verwendung des Designers veranschaulicht.

###Tag 1 – erste Schritte

Im Video von Tag 1 werden folgende Themen behandelt:

- Übersicht über Designer
- Grundlegende Workflows – "Hello World"
- Erstellen mehrerer MP4-Ausgabedateien für die Verwendung mit Azure Media Services-Streaming

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###Tag 2

Im Video von Tag 2 werden folgende Themen behandelt:

- Unterschiedliche Quelldateiszenarien – Verarbeiten von Audiodaten
- Workflows mit erweiterter Logik
- Graph-Phasen

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###Tag 3

Im Video von Tag 3 werden folgende Themen behandelt:

- Skripterstellung innerhalb von Workflows/Blaupausen
- Einschränkungen des aktuellen Encoders
- Fragen und Antworten
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]



##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)


##Weitere Informationen

[Azure Premium Encoder Workflow-Designer – Schulungsvideos](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=Oct15_HO2-->