<properties 
	pageTitle="Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services" 
	description="Diese Spezifikation beschreibt das Protokoll und Format für die Fragmented MP4-basierte Erfassung des Live-Streamings für Microsoft Azure Media Services. Microsoft Azure Media Services bietet einen Live-Streaming-Dienst, mit dem Kunden Liveereignisse und Sendungsinhalte mit Microsoft Azure als Cloud-Plattform in Echtzeit streamen können. Zum Zeitpunkt des Verfassens des Dokuments ist das vorcodierte Fragmented MP4 der einzige Mechanismus der Echtzeiterfassung für das Live-Streaming in Microsoft Azure Media Services. In diesem Dokument werden auch optimale Verfahren bei der Erstellung hoch redundanter und stabiler Mechanismen der Echtzeiterfassung beschrieben." 
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
	ms.date="05/21/2015" 
	ms.author="juliako"/>

#Spezifikation der Fragmented MP4-Echtzeiterfassung für Azure Media Services

Diese Spezifikation beschreibt das Protokoll und Format für die Fragmented MP4-basierte Erfassung des Live-Streamings für Microsoft Azure Media Services. Microsoft Azure Media Services bietet einen Live-Streaming-Dienst, mit dem Kunden Liveereignisse und Sendungsinhalte mit Microsoft Azure als Cloud-Plattform in Echtzeit streamen können. Zum Zeitpunkt des Verfassens des Dokuments ist das vorcodierte Fragmented MP4 der einzige Mechanismus der Echtzeiterfassung für das Live-Streaming in Microsoft Azure Media Services. In diesem Dokument werden auch optimale Verfahren bei der Erstellung hoch redundanter und stabiler Mechanismen der Echtzeiterfassung beschrieben.

##Konformität der Schlüsselbegriffe

Die Schlüsselwörter "MUSS", "DARF NICHT", "ERFORDERLICH", "SOLL", "SOLL NICHT", "SOLLTE", "SOLLTE NICHT", "EMPFOHLEN", "KANN" und "OPTIONAL" in diesem Dokument sind wie in RFC 2119 zu verstehen.

##Schematische Darstellung des Diensts 

In der folgenden schematischen Darstellung ist die allgemeine Architektur des Live-Streaming-Diensts in Microsoft Azure Media Services dargelegt:

1.	Der Live-Encoder überträgt Livefeeds in Kanäle, die über das Microsoft Azure Media Services-SDK erstellt und bereitgestellt werden.
2.	Die Kanäle, die Programme und der Streamingendpunkt in Microsoft Azure Media Services verarbeiten alle Live-Streaming-Funktionen, einschließlich Erfassung, Formatierung, Cloud-DVR, Sicherheit, Skalierbarkeit und Redundanz.
3.	Optional können Kunden auswählen, dass eine CDN-Ebene zwischen dem Streamingendpunkt und den Clientendpunkten bereitgestellt wird.
4.	Clientendpunkte streamen vom Streamingendpunkt über HTTP Adaptive Streaming-Protokolle (z. B. Smooth Streaming, DASH, HDS oder HLS).

![image1][image1]


##Bitstromformat – ISO 14496-12 Fragmented MP4

Das in diesem Dokument beschriebene Übertragungsformat zur Erfassung des Live-Streamings basiert auf [ISO-14496-12]. Eine ausführliche Erläuterung des Fragmented MP4-Formats und der Erweiterungen für Video-on-Demand-Dateien und die Erfassung des Live-Streamings finden Sie unter [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) (in englischer Sprache).

Es folgt eine Liste der speziellen Formatdefinitionen, die für die Echtzeiterfassung in Microsoft Azure Media Services gelten:

