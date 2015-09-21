<properties 
	pageTitle="Azure On-Demand Media Encoder – Überblick und Vergleich" 
	description="Dieses Thema bietet eine Übersicht und einen Vergleich über Azure On-Demand-Media Encoder." 
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

#Azure On-Demand Media Encoder – Überblick und Vergleich

##Übersicht über die Codierung

Encoder komprimieren digitale Medien mithilfe von Codecs. Encoder bieten üblicherweise verschiedene Einstellungen, mit denen Sie Eigenschaften der generierten Medien angeben können, z. B. verwendete Codecs, Dateiformat, Auflösung und Bitrate. Dateiformate sind Container, die das komprimierte Video sowie Informationen zu den Codecs enthalten, die für dessen Komprimierung verwendet wurden.

Codecs verfügen über zwei Komponenten: eine zum Komprimieren digitaler Mediendateien für die Übertragung und die andere zum Dekomprimieren digitaler Mediendateien für die Wiedergabe. Für die Komprimierung und Dekomprimierung von Audiodaten existieren Audiocodecs, und für Videodaten entsprechend Videocodecs. Codecs können entweder verlustfreie oder verlustbehaftete Komprimierung verwenden. Verlustfreie Codecs erhalten bei der Komprimierung alle Informationen. Das Ergebnis der Dekomprimierung ist eine Datei, die identisch mit dem Eingabemedium ist. Verlustfreie Codecs eignen sich daher gut für Archivierung und Speicherung. Verlustbehaftete Codecs verlieren beim Komprimieren einen Teil der Informationen, produzieren kleinere Dateien (im Vergleich zum Original) auf Kosten der Videoqualität und eignen sich gut zum Streaming über das Internet.

Es ist wichtig, den Unterschied zwischen Codecs und Dateiformaten zu kennen. Bei Codecs handelt es sich um die Software, die die Algorithmen für die Komprimierung/Dekomprimierung implementiert. Dateiformate dagegen sind die Container, die das komprimierte Video enthalten. Weitere Informationen finden Sie unter [Codierung im Vergleich zur Paketerstellung](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming, HDS), ohne dass Sie diese Streamingformate erneut verpacken müssen.

