<properties 
	pageTitle="Anleitung zur Behandlung von Problemen bei Livestreaming" 
	description="Dieses Thema bietet Empfehlungen zum Behandeln von Problemen bei Livestreaming." 
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
	ms.date="09/29/2015"    
	ms.author="juliako"/>

#Anleitung zur Behandlung von Problemen bei Livestreaming

Dieses Thema bietet Empfehlungen zum Behandeln von Problemen bei Livestreaming.

## Probleme im Zusammenhang mit lokalen Encodern 

Dieser Abschnitt enthält Empfehlungen zum Beheben von Problemen im Zusammenhang mit lokalen Encodern, die zum Senden eines Single-Bitrate-Datenstroms an für Livecodierung aktivierte AMS-Kanäle konfiguriert sind.

###Problem: Es ist keine Option zum Ausgeben eines progressiven Datenstroms vorhanden

- **Mögliches Problem**: Der verwendete Encoder führt kein automatisches Deinterlacing aus. 

	**Problembehandlungsschritte**: Suchen Sie auf der Encoder-Benutzeroberfläche nach einer Option für Deinterlacing. Sobald Deinterlacing aktiviert ist, suchen Sie erneut nach Einstellungen für die progressive Ausgabe.
 
###Problem: Ich habe verschiedene Encoderausgabeeinstellungen ausprobiert und kann immer noch keine Verbindung herstellen. 

- **Mögliches Problem**: Der Azure-Codierungskanal wurde nicht ordnungsgemäß zurückgesetzt. 

	**Problembehandlungsschritte**: Stellen Sie sicher, dass der Encoder keine Push-Übertragung an AMS mehr ausführt, beenden Sie den Kanal, und setzen Sie ihn zurück. Sobald er erneut ausgeführt wird, versuchen Sie mit den neuen Einstellungen, den Encoder zu verbinden. Wenn das Problem damit immer noch nicht behoben ist, erstellen Sie einen vollkommen neuen Kanal. Manchmal werden Kanäle nach mehreren fehlgeschlagenen Versuchen beschädigt.

- **Mögliches Problem**: Die GOP-Größe oder die Keyframe-Einstellungen sind nicht optimal.

	**Problembehandlungsschritte**: Die empfohlene GOP-Größe bzw. das empfohlene Keyframe-Intervall beträgt 2 Sekunden. Manche Encoder berechnen diese Einstellung als Anzahl von Bildern und andere als Anzahl von Sekunden. Beispiel: Bei der Ausgabe von 30 BpS beträgt die GOP-Größe 60 Bilder; dies entspricht 2 Sekunden.
	 
- **Mögliches Problem**: Der Datenstrom wird durch geschlossene Ports blockiert.

	**Problembehandlungsschritte**: Überprüfen Sie beim Streamen per RTMP in den Firewall- und/oder Proxyeinstellungen, ob die ausgehenden Ports 1935 und 1936 geöffnet sind. Wenn Sie RTP-Streaming verwenden, stellen Sie sicher, dass der ausgehende Port 2010 geöffnet ist.


###Problem: Beim Konfigurieren des Encoders für Streaming per RTP besteht keine Möglichkeit zur Eingabe eines Hostnamens. 

- **Mögliches Problem**: Viele RTP-Encoder lassen die Angabe von Hostnamen nicht zu, und es muss eine IP-Adresse bezogen werden.  

	**Problembehandlungsschritte**: Um die IP-Adresse zu suchen, öffnen Sie ein Eingabeaufforderungsfenster auf einem beliebigen Computer. Öffnen Sie zu diesem Zweck das Dialogfeld "Ausführen" (Windows-Taste+R), und geben Sie zum Öffnen der Eingabeaufforderung "cmd" ein.

	Sobald die Eingabeaufforderung geöffnet ist, geben Sie "Ping [AMS-Hostname]" ein.

	Der Hostname kann durch Weglassen der Portnummer aus der Azure-Erfassungs-URL abgeleitet werden, wie im folgenden Beispiel verdeutlicht:

	rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010/

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###Problem: Der veröffentlichte Datenstrom kann nicht wiedergegeben werden.
 
- **Mögliches Problem**: Es wird kein Streamingendpunkt ausgeführt, oder es sind keine Streamingeinheiten (Skalierungseinheiten) zugeordnet. 

	**Problembehandlungsschritte**: Navigieren Sie im AMSE-Tool zur Registerkarte "Streamingendpunkt", und stellen Sie sicher, dass ein Streamingendpunkt mit einer Streamingeinheit ausgeführt wird.
	
>[AZURE.NOTE]Wenn Sie nach dem Ausführen der Problembehandlungsschritte immer noch nicht streamen können, senden Sie über das Azure-Verwaltungsportal ein Supportticket.

##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=Nov15_HO3-->