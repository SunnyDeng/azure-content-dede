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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Codieren von On-Demand-Inhalten mit Azure Media Services

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md).

##Übersicht

Media Services unterstützen die folgenden Encoder:

- [Azure Media Encoder](#azure_media_encoder)
- [Medienencoder-Premium-Workflow](#media_encoder_premium_workflow) (öffentliche Vorschau)

Im [folgenden Abschnitt](#compare_encoders) werden die Codierfunktionen beider Encoder verglichen.

Standardmäßig kann jedes Media Services-Konto je eine aktive Codierungsaufgabe gleichzeitig aufweisen. Sie können Einheiten für die Codierung reservieren, mit denen Sie mehrere Codierungsaufgaben gleichzeitig ausführen kennen – jeweils eine für jede reservierte Einheit für die Codierung, die Sie erwerben. Informationen zum Skalieren von Codierungseinheiten finden Sie nachfolgend unter den Themen **Portal** und **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Azure Media Encoder

[Von Media Services Encoder unterstützte Formate](media-services-azure-media-encoder-formats.md): Erläutert die von **Azure Media Encoder** unterstützten Datei- und Streamformate.

**Azure Media Encoder** wird mithilfe von Encoder-Voreinstellungen konfiguriert, die [hier](https://msdn.microsoft.com/library/azure/dn619392.aspx) beschrieben werden. Die eigentlichen Voreinstellungsdateien für Azure Media Encoder finden Sie [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Führen Sie die Codierung mit **Azure Media Encoder** mithilfe des **Azure-Verwaltungsportals**, mithilfe von **.NET**, oder **REST-API** aus.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Weitere verwandte Themen

[Dynamische Paketerstellung](https://msdn.microsoft.com/library/azure/jj889436.aspx) – Beschreibt, wie Sie eine Codierung in MP4-Dateien mit adaptiver Bitrate durchführen und Smooth Streaming, Apple HLS oder MPEG-DASH dynamisch unterstützen.

[Steuern von Media Services Encoder-Ausgabedateinamen](https://msdn.microsoft.com/library/azure/dn303341.aspx): Beschreibt die Dateinamenskonvention von Azure Media Encoder und wie Sie die Ausgabedateinamen ändern.

[Codieren Ihrer Medien mit Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md): Beschreibt das Codieren von Audiospuren mit Dolby Digital Plus-Codierung.


##<a id="media_encoder_premium_wokrflow"></a>Medienencoder-Premium-Workflow (öffentliche Vorschau)

**Hinweis** Der in diesem Thema beschriebene Media Encoder Premium Workflow-Medienprozessor ist in China nicht verfügbar.

[Vom Medienencoder-Premium-Workflow unterstützte Formate](media-services-premium-workflow-encoder-formats.md): Erläutert die vom **Medienencoder-Premium-Workflow** unterstützten Dateiformate und Codecs.

**Medienencoder-Premium-Workflow** ist mit komplexen Workflows konfiguriert. Workflowdateien können mit dem Tool [Workflow-Designer](media-services-workflow-designer.md) erstellt werden.

Sie können die standardmäßigen Workflowdateien [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows) abrufen. Der Ordner enthält auch Beschreibungen dieser Dateien.

Codieren mit **Medienencoder-Premium-Workflow** mit **.NET**. Weitere Informationen finden Sie unter [Erweiterte Codierung mit dem Medienencoder-Premium-Workflow](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Vergleich der Encoder

In diesem Abschnitt werden die Codierungsfunktionen von **Azure Media Encoder** und **Medienencoder-Premium-Workflow** verglichen.

###Eingabeformate

Eingabecontainer/Dateiformate

<table border="1">
<tr><th>Eingabecontainer/Dateiformate</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Ja</td><td>Eingeschränkt</td></tr>
<tr><td>GXF</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MPEG-2-Transportdatenstrom</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG-2-Programmdatenstrom</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG-4/MP4</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Windows Media/ASF</td><td>Ja</td><td>Ja</td></tr>
<tr><td>AVI (unkomprimiert, 8-Bit/10-Bit)</td><td>Ja</td><td>Ja</td></tr>
<tr><td>3GPP/3GPP2</td><td>Nein</td><td>Ja</td></tr>
<tr><td>Smooth Streaming-Dateiformat (PIFF 1.3)</td><td>Nein</td><td>Ja</td></tr>
</table>

Codecs für Videoeingang

<table border="1">
<tr><th>Codecs für Videoeingang</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra</td><td>Ja</td><td>Nur 8-Bit 4:2:0</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>JPEG2000</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)</td><td>Ja</td><td>Bis zu 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Canopus HQ/HQX</td><td>Nein</td><td>Ja</td></tr>
</table>

Codecs für Audioeingang

<table border="1">
<tr><th>Codecs für Audioeingang</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M und 302M, AES3-2003)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Dolby® E</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG Layer 2</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Windows Media Audio</td><td>Ja</td><td>Ja</td></tr>
<tr><td>WAV/PCM</td><td>Ja</td><td>Ja</td></tr>
</table>

###Ausgabeformate

Ausgabecontainer/Dateiformate

<table border="1">
<tr><th>Ausgabecontainer/Dateiformate</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MXF (OP1a, XDCAM und AS02)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>DPP (einschließlich AS11)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>GXF</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MPEG-4/MP4</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Windows Media/ASF</td><td>Ja</td><td>Ja</td></tr>
<tr><td>AVI (unkomprimiert, 8-Bit/10-Bit)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Smooth Streaming-Dateiformat (PIFF 1.3)</td><td>Ja</td><td>Ja</td></tr>
</table>

Codecs für Videoausgang

<table border="1">
<tr><th>Codecs für Videoausgang</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC (H.264; 8-Bit; bis High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Ja</td><td>Nur 8-Bit 4:2:0 bis zu 1080p</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MPEG-1</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Erstellung von JPEG-Miniaturansichten</td><td>Ja</td><td>Ja</td></tr>
</table>

Codecs für Audioausgabe

<table border="1">
<tr><th>Codecs für Audioausgabe</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M und 302M, AES3-2003)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Ja</td><td>Ja</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3) bis 7.1</td><td>Ja</td><td>Bis zu 5.1</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)</td><td>Ja</td><td>Ja</td></tr>
<tr><td>MPEG Layer 2</td><td>Ja</td><td>Nein</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Ja</td><td>Nein</td></tr>
<tr><td>Windows Media Audio</td><td>Ja</td><td>Ja</td></tr>
</table>
##Verwandte Artikel

- [Introducing Premium Encoding in Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services) ("Einführung in die Premium-Codierung in Azure Media Services", in englischer Sprache)
- [How to Use Premium Encoding in Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services) ("Verwenden der Premium-Codierung in Azure Media Services", in englischer Sprache)
- [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO2-->