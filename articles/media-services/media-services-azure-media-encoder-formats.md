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
	ms.date="08/11/2015" 
	ms.author="juliako"/>

#Azure Media Encoder-Formate und -Codecs

Encoder komprimieren digitale Medien mithilfe von Codecs. Encoder bieten üblicherweise verschiedene Einstellungen, mit denen Sie Eigenschaften der generierten Medien angeben können, z. B. verwendete Codecs, Dateiformat, Auflösung und Bitrate. Dateiformate sind Container, die das komprimierte Video sowie Informationen zu den Codecs enthalten, die für dessen Komprimierung verwendet wurden.

Codecs verfügen über zwei Komponenten: eine zum Komprimieren digitaler Mediendateien für die Übertragung und die andere zum Dekomprimieren digitaler Mediendateien für die Wiedergabe. Für die Komprimierung und Dekomprimierung von Audiodaten existieren Audiocodecs, und für Videodaten entsprechend Videocodecs. Codecs können entweder verlustfreie oder verlustbehaftete Komprimierung verwenden. Verlustfreie Codecs erhalten bei der Komprimierung alle Informationen. Das Ergebnis der Dekomprimierung ist eine Datei, die identisch mit dem Eingabemedium ist. Verlustfreie Codecs eignen sich daher gut für Archivierung und Speicherung. Verlustbehaftete Codecs verlieren beim Komprimieren einen Teil der Informationen, produzieren kleinere Dateien (im Vergleich zum Original) auf Kosten der Videoqualität und eignen sich gut zum Streaming über das Internet. Die zwei Hauptcodecs, die vom Azure Media Encoder zum Codieren verwendet werden, sind H.264 und VC-1. Andere Codecs sind möglicherweise über unser Partner-Ökosystem von Encodern verfügbar

Es ist wichtig, den Unterschied zwischen Codecs und Dateiformaten zu kennen. Bei Codecs handelt es sich um die Software, die die Algorithmen für die Komprimierung/Dekomprimierung implementiert. Dateiformate dagegen sind die Container, die das komprimierte Video enthalten. Weitere Informationen finden Sie unter [Codierung im Vergleich zur Paketerstellung](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Dieses Dokument enthält eine Liste der gängigsten Import- und Exportdateiformate, die Sie mit dem Azure Media Encoder verwenden können.


[Azure Media Encoder-Importformate](#import_formats)

[Azure Media Encoder-Exportformate](#export_formats)


##<a id="import_formats"></a>Azure Media Encoder-Importformate 

Im folgenden Abschnitt werden die Codecs und Dateiformate aufgelistet, die für den Import unterstützt werden.

###Videocodecs

- H.264 (Baseline, Main und High Profile)
- MPEG-1 (einschließlich MPEG-PS)
- MPEG-2 (Simple und Main Profile und 4:2:2 Profile)
- MPEG-4 v2 (Simple Visual Profile und Advanced Simple Profile)
- VC-1 (Simple, Main und Advanced Profile)
- Windows Media Video (Simple, Main und Advanced Profile)
- DV (DVC, DVHD, DVSD, DVSL)
- Grass Valley HQ/HQX
 
###Audiocodecs

- AC-3 (Dolby Digital-Audio)
- AAC (AAC-LC, HE-AAC v1 mit AAC-LC-Kernfunktionen und HE-AAC v2 mit AAC-LC-Kernfunktionen)
- MP3
- Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professional und Windows Media Audio Lossless)

###Videodateiformate
 
Dateiformat|Dateierweiterungen
---|---
3GPP, 3GPP2|.3gp, .3g2, .3gp2
Advanced Systems Format (ASF)|.asf
Advanced Video Coding High Definition (AVCHD) [MPEG-2-Transportdatenstrom]|.mts, .m2ts
Audio-Video Interleaved (AVI)|.avi
Digitaler Camcorder MPEG-2 (MOD)|.mod
DVD-Transportdatenstrom (TS-Datei)|.ts
DVD-Videoobjektdatei (VOB)|.vob
Expression Encoder Screen Capture-Codec-Datei|.xesc
MP4|.mp4
MPEG-1-Systemdatenstrom|.mpeg, .mpg
MPEG-2-Videodatei|.m2v
Smooth Streaming-Dateiformat (PIFF 1.3)|.ismv
Windows Media Video (WMV)|.wmv


Einige nicht komprimierte Dateiformate werden unterstützt. Weitere Informationen finden Sie unter [Unterstützte nicht komprimierte Videoformate](#uncompressed).

###Audiodateiformate

Dateiformat|Dateierweiterungen
---|---
AC-3-Audio (Dolby Digital)|.ac3
Audio Interchange File Format (AIFF)|.aiff
Broadcast Wave Format|.bwf
MP3 (MPEG-1 Audio Layer 3)|.mp3
MP4-Audio|.m4A
MPEG-4 Audiobook|.m4b
WAVE-Datei|.wav
Windows Media Audio|.wma

###Bilddateiformate

Dateiformat|Dateierweiterungen
---|---
Bitmap|.bmp
GIF, animiertes GIF|.gif
JPEG|.jpeg, .jpg
PNG|.png
TIFF|.tif
WPF Canvas XAML|.xaml


##<a id="export_formats"></a>Azure Media Encoder-Exportformate

In der folgenden Tabelle werden die Codecs und Dateiformate aufgeführt, die für den Export unterstützt werden.


Dateiformat|Videocodec|Audiocodec
---|---|---
Windows Media (*.wmv; *.wma)|VC-1 (Advanced, Main und Simple Profile)|Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless
MP4 (*.mp4)|H.264 (High, Main und Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus
Smooth Streaming File Format (PIFF 1.1) (*.ismv; *.isma)|VC-1 (Advanced Profile)<p>H.264 (High, Main und Baseline Profile) |Windows Media Audio Standard, Windows Media Audio Professional<p><p>AAC-LC, HE-AAC v1, HE-AAC v2

Zusätzliche unterstützte Codecs und Filter in Media Services finden Sie unter [Windows DirectShow-Filter](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

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

<!---HONumber=August15_HO7-->