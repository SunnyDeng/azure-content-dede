<properties 
	pageTitle="Azure Media Encoder-Formate und -Codecs" 
	description="Dieses Thema bietet eine Übersicht über Azure Media Encoder-Formate und -Codecs." 
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
	ms.date="09/05/2015" 
	ms.author="juliako"/>

#Azure Media Encoder-Formate und -Codecs

Dieses Dokument enthält eine Liste der gängigsten Eingabe- und Ausgabedateiformate sowie Codecs, die Sie mit dem Azure Media Encoder verwenden können.


##Eingabedateiformate (Container)
 
Dateiformat (Dateierweiterungen)|Unterstützt
---|---
3GPP, 3GPP2 (.3gp, .3g2, .3gp2) |Ja
Advanced Systems Format (ASF) (.asf) |Ja
Advanced Video Coding High Definition (AVCHD) [MPEG-2-Transportdatenstrom] (.mts, .m2ts) |Ja
Audio-Video Interleaved (AVI) (.avi) |Ja
Digitaler Camcorder MPEG-2 (MOD) (.mod) |Ja
DVD-Transportdatenstrom (TS-Datei) (.ts) |Ja
DVD-Videoobjektdatei (VOB) (.vob) |Ja
Expression Encoder Screen Capture-Codec-Datei (.xesc) |Ja
MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Ja
MPEG-1-Systemdatenstrom (.mpeg, .mpg) |Ja
MPEG-2-Videodatei (.m2v) |Ja
Windows Media Video (WMV) (.wmv) |Ja
AC-3-Audio (Dolby Digital) (.ac3)|Ja
Audio Interchange File Format (AIFF) (.aiff)|Ja
Broadcast Wave Format (.bwf)|Ja
MP3 (MPEG-1 Audio Layer 3) (.mp3)|Ja
MPEG-4 Audiobook (.m4b)|Ja
WAVE-Datei (.wav)|Ja
Windows Media Audio (.wma)|Ja
Adobe® Flash® F4V |Nein		
MXF/SMPTE 377M |Eingeschränkt 
GXF |Nein		 
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Nein
Matroska/WebM |Nein


