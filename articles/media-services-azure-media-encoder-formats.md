<properties 
	pageTitle="Azure Media Encoder-Formate und -Codecs" 
	description="Dieses Thema bietet eine Übersicht über Azure Media Encoder-Formate und -Codecs" 
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
	ms.date="02/27/2015" 
	ms.author="juliako"/>

# Azure Media Encoder-Formate und -Codecs

Encoder komprimieren digitale Medien mithilfe von Codecs. Encoder bieten üblicherweise verschiedene Einstellungen, mit denen Sie Eigenschaften der generierten Medien angeben können, z. B. verwendete Codecs, Dateiformat, Auflösung und Bitrate. Dateiformate sind Container, die das komprimierte Video sowie Informationen zu den Codecs enthalten, die für dessen Komprimierung verwendet wurden. 

Codecs verfügen über zwei Komponenten: eine zum Komprimieren digitaler Mediendateien für die Übertragung und die andere zum Dekomprimieren digitaler Mediendateien für die Wiedergabe. Für die Komprimierung und Dekomprimierung von Audiodaten existieren Audiocodecs, und für Videodaten entsprechend Videocodecs. Codecs können entweder verlustfreie oder verlustbehaftete Komprimierung verwenden. Verlustfreie Codecs erhalten bei der Komprimierung alle Informationen. Das Ergebnis der Dekomprimierung ist eine Datei, die identisch mit dem Eingabemedium ist. Verlustfreie Codecs eignen sich daher gut für Archivierung und Speicherung. Verlustbehaftete Codecs verlieren beim Komprimieren einen Teil der Informationen, produzieren kleinere Dateien (im Vergleich zum Original) auf Kosten der Videoqualität und eignen sich gut zum Streaming über das Internet. Die zwei Hauptcodecs, die vom Azure Media Encoder zum Codieren verwendet werden, sind H.264 und VC-1. Andere Codecs sind möglicherweise über unser Partner-Ökosystem von Encodern verfügbar.

