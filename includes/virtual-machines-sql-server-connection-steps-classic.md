### Bestimmen des DNS-Namens des virtuellen Computers

Um sich von einem anderen Computer aus mit dem SQL Server-Datenbankmodul zu verbinden, müssen Sie den DNS-Namen (Domain Name System) des virtuellen Computers kennen. (Dies ist der Name, den das Internet verwendet, um den virtuellen Computer zu identifizieren. Sie können die IP-Adresse verwenden, aber die IP-Adresse ändert sich möglicherweise, wenn Azure aufgrund von Redundanz oder Wartungsarbeiten Ressourcen verschiebt. Der DNS-Name bleibt bestehen, da er zu einer neuen IP-Adresse weitergeleitet werden kann.)

1. Wählen Sie im Azure-Verwaltungsportal (oder aus dem vorherigen Schritt) **VIRTUELLE COMPUTER**. 

2. Suchen und kopieren Sie auf der Seite **INSTANZEN VIRTUELLER COMPUTER** in der Spalte **Schnelleinsicht** den DNS-Namen für den virtuellen Computer.

	![DNS-Name](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### Verbinden mit dem Datenbankmodul von einem anderen Computer aus
 
1. Öffnen Sie auf einem mit dem Internet verbundenen Computer SQL Server Management Studio.
2. Geben Sie im Dialogfeld **Verbindung mit Server herstellen ** oder **Verbindung mit Datenbankmodul herstellen** im Feld **Servername** den DNS-Namen des virtuellen Computers ein (in der vorherigen Aufgabe festgelegt) sowie die Portnummer eines öffentlichen Endpunkts im Format *DNSName, Portnummer* wie etwa **tutorialtestVM.cloudapp.net,57500**. Um die Portnummer zu erhalten, melden Sie sich beim Azure-Verwaltungsportal an und suchen den virtuellen Computer. Klicken Sie auf dem Dashboard auf **ENDPUNKTE**, und verwenden Sie den **ÖFFENTLICHEN PORT**, der **MSSQL** zugewiesen ist.![Öffentlicher Port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. Wählen Sie im Feld **Authentifizierung** den Eintrag **SQL Server-Authentifizierung**.
5. Geben Sie im Feld **Anmeldename** den Anmeldenamen ein, den Sie vorher erstellt haben.
6. Geben Sie im Feld **Kennwort** das Kennwort des Anmeldenamens ein, den Sie vorher erstellt haben.
7. Klicken Sie auf **Verbinden**.

	![Verbinden mit SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=AcomDC_0107_2016-->