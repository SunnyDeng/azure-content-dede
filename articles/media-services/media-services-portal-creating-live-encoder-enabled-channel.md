<properties 
	pageTitle="Verwenden des Azure-Portals, um Kanäle zu erstellen, von denen eine Livecodierung von Single-Bitrate- zu Multi-Bitrate-Datenströmen vorgenommen wird" 
	description="In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines Kanals über das Azure-Portal geführt, von dem ein Single-Bitrate-Livedatenstrom empfangen und in einen Multi-Bitrate-Datenstrom codiert wird." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015"  
	ms.author="juliako"/>


#Verwenden des Azure-Portals, um Kanäle zu erstellen, von denen eine Livecodierung von Single-Bitrate- zu Multi-Bitrate-Datenströmen vorgenommen wird

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines **Kanals** geführt, von dem ein Single-Bitrate-Livedatenstrom empfangen und in einen Multi-Bitrate-Datenstrom codiert wird.

>[AZURE.NOTE]Weitere konzeptuelle Informationen zu Kanälen, bei denen Livecodierung aktiviert ist, finden Sie unter [Arbeiten mit Kanälen, von denen Livecodierung von Single-Bitrate- in Multi-Bitrate-Livedatenströme ausgeführt wird](media-services-manage-live-encoder-enabled-channels.md).

##Allgemeines Livestreamingszenario

Im Folgenden werden grundlegende Schritte zum Erstellen allgemeiner Livestreaminganwendungen erläutert.

>[AZURE.NOTE]Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an Amslived unter Microsoft Punkt Com, wenn Sie einen Kanal für längere Zeit laufen lassen müssen.

1. Schließen Sie eine Videokamera an einen Computer an. Starten und konfigurieren Sie einen lokalen Liveencoder, von dem ein Single-Bitrate-Datenstrom in einem der folgenden Protokolle ausgegeben wird: RTMP, Smooth Streaming oder RTP (MPEG-TS). Weitere Informationen finden Sie unter [￼Windows Azure Media Services RTMP-Support und Liveencoder](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Dieser Schritt kann auch nach der Erstellung des Kanals ausgeführt werden.

1. Erstellen Sie einen Kanal, und starten Sie ihn.

1. Rufen Sie die Erfassungs-URL des Kanals ab.

	Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.
1. Rufen Sie die Vorschau-URL des Kanals ab. 

	Verwenden Sie diese URL, um sicherzustellen, dass der Livedatenstrom ordnungsgemäß vom Kanal empfangen wird.

3. Erstellen Sie ein Programm (dadurch wird auch ein Asset erstellt).
1. Veröffentlichen Sie das Programm (dadurch wird ein OnDemand-Locator für das zugehörige Asset erstellt).  

	Stellen Sie sicher, dass auf dem Streamingendgerät, von dem Sie Inhalte streamen möchten, mindestens eine für das Streaming reservierte Einheit verfügbar ist.
1. Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm.
2. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.
1. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.
1. Löschen Sie das Programm (und optional das Asset).   

##Dieses Lernprogramm umfasst folgende Punkte

In diesem Lernprogramm wird das Azure-Verwaltungsportal verwendet, um die folgenden Aufgaben ausführen:

2.  Konfigurieren von Streamingendgeräten
3.  Erstellen eines Kanals, der zum Ausführen von Livecodierung aktiviert ist
1.  Abrufen der Erfassungs-URL, um sie dem Liveencoder bereitzustellen Diese URL wird vom Liveencoder verwendet, um den Datenstrom in den Kanal zu leiten.
1.  Erstellen eines Programms (und eines Assets)
1.  Veröffentlichen des Assets und Abrufen von Streaming-URLs  
1.  Wiedergeben Ihrer Inhalte 
2.  Bereinigen

##Voraussetzungen
Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

- Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](azure.microsoft.com).
- Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Konto erstellen](media-services-create-account.md).
- Sie benötigen eine Webcam und einen Encoder, von dem ein Single-Bitrate-Livedatenstrom gesendet wird.

##Konfigurieren von Streamingendgeräten mithilfe des Portals

