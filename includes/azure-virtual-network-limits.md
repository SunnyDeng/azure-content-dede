<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Standardlimit</th>
   <th align="left" valign="middle">Maximales Limit</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Virtuelle Netzwerke</a><sup>1</sup> pro Abonnement</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Computer insgesamt<sup>2</sup> pro virtuellem Netzwerk</p></td>
   <td valign="middle"><p>2.048</p></td>
   <td valign="middle"><p>2.048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Parallele TCP-Verbindungen für einen virtuellen Computer bzw. eine Rolleninstanz</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>Access Control Lists (ACLs) pro Endpunkt<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Lokale Netzwerkstandorte pro virtuellem Netzwerk</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup> Jedes virtuelle Netzwerk unterstützt ein einzelnes [virtuelles Netzwerkgateway](http://msdn.microsoft.com/library/azure/jj156210.aspx).

<sup>2</sup> Die Gesamtzahl der Computer beinhaltet virtuelle Computer und Web-/Workerrolleninstanzen.

<sup>3</sup> ACL wird auf Eingabeendpunkten für virtuelle Computer unterstützt. Für Web-/Workerrollen wird ACL auf Eingabe- und Instanz-Eingabeendpunkten unterstützt.

<!---HONumber=July15_HO4-->