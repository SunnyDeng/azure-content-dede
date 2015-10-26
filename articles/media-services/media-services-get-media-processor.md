<properties 
	pageTitle="Erstellen eines Medienprozessors | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Medienprozessorkomponente erstellen können, um Medieninhalte für Azure Media Services zu codieren, zu ver- oder entschlüsseln, und um Formate zu konvertieren. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
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


#Gewusst wie: Abrufen einer Media Processor-Instanz

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)
 

##Übersicht

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.

Medienprozessorname|Beschreibung|Weitere Informationen
---|---|---
Azure Media Encoder|Zur Ausführung von Codieraufgaben mit dem Azure Media Encoder.|[Azure Media Encoder](media-services-encode-asset.md#azure_media_encoder)
Media Encoder Standard|Zur Ausführung von Codieraufgaben mit dem Media Encoder Standard.|[Azure Media Encoder](media-services-encode-asset.md#media_encoder_standard)
Media Encoder Premium Workflow|Zur Ausführung von Codieraufgaben mit dem Media Encoder Premium Workflow.|[Media Encoder Premium Workflow](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure Media Indexer| Macht Mediendateien und Inhalte durchsuchbar und generiert Untertitelspuren und Schlüsselwörter.|[Indizieren von Mediendateien mit Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (Vorschau)|Ermöglicht es Ihnen, die "Unregelmäßigkeiten" in Ihrem Video mithilfe von Videostabilisierung auszugleichen. Darüber hinaus können Sie Ihre Inhalte beschleunigen, um einen verwendbaren Clip zu erhalten.|		[Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
Storage Decryption| Entschlüsselt Medienobjekte, die mit der Speicherverschlüsselung verschlüsselt wurden.|N/V
Azure Media Packager|Konvertiert Medienobjekte von .mp4 in das Smooth Streaming-Format. Konvertiert außerdem Medienobjekte vom Smooth Streaming-Format in das Apple HTTP Live Streaming (HLS)-Format.|[Systemvoreinstellungen für den Azure Media Packager](http://msdn.microsoft.com/library/hh973635.aspx)
Azure Media Encryptor|Verschlüsselt Medienobjekte mit dem PlayReady-Schutz.|[Aufgabenvoreinstellungen für den Azure Media Packager](http://msdn.microsoft.com/library/hh973610.aspx)

##Abrufen von MediaProcessor

Das folgende Beispiel zeigt, wie Sie eine Medienprozessor-Instanz erstellen. Das folgende Codebeispiel setzt eine Variable auf Modulebene mit dem Namen "\_context" voraus, die Zugriff auf den Serverkontext bietet. Siehe [Vorgehensweise: Programmgesteuertes Verbinden mit Media Services].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}


##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)


##Nächste Schritte
Sie sind nun in der Lage, eine Medienprozessor-Instanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Medienobjekten][] fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Azure Media Encoder codieren können.

[Gewusst wie: Codieren von Medienobjekten]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Vorgehensweise: Programmgesteuertes Verbinden mit Media Services]: ../media-services-set-up-computer/

<!---HONumber=Oct15_HO3-->