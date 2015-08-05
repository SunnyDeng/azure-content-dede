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
	ms.date="07/16/2015" 
	ms.author="juliako"/>

#Codieren von On-Demand-Inhalten mit Azure Media Services

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md).

##Übersicht

Media Services unterstützen die folgenden Encoder:

- [Media Encoder Standard](#media_encoder_standard)
- [Azure Media Encoder](#azure_media_encoder)
- [Medienencoder-Premium-Workflow](#media_encoder_premium_workflow) (öffentliche Vorschau)

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


##<a id="media_encoder_premium_wokrflow"></a>Medienencoder-Premium-Workflow (Vorschau)

**Hinweis** Der in diesem Thema beschriebene Media Encoder Premium Workflow-Medienprozessor ist in China nicht verfügbar.

[Vom Media Encoder Premium Workflow unterstützte Formate](media-services-premium-workflow-encoder-formats.md): Erläutert die vom **Medienencoder-Premium-Workflow** unterstützten Dateiformate und Codecs.

**Medienencoder-Premium-Workflow** wird mit komplexen Workflows konfiguriert. Workflowdateien können mit dem Tool [Workflow-Designer](media-services-workflow-designer.md) erstellt werden.

Sie können die standardmäßigen Workflowdateien [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows) abrufen. Der Ordner enthält auch Beschreibungen dieser Dateien.

Codieren mit **Media Encoder Premium Workflow** mit **.NET**. Weitere Informationen finden Sie unter [Erweiterte Codierung mit dem Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Vergleich der Encoder

###<a id="billing"></a>Von Encodern verwendetes Abrechnungsverfahren

<table border="1">
<tr><th>Medienprozessorname</th><th>Geltende Preise</th><th>Hinweise</th></tr>
<tr><td><b>Windows Azure Media Encoder</b></td><td>LEGACY ENCODER</td><td>Codieraufgaben werden gemäß der Summe der Größen von Eingabemedienobjekten und Ausgabemedienobjekten in GB berechnet, und zwar zur <a href="http://azure.microsoft.com/pricing/details/media-services/">hier</a> angegebenen Rate in der Spalte LEGACY ENCODER.</td></tr>
<tr><td><b>Azure Media Encoder</b></td><td>ENCODER</td><td>Codieraufgaben werden gemäß der Summe der Größen von Ausgabemedienobjekten in GB berechnet, und zwar zur <a href="http://azure.microsoft.com/pricing/details/media-services/">hier</a> angegebenen Rate in der Spalte ENCODER.</td></tr>
<tr><td><b>Media Encoder Standard</b></td><td>ENCODER</td><td>Codieraufgaben werden gemäß der Summe der Größen von Ausgabemedienobjekten in GB berechnet, und zwar zur <a href="http://azure.microsoft.com/pricing/details/media-services/">hier</a> angegebenen Rate in der Spalte ENCODER.</td></tr>
<tr><td><b>Media Encoder Premium Workflow</b></td><td>PREMIUM ENCODER</td><td>Codierungsaufgaben werden gemäß der Größe von Ausgabemedienobjekten in GB angegeben, und zwar zur <a href="http://azure.microsoft.com/pricing/details/media-services/">hier</a> angegebenen Rate in der Spalte PREMIUM ENCODER.</td></tr>
</table>


In diesem Abschnitt werden die Codierungsfunktionen von **Azure Media Encoder**, **Media Encoder-Premium-Workflow** und **Media Encoder Standard** verglichen.


###Eingabeformate

Eingabecontainer/Dateiformate

<table border="1">
<tr><th>Eingabecontainer/Dateiformate</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th><th>Media Encoder Standard</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Ja</td><td>Nein</td><td>Ja</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Ja</td><td>Eingeschränkt</td><td>Ja</td></tr>
<tr><td>GXF</td><td>Ja</td><td>Nein</td><td>Ja</td></tr>
<tr><td>MPEG-2-Transportdatenstrom</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG-2-Programmdatenstrom</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG-4/MP4</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Windows Media/ASF</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>AVI (unkomprimiert, 8-Bit/10-Bit)</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>3GPP/3GPP2</td><td>Nein</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Smooth Streaming-Dateiformat (PIFF 1.3)</td><td>Nein</td><td>Ja</td><td>Ja</td></tr>
<tr><td><a href="https://msdn.microsoft.com/de-de/library/windows/desktop/dd692984(v=vs.85).aspx">Microsoft Digital Video Recording (DVR-MS)</a></td><td>Nein</td><td>Nein</td><td>Ja</td></tr>
<tr><td>Matroska/WebM</td><td>Nein</td><td>Nein</td><td>Ja</td></tr></table>

Codecs für Videoeingang

<table border="1">
<tr><th>Codecs für Videoeingang</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder</th><th>Media Encoder Standard</th></tr>
<tr><td>AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra</td><td>Ja</td><td>Nur 8-Bit 4:2:0</td><td>8-Bit 4:2:0 und 4:2:2</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Ja</td><td>Nein</td><td>Ja</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Ja</td><td>Nein</td><td>Ja</td></tr>
<tr><td>JPEG2000</td><td>Ja</td><td>Nein</td><td>Ja</td></tr>
<tr><td>MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)</td><td>Ja</td><td>Bis zu 422 Profile</td><td>Bis zu 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Canopus HQ/HQX</td><td>Nein</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MPEG-4 Teil&#160;2</td><td>Nein</td><td>Nein</td><td>Ja</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Theora">Theora</a></td><td>Nein</td><td>Nein</td><td>Ja</td></tr>
</table>

Codecs für Audioeingang

<table border="1">
<tr><th>Codecs für Audioeingang</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder</th><th>Media Encoder Standard</th></tr>
<tr><td>AES (SMPTE 331M und 302M, AES3-2003)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>Dolby® E</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Ja</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG Layer 2</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Windows Media Audio</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>WAV/PCM</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/FLAC">FLAC</a></td><td>Nein</td><td>Nein</td><td>Ja</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Opus_(audio_format)">Opus</a></td><td>Nein</td><td>Nein</td><td>Ja</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Vorbis">Vorbis</a></td><td>Nein</td><td>Nein</td><td>Ja</td></tr>
</table>

###Ausgabeformate

Ausgabecontainer/Dateiformate

<table border="1">
<tr><th>Ausgabecontainer/Dateiformate</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder</th><th>Media Encoder Standard</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>MXF (OP1a, XDCAM und AS02)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>DPP (einschließlich AS11)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>GXF</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>MPEG-4/MP4</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG-TS</td><td>Ja</td><td>Nein</td><td>Ja</td></tr>
<tr><td>Windows Media/ASF</td><td>Ja</td><td>Ja</td><td>Nein</td></tr>
<tr><td>AVI (unkomprimiert, 8-Bit/10-Bit)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>Smooth Streaming-Dateiformat (PIFF 1.3)</td><td>Ja</td><td>Ja</td><td>Nein</td></tr>
</table>

Codecs für Videoausgang

<table border="1">
<tr><th>Codecs für Videoausgang</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder</th><th>Media Encoder Standard</th></tr>
<tr><td>AVC (H.264; 8-Bit; bis High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Ja</td><td>Nur 8-Bit 4:2:0 bis 1080p</td><td>Nur 8-Bit 4:2:0</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>MPEG-1</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Ja</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Erstellung von JPEG-Miniaturansichten</td><td>Ja</td><td>Ja</td><td>Nein</td></tr>
</table>

Codecs für Audioausgabe

<table border="1">
<tr><th>Codecs für Audioausgabe</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder</th><th>Media Encoder Standard</th></tr>
<tr><td>AES (SMPTE 331M und 302M, AES3-2003)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Ja</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3) bis 7.1</td><td>Ja</td><td>Bis zu 5.1</td><td>Nein</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)</td><td>Ja</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG Layer 2</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Ja</td><td>Nein</td><td>Nein</td></tr>
<tr><td>Windows Media Audio</td><td>Ja</td><td>Ja&lt;/td<td>Nein</td></tr>
</table>

##Verwandte Artikel

- [Introducing Premium Encoding in Azure Media Services (in englischer Sprache)](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [How to Use Premium Encoding in Azure Media Services (in englischer Sprache)](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO4-->