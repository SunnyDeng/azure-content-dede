<properties 
	pageTitle="Verwenden von lokalen Encodern zum Senden von Livestreams mit mehreren Bitraten an einen Kanal" 
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
	ms.date="04/06/2015" 
	ms.author="juliako"/>

#Verwenden von lokalen Encodern zum Senden von Livestreams mit mehreren Bitraten an einen Kanal

##Übersicht

In Azure Media Services (AMS) stellt ein **Kanal** eine Pipeline für die Verarbeitung von Livestreaminginhalten dar. Mit einem **Programm** können Sie die Veröffentlichung und Speicherung von Segmenten in einem Livestream steuern. Kanäle verwalten Programme. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Stream von Inhalten aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist. 

Bei der Arbeit mit Livestreaming ist eine der am Workflow beteiligten Komponenten ein Livevideo-Encoder, der Signale von der Kamera in Streams konvertiert, die an einen Livestreamingdienst gesendet werden. Ein **Kanal** kann Liveeingabestreams von einem lokalen Live-Encoder empfangen, der einen RTMP- oder Fragmented MP4-Stream (Smooth Streaming) mit mehreren Bitraten ausgibt. Sie können folgende Live-Encoder verwenden, die Smooth Streaming ausgeben: Elemental, Envivio, Cisco.  Die folgenden Live-Encoder geben RTMP aus: Adobe Flash Live, Telestream Wirecast und Tricaster-Transcoder. Die erfassten Streams durchlaufen **Kanäle** ohne weitere Verarbeitung. Der Live-Encoder kann auch einen Stream mit nur einer Bitrate erfassen, aber da der Stream nicht verarbeitet wird, erhalten die Clientanwendungen ebenfalls einen Stream mit nur einer Bitrate (diese Option wird nicht empfohlen). Sobald der empfangene Inhalt veröffentlicht wurde, kann er über einen oder mehrere **Streamingendpunkte** an Wiedergabe-Clientanwendungen gestreamt werden. Die folgenden adaptiven Streamingprotokolle können verwendet werden, um den Datenstrom wiederzugeben: HLS, MPEG DASH, Smooth Stream, HDS. 

