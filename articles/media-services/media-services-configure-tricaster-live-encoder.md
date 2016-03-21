<properties 
	pageTitle="Konfigurieren des NewTek TriCaster-Encoders zum Senden eines Single-Bitrate-Livedatenstroms" 
	description="In diesem Thema wird beschrieben, wie Sie den Tricaster-Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert sind." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,cenkdin,anilmur" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="03/02/2016"   
	ms.author="juliako"/>

#Verwenden des NewTek TriCaster-Encoders zum Senden eines Single-Bitrate-Livedatenstroms

> [AZURE.SELECTOR]
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

In diesem Thema wird beschrieben, wie Sie den [NewTek TriCaster](http://newtek.com/products/tricaster-40.html)-Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert sind. Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

In diesem Tutorial wird gezeigt, wie Sie Azure Media Services (AMS) mit dem Tool Azure Media Services Explorer (AMSE) verwalten. Dieses Tool kann nur auf Windows-PCs ausgeführt werden. Unter Mac OS oder Linux verwenden Sie das klassische Azure-Portal, um [Kanäle](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) und [Programme](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program) zu erstellen.

>[AZURE.NOTE]Bei der Verwendung von TriCaster zum Senden eines Beitragsfeeds an AMS-Kanäle, die für Live Encoding aktiviert sind, können Video- oder Audiostörungen im Liveereignis auftreten, wenn Sie bestimmte Funktionen von TriCaster nutzen, z. B. schnelle Schnitte zwischen Feeds oder Wechseln zwischen Slates. Das AMS-Team arbeitet an einer Lösung zur Behebung dieser Probleme. Derzeit wird jedoch davon abgeraten, diese Funktionen zu verwenden.


##Voraussetzungen

- [Erstellen eines Azure Media Services-Kontos](media-services-create-account.md)
- Stellen Sie sicher, dass ein Streamingendpunkt mit mindestens einer zugeordneten Streamingeinheit ausgeführt wird. Weitere Informationen finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md)
- Installieren Sie die neueste Version des [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer)-Tools.
- Starten Sie das Tool, und stellen Sie eine Verbindung mit Ihrem AMS-Konto her.

##Tipps

- Verwenden Sie nach Möglichkeit eine Kabelverbindung zum Internet.
- Als Faustregel zum Bestimmen der erforderlichen Bandbreite verdoppeln Sie die Streamingbitraten. Dies ist zwar keine zwingende Voraussetzung, aber hilfreich, um die Auswirkungen einer Überlastung des Netzwerks zu verringern.
- Bei der Verwendung softwarebasierter Encoder schließen Sie alle nicht benötigten Programme.

## Erstellen eines Kanals

1.  Navigieren Sie im AMSE-Tool zur Registerkarte **Live**, und klicken Sie mit der rechten Maustaste in den Kanalbereich. Wählen Sie im Menü die Option **Kanal erstellen** aus.

