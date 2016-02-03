<properties 
	pageTitle="Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden" 
	description="In diesem Thema wird beschrieben, wie ein Kanal eingerichtet wird, von dem ein Single-Bitrate-Livedatenstrom aus einem lokalen Encoder empfangen und eine Livecodierung zu einem Datenstrom mit adaptiver Bitrate mit Media Services ausgeführt wird. Der Stream kann über einen oder mehrere Streamingendpunkte an Wiedergabe-Clientanwendungen übermittelt werden. Dazu dienen die folgenden adaptiven Streamingprotokolle: HLS, Smooth Stream, MPEG DASH, HDS." 
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
	ms.date="12/22/2015"  
	ms.author="juliako"/>

#Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden

##Übersicht

In Azure Media Services repräsentiert ein **Kanal** eine Pipeline zum Verarbeiten von Livestreaminginhalten. Es gibt zwei Arten, auf die Live-Eingabedatenströme von **Kanälen** empfangen werden können:

- Von einem lokalen Liveencoder wird Multi-Bitrate- **RTMP** oder -**Smooth Streaming** (fragmentiertes MP4) an den Kanal gesendet. Sie können die folgenden Liveencoder verwenden, von denen Multi-Bitrate-Smooth Streaming ausgegeben werden kann: Elemental, Envivio, Cisco. Von den folgenden Liveencodern wird RTMP ausgegeben: Adobe Flash Live, Telestream Wirecast und Tricaster-Transcoder. Die aufgenommenen Datenströme werden ohne weitere Verarbeitung durch die **Kanäle** geleitet. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.
- Ein Single-Bitrate-Datenstrom (in einem der folgenden Formate: **RTP** (MPEG-TS)), **RTMP** oder **Smooth Streaming** (fragmentiertes MP4)) wird an den **Kanal** gesendet, der zum Ausführen von Livecodierung mit Media Services aktiviert wurde. Vom **Kanal** wird dann eine Livecodierung des Single-Bitrate-Eingabedatenstroms in einen Multi-Bitrate-Videodatenstrom (adaptiv) ausgeführt. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.

Mit Media Services-Version 2.10 können Sie beim Erstellen eines Kanals angeben, wie der Eingabedatenstrom vom Kanal empfangen werden soll und ob vom Kanal eine Livecodierung des Datenstroms ausgeführt werden soll. Sie haben zwei Möglichkeiten:

- **None** (Keine): Geben Sie diesen Wert an, wenn ein lokaler Liveencoder verwendet werden soll, von dem ein Multi-Bitrate-Datenstrom ausgegeben wird. In diesem Fall wird der Eingabedatenstrom ohne Codierung an die Ausgabe geleitet. Bis zur Version 2.10 ist dies das Standardkanalverhalten. Ausführlichere Informationen zum Arbeiten mit Kanälen dieses Typs finden Sie unter [Arbeiten mit Kanälen, von denen Multi-Bitrate-Livedatenströme aus lokalen Encodern empfangen werden](media-services-manage-channels-overview.md).

- **Standard**: Wählen Sie diesen Wert, wenn Sie Media Services verwenden möchten, um einen Single-Bitrate-Livedatenstrom in einen Multi-Bitrate-Datenstrom zu codieren. Beachten Sie, dass das Live Encoding sich auf die Abrechnung auswirkt und dass die Beibehaltung des Status „Wird ausgeführt“ für einen Live Encoding-Kanal mit Gebühren verbunden ist. Es wird empfohlen, die Ausführung der Kanäle sofort zu beenden, wenn das Livestreaming-Ereignis abgeschlossen ist, um das Anfallen zusätzlicher Stundengebühren zu vermeiden.

>[AZURE.NOTE]In diesem Thema werden die Attribute der Kanäle erläutert, welche zum Ausführen der Livecodierung aktiviert sind (Codierungstyp **Standard**). Informationen zum Arbeiten mit Kanälen, die nicht zum Ausführen der Livecodierung aktiviert sind, finden Sie unter [Arbeiten mit Kanälen, von denen Multi-Bitrate-Livedatenströme aus lokalen Encodern empfangen werden](media-services-manage-channels-overview.md).


##Hinweise zur Gebührenberechnung