Wenn Sie mit Azure Media Services arbeiten, besteht eines der häufigsten Szenarien darin, Streaming mit adaptiver Bitrate an Ihre Clients zu übermitteln. Mit Adaptive Bitrate Streaming kann der Client während der Videodarstellung auf einen höheren oder niedrigeren Bitraten-Stream wechseln, basierend auf der aktuellen Netzwerkbandbreite, CPU-Auslastung und anderen Faktoren. Von Media Services werden die folgenden Streamingtechnologien mit adaptiver Bitrate unterstützt: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (nur mit Adobe PrimeTime/Access-Lizenz).

Beim Arbeiten mit Livestreaming wird ein Livedatenstrom mit mehreren Bitraten von einem lokalen Liveencoder (im Beispielfall Wirecast) in den Kanal geleitet. Wenn der Datenstrom durch einen Benutzer angefordert wird, so wird der Quelldatenstrom durch dynamische Paketerstellung in die angeforderte Bitrate (HLS, DASH oder Smooth) umgewandelt.

Um die dynamische Paketerstellung nutzen zu können, ist mindestens eine Streamingeinheit für den **Streamingendgerät** erforderlich, aus dem die Inhalte geliefert werden sollen.

Um die Anzahl der Einheiten zu ändern, die für das Streaming reserviert sind, gehen Sie folgendermaßen vor:

1. Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendienstes.

2. Wählen Sie die Seite STREAMING-ENDPUNKTE aus. Klicken Sie anschließend auf den Streamingendgerät, das Sie ändern möchten.

3. Um die Anzahl der Streaming-Einheiten anzugeben, wählen Sie die Registerkarte SKALIERUNG aus und verschieben anschließend den Schieberegler für die **reservierte Kapazität**.

	![Skalierungsseite](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-origin-scale.png)

4. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Das Zuordnen neuer Einheiten dauert etwa 20 Minuten.

	 
	>[AZURE.NOTE]Aktuell kann das Streaming bis zu eine Stunde lang deaktiviert werden, wenn Sie einen positiven Wert für die Streamingeinheiten zurück auf null setzen.
	>
	> Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet. Informationen zu den Preisen finden Sie unter [Media Services – Preisübersicht](http://go.microsoft.com/fwlink/?LinkId=275107).

 
##Erstellen eines KANALS

1.	Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf „Media Services“ und dann auf den Media Services-Kontonamen.
2.	Wählen Sie die Seite KANÄLE aus.
3.	Klicken Sie auf „Hinzufügen“, um einen neuen Kanal hinzuzufügen.

Wählen Sie **Standard** als Codierungstyp aus. Mit diesem Typen geben Sie an, dass Sie einen Kanal erstellen möchten, der für Livecodierung aktiviert ist. Dies bedeutet, dass der eingehende Single-Bitrate-Datenstrom an den Kanal gesendet und mithilfe angegebener Liveencodereinstellungen in einen Multi-Bitrate-Datenstrom codiert wird. Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, von denen Livecodierung von Single-Bitrate- in Multi-Bitrate-Livedatenströme ausgeführt wird](media-services-manage-live-encoder-enabled-channels.md).

![standard0][standard0]

Beim Codierungstyp **Standard** gibt es die folgenden gültigen Erfassungsprotokolloptionen:

- Fragmentiertes Single-Bitrate-MP4 (Smooth Streaming)
- Single-Bitrate-RTMP
- RTP (MPEG-TS): MPEG-2-Transportdatenstrom über RTP

Ausführliche Erläuterungen der Protokolle finden Sie unter [Arbeiten mit Kanälen, von denen Livecodierung von Single-Bitrate- in Multi-Bitrate-Livedatenströme ausgeführt wird](media-services-manage-live-encoder-enabled-channels.md).

![standard1][standard1]

Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal.

Auf der Seite **Werbung – Konfiguration** können Sie eine Quelle für AD-Markersignale angeben. Wenn Sie das Portal verwenden, können Sie nur den Wert „API“ auswählen. Dies bedeutet, dass vom Liveencoder im Kanal eine asynchrone AD-Marker-API überwacht werden soll. Wenn Sie das Portal verwenden, können Sie nur den Wert „API“ auswählen.

Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, von denen Livecodierung von Single-Bitrate- in Multi-Bitrate-Livedatenströme ausgeführt wird](media-services-manage-live-encoder-enabled-channels.md).

![standard2][standard2]

Auf der Seite **Codierungsvoreinstellungen** können Sie Systemvoreinstellungen auswählen. Derzeit ist die einzige Systemvoreinstellung, die Sie auswählen können, **Default 720p**.