![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Geben Sie einen Kanalnamen und optional eine Beschreibung ein. Wählen Sie unter "Kanaleinstellungen" **Standard** für die Option "Livecodierung" aus, und legen Sie das Eingabeprotokoll auf **RTMP** fest. Alle anderen Einstellungen können Sie unverändert lassen.


Stellen Sie sicher, dass die Option **Neuen Kanal jetzt starten** ausgewählt ist.

3. Klicken Sie auf **Kanal erstellen**. ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

>[AZURE.NOTE] Das Starten des Kanals kann bis zu 20 Minuten dauern.


Während der Kanal gestartet wird, können Sie [den Encoder konfigurieren](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

>[AZURE.IMPORTANT] Beachten Sie, dass die Abrechnung beginnt, sobald der Kanal betriebsbereit ist. Weitere Informationen finden Sie unter [Kanalstatus](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_tricaster_rtmp></a>Konfigurieren des NewTek TriCaster-Encoders

In diesem Tutorial werden die folgenden Ausgabeeinstellungen verwendet. Im restlichen Teil dieses Abschnitts werden die Konfigurationsschritte im Detail beschrieben.

**Video**:
 
- Codec: H.264 
- Profil: Hoch (Level 4.0) 
- Bitrate: 5.000 KBit/s 
- Keyframe: 2 Sekunden (60 Sekunden) 
- Bildfrequenz: 30
 
**Audio**:

- Codec: AAC (LC) 
- Bit Rate: 192 Kbit/s 
- Abtastrate: 44,1 kHz


###Konfigurationsschritte

1. Erstellen Sie ein neues **NewTek TriCaster**-Projekt abhängig von der verwendeten Videoeingabequelle. 
2. Suchen Sie in diesem Projekt die Schaltfläche **Streamen**, und klicken Sie auf das Zahnradsymbol daneben, um das Konfigurationsmenü für den Datenstrom zu öffnen.

	![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Klicken Sie nach dem Öffnen des Menüs unter der Überschrift "Verbindung" auf **Neu**. Wenn Sie zur Eingabe des Verbindungstyps aufgefordert werden, wählen Sie **Adobe Flash** aus.

	![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)

4. Klicken Sie auf **OK**.

5. Jetzt können Sie ein FMLE-Profil importieren, indem Sie auf den Dropdownpfeil unter **Streamingprofil** klicken und zu **Durchsuchen** navigieren.

	![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)

6. Navigieren Sie zum Speicherort des konfigurierten FMLE-Profils.
7. Wählen Sie es aus, und klicken Sie auf **OK**.

	Nachdem das Profil hochgeladen wurde, fahren Sie mit dem nächsten Schritt fort.

6. Rufen Sie die Eingabe-URL des Kanals ab, um diese dem Tricaster-**RTMP-Endpunkt** zuzuweisen.
	
	Navigieren Sie zurück zum AMSE-Tool, und überprüfen Sie den Abschlussstatus des Kanals. Sobald sich der Status von **Wird gestartet** in **Wird ausgeführt** ändert, können Sie die Eingabe-URL abrufen.
	  
	Wenn der Kanal ausgeführt wird, klicken Sie mit der rechten Maustaste auf den Kanalnamen, navigieren Sie nach unten, bewegen Sie den Mauszeiger über **Eingabe-URL in die Zwischenablage kopieren**, und wählen Sie dann **Primäre Eingabe-URL** aus.
	
	![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)

7. Fügen Sie diese Informationen im Tricaster-Projekt in das Feld **Speicherort** unter **Flash Server** ein. Weisen Sie außerdem im Feld **Datenstrom-ID** einen Datenstromnamen zu.

	Wenn dem FMLE-Profil Datenstrominformationen hinzugefügt wurden, können Sie diese hier auch importieren. Klicken Sie dazu auf **Einstellungen importieren**, navigieren Sie zum gespeicherten FMLE-Profil, und klicken Sie auf **OK**. Die relevanten Felder von Flash Server sollte mit den Informationen aus FMLE ausgefüllt werden.

	![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)

9. Klicken Sie abschließend am unteren Rand des Bildschirms auf **OK**. Wenn Video- und Audioeingaben in den Tricaster bereit sind, beginnen Sie das Streaming an AMS, indem Sie auf die Schaltfläche **Streamen** klicken.

	![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

>[AZURE.IMPORTANT] Bevor Sie auf **Streamen** klicken, **müssen** Sie sicherstellen, dass der Kanal bereit ist. Stellen Sie außerdem sicher, dass der Kanal nicht länger als 15 Minuten ohne ein Eingabe in einem betriebsbereiten Zustand verbleibt.

##Testen der Wiedergabe
  
1. Navigieren Sie zum AMSE-Tool, und klicken Sie mit der rechten Maustaste auf den Kanal, der getestet werden soll. Bewegen Sie den Mauszeiger im Menü über **Wiedergabevorschau**, und wählen Sie **mit Azure Media Player** aus.  

	![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Wenn der Datenstrom im Player angezeigt wird, wurde der Encoder ordnungsgemäß für die Verbindung mit AMS konfiguriert.

Wenn eine Fehlermeldung angezeigt wird, müssen Sie den Kanal zurücksetzen und die Encodereinstellungen anpassen. Eine Anleitung finden Sie im Thema [Problembehandlung](media-services-troubleshooting-live-streaming.md).

##Erstellen eines Programms

1. Nachdem die Kanalwiedergabe überprüft wurde, erstellen Sie ein Programm. Klicken Sie im AMSE-Tool auf der Registerkarte **Live** mit der rechten Maustaste in den Programmbereich, und wählen Sie **Neues Programm erstellen** aus.  

	![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)

2. Benennen Sie die Anwendung, und passen Sie ggf. die **Archivfensterlänge** an (standardmäßig auf 4 Stunden festgelegt). Sie können außerdem einen Speicherort angeben oder die Standardeinstellung beibehalten.
3. Aktivieren Sie das Kontrollkästchen **Programm jetzt starten**.
4. Klicken Sie auf **Programm erstellen**.  
  
	Hinweis: Die Programmerstellung erfordert weniger Zeit als die Kanalerstellung.
 
5. Sobald die Anwendung ausgeführt wird, bestätigen Sie die Wiedergabe, indem Sie mit der rechten Maustaste auf das Programm klicken, zu **Programm(e) wiedergeben** navigieren und dann **mit Azure Media Player** auswählen.
6. Nach der Bestätigung klicken Sie mit der rechten Maustaste erneut auf das Programm, und wählen Sie **Ausgabe-URL in die Zwischenablage kopieren** aus (bzw. rufen Sie diese Informationen über das Menü mit der Option **Programminformationen und -einstellungen** ab). 

Der Datenstrom kann jetzt in einen Player eingebettet oder an eine Zielgruppe für die Livewiedergabe verteilt werden.


## Problembehandlung

Eine Anleitung finden Sie im Thema [Problembehandlung](media-services-troubleshooting-live-streaming.md).


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0309_2016-->