Es ist wichtig, den Unterschied zwischen Codecs und Dateiformaten zu kennen. Bei Codecs handelt es sich um die Software, die die Algorithmen für die Komprimierung/Dekomprimierung implementiert. Dateiformate dagegen sind die Container, die das komprimierte Video enthalten. Weitere Informationen finden Sie unter [Codierung im Vergleich zur Paketerstellung](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Dieses Dokument enthält eine Liste der gängigsten Import- und Exportdateiformate, die Sie mit dem Azure Media Encoder verwenden können.


[Media Encoder-Importformate](#import_formats)

[Media Encoder-Exportformate](#export_formats)


## <a id="import_formats"></a>Media Encoder-Importformate 

Im folgenden Abschnitt werden die Codecs und Dateiformate aufgelistet, die für den Import unterstützt werden.

### Videocodecs

- H.264 (Baseline, Main und High Profile)
- MPEG-1 (einschließlich MPEG-PS)
- MPEG-2 (Simple und Main Profile und 4:2:2 Profile)
- MPEG-4 v2 (Simple Visual Profile und Advanced Simple Profile)
- VC-1 (Simple, Main und Advanced Profile)
- Windows Media Video (Simple, Main und Advanced Profile)
- DV (DVC, DVHD, DVSD, DVSL)
- Grass Valley HQ/HQX
 
### Audiocodecs

- AC-3 (Dolby Digital-Audio)
- AAC (AAC-LC, HE-AAC v1 mit AAC-LC-Kernfunktionen und HE-AAC v2 mit AAC-LC-Kernfunktionen)
- MP3
- Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professional und Windows Media Audio Lossless)

### Videodateiformate
 
<table border="1">
<tr><th>Dateiformat</th><th>Dateierweiterungen</th></tr>
<tr><td>3GPP, 3GPP2</td><td>.3gp, .3g2, .3gp2</td></tr>
<tr><td>Advanced Systems Format (ASF)</td><td>.asf</td></tr>
<tr><td>Advanced Video Coding High Definition (AVCHD) [MPEG-2-Transportdatenstrom]</td><td>.mts, .m2ts</td></tr>
<tr><td>Audio-Video Interleaved (AVI)</td><td>.avi</td></tr>
<tr><td>Digitaler Camcorder MPEG-2 (MOD)</td><td>.mod</td></tr>
<tr><td>DVD-Transportdatenstrom (TS-Datei)</td><td>.ts</td></tr>
<tr><td>DVD-Videoobjektdatei (VOB)</td><td>.vob</td></tr>
<tr><td>Expression Encoder Screen Capture-Codec-Datei</td><td>.xesc</td></tr>
<tr><td>MP4</td><td>.mp4</td></tr>
<tr><td>MPEG-1-Systemdatenstrom</td><td>.mpeg, .mpg</td></tr>
<tr><td>MPEG-2-Videodatei</td><td>.m2v</td></tr>
<tr><td>Smooth Streaming-Dateiformat (PIFF 1.3)</td><td>.ismv</td></tr>
<tr><td>Windows Media Video (WMV)</td><td>.wmv</td></tr>
</table>

Einige nicht komprimierte Dateiformate werden unterstützt. Weitere Informationen finden Sie unter [Unterstützte nicht komprimierte Videoformate](#uncompressed)

### Audiodateiformate

<table border="1">
<tr><th>Dateiformat</th><th>Dateierweiterungen</th></tr>
<tr><td>AC-3-Audio (Dolby Digital)</td><td>.ac3</td></tr>
<tr><td>Audio Interchange File Format (AIFF)</td><td>.aiff</td></tr>
<tr><td>Broadcast Wave Format</td><td>.bwf</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>.mp3</td></tr>
<tr><td>MP4-Audio</td><td>.m4A</td></tr>
<tr><td>MPEG-4 Audiobook</td><td>.m4b</td></tr>
<tr><td>WAVE-Datei</td><td>.wav</td></tr>
<tr><td>Windows Media Audio</td><td>.wma</td></tr>   
</table>

### Bilddateiformate

<table border="1">
<tr><th>Dateiformat</th><th>Dateierweiterungen</th></tr>
<tr><td>Bitmap</td><td>.bmp</td></tr>
<tr><td>GIF, animiertes GIF</td><td>.gif</td></tr>
<tr><td>JPEG</td><td>.jpeg, .jpg</td></tr>
<tr><td>PNG</td><td>.png</td></tr>
<tr><td>TIFF</td><td>.tif</td></tr>
<tr><td>WPF Canvas XAML</td><td>.xaml</td></tr>
</table>


## <a id="export_formats"></a>Media Encoder-Exportformate

In der folgenden Tabelle werden die Codecs und Dateiformate aufgeführt, die für den Export unterstützt werden.


<table border="1">
<tr><th>Dateiformat</th><th>Videocodec</th><th>Audiocodec</th></tr>
<tr><td>Windows Media (*.wmv; *.wma)</td><td>VC-1 (Advanced, Main und Simple Profile)</td><td>Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless</td></tr>
<tr><td>MP4 (*.mp4)</td><td>H.264 (High, Main und Baseline Profile)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.1) (*.ismv; *.isma)</td><td>VC-1 (Advanced Profile)<br/><br/>
H.264 (High, Main und Baseline Profile)</td><td>Windows Media Audio Standard, Windows Media Audio Professional<br/><br/>
AAC-LC, HE-AAC v1, HE-AAC v2</td></tr>
</table>

Zusätzliche unterstützte Codecs und Filter in Media Services finden Sie unter [Windows DirectShow-Filter](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

## <a id="uncompressed"></a>Unterstützte nicht komprimierte Videoformate 

Azure Media Services stellt Unterstützung für das Importieren nicht komprimierter Videodaten bereit.

Dies ist eine unvollständige Liste der unterstützten nicht komprimierten Formate.

<table border="1">
<tr><th>Nicht komprimiertes Videoformat</th><th>Beschreibung</th></tr>
<tr><td>Nicht komprimierte Daten im YVU9-Standardformat</td><td>Ein planares YUV-Format. Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Ein Y-Sample in jeder vertikalen Zeile, ein U- und V-Sample in jeder vierten vertikalen Zeile. 9 Bits pro Pixel.</td></tr>
<tr><td>Daten im YUV 411-Format</td><td>Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte. Die Bytereihenfolge (das niedrigste zuerst) lautet U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 12 Byte stellt 8 Bildpixel dar.</td></tr>
<tr><td>Daten im Y41P-Format</td><td>Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte. Die Bytereihenfolge (das niedrigste zuerst) ist U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 12 Byte stellt 8 Bildpixel dar.</td></tr>
<tr><td>Daten im YUY2-Format</td><td>Identisch mit UYVY, aber mit einer anderen Pixelreihenfolge. Die Bytereihenfolge (das niedrigste zuerst) lautet Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 4 Byte stellt 2 Bildpixel dar.</td></tr>
<tr><td>Daten im YVYU-Format</td><td>Ein gepacktes YUV-Format. Identisch mit UYVY, aber mit einer anderen Pixelreihenfolge. Die Bytereihenfolge (das niedrigste zuerst) lautet Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 4 Byte stellt 2 Bildpixel dar.</td></tr>
<tr><td>Daten im UYVY-Format</td><td>Ein gepacktes YUV-Format. Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem zweiten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte. Das beliebteste der verschiedenen YUV 4:2:2-Formate. Die Bytereihenfolge (das niedrigste zuerst) lautet U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 4 Byte stellt 2 Bildpixel dar.</td></tr>
<tr><td>Daten im YUV 211-Format</td><td>Ein gepacktes YUV-Format. Ein Y-Sample bei jedem zweiten Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte. Die Bytereihenfolge (das niedrigste zuerst) lautet Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, wobei das Suffix 0 das ganz linke Pixel darstellt und höhere Zahlen die von links nach rechts nummerierten Pixel darstellen. Jeder Block mit 4 Byte stellt 4 Bildpixel dar.</td></tr>
<tr><td>Cirrus Logic Jr YUV 411-Format</td><td>Das Cirrus Logic Jr YUV 411-Format mit weniger als 8 Bit pro Y-, U- und V-Sample. Ein Y-Sample bei jedem Pixel, ein U- und V-Sample bei jedem vierten Pixel horizontal in jeder Zeile. Jede vertikale Zeile enthält Sample-Werte.</td></tr>
<tr><td>Von Indeo generiertes YVU9-Format</td><td>Das von Indeo generierte YVU9-Format mit zusätzlichen Informationen über die Unterschiede zum letzten Frame. 9,5 Bit pro Pixel, wird jedoch als 9 angezeigt.</td></tr>
</table>

<!--HONumber=47-->
