<properties 
	pageTitle="Erstellen Sie mit dem Verwaltungsportal Kanäle, die Livestreams mit mehreren Bitraten von lokalen Encodern empfangen" 
	description="Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren von grundlegenden Media Services-Anwendungen zum Livestreamen, in denen ein Kanal einen Livestream mit mehreren Bitraten von lokalen Encodern empfängt." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="juliako"/>


#Erstellen Sie mit dem Verwaltungsportal Kanäle, die Livestreams mit mehreren Bitraten von lokalen Encodern empfangen

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]


Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren von grundlegenden Media Services-Anwendungen zum Livestreamen, in denen ein **Kanal** einen Livestream mit mehreren Bitraten von lokalen Encodern empfängt. Ausführlichere Informationen zum Arbeiten mit Kanälen und dazugehörigen Komponenten finden Sie unter [Arbeiten mit Kanälen, von denen Multi-Bitrate-Livedatenströme aus lokalen Encodern empfangen werden](media-services-channels-overview.md).

In diesem Lernprogramm wird das Azure-Verwaltungsportal verwendet, um die folgenden Aufgaben ausführen:

2.  Konfigurieren von Streamingendpunkten
3.  Erstellen eines Kanals
1.  Konfigurieren Sie einen Live-Encoder und nehmen Sie Live-Datenströme in den Kanal auf (bei diesem Schritt wird Wirecast verwendet)
1.  Erstellen eines Programms (und eines Medienobjekts)
1.  Veröffentlichen des Medienobjekts und Abrufen von Streaming-URLs  
1.  Wiedergeben Ihrer Inhalte 
2.  Bereinigen

##Voraussetzungen
Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

- Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](azure.microsoft.com).
- Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Konto erstellen](media-services-create-account.md).
- Sie benötigen eine Webcam und einen Encoder, von dem ein Multi-Bitrate-Livedatenstrom gesendet wird.

	 
##Konfigurieren von Streamingendpunkten mithilfe des Portals

Wenn Sie mit Azure Media Services arbeiten, besteht eines der häufigsten Szenarien darin, Streaming mit adaptiver Bitrate an Ihre Clients zu übermitteln. Mit adaptivem Bitrate-Streaming kann der Client während der Videodarstellung auf einen höheren oder niedrigeren Bitraten-Stream wechseln, basierend auf der aktuellen Netzwerkbandbreite, CPU-Auslastung und anderen Faktoren. Von Media Services werden die folgenden Streamingtechnologien mit adaptiver Bitrate unterstützt: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (nur mit Adobe PrimeTime/Access-Lizenz).

Beim Arbeiten mit Livestreaming wird ein Livedatenstrom mit mehreren Bitraten von einem lokalen Liveencoder (im Beispielfall Wirecast) in den Kanal geleitet. Wenn der Datenstrom durch einen Benutzer angefordert wird, so wird der Quelldatenstrom durch dynamische Paketerstellung in die angeforderte Bitrate (HLS, DASH oder Smooth) umgewandelt.

Um die dynamische Paketerstellung nutzen zu können, ist mindestens eine Streamingeinheit für den **Streamingendpunkt** erforderlich, aus dem die Inhalte geliefert werden sollen.

Um die Anzahl der Einheiten zu ändern, die für das Streaming reserviert sind, gehen Sie folgendermaßen vor:

