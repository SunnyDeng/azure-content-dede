<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource<sup>1</sup></th>
   <th align="left" valign="middle">Standardlimit</th>
</tr>
<tr>
   <td valign="middle"><p>TB pro Speicherkonto</p></td>
   <td valign="middle"><p>500&#160;TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Größe eines einzelnen Blob-Containers, einer Tabelle oder einer Warteschlange</p></td>
   <td valign="middle"><p>500&#160;TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Anzahl an Blob-Containern, Blobs, Dateifreigaben, Tabellen, Warteschlangen, Entitäten oder Meldungen pro Speicherkonto</p></td>
   <td valign="middle"><p>Die einzige Einschränkung besteht in der Speicherkontokapazität von 500&#160;TB.</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Größe einer Dateifreigabe</p></td>
   <td valign="middle"><p>5&#160;TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximale Anzahl an Dateien in einer Dateifreigabe</p></td>
   <td valign="middle"><p>Die einzige Einschränkung besteht in der Gesamtkapazität der Dateifreigabe von 5&#160;TB.</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximal 8 &#160;B IOPS pro persistentem Laufwerk (Basisstufe)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximal 8&#160;KB IOPS pro persistentem Laufwerk (Standardstufe)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Gesamtanfragerate (ausgehend von einer Objektgröße von 1&#160;KB) pro Speicherkonto</p></td>
   <td valign="middle"><p>Bis zu 20.000 Entitäten oder Meldungen pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Zieldurchsatz bei Einzel-Blob</p></td>
   <td valign="middle"><p>Bis zu 60&#160;MB pro Sekunde, oder bis zu 500 Anforderungen pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Zieldurchsatz bei Einzelwarteschlange (Meldungen mit 1&#160;KB)</p></td>
   <td valign="middle"><p>Bis zu 2000 Meldungen pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Zieldurchsatz bei einzelner Tabellenpartition (Entitäten mit 1&#160;KB)</p></td>
   <td valign="middle"><p>Bis zu 2000 Entitäten pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Eingang pro Speicherkonto (US-Regionen)</p></td>
   <td valign="middle"><p>10 Gbps mit aktiviertem GRS<sup>3</sup>, 20 Gbps bei LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Ausgang pro Speicherkonto (US-Regionen)</p></td>
   <td valign="middle"><p>20&#160;Gbps mit aktiviertem GRS<sup>3</sup>, 30&#160;Gbps bei LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Eingang pro Speicherkonto (Europa- und Asien-Regionen)</p></td>
   <td valign="middle"><p>5&#160;Gbps mit aktiviertem GRS<sup>3</sup>, 10&#160;Gbps bei LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Ausgang pro Speicherkonto (Europa- und Asien-Regionen)</p></td>
   <td valign="middle"><p>10&#160;GBit/s mit aktiviertem GRS<sup>3</sup>, 15&#160;GBit/s bei LRS</p></td>
</tr>
</table>

<sup>1</sup>Weitere Informationen zu diesen Einschränkungen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](../articles/storage/storage-scalability-targets.md).

<sup>2</sup>Legen Sie bei virtuellen Computern auf der Basisstufe nicht mehr als 66 stark genutzte VHDs in einem Speicherkonto ab, um ein Erreichen der Grenze von 20.000 bei der Gesamtanfragerate (20.000/300) zu verhindern. Legen Sie bei virtuellen Computern auf der Standardstufe nicht mehr als 40 stark genutzte VHDs in einem Speicherkonto ab (20.000/500). Weitere Informationen finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

<sup>3</sup> GRS steht für [georedundanter Speicher](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx). LRS steht für [lokal redundanter Speicher](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx). Beachten Sie, dass GRS ebenfalls lokal redundant ist.

<!---HONumber=62-->