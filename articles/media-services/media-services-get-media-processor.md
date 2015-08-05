<properties 
	pageTitle="Erstellen eines Medienprozessors - Azure" 
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
	ms.date="05/13/2015" 
	ms.author="juliako"/>


#Gewusst wie: Abrufen einer Media Processor-Instanz

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md).

##Übersicht

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Medienprozessorname</th>
       <th>Beschreibung</th>
	<th>Weitere Informationen</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>Zur Ausführung von Codieraufgaben mit dem Azure Media Encoder.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Aufgabenvoreinstellungen für den Azure Media Encoder</a></td>
    </tr>
    <tr>
       <td>Media Encoder Premium Workflow</td>
       <td>Zur Ausführung von Codieraufgaben mit dem Media Encoder Premium Workflow.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">Codierung mit dem Medienencoder-Premium-Workflow</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>Macht Mediendateien und Inhalte durchsuchbar und generiert Untertitelspuren und Schlüsselwörter.</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Indizieren von Mediendateien mit Azure Media Indexer</a></td>
    </tr>
    <tr>
        <td>Azure Media Packager</td>
        <td>Konvertiert Medienobjekte von .mp4 in das Smooth Streaming-Format. Konvertiert außerdem Medienobjekte vom Smooth Streaming-Format in das Apple HTTP Live Streaming (HLS)-Format.</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Systemvoreinstellungen für den Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Encryptor</td>
        <td>Verschlüsselt Medienobjekte mit dem PlayReady-Schutz.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Aufgabenvoreinstellungen für den Azure Media Packager</a></td>
    </tr>
	<tr>
		<td>Azure Media Hyperlapse (Vorschau)</td>
		<td>Ermöglicht es Ihnen, die "Unregelmäßigkeiten" in Ihrem Video mithilfe von Videostabilisierung auszugleichen. Darüber hinaus können Sie Ihre Inhalte beschleunigen, um einen verwendbaren Clip zu erhalten.</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkId=613274">Azure Media Hyperlapse</a></td>
	</tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Entschlüsselt Medienobjekte, die mit der Speicherverschlüsselung verschlüsselt wurden.</td>
		<td>–</td>
    </tr>  </tbody>
</table>

<br />

##Abrufen von MediaProcessor

Das folgende Beispiel zeigt, wie Sie eine Medienprozessor-Instanz erstellen. Das folgende Codebeispiel setzt eine Variable auf Modulebene mit dem Namen **_context** voraus, die Zugriff auf den Serverkontext bietet. Siehe [Gewusst wie: Programmgesteuertes Verbinden mit Media Services].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##Nächste Schritte
Sie sind nun in der Lage, eine Medienprozessor-Instanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Medienobjekten][] fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Azure Media Encoder codieren können.

[Gewusst wie: Codieren von Medienobjekten]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Gewusst wie: Programmgesteuertes Verbinden mit Media Services]: ../media-services-set-up-computer/

<!---HONumber=July15_HO4-->