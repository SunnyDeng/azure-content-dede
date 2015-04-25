<properties 
	pageTitle="Wiedergabe von Inhalten" 
	description="In diesem Thema wird beschrieben, wie Sie Inhalte wiedergeben." 
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
	ms.date="02/16/2015" 
	ms.author="juliako"/>


# Wiedergabe von Inhalten

Azure Media Services unterstützt zahlreiche gängige Streamingformate wie Smooth Streaming, HTTP Live Streaming und MPEG-Dash. In diesem Thema finden Sie Verweise auf vorhandene Player, die Sie zum Testen von Datenströmen verwenden können.  

## Wiedergabe Ihrer Inhalte mit vorhandenen Playern

Dieses Thema zeigt die vorhandenen Player, mit denen Sie Ihre Inhalte wiedergeben können.

>[AZURE.NOTE]Für die Wiedergabe dynamisch paketierter oder verschlüsselter Inhalte müssen Sie sicherstellen, dass mindestens eine Streamingeinheit für den Streamingendpunkt abgerufen wurde, über den die Inhalte bereitgestellt werden sollen. Informationen zum Skalieren von Streamingeinheiten finden Sie unter: [Skalieren von Streamingeinheiten](media-services-manage-origins#scale_streaming_endpoints.md).


### Azure-Verwaltungsportal - Media Services-Inhaltsplayer

Im **Azure-Verwaltungsportal** wird ein Inhaltsplayer bereitgestellt, mit dem Sie Ihre Videos testen können.

Klicken Sie auf das gewünschte Video (stellen Sie sicher, dass dieses [veröffentlicht wurde](media-services-manage-content#publish.md)), und klicken Sie auf die **Play**-Taste im unteren Bereich des Portals. 
 
Folgende Überlegungen sollten berücksichtigt werden:

- Der **MEDIA SERVICES-INHALTSPLAYER** gibt die Inhalt vom standardmäßigen Streamingendpunkt wieder. Wenn Sie die Wiedergabe von einem anderen Streamingendpunkt starten möchten, verwenden Sie einen anderen Player. Sie können beispielsweise den [Azure Media Services-Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) nutzen.
 

![AMSPlayer][AMSPlayer]

### Azure Media Services-Player

Verwenden Sie den [Azure Media Services-Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) zur Wiedergabe Ihrer Inhalte.


## Entwickeln von Videoplayern

Informationen zum Entwickeln eigener Player finden Sie unter [Entwickeln von Videoplayern](media-services-develop-video-players.md).
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png
<!--HONumber=47-->