Für einen Live Encoding-Kanal beginnt die Berechnung von Gebühren, sobald der Status über die API in „Wird ausgeführt“ geändert wird. Sie können den Status auch im klassischen Azure-Portal oder im Azure Media Services-Explorer anzeigen (http://aka.ms/amse).

In der folgenden Tabelle ist dargestellt, wie Kanalzustände den Abrechnungszuständen in der API und im klassischen Azure-Portal zugeordnet werden. Beachten Sie, dass sich die Zustände zwischen der API und der Benutzeroberfläche des Portals leicht unterscheiden. Sobald ein Kanal über die API in den Zustand „Wird ausgeführt“ oder im klassischen Azure-Portal in den Zustand „Bereit“ oder „Streaming“ versetzt wird, wird die Abrechnung aktiv. Um die weitere Gebührenberechnung für den Kanal zu beenden, müssen Sie den Kanal über die API oder im klassischen Azure-Portal beenden. Sie sind für das Beenden Ihrer Kanäle verantwortlich, wenn Sie die Nutzung des Live Encoding-Kanals abgeschlossen haben. Wenn Sie einen Codierkanal nicht beenden, werden weiter Gebühren berechnet.

###<a id="states"></a>Kanalstatus und ihre Zuordnung zum Abrechnungsmodus 

Dies ist der aktuelle Status des Kanals. Mögliche Werte sind:

- **Stopped** (Beendet): Dies ist der anfängliche Status des Kanals nach seiner Erstellung (es sei denn, im Portal wurde das automatische Starten gewählt). In diesem Status werden keine Gebühren berechnet. In diesem Status können die Eigenschaften des Kanals aktualisiert werden. Ein Streaming ist jedoch nicht zulässig.
- **Starting** (Wird gestartet): Der Kanal wird gestartet. In diesem Status werden keine Gebühren berechnet. In diesem Status sind weder Updates noch Streaming zulässig. Wenn ein Fehler auftritt, wird der Kanal in den Status „Stopped“ (Angehalten) geschaltet.
- **Running** (Wird ausgeführt): Vom Kanal können Livestreams verarbeitet werden. Die Nutzung wird jetzt berechnet. Sie müssen den Kanal beenden, um zu verhindern, dass weiter Gebühren berechnet werden. 
- **Stopping** (Wird beendet): Der Kanal wird beendet. In diesem Übergangszustand erfolgt keine Berechnung. In diesem Status sind weder Updates noch Streaming zulässig.
- **Deleting** (Wird gelöscht): Der Kanal wird gelöscht. In diesem Übergangszustand erfolgt keine Berechnung. In diesem Status sind weder Updates noch Streaming zulässig.

In der folgenden Tabelle ist die Zuordnung der Kanalstatus mit den Abrechnungsmodi aufgeführt.
 
Kanalstatus|Portal-UI-Indikatoren|Werden Gebühren berechnet?
---|---|---
Wird gestartet|Wird gestartet|Nein (Übergangsstatus)
Wird ausgeführt|Bereit (keine ausgeführten Programme)<br/>oder<br/>Streaming (mindestens ein laufendes Programm)|JA
Wird beendet|Wird beendet|Nein (Übergangsstatus)
Beendet|Beendet|Nein

##Live Encoding-Workflow
Im folgenden Diagramm ist ein Livedatenstrom-Workflow dargestellt, bei dem ein Single-Bitrate-Datenstrom empfangen (in einem der folgenden Protokolle: RTMP, Smooth Streaming oder RTP (MPEG-TS)) und dann in einen Multi-Bitrate-Datenstrom codiert wird.

![Liveworkflow][live-overview]


##In diesem Thema

- Übersicht über ein [allgemeines Livestreamingszenario](media-services-manage-live-encoder-enabled-channels.md#scenario)
- [Beschreibung von Kanälen und zugehörigen Komponenten](media-services-manage-live-encoder-enabled-channels.md#channel)
- [Überlegungen](media-services-manage-live-encoder-enabled-channels.md#Considerations)


##<a id="scenario"></a>Allgemeines Livestreamingszenario

Im Folgenden werden grundlegende Schritte zum Erstellen allgemeiner Livestreaminganwendungen erläutert.

>[AZURE.NOTE] Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an „AMSLiveD@microsoft.com“, wenn Sie einen Kanal für längere Zeit laufen lassen müssen. Beachten Sie, dass für das Live Encoding Gebühren berechnet werden und dass diese Gebühren stundenweise anfallen, wenn Sie einen Live Encoding-Kanal im Zustand „Wird ausgeführt“ belassen. Es wird empfohlen, die Ausführung der Kanäle sofort zu beenden, wenn das Livestreaming-Ereignis abgeschlossen ist, um das Anfallen zusätzlicher Stundengebühren zu vermeiden.

1. Schließen Sie eine Videokamera an einen Computer an. Starten und konfigurieren Sie einen lokalen Liveencoder, von dem ein **Single**-Bitrate-Datenstrom in einem der folgenden Protokolle ausgegeben wird: RTMP, Smooth Streaming oder RTP (MPEG-TS). Weitere Informationen finden Sie unter [Microsoft Azure Media Services RTMP-Support und Liveencoder](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Dieser Schritt kann auch nach der Erstellung des Kanals ausgeführt werden.

1. Erstellen Sie einen Kanal, und starten Sie ihn.

1. Rufen Sie die Erfassungs-URL des Kanals ab.

	Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.
1. Rufen Sie die Vorschau-URL des Kanals ab. 

	Verwenden Sie diese URL, um sicherzustellen, dass der Livestream ordnungsgemäß vom Kanal empfangen wird.

3. Erstellen Sie ein Programm.

	Im klassischen Azure-Portal wird beim Erstellen eines Programms auch ein Medienobjekt erstellt.

	Wenn Sie hingegen .NET SDK oder REST verwenden, müssen Sie beim Erstellen eines Programms ein Medienobjekt erstellen und angeben, dass es verwendet werden soll. 
1. Veröffentlichen Sie das mit dem Programm verknüpfte Medienobjekt.   

	Stellen Sie sicher, dass auf dem Streamingendpunkt, von dem Sie Inhalte streamen möchten, mindestens eine für das Streaming reservierte Einheit verfügbar ist.
1. Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm.
2. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.
1. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.
1. Löschen Sie das Programm (und optional das Medienobjekt).   

>[AZURE.NOTE] Es ist sehr wichtig, das Beenden eines Live Encoding-Kanals nicht zu vergessen. Beachten Sie, dass das Live Encoding stundenweise abgerechnet wird und dass die Beibehaltung des Status „Wird ausgeführt“ für einen Live Encoding-Kanal mit Gebühren verbunden ist. Es wird empfohlen, die Ausführung der Kanäle sofort zu beenden, wenn das Livestreaming-Ereignis abgeschlossen ist, um das Anfallen zusätzlicher Stundengebühren zu vermeiden.


Im Abschnitt [Livestreaming-Aufgaben](media-services-manage-channels-overview.md#tasks) finden Sie Verknüpfungen zu Themen, in welchen veranschaulicht wird, wie die beschriebenen Aufgaben auszuführen sind.


##<a id="channel"></a>Eingabe-/Erfassungskonfigurationen des Kanals

###<a id="Ingest_Protocols"></a>Erfassungsstreamingprotokoll

Wenn der **Encodertyp** die Einstellung **Standard** aufweist, gibt es folgende gültige Optionen:

- **RTP** (MPEG-TS): MPEG-2-Transportdatenstrom über RTP  
- Single-Bitrate **RTMP**
- Single-Bitrate **Fragmentiertes MP4** (Smooth Streaming)

Weitere Informationen finden Sie unter [Microsoft Azure Media Services RTMP-Support und Liveencoder](http://go.microsoft.com/fwlink/?LinkId=532824).

####RTP (MPEG-TS) – MPEG-2-Transportdatenstrom über RTP.  

Typisches Anwendungsbeispiel:

Professionelle Sendeanstalten arbeiten in der Regel mit lokalen High-End-Liveencodern von Herstellern wie Elemental Technologies, Ericsson, Ateme, Imagine oder Envivio, um Datenströme zu senden. Diese werden häufig in Verbindung mit einer IT-Abteilung und privaten Netzwerken verwendet.

Überlegungen:

- Es wird dringend empfohlen, als Eingabe einen Single-Program-Transportdatenstrom (SPTS) zu verwenden. 
- Sie können bis zu 8 Audiodatenströme mit MPEG-2 TS über RTP eingeben. 
- Der Videodatenstrom muss eine durchschnittliche Bitrate unter 15-Mbit/s aufweisen.
- Die aggregierte durchschnittliche Bitrate der Audiodatenströme muss unter 1 Mbit/s liegen.
- Folgende Codecs werden unterstützt:
	- MPEG-2/H.262-Video 
		
		- Main Profile (4:2:0)
		- High Profile (4:2:0, 4:2:2)
		- 422 Profile (4:2:0, 4:2:2)

	- MPEG-4 AVC/H.264-Video
	
		- Baseline, Main, High Profile (8-Bit 4:2:0)
		- High 10 Profile (10-Bit 4:2:0)
		- High 422 Profile (10-Bit 4:2:2)


	- MPEG-2 AAC-LC-Audio
	
		- Mono, Stereo, Surround (5.1, 7.1)
		- ADTS Paketerstellung im MPEG-2-Format

	- Dolby Digital-Audio (AC-3)

		- Mono, Stereo, Surround (5.1, 7.1)

	- MPEG-Audio (Layer II und III)
			
		- Mono, Stereo

- Zu den empfohlenen Sendeencodern gehören:
	- Ateme AM2102
	- Ericsson AVP2000
	- eVertz 3480
	- Ericsson RX8200
	- Imagine Communications Selenio ENC 1
	- Imagine Communications Selenio ENC 2
	- AdTec EN-30
	- AdTec EN-91P
	- AdTec EN-100
	- Harmonic ProStream 1000
	- Thor H-2 4HD-EM
	- eVertz 7880 SLKE
	- Cisco Spinnaker
	- Elemental Live

####<a id="single_bitrate_RTMP"></a>Single-Bitrate-RTMP

Überlegungen:

- Der eingehende Datenstrom darf kein Multi-Bitrate-Video enthalten.
- Der Videodatenstrom muss eine durchschnittliche Bitrate unter 15-Mbit/s aufweisen.
- Der Audiodatenstrom muss eine durchschnittliche Bitrate unter 1-Mbit/s aufweisen.
- Folgende Codecs werden unterstützt:

	- MPEG-4 AVC/H.264-Video  
	
		- Baseline, Main, High Profile (8-Bit 4:2:0)
		- High 10 Profile (10-Bit 4:2:0)
		- High 422 Profile (10-Bit 4:2:2)

	- MPEG-2 AAC-LC-Audio

		- Mono, Stereo, Surround (5.1, 7.1)
		- 44,1 kHz-Samplingrate
		- ADTS Paketerstellung im MPEG-2-Format
	
- Zu den empfohlenen Encodern gehören:

	- Telestream Wirecast
	- Flash Media Live Encoder
	- Tricaster

####Fragmentiertes Single-Bitrate-MP4 (Smooth Streaming)

Typisches Anwendungsbeispiel:

Verwenden Sie lokale Liveencoder von Herstellern wie Elemental Technologies, Ericsson, Ateme oder Envivio, um den Eingabedatenstrom über das offene Internet an ein Azure-Rechenzentrum in der Nähe zu senden.

Überlegungen:

Wie bei [Single-Bitrate-RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

####Weitere Überlegungen

- Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal. 
- Die maximale Auflösung für den eingehenden Videodatenstrom beträgt 1920 x 1080, und höchstens 60 Felder pro Sekunde (verschachtelt) bzw. 30 Bilder pro Sekunde (progressiv).


###Erfassungs-URLs (Endpunkte) 

Mit einem Kanal ist ein Eingabeendpunkt (Erfassungs-URL) verfügbar, den Sie im Liveencoder angeben, damit vom Encoder Datenströme an Ihre Kanäle übertragen werden können.

Wenn Sie einen Kanal erstellen, können Sie die Erfassungs-URLs abrufen. Der Kanal muss dazu nicht den Status **Running** (Wird ausgeführt) aufweisen. Wenn Sie beginnen möchten, Daten an den Kanal zu senden, ist der Status **Running** (Wird ausgeführt) hingegen erforderlich. Wenn die Datenerfassung durch den Kanal begonnen hat, können Sie über die Vorschau-URL eine Vorschau Ihres Datenstroms ansehen.

Sie können optional einen Livedatenstrom mit fragmentiertem MP4 (Smooth Streaming) über eine SSL-Verbindung erfassen. Zur Erfassung über SSL stellen Sie sicher, dass die Erfassungs-URL auf HTTPS aktualisiert wurde.

###Zulässige IP-Adressen

Sie können die IP-Adressen definieren, die zum Veröffentlichen von Videos in diesem Kanal zugelassen sind. Zulässige IP-Adressen können als eine einzelne IP-Adresse (z. B. „10.0.0.1“), als IP-Adressbereich mithilfe einer IP-Adresse und einer CIDR-Subnetzmaske (z. B. „10.0.0.1/22“) oder als IP-Adressbereich mithilfe einer Subnetzmaske in punktierter Dezimalschreibweise (z. B. „10.0.0.1(255.255.252.0)“) angegeben werden.

Wenn keine IP-Adressen angegeben werden und keine Regeldefinition vorhanden ist, so ist keine IP-Adresse zulässig. Um alle IP-Adressen zuzulassen, erstellen Sie eine Regel und legen 0.0.0.0/0 fest.


##Kanalvorschau 

###Vorschau-URLs

Mit den Kanälen ist ein Vorschauendpunkt (Vorschau-URL) verfügbar, mit dem Sie eine Vorschau anzeigen und Ihren Datenstrom vor der weiteren Verarbeitung und Übermittlung überprüfen können.

Wenn Sie einen Kanal erstellen, können Sie die Vorschau-URL abrufen. Der Kanal muss zum Abrufen der URL nicht den Status **Running** (Wird ausgeführt) aufweisen.

Sobald der Kanal mit dem Erfassen von Daten beginnt, können Sie eine Vorschau des Streams anzeigen.

>[AZURE.NOTE] Zurzeit kann die Vorschau des Datenstroms unabhängig vom angegebenen Eingabetyp nur in fragmentiertem MP4-Format (Smooth Streaming) geliefert werden. Sie können den Player [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) verwenden, um den Smooth Stream zu testen. Sie können auch einen Player verwenden, der im klassischen Azure-Portal gehostet wird, um Ihren Datenstrom anzuzeigen.

###Zulässige IP-Adressen

Sie können die IP-Adressen definieren, die zum Herstellen einer Verbindung mit dem Vorschauendpunkt zugelassen sind. Wenn keine IP-Adressen angegeben werden, wird jede IP-Adresse zugelassen. Zulässige IP-Adressen können als eine einzelne IP-Adresse (z. B. „10.0.0.1“), als IP-Adressbereich mithilfe einer IP-Adresse und einer CIDR-Subnetzmaske (z. B. „10.0.0.1/22“) oder als IP-Adressbereich mithilfe einer Subnetzmaske in punktierter Dezimalschreibweise (z. B. „10.0.0.1(255.255.252.0)“) angegeben werden.

##Einstellungen für die Livecodierung

In diesem Abschnitt wird beschrieben, wie die Einstellungen für den Liveencoder innerhalb des Kanals angepasst werden können, wenn der **Codierungstyp** des Kanals die Einstellung **Standard** aufweist.

>[AZURE.NOTE] Bei der Eingabe mehrerer Sprachspuren und beim Ausführen von Live Encoding mit Azure wird nur RTP für die Eingabe mehrerer Sprachen unterstützt. Sie können bis zu 8 Audiodatenströme mit MPEG-2 TS über RTP definieren. Das Erfassen mehrerer Audiospuren mit RTMP oder Smooth Streaming wird derzeit nicht unterstützt. Beim Durchführen von Live Encoding mit [lokalen Liveencodern](media-services-manage-channels-overview.md) gibt es keine solche Einschränkung, weil alle an AMS gesendeten Daten ohne weitere Verarbeitung über einen Kanal weitergeleitet werden.

###Quelle für AD-Marker

Sie können die Quelle für AD-Markersignale angeben. Der Standardwert lautet **Api**, der Liveencoder im Kanal soll also eine asynchrone **AD-Marker-API** überwachen.

Die andere gültige Option lautet **Scte35** (nur zulässig, wenn das Erfassungsstreamingprotokoll die Einstellung „RTP (MPEG-TS)“ aufweist. Wenn die Einstellung „Scte35“ lautet, werden Encoder SCTE 35-Signale aus dem RTP (MPEG TS)-Eingabedatenstrom analysiert.

###CEA 708-Untertitel

Dies ist ein optionales Kennzeichen für den Liveencoder, CEA 708-Untertiteldaten, die in das eingehende Video eingebettet sind, zu ignorieren. Wenn das Kennzeichen die Einstellung „False“ (Standard) aufweist, werden CEA 708-Daten erkannt und in die Ausgabe-Videodatenströme eingefügt.

###Videodatenstrom

Optional. Hier wird der Eingabe-Videodatenstrom beschrieben. Wird dieses Feld frei gelassen, wird der Standardwert verwendet. Diese Einstellung ist nur zulässig, wenn das Eingabestreamingprotokoll die Einstellung „RTP (MPEG-TS)“ aufweist.

####Index

Mit diesem nullbasierten Index wird angegeben, welcher Eingabe-Videodatenstrom vom Liveencoder im Kanal verarbeitet werden soll. Die Einstellung gilt nur, wenn das Erfassungsstreamingprotokoll die Einstellung „RTP (MPEG-TS)“ aufweist.

Der Standardwert lautet null. Es wird empfohlen, als Eingabe einen Single-Program-Transportdatenstrom (SPTS) zu verwenden. Wenn der Eingabedatenstrom mehrere Programme enthält, wird die Programmkartentabelle (PMT) in der Eingabe vom Liveencoder analysiert. Eingaben, welche die Datenstrom-Typnamen „MPEG-2-Video“ oder „H.264“ aufweisen, werden identifiziert und in der Reihenfolge angeordnet, die in der PMT angegeben ist. Der nullbasierte Index wird zum Abrufen des n-ten Eintrags in dieser Anordnung verwendet.

###Audiodatenstrom

Optional. Hier wird der Eingabe-Audiodatenstrom beschrieben. Wird dieses Feld frei gelassen, werden die angegebenen Standardwerte verwendet. Diese Einstellung ist nur zulässig, wenn das Eingabestreamingprotokoll die Einstellung „RTP (MPEG-TS)“ aufweist.

####Index

Es wird empfohlen, als Eingabe einen Single-Program-Transportdatenstrom (SPTS) zu verwenden. Wenn der Eingabedatenstrom mehrere Programme enthält, wird die Programmkartentabelle (PMT) in der Eingabe vom Liveencoder analysiert. Eingaben, welche die Datenstrom-Typnamen „MPEG-2 AAC ADTS“, „AC-3 System-A“, „AC-3 System-B“, „MPEG-2 Private PES“, „MPEG-1 Audio“ oder „MPEG-2 Audio“ aufweisen, werden identifiziert und in der Reihenfolge angeordnet, die in der PMT angegeben ist. Der nullbasierte Index wird zum Abrufen des n-ten Eintrags in dieser Anordnung verwendet.

####Sprache

Dies ist die Sprachen-ID des Audiodatenstroms entsprechend ISO 639-2, z. B. „DEU“. Wenn keine ID vorhanden ist, gilt die Standardeinstellung „UND“ (undefiniert).

Es können bis zu 8 Audiodatenstrom-Sätze angegeben werden, wenn die Eingabe an den Kanal als MPEG-2 TS über RTP erfolgt. Zwei Einträge mit dem gleichen Indexwert sind jedoch nicht möglich.

###<a id="preset"></a>Systemvoreinstellung

Hier ist die Voreinstellung angegeben, die vom Liveencoder in diesem Kanal verwendet werden soll. Derzeit lautet der einzig zulässige Wert **Default720p** (Standard).

Beachten Sie, dass Sie sich an amslived at Microsoft Punkt com wenden müssen, wenn Sie benutzerdefinierte Voreinstellungen verwenden müssen.

Durch **Default720p** wird das Video in die folgenden 7 Ebenen codiert:


####Ausgabe-Videodatenstrom

BitRate|Breite|Höhe|Max. Bilder/s|Profil|Name des Ausgabedatenstroms
---|---|---|---|---|---
3500|1280|720|30|Hoch|Video\_1280x720\_3500kbps
2200|960|540|30|Main|Video\_960x540\_2200kbps
1350|704|396|30|Main|Video\_704x396\_1350kbps
850|512|288|30|Main|Video\_512x288\_850kbps
550|384|216|30|Main|Video\_384x216\_550kbps
350|340|192|30|Grundwert|Video\_340x192\_350kbps
200|340|192|30|Grundwert|Video\_340x192\_200kbps


####Ausgabe-Audiodatenstrom

Audio wird mit einer Samplingrate von 44,1 kHz in Stereo-AAC-LC mit 64 KBit/s codiert.

##Signalisieren von Werbespots

Wenn Livecodierung bei Ihrem Kanal aktiviert ist, verfügen Sie über eine videoverarbeitende Komponente in der Pipeline, die Sie bearbeiten können. Sie können durch den Kanal Slates und/oder Werbespots in den ausgehenden Datenstrom mit adaptiver Bitrate einfügen lassen. Bei Slates handelt es sich um Standbilder, mit denen Sie den Live-Eingabefeed in bestimmten Situationen (z. B. bei Werbepausen) verdecken können. Bei Werbesignalen handelt es sich um zeitlich synchronisierte Signale, die Sie in den ausgehenden Datenstrom einbinden, damit vom Videoplayer bestimmte Aktionen ausgeführt werden, z. B. Umschalten zu einem Werbespot um eine bestimmte Zeit. In diesem [Blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) finden Sie einen Überblick über den SCTE-35-Signalmechanismus, der zu diesem Zweck verwendet wird. Im Folgenden wird ein typisches Szenario beschrieben, das Sie in Ihr Liveereignis implementieren könnten.

1. Zeigen Sie Ihren Zuschauern ein PRE-EVENT-Bild an, bevor das Programmereignis beginnt.
1. Zeigen Sie Ihren Zuschauern ein POST-EVENT-Bild an, wenn das Programmereignis endet.
1. Zeigen Sie Ihren Zuschauern ein ERROR-EVENT-Bild an, wenn während des Programmereignisses ein Fehler (z. B. Stromausfall) auftritt.
1. Zeigen Sie Ihren Zuschauern ein AD-BREAK-Bild an, um den Liveereignisfeed während Werbepausen auszublenden.

Beim Signalisieren von Werbespots können Sie die folgenden Eigenschaften festlegen:

###Duration (Dauer)

Dies ist die Dauer der Werbepause in Sekunden. Es muss sich um einen positiven Wert ungleich null handeln, damit die Werbepause gestartet werden kann. Wird eine Werbepause ausgeführt, und die Dauer für die CueId der aktuellen Werbepause lautet null, so wird die Werbepause abgebrochen.

###CueId

Dies ist der eindeutige Bezeichner einer Werbepause, der von nachgeschalteten Anwendungen verwendet wird, um entsprechende Aktionen auszuführen. Es muss sich um eine positive ganze Zahl handeln. Sie können für diesen Wert eine zufällige positive ganze Zahl festlegen oder zum Nachverfolgen der CueIds ein vorgeschaltetes System verwenden. Achten Sie darauf, alle IDs zu positiven ganzen Zahlen zu normalisieren, bevor Sie sie über die API senden.

###Show slate (Slate anzeigen)

Optional. Hierdurch wird dem Liveencoder signalisiert, bei Werbepausen zum [Standard-Slatebild](media-services-manage-live-encoder-enabled-channels.md#default_slate) zu wechseln und den eingehenden Videodatenstrom auszublenden. Im Slatezustand wird auch die Audioausgabe stummgeschaltet. Die Standardeinstellung lautet **false**.
 
Das zu verwendende Bild wird zum Zeitpunkt der Kanalerstellung über die Eigenschaft „Default slate asset Id“ angegeben. Das Slatebild wird gestreckt, um es an die Anzeigebildgröße anzupassen.


##Einfügen von Slatebildern

Dem Liveencoder im Kanal kann signalisiert werden, zu einem Slatebild zu wechseln. Auch das Beenden eines aktuellen Slates kann signalisiert werden.

Der Liveencoder kann so konfiguriert werden, dass in bestimmten Situationen, z. B. bei Werbepausen, zu einem Slatebild gewechselt und das eingehende Videosignal ausgeblendet wird. Wird kein solches Slate konfiguriert, so wird das eingehende Videosignal während dieser Werbepause nicht maskiert.

###Duration (Dauer)

Dies ist die Dauer des Slates in Sekunden. Es muss sich um einen positiven Wert ungleich null handeln, damit das Slate gestartet werden kann. Wird ein Slate ausgeführt und eine Dauer von null angegeben, so wird das aktuelle Slate beendet.

###Insert slate on ad marker (Slate bei AD-Marker einfügen)

Wenn für diese Einstellung der Wert „true“ festgelegt ist, wird der Liveencoder zum Einfügen eines Slatebilds während Werbepausen konfiguriert. Der Standardwert lautet „true“.

###<a id="default_slate"></a>Default slate Asset Id (ID des Slate-Standardmedienobjekts)

Optional. Hier wird die ID des Media Services-Medienobjekts angegeben, welches das Slatebild enthält. Der Standardwert lautet null.

**Hinweis**: Vor dem Erstellen des Kanals muss das Slate-Bild mit den folgenden Einschränkungen als dediziertes Medienobjekt hochgeladen werden (in diesem Medienobjekt dürfen sich keine anderen Dateien befinden).

- Auflösung von höchstens 1920 x 1080
- Größe von maximal 3 MB
- Der Dateinamen muss die Erweiterung *JPG aufweisen.
- Das Bild muss als einzige AssetFile in ein Medienobjekt hochgeladen werden. Diese AssetFile sollte als primäre Datei gekennzeichnet werden. Bei dem Medienobjekt ist keine Speicherverschlüsselung möglich.

Wird keine **default slate Asset Id** (ID des Slate-Standardmedienobjekts) angegeben, und die Option **insert slate on ad marker** (Slate bei AD-Marker einfügen) weist die Einstellung **true** auf, so wird ein Standardbild von Azure Media Services verwendet, um den eingehenden Videodatenstrom zu verdecken. Im Slatezustand wird auch die Audioausgabe stummgeschaltet.


##Kanalprogramme

Einem Kanal sind Programme zugeordnet, mit denen Sie das Veröffentlichen und Speichern von Segmenten in einem Livestream steuern können. Die Programme werden von den Kanälen verwaltet. Die Beziehung zwischen Kanal und Programm ähnelt der bei herkömmlichen Medien: Ein Kanal weist einen konstanten Datenstrom von Inhalten auf, und ein Programm ist auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet.

Über die Länge des **Archivierungsfensters** können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt des Programms beibehalten möchten. Es können Werte zwischen mindestens 5 Minuten und höchstens 25 Stunden eingestellt werden. Von der Länge des Archivierungsfensters wird außerdem bestimmt, wie lange von Clients von der aktuellen Liveposition aus maximal rückwärts gesucht werden kann. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Der Wert dieser Eigenschaft legt außerdem fest, wie lange Clientmanifeste wachsen können.

Jedem Programm ist ein Medienobjekt zugeordnet, von welchem die gestreamten Inhalte gespeichert werden. Ein Medienobjekt ist einem BLOB-Container im Azure Storage-Konto zugeordnet, und die im Medienobjekt enthaltenen Dateien werden als BLOBs in diesem Container gespeichert. Zum Veröffentlichen des Programms, damit Ihre Kunden den Datenstrom sehen können, müssen Sie einen OnDemand-Locator für das zugehörige Medienobjekt erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Kunden bereitstellen können.

Von einem Kanal können bis zu drei Programme gleichzeitig ausgeführt werden, sodass Sie von einem eingehenden Datenstrom mehrere Archive erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

Verwenden Sie vorhandene Programme nicht erneut für nachfolgende Ereignisse. Erstellen und starten Sie stattdessen für jedes Ereignis ein neues Programm wie im Abschnitt „Programmieren von Livestreaminganwendungen“ beschrieben.

Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.

Zum Löschen von archivierten Inhalten beenden und löschen Sie das Programm und löschen anschließend das zugehörige Medienobjekt. Medienobjekte können nicht gelöscht werden, wenn sie von Programmen verwendet werden. Zuerst muss das betreffende Programm gelöscht werden.

Auch nach dem Beenden und Löschen des Programms können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Medienobjekt nicht gelöscht wurde.

Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaming-Locator.


##Abrufen einer Miniaturansicht des Livefeeds

Wenn Livecodierung aktiviert ist, können Sie jetzt eine Vorschau des Livefeeds abrufen, sobald er den Kanal erreicht. Dies kann ein wertvolles Tool sein, um zu überprüfen, ob Ihre Livefeed den Kanal tatsächlich erreicht.

##<a id="states"></a>Kanalstatus und ihre Zuordnung zum Abrechnungsmodus 

Dies ist der aktuelle Status des Kanals. Mögliche Werte sind:

- **Stopped** (Beendet): Dies ist der Ausgangsstatus des Kanals nach der Erstellung. In diesem Status können die Eigenschaften des Kanals aktualisiert werden. Ein Streaming ist jedoch nicht zulässig.
- **Starting** (Wird gestartet): Der Kanal wird gestartet. In diesem Status sind weder Updates noch Streaming zulässig. Wenn ein Fehler auftritt, wird der Kanal in den Status „Stopped“ (Angehalten) geschaltet.
- **Running** (Wird ausgeführt): Vom Kanal können Livestreams verarbeitet werden.
- **Stopping** (Wird beendet): Der Kanal wird beendet. In diesem Status sind weder Updates noch Streaming zulässig.
- **Deleting** (Wird gelöscht): Der Kanal wird gelöscht. In diesem Status sind weder Updates noch Streaming zulässig.

In der folgenden Tabelle ist die Zuordnung der Kanalstatus mit den Abrechnungsmodi aufgeführt.
 
Kanalstatus|Portal-UI-Indikatoren|In Rechnung gestellt?
---|---|---
Wird gestartet|Wird gestartet|Nein (Übergangsstatus)
Wird ausgeführt|Bereit (keine ausgeführten Programme)<br/>oder<br/>Streaming (mindestens ein laufendes Programm)|Ja
Wird beendet|Wird beendet|Nein (Übergangsstatus)
Beendet|Beendet|Nein


>[AZURE.NOTE] Derzeit beträgt die durchschnittliche Dauer bis zum Start des Kanals ca. 2 Minuten, aber in Einzelfällen kann dies auch mehr als 20 Minuten dauern. Das Zurücksetzen des Kanals kann bis zu 5 Minuten dauern.


##<a id="Considerations"></a>Überlegungen

- Tritt für einen Kanal des Codierungstyps **Standard** ein Verlust des Eingabequellen-/Beitragsfeeds auf, wird der Verlust kompensiert, indem der Quellvideo-/-audiodatenstrom durch eine Fehlertafel (Fehlerslate) und Stille ersetzt wird. Der Kanal gibt solange eine Tafel aus, bis der Eingabe-/Beitragsfeed fortgesetzt wird. Es empfiehlt sich, einen Livekanal nicht länger als 2 Stunden in diesem Zustand zu belassen. Nach diesem Punkt ist weder das Verhalten des Kanals nach erneuter Verbindung der Eingabe noch sein Verhalten als Reaktion auf einen Rücksetzungsbefehl vorhersagbar. Sie müssen den Kanal beenden, ihn löschen und dann einen neuen erstellen.
- Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal. 
- Rufen Sie bei jeder Neukonfiguration des Liveencoders die **Reset**-Methode für den Kanal auf. Bevor Sie den Kanal zurückzusetzen, müssen Sie das Programm beenden. Wenn Sie den Kanal zurückgesetzt haben, starten Sie das Programm neu. 
- Ein Kanal kann nur beendet werden, wenn er den Status „Wird ausgeführt“ aufweist und alle Programme im Kanal beendet wurden.
- In der Standardeinstellung können Sie Ihrem Media Services-Konto nicht mehr als 5 Livekanäle hinzufügen. Hierbei handelt es sich um eine weiche Kontingentgrenze bei allen neuen Konten. Weitere Informationen finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).
- Sie können das Eingabeprotokoll nicht ändern, während der Kanal oder seine zugehörigen Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Eingabeprotokoll einen separaten Kanal.
- Es werden nur Kanäle in Rechnung gestellt, die den Status **Running** (Wird ausgeführt) aufweisen. Weitere Informationen finden Sie in [diesem Abschnitt](media-services-manage-live-encoder-enabled-channels.md#states).
- Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an Amslived unter Microsoft Punkt Com, wenn Sie einen Kanal für längere Zeit laufen lassen müssen.
- Stellen Sie sicher, dass auf dem Streamingendpunkt, von dem Sie Inhalte streamen möchten, mindestens eine für das Streaming reservierte Einheit verfügbar ist.
- Bei der Eingabe mehrerer Sprachspuren und beim Ausführen von Live Encoding mit Azure wird nur RTP für die Eingabe mehrerer Sprachen unterstützt. Sie können bis zu 8 Audiodatenströme mit MPEG-2 TS über RTP definieren. Das Erfassen mehrerer Audiospuren mit RTMP oder Smooth Streaming wird derzeit nicht unterstützt. Beim Durchführen von Live Encoding mit [lokalen Liveencodern](media-services-manage-channels-overview.md) gibt es keine solche Einschränkung, weil alle an AMS gesendeten Daten ohne weitere Verarbeitung über einen Kanal weitergeleitet werden.
- Vergessen Sie nicht, IHRE KANÄLE ZU BEENDEN, wenn Sie fertig sind. Wenn Sie dies nicht tun, werden weiter Gebühren berechnet. 

##Bekannte Probleme

- Die durchschnittliche Dauer bis zum Start des Kanals wurde auf ca. 2 Minuten verkürzt, aber in Einzelfällen kann dies immer noch mehr als 20 Minuten dauern.
- Die RTP-Unterstützung zielt auf professionelle Sendeanstalten ab. Bitte lesen Sie die Hinweise zu RTP in [diesem](http://azure.microsoft.com/blog/2015/04/13/an-introduction-to-live-encoding-with-azure-media-services/) Blog.
- Die Slate-Bilder sollten den [hier](media-services-manage-live-encoder-enabled-channels.md#default_slate) beschriebenen Einschränkungen entsprechen. Wenn Sie versuchen, einen Kanal mit einem Standard-Slatebild zu erstellen, das größer ist als 1920 x 1080, tritt bei der Anforderung ein Fehler auf.
- Wir weisen noch einmal darauf hin: Vergessen Sie nicht, IHRE KANÄLE ZU BEENDEN, wenn Sie mit dem Streaming fertig sind. Wenn Sie dies nicht tun, werden weiter Gebühren berechnet.

###Erstellen von Kanälen, von denen eine Livecodierung eines Single-Bitrate-Datenstroms in einen Datenstrom mit adaptiver Bitrate ausgeführt wird 

Wählen Sie **Portal**, **.NET** oder **REST API**, um auf Informationen zum Erstellen und Verwalten von Kanälen und Programmen zuzugreifen.

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Verwandte Themen

[Bereitstellen von Livestreamingereignissen mit Azure Media Services](media-services-live-streaming-workflow.md)

[Media Services-Konzepte](media-services-concepts.md)

[Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
 

<!---HONumber=AcomDC_0107_2016-->