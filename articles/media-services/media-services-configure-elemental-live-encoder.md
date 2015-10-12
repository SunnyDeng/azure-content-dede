<properties 
	pageTitle="Konfigurieren des Elemental Live-Encoders zum Senden eines Single-Bitrate-Livedatenstroms" 
	description="In diesem Thema wird beschrieben, wie Sie den Elemental Live-Encoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, die für das Live Encoding aktiviert sind." 
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

#Verwenden des Elemental Live-Encoders zum Senden eines Single-Bitrate-Livedatenstroms 

> [AZURE.SELECTOR]
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)
  
In diesem Thema wird beschrieben, wie Sie den [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live)-Encoder zum Senden eines Single-Bitrate-Livedatenstroms an AMS-Kanäle konfigurieren, die für das Live Encoding aktiviert sind. Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Live Encoding mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

In diesem Tutorial wird gezeigt, wie Sie Azure Media Services (AMS) mit Azure Media Services Explorer (AMSE) verwalten. Dieses Tool kann nur auf Windows-PCs ausgeführt werden. Unter Mac OS oder Linux verwenden Sie das Azure-Verwaltungsportal, um [Kanäle](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) und [Programme](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program) zu erstellen.

##Voraussetzungen

- Zum Erstellen von Liveereignissen müssen Sie über ausreichende Kenntnisse in Bezug auf die Verwendung der Elemental Live-Webschnittstelle verfügen.
- [Erstellen eines Azure Media Services-Kontos](media-services-create-account.md)
- Stellen Sie sicher, dass ein Streamingendpunkt mit mindestens einer Streamingeinheit vorhanden ist. Weitere Informationen finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md). 

- Installieren Sie die neueste Version des [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer)-Tools.
- Starten Sie das Tool, und stellen Sie eine Verbindung mit Ihrem AMS-Konto her.

##Tipps

- Verwenden Sie nach Möglichkeit eine kabelgebundene Internetverbindung. 
- Eine Faustregel zum Bestimmen der erforderlichen Bandbreite ist die Verdoppelung der Streamingbitraten. Dies ist zwar keine zwingende Voraussetzung, aber es ist hilfreich, um die Auswirkungen einer Überlastung des Netzwerks zu verringern.  
- Bei Verwendung softwarebasierter Encoder sollten Sie alle nicht benötigten Programme schließen.

## Elemental Live mit RTP Ingest

In diesem Abschnitt wird veranschaulicht, wie Sie den Elemental Live-Encoder konfigurieren, mit dem Sie einen Single-Bitrate-Livedatenstrom per RTP senden. Weitere Informationen finden Sie unter [MPEG TS-Datenstrom per RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### Erstellen eines Kanals

1.  Navigieren Sie im AMSE-Tool zur Registerkarte **Live**, und klicken Sie mit der rechten Maustaste in den Kanalbereich. Wählen Sie im Menü die Option **Kanal erstellen...** aus.  

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Geben Sie einen Kanalnamen und optional eine Beschreibung ein. Wählen Sie unter „Kanaleinstellungen“ die Option **Standard** für das Live Encoding aus, und legen Sie das Eingabeprotokoll auf **RTP (MPEG-TS)** fest. Sie können alle anderen Einstellungen unverändert lassen.


	 Stellen Sie sicher, dass die Option **Neuen Kanal jetzt starten** ausgewählt ist.
 
3. Klicken Sie auf **Kanal erstellen**. ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE]Das Starten des Kanals kann bis zu 20 Minuten dauern.

