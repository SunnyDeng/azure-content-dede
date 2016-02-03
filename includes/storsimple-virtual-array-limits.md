
| **Begrenzungsbezeichner** | **Begrenzung** | **Kommentare** |
|-----------------------------------------------------------------------------------------------|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Gesamtkapazität (einschließlich Cloud) | Bis zu 64 TB pro virtuellem Gerät |
| Maximale Anzahl von Anmeldeinformationen für das Speicherkonto pro Gerät | 1 | |
| Maximale Anzahl von Volumes/Freigaben | 16 | |
| Mindestgröße des mehrstufigen Volumes/der Freigabe | 500 GB | |
| Maximale Größe eines mehrstufigen Volumes/einer Freigabe | 20 TB | |
| Mindestgröße eines lokalen Volumes/einer Freigabe | 50 GB | |
| Maximale Größe eines lokalen Volumes/einer Freigabe | 2 TB | |
| Maximale Anzahl von iSCSI-Verbindungen von Initiatoren | 512 | |
| Maximale Anzahl von Zugriffssteuerungsdatensätzen pro Gerät | 64 | |
| Maximale Anzahl von Sicherungen, die vom virtuellen Gerät im Ordner *.backups* für den Dateiserver gespeichert werden | 5 | Dazu zählen die zuletzt geplanten (von der Standardsicherungsrichtlinie generierten) und manuelle Sicherungen. |
| Maximale Anzahl von geplanten Sicherungen, die vom Gerät gespeichert werden | 55 | 30 tägliche Sicherungen<br>12 monatliche Sicherungen<br>13 jährliche Sicherungen |
| Maximale Anzahl von manuellen Sicherungen, die vom Gerät gespeichert werden | 45 | |
| Maximale Anzahl von Volumes, die für Sicherung oder Wiederherstellung parallel verarbeitet werden können | 3 | Wenn mehr als drei Volumes vorhanden sind, werden sie nacheinander verarbeitet, sobald Verarbeitungsslots verfügbar werden. |
| Wiederherstellungszeit für Wiederherstellen | Schnelle Wiederherstellung | Die Wiederherstellung basiert auf dem Wärmebild und ist abhängig von der Volumegröße.<br>Sicherungsvorgänge können stattfinden, während ein Wiederherstellungsvorgang ausgeführt wird. |

<!---HONumber=AcomDC_0121_2016-->