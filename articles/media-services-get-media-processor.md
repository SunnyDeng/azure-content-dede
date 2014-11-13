<properties urlDisplayName="Create a Media Processor" pageTitle="Erstellen eines Medienprozessors - Azure" metaKeywords="" description="Erfahren Sie, wie Sie eine Medienprozessorkomponente erstellen k&ouml;nnen, um Medieninhalte f&uuml;r Azure Media Services zu codieren, zu ver- oder entschl&uuml;sseln, und um Formate zu konvertieren. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK f&uuml;r .NET." metaCanonical="" services="media-services" documentationCenter="" title="Gewusst wie: Abrufen einer Media Processor-Instanz" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/26/2014" ms.author="juliako" />

# Gewusst wie: Abrufen einer Media Processor-Instanz

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher][Vorgehensweise: Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher].

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.

| Medienprozessorname   | Beschreibung                                                                                                                                                                     | Weitere Informationen                                    |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| Azure Media Encoder   | Zur Ausführung von Codieraufgaben mit dem Media Encoder.                                                                                                                         | [Systemvoreinstellungen für den Azure Media Encoder][Systemvoreinstellungen für den Azure Media Encoder]   |
| Azure Media Packager  | Konvertiert Medienobjekte von .mp4 in das Smooth Streaming-Format. Konvertiert außerdem Medienobjekte vom Smooth Streaming-Format in das Apple HTTP Live Streaming (HLS)-Format. | [Systemvoreinstellungen für den Azure Media Packager][Systemvoreinstellungen für den Azure Media Packager]  |
| Azure Media Encryptor | Verschlüsselt Medienobjekte mit dem PlayReady-Schutz.                                                                                                                            | [Systemvoreinstellungen für den Azure Media Packager][1] |
| Azure Media Indexer   | Macht Mediendateien und Inhalte durchsuchbar und generiert Untertitelspuren und Schlüsselwörter.                                                                                 | N/V                                                      |
| Storage Decryption    | Entschlüsselt Medienobjekte, die mit der Speicherverschlüsselung verschlüsselt wurden.                                                                                           | N/V                                                      |

Das folgende Beispiel zeigt, wie Sie eine Medienprozessor-Instanz erstellen. Dieses Codebeispiel verwendet eine Variable auf Modulebene mit dem Namen \*\*\_context\*\*, um auf den Serverkontext zu verweisen, wie beschrieben in [Gewusst wie: Programmgesteuertes Verbinden mit Mediendiensten][Gewusst wie: Programmgesteuertes Verbinden mit Mediendiensten].

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## Nächste Schritte

Sie sind nun in der Lage, eine Medienprozessor-Instanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Medienobjekten][Gewusst wie: Codieren von Medienobjekten] fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Azure Media Encoder codieren können.

  [Vorgehensweise: Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher]: ../media-services-create-encrypted-asset-upload-storage/
  [Systemvoreinstellungen für den Azure Media Encoder]: http://msdn.microsoft.com/de-de/library/jj129582.aspx
  [Systemvoreinstellungen für den Azure Media Packager]: http://msdn.microsoft.com/de-de/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/de-de/library/hh973610.aspx
  [Gewusst wie: Programmgesteuertes Verbinden mit Mediendiensten]: ../media-services-set-up-computer/
  [Gewusst wie: Codieren von Medienobjekten]: ../media-services-encode-asset/
