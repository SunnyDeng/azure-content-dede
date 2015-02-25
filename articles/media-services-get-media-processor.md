<properties pageTitle="Erstellen eines Medienprozessors - Azure" description="Erfahren Sie, wie Sie eine Medienprozessorkomponente erstellen können, um Medieninhalte für Azure Media Services zu codieren, zu ver- oder entschlüsseln, und um Formate zu konvertieren. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>





<h1>Gewusst wie: Abrufen einer Media Processor-Instanz</h1>
Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure Media Services. Das vorherige Thema war [Gewusst wie: Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher](../ media-services-create-encrypted-asset-upload-storage/).

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

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
       <td>Zur Ausführung von Codieraufgaben mit dem Media Encoder.</td>
       <td><a href="http://msdn.microsoft.com/de-de/library/jj129582.aspx"> Systemvoreinstellungen für den Azure Media Encoder</a></td>
    </tr>
    <tr>
        <td>Azure Media Packager</td>
        <td>Konvertiert Medienobjekte von .mp4 in das Smooth Streaming-Format. Konvertiert außerdem Medienobjekte vom Smooth Streaming-Format in das Apple HTTP Live Streaming (HLS)-Format.</td>
		<td><a href="http://msdn.microsoft.com/de-de/library/hh973635.aspx">Systemvoreinstellungen für den Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Encryptor</td>
        <td>Verschlüsselt Medienobjekte mit dem PlayReady-Schutz.</td>
        <td><a href="http://msdn.microsoft.com/de-de/library/hh973610.aspx">Systemvoreinstellungen für den Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Indexer</td>
        <td>Macht Mediendateien und Inhalte durchsuchbar und generiert Untertitelspuren und Schlüsselwörter.</td>
		<td>N/V</td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Entschlüsselt Medienobjekte, die mit der Speicherverschlüsselung verschlüsselt wurden.</td>
		<td>N/V</td>
    </tr>  </tbody>
</table>

<br />

Das folgende Beispiel zeigt, wie Sie eine Medienprozessor-Instanz erstellen. Dieses Codebeispiel verwendet eine Variable auf Modulebene namens **_context**, um auf den Serverkontext zu verweisen, wie beschrieben in [Gewusst wie: Programmgesteuertes Verbinden mit Media Services].

<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
</code></pre>

<h2>Nächste Schritte</h2>
Sie sind nun in der Lage, eine Medienprozessor-Instanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Medienobjekten][] fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Azure Media Encoder codieren können.

[Gewusst wie: Codieren von Medienobjekten]: ../media-services-encode-asset/
[Systemvoreinstellungen für den Azure Media Encoder]: http://msdn.microsoft.com/de-de/library/jj129582.aspx
[Gewusst wie: Programmgesteuertes Verbinden mit Media Services]: ../media-services-set-up-computer/

<!--HONumber=42-->