1. Die Felder "ftyp", "LiveServerManifestBox" und "moov" MÜSSEN mit jeder Anforderung (HTTP POST) gesendet werden. Sie MÜSSEN am Anfang des Streams und immer dann gesendet werden, wenn der Encoder erneut eine Verbindung herstellen muss, um die Stream-Erfassung fortzusetzen. Weitere Informationen finden Sie in Abschnitt 6 in [1].
2. Im Abschnitt 3.3.2 in [1] wird ein optionales Feld mit dem Namen "StreamManifestBox" für die Echtzeiterfassung definiert. Aufgrund der Routinglogik des Microsoft Azure-Lastenausgleichsmoduls ist die Verwendung dieses Felds veraltet. Es SOLLTE bei der Erfassung in Microsoft Azure Media Services NICHT vorhanden sein. Wenn dieses Feld vorhanden ist, wird es in Azure Media Services ohne Meldung ignoriert.
3. Das in Abschnitt 3.2.3.2 in [1] definierte Feld "TrackFragmentExtendedHeaderBox" MUSS für jedes Fragment vorhanden sein.
4. Version 2 von TrackFragmentExtendedHeaderBox SOLLTE verwendet werden, um Mediensegmente mit identischen URLs in mehreren Rechenzentren zu generieren. Das Fragmentindexfeld ist ERFORDERLICH für das rechenzentrenübergreifende Failover indexbasierter Streaming-Formate wie z. B. Apple HTTP Live Streaming (HLS) und indexbasiertes MPEG-DASH. Zum Aktivieren des rechenzentrenübergreifenden Failovers MUSS der Fragmentindex zwischen mehreren Encodern synchronisiert und für jedes folgende Medienfragment jeweils um 1 erhöht werden, auch bei Neustarts und Fehlern der Encoder.
5. Im Abschnitt 3.3.6 in [1] wird das Feld mit der Bezeichnung "MovieFragmentRandomAccessBox" ("mfra") definiert, das am Ende der Echtzeiterfassung gesendet werden KANN, um das Ende des Streams (EOS, End-of-Stream) für den Kanal anzugeben. Aufgrund der Erfassungslogik von Azure Media Services ist die Verwendung von EOS (End-of-Stream) veraltet, und das Feld "mfra" für die Echtzeiterfassung SOLLTE NICHT gesendet werden. Wenn es dennoch gesendet wird, wird es in Azure Media Services ohne Meldung ignoriert. Es wird empfohlen, [Channel Reset](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) zu verwenden, um den Status des Erfassungspunkts zurückzusetzen. Es wird auch empfohlen, [Program Stop](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) zum Beenden einer Präsentation und eines Streams zu verwenden.
6. Die Dauer des MP4-Fragments SOLLTE konstant sein, um die Größe der Clientmanifeste zu reduzieren und die Clientdownloadheuristik durch Verwendung von Wiederholungstags zu verbessern. Die Dauer KANN schwanken, um nicht ganzzahlige Frameraten auszugleichen.
7. Die Dauer des MP4-Fragments SOLLTE zwischen ca. 2 und 6 Sekunden liegen.
8. Zeitstempel und Indizes des MP4-Fragments (fragment_absolute_time und fragment_index für TrackFragmentExtendedHeaderBox) SOLLTEN in aufsteigender Reihenfolge eingehen. Obwohl Fragmente in Azure Media Services dupliziert werden können, sind die Möglichkeiten, Fragmente entsprechend der Medienzeitachse neu anzuordnen, nur sehr begrenzt.

##Protokollformat – HTTP

Bei der Fragmented MP4-basierten Echtzeiterfassung für Microsoft Azure Media Services wird eine lang ausgeführte HTTP POST-Standardanforderung verwendet, um codierte Mediendaten im Fragmented MP4-Format an den Dienst zu übertragen. Jede HTTP POST-Anforderung sendet einen vollständigen Fragmented MP4-Bitstrom ("Stream"), der mit Headerfeldern ("ftyp", "Live Server Manifest Box" und "moov") beginnt und mit einer Sequenz von Fragmenten (Felder "moof" und "mdat") fortgesetzt wird. Weitere Informationen zur URL-Syntax für HTTP POST-Anforderungen finden Sie im Abschnitt 9.2 in [1]. Beispiel für die POST-URL:

	http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

Es folgen die detaillierten Anforderungen:

1. Der Encoder SOLLTE die Übertragung starten, indem eine HTTP POST-Anforderung mit leerem "Hauptteil" (Inhaltslänge = 0) mit der gleichen Erfassungs-URL gesendet wird. Damit kann schnell erkannt werden, ob der Echtzeiterfassungsendpunkt gültig ist und ob Authentifizierung oder andere Bedingungen erforderlich sind. Über das HTTP-Protokoll kann der Server erst eine HTTP-Antwort zurücksenden, wenn die gesamte Anforderung, einschließlich des POST-Texts, empfangen wurde. Aufgrund der Langfristigkeit von Liveereignissen kann der Encoder ohne diesen Schritt Fehler möglicherweise erst erkennen, nachdem alle Daten gesendet wurden.
2. Der Encoder MUSS alle als Ergebnis von (1) ausgegebenen Fehler oder Authentifizierungsanforderungen behandeln. Wenn (1) mit dem Antwortcode "200" erfolgreich ausgeführt wird, wird der Vorgang fortgesetzt.
3. Der Encoder MUSS eine neue HTTP POST-Anforderung mit dem Fragmented MP4-Stream beginnen. Die Nutzlast MUSS mit den Headerfeldern, gefolgt von Fragmenten, beginnen. Beachten Sie, dass die Felder "ftyp", "Live Server Manifest Box" und "moov" (in dieser Reihenfolge) mit jeder Anforderung gesendet werden MÜSSEN, selbst wenn der Encoder erneut eine Verbindung herstellen muss, weil die vorhergehende Anforderung vor dem Ende des Streams beendet wurde. 
4. Der Encoder MUSS für das Hochladen die segmentierte Transfercodierung verwenden, da es nicht möglich ist, die gesamte Inhaltslänge des Liveereignisses vorherzusagen.
5. Nach dem Ende des Ereignisses und dem Senden des letzten Fragments MUSS der Encoder die Nachrichtensequenz für die segmentierte Transfercodierung beenden (bei den meisten HTTP-Clientstapeln erfolgt dies automatisch). Der Encoder MUSS warten, bis der Dienst den letzten Antwortcode zurückgibt, und dann die Verbindung beenden. 
6. Der Encoder DARF das in Abschnitt 9.2 in [1] beschriebene Events()-Nomen NICHT für die Echtzeiterfassung in Microsoft Azure Media Services verwenden.
7. Wenn die HTTP POST-Anforderung vor dem Ende des Streams mit einem TCP-Fehler beendet wird oder das Zeitlimit überschreitet, MUSS der Encoder unter Verwendung einer neuen Verbindung eine neue Anforderung ausgeben und dabei die oben genannten Anforderungen erfüllen. Zudem gilt die zusätzliche Anforderung, dass der Encoder die vorherigen zwei MP4-Fragmente für jede Spur im Stream erneut senden und den Stream ohne Fehlstellen in der Medienzeitachse fortsetzen MUSS. Durch das erneute Senden der letzten beiden MP4-Fragmente für jede Spur wird sichergestellt, dass keine Daten verloren gehen. Mit anderen Worten: Wenn ein Stream eine Audio- und eine Videospur enthält und bei der aktuellen POST-Anforderung ein Fehler auftritt, muss der Encoder eine neue Verbindung herstellen und die letzten zwei zuvor erfolgreich gesendeten Fragmente für die Audiospur sowie die letzten zwei zuvor erfolgreich gesendeten Fragmente für die Videospur erneut senden, um sicherzustellen, dass keine Daten verloren gehen. Der Encoder MUSS einen vorausschauenden Puffer der Medienfragmente beibehalten, den er beim Wiederherstellen der Verbindung erneut sendet.

##Zeitskala 

