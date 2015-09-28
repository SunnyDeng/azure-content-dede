<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

4. Nach Klicken auf **Verbinden** wird eine Remotedesktopprotokolldatei (RDP-Datei) erstellt und heruntergeladen. Klicken Sie auf **Öffnen**, damit diese Datei verwendet wird.

5. Klicken Sie im Fenster "Remotedesktop" auf **Verbinden**, um den Vorgang fortzusetzen.

	![Stellen Sie eine Verbindung her.](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Geben Sie im Fenster "Windows-Sicherheit" den Benutzernamen und das Kennwort eines Administratorkontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**.

 	>[AZURE.TIP]In den meisten Fällen verwenden Sie den Benutzernamen und das Kennwort, die beim Erstellen des virtuellen Computers angegeben wurden. Überprüfen Sie den Benutzernamen, um sicherzustellen, dass die korrekten Domäneninformationen verfügbar sind:
	>
	>- Wenn der virtuelle Computer zu einer Domäne in Ihrer Organisation gehört, stellen Sie sicher, dass der Benutzername den Namen der Domäne enthält.
	>- Wenn der virtuelle Computer keiner Domäne angehört, entfernen Sie entweder alle Domäneninformationen, indem Sie die Zeile mit '' beginnen, oder verwenden Sie den Namen des virtuellen Computers als Domänennamen. Beispiel: `\MyUserName` oder `MyTestVM\MyUserName`.
	>- Wenn der virtuelle Computer ein Domänencontroller ist, geben Sie den Benutzernamen und das Kennwort eines Domänenadministratorkontos für diese Domäne an.

7.	Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen und das Anmelden zu beenden.

	![Bestätigen Sie die Identität des Computers.](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Sept15_HO3-->