<properties 
	pageTitle="Arbeiten mit Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen" 
	description="In diesem Thema wird das Einrichten eines Kanals beschrieben, der einen Livestream mit mehreren Bitraten von einem lokalen Encoder empfängt. Der Stream kann über einen oder mehrere Streamingendpunkte an Wiedergabe-Clientanwendungen übermittelt werden. Dazu dienen die folgenden adaptiven Streamingprotokolle: HLS, Smooth Stream, MPEG DASH, HDS." 
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
	ms.date="05/23/2015" 
	ms.author="juliako"/>

#Arbeiten mit Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen

##Übersicht

In Azure Media Services repräsentiert ein **Kanal** eine Pipeline zum Verarbeiten von Livestreaminginhalten. Es gibt zwei Arten, wie Live-Eingabedatenströme von **Kanälen** empfangen werden können:

- Von einem lokalen Liveencoder wird Multi-Bitrate- **RTMP** oder -**Smooth Streaming** (fragmentiertes MP4) an den Kanal gesendet. Sie können die folgenden Liveencoder verwenden, von denen Multi-Bitrate-Smooth Streaming ausgegeben werden kann: Elemental, Envivio, Cisco. Von den folgenden Liveencodern wird RTMP ausgegeben: Adobe Flash Live, Telestream Wirecast und Tricaster-Transcoder. Die aufgenommenen Datenströme werden ohne weitere Verarbeitung durch die **Kanäle** geleitet. Der Liveencoder kann auch einen Datenstrom mit einer Bitrate senden, dies wird jedoch nicht empfohlen. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.


Das folgende Diagramm stellt einen Livestreaming-Workflow dar, der über einen lokalen Live-Encoder Streams RTMP- oder Fragmented MP4-Streams (Smooth Streaming) mit mehreren Bitraten ausgibt.

![Liveworkflow][live-overview]

In diesem Thema werden folgende Themen behandelt:

