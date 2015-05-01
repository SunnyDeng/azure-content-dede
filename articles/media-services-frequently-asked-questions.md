<properties 
	pageTitle="Häufig gestellte Fragen" 
	description="Häufig gestellte Fragen (FAQs)" 
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
	ms.date="03/15/2015" 
	ms.author="juliako"/>


#Häufig gestellte Fragen  

##Übersicht

F: Wie wird die Indizierung skaliert?

A: Die reservierten Einheiten für Codierungs- und Indizierungsaufgaben sind identisch. Folgen Sie den Anweisungen unter [Skalieren von für die Codierung reservierten Einheiten](media-services-how-to-scale.md). **Hinweis**: Die Indizierungsleistung für Typen reservierter Einheiten ist nicht herabgesetzt.

F: Ich habe ein Video hochgeladen, codiert und veröffentlicht. Was könnte die Ursache dafür sein, dass das Video nicht wiedergegeben wird, wenn ich versuche, es zu streamen? 

A: Einer der häufigsten Gründe ist, dass Sie für den Streamingendpunkt, von dem Sie die Wiedergabe versuchen, nicht über mindestens eine reservierte Einheit für das Streaming verfügen.  Folgen Sie den Anweisungen unter [Skalieren von reservierten Einheiten für das Streaming](media-services-how-to-scale.md).

F: Ist ein Zusammensetzen bei einem Livestream möglich? 

A: Das Zusammensetzen (Compositing) von Livestreams wird derzeit nicht in Azure Media Services angeboten, Sie müssen dies daher im Vorfeld auf Ihrem Computer durchführen.

F: Kann ich das Azure CDN für das Livestreaming verwenden? 

A: Media Services unterstützt die Integration im Azure CDN. (Weitere Informationen finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md#enable_cdn)).  Sie können Livestreaming mit CDN verwenden. Azure Media Services ermöglicht die Smooth Streaming-, HLS- und MPEG-DASH-Ausgabe. Alle diese Formate verwenden HTTP zum Übertragen von Daten und bieten damit die Vorteile der HTTP-Zwischenspeicherung. Beim Livestreaming werden die eigentlichen Video- oder Audiodaten in Fragmente aufgeteilt, da dann einzeln im CDN zwischengespeichert werden. Die einzigen zu aktualisierenden Daten sind die Manifestdaten. CDN aktualisiert die Manifestdaten regelmäßig.

F: Unterstützt Azure Media Services das Speichern von Bildern?

A: Wenn Sie nur JPEG- oder PNG-Bilder speichern möchten, sollten Sie diese im Azure Blob-Speicher belassen. Es ergeben sich keine Vorteile daraus, sie in Ihrem Media Services-Konto zu speichern, außer Sie möchten sie zusammen mit Ihren Video- oder Audio-Medienobjekten aufbewahren. Dies gilt auch, wenn Sie die Bilder als Overlays im Video-Encoder verwenden möchten. Media Services Encoder unterstützt das Überlagern von Videos mit Bildern - deshalb werden JPEG und PNG auch als unterstützte Eingabeformate angegeben. Weitere Informationen finden Sie unter [Erstellen von Überlagerungen](https://msdn.microsoft.com/library/azure/dn640496.aspx).


<!--HONumber=52-->