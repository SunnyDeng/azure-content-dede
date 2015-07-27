<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
</tr>
<tr>
   <td valign="middle"><p>Datenbankgröße</p></td>
   <td valign="middle"><p>Abhängig von der Leistungsstufe<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Anmeldungen</p></td>
   <td valign="middle"><p>Abhängig von der Leistungsstufe<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Speicherauslastung</p></td>
   <td valign="middle"><p>16 MB Arbeitsspeicherzuweisung für mehr als 20 Sekunden</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sitzungen</p></td>
   <td valign="middle"><p>Abhängig von der Leistungsstufe<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Größe von "tempdb"</p></td>
   <td valign="middle"><p>5 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Transaktionsdauer</p></td>
   <td valign="middle"><p>24 Stunden<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Sperren pro Transaktion</p></td>
   <td valign="middle"><p>1 Mio.</p></td>
</tr>
<tr>
   <td valign="middle"><p>Größe pro Transaktion</p></td>
   <td valign="middle"><p>2 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Prozent des gesamten Protokollspeicherplatzes pro Transaktion</p></td>
   <td valign="middle"><p>20</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Anzahl gleichzeitiger Anforderungen (Arbeitsthreads)</p></td>
   <td valign="middle"><p>Abhängig von der Leistungsstufe<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>Für SQL-Datenbank sind Leistungsstufen definiert, z. B. Basic, Standard und Premium. Standard und Premium sind in weitere Leistungsstufen unterteilt. Ausführliche Informationen zu den Begrenzungen pro Stufe und Servicelevel finden Sie unter [Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Wenn bei einer Transaktion eine Ressource gesperrt wird, die für eine zugrunde liegende Systemaufgabe erforderlich ist, beträgt die maximale Dauer 20 Sekunden.

<!---HONumber=July15_HO3-->