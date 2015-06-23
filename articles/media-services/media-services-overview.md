<properties 
	pageTitle="Azure Media Services - Übersicht" 
	description="Dieses Thema bietet eine Übersicht über Azure Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="juliako"/>

#Azure Media Services - Übersicht

Microsoft Azure Media Services ist eine erweiterbare, cloudbasierte Plattform, die Entwicklern das Erstellen von skalierbaren Medienverwaltungslösungen und Bereitstellungsanwendungen ermöglicht. Media Services basiert auf REST-APIs, mit denen Sie auf sichere Weise Video- oder Audioinhalte hochladen, speichern, codieren und paketieren können - sowohl für eine bedarfsgesteuerte als auch für eine Livestreaming-basierte Bereitstellung auf verschiedenen Clients (z. B. TV, PC und mobile Geräte).

Sie können mithilfe von Media Services vollständige End-to-End-Workflows erstellen. Es ist auch möglich, Drittanbieterkomponenten für einige Elemente Ihres Workflows zu nutzen. Beispielsweise können Sie die Codierung mit einem Encoder eines Drittanbieters durchführen. Anschließend sorgen Sie mit Media Services für Upload, Schutz, Paketierung und Bereitstellung.

Zum Entwickeln von Media Services-Lösungen können Sie folgende Komponenten verwenden:

