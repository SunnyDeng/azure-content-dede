<properties 
	pageTitle="Erstellen von erweiterten Codierungs-Workflows mit Workflow-Designer" 
	description="Erfahren Sie, wie Sie mit dem Workflow-Designer erweiterte Codierungs-Workflows erstellen." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,johndeu,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/16/2015"  
	ms.author="juliako"/>


#Erstellen von erweiterten Codierungs-Workflows mit Workflow-Designer

##Übersicht

Der **Workflow-Designer** ist ein Windows-Desktoptool, das zum Entwerfen und Erstellen benutzerdefinierter Workflows für die Codierung mit dem **Medienencoder-Premium-Workflow** verwendet wird. Dank der Leistungsfähigkeit des Workflow-Designer-Tools können Sie komplexe Workflows entwerfen und erstellen, die in **Medienencoder-Premium** ausgeführt werden.

Workflows können Entscheidungsfindungslogik von Kunden und Verzweigungen enthalten, die auf den Eigenschaften der Eingabequelldatei basieren. Sie können Workflows mit überschreibbaren Eigenschaften und dynamischen Werten erstellen, um auch für sehr komplexe Codieraufgaben zu erreichen, dass sie leicht wiederholt und in der Cloud angepasst werden können.

Beispiele für Workflows, die Sie erstellen können:

- Entscheidungsbasierte Workflows, bei denen der Quellinhalt für die Auflösung untersucht wird und nur die gewünschten Ausgabespuren codiert werden. Dies ist hilfreich, da die überflüssigen Spuren beseitigt werden, die generiert werden, wenn der Quellinhalt versehentlich horizontal hochskaliert wird.
- Es können mehrere Eingabedateien verwendet werden, um Untertitel, Überlagerungen und die Verknüpfung von Inhalten zu unterstützen. 

Dieses Tool kann auch verwendet werden, um unsere [veröffentlichten Workflows](media-services-workflow-designer.md#existing_workflows) zu ändern.

>[AZURE.NOTE]Wenden Sie sich an mepd@microsoft.com, um eine Kopie des Workflow-Designer-Tools zu erhalten.


Sobald eine Workflowdatei erstellt wurde, kann sie als Medienobjekt hochgeladen werden und anschließend zum Codieren von Mediendateien verwendet werden. Informationen zum Codieren mit dem **Medienencoder-Premium-Workflow** unter Verwendung von **.NET** finden Sie im Thema [Erweiterte Codierung mit dem Medienencoder-Premium-Workflow](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Ändern vorhandener Workflows

Die standardmäßigen [veröffentlichten Workflows](media-services-workflow-designer.md#existing_workflows) können mit dem Designer-Tool geändert werden. Sie können die standardmäßigen Workflowdateien [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows) abrufen. Der Ordner enthält auch Beschreibungen dieser Dateien.

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

Falls Sie Support benötigen oder Fragen zur Erstellung von benutzerdefinierten Workflows im Workflow-Designer-Tool haben, können Sie eine E-Mail an mepd@microsoft.com senden.

##Siehe auch

[Azure Premium Encoder Workflow-Designer – Schulungsvideos](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=Oct15_HO4-->