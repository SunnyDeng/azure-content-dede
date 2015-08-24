Ressource|Standardlimit
---|---
Datenbankgröße|Abhängig von der Leistungsebene <sup>1</sup>
Anmeldungen|Abhängig von der Leistungsebene <sup>1</sup>
Speicherauslastung|16 MB Arbeitsspeicherzuweisung für mehr als 20 Sekunden
Sitzungen|Abhängig von der Leistungsebene <sup>1</sup>
Größe von "tempdb"|5 GB
Transaktionsdauer|24 Stunden<sup>2</sup>
Sperren pro Transaktion|1 Mio.
Größe pro Transaktion|2 GB
Prozent des gesamten Protokollspeicherplatzes pro Transaktion|20
Maximale Anzahl gleichzeitiger Anforderungen \(Arbeitsthreads\)|Abhängig von der Leistungsebene <sup>1</sup>


<sup>1</sup>Für SQL-Datenbank sind Leistungsebenen definiert, z. B. Basic, Standard und Premium. Standard und Premium sind in weitere Leistungsstufen unterteilt. Ausführliche Informationen zu den Begrenzungen pro Ebene und Servicelevel finden Sie unter [Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Wenn bei einer Transaktion eine Ressource gesperrt wird, die für eine zugrunde liegende Systemaufgabe erforderlich ist, beträgt die maximale Dauer 20 Sekunden.

<!---HONumber=August15_HO7-->