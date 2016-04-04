#### So installieren Sie MPIO auf dem Host

1. Öffnen Sie den Server-Manager auf Ihrem Windows Server-Host. Der Server-Manager wird standardmäßig gestartet, wenn sich ein Mitglied der Gruppe "Administratoren" an einem Computer unter Windows Server 2012 R2 oder Windows Server 2012 anmeldet. Wenn der Server-Manager nicht bereits geöffnet ist, klicken Sie auf **Start > Server-Manager**.

	![Server-Manager](./media/storsimple-install-mpio-windows-server/IC740997.png)

2. Klicken Sie auf **Server-Manager > Dashboard > Rollen und Features hinzufügen**. Dadurch wird der Assistent **Rollen und Features hinzufügen** gestartet.

	![Hinzufügen von Rollen und Features – Assistent 1](./media/storsimple-install-mpio-windows-server/IC740998.png)

3. Gehen Sie im Assistenten **Rollen und Features hinzufügen** folgendermaßen vor:

	- Klicken Sie auf der Seite **Vorbereitungen** auf **Weiter**.
	- Akzeptieren Sie auf der Seite **Auswählen des Installationstyps** die Standardeinstellung **Rollenbasierte oder featurebasierte Installation**. Klicken Sie auf **Weiter**.
	
		![Hinzufügen von Rollen und Features – Assistent 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
	- Wählen Sie auf der Seite **Zielserver auswählen** die Option **Einen Server aus Serverpool auswählen** aus. Ihr Hostserver sollte automatisch ermittelt werden. Klicken Sie auf **Weiter**.
	- Klicken Sie auf der Seite **Serverrollen auswählen** auf **Weiter**.
	- Wählen Sie auf der Seite **Auswählen von Features** die Option **Multipfad-E/A** aus, und klicken Sie dann auf **Weiter**.
	
		![Hinzufügen von Rollen und Features – Assistent 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
	- Bestätigen Sie auf der Seite **Installationsauswahl bestätigen** Ihre Auswahl, und wählen Sie dann wie unten gezeigt **Zielserver bei Bedarf automatisch neu starten aus**. Klicken Sie auf **Installieren**.
	
		![Hinzufügen von Rollen und Features – Assistent 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
	- Nachdem die Installation abgeschlossen wurde, erhalten Sie eine Benachrichtigung. Klicken Sie auf **Schließen**, um den Assistenten zu schließen.
	
		![Hinzufügen von Rollen und Features – Assistent 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

<!---HONumber=AcomDC_0323_2016-->