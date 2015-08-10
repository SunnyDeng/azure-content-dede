<properties 
   pageTitle="StorSimple-Hardwarekomponenten und Status"
   description="Hier erfahren Sie, wie Sie mithilfe des StorSimple Manager-Diensts die Hardwarekomponenten Ihres StorSimple-Geräts überwachen."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/28/2015"
   ms.author="alkohli" />

# StorSimple-Hardwarekomponenten und Status

## Übersicht

Dieser Artikel beschreibt die verschiedenen physischen und logischen Komponenten Ihres lokalen StorSimple-Geräts. Außerdem wird erläutert, wie Sie den Status der Gerätekomponenten auf der Seite **Wartung** des StorSimple Manager-Diensts überwachen.

Die Seite **Wartung** zeigt den Hardwarestatus aller StorSimple-Gerätekomponenten. Unter der Liste mit den Komponenten für 8100 befinden sich drei Abschnitte, die Folgendes beschreiben:

- **Gemeinsam genutzte Komponenten**: Diese Komponenten sind nicht Teil der Controller. Hierbei handelt es sich beispielsweise um Laufwerke, Gehäuse, PCM-Komponenten und Fühler für PCM-Temperatur, -Strom und -Spannung.

- **Controller 0-Komponenten**: Die Komponenten von Controller 0. Hierzu zählen beispielsweise Controller, SAS-Erweiterung und -Connector, Controllertemperaturfühler und die verschiedenen Netzwerkschnittstellen.

- **Controller 1-Komponenten**: Die Komponenten von Controller 1 \(ähnlich wie bei Controller 0\).

Ein Gerät vom Typ 8600 verfügt über zusätzliche Komponenten für das EBOD-Gehäuse. Unter der Komponentenliste befinden sich fünf Abschnitte. Davon enthalten drei Abschnitte die Komponenten des primären Gehäuses. Diese entsprechen der Beschreibung für 8100. Die beiden zusätzlichen Abschnitte beziehen sich auf das EBOD-Gehäuse und beschreiben Folgendes:

- **Gemeinsam genutzte EBOD-Komponenten**: Die Komponenten im EBOD-Gehäuse und PCM, die nicht Teil des EBOD-Controllers sind.

- **EBOD-Controller 0-Komponenten**: Die Komponenten im EBOD-Gehäuse 0. Hierzu zählen beispielsweise EBOD-Controller, SAS-Erweiterung und -Connector sowie Controllertemperaturfühler.

- **EBOD-Controller 1-Komponenten**: Die Komponenten von EBOD-Gehäuse 1 \(ähnlich wie bei EBOD-Gehäuse 0\).

>[AZURE.NOTE]**Bei virtuellen StorSimple-Geräten \(1100\) ist auf der Wartungsseite kein Hardwarestatusbereich vorhanden.**


## Überwachen des Hardwarestatus

Führen Sie die folgenden Schritte aus, um den Hardwarestatus einer Gerätekomponente anzuzeigen:

1. Navigieren Sie zu **Geräte**, und wählen Sie ein bestimmtes StorSimple-Gerät aus. Klicken Sie im Menü auf Geräteebene auf die Registerkarte **Wartung**. 
2. Navigieren Sie zum Abschnitt **Hardwarestatus**, und wählen Sie eine der verfügbaren Komponenten aus, die weiter oben beschrieben wurden. Klicken Sie einfach auf den Pfeil vor einer Komponentenbezeichnung, um die Liste zu erweitern und den Status der verschiedenen Gerätekomponenten anzuzeigen. Weitere Informationen finden Sie in der [ausführlichen Komponentenliste für das primäre Gehäuse](Component-list-for-primary-enclosure) sowie in der [ausführlichen Komponentenliste für das EBOD-Gehäuse](Component-list-for-EBOD-enclosure).
2. Der Komponentenstatus basiert auf dem folgenden Farbschema:
	-  **Grünes Häkchen**: Die Komponente ist frei von Fehlern oder hat den Status **OK**.
	-  **Gelb**: Für die Komponente liegt eine Warnung vor.
	-  **Rotes Ausrufezeichen**: Die Komponente ist fehlerhaft oder hat den Status **Eingreifen erforderlich**.
	-  **Weiß mit schwarzem Text**: Die Komponente ist nicht vorhanden.

