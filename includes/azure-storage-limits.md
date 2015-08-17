<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
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
   <td valign="middle"><p>Maximal 8 KB IOPS pro persistentem Laufwerk (virtueller Computer mit Basic-Tarif)</p></td>
   <td valign="middle"><p>300<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximal 8 KB IOPS pro persistentem Laufwerk (virtueller Computer mit Standard-Tarif)</p></td>
   <td valign="middle"><p>500<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Gesamtanfragerate (ausgehend von einer Objektgröße von 1&#160;KB) pro Speicherkonto</p></td>
   <td valign="middle"><p>Bis zu 20.000 IOPS, Entitäten pro Sekunde oder Nachrichten pro Sekunde</p></td>
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
   <td valign="middle"><p>Zieldurchsatz für eine einzelne Dateifreigabe (Vorschau)</p></td>
   <td valign="middle"><p>Bis 60 MB pro Sekunde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Eingang<sup>2</sup> pro Speicherkonto (US-Regionen)</p></td>
   <td valign="middle"><p>10 Gbps mit aktiviertem GRS<sup>3</sup>, 20 Gbps bei LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Ausgang<sup>2</sup> pro Speicherkonto (US-Regionen)</p></td>
   <td valign="middle"><p>20&#160;Gbps mit aktiviertem GRS<sup>3</sup>, 30&#160;Gbps bei LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Eingang<sup>2</sup> pro Speicherkonto (Regionen Europa und Asien)</p></td>
   <td valign="middle"><p>5&#160;Gbps mit aktiviertem GRS<sup>3</sup>, 10&#160;Gbps bei LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Max. Ausgang<sup>2</sup> pro Speicherkonto (Regionen Europa und Asien)</p></td>
   <td valign="middle"><p>10&#160;GBit/s mit aktiviertem GRS<sup>3</sup>, 15&#160;GBit/s bei LRS</p></td>
</tr>
</table>

<sup>1</sup>Die gesamte Anforderungsrate für ein Speicherkonto ist auf 20.000 IOPS beschränkt. Wenn ein virtueller Computer die maximalen IOPS pro Datenträger belegt, sollten Sie sicherstellen, dass die gesamten IOPS auf allen virtuellen Festplatten der virtuellen Computer die Beschränkung für das Speicherkonto (20.000 IOPS) nicht überschreiten, um eine Drosselung zu vermeiden.

Basierend auf der Begrenzung für Transaktionen können Sie die Anzahl der Datenträger mit hoher Auslastung, die in einem Speicherkonto unterstützt werden, grob berechnen. Im Basic-Tarif liegt die maximal zulässige Anzahl der Datenträger mit hoher Auslastung für einen virtuellen Computer beispielsweise bei 66 Datenträgern (20.000/300 IOPS pro Datenträger) und im Standard-Tarif bei ungefähr 40 Datenträgern (20.000/500 IOPS pro Datenträger). Beachten Sie jedoch, dass das Speicherkonto eine größere Anzahl von Datenträgern unterstützen kann, wenn nicht alle Datenträger gleichzeitig eine hohe Auslastung aufweisen.

<sup>2</sup>*Eingehend* bezieht sich auf alle Daten (Anforderungen), die an ein Speicherkonto gesendet werden. *Ausgehend* bezieht sich auf alle Daten (Antworten), die von einem Speicherkonto empfangen werden.

<sup>3</sup>GRS bezieht sich auf den georedundanten Speicher und LRS auf den lokal redundanten Speicher.

<!---HONumber=August15_HO6-->