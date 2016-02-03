### Öffnen der TCP-Ports in der Windows-Firewall für die Standardinstanz des Datenbankmoduls

1. Stellen Sie mit Windows Remotedesktop eine Verbindung zum virtuellen Computer her. Geben Sie nach der Anmeldung auf dem Startbildschirm **WF.msc** ein, und drücken Sie dann die EINGABETASTE. 

	![Starten des Firewall-Programms](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
2. Klicken Sie in **Windows-Firewall mit erweiterter Sicherheit** im linken Bereich mit der rechten Maustaste auf **Eingehende Regeln**, und klicken Sie dann im Aktionsbereich auf **Neue Regel**.

	![Neue Regel](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)

3. Wählen Sie im Dialogfeld **Assistent für neue eingehende Regel ** unter **Regeltyp** die Option **Port** aus, und klicken Sie dann auf **Weiter**.

4. Verwenden Sie im Dialogfeld **Protokolle und Ports** die Standardeinstellung **TCP**. Geben Sie im Feld **Bestimmte lokale Ports** die Portnummer der Instanz des Datenbankmoduls ein (**1433** für die Standardinstanz oder die von Ihnen im Endpunktschritt ausgewählte Nummer für den privaten Port).

	![TCP-Port 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)

5. Klicken Sie auf **Weiter**.

6. Klicken Sie im Dialogfeld **Aktion** auf **Verbindung zulassen** und anschließend auf **Weiter**.

	**Sicherheitshinweis:** Die Auswahl von **Verbindung zulassen, wenn sie sicher ist** kann für zusätzliche Sicherheit sorgen. Wählen Sie diese Option, wenn Sie zusätzliche Sicherheitsoptionen in Ihrer Umgebung konfigurieren möchten.

	![Verbindungen zulassen](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)

7. Klicken Sie im Dialogfeld **Profil** auf **Öffentlich**, **Privat** und **Domäne**. Klicken Sie auf **Next**.

    **Sicherheitshinweis:** Die Auswahl von **Öffentlich** ermöglicht den Zugriff über das Internet. Sie sollten möglichst ein restriktiveres Profil auswählen.

	![Öffentliches Profil](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)

8. Geben Sie im Dialogfeld **Name** einen Namen und eine Beschreibung für diese Regel ein, und klicken Sie dann auf **Fertig stellen**.

	![Regelname](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Öffnen Sie gegebenenfalls zusätzliche Ports für andere Komponenten. Weitere Informationen finden Sie unter [Konfigurieren der Windows-Firewall für den SQL Server-Zugriff](http://msdn.microsoft.com/library/cc646023.aspx).


### Konfigurieren von SQL Server für das Lauschen des TCP-Protokolls

1. Geben Sie während der Verbindung mit dem virtuellen Computer auf der Startseite **SQL Server-Konfigurations-Manager ** ein, und drücken Sie die EINGABETASTE.
	
	![SSCM öffnen](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)

2. Erweitern Sie im SQL Server-Konfigurationsmanager im Konsolenbereich **SQL Server-Netzwerkkonfiguration**.

3. Klicken Sie im Konsolenbereich auf **Protokolle für MSSQLSERVER** (der Standardinstanzenname). Klicken Sie im Detailbereich mit der rechten Maustaste auf TCP, dies sollte standardmäßig für Katalogimages aktiviert sein. Klicken Sie für eigene Images auf **Aktivieren**, falls der Status Deaktiviert ist.

	![TCP aktivieren](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)

5. Klicken Sie im linken Bereich auf **SQL Server-Dienste**. Klicken Sie im Detailsbereich mit der rechten Maustaste auf **SQL Server (_Instanzenname_)** (die Standardinstanz ist **SQL Server (MSSQLSERVER)**), und klicken Sie dann auf **Neu starten**, um die Instanz von SQL Server zu stoppen und neu zu starten.

	![Datenbankmodul neu starten](./media/virtual-machines-sql-server-connection-steps/11Restart.png)

7. Schließen Sie den SQL Server-Konfigurations-Manager.

Weitere Informationen zur Aktivierung von Protokollen für SQL Server-Datenbankmodule finden Sie unter [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](http://msdn.microsoft.com/library/ms191294.aspx).

### Konfigurieren von SQL Server für die Authentifizierung mit gemischtem Modus

Das SQL Server-Datenbankmodul kann ohne Domänenumgebung keine Windows-Authentifizierung nutzen. Um eine Verbindung zum Datenbankmodul von einem anderen Computer aus herzustellen, konfigurieren Sie SQL Server für die Authentifizierung mit gemischtem Modus. Die Authentifizierung mit gemischtem Modus ermöglicht sowohl die SQL Server-Authentifizierung als auch die Windows-Authentifizierung.

>[AZURE.NOTE]Die Konfiguration der Authentifizierung mit gemischtem Modus ist möglicherweise nicht notwendig, wenn Sie ein Azure Virtual Network mit einer konfigurierten Domänenumgebung eingerichtet haben.

1. Geben Sie während der Verbindung mit dem virtuellen Computer auf der Startseite **SQL Server 2014 Management Studio** ein, und klicken Sie auf das ausgewählte Symbol.

	![SSMS starten](./media/virtual-machines-sql-server-connection-steps/18Start-SSMS.png)

	Wenn Sie Management Studio erstmalig öffnen, muss dieses eine Management Studio-Benutzerumgebung erstellen. Dies kann einige Zeit dauern.

2. Management Studio zeigt das Dialogfeld **Verbindung mit Server herstellen** an. Geben Sie im Feld **Servername** den Namen des virtuellen Computers ein, um das Datenbankmodul mit dem Objekt-Explorer zu verbinden. (Anstelle des Namen des virtuellen Computers können Sie auch **(lokal)** oder einen einzigen Punkt als **Servername** verwenden. Wählen Sie **Windows-Authentifizierung**, und lassen Sie **_your\_VM\_name_\\your\_local\_administrator** im Feld **Benutzername**. Klicken Sie auf **Verbinden**.

	![Mit Server verbinden](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)

3. Klicken Sie im Objekt-Explorer von SQL Server Management Studio mit der rechten Maustaste auf die Instanz von SQL Server (den Namen des virtuellen Computers), und klicken Sie dann auf **Eigenschaften**.

	![Servereigenschaften](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)

4. Wählen Sie auf der Seite **Sicherheit**, unter **Serverauthentifizierung**, die Option **SQL Server- und Windows-Authentifizierungsmodus**, und klicken Sie dann auf **OK**.

	![Authentifizierungsmodus auswählen](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)

5. Klicken Sie im Dialogfeld von SQL Server Management Studio auf **OK**, um zu bestätigen, dass es erforderlich ist, SQL Server neu zu starten.

6. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Server, und klicken Sie dann auf **Neu starten**. (Wenn SQL Server-Agent ausgeführt wird, muss dieser ebenfalls neu gestartet werden.)

	![Neu starten](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)

7. Klicken Sie im Dialogfeld von SQL Server Management Studio auf **Ja**, um zu bestätigen, dass SQL Server neu gestartet werden soll.

### Erstellen von Anmeldenamen für die SQL Server-Authentifizierung

Um von einem anderen Computer aus eine Verbindung zum Datenbankmodul herzustellen, müssen Sie mindestens einen Anmeldenamen für die SQL Server-Authentifizierung erstellen.

1. Erweitern Sie in SQL Server Management Studio-Objekt-Explorer den Ordner der Serverinstanz, in der der neue Anmeldename erstellt werden soll.

2. Klicken Sie mit der rechten Maustaste auf den Ordner **Sicherheit**, zeigen Sie auf **Neu**, und wählen Sie **Anmeldung...** aus.

	![Neue Anmeldung](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)

3. Geben Sie im Dialogfeld **Anmeldung – Neu** auf der Seite **Allgemein** im Feld **Anmeldename** den Namen des neuen Benutzers ein.

4. Wählen Sie **SQL Server-Authentifizierung**.

5. Geben Sie im Feld **Kennwort** ein Kennwort für den neuen Benutzer ein. Geben Sie dieses Kennwort erneut im Feld **Kennwort bestätigen** ein.

6. Wählen Sie **Kennwortrichtlinie erzwingen** (empfohlen) aus, um die Kennwortrichtlinienoptionen bezüglich Komplexität und Durchführung durchzusetzen. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

7. Wählen Sie **Ablauf des Kennwortes erzwingen** (empfohlen) aus, um die Kennwortrichtlinienoptionen bezüglich des Ablaufs durchzusetzen. Kennwortrichtlinie erzwingen muss ausgewählt werden, um dieses Kontrollkästchen zu aktivieren. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

8. Wählen Sie **Benutzer muss das Kennwort bei der nächsten Anmeldung ändern**, damit der Benutzer gezwungen wird, ein neues Kennwort nach der erstmaligen Verwendung des Anmeldenamens zu erstellen (Wird empfohlen, falls dieser Anmeldenamen von einer anderen Person verwendet wird. Wenn Sie den Anmeldenamen selbst verwenden werden, sollten Sie diese Option nicht auswählen). Ablauf des Kennwortes erzwingen muss ausgewählt werden, um dieses Kontrollkästchen zu aktivieren. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

9. Wählen Sie aus der Liste **Standarddatenbank** eine Standarddatenbank für den Anmeldenamen aus. **master** ist die Standardoption. Falls Sie noch keine Benutzerdatenbank erstellt haben, lassen Sie diese Einstellung auf **master**.

10. Belassen Sie in der Liste **Standardsprache** den Wert **Standard**.
    
	![Anmeldenameneigenschaften](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)

11. Falls es sich um den ersten Anmeldenamen handelt, den Sie erstellen, werden Sie diesen Anmeldenamen wahrscheinlich als SQL Server-Administrator bezeichnen. Prüfen Sie in diesem Fall **sysadmin** auf der Seite **Serverrollen**.

	**Sicherheitshinweis:** Mitglieder der festen Serverrolle sysadmin haben vollständige Kontrolle über das Datenbankmodul. Sie sollten die Mitgliedschaft zu dieser Rolle sorgfältig beschränken.

	![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)

12. Klicken Sie auf OK.

Weitere Informationen zu Anmeldenamen für SQL Server finden Sie unter [Erstellen eines Anmeldenamens](http://msdn.microsoft.com/library/aa337562.aspx).

<!---HONumber=AcomDC_0107_2016-->