Während der Kanal gestartet wird, können Sie [den Encoder konfigurieren](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

>[AZURE.IMPORTANT]Beachten Sie, dass die Abrechnung beginnt, sobald der Kanal betriebsbereit ist. Weitere Informationen finden Sie unter [Kanalstatus](media-services-manage-live-encoder-enabled-channels.md#states).

###<a id=configure_elemental_rtp></a>Konfigurieren des Elemental Live-Encoders 

In diesem Tutorial werden die folgenden Ausgabeeinstellungen verwendet. Im restlichen Teil dieses Abschnitts werden die Konfigurationsschritte im Detail beschrieben.

**Video**:
 
- Codec: H.264 
- Profil: Hoch (Level 4.0) 
- Bitrate: 5.000 KBit/s 
- Keyframe: 2 Sekunden (60 Sekunden) 
- Bildfrequenz: 30
 
**Audio**:

- Codec: AAC (LC) 
- Bitrate: 192 KBit/s 
- Abtastrate: 44,1 kHz


####Konfigurationsschritte

1. Navigieren Sie zur Webschnittstelle **Elemental Live**, und richten Sie den Encoder für das **UDP/TS**-Streaming ein. 

2. Führen Sie nach dem Erstellen eines neuen Ereignisses einen Bildlauf nach unten zu den Ausgabegruppen durch, und fügen Sie die Ausgabegruppe **UDP/TS** hinzu.

3. Erstellen Sie eine neue Ausgabe, indem Sie **Neuer Datenstrom** auswählen und auf **Ausgabe hinzufügen** klicken.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
	
	>[AZURE.NOTE]Es wird empfohlen, den Zeitcode für das Elemental-Ereignis auf „Systemuhr“ festzulegen, damit der Encoder die Verbindung bei einem Ausfall des Datenstroms erneut herstellen kann.

4. Klicken Sie nach dem Erstellen der Ausgabe nun auf **Datenstrom hinzufügen**. Jetzt können die Ausgabeeinstellungen konfiguriert werden.
5. Führen Sie einen Bildlauf zum gerade erstellten Element „Datenstrom 1“ durch, klicken Sie links auf die Registerkarte **Video**, und erweitern Sie den Abschnitt mit den erweiterten Einstellungen (**Erweitert**). 

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

	Elemental Live verfügt zwar über viele verfügbare Anpassungsmöglichkeiten, aber für die ersten Schritte beim Streaming zu AMS werden die folgenden Einstellungen empfohlen.
	
	- Auflösung: 1280 x 720 
	- Bildfrequenz: 30 
	- GOP-Größe: 60 Bilder 
	- Zeilensprungmodus: Progressiv 
	- Bitrate: 5.000.000 Bit/s (kann je nach Netzwerkbeschränkungen angepasst werden) 
	

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. Rufen Sie die Eingabe-URL des Kanals ab.
	
	Navigieren Sie zurück zum AMSE-Tool, und überprüfen Sie den Abschlussstatus des Kanals. Sobald sich der Status von **Wird gestartet** in **Wird ausgeführt** ändert, können Sie die Eingabe-URL abrufen.
	  
	Wenn der Kanal ausgeführt wird, klicken Sie mit der rechten Maustaste auf den Kanalnamen, navigieren Sie nach unten, bewegen Sie den Mauszeiger über **Eingabe-URL in die Zwischenablage kopieren**, und wählen Sie dann **Primäre Eingabe-URL** aus.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
	
1. Fügen Sie diese Informationen in das Feld **Primärziel** von Elemental ein. Für alle anderen Einstellungen können Sie die Standardeinstellung beibehalten.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

	Wiederholen Sie diese Schritte mit der sekundären Eingabe-URL, um für zusätzliche Redundanz zu sorgen. Erstellen Sie hierzu die separate Registerkarte „Ausgabe“ für das UDP/TS-Streaming.
	
7. Klicken Sie auf **Erstellen** (bei Erstellung eines neuen Ereignisses) oder **Aktualisieren** (bei Bearbeitung eines vorhandenen Ereignisses), und fahren Sie dann fort, um den Encoder zu starten.

>[AZURE.IMPORTANT]Bevor Sie auf der Elemental Live-Webschnittstelle auf **Start** klicken, **müssen** Sie sicherstellen, dass der Kanal bereit ist. Stellen Sie außerdem sicher, dass der Kanal nicht länger als 15 Minuten ohne Ereignis in einem betriebsbereiten Zustand verbleibt.

Nachdem der Datenstrom 30 Sekunden lang ausgeführt wurde, navigieren Sie zurück zum AMSE-Tool, und testen sie die Wiedergabe.

###Testen der Wiedergabe
  
1. Navigieren Sie zum AMSE-Tool, und klicken Sie mit der rechten Maustaste auf den Kanal, der getestet werden soll. Bewegen Sie den Mauszeiger im Menü über **Wiedergabevorschau**, und wählen Sie **mit Azure Media Player** aus.  

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Wenn der Datenstrom im Player angezeigt wird, wurde der Encoder ordnungsgemäß für die Verbindung mit AMS konfiguriert.

Wenn eine Fehlermeldung angezeigt wird, müssen Sie den Kanal zurücksetzen und die Encodereinstellungen anpassen. Eine Anleitung finden Sie im Thema [Problembehandlung](media-services-troubleshooting-live-streaming.md).

###Erstellen eines Programms

1. Nachdem die Kanalwiedergabe überprüft wurde, erstellen Sie ein Programm. Klicken Sie im AMSE-Tool auf der Registerkarte **Live** mit der rechten Maustaste in den Programmbereich, und wählen Sie **Neues Programm erstellen** aus.  

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. Benennen Sie die Anwendung, und passen Sie ggf. die **Archivfensterlänge** an (standardmäßig auf 4 Stunden festgelegt). Sie können außerdem einen Speicherort angeben oder die Standardeinstellung beibehalten.
3. Aktivieren Sie das Kontrollkästchen **Programm jetzt starten**.
4. Klicken Sie auf **Programm erstellen**.  
  
	Hinweis: Die Programmerstellung erfordert weniger Zeit als die Kanalerstellung.
 
5. Sobald die Anwendung ausgeführt wird, bestätigen Sie die Wiedergabe, indem Sie mit der rechten Maustaste auf das Programm klicken, zu **Programm(e) wiedergeben** navigieren und dann **mit Azure Media Player** auswählen.
6. Nach der Bestätigung klicken Sie mit der rechten Maustaste erneut auf das Programm, und wählen Sie **Ausgabe-URL in die Zwischenablage kopieren** aus (bzw. rufen Sie diese Informationen über das Menü mit der Option **Programminformationen und -einstellungen** ab). 

Der Datenstrom kann jetzt in einen Player eingebettet oder an eine Zielgruppe für die Livewiedergabe verteilt werden.

## Problembehandlung

Eine Anleitung finden Sie im Thema [Problembehandlung](media-services-troubleshooting-live-streaming.md).

##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

<!---HONumber=Oct15_HO1-->