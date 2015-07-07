<properties 
   pageTitle="StorSimple-Systemeinschränkungen"
   description="Beschreibt Systemeinschränkungen und empfohlene Größe für StorSimple-Komponenten und -Verbindungen."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# StorSimple-Systemeinschränkungen

## Übersicht

StorSimple bietet skalierbaren und flexiblen Speicher für Ihr Rechenzentrum. Es gibt jedoch einige Einschränkungen, die Sie bei Planung, Bereitstellung und Betrieb Ihrer StorSimple-Lösung beachten sollten. Die folgende Tabelle enthält eine Beschreibung dieser Einschränkungen sowie einige Empfehlungen, damit Sie Ihre StorSimple-Lösung optimal nutzen können.

| Grenzwertbezeichner | Grenzwert | Kommentare |
|----------------- | ------|--------- |
| Maximale Anzahl von Anmeldeinformationen für das Speicherkonto | 64 | |
| Maximale Anzahl von Volumecontainern | 64 | |
| Maximale Anzahl von Volumes | 255 | |
| Maximale Anzahl von Bandbreitenvorlagen | 25 | |
| Maximale Anzahl von Zeitplänen pro Bandbreitenvorlage | 168 | Ein Zeitplan pro Stunde an jedem Tag der Woche. |
| Maximale Größe eines Volumes | 64 TB | Die empfohlene Größe für ein NTFS-Volume beträgt 64 TB. |
| Maximale Anzahl von iSCSI-Verbindungen | 512 | |
| Maximale Anzahl von iSCSI-Verbindungen von Initiatoren | 512 | |
| Maximale Anzahl von Zugriffssteuerungsdatensätze pro Gerät | 64 | |
| Maximale Anzahl von Volumes pro Sicherungsrichtlinie | 24 | |
| Maximale Anzahl von beibehaltenen Sicherungen pro Sicherungsrichtlinie | 64 | |
| Maximale Anzahl von Zeitplänen pro Sicherungsrichtlinie | 10 | |
| Maximale Anzahl von Momentaufnahmen eines beliebigen Typs, die pro Volume beibehalten werden können | 256 | Dies schließt lokale Momentaufnahmen und Cloudmomentaufnahmen ein. |
| Maximale Anzahl von Momentaufnahmen in einem Gerät | 10.000 | |
| Maximale Anzahl von Volumes, die für Sicherung, Wiederherstellung oder Klonen parallel verarbeitet werden können | 16 |<ul><li>Wenn mehr als 16 Volumes vorhanden sind, werden diese sequenziell verarbeitet, sobald Verarbeitungsslots verfügbar werden.</li><li>Neue Sicherungen eines geklonten oder wiederhergestellten Volumes können erst dann erfolgen, wenn der Vorgang abgeschlossen ist.</li></ul>|
| Wiederherstellungszeit für Wiederherstellen und Klonen | < 2 Minuten | <ul><li>Das Volume wird unabhängig von dessen Größe innerhalb von 2 Minuten nach einem Wiederherstellungs- oder Klonvorgang zur Verfügung gestellt.</li><li>Die Volumeleistung ist zunächst möglicherweise geringer als üblich, da sich der überwiegende Teil der Daten und Metadaten noch in der Cloud befindet. Die Leistung kann sich steigern, wenn die Daten aus der Cloud in das StorSimple-Gerät übertragen wurden.</li><li>Die Gesamtzeit, die zum Herunterladen der Metadaten benötigt wird, hängt von der zugewiesenen Volumegröße ab. Die Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Diese Rate kann durch die Internetbandbreite zur Cloud beeinträchtigt werden.</li><li>Der Wiederherstellungs- oder Klonvorgang ist abgeschlossen, wenn sich alle Metadaten auf dem Gerät befinden.</li><li>Sicherungsvorgänge können erst ausgeführt werden, wenn der Wiederherstellungs- oder Klonvorgang vollständig abgeschlossen ist.|
| Thin-Restore-Verfügbarkeit | Letztes Failover | |
| Maximaler Lese-/Schreibdurchsatz des Clients (bei Bereitstellung der Daten auf SSD-Ebene)* | 920/720 MB/s mit einer einzelnen 10-GbE-Netzwerkschnittstelle | Bis zu 2x mit MPIO und zwei Netzwerkschnittstellen. |
| Maximaler Lese-/Schreibdurchsatz des Clients (bei Bereitstellung der Daten auf HDD-Ebene)* | 120/250 MB/s |
| Maximaler Lese-/Schreibdurchsatz des Clients (bei Bereitstellung der Daten auf Cloudebene)* | 11/41 MB/s | Der Lesedurchsatz hängt davon ab, dass Clients eine ausreichende E/A-Warteschlangenlänge generieren und verwalten. |

* Der maximale Durchsatz pro E/A-Typ wurde mit 100-%-Lese- und 100-%-Schreibszenarios gemessen. Der tatsächliche Durchsatz kann geringer sein und hängt von der E/A-Mischung und den Netzwerkbedingungen ab.

## Nächste Schritte

Überprüfen Sie die [StorSimple-Systemanforderungen](storsimple-system-requirements.md).

<!---HONumber=62-->