- [Media Services-REST-API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Eines der verfügbaren Client-SDKs: [Azure Media Services-SDK für .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure-SDK für Java](https://github.com/Azure/azure-sdk-for-java), [Azure Media Services für Node.js](https://github.com/fritzy/node-azure-media), [Azure-PHP-SDK](https://github.com/Azure/azure-sdk-for-php)
- Vorhandene Tools: [Azure-Verwaltungsportal](http://manage.windowsazure.com/) oder [Azure Media Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).

**Vereinbarung zum Servicelevel (SLA)**: Media Services gewährleistet eine Verfügbarkeit von 99,9 % für die REST-API-Transaktionen zur Media Services-Codierung. Das bedarfsgesteuerte Streaming sorgt für eine erfolgreiche Verarbeitung von Dienstanforderungen mit 99.9 % Verfügbarkeitsgarantie für vorhandene Medieninhalte, wenn mindestens eine reservierte Einheit für das Streaming erworben wird. Die Verfügbarkeit wird über einen monatlichen Abrechnungszyklus berechnet. Wenn Sie weitere Informationen wünschen, laden Sie das [SLA-Dokument](https://www.microsoft.com/download/details.aspx?id=39302) herunter.

Das folgende Poster zeigt Azure Media Services-Workflows, von der Medienerstellung bis zur Nutzung. Sie können das Poster hier herunterladen: [Azure Media Services-Poster](http://www.microsoft.com/download/details.aspx?id=38195).

![Übersicht][overview]

##Konzepte

Weitere Informationen finden Sie unter [Konzepte](media-services-concepts.md)

##Auswählen der Medienfunktionen

Einer der ersten Schritte beim Freigeben von Videoinhalten ist die Entscheidung, welche Art von Benutzererlebnis Sie Ihren Kunden bieten möchten. Wie zeigen Ihre Kunden die Videoinhalte an? Verfügen Sie über eine Internetverbindung? Werden die Inhalte auf einem Computer oder einem Handheldgerät angezeigt? Erwarten Ihre Kunden Videos in HD-Qualität? Fragen wie diese können Sie dabei unterstützen, Ihren Kunden ein optimales Benutzererlebnis zu bieten.

###Videozugriff
 
Es gibt vier grundlegende Methoden, über die Ihre Kunden auf Videos zugreifen können:

- Offlineanzeige 
- Progressiver Download
- Streaming/Streaming mit adaptiven Bitraten

####Offlineanzeige

Für die Offlineanzeige eines Videos lädt ein Kunde das gesamte Video auf seinen Computer oder das Gerät herunter. Videos sind meistens recht groß. Bis zum Abschluss des Downloads kann es daher einige Zeit dauern. Der Vorteil der Offlineanzeige besteht darin, dass keine Netzwerkverbindung zum Anzeigen des Videos erforderlich ist, nachdem es auf das Gerät heruntergeladen wurde. 

####Progressiver Download

Für den progressiven Download ist es erforderlich, dass der Kunde über eine Internetverbindung verfügt. Die Wiedergabe des Videos kann bereits gestartet werden, bevor das gesamte Video heruntergeladen wurde. Für beide Verfahren ist es erforderlich, dass das vom Kunden zum Anzeigen des Videos verwendete Gerät über ausreichend Speicherplatz zum Speichern des gesamten Videos verfügt.

####Streaming

Streamingtechnologien erfordern ebenfalls eine Internetverbindung. Mit ihnen wird jedoch jeweils nur ein kleiner Teil des Video heruntergeladen und nach der Wiedergabe wieder verworfen. Auf diese Weise wird nur sehr wenig Speicherplatz auf dem Anzeigegerät benötigt. Der Durchsatz einer Netzwerkverbindung kann unterschiedlich sein. Kunden erwarten jedoch unabhängig von der Netzwerkbandbreite, Videos anzeigen zu können. Bei adaptiven Bitratentechnologien können Videoplayeranwendungen die Netzwerkbedingungen ermitteln und eine Auswahl aus mehreren Bitraten treffen. Wenn die Netzwerkleistung absinkt, kann der Client eine niedrigere Bitrate auswählen, und der Player kann die Wiedergabe des Videos mit einer geringeren Videoqualität fortsetzen. Verbessert sich die Netzwerkleistung, kann der Client auf eine höhere Bitrate umschalten und eine verbesserte Videoqualität anbieten. Azure Media Services unterstützt die folgenden Technologien mit adaptiven Bitraten: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS.

###Anzeigegeräte

Eine weitere Entscheidung, die getroffen werden muss, bezieht sich auf die Gerätetypen, die Ihr Kunde zur Wiedergabe der Videos verwendet. Media Services bietet Unterstützung für Webbrowser, Smartphones, Tablets, XBox, Set-Top-Boxen und Fernseher mit Internetverbindung.

####Webbrowser

Webbrowser können auf Windows-PCs, Macintosh-PCs und Smartphones ausgeführt werden. Bei der Ausführung auf PCs oder Macintosh-PCs können Sie die Vorteile des großformatigen Bildschirms und der hohen Speicherkapazität nutzen. Auf diese Weise können Sie Videos mit höherer Qualität streamen. Windows-PCs oder Macintosh-PCs können von Media Services bereitgestellte Videos mithilfe einer systemeigenen Anwendung oder eines HTML-kompatiblen Webbrowsers wiedergeben. Systemeigene Anwendungen können Smooth Streaming, Apple HLS, einen progressiven Download oder die Offlineanzeige unterstützen. HTML5-Webseiten unterstützen den progressiven Download.


####Smartphones

Smartphones verfügen über kleine Bildschirme und geringere Speicherkapazitäten. Streaming ist die beste Wahl für diese Geräte. Es werden iPhones, Windows Phones und Android-Telefone unterstützt. iPhones und Android-Telefone unterstützen Smooth Streaming und HLS. Windows Phone bieten Unterstützung für Smooth Streaming.

###Tablets

Tablets besitzen größere Bildschirme als Smartphones, weisen in der Regel jedoch eine kleinere Speicherkapazität auf. Streaming ist daher die beste Wahl für Tablets. Tablets mit größeren Speicherkapazitäten können auch die Offlineanzeige sowie den progressiven Download nutzen.

####XBox

XBox-Konsolen verfügen über große Bildschirme und eine größere Speicherkapazität. Sie eignen sich daher für Offlineanzeige, progressiven Download und Streaming.
Set-Top-Boxen und Fernseher mit Internetverbindung
Diese Geräte besitzen ebenfalls einen großen Bildschirm, jedoch nur minimale Speicherkapazität. Für sie eignet sich Streaming am besten.

###Unterstützte Technologien nach Gerät

Die folgende Tabelle zeigt jeden Gerätetyp sowie die Clienttechnologien, die von Media Services unterstützt werden:
 
<table border="1">
<tr><th>Gerät</th><th>Technologien</th></tr>
<tr><td>iOS</td><td>Smooth Streaming, Apple HLS, progressiver Download</td></tr>
<tr><td>Windows Phone 8</td><td>Smooth Streaming</td></tr>
<tr><td>Windows RT</td><td>Smooth Streaming</td></tr>
<tr><td>Windows</td><td>Smooth Streaming, progressiver Download</td></tr>
<tr><td>Android-Telefone</td><td>Smooth Streaming und Apple HLS</td></tr>
<tr><td>XBox</td><td>Smooth Streaming</td></tr>
<tr><td>Macintosh</td><td>Apple HLS, progressiver Download</td></tr>
<tr><td>Set-Top-Box, Fernseher mit Internetverbindung</td><td>Smooth Streaming, Apple HLS, progressiver Download</td></tr>
</table>


##Bereitstellen von Medien-on-Demand mit Azure Media Services

Weitere Informationen finden Sie unter [Media Services-VoD-Workflow](media-services-video-on-demand-workflow.md).

##Bereitstellen von Livestreaming mit Azure Media Services

Weitere Informationen finden Sie unter [Media Services: Livestreaming-Workflow](media-services-live-streaming-workflow.md). Dort finden Sie auch Links zu Themen, in denen die oben genannten Aufgaben beschrieben werden.

##Clients

Azure Media Services stellt die Tools zur Verfügung, die zum Erstellen leistungsstarker, dynamischer Clientplayeranwendungen für die gängigsten Plattformen erforderlich sind, darunter: iOS-Geräte, Android-Geräte, Windows, Windows Phone, Xbox und Set-Top-Boxen.

- [Smooth Streaming Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Microsoft Media-Plattform: Player Framework](http://playerframework.codeplex.com/) 
- [HTML5 Player Framework-Dokumentation](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Microsoft Smooth Streaming-Plug-In für OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Media Player Framework für iOS](https://github.com/Azure/azure-media-player-framework) 
- [Lizenzierung des Microsoft(r) Smooth Streaming Client Porting Kit](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- Erstellen von Videoanwendungen unter Windows 8 
- [Entwickeln von Videoanwendungen für Xbox](http://xbox.create.msdn.com/) 

Weitere Informationen finden Sie unter [Entwickeln von Videoplayeranwendungen](media-services-develop-video-players.md).

##Muster & Leitfäden

[Muster & Leitfäden](https://wamsg.codeplex.com/)
[Onlinedokumentation](https://msdn.microsoft.com/library/dn735912.aspx)
[Herunterladbares E-Book](https://www.microsoft.com/download/details.aspx?id=42629)

##Support

Der [Azure-Support](http://azure.microsoft.com/support/options/) bietet Supportoptionen für Azure, Media Services eingeschlossen.



<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png


<!--HONumber=52--> 