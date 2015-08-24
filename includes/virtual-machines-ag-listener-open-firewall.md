In diesem Schritt erstellen Sie eine Firewallregel, um den Testport für den Endpunkt mit Lastenausgleich \(59999 wie bereits angegeben\) zu öffnen, und eine weitere Regel, um den Port des Verfügbarkeitsgruppenlisteners zu öffnen. Da Sie den Endpunkt mit Lastenausgleich auf den Azure-VMs erstellt haben, die Verfügbarkeitsgruppenreplikate enthalten, müssen Sie den Testport und den Listenerport auf den entsprechenden Azure-VMs öffnen.

1. Starten Sie auf virtuellen Computern, die Replikate hosten, **Windows-Firewall mit erweiterter Sicherheit**.

1. Klicken Sie mit der rechten Maustaste auf **Eingehende Regeln**, und klicken Sie dann auf **Neue Regel**.

1. Wählen Sie auf der Seite **Regeltyp** die Option **Port** aus, und klicken Sie dann auf **Weiter**.

1. Wählen Sie auf der Seite **Protokoll und Ports** die Option **TCP** aus, und geben Sie **59999** in das Feld **Bestimmte lokale Ports** ein. Klicken Sie auf **Weiter**.

1. Lassen Sie auf der Seite **Aktion** die Option **Verbindung zulassen** ausgewählt, und klicken Sie auf **Weiter**.

1. Übernehmen Sie auf der Seite **Profil** die Standardeinstellungen, und klicken Sie auf **Weiter**.

1. Geben Sie auf der Seite **Name** einen Regelnamen, z. B.**AlwaysOn-Listenertestport**, in das Textfeld **Name** ein, und klicken Sie auf **Fertig stellen**.

1. Wiederholen Sie die oben genannten Schritte für den Port des Verfügbarkeitsgruppenlisteners \(wie weiter oben im $EndpointPort-Parameter des Skripts angegeben\), und geben Sie einen geeigneten Regelnamen an, z. B.**AlwaysOn-Listenerport**.

<!---HONumber=August15_HO7-->