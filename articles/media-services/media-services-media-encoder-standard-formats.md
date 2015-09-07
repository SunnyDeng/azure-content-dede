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
	ms.date="08/11/2015"
	ms.author="juliako"/>

#Media Encoder Standard-Formate und -Codecs


Dieses Dokument enthält eine Liste der gängigsten Import- und Exportdateiformate, die Sie mit Media Encoder Standard verwenden können.


[Media Encoder-Importformate ](#import_formats)

[Media Encoder-Exportformate](#export_formats)


##<a id="import_formats"></a>Media Encoder-Standardimportformate 

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
- AAC (AAC-LC, AAC-HE und AAC-HEv2)
- WMA9/Pro
- MP3 (MPEG-1 Audio Layer 3)
- FLAC
- Opus
- Vorbis
 
###Formate

Dateiformat|Dateierweiterungen
---|---
FLV (mit H.264- und AAC-Codecs) |.flv
MP4/ISMV|* .ismv
MPEG2-PS, MPEG2-TS, 3GP|.ts, .ps, .3gp
MXF|.mxf
WMV/ASF|.mwv, .asf
DVR-MS|.dvr-ms 
AVI|.avi
Matroska|.mkv
GXF|.gxf
WAVE/WAV |.wav


##<a id="export_formats"></a>Media Encoder-Standardexportformate

In der folgenden Tabelle werden die Codecs und Dateiformate aufgeführt, die für den Export unterstützt werden.


Dateiformat|Videocodec|Audiocodec
---|---|---
MP4 (* .mp4)<br/><br/>(einschließlich Multi-Bitrate-MP4-Container) |H.264 (High, Main und Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (High, Main und Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 

##Weitere Informationen

[Codieren von On-Demand-Inhalten mit Azure Media Services](media-services-encode-asset.md)

<!---HONumber=August15_HO9-->