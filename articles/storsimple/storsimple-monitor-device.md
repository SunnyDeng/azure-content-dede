<properties 
   pageTitle="Verwenden des StorSimple Manager-Diensts zum Überwachen Ihres StorSimple-Geräts | Microsoft Azure"
   description="Beschreibt, wie Sie den StorSimple Manager-Dienst verwenden, um E/A-Leistung, Kapazitätsauslastung, Netzwerkdurchsatz und Geräteleistung zu überwachen."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/10/2015"
   ms.author="alkohli" />

# Verwenden des StorSimple Manager-Diensts zum Überwachen Ihres StorSimple-Geräts 

## Übersicht

Sie können den StorSimple Manager-Dienst verwenden, um bestimmte Geräte innerhalb Ihrer StorSimple-Lösung zu überwachen. Sie können benutzerdefinierte Diagramme basierend auf Metriken zu EA-Leistung, Kapazitätsauslastung, Netzwerkdurchsatz und Geräteleistung erstellen.

Um die Überwachungsinformationen für ein bestimmtes Gerät anzuzeigen, wählen Sie im Verwaltungsportal den StorSimple Manager-Dienst aus, klicken Sie auf die Registerkarte **Überwachen**, und wählen Sie das gewünschte Gerät aus der Geräteliste aus. Die Seite **Überwachen** enthält die folgenden Informationen.

## E/A-Leistung 

**E/A-Leistung** verfolgt Metrikdaten nach, die in Zusammenhang mit Lese- und Schreibvorgängen zwischen den iSCSI-Initiatorschnittstellen auf dem Hostserver und dem Gerät oder dem Gerät und der Cloud stehen. Die Leistung kann für ein bestimmtes Volume, einen bestimmten Volumecontainer oder alle Volumecontainer gemessen werden.

## Kapazitätsauslastung 

**Kapazitätsauslastung** verfolgt Metrikdaten im Zusammenhang mit der Menge an Datenspeicherplatz nach, die von den Volumes, den Volumecontainern oder dem Gerät verwendet wird. Sie können Berichte zur Kapazitätsauslastung des primären Speichers, des Cloudspeichers oder des Gerätespeichers erstellen. Die Kapazitätsauslastung kann für ein bestimmtes Volume, einen bestimmten Volumecontainer oder alle Volumecontainer gemessen werden.

- **Kapazitätsauslastung Primärspeicher** zeigt die Menge an Daten, die in StorSimple-Volumes geschrieben werden, bevor diese Daten dedupliziert und komprimiert werden.

- **Kapazitätsauslastung Cloudspeicher** zeigt die Menge an genutztem Speicherplatz in der Cloud. Diese Daten sind dedupliziert und komprimiert. Diese Datenmenge umfasst Cloudmomentaufnahmen, die möglicherweise Daten enthalten, die in keinem Primärvolume reflektiert und aus Gründen der Legacy oder im Rahmen einer vorgeschriebenen Aufbewahrung gespeichert werden. Sie können die Auslastungszahlen des Primär- und Cloudspeichers vergleichen, um einen Eindruck der Datenreduzierungsrate zu erhalten, wenngleich das Ergebnis des Vergleichs nicht ganz exakt ist.

- **Kapazitätsauslastung Gerätespeicher** zeigt die Gesamtauslastung des Geräts an. Diese Zahl ist höher als die Auslastung des Primärspeichers, da sie auch die lineare SSD-Schicht umfasst. Diese Schicht enthält eine Menge an Daten, die auch auf den anderen Schichten des Geräts vorhanden ist. Die Kapazität der linearen SSD-Schicht ist zyklisch angelegt, d. h. wenn neue Daten eintreffen, werden die alten Daten in die Cloud verschoben (und zu diesem Zeitpunkt dedupliziert und komprimiert).

Im Lauf der Zeit wird sich die Kapazitätsauslastung des Primärspeichers und des Geräts wahrscheinlich parallel erhöhen, bis die ersten Daten in die Cloud verschoben werden. Zu diesem Zeitpunkt wird die Kapazitätsauslastung des Geräts vermutlich auf dem gleichen Niveau bleiben, die Kapazitätsauslastung des Primärspeichers wird sich jedoch erhöhen, da mehr Daten geschrieben werden.

## Netzwerkdurchsatz

**Netzwerkdurchsatz** verfolgt Metrikdaten im Zusammenhang mit der Menge an Daten nach, die von den Netzwerkschnittstellen des iSCSI-Initiators auf dem Hostserver und dem Gerät sowie zwischen Gerät und Cloud übertragen werden. Sie können diese Metrikdaten für jede iSCSI-Netzwerkschnittstelle Ihres Geräts überwachen.

## Geräteleistung 

**Geräteleistung** verfolgt Metrikdaten zur Leistung Ihres Geräts nach.

## Nächste Schritte

[Erfahren Sie, wie Sie das StorSimple Manager-Dienstdashboardsfür Geräte verwenden](storsimple-device-dashboard.md).

<!---HONumber=August15_HO7-->