Unter [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) wird die Verwendung der Zeitskala für SmoothStreamingMedia (Abschnitt 2.2.2.1), StreamElement (Abschnitt 2.2.2.3), StreamFragmentElement (Abschnitt 2.2.2.6) und LiveSMIL (Abschnitt 2.2.7.3.1) beschrieben. Wenn der Zeitskala-Wert nicht vorhanden ist, wird der Standardwert "10.000.000" (10 MHz) verwendet. Obwohl die Spezifikation des Smooth Streaming-Formats die Verwendung anderer Zeitskala-Werte nicht blockiert, verwenden die meisten Encoder-Implementierungen diesen Standardwert (10 MHz), um Smooth Streaming-Erfassungsdaten zu generieren. Wegen der Funktion [Azure Media Services Dynamic Packaging](media-services-dynamic-packaging-overview.md) wird empfohlen, 90 kHz als Zeitskala für Videostreams sowie 44,1 kHz oder 48,1 kHz für Audiostreams zu verwenden. Wenn für verschiedene Streams unterschiedliche Zeitskala-Werte verwendet werden, MUSS die Zeitskala der Streamebene gesendet werden. Weitere Informationen finden Sie unter [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

##Definition von "Stream"  

Ein "Stream" ist die Basiseinheit für Vorgänge in der Echtzeiterfassung beim Erstellen von Livepräsentationen sowie beim Verarbeiten von Streaming-Failovers und Redundanzszenarios. Ein "Stream" ist definiert als ein eindeutiger Fragmented MP4-Bitstrom, der eine oder mehrere Spuren enthalten kann. Eine vollständige Livepräsentation kann je nach Konfiguration des/der Live-Encoder einen oder mehrere Streams enthalten. In den folgenden Beispielen werden verschiedene Optionen zum Erstellen einer vollständigen Livepräsentation unter Verwendung eines oder mehrerer Streams veranschaulicht.

**Beispiel:**

Der Kunde möchte eine Live-Streaming-Präsentation erstellen, die die folgenden Audio- und Videobitraten enthält:

Video – 3.000 KBit/s, 1.500 KBit/s, 750 KBit/s

Audio – 128 KBit/s

###Option 1: alle Spuren in einem Stream

Bei dieser Option generiert ein einzelner Encoder alle Audio- und Videospuren und bündelt sie in einem Fragmented MP4-Bitstrom, der dann über eine einzelne HTTP POST-Verbindung gesendet wird. In diesem Beispiel gibt es nur einen Stream für die Livepräsentation:

![Bild2][image2]

###Option 2: jede Spur in einem separaten Stream

Bei dieser Option fügt der Encoder jeweils nur eine Spur in jeden Fragment MP4-Bitstrom ein und sendet alle Streams über mehrere separate HTTP-Verbindungen. Dies kann mit einem Encoder oder mehreren Encodern erfolgen. Aus der Perspektive der Echtzeiterfassung besteht diese Livepräsentation aus vier Streams.

![Bild3][image3]

###Option 3: Bündeln der Audiospur mit der Videospur mit der niedrigsten Bitrate in einem Stream

Bei dieser Option wird die Audiospur mit der Videospur mit der niedrigsten Bitrate in einem Fragment MP4-Bitstrom gebündelt, die anderen zwei Videospuren werden in jeweils separaten Streams gesendet.


![Bild4][image4]


###Zusammenfassung

Die obigen Beispiele sind keine vollständige Liste aller möglichen Erfassungsoptionen für dieses Beispiel. Tatsächlich werden bei der Echtzeiterfassung alle möglichen Gruppierungen der Spuren in Streams unterstützt. Basierend auf der technischen Komplexität, der Kapazität des Encoders sowie auf Überlegungen zu Redundanz und Failover können Kunden und Anbieter von Encodern ihre eigenen Implementierungen auswählen. Es ist jedoch zu beachten, dass es in den meisten Fällen nur eine Audiospur für die gesamte Livepräsentation gibt. Daher ist es wichtig, die Stabilität des Streams zu gewährleisten, der die Audiospur enthält. Diese Überlegung führt häufig dazu, dass die Audiospur einen eigenen Stream erhält (wie in Option 2) oder dass sie mit der Videospur mit der niedrigsten Bitrate gebündelt wird (wie in Option 3). Auch im Hinblick auf eine bessere Redundanz und Fehlertoleranz wird das Senden der gleichen Audiospur in zwei verschiedenen Streams (Option 2 mit redundanten Audiospuren) oder die Bündelung der Audiospur in mindestens zwei Videospuren mit der niedrigsten Bitrate (Option 3 mit gebündeltem Audiostream in mindestens zwei Videostreams) für die Echtzeiterfassung in Microsoft Azure Media Services dringend empfohlen.

##Dienstfailover 

Angesichts der Eigenschaften des Live-Streamings ist eine gute Failoverunterstützung von großer Bedeutung für die gewährleistete Verfügbarkeit des Diensts. Microsoft Azure Media Services ist so konzipiert, dass verschiedene Arten von Fehlern, darunter Netzwerkfehler, Serverfehler oder Speicherprobleme, behoben werden können. Bei Verwendung in Verbindung mit der geeigneten Failoverlogik des Live-Encoders erhalten Kunden einen hoch zuverlässigen Live-Streaming-Dienst in der Cloud.

In diesem Abschnitt werden Szenarios für Dienstfailover erörtert. In diesem Fall erfolgt der Fehler innerhalb des Diensts und tritt als Netzwerkfehler zutage. Es folgen einige Empfehlungen für die Encoder-Implementierung zur Behebung des Dienstfailovers:


1. Verwenden Sie für die Herstellung der TCP-Verbindung ein Timeout von 10 Sekunden. Wenn ein Versuch zum Herstellen der Verbindung länger als 10 Sekunden dauert, wird der Vorgang abgebrochen und wiederholt. 
2. Verwenden Sie ein kurzes Timeout für das Senden der HTTP-Anforderungsnachrichtenabschnitte. Wenn die Dauer des MP4-Zielfragments N Sekunden ist, verwenden Sie für das Senden ein Timeout zwischen N und 2N Sekunden. Verwenden Sie z. B. ein Timeout von 6 bis 12 Sekunden, wenn die Dauer des MP4-Fragments 6 Sekunden beträgt. Wenn eine Zeitüberschreitung auftritt, setzen Sie die Verbindung zurück, öffnen Sie eine neue Verbindung, und setzen Sie die Erfassung des Streams über die neue Verbindung fort. 
3. Definieren Sie für jede Spur einen kontinuierlichen Puffer, der jeweils die letzten zwei Fragmente enthält, die erfolgreich und vollständig an den Dienst gesendet wurden. Wenn die HTTP POST-Anforderung für einen Stream vor dem Ende des Streams beendet wird oder das Zeitlimit überschreitet, öffnen Sie eine neue Verbindung, und starten Sie eine neue HTTP POST-Anforderung. Senden Sie die Header des Streams sowie die letzten zwei Fragmente für jede Spur erneut, und setzen Sie den Stream ohne Fehlstellen in der Medienzeitachse fort. Dadurch verringert sich die Wahrscheinlichkeit eines Datenverlusts.
4. Es wird empfohlen, dass der Encoder die Anzahl der Wiederholungsversuche zum Herstellen einer Verbindung oder zum Fortsetzen des Streamings nach dem Auftreten eines TCP-Fehlers NICHT begrenzt.
5. Nach einem TCP-Fehler gilt Folgendes:
	1. Die aktuelle Verbindung MUSS getrennt werden, und eine neue Verbindung für eine neue HTTP POST-Anforderung MUSS hergestellt werden.
	2. Die neue HTTP POST-URL MUSS mit der ursprünglichen POST-URL identisch sein.
	3. Die neue HTTP POST-Anforderung MUSS Streamheader (Felder "ftyp", "Live Server Manifest Box" und "moov") enthalten, die identisch mit den Streamheadern in der ursprünglichen POST-Anforderung sind.
	4. Die letzten zwei Fragmente für jede Spur MÜSSEN erneut gesendet werden, und das Streaming muss ohne Fehlstellen in der Medienzeitachse fortgesetzt werden. Die Zeitstempel der MP4-Fragmente müssen kontinuierlich erhöht werden, auch bei mehreren HTTP POST-Anforderungen.
6. Der Encoder SOLLTE die HTTP POST-Anforderung beenden, wenn Daten nicht mit einer Rate gesendet werden, die der Dauer des MP4-Fragments entspricht. Eine HTTP POST-Anforderung, die keine Daten sendet, kann verhindern, dass Azure Media Services im Fall einer Aktualisierung des Diensts die Verbindung mit dem Encoder schnell trennt. Aus diesem Grund SOLLTEN die HTTP POST-Anforderungen für platzsparende Spuren (Signal für Werbung) kurzlebig sein und beendet werden, sobald das Fragment gesendet wurde.

##Encoder-Failover

Das Encoder-Failover ist das zweite Failoverszenario, das für die Übermittlung des End-to-End-Live-Streamings betrachtet werden muss. In diesem Szenario ist der Fehler beim Encoder aufgetreten.

![Bild5][image5]


Im Folgenden sind die Erwartungen im Hinblick auf den Echtzeiterfassungsendpunkt bei einem Encoder-Failover aufgeführt:

1. Eine neue Encoder-Instanz SOLLTE erstellt werden, um das Streaming fortzusetzen, wie in der Abbildung oben dargestellt (Stream für Video mit einer Größe von 3.000 K mit einer gestrichelten Linie dargestellt).
2. Der neue Encoder MUSS die gleiche URL für HTTP POST-Anforderungen verwenden wie die Instanz, bei der der Fehler aufgetreten ist.
3. Die POST-Anforderung des neuen Encoders MUSS die gleichen Fragmented MP4-Headerfelder enthalten wie die Instanz, bei der der Fehler aufgetreten ist.
4. Der neue Encoder MUSS ordnungsgemäß mit allen anderen für die gleiche Livepräsentation ausgeführten Encodern synchronisiert werden, sodass synchronisierte Audio- und Videobeispiele mit abgeglichenen Fragmentgrenzen generiert werden.
5. Der neue Stream MUSS dem vorherigen Stream semantisch entsprechen und auf Header- und Fragmentebene austauschbar sein.
6. Der neue Encoder SOLLTE versuchen, den Datenverlust zu minimieren. Die Werte für "fragment_absolute_time" und "fragment_index" von Medienfragmenten SOLLTEN sich ab dem Punkt erhöhen, an dem der Encoder zuletzt beendet wurde. Die Werte für "fragment_absolute_time" und "fragment_index" SOLLTEN sich kontinuierlich erhöhen, können jedoch bei Bedarf eine Fehlstelle aufweisen. Azure Media Services ignoriert Fragmente, die bereits empfangen und verarbeitet wurden. Daher ist es besser, im Zweifelsfall Fragmente erneut zu senden, als Fehlstellen in die Medienzeitachse einzuführen. 

##Encoder-Redundanz 

Für bestimmte kritische Liveereignisse, die eine noch höhere Verfügbarkeit und Quality of Experience erfordern, wird der Einsatz redundanter Aktiv-Aktiv-Encoder empfohlen, um ein nahtloses Failover ohne Datenverlust zu erreichen.

![Bild6][image6]

Wie in der Abbildung oben dargestellt, gibt es zwei Gruppen von Encodern, die zwei Kopien jedes Streams gleichzeitig in den Live-Streaming-Dienst übertragen. Dieses Setup wird unterstützt, da Microsoft Azure Media Services doppelte Fragmente basierend auf der Stream-ID und dem Zeitstempel des Fragments herausfiltern kann. Im resultierenden Livestream und Archiv sind die bestmöglichen Streams aus den beiden Quellen in einer einzelnen Kopie aller Streams zusammengefasst. In einem hypothetischen Extremfall ist der resultierende Livestream aus dem Dienst kontinuierlich ohne Datenverlust, solange zu einem bestimmten Zeitpunkt jeweils ein Encoder für jeden Stream ausgeführt wird (dabei muss es sich nicht um denselben Encoder handeln).

Die Anforderungen für dieses Szenario sind nahezu identisch mit den Anforderungen für das Szenario des Encoder-Failovers, mit der Ausnahme, dass die zweite Gruppe von Encodern zur gleichen Zeit wie die primären Encoder ausgeführt wird.

##Dienstredundanz  

Für eine hoch redundante globale Verteilung ist es manchmal erforderlich, eine regionsübergreifende Sicherung durchzuführen, um regionalen Ausfällen entgegenzuwirken. Durch Erweiterung der Topologie unter "Encoder-Redundanz" können Kunden eine redundante Dienstbereitstellung in einer anderen Region festlegen, die mit der zweiten Gruppe von Encodern verbunden ist. Kunden können zudem mit einem CDN-Anbieter zusammenarbeiten, um einen Global Traffic Manager (GTM) vor den beiden Dienstbereitstellungen bereitzustellen und so den Clientdatenverkehr nahtlos weiterzuleiten. Die Anforderungen für die Encoder sind identisch mit den Anforderungen für den Fall unter "Encoder-Redundanz", mit der einzigen Ausnahme, dass die zweite Gruppe von Encodern auf einen anderen Echtzeiterfassungsendpunkt verweisen muss. Dieses Setup ist in der folgenden Abbildung dargestellt:

![Bild7][image7]

##Spezielle Typen von Erfassungsformaten 

In diesem Abschnitt werden einige spezielle Formattypen für die Echtzeiterfassung erörtert, die für einige spezifische Szenarios entwickelt wurden.

###Platzsparende Spur

Bei der Übermittlung einer Live-Streaming-Präsentation mit einer reichhaltigen Clientumgebung ist es häufig erforderlich, zeitsynchronisierte Ereignisse oder In-Band-Signale mit den wichtigsten Mediendaten zu übertragen. Ein Beispiel hierfür ist das dynamische Einfügen von Live-Werbung. Diese Art der Ereignissignalisierung unterscheidet sich aufgrund ihres selteneren Vorkommens vom regulären Audio- und Videostreaming. Mit anderen Worten, die Signalisierungsdaten werden normalerweise nicht kontinuierlich gesendet, und das jeweilige Intervall kann nur schwer vorhergesagt werden. Das Konzept von platzsparenden Spuren wurde speziell entwickelt, um In-Band-Signalisierungsdaten zu erfassen und zu übertragen.

Es folgt eine empfohlene Implementierung für die Erfassung von platzsparenden Spuren:

1. Erstellen Sie einen separaten Fragmented MP4-Bitstrom, der nur platzsparende Spuren ohne Audio- und Videospuren enthält.
2. Verwenden Sie im Feld "Live Server Manifest Box" entsprechend der Definition im Abschnitt 6 in [1] den Parameter "parentTrackName", um den Namen der übergeordneten Spur anzugeben. Weitere Informationen finden Sie im Abschnitt 4.2.1.2.1.2 in [1].
3. Im Feld "Live Server Manifest Box" MUSS "manifestOutput" auf "true" gesetzt werden.
4. Aufgrund des seltenen Eintretens des Signalisierungsereignisses wird Folgendes empfohlen:
	1. Zu Beginn des Liveereignisses sendet der Encoder die ursprünglichen Headerfelder an den Dienst, sodass der Dienst die platzsparende Spur im Clientmanifest registrieren kann.
	2. Der Encoder SOLLTE die HTTP POST-Anforderung beenden, wenn keine Daten gesendet werden. Eine lang ausgeführte HTTP POST-Anforderung, die keine Daten sendet, kann verhindern, dass Azure Media Services im Fall einer Aktualisierung des Diensts oder eines Serverneustarts die Verbindung mit dem Encoder schnell trennt, da der Medienserver vorübergehend in einem Empfangsvorgang im Socket blockiert ist. 
	3. In dem Zeitraum, in dem keine Signalisierungsdaten vorhanden sind, SOLLTE der Encoder die HTTP POST-Anforderung schließen. Während die POST-Anforderung aktiv ist, SOLLTE der Encoder Daten senden. 
	4. Beim Senden von platzsparenden Fragmenten kann der Encoder den expliziten Header "Content-Length" festlegen, sofern dieser vorhanden ist.
	5. Beim Senden von platzsparenden Fragmenten über eine neue Verbindung SOLLTE der Encoder das Senden mit den Headerfeldern starten, gefolgt von den neuen Fragmenten. Dies ist notwendig für den Fall, dass inzwischen ein Failover aufgetreten ist und die neue Verbindung mit einem neuen Server hergestellt wird, auf dem die platzsparende Spur noch nicht bekannt ist.
	6. Das Fragment der platzsparenden Spur wird für den Client zur Verfügung gestellt, wenn das Fragment der entsprechenden übergeordneten Spur mit dem gleichen oder einem größeren Zeitstempelwert für den Client zur Verfügung gestellt wird. Wenn für das platzsparende Fragment beispielsweise der Zeitstempel t = 1.000 festgelegt ist, wird erwartet, dass der Client das platzsparende Fragment mit geringer Dichte mit t = 1.000 herunterladen kann, nachdem der Client das Video-Fragment (angenommen, der Name der übergeordneten Spur lautet "Video") mit dem Zeitstempel 1.000 oder höher erkannt hat. Beachten Sie, dass das tatsächliche Signal sehr wohl auch für eine andere Position in der Zeitachse der Präsentation für seinen eigentlichen Zweck verwendet werden kann. Im Beispiel oben ist es möglich, dass das platzsparende Fragment mit t = 1.000 eine XML-Nutzlast aufweist, die für das Einfügen einer Werbung in einer Position definiert ist, die zeitlich einige Sekunden später folgt.
	7. Die Nutzlast des platzsparenden Fragments kann je nach Szenario in unterschiedlichen Formaten vorliegen (z. B. XML, Text oder binär). 


###Redundante Audiospur

In einem typischen HTTP Adaptive Streaming-Szenario (z. B. Smooth Streaming oder DASH) befindet sich häufig nur eine Audiospur in der gesamten Präsentation. Im Gegensatz zu Videospuren, die über mehrere Qualitätsstufen verfügen, aus denen sich der Client bei Fehlerzuständen eine Stufe auswählen kann, kann die Audiospur eine einzelne Fehlerquelle sein, wenn die Erfassung des Streams, der die Audiospur enthält, fehlerhaft ist.

Um dieses Problem zu beheben, unterstützt Microsoft Azure Media Services die Echtzeiterfassung redundanter Audiospuren. Dahinter steht die Idee, dass die gleiche Audiospur mehrmals in anderen Streams gesendet werden kann. Der Dienst registriert die Audiospur nur einmal im Clientmanifest, kann jedoch redundante Audiospuren als Sicherungskopien zum Abrufen von Audiofragmenten verwenden, wenn bei der primären Audiospur Probleme auftreten. Zur Erfassung redundanter Audiospuren gelten folgende Bedingungen für den Encoder:

1. Der Encoder muss die gleiche Audiospur in mehreren Fragmented MP4-Bitströmen erstellen. Die redundanten Audiospuren MÜSSEN semantisch den genau gleichen Fragmentzeitstempeln entsprechen und auf Header- und Fragmentebene austauschbar sein.
2. Der Encoder muss sicherstellen, dass der Eintrag "audio" in "Live Server Manifest" (siehe Abschnitt 6 in [1]) für alle redundanten Audiospuren identisch ist.

Es folgt eine empfohlene Implementierung für redundante Audiospuren:

1. Senden Sie jede eindeutige Audiospur allein in einem separaten Stream. Senden Sie auch einen redundanten Stream für jeden dieser Audiospurstreams, wobei sich der zweite Stream vom ersten Stream nur durch den Bezeichner in der HTTP POST-URL unterscheidet: {protocol}://{server address}/{publishing point path}/Streams({identifier}).
2. Verwenden Sie separate Streams, um die Videos mit den beiden niedrigsten Bitraten zu senden. Jeder dieser Streams SOLLTE auch eine Kopie jeder eindeutigen Audiospur enthalten. Wenn beispielsweise mehrere Sprachen unterstützt werden, SOLLTEN diese Streams Audiospuren für jede Sprache enthalten.
3. Verwenden Sie separate Serverinstanzen (Encoder) zum Codieren und Senden der in (1) und (2) genannten redundanten Streams. 


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

<!---HONumber=58--> 