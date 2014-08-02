<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

Gewusst wie: Abrufen einer Media Processor-Instanz
==================================================

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Gewusst wie: Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher](http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409).

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Medienprozessorname
Beschreibung
Weitere Informationen</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure Media Encoder
Zur Ausführung von Codieraufgaben mit dem Media Encoder.
<a href="http://msdn.microsoft.com/en-us/library/jj129582.aspx">Systemvoreinstellungen für den Azure Media Encoder</a></td>
<td align="left">Azure Media Packager
Konvertiert Medienobjekte von .mp4 in das Smooth Streaming-Format. Konvertiert außerdem Medienobjekte vom Smooth Streaming-Format in das Apple HTTP Live Streaming (HLS)-Format.
<a href="http://msdn.microsoft.com/en-us/library/hh973635.aspx">Systemvoreinstellungen für den Azure Media Packager</a></td>
<td align="left">Azure Media Encryptor
Verschlüsselt Medienobjekte mit dem PlayReady-Schutz.
<a href="http://msdn.microsoft.com/en-us/library/hh973610.aspx">Systemvoreinstellungen für den Azure Media Packager</a></td>
</tr>
</tbody>
</table>

Das folgende Beispiel zeigt, wie Sie eine Medienprozessor-Instanz erstellen. Das folgende Beispiel setzt eine Variable auf Modulebene mit dem Namen **\_context** voraus, die Zugriff auf den Serverkontext bietet. Siehe [Gewusst wie: Programmgesteuertes Verbinden mit Mediendiensten](http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services).

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
     	var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        	ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    	if (processor == null)
        	throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    	return processor;
	}

Nächste Schritte
----------------

Sie sind nun in der Lage, eine Medienprozessor-Instanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Medienobjekten](http://go.microsoft.com/fwlink/?LinkId=301753) fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Azure Media Encoder codieren können.