- [Allgemeines Livestreamingszenario](media-services-manage-channels-overview.md#scenario)
- [Beschreibung von Kanälen und zugehörigen Komponenten](media-services-manage-channels-overview.md#channel)
- [Überlegungen](media-services-manage-channels-overview.md#considerations)
- [Aufgaben im Zusammenhang mit Livestreaming](media-services-manage-channels-overview.md#tasks)

##<a id="scenario"></a>Allgemeines Livestreamingszenario
Die folgenden Schritte beschreiben die Aufgaben zum Erstellen von gebräuchlichen Livestreaminganwendungen.

1. Schließen Sie eine Videokamera an einen Computer an. Starten und konfigurieren Sie einen lokalen Live-Encoder, der einen RTMP- oder Fragmented MP4-Stream (Smooth Streaming) mit mehreren Bitraten ausgibt. Weitere Informationen finden Sie unter [￼Windows Azure Media Services RTMP-Support und Liveencoder](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Dieser Schritt kann auch nach der Erstellung des Kanals ausgeführt werden.

1. Erstellen Sie einen Kanal, und starten Sie ihn.
1. Rufen Sie die Erfassungs-URL des Kanals ab. 

	Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.
1. Rufen Sie die Vorschau-URL des Kanals ab. 

	Verwenden Sie diese URL, um sicherzustellen, dass der Livestream ordnungsgemäß vom Kanal empfangen wird.

3. Erstellen Sie ein Programm.

	Im Azure-Verwaltungsportal wird beim Erstellen eines Programms auch ein Medienobjekt erstellt.

	Wenn Sie hingegen .NET SDK oder REST verwenden, müssen Sie beim Erstellen eines Programms ein Medienobjekt erstellen und angeben, dass es verwendet werden soll. 
1. Veröffentlichen Sie das mit dem Programm verknüpfte Medienobjekt.   

	Stellen Sie sicher, dass auf dem Streamingendpunkt, von dem Sie Inhalte streamen möchten, mindestens eine für das Streaming reservierte Einheit verfügbar ist.
1. Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm.
2. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.
1. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.
1. Löschen Sie das Programm (und optional das Medienobjekt).     

Im Abschnitt [Livestreaming-Aufgaben](media-services-manage-channels-overview.md#tasks) finden Sie Verknüpfungen zu Themen, in welchen veranschaulicht wird, wie die beschriebenen Aufgaben auszuführen sind.

##<a id="channel"></a>Beschreibung von Kanälen und zugehörigen Komponenten

###<a id="channel_input"></a>Kanaleingangskonfigurationen (Erfassung)

####<a id="ingest_protocols"></a>Erfassungsstreamingprotokoll

Media Services unterstützt die Erfassung von Livefeeds mithilfe der folgenden Streamingprotokolle:

- **Fragmented MP4 mit mehreren Bitraten**
 
- **RTMP mit mehreren Bitraten**

	Wenn das Erfassungs-Streamingprotokoll **RTMP** aktiviert ist, werden zwei Erfassungs-(Eingangs-)Endpunkte für den Kanal erstellt:
	
	**Primäre URL**: Gibt die vollqualifizierte URL des primären RTMP-Erfassungsendpunkts für den Kanal an.

	**Sekundäre URL** (optional): Gibt die vollqualifizierte URL des sekundären RTMP-Erfassungsendpunkts für den Kanal an.


	Verwenden Sie die sekundäre URL, um die Beständigkeit und Fehlertoleranz des Erfassungsdatenstroms sowie das Failover und die Fehlertoleranz des Encoders zu verbessern. Dies gilt insbesondere für die folgenden Szenarien:

	- Einzelne Encoder, die einen doppelten Push zu primären und sekundären URLs ausführen:
	
		Der Hauptzweck ist hierbei, mehr Resilienz gegenüber Netzwerkfluktuationen und Jitter bereitzustellen. Einige RTMP-Encoder können Trennungen der Netzwerkverbindung nicht gut verwalten. Wenn die Netzwerkverbindung getrennt wird, beendet der Encoder möglicherweise die Codierung und sendet die gepufferten Daten nicht, wenn die Verbindung wiederhergestellt wird. Dies führt zu Diskontinuitäten und Datenverlust. Trennungen der Netzwerkverbindung können aufgrund einer instabilen Netzwerkverbindung oder Wartungen auf der Seite von Azure auftreten. Durch primäre/sekundäre URLs werden Netzwerkprobleme reduziert und es kann ein kontrollierter Aktualisierungsprozess bereitgestellt werden. Jedes Mal, wenn eine geplante Netzwerktrennung auftritt, verwaltet Media Services die primäre und sekundäre Trennung und stellt eine verzögerte Trennung dazwischen bereit. So erhalten die Encoder Zeit, um Daten zu senden und die Verbindung wiederherzustellen. Die Reihenfolge der Trennungen kann zufällig sein. Es gibt jedoch immer eine Verzögerung zwischen primär/sekundär oder sekundär/primär. In diesem Szenario ist der Encoder immer noch die einzige Fehlerquelle.
	 
	- Mehrere Encoder, bei denen jeder Encoder einen Push zum dedizierten Endpunkt ausführt:
		
		Dieses Szenario bietet Redundanz für Encoder sowie Erfassung. In diesem Szenario überträgt encoder1 an die primäre URL und encoder2 sendet an die sekundäre URL. Wenn ein Encoderfehler auftritt, kann der andere Encoder weiterhin Daten senden. Die Datenredundanz kann erhalten werden, da Media Services die primäre und sekundäre Verbindung nicht zur gleichen Zeit trennt. Bei diesem Szenario wird davon ausgegangen, dass die Encoder zeitsynchronisiert sind und genau die gleichen Daten bereitgestellt werden.
 
	- Mehrere Encoder, die einen doppelten Push zu primären und sekundären URLs ausführen:
	
		In diesem Szenario senden beide Encoder Daten zu primären und sekundären URLs. Dies bietet die höchste Zuverlässigkeit und Fehlertoleranz sowie Datenredundanz. Es kann ein Ausfall beider Encoder toleriert werden, ebenso wie Trennungen der Netzwerkverbindung, auch wenn ein Encoder nicht mehr ausgeführt wird. Bei diesem Szenario wird davon ausgegangen, dass die Encoder zeitsynchronisiert sind und genau die gleichen Daten bereitgestellt werden.

Informationen zu RTMP-Liveencodern finden Sie unter [Azure Media Services RTMP Support and Live Encoders](http://go.microsoft.com/fwlink/?LinkId=532824) (in englischer Sprache).

Es gelten die folgenden Bedingungen:

- Stellen Sie sicher, dass Sie ausreichend ungenutzte Internetbandbreite zum Senden an die Erfassungspunkte haben. 
- Das Verwenden einer sekundären Erfassungs-URL erfordert zusätzlichen Bandbreite. 
- Der eingehende Stream mit mehreren Bitraten kann maximal zehn Ebenen (oder Schichten) der Videoqualität und maximal fünf Audiospuren aufweisen.
- Die höchste durchschnittliche Bitrate für die Videoqualitätsebenen oder -schichten sollte unter 10 Mbit/s liegen.
- Die zusammengezählten durchschnittlichen Bitraten für alle Video- und Audiostreams sollten unter 25 Mbit/s liegen.
- Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal. 
- Sie können auch eine einzelne Bitrate in Ihrem Kanal erfassen, aber da der Datenstrom nicht vom Kanal verarbeitet wird, erhalten die Clientanwendungen ebenfalls einen Datenstrom mit nur einer Bitrate (diese Option wird nicht empfohlen).

####Erfassungs-URLs (Endpunkte) 

Ein Kanal stellt einen Eingabeendpunkt (Erfassungs-URL) dar, den Sie im Live-Encoder angeben, damit dieser Streams an die Kanäle übertragen kann.

Sie können die Erfassungs-URLs beim Erstellen des Kanals erhalten. Der Kanal muss dazu nicht den Status **Running** (Wird ausgeführt) aufweisen. Wenn Sie beginnen möchten, Daten an den Kanal zu senden, muss der Kanal den Status **Running** (Wird ausgeführt) aufweisen. Sobald der Kanal mit dem Erfassen von Daten beginnt, können Sie Ihren Datenstrom über die Vorschau-URL anzeigen.

Sie können Fragmented MP4-Livestreams (Smooth Streaming) über eine SSL-Verbindung erfassen. Zur Erfassung über SSL stellen Sie sicher, dass die Erfassungs-URL auf HTTPS aktualisiert wurde. Derzeit kann RTMP nicht über SSL erfasst werden.

####<a id="keyframe_interval"></a>Keyframe-Intervall

Wenn ein lokaler Live-Encoder Zum Erzeugen eines Streams mit mehreren Bitraten verwendet wird, gibt das Keyframe-Intervall die GOP-Dauer (wie von diesem externen Encoder verwendet) an. Sobald dieser eingehende Datenstrom durch den Kanal empfangen wird, können Sie Ihren Livedatenstrom in einem der folgenden Formate für Wiedergabe-Clientanwendungen bereitstellen: Smooth Streaming, DASH und HLS. Bei Livestreaming wird HLS immer dynamisch verpackt. Standardmäßig berechnet Media Services das HLS Segment-Paketerstellungsverhältnis (Fragmente pro Segment) basierend auf dem Keyframe-Intervall, auch bezeichnet als Gruppe von Bildern, GOP, die vom Live-Encoder empfangen wird.

Die folgende Tabelle zeigt, wie die Segmentdauer berechnet wird:

Keyframe-Intervall|Paketerstellungsverhältnis für HLS-Segmente (FragmentsPerSegment)|Beispiel
---|---|---
kleiner oder gleich 3 Sekunden|3:1|Wenn das KeyFrameInterval (oder GOP) 2 Sekunden beträgt, ist das Standard-Paketerstellungsverhältnis für HLS-Segmente 3:1, und es wird ein HLS-Segment von 6 Sekunden erstellt.
3 bis 5 Sekunden|2:1|Wenn das KeyFrameInterval (oder GOP) 4 Sekunden beträgt, ist das Standard-Paketerstellungsverhältnis für HLS-Segmente 2:1, und es wird ein HLS-Segment von 8 Sekunden erstellt.
größer als 5 Sekunden|1:1|Wenn das KeyFrameInterval (oder GOP) 6 Sekunden beträgt, ist das Standard-Paketerstellungsverhältnis für HLS-Segmente 1:1, und es wird ein HLS-Segment von 6 Sekunden erstellt.


Sie können das Verhältnis der Fragmente pro Segment ändern, indem Sie den Ausgang des Kanals konfigurieren und FragmentsPerSegment für ChannelOutputHls festlegen.

Sie können den Keyframe-Intervallwert auch durch Festlegen der KeyFrameInterval-Eigenschaft für ChannelInput ändern.

Wenn Sie das KeyFrameInterval explizit festlegen, wird FragmentsPerSegment für das Paketerstellungsverhältnis für HLS-Segmente anhand der oben beschriebenen Regeln berechnet.

Wenn Sie sowohl KeyFrameInterval als auch FragmentsPerSegment explizit festlegen, werden von Media Services die festgelegten Werte verwendet.


####Zulässige IP-Adressen

Sie können die IP-Adressen definieren, die zum Veröffentlichen von Videos in diesem Kanal zugelassen sind. Zulässige IP-Adressen können als eine einzelne IP-Adresse (z. B. „10.0.0.1“), als IP-Adressbereich mithilfe einer IP-Adresse und einer CIDR-Subnetzmaske (z. B. „10.0.0.1/22“) oder als IP-Adressbereich mithilfe einer Subnetzmaske in punktierter Dezimalschreibweise (z. B. „10.0.0.1(255.255.252.0)“) angegeben werden.

Wenn keine IP-Adressen angegeben sind und es keine Regeldefinition gibt, sind keine IP-Adressen zulässig. Um alle IP-Adressen zuzulassen, erstellen Sie eine Regel und legen „0.0.0.0/0“ fest.

###Kanalvorschau 

####Vorschau-URLs

Mit den Kanälen ist ein Vorschauendpunkt (Vorschau-URL) verfügbar, mit dem Sie eine Vorschau anzeigen und Ihren Datenstrom vor der weiteren Verarbeitung und Übermittlung überprüfen können.

Wenn Sie einen Kanal erstellen, können Sie die Vorschau-URL abrufen. Der Kanal muss zum Abrufen der URL nicht den Status **Running** (Wird ausgeführt) aufweisen.

Sobald der Kanal mit dem Erfassen von Daten beginnt, können Sie eine Vorschau des Streams anzeigen.

Beachten Sie, dass der Vorschaustream derzeit unabhängig vom angegebenen Eingabetyp nur im Fragmented MP4-Format (Smooth Streaming) bereitgestellt werden kann. Sie können den Player [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) verwenden, um den Smooth Stream zu testen. Sie können auch mit dem im Azure Management Portal gehosteten Player Ihren Stream wiedergeben.


####Zulässige IP-Adressen

Sie können die IP-Adressen definieren, die zum Herstellen einer Verbindung mit dem Vorschauendpunkt zugelassen sind. Wenn keine IP-Adressen angegeben werden, wird jede IP-Adresse zugelassen. Zulässige IP-Adressen können als eine einzelne IP-Adresse (z. B. „10.0.0.1“), als IP-Adressbereich mithilfe einer IP-Adresse und einer CIDR-Subnetzmaske (z. B. „10.0.0.1/22“) oder als IP-Adressbereich mithilfe einer Subnetzmaske in punktierter Dezimalschreibweise (z. B. „10.0.0.1(255.255.252.0)“) angegeben werden.

###Kanalausgabe

Weitere Informationen finden Sie im Abschnitt zum [Festlegen von Keyframe-Intervallen](#keyframe_interval).


###Programme von Kanälen

Einem Kanal sind Programme zugeordnet, mit denen Sie das Veröffentlichen und Speichern von Segmenten in einem Livestream steuern können. Die Programme werden von den Kanälen verwaltet. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Inhaltsdatenstrom aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist.

Über die Länge des **Archivierungsfensters** können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt des Programms beibehalten möchten. Es können Werte zwischen mindestens 5 Minuten und höchstens 25 Stunden eingestellt werden. Von der Länge des Archivierungsfensters wird außerdem bestimmt, wie lange von Clients von der aktuellen Liveposition aus maximal rückwärts gesucht werden kann. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Der Wert dieser Eigenschaft legt außerdem fest, wie lange Clientmanifeste wachsen können.

Jedem Programm ist ein Medienobjekt zugeordnet, von welchem die gestreamten Inhalte gespeichert werden. Ein Medienobjekt ist einem BLOB-Container im Azure Storage-Konto zugeordnet, und die im Medienobjekt enthaltenen Dateien werden als BLOBs in diesem Container gespeichert. Zum Veröffentlichen des Programms, damit Ihre Kunden den Datenstrom sehen können, müssen Sie einen OnDemand-Locator für das zugehörige Medienobjekt erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Kunden bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Programme, sodass Sie mehrere Archive desselben eingehenden Datenstroms erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

Verwenden Sie vorhandene Programme nicht erneut für nachfolgende Ereignisse. Erstellen und starten Sie stattdessen für jedes Ereignis ein neues Programm wie im Abschnitt „Programmieren von Livestreaminganwendungen“ beschrieben.

Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.

Zum Löschen von archivierten Inhalten beenden und löschen Sie das Programm und löschen anschließend das zugehörige Medienobjekt. Medienobjekte können nicht gelöscht werden, wenn sie von Programmen verwendet werden. Zuerst muss das betreffende Programm gelöscht werden.

Auch nach dem Beenden und Löschen des Programms können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Medienobjekt nicht gelöscht wurde.

Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaminglocator.

##<a id="states"></a>Kanalstatus und ihre Zuordnung zum Abrechnungsmodus 

Dies ist der aktuelle Status des Kanals. Mögliche Werte sind:

- **Stopped** (Beendet): Dies ist der Ausgangsstatus des Kanals nach der Erstellung. In diesem Status können die Eigenschaften des Kanals aktualisiert werden. Ein Streaming ist jedoch nicht zulässig.
- **Starting** (Wird gestartet): Der Kanal wird gestartet. In diesem Status sind weder Updates noch Streaming zulässig. Wenn ein Fehler auftritt, wird der Kanal in den Status „Stopped“ (Angehalten) geschaltet.
- **Running** (Wird ausgeführt): Vom Kanal können Livestreams verarbeitet werden.
- **Stopping** (Wird beendet): Der Kanal wird beendet. In diesem Status sind weder Updates noch Streaming zulässig.
- **Deleting** (Wird gelöscht): Der Kanal wird gelöscht. In diesem Status sind weder Updates noch Streaming zulässig.

In der folgenden Tabelle ist die Zuordnung der Kanalstatus mit den Abrechnungsmodi aufgeführt.
 
Kanalstatus|Portal-UI-Indikatoren|In Rechnung gestellt?
---|---|---|---
Wird gestartet|Wird gestartet|Nein (Übergangsstatus)
Wird ausgeführt|Bereit (keine ausgeführten Programme)<p>oder<p>Streaming (mindestens ein laufendes Programm)|Ja
Wird beendet|Wird beendet|Nein (Übergangsstatus)
Beendet|Beendet|Nein

###Untertitel und Werbeeinblendungen 

Die folgende Tabelle zeigt die unterstützten Untertitel- und Werbeeinblendungsstandards.

Standard|Hinweise
---|---
CEA-708 und EIA-608 (708/608)|CEA-708 und EIA 608 sind Untertitelstandards für die USA und Kanada.<p><p>Derzeit werden Untertitel nur unterstützt, wenn sie im codierten Eingabestream übertragen werden. Sie müssen einen Live-Media Encoder verwenden, der Untertitel nach 608 oder 708 in den codierten Stream, die an Media Services gesendet wird, einfügen kann. Media Services sendet die Inhalte mit eingefügten Untertiteln an Ihre Zuschauer.
TTML in ISMT (Smooth Streaming-Texttracks)|Die dynamische Paketerstellung von Media Services ermöglicht Ihren Clients das Streamen von Inhalten in einem der folgenden Formate: MPEG DASH, HLS oder Smooth Streaming. Wenn Sie jedoch Fragmented MP4 (Smooth Streaming) mit Untertitel in ISMT (Smooth Streaming-Texttracks) erfassen, kann der Stream nur an Smooth Streaming-Clients gesendet werden.
SCTE-35|Hierbei handelt es sich um ein digitales Signalisierungssystem zum Einfügen von Werbeeinblendungen. Downstream-Empfänger verwenden das Signal zum Einfügen von Werbung in den Stream für die vorgesehene Zeit. SCTE-35 muss als platzsparende Spur im Eingabestream versendet werden. <p><p>Beachten Sie, dass derzeit als Eingabestreamformat mit Werbesignalen nur Fragmented MP4 (Smooth Streaming) unterstützt wird. Das einzige unterstützte Ausgabeformat ist ebenfalls Smooth Streaming.


##<a id="Considerations"></a>Überlegungen

Wenn Sie mit einem lokalen Live-Encoder einen Datenstrom mit mehreren Bitraten an einem Kanal senden, gelten folgende Einschränkungen:

- Stellen Sie sicher, dass Sie ausreichend ungenutzte Internetbandbreite zum Senden an die Erfassungspunkte haben. 
- Der eingehende Stream mit mehreren Bitraten kann maximal zehn Videoqualitätsebenen (zehn Schichten) und maximal fünf Audiospuren aufweisen.
- Die höchste durchschnittliche Bitrate für die Videoqualitätsebenen oder -schichten sollte unter 10 Mbit/s liegen.
- Die zusammengezählten durchschnittlichen Bitraten für alle Video- und Audiostreams sollten unter 25 Mbit/s liegen.
- Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal. 


Weitere Aspekte im Zusammenhang mit der Arbeit mit Kanälen und zugehörigen Komponenten:

- Rufen Sie bei jeder Neukonfiguration des Liveencoders die **Reset**-Methode für den Kanal auf. Bevor Sie den Kanal zurückzusetzen, müssen Sie das Programm beenden. Wenn Sie den Kanal zurückgesetzt haben, starten Sie das Programm neu. 
- Ein Kanal kann nur beendet werden, wenn er den Status „Wird ausgeführt“ aufweist und alle Programme im Kanal beendet wurden.
- In der Standardeinstellung können Sie Ihrem Media Services-Konto nur fünf Kanäle hinzufügen. Weitere Informationen finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).
- Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal. 
- Es werden nur Kanäle in Rechnung gestellt, die den Status **Running** (Wird ausgeführt) aufweisen. Weitere Informationen finden Sie in [￼diesem Abschnitt](media-services-manage-channels-overview.md#states).

##<a id="tasks"></a>Aufgaben im Zusammenhang mit Livestreaming

###Erstellen eines Media Services-Kontos

[Erstellen Sie ein Azure Media Services-Konto](media-services-create-account.md).

###Konfigurieren von Streamingendpunkten

Eine Übersicht über Streamingendpunkte sowie Informationen zu deren Verwaltung finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md).

###Einrichten der Entwicklungsumgebung  

Wählen Sie **.NET** oder **REST API** für Ihre Entwicklungsumgebung.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###Programmgesteuerte Verbindung  

Wählen Sie **.NET** oder **REST API**, um eine programmgesteuerte Verbindung mit Azure Media Services herzustellen.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]

###Erstellen von Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen

Weitere Informationen zu lokalen Liveencodern finden Sie unter [Verwenden von Drittanbieter-Liveencodern mit Azure Media Services](https://msdn.microsoft.com/library/azure/dn783464.aspx).

Wählen Sie **Portal**, **.NET** oder **REST API**, um auf Informationen zum Erstellen und Verwalten von Kanälen und Programmen zuzugreifen.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

###Schutz der Medienobjekte

**Übersicht**:

[Inhaltsschutz – Übersicht](media-services-content-protection-overview.md)


Wenn Sie Medienobjekt, das mit einem Programm verknüpft ist, mit AES (Advanced Encryption Standard, mit 128-Bit-Verschlüsselungsschlüsseln) oder PlayReady-DRM verschlüsseln möchten, müssen Sie einen Inhaltsschlüssel erstellen.

Verwenden Sie **.NET** oder **REST API**, um Schlüssel zu erstellen.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

Nach dem Erstellen des Inhaltsschlüssels können Sie die Autorisierungsrichtlinie für Schlüssel mithilfe von **.NET** oder **REST API** konfigurieren.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

####Integrieren in Partneranwendungen

[Übermitteln von DRM-Lizenzen an Azure Media Services mithilfe von castLabs](media-services-castlabs-integration.md)

###Veröffentlichen und Bereitstellen von Medienobjekten

**Übersicht**:

- [Dynamische Paketerstellung – Übersicht](media-services-dynamic-packaging-overview.md)

Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Veröffentlichen von Medienobjekten (durch Locator-Erstellung) mithilfe des **Azure-Verwaltungsportals** oder mithilfe von **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]

Bereitstellen von Inhalten

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)


###Aktivieren von Azure CDN

Von Media Services wird die Integration mit Azure CDN unterstützt. Informationen zum Aktivieren von Azure CDN finden Sie unter [Verwalten von Streamingendpunkten in Media Services-Konten](media-services-manage-origins.md#enable_cdn).

###Skalieren eines Media Services-Kontos

Sie können **Media Services** skalieren, indem Sie die Anzahl der **reservierten Einheiten für das Streaming** angeben, die für Ihr Konto bereitgestellt werden sollen.

Informationen zum Skalieren von Streamingeinheiten finden Sie unter [Skalieren von Streamingeinheiten](media-services-manage-origins.md#scale_streaming_endpoints.md).

##Verwandte Themen

[Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[Bereitstellen von Livestreamingereignissen mit Azure Media Services](media-services-live-streaming-workflow.md)

[Media Services-Konzepte](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
 

<!---HONumber=August15_HO6-->