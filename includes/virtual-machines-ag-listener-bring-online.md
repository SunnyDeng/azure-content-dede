1. Wechseln Sie wieder zum Failovercluster-Manager. Erweitern Sie **Rollen**, und markieren Sie dann die Verfügbarkeitsgruppe. Klicken Sie auf der Registerkarte **Ressourcen** mit der rechten Maustaste auf den Listenernamen, und klicken Sie auf "Eigenschaften".

1. Klicken Sie auf die Registerkarte **Abhängigkeiten**. Wenn mehrere Ressourcen aufgeführt sind, überprüfen Sie, ob die IP-Adressen OR-Abhängigkeiten aufweisen \(statt AND-Abhängigkeiten\). Klicken Sie auf **OK**.

1. Klicken Sie mit der rechten Maustaste auf den Listenernamen, und klicken Sie auf **Online schalten**.

1. Sobald der Listener online ist, klicken Sie auf der Registerkarte **Ressourcen** mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und klicken Sie auf **Eigenschaften**.

![Konfigurieren der Verfügbarkeitsgruppenressource](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

1. Erstellen Sie eine Abhängigkeit für die Ressource des Listenernamens \(nicht den Ressourcennamen der IP-Adresse\). Klicken Sie auf **OK**.

![Hinzufügen von Abhängigkeiten zum Listenernamen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

1. Starten Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit dem primären Replikat her.

1. Navigieren Sie zu **Hohe Verfügbarkeit mit AlwaysOn** \| **Verfügbarkeitsgruppen** \| **<AvailabilityGroupName>** \| **Verfügbarkeitsgruppenlistener**.

3. Jetzt sollte der Listenername angezeigt werden, den Sie im Failovercluster-Manager erstellt haben. Klicken Sie mit der rechten Maustaste auf den Listenernamen, und klicken Sie auf **Eigenschaften**.

1. Geben Sie mit dem zuvor verwendeten $EndpointPort im Feld **Port** die Portnummer für den Verfügbarkeitsgruppenlistener an \(in diesem Tutorial war 1433 der Standardwert\), klicken Sie dann auf **OK**.

<!---HONumber=August15_HO7-->