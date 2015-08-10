<properties 
	pageTitle="Media Encoder Standard-Formate und -Codecs" 
	description="Dieses Thema bietet eine Übersicht über Azure Media Encoder Standard-Formate und -Codecs." 
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
	ms.date="07/08/2015" 
	ms.author="juliako"/>

#Media Encoder Standard-Formate und -Codecs


Dieses Dokument enthält eine Liste der gängigsten Import- und Exportdateiformate, die Sie mit Media Encoder Standard verwenden können.


[Media Encoder-Importformate ](#import_formats)

[Media Encoder-Exportformate](#export_formats)


##<a id="import_formats"></a>Media Encoder-Importformate 

Im folgenden Abschnitt werden die Codecs und Dateiformate aufgelistet, die für den Import unterstützt werden.


##Videocodecs:

- MPEG-2
- H.264
- YUV420, nicht komprimiert oder Mezzanine
- DNxHD
- VC-1/WMV9
- MPEG-4 Teil 2
- JPEG 2000
- Theora

###Audiocodecs

- PCM
- AAC \(AAC-LC, AAC-HE und AAC-HEv2\)
- WMA9/Pro
- MP3 \(MPEG-1 Audio Layer 3\)
- FLAC
- Opus
- Vorbis
 
###Formate

<table border="1">
<tr><th>Dateiformat</th><th>Dateierweiterungen</th></tr>
<tr><td>FLV (mit H.264- und AAC-Codecs) </td><td>.flv</td></tr>
<tr><td>MP4/ISMV</td><td>*.ismv</td></tr>
<tr><td>MPEG2-PS, MPEG2-TS, 3GP</td><td>.ts, .ps, .3gp</td></tr>
<tr><td>MXF</td><td>.mxf</td></tr>
<tr><td>WMV/ASF</td><td>.mwv, .asf</td></tr>
<tr><td>DVR-MS</td><td>.dvr-ms </td></tr>
<tr><td>AVI</td><td>.avi</td></tr>
<tr><td>Matroska</td><td>.mkv</td></tr>
<tr><td>GXF</td><td>.gxf</td></tr>
<tr><td>WAVE/WAV </td><td>.wav</td></tr>
</table>

##<a id="export_formats"></a>Media Encoder-Exportformate

In der folgenden Tabelle werden die Codecs und Dateiformate aufgeführt, die für den Export unterstützt werden.


<table border="1">
<tr><th>Dateiformat</th><th>Videocodec</th><th>Audiocodec</th></tr>
<tr><td>MP4 (*.mp4)<br/><br/>(einschließlich Multi-Mitrate-MP4-Container) </td><td>H.264 (High, Main und Baseline Profile)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2 </td></tr>
<tr><td>MPEG2-TS </td><td>H.264 (High, Main und Baseline Profile)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2 </td></tr>
</table>

##Weitere Informationen

[Codieren von On-Demand-Inhalten mit Azure Media Services](media-services-encode-asset.md)

<!---HONumber=July15_HO5-->