Einige nicht komprimierte Dateiformate werden unterstützt. Weitere Informationen finden Sie unter [Unterstützte nicht komprimierte Videoformate](#uncompressed).

##Codecs für Videoeingang

Codecs für Videoeingang|Unterstützt
---|--- 
H.264 (Baseline, Main und High Profile) |Ja
AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra |Nur 8-Bit 4:2:0
Avid DNxHD (in MXF) |Nein
DVCPro/DVCProHD (in MXF) |Nein
JPEG2000 |Nein
MPEG-2 (Simple und Main Profile und 4:2:2 Profile) |Bis zu 4:2:2 Profile
MPEG-1 (einschließlich MPEG-PS) |Ja
Windows Media Video/VC-1 |Ja
Canopus HQ/HQX |Ja
MPEG-4 v2 (Simple Visual Profile und Advanced Simple Profile) |Ja
[Theora](https://en.wikipedia.org/wiki/Theora) |Nein
VC-1 (Simple, Main und Advanced Profile) |Ja
Windows Media Video (Simple, Main und Advanced Profile) |Ja
DV (DVC, DVHD, DVSD, DVSL) |Ja
Grass Valley HQ/HQX |Ja
 

##Codecs für Audioeingang

Codecs für Audioeingang|Unterstützt
---|---
AES (SMPTE 331M und 302M, AES3-2003) |Nein
Dolby® E |Nein
Dolby® Digital (AC3) |Ja
Dolby® Digital Plus (E-AC3) |Nein
AAC (AAC-LC, HE-AAC v1 mit AAC-LC-Kernfunktionen und HE-AAC v2 mit AAC-LC-Kernfunktionen; bis 5.1)|Ja
MPEG Layer 2|Ja|Ja|Ja
MP3 (MPEG-1 Audio Layer 3)|Ja
Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professional und Windows Media Audio Lossless) |Ja
WAV/PCM|Ja
[FLAC](https://en.wikipedia.org/wiki/FLAC)|Nein
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Nein
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)|Nein


##Eingabeformate für Bilddateien

Dateiformat (Dateierweiterungen) | Unterstützt
---|---
Bitmap (.bmp) | Ja
GIF, animiertes GIF (.gif)| Ja
JPEG (.jpeg, .jpg)| Ja
PNG (.png)| Ja
TIFF (.tif)| Ja
WPF Canvas XAML (.xaml)| Ja


##Ausgabeformate und Codecs

In der folgenden Tabelle werden die Codecs und Dateiformate aufgeführt, die für den Export unterstützt werden.

Dateiformat|Videocodec|Audiocodec
---|---|---
Windows Media (*.wmv; *.wma)|VC-1 (Advanced, Main und Simple Profile)|Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless
MP4 (*.mp4)|H.264 (High, Main und Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus
Smooth Streaming File Format (PIFF 1.1) (*.ismv; *.isma)|VC-1 (Advanced Profile)<p>H.264 (High, Main und Baseline Profile) |Windows Media Audio Standard, Windows Media Audio Professional<p><p>AAC-LC, HE-AAC v1, HE-AAC v2

Zusätzliche unterstützte Codecs und Filter in Media Services finden Sie unter [DirectShow Filters](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx) (in englischer Sprache).

##<a id="uncompressed"></a>Unterstützte nicht komprimierte Videoformate 

Azure Media Services stellt Unterstützung für das Importieren nicht komprimierter Videodaten bereit.

Dies ist eine unvollständige Liste der unterstützten nicht komprimierten Formate.

Nicht komprimiertes Videoformat|Beschreibung
---|---
Nicht komprimierte Daten im YVU9-Standardformat|Ein planares YUV-Format. Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Ein Y-Sample in jeder vertikalen Zeile, ein U- und V-Sample in jeder vierten vertikalen Zeile. 9 Bits pro Pixel.
Daten im YUV 411-Format|Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte. Die Bytereihenfolge (das niedrigste zuerst) lautet U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 12 Byte stellt 8 Bildpixel dar.
Daten im Y41P-Format|Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte. Die Bytereihenfolge (das niedrigste zuerst) ist U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 12 Byte stellt 8 Bildpixel dar.
Daten im YUY2-Format|Identisch mit UYVY, aber mit einer anderen Pixelreihenfolge. Die Bytereihenfolge (das niedrigste zuerst) lautet Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 4 Byte stellt 2 Bildpixel dar.
Daten im YVYU-Format|Ein gepacktes YUV-Format. Identisch mit UYVY, aber mit einer anderen Pixelreihenfolge. Die Bytereihenfolge (das niedrigste zuerst) lautet Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 4 Byte stellt 2 Bildpixel dar.
Daten im UYVY-Format|Ein gepacktes YUV-Format. Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem zweiten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte. Das beliebteste der verschiedenen YUV 4:2:2-Formate. Die Bytereihenfolge (das niedrigste zuerst) lautet U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 4 Byte stellt 2 Bildpixel dar.
Daten im YUV 211-Format|Ein gepacktes YUV-Format. Ein Y-Sample bei jedem zweiten Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte. Die Bytereihenfolge (das niedrigste zuerst) lautet Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 4 Byte stellt 4 Bildpixel dar.
Cirrus Logic Jr YUV 411-Format|Das Cirrus Logic Jr YUV 411-Format mit weniger als 8 Bit pro Y-, U- und V-Sample. Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte.
Von Indeo generiertes YVU9-Format|Das von Indeo generierte YVU9-Format mit zusätzlichen Informationen über die Unterschiede zum letzten Frame. 9,5 Bit pro Pixel, wird jedoch als 9 angezeigt.


##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

<!---HONumber=Sept15_HO2-->