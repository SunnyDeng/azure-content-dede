<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Gewusst wie: Abrufen einer Media Processor-Instanz

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher][].

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.



<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Medienprozessorname </th>
       <th>Beschreibung  /th>
	<th> Weitere Informationen </th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>Zur Ausführung von Codieraufgaben mit dem Media Encoder.    </td>
       <td> <a href="http://msdn.microsoft.com/en-us/library/jj129582.aspx">Systemvoreinstellungen für den Azure Media Encoder</a> </td>
    </tr>
    <tr>
        <td>Azure Media Packager</td>
        <td>Konvertiert Medienobjekte von .mp4 in das Smooth Streaming-Format. Konvertiert außerdem Medienobjekte vom Smooth Streaming-Format in das Apple HTTP Live Streaming (HLS)-Format.</td>
		<td><a href="http://msdn.microsoft.com/en-us/library/hh973635.aspx"> Systemvoreinstellungen für den Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Encryptor</td>
        <td>Verschlüsselt Medienobjekte mit dem PlayReady-Schutz.    </td>
        <td> <a href="http://msdn.microsoft.com/en-us/library/hh973610.aspx">Systemvoreinstellungen für den Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Entschlüsselt Medienobjekte, die mit der Speicherverschlüsselung verschlüsselt wurden. </td>
		<td>N/V      </td>
    </tr>
  </tbody>
</table>

Das folgende Beispiel zeigt, wie Sie eine Medienprozessor-Instanz erstellen. Dieses Codebeispiel verwendet eine Variable auf Modulebene mit dem Namen \*\*\_context\*\*, um auf den Serverkontext zu verweisen, wie beschrieben in [Gewusst wie: Programmgesteuertes Verbinden mit Mediendiensten][].

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## Nächste Schritte

Sie sind nun in der Lage, eine Medienprozessor-Instanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Medienobjekten][] fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Azure Media Encoder codieren können.

  [Vorgehensweise: Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher]: http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409
  [Systemvoreinstellungen für den Azure Media Encoder]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [Systemvoreinstellungen für den Azure Media Packager]: http://msdn.microsoft.com/en-us/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [Gewusst wie: Programmgesteuertes Verbinden mit Mediendiensten]: http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services
  [Gewusst wie: Codieren von Medienobjekten]: http://go.microsoft.com/fwlink/?LinkId=301753
