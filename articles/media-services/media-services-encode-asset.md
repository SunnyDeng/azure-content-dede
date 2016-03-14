<properties 
	pageTitle="Azure On-Demand Media Encoder – Überblick und Vergleich" 
	description="Dieses Thema bietet eine Übersicht und einen Vergleich über Azure On-Demand-Media Encoder." 
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
 	ms.date="02/25/2016"  
	ms.author="juliako"/>

#Azure On-Demand Media Encoder – Überblick und Vergleich

##Übersicht über die Codierung

Azure Media Services bietet verschiedene Optionen für die Codierung von Medien in der Cloud.

Bei Verwendung von Media Services ist es wichtig, den Unterschied zwischen Codecs und Dateiformaten zu kennen. Bei Codecs handelt es sich um die Software, die die Algorithmen für die Komprimierung/Dekomprimierung implementiert. Dateiformate dagegen sind die Container, die das komprimierte Video enthalten.

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming, HDS), ohne dass Sie diese Streamingformate erneut verpacken müssen.

Um die [dynamische Paketerstellung](media-services-dynamic-packaging-overview.md) nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren Ihrer Zwischendatei (Quelle) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate (die Codierungsschritte werden weiter unten in diesem Lernprogramm beschrieben)
- Abrufen von mindestens einer On-Demand-Streamingeinheit für den Streamingendpunkt, von dem aus Sie die Bereitstellung Ihrer Inhalte planen. Weitere Informationen finden Sie unter [Skalieren von reservierten Einheiten für bedarfsgesteuertes Streaming](media-services-manage-origins.md#scale_streaming_endpoints/).

Media Services unterstützt die folgenden On-Demand-Encoder, die in diesem Artikel beschrieben werden:

- [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
- [Media Encoder Premium Workflow](media-services-encode-asset.md#media-encoder-premium-workflow)

Dieser Artikel enthält eine kurze Übersicht über On-Demand-Media Encoder und stellt Links zu Artikeln bereit, die detailliertere Informationen bieten. Das Thema enthält auch einen Encodervergleich.

Beachten Sie, dass jedes Media Services-Konto standardmäßig je eine aktive Codierungsaufgabe gleichzeitig aufweisen kann. Sie können Einheiten für die Codierung reservieren, mit denen Sie mehrere Codierungsaufgaben gleichzeitig ausführen kennen – jeweils eine für jede reservierte Einheit für die Codierung, die Sie erwerben. Weitere Informationen finden Sie unter [Skalieren der Codierung](media-services-portal-encoding-units.md).

##Media Encoder Standard

###Gewusst wie

[Gewusst wie: Codieren mit Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formate

[Formate und Codecs](media-services-media-encoder-standard-formats.md)

###Voreinstellungen

Media Encoder Standard wird mithilfe von Encoder-Voreinstellungen konfiguriert, die [hier](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409) beschrieben sind.

###Eingabe- und Ausgabemetadaten

Die Eingabemetadaten für den Encoder werden [hier](http://msdn.microsoft.com/library/azure/dn783120.aspx) beschrieben.

Die Ausgabemetadaten für den Encoder werden [hier](http://msdn.microsoft.com/library/azure/dn783217.aspx) beschrieben.

###Generieren von Miniaturansichten

Informationen finden Sie unter [Generieren von Miniaturansichten mithilfe von Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###Kürzen von Videos (Clipping)

Informationen finden Sie unter [Kürzen von Videos mithilfe von Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###Erstellen von Überlagerungen

Informationen finden Sie unter [Erstellen von Überlagerungen mithilfe von Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#overlay).

###Weitere Informationen

[Media Services-Blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Media Encoder Premium Workflow

###Übersicht

[Introducing Premium Encoding in Azure Media Services (in englischer Sprache)](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###Gewusst wie

Media Encoder Premium Workflow ist mit komplexen Workflows konfiguriert. Workflowdateien können mit dem Tool [Workflow-Designer](media-services-workflow-designer.md) erstellt und aktualisiert werden.

[How to Use Premium Encoding in Azure Media Services (in englischer Sprache)](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###Bekannte Probleme

Wenn Ihr Eingabevideo keine Untertitel enthält, enthält das Ausgabemedienobjekt trotzdem eine leere TTML-Datei.


##<a id="compare_encoders"></a>Vergleich der Encoder

###<a id="billing"></a>Von Encodern verwendetes Abrechnungsverfahren

Medienprozessorname|Geltende Preise|Hinweise
---|---|---
**Media Encoder Standard** |ENCODER|Codieraufgaben werden gemäß der Summe der Größen von Ausgabemedienobjekten in GB berechnet, und zwar zur [hier][1] angegebenen Rate in der Spalte ENCODER.
**Media Encoder Premium Workflow** |PREMIUM ENCODER|Codieraufgaben werden gemäß der Summe der Größen von Ausgabemedienobjekten in GB berechnet, und zwar zur [hier][1] angegebenen Rate in der Spalte PREMIUM ENCODER.


In diesem Abschnitt werden die Codierungsfunktionen von **Media Encoder Standard** und **Media Encoder Premium Workflow** verglichen.


###Eingabecontainer/Dateiformate

Eingabecontainer/Dateiformate|Media Encoder Standard|Media Encoder Premium Workflow
---|---|---
Adobe® Flash® F4V |Ja|Ja
MXF/SMPTE 377M |Ja|Ja
GXF |Ja|Ja
MPEG-2-Transportdatenstrom |Ja|Ja
MPEG-2-Programmdatenstrom |Ja|Ja
MPEG-4/MP4 |Ja|Ja
Windows Media/ASF |Ja|Ja
AVI (unkomprimiert, 8-Bit/10-Bit)|Ja|Ja
3GPP/3GPP2 |Ja|Nein
Smooth Streaming-Dateiformat (PIFF 1.3)|Ja|Nein
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Ja|Nein
Matroska/WebM |Ja|Nein
QuickTime (.mov) |Ja|Nein

###Codecs für Videoeingang

Codecs für Videoeingang|Media Encoder Standard|Media Encoder Premium Workflow
---|---|---
AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra |8-Bit 4:2:0 und 4:2:2|Ja
Avid DNxHD (in MXF) |Ja|Ja
DVCPro/DVCProHD (in MXF) |Ja|Ja
JPEG2000 |Ja|Ja
MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)|Bis zu 422 Profile|Ja
MPEG-1 |Ja|Ja
Windows Media Video/VC-1 |Ja|Ja
Canopus HQ/HQX |Nein|Nein
MPEG-4 Teil 2 |Ja|Nein
[Theora](https://en.wikipedia.org/wiki/Theora) |Ja|Nein
Apple ProRes 422 |Ja|Nein
Apple ProRes 422 LT |Ja|Nein
Apple ProRes 422 HQ |Ja|Nein
Apple ProRes Proxy|Ja|Nein
Apple ProRes 4444 |Ja|Nein
Apple ProRes 4444 XQ |Ja|Nein

###Codecs für Audioeingang

Codecs für Audioeingang|Media Encoder Standard|Media Encoder Premium Workflow
---|---|---
AES (SMPTE 331M und 302M, AES3-2003) |Nein|Ja
Dolby® E |Nein|Ja
Dolby® Digital (AC3) |Nein|Ja
Dolby® Digital Plus (E-AC3) |Nein|Ja
AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)|Ja|Ja
MPEG Layer 2|Ja|Ja
MP3 (MPEG-1 Audio Layer 3)|Ja|Ja
Windows Media Audio|Ja|Ja
WAV/PCM|Ja|Ja
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Ja|Nein
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Ja|Nein
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Ja|Nein


###Ausgabecontainer/Dateiformate

Ausgabecontainer/Dateiformate|Media Encoder Standard|Media Encoder Premium Workflow
---|---|---
Adobe® Flash® F4V|Nein|Ja
MXF (OP1a, XDCAM und AS02)|Nein|Ja
DPP (einschließlich AS11)|Nein|Ja
GXF|Nein|Ja
MPEG-4/MP4|Ja|Ja
MPEG-TS|Ja|Ja
Windows Media/ASF|Nein|Ja
AVI (unkomprimiert, 8-Bit/10-Bit)|Nein|Ja
Smooth Streaming-Dateiformat (PIFF 1.3)|Nein|Ja

###Codecs für Videoausgang

Codecs für Videoausgang|Media Encoder Standard|Media Encoder Premium Workflow
---|---|---
AVC (H.264; 8-Bit; bis High Profile, Level 5.2; 4K Ultra HD; AVC Intra)|Nur 8-Bit 4:2:0|Ja
Avid DNxHD (in MXF)|Nein|Ja
DVCPro/DVCProHD (in MXF)|Nein|Ja
MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)|Nein|Ja
MPEG-1|Nein|Ja
Windows Media Video/VC-1|Nein|Ja
Erstellung von JPEG-Miniaturansichten|Nein|Ja

###Codecs für Audioausgabe

Codecs für Audioausgabe|Media Encoder Standard|Media Encoder Premium Workflow
---|---|---
AES (SMPTE 331M und 302M, AES3-2003)|Nein|Ja
Dolby® Digital (AC3)|Nein|Ja
Dolby® Digital Plus (E-AC3) bis 7.1|Nein|Ja
AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)|Ja|Ja
MPEG Layer 2|Nein|Ja
MP3 (MPEG-1 Audio Layer 3)|Nein|Ja
Windows Media Audio|Nein|Ja


##Fehlercodes  

Die folgende Tabelle enthält die Fehlercodes, die zurückgegeben werden können, wenn bei der Ausführung einer Codieraufgabe ein Fehler auftritt. Verwenden Sie zum Abrufen von Fehlerdetails im .NET-Code die [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx)-Klasse. Verwenden Sie zum Abrufen von Fehlerdetails im REST-Code die [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx)-REST-API.

ErrorDetail.Code|Mögliche Ursachen des Fehlers
-----|-----------------------
Unknown| Unbekannter Fehler beim Ausführen der Aufgabe
ErrorDownloadingInputAssetMalformedContent|Kategorie von Fehlern, die Fehler beim Herunterladen von Eingabe-Assets abdeckt, z. B. fehlerhafte Dateinamen, Dateien mit Nulllänge, falsche Formate usw.
ErrorDownloadingInputAssetServiceFailure|Kategorie von Fehlern, die Probleme auf der Dienstseite abdeckt, z. B. Netzwerk- oder Speicherfehler beim Herunterladen.
ErrorParsingConfiguration|Kategorie von Fehlern, bei denen die Aufgabe <see cref="MediaTask.PrivateData"/> (Konfiguration) nicht gültig ist, z. B. wenn die Konfiguration keine gültige Systemvoreinstellung ist oder ungültige XML-Daten enthält.
ErrorExecutingTaskMalformedContent|Kategorie von Fehlern während der Ausführung der Aufgabe, wenn Probleme in den Eingabemediendateien zu Fehlern führen.
ErrorExecutingTaskUnsupportedFormat|Kategorie von Fehlern, bei denen die bereitgestellten Dateien mit dem Medienprozessor nicht verarbeitet werden können: keine Unterstützung des Medienformats oder fehlende Übereinstimmung mit der Konfiguration. Beispiel: Der Versuch, eine Audioausgabe für ein Asset zu erstellen, die nur Video ermöglicht.
ErrorProcessingTask|Kategorie anderer Fehler, die für den Medienprozessor beim Verarbeiten der Aufgabe auftreten und sich nicht auf den Inhalt beziehen.
ErrorUploadingOutputAsset|Kategorie von Fehlern beim Hochladen des Ausgabe-Assets.
ErrorCancelingTask|Kategorie von Fehlern, die auftreten, wenn versucht wird, die Aufgabe abzubrechen.
TransientError|Kategorie von Fehlern, mit der vorübergehende Probleme abgedeckt werden (z. B. temporäre Netzwerkprobleme mit Azure Storage).


Öffnen Sie ein [Supportticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um Hilfe vom **Media Services**-Team zu erhalten.



##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Verwandte Artikel

- [Ausführen von Aufgaben für die erweiterte Codierung durch Anpassen der Media Encoder Standard-Voreinstellungen](media-services-custom-mes-presets-with-dotnet.md)
- [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=AcomDC_0302_2016-->