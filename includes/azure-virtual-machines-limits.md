<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
   <th align="left" valign="middle">Maximales Limit</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Virtuelle Computer</a> pro Clouddienst<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Eingabeendpunkte pro Clouddienst<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Die in der Dienstverwaltung (und nicht im Ressourcen-Manager) erstellten virtuellen Computer werden automatisch in einem Clouddienst gespeichert. Für den Lastenausgleich und die Verfügbarkeit können Sie diesem Clouddienst weitere virtuelle Computer hinzufügen. Siehe [Verbinden virtueller Computer mit einem virtuellen Netzwerk oder einem Clouddienst](../virtual-machines/cloud-services-connect-virtual-machine.md).

<sup>2</sup>Eingabeendpunkte ermöglichen die Kommunikation mit einem virtuellen Computer von außerhalb des Clouddiensts des virtuellen Computers. Virtuelle Computer im gleichen Clouddienst oder virtuellen Netzwerk können automatisch miteinander kommunizieren. Siehe [Einrichten von Endpunkten für einen virtuellen Computer](../virtual-machines/virtual-machines-set-up-endpoints.md)

<!---HONumber=July15_HO3-->