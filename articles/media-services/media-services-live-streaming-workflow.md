<properties 
	pageTitle="Bereitstellen von Livestreaming mit Azure Media Services" 
	description="Dieses Thema bietet einen Überblick über die beim Livestreaming beteiligten Hauptkomponenten." 
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
	ms.date="09/28/2015"  
	ms.author="juliako"/>


#Bereitstellen von Livestreamingereignissen mit Azure Media Services

##Übersicht

Beim Arbeiten mit Livestreaming werden normalerweise die folgenden Komponenten verwendet:

- Eine Kamera, mit der ein Ereignis übertragen wird.
- Ein Live-Video-Encoder, der Signale von der Kamera in Datenströme konvertiert, die an einen Livestreaming-Dienst gesendet werden. 
  
	Optional mehrere Live-Encoder. Für bestimmte kritische Liveereignisse, die eine sehr hohe Verfügbarkeit und Quality of Experience erfordern, wird der Einsatz redundanter Aktiv-Aktiv-Encoder empfohlen, um ein nahtloses Failover ohne Datenverlust zu erreichen.
- Ein Livestreaming-Dienst, der Ihnen Folgendes ermöglicht: 
	- Erfassen von Liveinhalten über verschiedene Livestreaming-Protokolle (z. B. RTMP oder Smooth Streaming) 
	- Codieren des Datenstroms in einen Datenstrom mit adaptiver Bitrate
	- Vorschau des Livestreams
	- Speichern des erfassten Inhalts für ein späteres Streaming (Video-on-Demand)
	- Übermitteln der Inhalte über häufig verwendete Streaming-Protokolle (z. B. MPEG DASH, Smooth, HLS, HDS) direkt an die Kunden oder an ein Content Delivery Network (CDN) zur weiteren Verteilung 
	
		
Mit **Microsoft Azure Media Services** (AMS) können Sie Livestreaming-Inhalte erfassen, codieren, in der Vorschau anzeigen, speichern und bereitstellen.

Bei der Übermittlung Ihrer Inhalte für Kunden besteht Ihr Ziel darin, qualitativ hochwertige Videos für unterschiedliche Geräte unter verschiedenen Netzwerkbedingungen bereitzustellen. Damit Qualität und Netzwerkbedingungen sichergestellt werden, codieren Sie den Datenstrom mit Live-Encodern in einen Videodatenstrom mit mehreren Bitraten (adaptive Bitrate). Verwenden Sie die [dynamische Paketerstellung](media-services-dynamic-packaging-overview.md) von Media Services, um den Datenstrom dynamisch erneut in verschiedene Protokolle zu packen. Media Services unterstützt die Übermittlung der folgenden Streamingtechnologien mit adaptiver Bitrate: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (nur mit Adobe PrimeTime/Access-Lizenz).

in Azure Media Services verarbeiten die **Kanäle**, **Programme** und **Streamingendpunkte** alle Livestreaming-Funktionen, einschließlich Erfassung, Formatierung, DVR, Sicherheit, Skalierbarkeit und Redundanz.

Ein **Kanal** stellt eine Pipeline zum Verarbeiten von Livestreaming-Inhalten dar. Derzeit kann ein Kanal Live-Eingabedatenströme auf folgende Weise empfangen:


- Ein lokaler Liveencoder sendet einen Single-Bitrate-Datenstrom an den Kanal, der zum Ausführen von Livecodierung mit Media Services in einem der folgenden Formate aktiviert wurde: RTP (MPEG-TS), RTMP oder Smooth Streaming (fragmentiertes MP4). Vom Kanal wird dann eine Livecodierung des Single-Bitrate-Eingabedatenstroms in einen Multi-Bitrate-Videodatenstrom (adaptiv) ausgeführt. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.

- Von einem lokalen Live-Encoder wird Multi-Bitrate-**RTMP** oder -**Smooth Streaming** (fragmentiertes MP4) an den Kanal gesendet. Sie können die folgenden Liveencoder verwenden, von denen Multi-Bitrate-Smooth Streaming ausgegeben werden kann: Elemental, Envivio, Cisco. Von den folgenden Liveencodern wird RTMP ausgegeben: Adobe Flash Live, Telestream Wirecast und Tricaster-Transcoder. Die aufgenommenen Datenströme werden ohne weitere Verarbeitung durch die **Kanäle** geleitet. Ihr Liveencoder kann auch einen Single-Bitrate-Datenstrom an einen Kanal senden, der nicht für die Live-Codierung aktiviert ist. Dies wird jedoch nicht empfohlen. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.


##Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden


Das folgende Diagramm zeigt die Hauptkomponenten der AMS-Plattform, die in Livestreaming-Workflows beteiligt sind, wenn ein Kanal zum Ausführen der Livecodierung mit Media Services aktiviert ist.

![Liveworkflow][live-overview1]

Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).


##Arbeiten mit Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen


Das folgende Diagramm zeigt die Hauptkomponenten der AMS-Plattform, die am Livestreaming-Workflow beteiligt sind.

![Liveworkflow][live-overview2]

Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen](media-services-manage-channels-overview.md).


##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

##Verwandte Themen

[Media Services-Konzepte](media-services-concepts.md)

[Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services](media-services-fmp4-live-ingest-overview.md)





[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png

[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=Oct15_HO3-->