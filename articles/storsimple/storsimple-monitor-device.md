<properties 
   pageTitle="Überwachen des StorSimple-Geräts | Microsoft Azure"
	description="Beschreibt, wie Sie den StorSimple Manager-Dienst verwenden, um E/A-Leistung, Kapazitätsauslastung, Netzwerkdurchsatz und Geräteleistung zu überwachen."
	services="storsimple"
	documentationCenter="NA"
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="09/02/2015"
	ms.author="alkohli"/>

# Verwenden des StorSimple Manager-Diensts zum Überwachen Ihres StorSimple-Geräts 

## Übersicht

Sie können den StorSimple Manager-Dienst verwenden, um bestimmte Geräte innerhalb Ihrer StorSimple-Lösung zu überwachen. Sie können benutzerdefinierte Diagramme basierend auf Metriken zu EA-Leistung, Kapazitätsauslastung, Netzwerkdurchsatz und Geräteleistung erstellen.

Um die Überwachungsinformationen für ein bestimmtes Gerät anzuzeigen, wählen Sie im Azure-Portal den StorSimple Manager-Dienst aus, klicken Sie auf die Registerkarte **Überwachen**, und wählen Sie das gewünschte Gerät aus der Geräteliste aus. Die Seite **Überwachen** enthält die folgenden Informationen.

## E/A-Leistung 

**E/A-Leistung** verfolgt Metrikdaten nach, die in Zusammenhang mit Lese- und Schreibvorgängen zwischen den iSCSI-Initiatorschnittstellen auf dem Hostserver und dem Gerät oder dem Gerät und der Cloud stehen. Die Leistung kann für ein bestimmtes Volume, einen bestimmten Volumecontainer oder alle Volumecontainer gemessen werden.

Im Diagramm unten sind die EA-Vorgänge für den Initiator Ihres Geräts für alle Volumes für ein Gerät in der Produktionsumgebung aufgeführt. Bei den dargestellten Metriken handelt es sich um gelesene und geschriebene Bytes pro Sekunde, Lese- und Schreib-EA-Vorgänge pro Sekunde und um die Wartezeiten bei Lese- und Schreibzugriffen.

