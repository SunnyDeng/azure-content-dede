<properties 
	pageTitle="Wiedergabe von Inhalten" 
	description="In diesem Thema finden Sie vorhandene Player, die Sie zum Wiedergeben Ihrer Inhalte verwenden können." 
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
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#Wiedergabe Ihrer Inhalte mit vorhandenen Playern

Azure Media Services unterstützt zahlreiche gängige Streamingformate wie Smooth Streaming, HTTP Live Streaming und MPEG-Dash. In diesem Thema finden Sie Verweise auf vorhandene Player, die Sie zum Testen von Datenströmen verwenden können.

>[AZURE.NOTE]Für die Wiedergabe dynamisch paketierter oder verschlüsselter Inhalte müssen Sie sicherstellen, dass mindestens eine Streamingeinheit für den Streamingendpunkt abgerufen wurde, über den die Inhalte bereitgestellt werden sollen. Informationen zum Skalieren von Streamingeinheiten finden Sie unter [Skalieren von Streamingeinheiten](media-services-manage-origins.md#scale_streaming_endpoints).

###Klassisches Azure-Portal – Media Services-Inhaltsplayer

Im **klassischen Azure-Portal** wird ein Inhaltsplayer bereitgestellt, mit dem Sie Ihre Videos testen können.

Klicken Sie auf den gewünschten Videoinhalt (vergewissern Sie sich, dass er [veröffentlicht](media-services-manage-content.md#publish) wurde), und klicken Sie auf die Schaltfläche **Abspielen** am unteren Rand des Portals.
 
Folgende Überlegungen sollten berücksichtigt werden:

- Der **MEDIA SERVICES-INHALTSPLAYER** gibt die Inhalt vom standardmäßigen Streamingendpunkt wieder. Wenn Sie die Wiedergabe von einem anderen Streamingendpunkt starten möchten, verwenden Sie einen anderen Player. Beispiel: [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

###Azure Media Player

Verwenden Sie [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) zum Wiedergeben Ihrer Inhalte (offen oder geschützt) in den folgenden Formaten:

- Smooth Streaming
- MPEG DASH
- HLS
- Progressive MP4


###Flash Player

####AES-verschlüsselt mit Token 

[http://aestoken.azurewebsites.net]("http://aestoken.azurewebsites.net)

###Silverlight-Player

####Überwachung

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####PlayReady mit Token

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### DASH-Player

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###Andere

Sie können zum Testen von HLS-URLs auch Folgendes verwenden:

- **Safari** auf einem iOS-Gerät oder
- **3ivx HLS Player** unter Windows.

##Entwickeln von Videoplayern

Informationen zum Entwickeln eigener Player finden Sie unter [Entwickeln von Videoplayern](media-services-develop-video-players.md).




##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

<!---HONumber=AcomDC_0211_2016-->