3. Sollte sich eine Komponente nicht in einem fehlerfreien Zustand befinden, wenden Sie sich an den Support von Microsoft. Sind Warnungen auf dem Gerät aktiviert, werden Sie per E-Mail benachrichtigt. Informationen zum Austauschen fehlerhafter Hardwarekomponenten finden Sie im [Handbuch für den Austausch von Hardwarekomponenten](https://msdn.microsoft.com/library/azure/dn757736.aspx).


## Komponentenliste für das StorSimple-Gerät

Die folgende Tabelle gibt Aufschluss über die physischen und logischen Komponenten Ihres lokalen StorSimple-Geräts. Das Gerät verfügt über zwei Gehäuse: ein primäres Gehäuse und ein EBOD-Gehäuse. Für die beiden Gehäuse ist jeweils eine eigene Tabelle vorhanden.

### Komponentenliste für das primäre Gehäuse

|Komponente|Modul|Typ|Ort|FRU \(Field Replaceable Unit\)?|Beschreibung|
|---|---|---|---|---|---|
|Laufwerk in Slot *n*|Laufwerke|Physisch|Shared  
|Ja|Die einzelnen SSD- oder HDD-Laufwerke des primären Gehäuses werden jeweils in einer eigenen Zeile angegeben.|
|Ambiente-Temperatursensor|Gehäuse|Physisch|Shared  
|Nein|Misst die Temperatur im Gehäuse.|
|Midplane-Temperatursensor|Gehäuse|Physisch|Shared  
|Nein|Misst die Temperatur auf der mittleren Ebene.|
|Hörbarer Alarm|Gehäuse|Physisch|Shared  
|Nein|Gibt an, ob das Subsystem für den akustischen Alarm im Gehäuse funktionsfähig ist.|
|Gehäuse|Gehäuse|Physisch|Shared  
|Ja|Gibt an, dass ein Gehäuse vorhanden ist.|
|Gehäuseeinstellungen|Gehäuse|Physisch|Shared  
|Nein|Bezieht sich auf das vordere Bedienungsfeld des Gehäuses.|
|Spannungssensoren|PCM|Physisch|Shared  
|Nein|Der angezeigte Zustand zahlreicher Spannungssensoren gibt Aufschluss darüber, ob die gemessene Spannung innerhalb des Toleranzbereichs liegt.|
|Stromsensoren|PCM|Physisch|Shared  
|Nein|Der angezeigte Zustand zahlreicher Stromsensoren gibt Aufschluss darüber, ob die gemessene Stromstärke innerhalb des Toleranzbereichs liegt.|
|Temperatursensoren in PCM|PCM|Physisch|Shared  
|Nein|Der angezeigte Zustand zahlreicher Temperatursensoren \(beispielsweise Einlass- und Hotspot-Sensoren\) gibt Aufschluss darüber, ob die gemessene Temperatur innerhalb des Toleranzbereichs liegt.|
|Stromversorgung n|PCM|Physisch|Shared  
|Ja|Für die einzelnen Stromversorgungen in den beiden PCMs an der Geräterückseite wird jeweils eine eigene Zeile angezeigt.|
|Kühlung *n*|PCM|Physisch|Shared  
|Ja|Für die vier Lüfter in den beiden PCMs wird jeweils eine eigene Zeile angezeigt.|
|Akku *n*|PCM|Physisch|Shared  
|Ja|Für die einzelnen Sicherungsakkumodule im PCM wird jeweils eine eigene Zeile angezeigt.|
|Metis|–|Logisch|Shared  
|–|Zeigt den Zustand der Akkus an und gibt Aufschluss über Ladebedarf und Haltbarkeit.|
|Cluster|–|Logisch|Shared  
|–|Zeigt den Zustand des Clusters, der zwischen den beiden integrierten Controller-Modulen erstellt wird.|
|Clusterknoten|–|Logisch|Shared  
|–|Gibt den Zustand des Controllers als Teil des Clusters an.|
|Clusterquorum|–|Logisch||–|Gibt an, dass die Mehrheitsdatenträger-Mitgliedschaft im HDD-Speicherpool vorhanden ist.|
|HDD-Datenbereich|–|Logisch|Shared  
|–|Der Speicherplatz, der für Daten im HDD-Speicherpool verwendet wird.|
|HDD-Verwaltungsbereich|–|Logisch|Shared  
|–|Der Platz, der im HDD-Speicherpool für Verwaltungsaufgaben reserviert ist.|
|HDD-Quorumbereich|–|Logisch|Shared  
|–|Der Platz, der im HDD-Speicherpool für das Clusterquorum reserviert ist.|
|HDD-Ersetzungsbereich|–|Logisch|Shared  
|–|Der Platz, der im HDD-Speicherpool für die Controllerersetzung reserviert ist.|
|SSD-Datenbereich|–|Logisch|Shared  
|–|Der Speicherplatz, der im SSD-Speicherpool für Daten verwendet wird.|
|SSD-NVRAM-Bereich|–|Logisch|Shared  
|–|Der Speicherplatz im SSD-Speicherpool, der für die NVRAM-Logik reserviert ist.|
|HDD-Speicherpool|–|Logisch|Shared  
|–|Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-HDDs erstellt wird.|
|SSD-Speicherpool|–|Logisch|Shared  
|–|Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-SSDs erstellt wird.|
|Controller *n* \[Zustand\]|E/A|Physisch|Controller|Ja|Zeigt den Zustand des Controllers im Gehäuse und gibt Aufschluss darüber, ob er aktiv oder im Standbymodus ist.|
|Temperatursensoren im Controller|E/A|Physisch|Controller|Nein|Der angezeigte Zustand zahlreicher Temperatursensoren \(etwa für das E/A-Modul, für die CPU-Temperatur sowie DIMM- und PCIe-Sensoren\) gibt Aufschluss darüber, ob die Temperatur innerhalb des Toleranzbereichs liegt.|
|SAS-Erweiterung|E/A|Physisch|Controller|Nein|Gibt den Zustand der SAS-Erweiterung an, über die der integrierte Speicher mit dem Controller verbunden wird.|
|SAS-Verbinder *n*|E/A|Physisch|Controller|Nein|Gibt den Zustand der einzelnen SAS-Verbinder an, über die der integrierte Speicher mit der SAS-Erweiterung verbunden wird.|
|SBB-Midplane-Interconnect|E/A|Physisch|Controller|Nein|Gibt den Zustand des Midplane-Verbinders an, über den die einzelnen Controller mit der mittleren Ebene verbunden werden.|
|Prozessorkern|E/A|Physisch|Controller|Nein|Gibt den Zustand der Prozessorkerne in den einzelnen Controllern an.|
|Gehäuseelektronikleistung|E/A|Physisch|Controller|Nein|Gibt den Zustand der Stromversorgung des Gehäuses an.|
|Gehäuseelektronikdiagnose|E/A|Physisch|Controller|Nein|Gibt den Zustand der vom Controller bereitgestellten Diagnosesubsysteme an.|
|Baseboard-Verwaltungscontroller|E/A|Physisch|Controller|Nein|Gibt den Zustand des Baseboard-Verwaltungscontrollers \(Baseboard Management Controller, BMC\) an. Hierbei handelt es sich um einen speziellen Dienstprozessor, der das Hardwaregerät mithilfe von Sensoren überwacht und mit dem Systemadministrator über eine unabhängige Verbindung kommuniziert.|
|Ethernet|E/A|Physisch|Controller|Nein|Gibt den Zustand der einzelnen Netzwerkschnittstellen \(Verwaltungs- und Datenports des Controllers\) an.|
|NVRAM|E/A|Physisch|Controller|Nein|Gibt den Zustand des NVRAM \(permanenter, vom Akku versorgter Arbeitsspeicher zur Bewahrung anwendungskritischer Informationen bei einem Stromausfall\) an.|

### Komponentenliste für das EBOD-Gehäuse

|Komponente|Modul|Typ|Standort|FRU?|Beschreibung|
|---|---|---|---|---|---|
|Laufwerk in Slot *n*|Laufwerke|Physisch|Shared  
|Ja|Die einzelnen HDD-Laufwerke an der Vorderseite des EBOD-Gehäuses werden jeweils in einer eigenen Zeile angegeben.|
|Ambiente-Temperatursensor|Gehäuse|Physisch|Shared  
|Nein|Misst die Temperatur im Gehäuse.|
|Midplane-Temperatursensor|Gehäuse|Physisch|Shared  
|Nein|Misst die Temperatur auf der mittleren Ebene.|
|Hörbarer Alarm|Gehäuse|Physisch|Shared  
|Nein|Gibt an, ob das Subsystem für den akustischen Alarm im Gehäuse funktionsfähig ist.|
|Gehäuse|Gehäuse|Physisch|Shared  
|Ja|Gibt an, dass ein Gehäuse vorhanden ist.|
|Gehäuseeinstellungen|Gehäuse|Physisch|Shared  
|Nein|Bezieht sich auf das OPS oder vordere Bedienungsfeld des Gehäuses.|
|Spannungssensoren|PCM|Physisch|Shared  
|Nein|Der angezeigte Zustand zahlreicher Spannungssensoren gibt Aufschluss darüber, ob die gemessene Spannung innerhalb des Toleranzbereichs liegt.|
|Stromsensoren|PCM|Physisch|Shared  
|Nein|Der angezeigte Zustand zahlreicher Stromsensoren gibt Aufschluss darüber, ob die gemessene Stromstärke innerhalb des Toleranzbereichs liegt.|
|Temperatursensoren in PCM|PCM|Physisch|Shared  
|Nein|Der angezeigte Zustand zahlreicher Temperatursensoren \(beispielsweise Einlass- und Hotspot-Sensoren\) gibt Aufschluss darüber, ob die gemessene Temperatur innerhalb des Toleranzbereichs liegt.|
|Stromversorgung *n*|PCM|Physisch|Shared  
|Ja|Für die einzelnen Stromversorgungen in den beiden PCMs an der Geräterückseite wird jeweils eine eigene Zeile angezeigt.|
|Kühlung *n*|PCM|Physisch|Shared  
|Ja|Für die vier Lüfter in den beiden PCMs wird jeweils eine eigene Zeile angezeigt.|
|Lokaler Speicher \[HDD\]|–|Logisch|Shared  
|–|Zeigt den Zustand des logischen Speicherpools an, der auf der Grundlage von Geräte-HDDs erstellt wird.|
|Controller *n* \[Zustand\]|E/A|Physisch|Controller|Ja|Zeigt den Zustand der Controller im EBOD-Modul an.|
|Temperatursensoren in EBOD|E/A|Physisch|Controller|Nein|Der angezeigte Zustand zahlreicher Temperatursensoren der einzelnen Controller gibt Aufschluss darüber, ob die Temperatur innerhalb des Toleranzbereichs liegt.|
|SAS-Erweiterung|E/A|Physisch|Controller|Nein|Gibt den Zustand der SAS-Erweiterung an, über die der integrierte Speicher mit dem Controller verbunden wird.|
|SAS-Verbinder *n*|E/A|Physisch|Controller|Nein|Gibt den Zustand der einzelnen SAS-Verbinder an, über die der integrierte Speicher mit der SAS-Erweiterung verbunden wird.|
|SBB-Midplane-Interconnect|E/A|Physisch|Controller|Nein|Gibt den Zustand des Midplane-Verbinders an, über den die einzelnen Controller mit der mittleren Ebene verbunden werden.|
|Gehäuseelektronikleistung|E/A|Physisch|Controller|Nein|Gibt den Zustand der Stromversorgung des Gehäuses an.|
|Gehäuseelektronikdiagnose|E/A|Physisch|Controller|Nein|Gibt den Zustand der vom Controller bereitgestellten Diagnosesubsysteme an.|
|Verbindung mit Gerätecontroller|E/A|Physisch|Controller|Nein|Gibt den Zustand der Verbindung zwischen dem EBOD-E/A-Modul und dem Gerätecontroller an.|

## Nächster Schritt
Informationen zum Behandeln von Problemen mit einer beeinträchtigten oder fehlerhaften Gerätekomponente finden Sie unter [Problembehandlung mit der Überwachungsindikatoren](storsimple-monitoring-indicators).

Informationen zum Austauschen einer fehlerhaften Hardwarekomponente finden Sie im [Handbuch für den Austausch von Hardwarekomponenten](https://msdn.microsoft.com/library/azure/dn757736.aspx).

Sollten weiterhin Geräteprobleme auftreten, [wenden Sie sich an den Support von Microsoft](storsimple-contact-microsoft-support.md).

<!---HONumber=July15_HO5-->