![EA-Leistung vom Initiator an das Gerät](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Für das gleiche Gerät sind die EA-Vorgänge für Daten vom Gerät zur Cloud für alle Volumecontainer dargestellt. Auf diesem Gerät befinden sich die Daten nur in der linearen Schicht, und nichts wurde in die Cloud ausgelagert. Es treten keine Lese-/Schreibvorgänge vom Gerät in die Cloud auf. Daher treten die Spitzen im Diagramm im Abstand von 5 Minuten auf, was der Häufigkeit entspricht, mit der das Taktsignal zwischen dem Gerät und dem Dienst überprüft wird.

![EA-Leistung vom Gerät in die Cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Für das gleiche Gerät wurde beginnend um 14:00 Uhr eine Cloudmomentaufnahme für Volumedaten erstellt. Dies hat bewirkt, dass Daten vom Gerät in die Cloud übertragen wurden. Innerhalb dieses Zeitraums wurden Lese-/Schreibvorgänge für die Cloud bedient. Das EA-Diagramm zeigt in dem Zeitraum, in dem die Momentaufnahme erstellt wurde, eine Spitze in den verschiedenen Metrikdaten.

![EA-Leistung vom Gerät in die Cloud nach einer Cloudmomentaufnahme](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## Kapazitätsauslastung 

**Kapazitätsauslastung** verfolgt Metrikdaten im Zusammenhang mit der Menge an Datenspeicherplatz nach, die von den Volumes, den Volumecontainern oder dem Gerät verwendet wird. Sie können Berichte zur Kapazitätsauslastung des primären Speichers, des Cloudspeichers oder des Gerätespeichers erstellen. Die Kapazitätsauslastung kann für ein bestimmtes Volume, einen bestimmten Volumecontainer oder alle Volumecontainer gemessen werden.

Die primäre, die Cloud- und die Gerätespeicherkapazität kann wie folgt beschrieben werden:

- **Kapazitätsauslastung Primärspeicher** zeigt die Menge an Daten, die in StorSimple-Volumes geschrieben werden, bevor diese Daten dedupliziert und komprimiert werden. Die folgenden Diagramme stellen die Kapazitätsauslastung des Primärspeichers eines StorSimple-Geräts vor und nach dem Erstellen einer Cloudmomentaufnahme dar. Unter der Voraussetzung, dass es sich nur um Volumedaten handelt, sollte eine Cloudmomentaufnahme keine Auswirkungen auf den Primärspeicher haben. Wie Sie sehen, zeigt das Diagramm als Ergebnis der Erstellung der Cloudmomentaufnahme keinen Unterschied in der Kapazitätsauslastung des Primärspeichers. Beachten Sie, dass die Erstellung der Cloudmomentaufnahme auf dem betreffenden Gerät um 14:00 Uhr gestartet wurde.

	![Kapazitätsauslastung des Primärspeichers vor einer Cloudmomentaufnahme](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)
	
	![Kapazitätsauslastung des Primärspeichers nach einer Cloudmomentaufnahme](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)


- **Kapazitätsauslastung Cloudspeicher** zeigt die Menge an genutztem Speicherplatz in der Cloud. Diese Daten sind dedupliziert und komprimiert. Diese Datenmenge umfasst Cloudmomentaufnahmen, die möglicherweise Daten enthalten, die in keinem Primärvolume reflektiert und aus Gründen der Legacy oder im Rahmen einer vorgeschriebenen Aufbewahrung gespeichert werden. Sie können die Auslastungszahlen des Primär- und Cloudspeichers vergleichen, um einen Eindruck der Datenreduzierungsrate zu erhalten, wenngleich das Ergebnis des Vergleichs nicht ganz exakt ist. Die folgenden Diagramme stellen die Kapazitätsauslastung des Cloudspeichers eines StorSimple-Geräts vor und nach dem Erstellen einer Cloudmomentaufnahme dar. Die Cloudmomentaufnahme wurde auf dem betreffenden Gerät ungefähr um 14:00 Uhr gestartet, und Sie können sehen, dass die Kapazitätsauslastung des Cloudspeichers um diese Zeit in die Höhe schnellte und von 5,73 MB auf 4,04 GB anstieg.

	![Kapazitätsauslastung des Cloudspeichers vor einer Cloudmomentaufnahme](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

	![Kapazitätsauslastung des Cloudspeichers nach einer Cloudmomentaufnahme](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


- **Kapazitätsauslastung Gerätespeicher** zeigt die Gesamtauslastung des Geräts an. Diese Zahl ist höher als die Auslastung des Primärspeichers, da sie auch die lineare SSD-Schicht umfasst. Diese Schicht enthält eine Menge an Daten, die auch auf den anderen Schichten des Geräts vorhanden ist. Die Kapazität der linearen SSD-Schicht ist zyklisch angelegt, d. h. wenn neue Daten eintreffen, werden die alten Daten in die HDD-Schicht (und zu diesem Zeitpunkt dedupliziert und komprimiert) und anschließend in die Cloud verschoben.

	Im Lauf der Zeit wird sich die Kapazitätsauslastung des Primärspeichers und des Geräts wahrscheinlich parallel erhöhen, bis die ersten Daten in die Cloud verschoben werden. Zu diesem Zeitpunkt wird die Kapazitätsauslastung des Geräts vermutlich auf dem gleichen Niveau bleiben, die Kapazitätsauslastung des Primärspeichers wird sich jedoch erhöhen, da mehr Daten geschrieben werden.

	Die folgenden Diagramme stellen die Kapazitätsauslastung des Primärspeichers eines StorSimple-Geräts vor und nach dem Erstellen einer Cloudmomentaufnahme dar. Die Cloudmomentaufnahme begann um 14:00 Uhr, und die Kapazitätsauslastung des Gerätespeichers begann um diese Zeit, abzunehmen. Die Kapazitätsauslastung des Gerätespeichers fiel von 11,58 GB auf 7,48 GB. Dies weist darauf hin, dass die unkomprimierten Daten in der linearen SSD-Schicht höchst wahrscheinlich dedupliziert, komprimiert und in die HDD-Schicht verschoben wurden. Beachten Sie, dass dieses Abfallen möglicherweise nicht zu beobachten ist, wenn das Gerät sowohl in der SSD- als auch in der HDD-Schicht bereits eine große Menge Daten aufweist. In diesem Beispiel hat das Gerät eine kleine Datenmenge.

	![Kapazitätsauslastung des Gerätespeichers vor einer Cloudmomentaufnahme](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

	![Kapazitätsauslastung des Gerätespeichers nach einer Cloudmomentaufnahme](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## Netzwerkdurchsatz

**Netzwerkdurchsatz** verfolgt Metrikdaten im Zusammenhang mit der Menge an Daten nach, die von den Netzwerkschnittstellen des iSCSI-Initiators auf dem Hostserver und dem Gerät sowie zwischen Gerät und Cloud übertragen werden. Sie können diese Metrikdaten für jede iSCSI-Netzwerkschnittstelle Ihres Geräts überwachen.

Das Diagramm unten stellt den Netzwerkdurchsatz für „Data 0“ und „Data 4“ dar; bei beiden handelt es sich um 1 GbE-Netzwerkschnittstellen auf dem Gerät. In diesem Fall war „Data 0“ cloudfähig, während „Data 4“ iSCSI-fähig war. Sowohl der eingehende als auch der ausgehende Datenverkehr des StorSimple-Geräts ist dargestellt. Beachten Sie, dass die flache Linie im Diagramm, die um 15:24 beginnt, auf die Tatsache zurückzuführen ist, dass Daten nur alle 5 Minuten erfasst werden, und daher ignoriert werden kann.

![Netzwerkdurchsatz für Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Netzwerkdurchsatz für Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## Geräteleistung 

**Geräteleistung** verfolgt Metrikdaten zur Leistung des Geräts nach. Das folgende Diagramm zeigt die Statistik der CPU-Auslastung für ein Gerät in einer Produktionsumgebung.

![CPU-Auslastung des Geräts](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## Nächste Schritte

[Informationen zur Verwendung des StorSimple Manager-Dienstdashboards für Geräte](storsimple-device-dashboard.md).

<!---HONumber=September15_HO1-->