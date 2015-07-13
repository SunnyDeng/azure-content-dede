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

<sup>1</sup> Wenn Sie einen virtuellen Computer außerhalb einer Azure-Ressourcengruppe erstellen, wird automatisch auch ein Clouddienst erzeugt, der diesen Computer enthält. Sie können anschließend mehrere virtuelle Computer in diesem Clouddienst hinzufügen.

<sup>2</sup> Eingabeendpunkte dienen zur Kommunikation mit virtuellen Computern außerhalb des aktuellen Clouddiensts. Virtuelle Computer innerhalb des gleichen Clouddiensts erlauben automatisch die Kommunikation zwischen allen UDP- und TCP-Ports für die interne Kommunikation.

<!---HONumber=62-->