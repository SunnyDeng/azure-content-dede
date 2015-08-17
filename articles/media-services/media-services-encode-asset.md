<properties 
	pageTitle="Codieren von On-Demand-Inhalten mit Azure Media Services" 
	description="Dieses Thema bietet einen Überblick über die Codierung von On-Demand-Inhalten mit Media Services." 
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
	ms.date="07/23/2015" 
	ms.author="juliako"/>

#Codieren von On-Demand-Inhalten mit Azure Media Services

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md).

##Übersicht

Media Services unterstützen die folgenden Encoder:

- [Media Encoder Standard](#media_encoder_standard)
- [Azure Media Encoder](#azure_media_encoder)
- [Media Encoder Premium Workflow](#media_encoder_premium_workflow)

Im [folgenden Abschnitt](#compare_encoders) werden die Codierfunktionen von unterstützten Encodern verglichen.

Standardmäßig kann jedes Media Services-Konto je eine aktive Codierungsaufgabe gleichzeitig aufweisen. Sie können Einheiten für die Codierung reservieren, mit denen Sie mehrere Codierungsaufgaben gleichzeitig ausführen kennen – jeweils eine für jede reservierte Einheit für die Codierung, die Sie erwerben. Informationen zum Skalieren von Codierungseinheiten finden Sie nachfolgend unter den Themen **Portal** und **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>Media Encoder Standard

[Von Media Encoder Standard unterstützte Formate](media-services-media-encoder-standard-formats.md): Es werden die von **Media Encoder Standard** unterstützten Datei- und Datenstromformate beschrieben.

**Media Encoder Standard** wird mit einer der Encoder-Voreinstellungen konfiguriert, die [hier](http://go.microsoft.com/fwlink/?LinkId=618336) beschrieben sind, oder mit benutzerdefinierten Voreinstellungen, die auf [diesen](http://go.microsoft.com/fwlink/?LinkId=618336) Voreinstellungen basieren.

Weitere Informationen finden Sie in [diesem Blog](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

##<a id="azure_media_encoder"></a>Azure Media Encoder

[Von Media Services Encoder unterstützte Formate](media-services-azure-media-encoder-formats.md): Es werden die von **Azure Media Encoder** unterstützten Datei- und Datenstromformate beschrieben.

**Azure Media Encoder** wird mithilfe von Encoder-Voreinstellungen konfiguriert, die [hier](https://msdn.microsoft.com/library/azure/dn619392.aspx) beschrieben sind. Die eigentlichen Voreinstellungsdateien für Azure Media Encoder finden Sie [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Führen Sie die Codierung mit **Azure Media Encoder** mithilfe des **Azure-Verwaltungsportals**, mithilfe von **.NET**, oder **REST-API** aus.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Weitere verwandte Themen

[Dynamische Paketerstellung](https://msdn.microsoft.com/library/azure/jj889436.aspx): Beschreibt, wie Sie eine Codierung in MP4-Dateien mit adaptiver Bitrate durchführen und Smooth Streaming, Apple HLS oder MPEG-DASH dynamisch unterstützen.

[Steuern von Media Services Encoder-Ausgabedateinamen](https://msdn.microsoft.com/library/azure/dn303341.aspx): Beschreibt die Benennungskonvention für Dateinamen von Azure Media Encoder und die Änderung von Ausgabedateinamen.

[Codieren Ihrer Medien mit Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md): Beschreibt das Codieren von Audiospuren mit Dolby Digital Plus-Codierung.


##<a id="media_encoder_premium_wokrflow"></a>Media Encoder Premium Workflow 

**Hinweis** Der in diesem Thema beschriebene Media Encoder Premium Workflow-Medienprozessor ist in China nicht verfügbar.

[Vom Media Encoder Premium Workflow unterstützte Formate](media-services-premium-workflow-encoder-formats.md): Erläutert die vom **Medienencoder-Premium-Workflow** unterstützten Dateiformate und Codecs.

**Medienencoder-Premium-Workflow** wird mit komplexen Workflows konfiguriert. Workflowdateien können mit dem Tool [Workflow-Designer](media-services-workflow-designer.md) erstellt werden.

Sie können die standardmäßigen Workflowdateien [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows) abrufen. Der Ordner enthält auch Beschreibungen dieser Dateien.

Codieren mit **Media Encoder Premium Workflow** mit **.NET**. Weitere Informationen finden Sie unter [Erweiterte Codierung mit dem Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Vergleich der Encoder

###<a id="billing"></a>Von Encodern verwendetes Abrechnungsverfahren

Medienprozessorname|Geltende Preise|Hinweise
---|---|---
**Windows Azure Media Encoder** |LEGACY ENCODER|Codieraufgaben werden gemäß der Summe der Größen von Eingabemedienobjekt(en) und Ausgabemedienobjekt in GB berechnet, und zwar zur [hier][1] angegebenen Rate in der Spalte LEGACY ENCODER.
**Azure Media Encoder** |ENCODER|Codieraufgaben werden gemäß der Summe der Größen von Ausgabemedienobjekten in GB berechnet, und zwar zur [hier][1] angegebenen Rate in der Spalte ENCODER.
**Media Encoder Standard** |ENCODER|Codieraufgaben werden gemäß der Summe der Größen von Ausgabemedienobjekten in GB berechnet, und zwar zur [hier][1] angegebenen Rate in der Spalte ENCODER.
**Media Encoder Premium Workflow** |PREMIUM ENCODER|Codierungsaufgaben werden gemäß der Größe von Ausgabemedienobjekten in GB angegeben, und zwar zur [hier][1] angegebenen Rate in der Spalte PREMIUM ENCODER.



In diesem Abschnitt werden die Codierungsfunktionen von **Azure Media Encoder**, **Media Encoder-Premium-Workflow** und **Media Encoder Standard** verglichen.


###Eingabeformate

Eingabecontainer/Dateiformate

Eingabecontainer/Dateiformate|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
Adobe® Flash® F4V|Ja|Nein|Ja
MXF/SMPTE 377M|Ja|Eingeschränkt|Ja
GXF|Ja|Nein|Ja
MPEG-2-Transportdatenstrom|Ja|Ja|Ja
MPEG-2-Programmdatenstrom|Ja|Ja|Ja
MPEG-4/MP4|Ja|Ja|Ja
Windows Media/ASF|Ja|Ja|Ja
AVI (unkomprimiert, 8-Bit/10-Bit)|Ja|Ja|Ja
3GPP/3GPP2|Nein|Ja|Ja
Smooth Streaming-Dateiformat (PIFF 1.3)|Nein|Ja|Ja
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Nein|Nein|Ja
Matroska/WebM|Nein|Nein|Ja

Codecs für Videoeingang

Codecs für Videoeingang|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra|Ja|Nur 8-Bit 4:2:0|8-Bit 4:2:0 und 4:2:2
Avid DNxHD (in MXF)|Ja|Nein|Ja
DVCPro/DVCProHD (in MXF)|Ja|Nein|Ja
JPEG2000|Ja|Nein|Ja
MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)|Ja|Bis zu 422 Profile|Bis zu 422 Profile
MPEG-1|Ja|Ja|Ja
Windows Media Video/VC-1|Ja|Ja|Ja
Canopus HQ/HQX|Nein|Ja|Nein
MPEG-4 Teil 2|Nein|Nein|Ja
[Theora](https://en.wikipedia.org/wiki/Theora)|Nein|Nein|Ja

Codecs für Audioeingang

Codecs für Audioeingang|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
AES (SMPTE 331M und 302M, AES3-2003)|Ja|Nein|Nein
Dolby® E|Ja|Nein|Nein
Dolby® Digital (AC3)|Ja|Ja|Nein
Dolby® Digital Plus (E-AC3)|Ja|Nein|Nein
AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)|Ja|Ja|Ja
MPEG Layer 2|Ja|Ja|Ja
MP3 (MPEG-1 Audio Layer 3)|Ja|Ja|Ja
Windows Media Audio|Ja|Ja|Ja
WAV/PCM|Ja|Ja|Ja
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Nein|Nein|Ja
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Nein|Nein|Ja
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Nein|Nein|Ja

###Ausgabeformate

Ausgabecontainer/Dateiformate

Ausgabecontainer/Dateiformate|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
Adobe® Flash® F4V|Ja|Nein|Nein
MXF (OP1a, XDCAM und AS02)|Ja|Nein|Nein
DPP (einschließlich AS11)|Ja|Nein|Nein
GXF|Ja|Nein|Nein
MPEG-4/MP4|Ja|Ja|Ja
MPEG-TS|Ja|Nein|Ja
Windows Media/ASF|Ja|Ja|Nein
AVI (unkomprimiert, 8-Bit/10-Bit)|Ja|Nein|Nein
Smooth Streaming-Dateiformat (PIFF 1.3)|Ja|Ja|Nein

Codecs für Videoausgang

Codecs für Videoausgang|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
AVC (H.264; 8-Bit; bis High Profile, Level 5.2; 4K Ultra HD; AVC Intra)|Ja|Nur 8-Bit 4:2:0 bis 1080p|Nur 8-Bit 4:2:0
Avid DNxHD (in MXF)|Ja|Nein|Nein
DVCPro/DVCProHD (in MXF)|Ja|Nein|Nein
MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)|Ja|Nein|Nein
MPEG-1|Ja|Nein|Nein
Windows Media Video/VC-1|Ja|Ja|Nein
Erstellung von JPEG-Miniaturansichten|Ja|Ja|Nein

Codecs für Audioausgabe

Codecs für Audioausgabe|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
AES (SMPTE 331M und 302M, AES3-2003)|Ja|Nein|Nein
Dolby® Digital (AC3)|Ja|Ja|Nein
Dolby® Digital Plus (E-AC3) bis 7.1|Ja|Bis zu 5.1|Nein
AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)|Ja|Ja|Ja
MPEG Layer 2|Ja|Nein|Nein
MP3 (MPEG-1 Audio Layer 3)|Ja|Nein|Nein
Windows Media Audio|Ja|Ja|Nein

##Verwandte Artikel

- [Introducing Premium Encoding in Azure Media Services (in englischer Sprache)](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [How to Use Premium Encoding in Azure Media Services (in englischer Sprache)](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=August15_HO6-->