Ein **Streamingendpunkt** stellt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Client-Player-Anwendung oder einem Netzwerk für die Inhaltsübermittlung (Content Delivery Network, CDN) bereitstellen kann. Der ausgehende Stream des Streamingendpunkt-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein. Darüber hinaus können Sie die Kapazität des Streamingendpunkt-Diensts für die Verarbeitung wachsender Bandbreitenanforderungen steuern, indem Sie die reservierten Einheiten für das Streaming anpassen. Anwendungen in einer Produktionsumgebung sollten Sie mindestens eine reservierte Einheit zuweisen. Weitere Informationen finden Sie unter [Skalieren eines Mediendiensts](media-services-manage-origins.md#scale_streaming_endpoints).

Das folgende Diagramm stellt einen Livestreaming-Workflow dar, der über einen lokalen Live-Encoder Streams RTMP- oder Fragmented MP4-Streams (Smooth Streaming) mit mehreren Bitraten ausgibt. 

![Live-Workflow][live-overview]

Dieses Thema enthält einen Überblick über einen Kanal, der einen Livestream mit mehreren Bitraten von einem lokalen Encoder empfängt. Außerdem werden Komponenten im Zusammenhang mit Kanälen erläutert.

##<a id="scenarios"></a>Ein häufiges Szenario für Livestreaming

Die folgenden Schritte beschreiben die Aufgaben zum Erstellen von gebräuchlichen Livestreaminganwendungen.

1. Schließen Sie eine Videokamera an einen Computer an. Starten und konfigurieren Sie einen lokalen Live-Encoder, der einen RTMP- oder Fragmented MP4-Stream (Smooth Streaming) mit mehreren Bitraten ausgibt. Weitere Informationen finden Sie unter [Azure Media Services RTMP Support and Live Encoders](http://go.microsoft.com/fwlink/?LinkId=532824) (in englischer Sprache).
	
	Dieser Schritt kann auch ausgeführt werden, nachdem Sie den Kanal erstellt haben.

1. Erstellen und starten Sie einen Kanal.
1. Rufen Sie die Erfassungs-URL des Kanals ab. 

	Die Erfassungs-URL wird vom Live-Encoder verwendet, um den Datenstrom an den Kanal senden.
1. Rufen Sie die Vorschau-URL des Kanals ab. 

	Mit dieser URL können Sie sicherstellen, dass der Kanal den Livestream ordnungsgemäß empfängt.

2. Erstellen Sie ein Medienobjekt.
3. Wenn das Medienobjekt dynamisch während der Wiedergabe verschlüsselt werden soll, führen Sie folgende Schritte aus: 	
	
	1. 	Erstellen Sie einen Inhaltsschlüssel. 
	1. 	Konfigurieren Sie Autorisierungsrichtlinien für den Inhaltsschlüssel.
1. Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte (wird zur dynamischen Paketerstellung und zur dynamischen Verschlüsselung verwendet).
3. Erstellen Sie ein Programm, und legen Sie fest, dass das erstellte Medienobjekt verwendet werden soll.
1. Veröffentlichen Sie das dem Programm zugeordnete Medienobjekt durch Erstellen eines OnDemand-Locators.  

	Vergewissern Sie sich, dass auf dem Streamingendpunkt, von dem aus Inhalte gestreamt werden sollen, mindestens eine reservierte Einheit für das Streaming vorhanden ist.
1. Starten Sie das Programm, wenn Sie bereit sind und mit Streaming und Archivierung beginnen möchten.
1. Beenden Sie das Programm, wenn Sie Streaming und Archivierung für das Ereignis beenden möchten.
1. Löschen Sie das Programm (und optional das Medienobjekt).   

Der Abschnitt [Livestreaming-Aufgaben](media-services-manage-channels-overview.md#tasks) enthält Links zu Themen, die das Ausführen der oben beschriebenen Aufgaben veranschaulichen.


##<a id="channel_input"></a>Kanaleingangskonfigurationen (Erfassung)

###<a id="ingest_protocols"></a>Erfassungs-Streamingprotokolle

Media Services unterstützt die Erfassung von Livefeeds mithilfe der folgenden Streamingprotokolle: 

- **Fragmented MP4 mit mehreren Bitraten**
 
- **RTMP  mit mehreren Bitraten** 

	Wenn das Erfassungs-Streamingprotokoll **RTMP** aktiviert ist, werden zwei Erfassungs-(Eingangs-)Endpunkte für den Kanal erstellt: 
	
	**Primäre URL**: Gibt die vollqualifizierte URL des primären RTMP-Erfassungsendpunkts für den Kanal an.

	**Sekundäre URL**(optional): Gibt die vollqualifizierte URL des sekundären RTMP-Erfassungsendpunkts für den Kanal an. 

	Verwenden Sie die sekundäre URL, um die Beständigkeit und Fehlertoleranz des Erfassungsstreams sowie das Failover und die Fehlertoleranz des Encoders zu verbessern. 
	
	- Verbesserung der Beständigkeit und Fehlertoleranz des Erfassungsstreams:
		
		Senden Sie mit einem Encoder dieselben Daten an die primäre und die sekundäre Erfassungs-URL. Die meisten RTMP-Encoder (beispielsweise Flash Media Encoder oder Wirecast) können primäre und sekundäre URLs verarbeiten.

	- So setzen Sie Encoder-Failover und -Fehlertoleranz um
		
		Erzeugen Sie mit mehreren Encodern dieselben Daten, und senden Sie sie an die primäre und die sekundäre Erfassungs-URL. Durch diesen Ansatz werden sowohl die Erfassungsbeständigkeit als auch die hohe Verfügbarkeit des Encoders verbessert. HINWEIS: Der Encoder muss Szenarios für hohe Verfügbarkeit unterstützen und intern zeitsynchronisiert werden. (Weitere Informationen finden Sie im Handbuch des Encoders.)
	
 	Das Verwenden einer sekundären Erfassungs-URL erfordert zusätzlichen Bandbreite. 

Beachten Sie, dass Sie auch eine einzige Bitrate in Ihrem Kanal erfassen können, aber da der Stream nicht verarbeitet wird, erhalten die Clientanwendungen ebenfalls einen Stream mit nur einer Bitrate (diese Option wird nicht empfohlen).

Informationen zu RTMP-Live-Encodern finden Sie unter [Azure Media Services RTMP Support and Live Encoders](http://go.microsoft.com/fwlink/?LinkId=532824) (in englischer Sprache).

Es gelten die folgenden Bedingungen:

- Stellen Sie sicher, dass Sie ausreichend ungenutzte Internetbandbreite zum Senden an die Erfassungspunkte haben. 
- Der eingehende Stream mit mehreren Bitraten kann maximal zehn Ebenen (oder Schichten) der Videoqualität und maximal fünf Audiospuren aufweisen.
- Die höchste durchschnittliche Bitrate für die Videoqualitätsebenen oder -schichten sollte unter 10 Mbit/s liegen.
- Die zusammengezählten durchschnittlichen Bitraten für alle Video- und Audiostreams sollten unter 25 Mbit/s liegen.
- Das Eingabeprotokoll kann während der Ausführung des Kanals oder seiner zugehörigen Programme nicht geändert werden. Wenn Sie verschiedene Protokolle benötigen, sollten Sie separate Kanäle für jedes Eingangsprotokoll erstellen. 


###Erfassungs-URLs (Endpunkte) 

Ein Kanal stellt einen Eingabeendpunkt (Erfassungs-URL) dar, den Sie im Live-Encoder angeben, damit dieser Streams an die Kanäle übertragen kann.   

Sie können die Erfassungs-URLs beim Erstellen des Kanals erhalten. Zum Abfragen dieser URLs muss sich der Kanal nicht im gestarteten Zustand befinden. Wenn Sie zum Übermitteln von Daten an den Kanal bereit sind, muss der Kanal gestartet werden. Sobald der Kanal mit dem Erfassen von Daten beginnt, können Sie Ihren Datenstrom über die Vorschau-URL anzeigen.

Sie können Fragmented MP4-Livestreams (Smooth Streaming) über eine SSL-Verbindung erfassen. Zur Erfassung über SSL stellen Sie sicher, dass die Erfassungs-URL auf HTTPS aktualisiert wurde. Derzeit kann RTMP nicht über SSL erfasst werden. 

###<a id="keyframe_interval"></a>Keyframe-Intervall

Wenn ein lokaler Live-Encoder Zum Erzeugen eines Streams mit mehreren Bitraten verwendet wird, gibt das Keyframe-Intervall die GOP-Dauer (wie von diesem externen Encoder verwendet) an. Sobald dieser eingehende Stream durch den Kanal empfangen wird, können Sie Ihren Livestream in einem der folgenden Formate für Wiedergabe-Clientanwendungen bereitstellen: Smooth Streaming, DASH und HLS. Bei Livestreaming wird HLS immer dynamisch verpackt. Standardmäßig berechnet Media Services das HLS Segment-Paketerstellungsverhältnis (Fragmente pro Segment) basierend auf dem Keyframe-Intervall, auch bezeichnet als Gruppe von Bildern, GOP, die vom Live-Encoder empfangen wird. 

Die folgende Tabelle zeigt, wie die Segmentdauer berechnet wird:

<table border="1">
<tr><th>Keyframe-Intervall</th><th>Paketerstellungsverhältnis für HLS-Segmente (FragmentsPerSegment)</th><th>Beispiel</th></tr>
<tr><td>kleiner oder gleich 3 Sekunden</td><td>3:1</td><td>Wenn das KeyFrameInterval (oder GOP) 2 Sekunden beträgt, ist das Standard-Paketerstellungsverhältnis für HLS-Segmente 3:1, und es wird ein HLS-Segment von 6 Sekunden erstellt.</td></tr>
<tr><td>3 bis 5 Sekunden</td><td>2:1</td><td>Wenn das KeyFrameInterval (oder GOP) 4 Sekunden beträgt, ist das Standard-Paketerstellungsverhältnis für HLS-Segmente 2:1, und es wird ein HLS-Segment von 8 Sekunden erstellt.</td></tr>
<tr><td>größer als 5 Sekunden</td><td>1:1</td><td>Wenn das KeyFrameInterval (oder GOP) 6 Sekunden beträgt, ist das Standard-Paketerstellungsverhältnis für HLS-Segmente 1:1, und es wird ein HLS-Segment von 6 Sekunden erstellt.</td></tr>
</table>

Sie können das Verhältnis der Fragmente pro Segment ändern, indem Sie den Ausgang des Kanals konfigurieren und FragmentsPerSegment für ChannelOutputHls festlegen. 

Sie können den Keyframe-Intervallwert auch durch Festlegen der KeyFrameInterval-Eigenschaft für ChannelInput ändern. 

Wenn Sie das KeyFrameInterval explizit festlegen, wird FragmentsPerSegment für das Paketerstellungsverhältnis für HLS-Segmente anhand der oben beschriebenen Regeln berechnet.  

Wenn Sie sowohl KeyFrameInterval als auch FragmentsPerSegment explizit festlegen, werden von Media Services die festgelegten Werte verwendet. 


###Zulässige IP-Adressen

Sie können die IP-Adressen definieren, über die Videos in diesen Kanal veröffentlicht werden können. Zulässige IP-Adressen können als einzelne IP-Adresse (z. B. "10.0.0.1"), IP-Adressbereiche mit einer IP-Adresse und einer CIDR-Subnetzmaske (z. B. "10.0.0.1/22") oder IP-Adressbereiche mit einer IP-Adresse und einer Subnetzmaske in Punkt-Dezimalschreibweise (z. B. "10.0.0.1(255.255.252.0)") angegeben werden. 

Wenn keine IP-Adressen angegeben sind und es keine Regeldefinition gibt, sind keine IP-Adressen zulässig. Um alle IP-Adressen zuzulassen, erstellen Sie eine Regel, und legen Sie "0.0.0.0/0" für sie fest.

##Kanalvorschau 

###Vorschau-URLs

Kanäle bieten einen Vorschauendpunkt (Vorschau-URL), mit dem Sie eine Vorschau des Streams anzeigen und überprüfen können, bevor Sie diesen weiter verarbeiten und übermitteln.

Sie können die Vorschau-URL beim Erstellen des Kanals erhalten. Zum Erhalten dieser URL muss sich der Kanal nicht im gestarteten Zustand befinden. 

Sobald der Kanal mit dem Erfassen von Daten beginnt, können Sie eine Vorschau des Streams anzeigen.

Beachten Sie, dass der Vorschaustream derzeit unabhängig vom angegebenen Eingabetyp nur im Fragmented MP4-Format (Smooth Streaming) bereitgestellt werden kann. Sie können den Smooth Stream mit dem [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)-Player testen. Sie können auch mit dem im Azure Management Portal gehosteten Player Ihren Stream wiedergeben.


###Zulässige IP-Adressen

Sie können die IP-Adressen definieren, die eine Verbindung zum Vorschauendpunkt herstellen können. Falls keine IP-Adressen angegeben sind, dürfen alle IP-Adressen eine Verbindung herstellen. Zulässige IP-Adressen können als einzelne IP-Adresse (z. B. "10.0.0.1"), IP-Adressbereiche mit einer IP-Adresse und einer CIDR-Subnetzmaske (z. B. "10.0.0.1/22") oder IP-Adressbereiche mit einer IP-Adresse und einer Subnetzmaske in Punkt-Dezimalschreibweise (z. B. "10.0.0.1(255.255.252.0)") angegeben werden.

##Kanalausgabe

Weitere Informationen finden Sie im Abschnitt [Festlegen von Keyframe-Intervallen](#keyframe_interval) wechseln.


##Programme von Kanälen

Ein Kanal ist Programmen zugewiesen, mit denen Sie die Veröffentlichung und Speicherung von Segmenten in einem Livestream steuern können. Kanäle verwalten Programme. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Stream von Inhalten aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist.

Indem Sie die Länge des **Archivierungsfensters** festlegen, können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt für das Programm beibehalten möchten. Dieser Wert kann von mindestens 5 Minuten bis zu einem Höchstwert von 25 Stunden eingestellt werden. Die Länge des Archivierungsfensters bestimmt außerdem die maximale Dauer, für die Clients von der aktuellen Liveposition aus rückwärts suchen können. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Dieser Wert der Eigenschaft legt außerdem fest, wie lange Clientmanifeste wachsen können.

Jedes Programm ist mit einem Medienobjekt verknüpft. Zum Veröffentlichen des Programms müssen Sie einen OnDemand-Locator für das zugehörige Medienobjekt erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Clients bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Programme, sodass Sie mehrere Archive desselben eingehenden Streams erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise besteht Ihre Geschäftsanforderung darin, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Zu diesem Zweck müssen Sie zwei gleichzeitig ausgeführte Programme erstellen. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

Vorhandene Programme sollten nicht für neue Ereignisse wiederverwendet werden. Erstellen und starten Sie stattdessen ein neues Programm für jedes Ereignis, wie im Abschnitt "Programmieren von Livestreaming-Anwendungen" beschrieben.

Starten Sie das Programm, wenn Sie bereit sind und mit Streaming und Archivierung beginnen möchten. Beenden Sie das Programm, wenn Sie Streaming und Archivierung für das Ereignis beenden möchten. 

Um archivierte Inhalte zu löschen, beenden Sie das Programm, löschen Sie es, und löschen Sie anschließend das zugehörige Medienobjekt. Eine Medienobjekt kann nicht gelöscht werden, wenn es von einem Programm verwendet wird. Zuerst muss das Programm gelöscht werden. 

Selbst nachdem Sie das Programm beendet und gelöscht haben, können die Benutzer archivierte Inhalte möglicherweise als Video on Demand streamen, solange Sie nicht das Medienobjekt löschen.

Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaming-Locator.

##Kanalstatus

Der aktuelle Status des Kanals. Mögliche Werte sind:

- Beendet. Dies ist der anfängliche Status des Kanals nach seiner Erstellung. In diesem Status können die Channel-Eigenschaften aktualisiert werden, Streaming ist jedoch nicht zulässig.
- Wird gestartet. Der Kanal wird gestartet. In diesem Status sind keine Updates oder Streaming zulässig. Wenn ein Fehler auftritt, kehr der Kanal zum Status "Beendet" zurück.
- Wird ausgeführt. Der Kanal kann Livestreams verarbeiten.
- Wird beendet. Der Kanal wird beendet. In diesem Status sind keine Updates oder Streaming zulässig.
- Wird gelöscht. Der Kanal wird gelöscht. In diesem Status sind keine Updates oder Streaming zulässig. 

##Untertitel und Werbeeinblendungen 

Die folgende Tabelle zeigt die unterstützten Untertitel- und Werbeeinblendungsstandards.

<table border="1">
<tr><th>Standard</th><th>Hinweise</th></tr>
<tr><td>CEA-708 und EIA-608 (708/608)</td><td>CEA-708 und EIA-608 sind Untertitelstandards für die USA und Kanada.<br/>Derzeit werden Untertitel nur unterstützt, wenn sie im codierten Eingabestream geführt werden. Sie müssen einen Live-Media Encoder verwenden, der Untertitel nach 608 oder 708 in den codierten Stream, die an Media Services gesendet wird, einfügen kann. Media Services sendet die Inhalte mit eingefügten Untertiteln an Ihre Zuschauer.</td></tr>
<tr><td>TTML in ISMT (Smooth Streaming-Texttracks)</td><td>Die dynamische Paketerstellung von Media Services ermöglicht Ihren Clients das Streamen von Inhalten in einem der folgenden Formate: MPEG DASH, HLS oder Smooth Streaming. Wenn Sie jedoch Fragmented MP4 (Smooth Streaming) mit Untertitel in ISMT (Smooth Streaming-Texttracks) erfassen, kann der Stream nur an Smooth Streaming-Clients gesendet werden.</td></tr>
<tr><td>SCTE-35</td><td>Hierbei handelt es sich um ein digitales Signalisierungssystem zum Einfügen von Werbeeinblendungen. Downstream-Empfänger verwenden das Signal zum Einfügen von Werbung in den Stream für die vorgesehene Zeit. SCTE-35 muss als Spur mit geringer Dichte in den Eingabestream gesendet werden.<br/>Beachten Sie, dass derzeit als Eingabestreamformat mit Werbesignalen nur Fragmented MP4 (Smooth Streaming) unterstützt wird. Das einzige unterstützte Ausgabeformat ist ebenfalls Smooth Streaming.</td></tr>
</table>

##Überlegungen

Wenn Sie mit einem lokalen Live-Encoder einen Datenstrom mit mehreren Bitraten an einem Kanal senden, gelten folgende Einschränkungen:

- Stellen Sie sicher, dass Sie ausreichend ungenutzte Internetbandbreite zum Senden an die Erfassungspunkte haben. 
- Der eingehende Stream mit mehreren Bitraten kann maximal zehn Videoqualitätsebenen (zehn Schichten) und maximal fünf Audiospuren aufweisen.
- Die höchste durchschnittliche Bitrate für die Videoqualitätsebenen oder -schichten sollte unter 10 Mbit/s liegen.
- Die zusammengezählten durchschnittlichen Bitraten für alle Video- und Audiostreams sollten unter 25 Mbit/s liegen.
- Das Eingabeprotokoll kann während der Ausführung des Kanals oder seiner zugehörigen Programme nicht geändert werden. Wenn Sie verschiedene Protokolle benötigen, sollten Sie separate Kanäle für jedes Eingangsprotokoll erstellen. 


Weitere Aspekte im Zusammenhang mit der Arbeit mit Kanälen und zugehörigen Komponenten:


- Eine Abrechnung erfolgt nur für ausgeführte Kanäle.
- Rufen Sie bei jeder Neukonfiguration des Live-Encoders die **Reset**-Methode für den Kanal auf. Bevor Sie den Kanal zurückzusetzen, müssen Sie das Programm beenden. Nachdem Sie den Kanal zurückgesetzt haben, starten Sie das Programm neu. 
- Ein Kanal kann nur beendet werden, wenn er sich im Zustand "Wird ausgeführt" befindet und alle Programme im Kanal beendet wurden.
- In der Standardeinstellung können Sie Ihrem Media Services-Konto nur fünf Kanäle hinzufügen. Weitere Informationen finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).
- Das Eingabeprotokoll kann während der Ausführung des Kanals oder seiner zugehörigen Programme nicht geändert werden. Wenn Sie verschiedene Protokolle benötigen, sollten Sie separate Kanäle für jedes Eingangsprotokoll erstellen. 

##<a id="tasks"></a>Aufgaben im Zusammenhang mit Livestreaming

###Einrichten des Computers

Informationen zum Einrichten des Computers finden Sie unter [Einrichten des Computers](media-services-set-up-computer.md).

###Konfigurieren von Streamingendpunkten

Eine Übersicht über Streamingendpunkte sowie Informationen zu deren Verwaltung finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md)

###Verwenden lokaler Live-Encoder für die Ausgabe von Streams mit variabler Bitrate an einen Kanal

Weitere Informationen finden Sie unter [Verwenden von Drittanbieter-Liveencodern mit Azure Media Services](https://msdn.microsoft.com/library/azure/dn783464.aspx).

###Verwalten von Kanälen, Programmen und Medienobjekten
Eine ausführliche Übersicht finden Sie unter [Verwalten von Kanälen und Programmen - Übersicht](media-services-manage-channels-overview.md).

Wählen Sie **Portal**, **.NET** oder **REST-API**, um Beispiele anzuzeigen.

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]

###Konfigurieren von Übermittlungsrichtlinien für Medienobjekte

Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

###Erstellen von Inhaltsschlüsseln

Erstellen Sie einen Inhaltsschlüssel für das Verschlüsseln Ihres Medienobjekts mit **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

###Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel 

Konfigurieren Sie Inhaltsschutz und Autorisierungsrichtlinien für Inhaltsschlüssel mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]


###Veröffentlichen von Medienobjekten

Veröffentlichen Sie Medienobjekte (durch Locator-Erstellung) mithilfe des **Azure-Verwaltungsportals** oder mit **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


###Aktivieren von Azure CDN

Media Services unterstützt die Integration in Azure CDN. Informationen zum Aktivieren von Azure CDN finden Sie unter [Verwalten von Streamingendpunkten in Media Services-Konten](media-services-manage-origins.md#enable_cdn).

###Skalieren eines Media Services-Kontos

Sie können **Media Services** skalieren, indem Sie die Anzahl der **reservierten Einheiten für das Streaming** angeben, die für Ihr Konto bereitgestellt werden sollen. 

Informationen zum Skalieren von Streamingeinheiten finden Sie unter: [Skalieren von Streamingeinheiten](media-services-manage-origins.md#scale_streaming_endpoints.md).


[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png

<!--HONumber=52-->