![standard3][standard3]

Auf der Seite **Kanalerstellung** können Sie die IP-Adressen definieren, die zum Veröffentlichen von Videos in diesem Kanal zugelassen sind. Zulässige IP-Adressen können als eine einzelne IP-Adresse (z. B. „10.0.0.1“), als IP-Adressbereich mithilfe einer IP-Adresse und einer CIDR-Subnetzmaske (z. B. „10.0.0.1/22“) oder als IP-Adressbereich mithilfe einer Subnetzmaske in punktierter Dezimalschreibweise (z. B. „10.0.0.1(255.255.252.0)“) angegeben werden.

Wenn keine IP-Adressen angegeben werden und keine Regeldefinition vorhanden ist, so ist keine IP-Adresse zulässig. Um alle IP-Adressen zuzulassen, erstellen Sie eine Regel und legen „0.0.0.0/0“ fest.


![standard4][standard4]

>[AZURE.NOTE]Derzeit kann der Kanalstart bis zu 30 Minuten dauern. Das Zurücksetzen des Kanals kann bis zu 5 Minuten dauern.

Nachdem Sie den Kanal erstellt haben, können Sie die Registerkarte **ENCODER** auswählen, um die Konfigurationen Ihrer Kanäle anzusehen. Sie können auch Werbespots und Slates verwalten.

![standard5][standard5]

Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, von denen Livecodierung von Single-Bitrate- in Multi-Bitrate-Livedatenströme ausgeführt wird](media-services-manage-live-encoder-enabled-channels.md).


##Abrufen von Erfassungs-URLs

Wenn der Kanal erstellt wurde, können Sie Erfassungs-URLs abrufen, die Sie dem Liveencoder bereitstellen. Diese URLs werden vom Encoder zur Eingabe eines Livedatenstroms verwendet.

![readychannel](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ready-channel.png)