Um die [dynamische Paketerstellung](media-services-dynamic-packaging-overview.md) nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren Ihrer Zwischendatei (Quelle) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate (die Codierungsschritte werden weiter unten in diesem Lernprogramm beschrieben)
- Abrufen von mindestens einer On-Demand-Streamingeinheit für den Streamingendpunkt, von dem aus Sie die Bereitstellung Ihrer Inhalte planen. Weitere Informationen finden Sie unter [Skalieren von reservierten Einheiten für bedarfsgesteuertes Streaming](media-services-manage-origins.md#scale_streaming_endpoints/).

Media Services unterstützt die folgenden On-Demand-Encoder, die in diesem Artikel beschrieben werden:

- **Media Encoder Standard**
- **Azure Media Encoder** 
- **Media Encoder Premium Workflow**

Dieser Artikel enthält eine kurze Übersicht über On-Demand-Media Encoder und stellt Links zu Artikeln bereit, die detailliertere Informationen bieten. Das Thema enthält auch einen Encodervergleich.

Beachten Sie, dass jedes Media Services-Konto standardmäßig je eine aktive Codierungsaufgabe gleichzeitig aufweisen kann. Sie können Einheiten für die Codierung reservieren, mit denen Sie mehrere Codierungsaufgaben gleichzeitig ausführen kennen – jeweils eine für jede reservierte Einheit für die Codierung, die Sie erwerben. Weitere Informationen finden Sie unter [Skalieren von Codierungseinheiten](media-services-portal-encoding-units.md).

##Media Encoder Standard

###Übersicht

Es wird empfohlen, den Media Encoder Standard-Encoder zu verwenden. Allerdings ist dieser über das Azure-Portal derzeit nicht verfügbar.

Im Vergleich zu Azure Media Encoder unterstützt dieser Encoder weitere Eingabe- und Ausgabeformate und Codecs. Weitere Vorteile sind:

- Höhere Toleranz, was die Erstellung der Eingabedatei betrifft
- Bessere Qualität des H.264-Codecs als Azure Media Encoder
- Basiert auf einer neueren und flexibleren Pipeline
- Stabiler und robuster

###Gewusst wie

[Gewusst wie: Codieren mit Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formate

[Formate und Codecs](media-services-media-encoder-standard-formats.md)

###Voreinstellungen

Media Encoder Standard wird mithilfe einer der Encoder-Voreinstellungen konfiguriert, die [hier](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409) beschrieben sind.

###Eingabe- und Ausgabemetadaten

Die Eingabemetadaten des Encoders werden [hier](http://msdn.microsoft.com/library/azure/dn783120.aspx) beschrieben (wie für Azure Media Encoder).

Die Ausgabemetadaten des Encoders werden [hier](http://msdn.microsoft.com/library/azure/dn783217.aspx) beschrieben (wie für Azure Media Encoder).

###Miniaturansicht

Wird derzeit nicht unterstützt.

###Audio- und/oder Videoüberlagerungen

Wird derzeit nicht unterstützt.

###Weitere Informationen

[Media Services-Blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Azure Media Encoder

###Übersicht

Azure Media Encoder ist einer der Encoder, die von Media Services unterstützt werden. Es wird empfohlen, ab Juli 2015 [Media Encoder Standard](media-services-encode-asset.md#media_encoder_standard) zu verwenden.

###Gewusst wie

[Gewusst wie: Codieren mit Azure Media Encoder](media-services-dotnet-encode-asset.md)

###Formate

[Formate und Codecs](media-services-azure-media-encoder-formats.md)

###Voreinstellungen

Azure Media Encoder wird mithilfe von Encoder-Voreinstellungen konfiguriert, die [hier](https://msdn.microsoft.com/library/azure/dn619392.aspx) beschrieben sind. Die eigentlichen Voreinstellungsdateien für Azure Media Encoder finden Sie [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

###Eingabe- und Ausgabemetadaten

Die Eingabemetadaten für den Encoder werden [hier](http://msdn.microsoft.com/library/azure/dn783120.aspx) beschrieben.

Die Ausgabemetadaten für den Encoder werden [hier](http://msdn.microsoft.com/library/azure/dn783217.aspx) beschrieben.

###Miniaturansicht

[Erstellen einer Miniaturansicht](https://msdn.microsoft.com/library/azure/Dn673581.aspx)

###Audio- und/oder Videoüberlagerungen

[Erstellen von Überlagerungen](media-services-azure-media-customize-ame-presets.md#creating-overlays).

###Benennungskonvention

[Vorgehensweise beim Ändern der Ausgabedateinamen](media-services-azure-media-customize-ame-presets.md#controlling-azure-media-encoder-output-file-names)

###Weitere Informationen

[Codieren Ihrer Medien mit Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md)

##Media Encoder Premium Workflow
	
Media Encoder Premium Workflow ist mit komplexen Workflows konfiguriert. Workflowdateien können mit dem Tool [Workflow-Designer](media-services-workflow-designer.md) erstellt und aktualisiert werden.

Weitere Informationen finden Sie unter:

- [Introducing Premium Encoding in Azure Media Services (in englischer Sprache)](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [How to Use Premium Encoding in Azure Media Services (in englischer Sprache)](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)


##<a id="compare_encoders"></a>Vergleich der Encoder

###<a id="billing"></a>Von Encodern verwendetes Abrechnungsverfahren

Medienprozessorname|Geltende Preise|Hinweise
---|---|---
**Media Encoder Standard** |ENCODER|Codieraufgaben werden gemäß der Summe der Größen von Ausgabemedienobjekten in GB berechnet, und zwar zur [hier][1] angegebenen Rate in der Spalte ENCODER.
**Azure Media Encoder** |ENCODER|Codieraufgaben werden gemäß der Summe der Größen von Ausgabemedienobjekten in GB berechnet, und zwar zur [hier][1] angegebenen Rate in der Spalte ENCODER.
**Media Encoder Premium Workflow** |PREMIUM ENCODER|Codierungsaufgaben werden gemäß der Größe von Ausgabemedienobjekten in GB angegeben, und zwar zur [hier][1] angegebenen Rate in der Spalte PREMIUM ENCODER.


In diesem Abschnitt werden die Codierungsfunktionen von **Media Encoder Standard**, **Azure Media Encoder** und **Media Encoder Premium Workflow** verglichen.


###Eingabecontainer/Dateiformate

Eingabecontainer/Dateiformate|Media Encoder Standard|Azure Media Encoder|Media Encoder Premium Workflow
---|---|---|---
Adobe® Flash® F4V |Ja|Nein |Ja
MXF/SMPTE 377M |Ja|Eingeschränkt|Ja
GXF |Ja|Nein |Ja
MPEG-2-Transportdatenstrom |Ja|Ja |Ja
MPEG-2-Programmdatenstrom |Ja|Ja |Ja
MPEG-4/MP4 |Ja|Ja |Ja
Windows Media/ASF |Ja|Ja |Ja
AVI (unkomprimiert, 8-Bit/10-Bit)|Ja|Ja |Ja
3GPP/3GPP2 |Ja|Ja |Nein
Smooth Streaming-Dateiformat (PIFF 1.3)|Ja|Ja|Nein
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Ja|Nein|Nein
Matroska/WebM |Ja|Nein|Nein

###Codecs für Videoeingang

Codecs für Videoeingang|Media Encoder Standard|Azure Media Encoder|Media Encoder Premium Workflow
---|---|---|---
AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra |8-Bit 4:2:0 und 4:2:2|Nur 8-Bit 4:2:0|Ja
Avid DNxHD (in MXF) |Ja|Nein|Ja
DVCPro/DVCProHD (in MXF) |Ja|Nein|Ja
JPEG2000 |Ja|Nein|Ja
MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)|Bis zu 422 Profile|Bis zu 422 Profile|Ja
MPEG-1 |Ja|Ja|Ja
Windows Media Video/VC-1 |Ja|Ja|Ja
Canopus HQ/HQX |Nein|Ja|Nein
MPEG-4 Teil 2 |Ja|Nein|Nein
[Theora](https://en.wikipedia.org/wiki/Theora) |Ja|Nein|Nein

###Codecs für Audioeingang

Codecs für Audioeingang|Media Encoder Standard|Azure Media Encoder|Media Encoder Premium Workflow
---|---|---|---
AES (SMPTE 331M und 302M, AES3-2003) |Nein|Nein|Ja
Dolby® E |Nein|Nein|Ja
Dolby® Digital (AC3) |Nein|Ja|Ja
Dolby® Digital Plus (E-AC3) |Nein|Nein|Ja
AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)|Ja|Ja|Ja
MPEG Layer 2|Ja|Ja|Ja
MP3 (MPEG-1 Audio Layer 3)|Ja|Ja|Ja
Windows Media Audio|Ja|Ja|Ja
WAV/PCM|Ja|Ja|Ja
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Ja|Nein|Nein
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Ja|Nein|Nein
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Ja|Nein|Nein


###Ausgabecontainer/Dateiformate

Ausgabecontainer/Dateiformate|Media Encoder Standard|Azure Media Encoder|Media Encoder Premium Workflow
---|---|---|---
Adobe® Flash® F4V|Nein|Nein|Ja
MXF (OP1a, XDCAM und AS02)|Nein|Nein|Ja
DPP (einschließlich AS11)|Nein|Nein|Ja
GXF|Nein|Nein|Ja
MPEG-4/MP4|Ja|Ja|Ja
MPEG-TS|Ja|Nein|Ja
Windows Media/ASF|Nein|Ja|Ja
AVI (unkomprimiert, 8-Bit/10-Bit)|Nein|Nein|Ja
Smooth Streaming-Dateiformat (PIFF 1.3)|Nein|Ja|Ja

###Codecs für Videoausgang

Codecs für Videoausgang|Media Encoder Standard|Azure Media Encoder|Media Encoder Premium Workflow
---|---|---|---
AVC (H.264; 8-Bit; bis High Profile, Level 5.2; 4K Ultra HD; AVC Intra)|Nur 8-Bit 4:2:0|Nur 8-Bit 4:2:0 bis 1080p|Ja
Avid DNxHD (in MXF)|Nein|Nein|Ja
DVCPro/DVCProHD (in MXF)|Nein|Nein|Ja
MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)|Nein|Nein|Ja
MPEG-1|Nein|Nein|Ja
Windows Media Video/VC-1|Nein|Ja|Ja
Erstellung von JPEG-Miniaturansichten|Nein|Ja|Ja

###Codecs für Audioausgabe

Codecs für Audioausgabe|Media Encoder Standard|Azure Media Encoder|Media Encoder Premium Workflow
---|---|---|---
AES (SMPTE 331M und 302M, AES3-2003)|Nein|Nein|Ja
Dolby® Digital (AC3)|Nein|Ja|Ja
Dolby® Digital Plus (E-AC3) bis 7.1|Nein|Bis zu 5.1|Ja
AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)|Ja|Ja|Ja
MPEG Layer 2|Nein|Nein|Ja
MP3 (MPEG-1 Audio Layer 3)|Nein|Nein|Ja
Windows Media Audio|Nein|Ja|Ja


##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

##Verwandte Artikel

- [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=Sept15_HO2-->