<properties 
	pageTitle="Erstellen eines Medienprozessors | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Medienprozessorkomponente erstellen können, um Medieninhalte für Azure Media Services zu codieren, zu ver- oder entschlüsseln, und um Formate zu konvertieren. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="juliako"/>


#Gewusst wie: Abrufen einer Media Processor-Instanz

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)


##Übersicht

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.

Medienprozessorname|Beschreibung|Weitere Informationen
---|---|---
Media Encoder Standard|Umfasst Standardfunktionen für bedarfsgesteuerte Codierung. |[Azure On-Demand Media Encoder – Überblick und Vergleich](media-services-encode-asset.md)
Media Encoder Premium Workflow|Zur Ausführung von Codieraufgaben mit dem Media Encoder Premium Workflow.|[Azure On-Demand Media Encoder – Überblick und Vergleich](media-services-encode-asset.md)
Azure Media Indexer| Macht Mediendateien und Inhalte durchsuchbar und generiert Untertitelspuren und Schlüsselwörter.|[Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (Vorschau)|Ermöglicht es Ihnen, die "Unregelmäßigkeiten" in Ihrem Video mithilfe von Videostabilisierung auszugleichen. Darüber hinaus können Sie Ihre Inhalte beschleunigen, um einen verwendbaren Clip zu erhalten.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|Veraltet
Storage Decryption| Veraltet|
Azure Media Packager|Veraltet|
Azure Media Encryptor|Veraltet|

##Abrufen von Medienprozessoren

Das folgende Beispiel zeigt, wie Sie eine Medienprozessor-Instanz erstellen. Das folgende Codebeispiel setzt eine Variable auf Modulebene mit dem Namen "\_context" voraus, die Zugriff auf den Serverkontext bietet. Siehe [Vorgehensweise: Programmgesteuertes Verbinden mit Media Services](media-services-dotnet-connect_programmatically.md).

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
		var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		if (processor == null)
		throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		return processor;
	}


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Nächste Schritte

Sie sind nun in der Lage, eine Medienprozessorinstanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Assets](media-services-dotnet-encode-with-media-encoder-standard.md) fortfahren. Dort lernen Sie, wie Sie Assets mit dem Media Encoder Standard codieren können.

<!---HONumber=AcomDC_0302_2016-->