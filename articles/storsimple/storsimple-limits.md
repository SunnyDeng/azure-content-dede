<properties 
   pageTitle="StorSimple-Systembegrenzungen | Microsoft Azure"
   description="Beschreibt Systembegrenzungen und empfohlene Größen für StorSimple-Komponenten und Verbindungen der 8000 Serie."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="02/29/2016"
   ms.author="alkohli" />

# StorSimple-Systemeinschränkungen

## Übersicht

StorSimple bietet skalierbaren und flexiblen Speicher für Ihr Rechenzentrum. Es gibt jedoch einige Einschränkungen, die Sie bei Planung, Bereitstellung und Betrieb Ihrer StorSimple-Lösung beachten sollten. Die folgende Tabelle enthält eine Beschreibung dieser Einschränkungen sowie einige Empfehlungen, damit Sie Ihre StorSimple-Lösung optimal nutzen können.

| Grenzwertbezeichner | Begrenzung | Kommentare |
|----------------- | ------|--------- |
| Maximale Anzahl von Anmeldeinformationen für das Speicherkonto | 64 | |
| Maximale Anzahl von Volumecontainern | 64 | |
| Maximale Anzahl von Volumes | 255 | |
| Maximale Anzahl von Zeitplänen pro Bandbreitenvorlage | 168 | Einen Zeitplan für jede Stunde, jeden Tag der Woche (24 * 7). |
| Maximale Größe eines mehrstufigen Volumes auf physischen Geräten | 64 TB für 8100 und 8600 | 8100 und 8600 sind physische Geräte. |
| Maximale Größe eines mehrstufigen Volumes auf virtuellen Geräten in Azure | 30 TB für 8010 <br></br> 64 TB für 8020 | 8010 und 8020 sind virtuelle Geräte in Azure, die Standardspeicher und Storage Premium verwenden. |
| Maximale Größe eines lokal fixierten Volumes auf physischen Geräten | 8 TB für 8100 <br></br> 20 TB für 8600 | 8100 und 8600 sind physische Geräte. |
| Maximale Anzahl von iSCSI-Verbindungen | 512 | |
| Maximale Anzahl von iSCSI-Verbindungen von Initiatoren | 512 | |
| Maximale Anzahl von Zugriffssteuerungsdatensätzen pro Gerät | 64 | |
| Maximale Anzahl von Volumes pro Sicherungsrichtlinie | 24 | |
| Maximale Anzahl von Sicherungen, die pro Zeitplan (in einer Sicherungsrichtlinie) beibehalten werden | 64 | |
| Maximale Anzahl von Zeitplänen pro Sicherungsrichtlinie | 10 | |
| Maximale Anzahl von Momentaufnahmen beliebigen Typs, die pro Volume beibehalten werden können | 256 | Dies beinhaltet lokale Momentaufnahmen und Cloud-Momentaufnahmen. |
| Maximale Anzahl von Momentaufnahmen, die in einem Gerät vorhanden sein können | 10\.000 | |
| Maximale Anzahl von Volumes, die für Sicherung, Wiederherstellung oder Klonen parallel verarbeitet werden können | 16 |<ul><li>Wenn mehr als 16 Volumes vorhanden sind, werden diese sequenziell verarbeitet, wenn Verarbeitungsslots verfügbar werden.</li><li>Neue Sicherungen eines geklonten oder wiederhergestellten Volumes können erst stattfinden, nachdem der Vorgang abgeschlossen wurde. Allerdings sind für ein lokales Volume Sicherungen zulässig, nachdem das Volume online geschaltet wurde.</li></ul>|
| Wiederherstellungszeit für das Wiederherstellen und Klonen mehrstufiger Volumes | < 2 Minuten | <ul><li>Das Volume wird innerhalb von zwei Minuten nach Wiederherstellungs- oder Klonvorgängen unabhängig von der Datenträgergröße zur Verfügung gestellt.</li><li>Die Volumeleistung ist zunächst möglicherweise schlechter als normal, da sich die meisten Daten und Metadaten noch in der Cloud befinden. Die Leistung kann steigen, wenn die Daten aus der Cloud auf das StorSimple-Gerät übertragen werden. </li><li>Die Gesamtzeit zum Herunterladen der Metadaten hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite zur Cloud beeinflusst werden.</li><li>Der Wiederherstellungs- oder Klonvorgang ist abgeschlossen, wenn sich alle Metadaten auf dem Gerät befinden.</li><li>Sicherungsvorgänge können erst ausgeführt werden, wenn das Wiederherstellen oder Klonen vollständig abgeschlossen ist.|
| Wiederherstellungszeit für das Wiederherstellen lokal fixierter Volumes | < 2 Minuten | <ul><li>Das Volume wird unabhängig von der Datenträgergröße innerhalb von zwei Minuten nach Wiederherstellungsvorgängen zur Verfügung gestellt.</li><li>Die Volumeleistung ist zunächst möglicherweise schlechter als normal, da sich die meisten Daten und Metadaten noch in der Cloud befinden. Die Leistung kann steigen, wenn die Daten aus der Cloud auf das StorSimple-Gerät übertragen werden. </li><li>Die Gesamtzeit zum Herunterladen der Metadaten hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite zur Cloud beeinflusst werden.</li><li>Im Gegensatz zu mehrschichtigen Volumes werden bei lokal fixierten Volumes die Volumedaten lokal auf das Gerät heruntergeladen. Die Wiederherstellung ist abgeschlossen, wenn alle Daten des Datenträgers auf das Gerät geladen wurden.</li><li>Die Wiederherstellungsvorgänge können lange dauern, und die Gesamtzeit zum Abschließen des Wiederherstellungsvorgangs hängt von der Größe des bereitgestellten lokalen Volumes, der Internetbandbreite und den vorhandenen Daten auf dem Gerät ab. Sicherungsvorgänge auf lokal fixierten Volumes sind zulässig, während die Wiederherstellung ausgeführt wird.|
|Verarbeitungsrate für Cloudmomentaufnahmen| 15 Minuten/TB | <ul><li>Die minimale Zeit zum Vorbereiten einer Cloudmomentaufnahme für das Hochladen. Diese gilt pro TB an zugewiesenen Volumedaten in der Sicherung. </li><li> Die Gesamtzeit für eine Cloudmomentaufnahme wird berechnet, indem diese Zeit zu der Hochladezeit für die Momentaufnahme addiert wird. Letztere wird von der Internetbandbreite zur Cloud beeinflusst.
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von SSD-Ebene aus bereitgestellt)* | 920/720 MB/s mit einer einzelnen 10-GbE-Netzwerkschnittstelle | Bis zu 2x mit MPIO und zwei Netzwerkschnittstellen. |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von HDD-Ebene aus bereitgestellt)* | 120/250 MB/s |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von Cloud-Ebene aus bereitgestellt)* | 11/41 MB/s | Lesedurchsatz hängt von Clients ab, die genügend E/A-Warteschlangentiefe generieren und verwalten müssen. |

& #42; Der maximale Durchsatz pro E/A-Typ wurde mit 100 %-Lese- und 100 %-Schreibszenarien gemessen. Der tatsächliche Durchsatz kann geringer sein und hängt von der E/A-Mischung und den Netzwerkbedingungen ab.

## Nächste Schritte

Überprüfen Sie die [StorSimple-Systemanforderungen](storsimple-system-requirements.md).

<!---HONumber=AcomDC_0309_2016-->