![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##Erstellen und Verwalten von Programmen

###Übersicht

Einem Kanal sind Programme zugeordnet, mit denen Sie das Veröffentlichen und Speichern von Segmenten in einem Livedatenstrom steuern können. Die Programme werden von den Kanälen verwaltet. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Inhaltsdatenstrom aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist.

Über die Länge des **Archivierungsfensters** können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt des Programms beibehalten möchten. Es können Werte zwischen mindestens 5 Minuten und höchstens 25 Stunden eingestellt werden. Von der Länge des Archivierungsfensters wird außerdem bestimmt, wie lange von Clients von der aktuellen Liveposition aus maximal rückwärts gesucht werden kann. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Durch den Wert dieser Eigenschaft wird außerdem festgelegt, wie lange Clientmanifeste wachsen können.

Jedes Programm ist mit einem Asset verknüpft. Zum Veröffentlichen des Programms müssen Sie einen OnDemand-Locator für das zugehörige Asset erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Kunden bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Programme, sodass Sie mehrere Archive desselben eingehenden Datenstroms erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

Verwenden Sie vorhandene Programme nicht erneut für nachfolgende Ereignisse. Erstellen und starten Sie stattdessen für jedes Ereignis ein neues Programm wie im Abschnitt „Programmieren von Livestreaminganwendungen“ beschrieben.

Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.

Zum Löschen von archivierten Inhalten beenden und löschen Sie das Programm und löschen anschließend das zugehörige Asset. Assets können nicht gelöscht werden, wenn sie von Programmen verwendet werden. Zuerst muss das betreffende Programm gelöscht werden.

Auch nach dem Beenden und Löschen des Programms können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Asset nicht gelöscht wurde.

Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaminglocator.

###Erstellen, Starten und Beenden von Programmen

Wenn der Datenstrom in den Kanal gelangt, können Sie das Streamingereignis starten, indem Sie ein Asset, ein Programm und einen Streaminglocator erstellen. Dadurch wird der Datenstrom archiviert und über den Streamingendgerät für die Zuschauer verfügbar gemacht.

Es gibt zwei Möglichkeiten, das Ereignis zu starten:

1. Klicken Sie auf der Seite **KANAL** auf **HINZUFÜGEN**, um ein neues Programm hinzuzufügen.

	Geben Sie Folgendes an: Name des Programms, Name des Assets, Archivfenster, Verschlüsselungsoption.
	
	![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
	
	Wenn Sie das Kontrollkästchen **Dieses Programm jetzt veröffentlichen** aktiviert gelassen haben, werden das Programm und die VERÖFFENTLICHUNGS-URLs erstellt.
	
	Wenn das Streaming des Programms gestartet werden soll, klicken Sie auf **START**.

	Wenn Sie das Programm gestartet haben, können Sie auf WIEDERGABE klicken, um die Wiedergabe der Inhalte zu starten.


	![createdprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-created-program.png)

2. Alternativ können Sie einfach auf der Seite **KANAL** auf die Schaltfläche **STREAMING STARTEN** klicken. Dadurch erstellen Sie ein Asset, ein Programm und einen Streaminglocator.

	Das Programm hat den Namen „DefaultProgram“, und das Archivfenster ist auf 1 Stunde festgelegt.

	Sie können das veröffentlichte Programm auf der Seite KANAL wiedergeben.

	![channelpublish](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-channel-play.png)


Wenn Sie auf der Seite **KANAL** auf **STREAMING BEENDEN** klicken, wird das Standardprogramm beendet und gelöscht. Das Asset bleibt erhalten, und Sie können es auf der Seite **INHALT** veröffentlichen bzw. die Veröffentlichung aufheben.

Wenn Sie zur Seite **INHALT** wechseln, sehen Sie die Ressourcen, die für Ihre Programme erstellt wurden.

![contentasset](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-content-assets.png)


##Wiedergeben von Inhalten

Um Ihren Benutzern eine URL bereitzustellen, die zum Streamen Ihrer Inhalte verwendet werden kann, müssen Sie Ihr Asset zunächst „veröffentlichen“ wie im vorigen Abschnitt beschrieben, indem Sie einen Locator erstellen (wenn Sie ein Asset mithilfe des Portals veröffentlichen, werden die Locators automatisch erstellt). Locators ermöglichen den Zugriff auf Dateien im Asset.

Je nach dem Streamingprotokoll, das zum Wiedergeben Ihrer Inhalte verwendet werden soll, müssen Sie möglicherweise die URL ändern, die Sie über den Link **URL VERÖFFENTLICHEN** des Kanals/Programms erhalten haben.

Durch dynamische Paketerstellung wird der Livedatenstrom in das angegebene Protokoll gepackt.

Eine Streaming-URL, mit der Sie Smooth Streaming-Assets wiedergeben können, weist standardmäßig das folgende Format auf:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Um eine HLS-Streaming-URL zu erstellen, fügen Sie „(format=m3u8-aapl)“ an die URL an.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Um eine MPEG DASH-Streaming-URL zu erstellen, fügen Sie „(format=mpd-time-csf)“ an die URL an.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Weitere Informationen zum Bereitstellen von Inhalten finden Sie unter [Bereitstellen von Inhalten](media-services-deliver-content-overview.md).

Sie können Smooth Stream mit dem [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) wiedergeben oder iOS- und Android-Geräte verwenden, um HLS Version 3 wiederzugeben.

##Bereinigen

Wenn die Streamingereignisse beendet sind und Sie die zuvor bereitgestellten Ressourcen bereinigen möchten, führen Sie das folgende Verfahren aus:

- Beenden Sie die Datenstromeingabe vom Encoder.
- Beenden Sie den Kanal. Wenn der Kanal beendet ist, fallen keine Kosten an. Wenn Sie den Kanal erneut starten, weist er die gleiche Erfassungs-URL auf, damit Sie den Encoder nicht erneut konfigurieren müssen.
- Sie können Ihren Streamingendgerät beenden, sofern Sie das Archiv Ihres Liveereignisses nicht als bedarfsgesteuerten Datenstrom bereitstellen möchten. Durch Kanäle im angehaltenen Zustand fallen keine Kosten an.
  

##Überlegungen

- Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an Amslived unter Microsoft Punkt Com, wenn Sie einen Kanal für längere Zeit laufen lassen müssen.
- Stellen Sie sicher, dass auf dem Streamingendgerät, von dem Sie Inhalte streamen möchten, mindestens eine für das Streaming reservierte Einheit verfügbar ist.

##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)


[standard0]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard0.png
[standard1]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard1.png
[standard2]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard2.png
[standard3]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard3.png
[standard4]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard4.png
[standard5]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard_encode.png

<!---HONumber=Oct15_HO4-->