1. Klicken Sie im [Management Portal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendienstes.

2. Wählen Sie die Seite STREAMING-ENDPUNKTE aus. Klicken Sie anschließend auf den Streaming-Endpunkt, den Sie ändern möchten.

3. Um die Anzahl der Streaming-Einheiten anzugeben, wählen Sie die Registerkarte SKALIERUNG aus und verschieben anschließend den Schieberegler für die **reservierte Kapazität**.

	![Skalierungsseite](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.

	Das Zuordnen neuer Einheiten dauert etwa 20 Minuten.

	 
	>[AZURE.NOTE]Aktuell kann das Streaming bis zu eine Stunde lang deaktiviert werden, wenn Sie einen positiven Wert für die Streamingeinheiten zurück auf null setzen.
	>
	> Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet. Informationen zu den Preisen finden Sie unter [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107).


##Erstellen eines Kanals

Wählen Sie im Azure-Verwaltungsportal die Seite **KANAL** aus. Klicken Sie dann auf **NEU**. Geben Sie im Dialogfeld **Erstellen eines neuen Live-Kanals** einen Namen für den Kanal ein.

![createchannel](./media/media-services-managing-channels/media-services-create-channel.png)

Klicken Sie auf **OK**.

Nach einigen Minuten wird der Kanal erstellt und gestartet.

##Abrufen von Erfassungs-URLs

Wenn der Kanal erstellt wurde, können Sie Erfassungs-URLs abrufen, die Sie dem Liveencoder bereitstellen. Diese URLs werden vom Encoder zur Eingabe eines Livedatenstroms verwendet.

![readychannel](./media/media-services-managing-channels/media-services-ready-channel.png)


![ingesturls](./media/media-services-managing-channels/media-services-ingest-urls.png)

Weitere Informationen über Erfassungs-URLs finden Sie unter [Verwenden von lokalen Encodern zum Senden von Livestreams mit mehreren Bitraten an einen Kanal](media-services-channels-overview.md).

##Konfigurieren Sie einen Live-Encoder und erfassen Sie Livedatenströme 

>[AZURE.NOTE]Dieser Schritt erfordert die Erfassungs-URL des Kanals, die im vorherigen Schritt erwähnt wurde.

Ausführliche Informationen zum Konfigurieren von Wirecast und der Erfassung von Streams finden Sie unter [Wirecast-Konfiguration](http://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/).

>[AZURE.NOTE]Wenn Sie aus irgendeinem Grund den Encoder stoppen und anschließend neu starten müssen, setzen Sie zuerst den Kanal zurück, indem Sie auf den Befehl **Zurücksetzen** im Azure-Verwaltungsportal klicken.


##Erstellen und Verwalten von Programmen

###Übersicht

Einem Kanal sind Programme zugeordnet, mit denen Sie das Veröffentlichen und Speichern von Segmenten in einem Livedatenstrom steuern können. Die Programme werden von den Kanälen verwaltet. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Inhaltsdatenstrom aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist.

Über die Länge des **Archivierungsfensters** können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt des Programms beibehalten möchten. Es können Werte zwischen mindestens 5 Minuten und höchstens 25 Stunden eingestellt werden. Von der Länge des Archivierungsfensters wird außerdem bestimmt, wie lange von Clients von der aktuellen Liveposition aus maximal rückwärts gesucht werden kann. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Durch den Wert dieser Eigenschaft wird außerdem festgelegt, wie lange Clientmanifeste wachsen können.

Jedes Programm ist mit einem Medienobjekt verknüpft. Zum Veröffentlichen des Programms müssen Sie einen OnDemand-Locator für das zugehörige Medienobjekt erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Kunden bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Programme, sodass Sie mehrere Archive desselben eingehenden Datenstroms erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

Verwenden Sie vorhandene Programme nicht erneut für nachfolgende Ereignisse. Erstellen und starten Sie stattdessen für jedes Ereignis ein neues Programm wie im Abschnitt „Programmieren von Livestreaminganwendungen“ beschrieben.

Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.

Zum Löschen von archivierten Inhalten beenden und löschen Sie das Programm und löschen anschließend das zugehörige Medienobjekt. Medienobjekte können nicht gelöscht werden, wenn sie von Programmen verwendet werden. Zuerst muss das betreffende Programm gelöscht werden.

Auch nach dem Beenden und Löschen des Programms können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Medienobjekt nicht gelöscht wurde.

Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaminglocator.

###Erstellen, Starten und Beenden von Programmen

Wenn der Datenstrom in den Kanal gelangt, können Sie das Streamingereignis starten, indem Sie ein Medienobjekt, ein Programm und einen Streaminglocator erstellen. Dadurch wird der Datenstrom archiviert und über den Streamingendpunkt für die Zuschauer verfügbar gemacht.

Es gibt zwei Möglichkeiten, das Ereignis zu starten:

1. Klicken Sie auf der Seite **KANAL** auf **HINZUFÜGEN**, um ein neues Programm hinzuzufügen.

	Geben Sie Folgendes an: Name des Programms, Name des Medienobjekts, Archivfenster, Verschlüsselungsoption.
	
	![createprogram](./media/media-services-managing-channels/media-services-create-program.png)
	
	Wenn Sie das Kontrollkästchen **Dieses Programm jetzt veröffentlichen** aktiviert gelassen haben, werden das Programm und die VERÖFFENTLICHUNGS-URLs erstellt.
	
	Wenn das Streaming des Programms gestartet werden soll, klicken Sie auf **START**.

	Wenn Sie das Programm gestartet haben, können Sie auf WIEDERGABE klicken, um die Wiedergabe der Inhalte zu starten.


	![createdprogram](./media/media-services-managing-channels/media-services-created-program.png)

2. Alternativ können Sie einfach auf der Seite **KANAL** auf die Schaltfläche **STREAMING STARTEN** klicken. Dadurch erstellen Sie ein Medienobjekt, ein Programm und einen Streaminglocator.

	Das Programm hat den Namen „DefaultProgram“, und das Archivfenster ist auf 1 Stunde festgelegt.

	Sie können das veröffentlichte Programm auf der Seite KANAL wiedergeben.

	![channelpublish](./media/media-services-managing-channels/media-services-channel-play.png)


Wenn Sie auf der Seite **KANAL** auf **STREAMING BEENDEN** klicken, wird das Standardprogramm beendet und gelöscht. Das Medienobjekt bleibt erhalten, und Sie können es auf der Seite **INHALT** veröffentlichen bzw. die Veröffentlichung aufheben.

Wenn Sie zur Seite **INHALT** wechseln, sehen Sie die Ressourcen, die für Ihre Programme erstellt wurden.

![contentasset](./media/media-services-managing-channels/media-services-content-assets.png)


##Wiedergeben von Inhalten

Um Ihren Benutzern eine URL bereitzustellen, die zum Streamen Ihrer Inhalte verwendet werden kann, müssen Sie Ihr Medienobjekt zunächst „veröffentlichen“ wie im vorigen Abschnitt beschrieben, indem Sie einen Locator erstellen (wenn Sie ein Medienobjekt mithilfe des Portals veröffentlichen, werden die Locators automatisch erstellt). Locators ermöglichen den Zugriff auf Dateien im Medienobjekt.

Je nach dem Streamingprotokoll, das zum Wiedergeben Ihrer Inhalte verwendet werden soll, müssen Sie möglicherweise die URL ändern, die Sie über den Link **URL VERÖFFENTLICHEN** des Kanals/Programms erhalten haben.

Durch dynamische Paketerstellung wird der Livedatenstrom in das angegebene Protokoll gepackt.

Eine Streaming-URL, mit der Sie Smooth Streaming-Medienobjekte wiedergeben können, weist standardmäßig das folgende Format auf:

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
- Sie können Ihren Streamingendpunkt beenden, sofern Sie das Archiv Ihres Liveereignisses nicht als bedarfsgesteuerten Datenstrom bereitstellen möchten. Durch Kanäle im angehaltenen Zustand fallen keine Kosten an.



##Nächster Schritt

Erfahren Sie mehr über das Erstellen von Livestreaming-Anwendungen [Livestreaming-Anwendungen erstellen](media-services-live-streaming-workflow.md)

###Zusätzliche Ressourcen
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Stellen Sie Ihr Video jetzt online!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamische Pakete und mobile Geräte</a>


<!-- Anchors. -->


<!-- URLs. -->
[Management Portal]: http://manage.windowsazure.com/

<!-- Images -->

<!--HONumber=52-->
