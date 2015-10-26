<!--author=alkohli last changed: 9/17/15-->

| Begrenzungsbezeichner | Begrenzung | Kommentare |
|----------------- | ------|--------- |
| Maximale Anzahl von Anmeldeinformationen für das Speicherkonto | 64 | |
| Maximale Anzahl von Volumecontainern | 64 | |
| Maximale Anzahl von Volumes | 255 | |
| Maximale Anzahl von Zeitplänen pro Bandbreitenvorlage | 168 | Einen Zeitplan für jede Stunde, jeden Tag der Woche (24 * 7). |
| Maximale Größe eines Volumes | 64 TB | |
| Maximale Anzahl von iSCSI-Verbindungen | 512 | |
| Maximale Anzahl von iSCSI-Verbindungen von Initiatoren | 512 | |
| Maximale Anzahl von Zugriffssteuerungsdatensätzen pro Gerät | 64 | |
| Maximale Anzahl von Volumes pro Sicherungsrichtlinie | 24 | |
| Maximale Anzahl von Sicherungen, die pro Sicherungsrichtlinie beibehalten werden | 64 | |
| Maximale Anzahl von Zeitplänen pro Sicherungsrichtlinie | 10 | |
| Maximale Anzahl von Momentaufnahmen beliebigen Typs, die pro Volume beibehalten werden können | 256 | Dies beinhaltet lokale Momentaufnahmen und Cloud-Momentaufnahmen. |
| Maximale Anzahl von Momentaufnahmen, die in einem Gerät vorhanden sein können | 10\.000 | |
| Maximale Anzahl von Volumes, die für Sicherung, Wiederherstellung oder Klonen parallel verarbeitet werden können | 16 |<ul><li>Wenn mehr als 16 Volumes vorhanden sind, werden diese sequenziell verarbeitet, wenn Verarbeitungsslots verfügbar werden.</li><li>Neue Sicherungen eines geklonten oder wiederhergestellten Volumes können erst stattfinden, nachdem der Vorgang abgeschlossen wurde.</li></ul>|
| Wiederherstellungszeit für Wiederherstellen und Klonen | < 2 Minuten | <ul><li>Das Volume wird innerhalb von zwei Minuten nach Wiederherstellungs- oder Klonvorgängen unabhängig von der Datenträgergröße zur Verfügung gestellt.</li><li>Die Volumeleistung ist zunächst möglicherweise schlechter als normal, da sich die meisten Daten und Metadaten noch in der Cloud befinden. Die Leistung kann steigen, wenn die Daten aus der Cloud auf das StorSimple-Gerät übertragen werden. </li><li>Die Gesamtzeit zum Herunterladen der Metadaten hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite zur Cloud beeinflusst werden.</li><li>Der Wiederherstellungs- oder Klonvorgang ist abgeschlossen, wenn sich alle Metadaten auf dem Gerät befinden.</li><li>Sicherungsvorgänge können erst ausgeführt werden, wenn das Wiederherstellen oder Klonen vollständig abgeschlossen ist.|
| Thin-Wiederherstellungsverfügbarkeit | Letztes Failover | |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von SSD-Ebene aus bereitgestellt)* | 920/720 MB/s mit einer einzelnen 10-GbE-Netzwerkschnittstelle | Bis zu 2x mit MPIO und zwei Netzwerkschnittstellen. |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von HDD-Ebene aus bereitgestellt)* | 120/250 MB/s |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von Cloud-Ebene aus bereitgestellt)* | 11/41 MB/s | Lesedurchsatz hängt von Clients ab, die genügend E/A-Warteschlangentiefe generieren und verwalten müssen. |

& #42; Der maximale Durchsatz pro E/A-Typ wurde mit 100 %-Lese- und 100 %-Schreibszenarien gemessen. Der tatsächliche Durchsatz kann geringer sein und hängt von der E/A-Mischung und den Netzwerkbedingungen ab.

<!---HONumber=Oct15_HO3-->