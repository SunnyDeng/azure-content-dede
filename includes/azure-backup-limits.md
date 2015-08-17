
Für Azure Backup gelten die folgenden Beschränkungen.

| Bezeichnung | Standardlimit |
|---|---|
|Anzahl der Server/Computer, die für jeden Tresor registriert werden können|50|
|Größe einer Datenquelle für im Azure-Tresorspeicher gespeicherten Daten|Max. 1,65 TB<sup>1</sup>|
|Anzahl der Sicherungstresore, die in einem Azure-Abonnement erstellt werden können|25|
|Häufigkeit der geplanten Sicherungen pro Tag|3 Sicherungen pro Tag für Windows Server und Windows Client <br/> 2 Sicherungen pro Tag für SCDPM|
|Anzahl der Wiederherstellungspunkte, die erstellt werden können|366<sup>2</sup>|
|Datenträger, die für die Sicherung an einen virtuellen Azure-Computer angeschlossen sind|5|

- <sup>1</sup>Die Beschränkung von 1,65 TB gilt nicht für IaaS-VM-Sicherungen.
- <sup>2</sup>Mithilfe einer Permutation kann die Anzahl auf weniger als 366 festgelegt werden.

<!---HONumber=August15_HO6-->