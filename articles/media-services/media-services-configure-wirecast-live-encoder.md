<properties 
	pageTitle="Konfigurieren des Telestream Wirecast-Encoders zum Senden eines Single-Bitrate-Livedatenstroms" 
	description="In diesem Thema wird beschrieben, wie Sie den Wirecast-Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert sind." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,cenkdin,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="10/15/2015"  
	ms.author="juliako"/>

#Verwenden des Wirecast-Encoders zum Senden eines Single-Bitrate-Livedatenstroms

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

In diesem Thema wird beschrieben, wie Sie den [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm)-Liveencoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, für die Livecodierung aktiviert sind. Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

In diesem Tutorial wird gezeigt, wie Sie Azure Media Services (AMS) mit dem Tool Azure Media Services Explorer (AMSE) verwalten. Dieses Tool kann nur auf Windows-PCs ausgeführt werden. Unter Mac OS oder Linux verwenden Sie das klassische Azure-Portal, um [Kanäle](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) und [Programme](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program) zu erstellen.


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

![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Geben Sie einen Kanalnamen und optional eine Beschreibung ein. Wählen Sie unter "Kanaleinstellungen" **Standard** für die Option "Livecodierung" aus, und legen Sie das Eingabeprotokoll auf **RTMP** fest. Alle anderen Einstellungen können Sie unverändert lassen.


Stellen Sie sicher, dass die Option **Neuen Kanal jetzt starten** ausgewählt ist.

3. Klicken Sie auf **Kanal erstellen**. ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE]Das Starten des Kanals kann bis zu 20 Minuten dauern.

Während der Kanal gestartet wird, können Sie [den Encoder konfigurieren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT]Beachten Sie, dass die Abrechnung beginnt, sobald der Kanal betriebsbereit ist. Weitere Informationen finden Sie unter [Kanalstatus](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Konfigurieren des Telestream Wirecast-Encoders.

In diesem Tutorial werden die folgenden Ausgabeeinstellungen verwendet. Im restlichen Teil dieses Abschnitts werden die Konfigurationsschritte im Detail beschrieben.

**Video**:
 
- Codec: H.264 
- Profil: Hoch (Level 4.0) 
- Bitrate: 5.000 KBit/s 
- Keyframe: 2 Sekunden (60 Sekunden) 
- Bildfrequenz: 30
 
**Audio**:

- Codec: AAC (LC) 
- Bit Rate: 192 Kbit/s 
- Abtastrate: 44,1 kHz


###Konfigurationsschritte

1. Öffnen Sie die Telestream Wirecast-Anwendung auf dem verwendeten Computer, und konfigurieren Sie sie für das RTMP-Streaming.
2. Konfigurieren Sie die Ausgabe, indem Sie zur Registerkarte **Ausgabe** navigieren und **Ausgabeeinstellungen...** auswählen.
	
	Stellen Sie sicher, dass das **Ausgabeziel** auf **RTMP-Server** festgelegt ist.
3. Klicken Sie auf **OK**.
4. Legen Sie auf der Einstellungsseite das Feld **Ziel** auf **Azure Media Services** fest.
 
	Das Codierungsprofil ist auf **Azure H.264 720p 16:9 (1280x720)** voreingestellt. Zum Anpassen dieser Einstellungen wählen Sie das Zahnradsymbol rechts neben der Dropdownliste aus und wählen dann **Neue Voreinstellung** aus.

	![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Konfigurieren Sie die Encodervoreinstellungen.

	Benennen Sie die Voreinstellung, und überprüfen Sie, ob die folgenden empfohlenen Einstellungen vorliegen:

	**Video**
	
	- Encoder: MainConcept h. 264
	- Bilder pro Sekunde: 30
	- Durchschnittliche Bitrate: 5000 Kbit/s (kann basierend auf Netzwerkbeschränkungen angepasst werden)
	- Profil: Main
	- Keyframe alle: 60 Bilder

	**Audio**

	- Zielbitrate: 192 Kbit/s
	- Abtastrate: 44,100 kHz
	 
	![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Klicken Sie auf **Speichern**.

	Das neu erstellte Profil kann jetzt im Feld "Codierung" ausgewählt werden.

	Stellen Sie sicher, dass das neue Profil ausgewählt ist.

7. Rufen Sie die Eingabe-URL des Kanals ab, um diese dem Wirecast-**RTMP-Endpunkt** zuzuweisen.
	
	Navigieren Sie zurück zum AMSE-Tool, und überprüfen Sie den Abschlussstatus des Kanals. Sobald sich der Status von **Wird gestartet** in **Wird ausgeführt** ändert, können Sie die Eingabe-URL abrufen.
	  
	Wenn der Kanal ausgeführt wird, klicken Sie mit der rechten Maustaste auf den Kanalnamen, navigieren Sie nach unten, bewegen Sie den Mauszeiger über **Eingabe-URL in die Zwischenablage kopieren**, und wählen Sie dann **Primäre Eingabe-URL** aus.
	
	![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Fügen Sie diese Informationen im Wirecast-Fenster **Ausgabeoptionen** im Ausgabebereich in das Feld **Adresse** ein, und weisen Sie einen Datenstromnamen zu.


	![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Klicken Sie auf **OK**.

10. Vergewissern Sie sich im **Wirecast**-Hauptbildschirm, dass die Eingabequellen für Video und Audio bereit sind, und klicken Sie dann in der linken oberen Ecke auf **Streamen**.

	![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]Bevor Sie auf **Streamen** klicken, **müssen** Sie sicherstellen, dass der Kanal bereit ist. Stellen Sie außerdem sicher, dass der Kanal nicht länger als 15 Minuten ohne ein Eingabe in einem betriebsbereiten Zustand verbleibt.

##Testen der Wiedergabe
  
1. Navigieren Sie zum AMSE-Tool, und klicken Sie mit der rechten Maustaste auf den Kanal, der getestet werden soll. Bewegen Sie den Mauszeiger im Menü über **Wiedergabevorschau**, und wählen Sie **mit Azure Media Player** aus.  

	![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Wenn der Datenstrom im Player angezeigt wird, wurde der Encoder ordnungsgemäß für die Verbindung mit AMS konfiguriert.

Wenn eine Fehlermeldung angezeigt wird, müssen Sie den Kanal zurücksetzen und die Encodereinstellungen anpassen. Eine Anleitung finden Sie im Thema [Problembehandlung](media-services-troubleshooting-live-streaming.md).

##Erstellen eines Programms

1. Nachdem die Kanalwiedergabe überprüft wurde, erstellen Sie ein Programm. Klicken Sie im AMSE-Tool auf der Registerkarte **Live** mit der rechten Maustaste in den Programmbereich, und wählen Sie **Neues Programm erstellen** aus.  

	![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

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

<!---HONumber=AcomDC_1203_2015-->